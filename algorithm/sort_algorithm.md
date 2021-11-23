# 冒泡排序
```c++
//时间复杂度O(n2)
//空间复杂度O(n)
void bubble_sort(int ar[], int size){
    int flag = 0;
    for(int i=0;i<size-2;++i){
        for(int j=0;j<size-i-i;++j){
            if(ar[j] > ar[j+1]){
                int temp = ar[j];
                ar[j] = ar[j+1];
                ar[j] = temp;
                flag = 1;
            }
        }
        if(flag == 0) return;
        flag = 0;
    }
}
```
# 简单选择排序
```c++
//时间复杂度O(n2)
//空间复杂度O(n)
void select_Sort(int ar[], int size){
    int min;
    int minNum;
    for(int i=1;i<size;++i){
        min = i-1;
        for(int j=i;j<size;++j){
            if(ar[j] < ar[min]) min = j;
        }
        if(min != i-1){
            minNum = ar[min];
            ar[min] = ar[i-1];
            ar[i-1] = minNum;
        }
    }
}
```

# 插入排序
```c++
//时间复杂度O(n2)
//空间复杂度o(n)
//比bubble和select稍好
void insert_Sort(int ar[], int size){
    for(int i=1;i<size;++i){
        if(ar[i] < ar[i-1]){
            int num = ar[i];
            int j=i-1;
            for(;j>=0 && ar[j] > num;--j){
                ar[j+1] = ar[j];
            }
            ar[j+1] = num;
        }
    }
}
```

## 快速排序
```c++
void swap(int arr[],int n1,int n2){
    int temp = arr[n1];
    arr[n1] = arr[n2];
    arr[n2] = temp;
}

int Partition(int arr[],int low,int hight){
    int pivotKey = arr[low];
    while(low < hight){
        while(low<hight && arr[hight] >= pivotKey) --hight;
        swap(arr, low, hight);
        while (low<hight && arr[low] <= pivotKey) ++low;
        swap(arr, low, hight);
    }
    return low;
}
void Qsort(int arr[],int low,int hight){
    if (low < hight) {
        int pivot = Partition(arr, low, hight);
        Qsort(arr, low, pivot-1);
        Qsort(arr, pivot+1, hight);
    }
}
void quick_sort(int arr[],int size){
    Qsort(arr,0,size);
}
```