[题目链接](#https://www.lintcode.com/problem/sliding-window-maximum/description)  
362. 滑动窗口的最大值.  
给出一个可能包含重复的整数数组，和一个大小为 k 的滑动窗口, 从左到右在数组中滑动这个窗口，找到数组中每个窗口内的最大值。

样例
样例 1:

输入:
[1,2,7,7,8]
3
输出:
[7,7,8]

解释：
最开始，窗口的状态如下：`[|1, 2 ,7| ,7 , 8]`, 最大值为 `7`;
然后窗口向右移动一位：`[1, |2, 7, 7|, 8]`, 最大值为 `7`;
最后窗口再向右移动一位：`[1, 2, |7, 7, 8|]`, 最大值为 `8`.
样例 2:

输入:
[1,2,3,1,2,3]
5
输出:
[3,3]

解释:
最开始，窗口的状态如下： `[|1,2,3,1,2 | ,3]` , 最大值为`3`;
然后窗口向右移动一位.`[1, |2,3,1,2,3]`, 最大值为 `3`;
挑战
O(n)时间，O(k)的额外空间

输入测试数据 (每行一个参数)
如何理解测试数据？

```c++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int> nums, int k) {
        multiset<int, greater<int>> s;
        vector<int> r;
    	for (int i = 0; i < nums.size(); ++i) {
    		s.insert(nums[i]);
    		if (i >= k)
    			s.erase(s.find(nums[i - k]));
    		if (i >= k - 1)
    			r.push_back(*s.begin());
    	}
    	return r;
    }
};
```

```c++

/*
主要问题是如何获取当前区间的最大值，传统的方法是需要排序的，这个排序操作需要O（k）的代价
我们采用
*/
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int> nums, int k) {
       deque<int> dq ;
       vector<int> r;
        if(nums.size() < k || k<=0)
            return r;
    	for (int i = 0; i < nums.size(); ++i) {
    		while(!dq.empty() && (nums[dq.back()]< nums[i])){
    		    dq.pop_back();//0 
    		}
    		dq.push_back(i);
    		if(dq.front() == i - k){
    		    dq.pop_front();
    		}
    		if(i >= k -1){
    		    r.push_back(nums[dq.front()]);
    		}
    	}
    	return r;
    }
};
```