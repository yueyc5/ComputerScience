[1206. 下一个更大的数 I](#https://www.lintcode.com/problem/next-greater-element-i/description)  
你有两个数组 nums1和nums2（互不重复），其中nums1是nums2的子集。 在nums2的相应位置找到nums1所有元素的下一个更大数字。

nums1中的数字x的下一个更大数字是nums2中x右边第一个更大的数字。 如果它不存在，则为此数字输出-1。

样例
例子 1:

输入: nums1 = [4,1,2], nums2 = [1,3,4,2].
输出: [-1,3,-1]
解释:
     对于第一个数组中的数字4，在第二个数组中找不到下一个更大的数字，因此输出-1。
     对于第一个数组中的数字1，第二个数组中的下一个更大数字是3。
     对于第一个数组中的数字2，第二个数组中没有下一个更大的数字，因此输出-1。
例子 2:

  
   
   输入: nums1 = [2,4], nums2 = [1,2,3,4].
  
  输出: [3,-1]
  
  解释:  
  对于第一个数组中的数字2，第二个数组中的下一个更大数字是3。  
   对于第一个数组中的数字4，第二个数组中没有下一个更大的数字，因此输出-1。
  
  注意事项
  
  1.nums1和nums2中的所有数字都是唯一的。
  
  2.nums1和nums2的长度不超过1000。
  
```c++
class Solution {
public:
    /**
     * @param nums1: an array
     * @param nums2: an array
     * @return:  find all the next greater numbers for nums1's elements in the corresponding places of nums2
     */
    vector<int> nextGreaterElement(vector<int> &nums1, vector<int> &nums2) {
        // Write your code here
        std::stack<int> s;
        unordered_map<int , int> help;
        vector<int> ret ;
        for (int i = 0; i < nums2.size(); i++) {
            while(!s.empty() &&  nums2[i] >nums2[s.top()]){
                int t = s.top();
                s.pop();
                help.insert(make_pair(nums2[t],nums2[i]));
            }
            s.push(i);
        }
        for (int i = 0; i < nums1.size(); i++) {
           if(help.count(nums1[i]))
            ret.push_back(help[nums1[i]]);
            else
            ret.push_back(-1);
        }
        return ret;
    }
};
```