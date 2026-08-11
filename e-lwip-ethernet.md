---
title: "lwIP移植笔记 - ethernet篇"
description: "enc28j60"
date: 2020-07-25T10:08:08+08:00
---

  ethernet的移植工作，主要是“填空”ethernetif.c的过程！    
  （PS. 再一次感谢Adam Dunkels大侠的无私工作！以及对他的敬意！）

# 头文件
> ethernetif.h

```c
#ifndef ETHERNETIF_H_INCLUDED
#define ETHERNETIF_H_INCLUDED

#include "lwip/netif.h"
#include "lwip/ip_addr.h"
#include "lwip/err.h"
#include "netif/etharp.h"

err_t ethernetif_init(struct netif* netif);

void ethernetif_input(struct netif* netif);

#endif /* ETHERNETIF_H_INCLUDED */
```

# ethernet interface
> ethernetif.c位置: ../src/netif

- 初始化（低级）    
```c
static void
low_level_init(struct netif* netif)
```
- 发送（低级）     
```c
static err_t
low_level_output(struct netif* netif, struct pbuf* p)
```    
- 接收（低级）    
```c
static struct pbuf*
low_level_input(struct netif* netif)
```

## 0. define hardware
  根据硬件的不同！
```c
#include "enc28j60.h"
```

##　1. 初始化（低级）

### （1） 初始化MAC地址
```c
netif->hwaddr[0] = 0x00;
netif->hwaddr[1] = 0x04;
netif->hwaddr[2] = 0x25;
netif->hwaddr[3] = 0x1C;
netif->hwaddr[4] = 0xA0;
netif->hwaddr[5] = 0x02;
```

### （2） 初始化网卡
```c
enc28j60Init(netif->hwaddr);
```

### （3）中断使能
  如果使用RTOS，则设置MCU的SPI中断使能；    
  例如在CM0/3/4中：
```c
NVIC_SetPriority(SPI_IRQn, INT_PRIORITY_SPI);
NVIC_EnableIRQ(SPI_IRQn);
```

## 2. 发送（低级）

### RTOS（None）
> 在不使用RTOS的情况下

#### （1） 初始化传输
```c
enc28j60InitiateTransfer(p->tot_len);  // initiate transfer();
```

#### init transfer
```c
void enc28j60InitiateTransfer(uint8_t len)
{
  while((enc28j60Read(ECON1) & ECON1_TXRTS)!= 0);

  enc28j60Write(EWRPTL, TXSTART_INIT & 0xFF);
  enc28j60Write(EWRPTH, TXSTART_INIT >> 8);

  enc28j60Write(ETXNDL, (TXSTART_INIT + len) & 0xFF);
  enc28j60Write(ETXNDH, (TXSTART_INIT + len) >>8);

  enc28j60WriteOp(ENC28J60_WRITE_BUF_MEM, 0, 0x00);
}
```

#### （2） 准备传输
```c
// send data from(q->payload, q->len);
enc28j60Writebuf( q->payload, q->len );
```
#### （3） 开始传输
```c
// signal that packet should be sent();
enc28j60Sent();
```

#### sent
```c
void enc28j60Sent(void)
{
  enc28j60WriteOp(ENC28J60_BIT_FIELD_SET, ECON1, ECON1_TXRTS);

  if( (enc28j60Read(EIR) & EIR_TXERIF) ) {
    enc28j60SetBank(ECON1);
    enc28j60WriteOp(ENC28J60_BIT_FIELD_CLR, ECON1, ECON1_TXRTS);
  }
}
```

### RTOS
  给RTOS信号:
```c
xSemaphoreGive(enc28j60_dev->sync_sem);
```

## 3. 接收（低级）

### （1）读pbuf中的数据
```c
p = low_level_input(netif);
```

### （2） 读数据包
```c
/* points to packet payload, which starts with an Ethernet header */
  ethhdr = p->payload;
```

### （3） 释放pbuf
```c
pbuf_free(p);
```

### （4） len大小
```c
#if LWIP_STATS
		lwip_rx_count += p->tot_len;
#endif
```


# Reference
- [Network interfaces management](https://lwip.fandom.com/wiki/Network_interfaces_management)
