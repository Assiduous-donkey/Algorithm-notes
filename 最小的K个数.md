# 最小的K个数

输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。

## 常规思路

升序排序，然后取前K个数字。可以用快速排序也可以借助优先队列(时间复杂度为O(nlgn))

## 进阶思路————快速选择

采用类似快速排序的思路，取一个数然后将序列中小于它的数放到该数的左边，大于它的数放到该数的右边，更新数组。
根据有多少个数小于该数决定是否已经找到答案或者选择哪一个子序列继续查询。

### 具体代码

```cpp
class Solution {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        // 快速排序  215类似
        int left=0,right=input.size(),pivot;
        if(k==right) return input;
        if(k>right) return {};
        while(left<right){  // 查找的序列范围[left,right)
            pivot=quick_choose(input,left,right);
            if(pivot==k) break;
            else if(pivot<k) left=pivot+1;
            else right=pivot;
        }
        vector<int> res;
        for(int i=0;i<pivot;++i) res.push_back(input[i]);
        return res;
    }
    int quick_choose(vector<int> &input,int left,int right){
        // 以序列的第一个数为候选数
        // 将序列中小于等于该数的元素置于该数左边，大于的置于右边
        int pivot=left;
        for(int i=left+1;i<right;++i){
            if(input[i]<=candidate){    // 小于等于的数放在左边
                pivot+=1;
                swap(input[i],input[pivot]);
            }
        }
        swap(input[pivot],input[left]); // 以序列的第一个元素为分界线
        return pivot;
    }
};
```

### 平均时间复杂度

若每次都将查询范围缩小一半，则时间复杂度=$O(n+n/2+n/4+...+1)=O(2n-1)=O(n)$(等比数列求和的计算)
