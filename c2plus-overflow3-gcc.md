---
title: "size_t warning"
description: "comparison of integer expressions of different signedness"
date: 2026-03-21T11:08:08+08:00
---
- IDE: CDT + [Mingw 13.1.0](https://github.com/niXman/mingw-builds-binaries)

---
**NOTE**

No warning messages are displayed in CDT.
---

## heapsort
```
#include <iostream>
#include <vector>
using namespace std;

// TIP To <b>Run</b> code, press <shortcut actionId="Run"/> or click the <icon src="AllIcons.Actions.Execute"/> icon in the gutter.
/**
 * Internal method for heapsort.
 * i is the index of an item in the heap.
 * Returns the index of the left child.
 */
inline int leftChild( int i )
{
    return 2 * i + 1;
}

/**
 * Internal method for heapsort that is used in deleteMax and buildHeap.
 * i is the position from which to percolate down.
 * n is the logical size of the binary heap.
 */
template <typename Comparable>
void percDown( vector<Comparable> & a, int i, int n )
{
    int child;
    Comparable tmp;

    for( tmp = std::move( a[ i ] ); leftChild( i ) < n; i = child )
    {
        child = leftChild( i );
        if( child != n-1 && a[ child ] < a[ child + 1 ] )
            ++child;
        if( tmp < a[ child ] )
            a[ i ] = std::move( a[ child ] );
        else
            break;
    }
    a[ i ] = std::move( tmp );
}

/**
 * Standard heapsort.
 */
template <typename Comparable>
void heapsort( vector<Comparable> & a)
{
    for( int i = a.size( ) / 2- 1; i >= 0; --i ) /* buildHeap */
        percDown( a, i, a.size() );
    for( int j = a.size( )- 1; j > 0; --j )
    {
        std::swap( a[ 0 ], a[ j ] ); /* deleteMax */
        percDown( a, 0, j );
    }
}

int main() {
    vector v = {81, 94, 11, 96, 12, 35, 17, 95, 28, 58, 41, 75, 15};
    heapsort(v);

    for(int i : v)
        cout << i << " ";

    return 0;
    // TIP See CLion help at <a href="https://www.jetbrains.com/help/clion/">jetbrains.com/help/clion/</a>. Also, you can try interactive lessons for CLion by selecting 'Help | Learn IDE Features' from the main menu.
}
```

- Warn info - vs2022
```
ConsoleApplication1.cpp(45,16): warning C4267: 'initializing': conversion from 'size_t' to 'int', possible loss of data
```

## Ref
- [size_t vs int warning](https://stackoverflow.com/questions/6578048/size-t-vs-int-warning)
- [std::vector](https://en.cppreference.com/w/cpp/container/vector)
- [Data Structures and Algorithm Analysis in C++ (Fourth Edition)](http://users.cis.fiu.edu/~weiss/dsaa_c++4/code/Sort.h)
