###[621. 最大子数组 V](#https://www.lintcode.com/problem/maximum-subarray-v/solution)
给定一个整数数组，找到长度在 k1 与 k2 之间(包括 k1, k2)的子数组并且使它们的和最大，返回这个最大值，如果数组元素个数小于 k1 则返回 0

样例
样例 1:

输入:
[-2,2,-3,4,-1,2,1,-5,3]
2
4
输出:
6

解释:
连续子数组为 `[4,-1,2,1]` 时有最大和 `6`
样例 2:

输入:
[1,1,2,3]
5
10
输出:
0
注意事项
确保结果是一个整型
####题目解析：对于本题来说同样是需要维持长度为一定值的窗口，同时满足队列的特点，因此，这里使用双向队列来解决这个问题。这个题目的本质是维持一个单调递增的双向队列。
#### 首先，队列为空，元素下标直接进入队列，如果队列不为空的时候，首先判断当前元素的个数是否超过上限，如果超过上限值，那么我们需要做的就是将队列头部分元素出栈。接着我们需要判断长度为k1的时候的元素和的最大值和 长度为 i - dq.back()的元素的最大值的比较，`sum[dq.back()] >sum[i - k1]`,如果满足条件，那么说明`dq.back()+1 - i `不可能获取得到和的最大值，因此去掉这个部分。 由于我们是单调递增队列，因此，我们用当前元素值减去队列第一个元素位置所代表的值，就能够获取得到最大的和。
```c++
class Solution {
public:
    std::deque<int> dq ;
    /**
     * @param nums: an array of integers
     * @param k1: An integer
     * @param k2: An integer
     * @return: the largest sum
     */
    int maxSubarray5(vector<int> &nums, int k1, int k2) {
        // write your code here
        if(nums.size() <k1 )
            return 0;
        vector<long> sum(nums.size()+1, 0);
        long maxs = LONG_MIN;
        sum[0] = 0;//特殊处理第0个位置
        for (int i = 1; i <= nums.size(); i++) {
            sum[i] +=sum[i -1]+nums[i-1];
            if(!dq.empty() && (i - dq.front() >k2)){
                dq.pop_front();
            }
            if(i >=k1){
                while(!dq.empty()&&sum[dq.back()] >sum[i - k1]){
                    dq.pop_back();
                }
                dq.push_back(i - k1);
            }
            if(!dq.empty()){
                    maxs = max(maxs, sum[i] - sum[dq.front()]);
            }
        }
        return maxs;
    }
};//暴力解决方案 证明这个数组确实能够维持当前的最大值。
```