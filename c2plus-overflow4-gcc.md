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


## MergeSort
```
/**
 * Internal method that makes recursive calls.
 * a is an array of Comparable items.
 * tmpArray is an array to place the merged result.
 * left is the left-most index of the subarray.
 * right is the right-most index of the subarray.
 */
template <typename Comparable>
void mergeSort( vector<Comparable> & a,
                vector<Comparable> & tmpArray, int left, int right )
{
    if( left < right )
    {
        int center = ( left + right ) / 2;
        mergeSort( a, tmpArray, left, center );
        mergeSort( a, tmpArray, center + 1, right );
        merge( a, tmpArray, left, center + 1, right );
    }
}

/**
 * Mergesort algorithm (driver).
 */
template <typename Comparable>
void mergeSort( vector<Comparable> & a )
{
    vector<Comparable> tmpArray( a.size( ) );

    mergeSort( a, tmpArray, 0, a.size( ) - 1 );
}
```
- merge
```
/**
 * Internal method that merges two sorted halves of a subarray.
 * a is an array of Comparable items.
 * tmpArray is an array to place the merged result.
 * leftPos is the left-most index of the subarray.
 * rightPos is the index of the start of the second half.
 * rightEnd is the right-most index of the subarray.
 */
template <typename Comparable>
void merge( vector<Comparable> & a, vector<Comparable> & tmpArray,
            int leftPos, int rightPos, int rightEnd )
{
    int leftEnd = rightPos - 1;
    int tmpPos = leftPos;
    int numElements = rightEnd - leftPos + 1;

    // Main loop
    while( leftPos <= leftEnd && rightPos <= rightEnd )
        if( a[ leftPos ] <= a[ rightPos ] )
            tmpArray[ tmpPos++ ] = std::move( a[ leftPos++ ] );
        else
            tmpArray[ tmpPos++ ] = std::move( a[ rightPos++ ] );

    while( leftPos <= leftEnd )    // Copy rest of first half
        tmpArray[ tmpPos++ ] = std::move( a[ leftPos++ ] );

    while( rightPos <= rightEnd )  // Copy rest of right half
        tmpArray[ tmpPos++ ] = std::move( a[ rightPos++ ] );

    // Copy tmpArray back
    for( int i = 0; i < numElements; ++i, --rightEnd )
        a[ rightEnd ] = std::move( tmpArray[ rightEnd ] );
}
```
- main
```
int main() {
	vector v = {81, 94, 11, 96, 12, 35, 17, 95, 28, 58, 41, 75, 15};

	mergeSort(v);

    for(size_t i = 0; i < v.size(); ++i)
        cout << v[i] << " ";

    return 0;
}
```

## Ref
- [size_t vs int warning](https://stackoverflow.com/questions/6578048/size-t-vs-int-warning)
- [std::vector](https://en.cppreference.com/w/cpp/container/vector)
- [Data Structures and Algorithm Analysis in C++ (Fourth Edition)](http://users.cis.fiu.edu/~weiss/dsaa_c++4/code/Sort.h)
