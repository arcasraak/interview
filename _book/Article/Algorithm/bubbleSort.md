# 冒泡排序

冒泡排序（Bubble Sort）也是一种简单直观的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。

## 算法步骤

1. 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。
3. 针对所有的元素重复以上的步骤，除了最后一个。
4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

## javascript  array bubble sort code

```javascript
function bubbleSort(arr,foo=null){
    // 3. 每一遍循环都得到一个较大值，j的数值记录得到较大值的个数
    for(var j=0; j<arr.length-1; j++){ 
        // 2. 每一遍循环都得到一个较大值，所以下次循环可以不再处理那些较大值
        for (var i=0; i< arr.length-1-j; i++){ 
             // 1. 两个相邻的位置进行比较，如果前值大于后值，交换位置，循环一遍，较大值位置将后移
            if(arr[i] > arr[i+1]){  
                foo = arr[i]; 
                arr[i] = arr[i+1]; 
                arr[i+1] = foo; 
            }
        }
    }
    return arr;
}
```

## TypeScript  array bubble sort code


## ES6  array bubble sort code