# 和为S的连续正数序列

给定一个整数S，输出所有可能的和为S的连续整数序列。
如若S=100，则序列[18,19,20,21,22]满足要求。
输出所有和为S的连续正数序列。序列内按照从小至大的顺序，序列间按照开始数字从小到大的顺序。

## 穷举算法————滑动窗口

由于要记录序列的内容，且需要从序列头部添加元素以及从序列尾部添加元素，所以采用deque。

```cpp
class Solution {
public:
    vector<vector<int> > FindContinuousSequence(int sum) {
        // 滑动窗口
        vector<vector<int>> res;
        if(sum<=2) return res;
        deque<int> seq; // 模拟滑动窗口
        int left=1,right=(sum+1)/2,seq_sum=0;
        while(left<=right){ // 可取的区间范围
            seq_sum+=left;
            seq.push_back(left);
            if(seq_sum<sum) left+=1;
            else if(seq_sum==sum){
                vector<int> one_of_seq;
                for(int i=0;i<seq.size();++i) one_of_seq.push_back(seq[i]);
                res.push_back(one_of_seq);
                seq_sum-=seq[0];
                seq.pop_front();
                left+=1;
            }
            else{
                while(!seq.empty()&&seq_sum>sum){
                    seq_sum-=seq[0];
                    seq.pop_front();
                }
                if(seq.empty()) break;
                seq_sum-=seq.back(),seq.pop_back();
            }
        }
        return res;
    }
};

```

## 双指针

利用连续序列和的计算公式：[left,right]的和=(left+right)*(right-left+1)/2;

```cpp
class Solution {
public:
    vector<vector<int> > FindContinuousSequence(int sum) {
        // 滑动窗口
        vector<vector<int>> res;
        if(sum<=2) return res;
        int left=1,right=2;
        while(left<right){
            int cur_sum=(left+right)*(right-left+1)/2;
            if(cur_sum==sum){
                res.push_back({});
                for(int i=left;i<=right;++i) res.back().push_back(i);
                left+=1;
            }
            else if(cur_sum<sum) right+=1;
            else left+=1;
        }
        return res;
    }
};
```
