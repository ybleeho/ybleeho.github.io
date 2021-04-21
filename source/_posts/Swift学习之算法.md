---
title: Swift学习之算法
date: 2019-12-17 23:56:26
tags:
---



> 接触一门语言，使用这个语言写写算法感觉可以快速掌握一些基础语句和特点，
> 
> 为此打算使用swift写一些简单常用的算法，并与javascript比较来快速掌握语言精髓。

# 排序

## 快速排序

```
//swift 版本
import UIKit

let arr = [3, 5, 2, 7, 1, 4]
func quicksort<T: Comparable>(_ a: [T]) -> [T] {
  guard a.count > 1 else { return a }
  let pivot = a[a.count/2]
  let less = a.filter { $0 < pivot }
  let equal = a.filter { $0 == pivot }
  let greater = a.filter { $0 > pivot }
  return quicksort(less) + equal + quicksort(greater)
}
quicksort(arr) //[1, 2, 3, 4, 5, 7]
```

```
function quicksort(arr) {
    if(arr.length <= 1) return arr
    const pivot = arr[Math.round(arr.length/2)]
    const less = arr.filter(e => e < pivot)
    const equal = arr.filter(e => e === pivot)
    const greater = arr.filter(e => e > pivot)

    return [].concat(quicksort(less), equal, quicksort(greater))
}


const test = [1, 2, 3, 4, 5, 7];
quicksort(test)//[1, 2, 3, 4, 5, 7]
```


