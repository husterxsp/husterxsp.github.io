---
layout: post
title: 快速排序
date: 2017-12-18
author: "Xsp"
catalog: true
tags:
    - Algorithm
---

```cpp

#include <iostream>
#include <vector>
#include <time.h>

using namespace std;

void Swap(int &s, int &t) {
    int tmp = s;
    s = t;
    t = tmp;
}
int Partition(vector<int>& A, int p, int r) {
    int i = p, j = r + 1;
    int x = A[p];
    
    while (true) {
        while(++i <= r && A[i] < x);
        
        while(--j >=0 && A[j] > x);
        
        if (i >= j) break;
        
        Swap(A[i], A[j]);
    }
    A[p] = A[j];
    A[j] = x;
    
    return j;
}
void QuickSort(vector<int>& A, int p, int r) {
    if (p < r) {
        int q = Partition(A, p, r);
        QuickSort(A, p, q - 1);
        QuickSort(A, q + 1, r);
    }
}
int main() {
    int len = 100;
    srand (time(NULL));
    
    vector<int> A(len);
    
    for (int i = 0; i < len; i++) {
        A[i] = rand() % len + 1;
    }
    
    QuickSort(A, 0, len - 1);
    for (int i = 0; i < len; i++) {
        cout << A[i] << endl;
    }
    return 0;
}
```
