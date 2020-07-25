---
title: "4. Median of Two Sorted Arrays"
---

### 4. Median of Two Sorted Arrays

> [source link](https://leetcode.com/problems/median-of-two-sorted-arrays/)

##### Description

There are two sorted arrays nums1 and nums2 of size m and n respectively.

Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

You may assume nums1 and nums2 cannot be both empty.

##### Signature

```c
double findMedianSortedArrays(int *nums1, int nums1Size, int *nums2, int nums2Size);
```

##### Analysis

1. First, we figure out the longer array and the shorter array

   ```c
   int *list1, *list2, m, n;
   list1 = nums1Size > nums2Size ? nums1 : nums2;
   m = nums1Size > nums2Size ? nums1Size : nums2Size;
   list2 = nums1Size > nums2Size ? nums2 : nums1;
   n = nums1Size > nums2Size ? nums2Size : nums1Size;
   ```

   Notice that we use `?:` to let assembler to generate conditional move instructions, which will run faster.  


2. Now we will handle a edge condition: list2 (that shorter array) is empty.

   In that case we just need to consider list1 to obtain the median:

   ```c
   if (n == 0)
       return (list1[(m - 1) / 2] + list1[(m - 1) / 2 + ((m - 1) & 1)]) / (double)2;
   ```

3. After that, we will go through the main part: we use binary search to guarantee the O(log) time complexity.  

   The method is: we use index i and j to divide list1 and list2 into two group: list1[0, i-1]&list2[0, j-1] and list1[i, m-1]&list2[j, n-1]. So we can let j be variable which domain is [0, n]\(n indicate all list2 is in left part) and  $\ i = \frac{m+n+1}{2} - j$ so that the left part length is half of whole length.

   ``` c
   int low = 0, high = n, half_len = (m + n + 1) / 2;
   int i, j;
   while (low <= high) //binary search
   {
       j = (low + high) / 2;
       i = half_len - j;
   }
   ```

   The end condition is `list1[i-1]<=list2[j]` and `list2[j-1]<=list1[i]`. And also we must pay full attention on these edge condition such as i=0, i=n … Because in this context, index=0 indicate the whole corresponding list in the right part and index=len indicate the whole list in the left part.

   The search parts are quite simple, when we meet the perfect i and j, we will use them to evaluate the `leftmax` and `rightmin` , and then return the median.

   ```c
   while(...)
   {
       ... ...
       if (j < n && i > 0 && list1[i - 1] > list2[j]) //move j to right
       {
               low = j + 1;
       }
       else if (i < m && j > 0 && list2[j - 1] > list1[i]) //move j to left
       {
               high = j;
       }
       else // i,j meets our require
       {
               //edge check: 0 and m(or n)
               int l_max, r_min;
               if (i == 0)
                   l_max = list2[j - 1];
               else if (j == 0)
                   l_max = list1[i - 1];
               else
                   l_max = list1[i - 1] > list2[j - 1] ? list1[i - 1] : list2[j - 1];
               if ((m + n) & 1)
                   return l_max;
               if (i == m)
                   r_min = list2[j];
               else if (j == n)
                   r_min = list1[i];
               else
                   r_min = list1[i] > list2[j] ? list2[j] : list1[i];
               return ((double)(l_max) + r_min) / 2;
       }
   }
   ```

##### Solution

```c
double findMedianSortedArrays(int *nums1, int nums1Size, int *nums2, int nums2Size)
{
    //list1.len = m list2.len = n m>n
    int *list1, *list2, m, n;
    list1 = nums1Size > nums2Size ? nums1 : nums2;
    m = nums1Size > nums2Size ? nums1Size : nums2Size;
    list2 = nums1Size > nums2Size ? nums2 : nums1;
    n = nums1Size > nums2Size ? nums2Size : nums1Size;
    if (n == 0)
        return (list1[(m - 1) / 2] + list1[(m - 1) / 2 + ((m - 1) & 1)]) / (double)2;
    int low = 0, high = n, half_len = (m + n + 1) / 2;
    int i, j;
    //must be <=, so that index(i and j) can equal to high
    while (low <= high) //binary search
    {
        //list1 -> [0,i]+[i+1,m-1]
        //list2 -> [0,j]+[j+1,n-1]
        j = (low + high) / 2;
        i = half_len - j;
        //must ensure index i and j are valid
        if (j < n && i > 0 && list1[i - 1] > list2[j]) //move j to right
        {
            low = j + 1;
        }
        else if (i < m && j > 0 && list2[j - 1] > list1[i]) //move j to left
        {
            high = j;
        }
        else // i,j meets our require
        {
            //edge check: 0 and m(or n)
            int l_max, r_min;
            if (i == 0)
                l_max = list2[j - 1];
            else if (j == 0)
                l_max = list1[i - 1];
            else
                l_max = list1[i - 1] > list2[j - 1] ? list1[i - 1] : list2[j - 1];
            if ((m + n) & 1)
                return l_max;
            if (i == m)
                r_min = list2[j];
            else if (j == n)
                r_min = list1[i];
            else
                r_min = list1[i] > list2[j] ? list2[j] : list1[i];
            return ((double)(l_max) + r_min) / 2;
        }
    }
    return (double)0;
}
```

