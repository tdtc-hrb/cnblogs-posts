---
title: "signed warning"
description: "comparison of integer expressions of different signedness"
date: 2025-01-11T11:08:08+08:00
---
For development environment settings, see: [pictures](https://blog.csdn.net/xiaobin_HLJ80/article/details/19500207#t0)
and [run application](https://tdtc-hrb.github.io/csdn/post/c-console_app)

## shellsort
```c++
#include <iostream>
#include <vector>

using namespace std;

template <typename Comparable>
void shellsort(vector<Comparable> &a)
{
    for(int gap = a.size() / 2; gap > 0; gap /= 2){
    	for(int i = gap; i < a.size(); ++i)
    	{
    		Comparable tmp = move(a[i]);
    		int j = i;

    		for (; j >= gap && tmp < a[j - gap]; j -= gap)
    		{
    			a[j] = move(a[j - gap]);
    		}
    		a[j] = move(tmp);
    	}
    	cout<< "\n" << gap << endl;
    	for (int n : a)
    		cout << n << ' ';
    }
}

int main() {
    vector<int> a = { 81, 94, 11, 96, 12, 35, 17, 95, 28, 58, 41, 75, 15};
    shellsort(a);
	return 0;
}
```

- Warn info
```
warning: comparison of integer expressions of different signedness: 
'int' and 'std::vector<int>::size_type' {aka 'long long unsigned int'} [-Wsign-compare]
   10 |         for(int i = gap; i < a.size(); ++i)
```

## Ref
- [A warning - comparison between signed and unsigned integer expressions](https://stackoverflow.com/a/3660928)
- [std::vector](https://en.cppreference.com/w/cpp/container/vector)
- [Data Structures and Algorithm Analysis in C++ (Fourth Edition)](http://users.cis.fiu.edu/~weiss/dsaa_c++4/code/Sort.h)
