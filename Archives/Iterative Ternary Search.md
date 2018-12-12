# Iterative Ternary Search

```
>>> 输入

>>> left right

>>> while left < right
	>>> take arr[left] arr[right] if target eq return
	>>> if leftThrid <= target left = leftThird
	>>> else right = rightThrid
```

> 左界取数组的左边三分之一 右界取数组右边的三分之一
>
> 如果结果比左界大
>
> 将左界向右移动
>
> 如果结果比左界小
>
> 将右界向左移动