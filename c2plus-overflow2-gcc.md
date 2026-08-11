---
title: "Accessing map value by index"
description: "Using std::advance or std::next"
date: 2026-03-11T11:08:08+08:00
---
c<sup>2</sup> = a<sup>2</sup> + b<sup>2</sup>
### define and use
- aMap and bMap
```
    std::map<int, string> aMap;
    std::map<int, string> bMap;
```
- calc it
```
    int a = getMapKey(aMap, 0);
    int b = getMapKey(bMap, 0);
```

### getMapKey
`int getMapKey(std::map<int, std::string> myMap ,int index) { }`
- next
```
    auto it = myMap.begin();
    auto it2 = std::next(it, index);
    return it2->first;
```
- advance
```
    auto it = myMap.begin();
    std::advance(it, index);
    return it->first;
```
#### diff
|std::next|std::advance|
|-|-|
|It is used to return nth successor of an iterator|It does not have any return type.|
|It takes two parameters that are -: number of elements and a iterator.|It takes two parameters number of elements and iterator|
|Its Time complexity in best case is constant|Its Time complexity in best case is constant|
|Its Time complexity in worst case is linear|Its Time complexity in worst case is linear|

### test
```
#include <iostream>
#include <map>
#include <cmath>

int getMapKey(std::map<int, std::string> myMap ,int index) {
    auto it = myMap.begin();
    auto it2 = std::next(it, index);
    return it2->first;
}

int main() {
    std::map<int, std::string> aMap;
    std::map<int, std::string> bMap;

    aMap.insert(std::pair<int, std::string>(3, "a"));
    bMap.insert(std::pair<int, std::string>(4, "b"));

    int a = getMapKey(aMap, 0);
    int b = getMapKey(bMap, 0);

    int c = pow(a, 2) + pow(b, 2);

    std::cout << sqrt(c) << std::endl;
    return 0;
}
```


## Ref
- [Accessing map value by index](https://stackoverflow.com/questions/7856453/accessing-map-value-by-index)
- [std::advance - c++17](https://en.cppreference.com/w/cpp/iterator/advance.html)
- [std::next - c++11](https://en.cppreference.com/w/cpp/iterator/next.html)
- [std::next vs std::advance in C++](https://www.geeksforgeeks.org/cpp/stdnext-vs-stdadvance-in-cpp/)
