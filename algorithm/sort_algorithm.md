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
