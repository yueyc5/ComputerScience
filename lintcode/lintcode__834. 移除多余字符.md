[834. 移除多余字符](#https://www.lintcode.com/problem/remove-duplicate-letters/description)
  
  给定一个字符串s由小写字符组成，移除多余的字符使得每个字符只出现一次。你必须保证结果是字典序是最小的合法字符串。

样例
样例1

输入: s = "bcabc"
  
  输出: "abc"
样例2

输入: s = "cbacdcbc"
  
 输出: "acdb"
####算法思路：用一个栈来维护答案，从左往右扫描字符串，当栈顶元素字典序小于当前扫描的字符，并且栈顶元素在s未被扫描到的部分中还有出现时，栈顶元素出栈，并继续比较新的栈顶元素与当前字符字符，重复上面的过程，直到不符合上述条件时，再让当前字符入栈。最后答案就是栈底到栈顶元素组成的字符串。
 
 ```c++
 class Solution {
public:
    /**
     * @param s: a string
     * @return: return a string
     */
    string removeDuplicateLetters(string &s) {
        // write your code here
    vector<int> countNumber(26,0);
    vector<bool> isInString(26,false);
    stack<int> stk;
    string res ="";
    for (int i = 0; i < s.size(); i++) {
        countNumber[s[i] - 'a'] ++;
    }
    for (int i = 0; i < s.size(); i++) {
        int index = s[i] - 'a';
        countNumber[index]--;
        if(isInString[index]) continue;
        while(!stk.empty() && stk.top() > index && countNumber[stk.top()] >0){
            isInString[stk.top()] = false;
            stk.pop();
        }
        stk.push(index);
        isInString[index] = true;
    }
   while(!stk.empty()){//point 1 the value sequence is reverse with our aim
       int c = stk.top();
       stk.pop();
       res = char(c +'a') + res; 
   }
       return res; 
}
};

 ```