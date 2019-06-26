[423. 有效的括号序列](#https://www.lintcode.com/problem/valid-parentheses/description)  

给定一个字符串所表示的括号序列，包含以下字符： '(', ')', '{', '}', '[' and ']'， 判定是否是有效的括号序列。

括号必须依照 "()" 顺序表示， "()[]{}" 是有效的括号，但 "([)]" 则是无效的括号。

样例
样例 1：

输入："([)]"
输出：False
样例 2：

输入："()[]{}"
输出：True
挑战
O(n)的时间，n 为括号的个数。

```c++
class Solution {
public:
    stack<char> stk;
    /**
     * @param s: A string
     * @return: whether the string is a valid parentheses
     */
    bool isValidParentheses(string s) {
        // write your code here
        if(s == "")
            return true;
        for (int i = 0; i < s.size(); i++){
            if (s[i] == '(' || s[i] == '['||s[i] == '{'){
                stk.push(s[i]);
            }
            else{
                 char c;
                if(!stk.empty()){
                       c = stk.top();
                       stk.pop();// 
                   }
                else
                    return false;
                
                if ((s[i] == ')' && c == '(')||(s[i] == ']' && c == '[')||(s[i] == '}' && c == '{')) {
                    continue;
                }
                return false;
            }
            
        }
        if (stk.empty()){
            return true;
        }
        return false;
    }
};

```

