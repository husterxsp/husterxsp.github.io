---
layout: post
title: 改进的归并排序
date: 2017-12-18
author: "Xsp"
catalog: true
tags:
    - Algorithm
---

采用链表的形式，避免数组元素的频繁交换。
```cpp
#include <iostream>
#include <vector>
#include <time.h>

using namespace std;
int InSort(vector<int>& A, vector<int>& Link, int low, int high) {
    Link[0] = low;
    for (int i = low + 1; i <= high; i++) {
        int j = low, k = 0, kPrev = 0;
        while (j < i) {
            kPrev = k;
            k = Link[k];
            if (A[i] <= A[k]) break;
            j++;
        }
        if (j == i) {
            Link[k] = i;
        }
        else {
            Link[kPrev] = i;
            Link[i] = k;
        }
    }
    return Link[0];
}
int MergeL(vector<int>& A, vector<int>& Link, int q, int r) {
    int i = q, j = r, k = 0;

    while (i != 0 && j != 0) {
        if (A[i] <= A[j]) {
            Link[k] = i;
            k = i;
            i = Link[i];
        }
        else {
            Link[k] = j;
            k = j;
            j = Link[j];
        }
    }
    if (i == 0) Link[k] = j;
    else Link[k] = i;
    return Link[0];
}
int MergeSortL(vector<int>& A, vector<int>& Link, int low, int high) {
    if (high - low + 1 < 5) {
        // 元素较少时直接用插入排序
        return InSort(A, Link, low, high);
    }
    else {
        int mid = low + (high - low) / 2;
        int q = MergeSortL(A, Link, low, mid);
        int r = MergeSortL(A, Link, mid + 1, high);

        return MergeL(A, Link, q, r);
    }
    return 0;
}
int main() {
    int len = 100;
    srand (time(NULL));
    vector<int> A(len + 1);
    vector<int> Link(len + 1);

    for (int i = 1; i <= len; i++) {
        A[i] = rand() % len + 1;
    }

    MergeSortL(A, Link, 1, len);

    int k = 0;
    for (int i = 0; i < len; i++) {
        cout << A[Link[k]] << endl;
        k = Link[k];
    }
    return 0;
}
```

草稿思路，便于回想。
![](/img/post/2017-12-18-improved-merge-sort-1.jpg)
