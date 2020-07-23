---
title: "5. Longest Palindromic Substring"
---

### 5. Longest Palindromic Substring

> [source link](https://leetcode.com/problems/longest-palindromic-substring/)

##### Description

Given a string s, find the longest palindromic substring in s. You may assume that the maximum length of s is 1000.

##### Signature

```c
char *longestPalindrome(char *s);
```

##### Analysis

1. First we notice that the palindromic string has two forms: “aba” and “abba”, so we must consider them separately. I use the method that first locate the center point, and then test the offset of that center point.

   ```c
   for (int center = 0; center < len - 1; center++)
   {
     int offset_max = (len - 1 - center) > center ? center : (len - 1 - center);
     int offset;
     for (offset = 0; offset <= offset_max; offset++)
     {
       if (s[center + offset] != s[center - offset])
       {
         --offset;
         break;
       }
       else if (offset == offset_max)
       {
         break;
       }
     }
     flag = offset_r >= offset ? flag : 1;
     center_r = offset_r >= offset ? center_r : center;
     offset_r = offset_r >= offset ? offset_r : offset;
     if (s[center] == s[center + 1])
     {
       offset_max = (len - 2 - center) > center ? center : (len - 2 - center);
       for (offset = 0; offset <= offset_max; offset++)
       {
         if (s[center + offset + 1] != s[center - offset])
         {
           --offset;
           break;
         }
         else if (offset == offset_max)
         {
           break;
         }
       }
       flag = offset_r > offset ? flag : 0;
       center_r = offset_r > offset ? center_r : center;
       offset_r = offset_r > offset ? offset_r : offset;
     }
   }
   ```

   The `center_r` and `offset_r` will updated if local offset are bigger — which indicates that the length of local new palindromic substring are greater.

2. You may notice that the above code is buggy because of some edge conditions. So we need to add that conditions handler.

   ```c
   int len = strlen(s);
   if (len < 2)
     return s;
   else if (len==2){
     if(s[0]==s[1])
       return s;
     else
       return s + 1;
   }
   ```

   

##### Solution

```c
char *longestPalindrome(char *s)
{
    int len = strlen(s);
    if (len < 2)
        return s;
    else if (len==2){
        if(s[0]==s[1])
            return s;
        else
            return s + 1;
    }
    int center_r, offset_r = -1;
    int flag = 1; //1 represent string like 'aba'
    for (int center = 0; center < len - 1; center++)
    {
        int offset_max = (len - 1 - center) > center ? center : (len - 1 - center);
        int offset;
        for (offset = 0; offset <= offset_max; offset++)
        {
            if (s[center + offset] != s[center - offset])
            {
                --offset;
                break;
            }
            else if (offset == offset_max)
            {
                break;
            }
        }
        flag = offset_r >= offset ? flag : 1;
        center_r = offset_r >= offset ? center_r : center;
        offset_r = offset_r >= offset ? offset_r : offset;
        if (s[center] == s[center + 1])
        {
            offset_max = (len - 2 - center) > center ? center : (len - 2 - center);
            for (offset = 0; offset <= offset_max; offset++)
            {
                if (s[center + offset + 1] != s[center - offset])
                {
                    --offset;
                    break;
                }
                else if (offset == offset_max)
                {
                    break;
                }
            }
            flag = offset_r > offset ? flag : 0;
            center_r = offset_r > offset ? center_r : center;
            offset_r = offset_r > offset ? offset_r : offset;
        }
    }
    char *r = (char *)malloc((2 * offset_r + 3) * sizeof(char));
    if (flag)
    {
        strncpy(r, s - offset_r + center_r, 2 * offset_r + 1);
        r[2 * offset_r + 1] = '\0';
    }
    else
    {
        strncpy(r, s - offset_r + center_r, 2 * offset_r + 2);
        r[2 * offset_r + 2] = '\0';
    }
    return r;
}
```

