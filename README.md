# Back-of-envelope calculation of upper space bound for delta compression of a sorted array

Suppose we have a sorted integer array. We would like to compress it taking adavantage of the very basic knowlwdge: the fact that it is sorted and the size of the integer.

So let `n` be the size of array and `m` the size of the integer. The space requirements for such an array are `n * m`.

The maximum possible difference between any two array elements is `2^m - 1`. As the array is sorted this is also the maximum possible difference between the first and the last array elements. So the maximum average difference between two neighbor elements is `(2^m - 1) / n`. And the number of bits required to keep an avarage delta value in the worst case would be `log ((2^m - 1) / n)`.

So potentially keeping deltas between neighbor elements utilizes smaller numbers of bits than the original element values. This is not always the case because the number of bits is rounded up to an integer.

For instance, for a sorted array `[10, 50, 250]` the corresponding delta array is `[10, 40, 200]` and the number of utilized bits is `[4, 6, 8]` in both cases. However for a sorted array `[10, 41, 72, 103, 134, 165, 196, 227]` the corresponding delta array is [10, 31, 31, 31, 31, 31, 31, 31] and the number of utilized bits from `[4, 6, 7, 7, 8, 8, 8, 8]` becomes `[4, 5, 5, 5, 5, 5, 5]`.

We would like to keep only significant bits of delta, however this parameter might change from one element to another. So we need to keep the bit-length of every delta too. A possible way to do it is to allocate `log(m)` aux bits per element. The values 0 -- 7 would encode 1 -- 8 bit-length correspondingly.

Thus the total number of bits needed to keep a delta-encoded array would be: `n * (log(m) + log((2^m - 1) / n))`. Again the number of bits has to be rounded up to an integer. But ignore rounding for simplicity. Then we get:

`S = n * m + n * (log(m - m/(2^m)) - log(n))`

The first addend is equal to the space requirements of the uncompressed array. If the second addend is positive the compression might fail in the worst case. If the second addend is negative then we would get some compression in any case. Approximately if `n > m` we save at least one bit per array element, if `n > 2 * m` we save at least two bits per element, etc.
