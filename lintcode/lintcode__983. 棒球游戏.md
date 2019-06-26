[983. 棒球游戏](#https://www.lintcode.com/problem/baseball-game/description)
  
  现在你是棒球比赛分记录员。
  给定一个字符串数组，每一个字符串可以是以下4种中的其中一个：
  整数 (一个回合的分数): 直接表示这回合你得到的分数。
  
  "+" (一个回合的分数): 表示这回合你获得的分数为前两个 有效 分数之和。
  
  "D" (一个回合的分数): 表示这回合你得到的分数为你上一次获得的有效分数的两倍。
  
  "C" (一种操作，而非一个回合的分数): 表示你上回合的有效分数是无效的，需要移除。
  
  每一轮的操作都是永久性的，可能会影响之前和之后的一轮。
你需要返回在所有回合中获得总分数。

样例
样例1:

输入: ["5","2","C","D","+"]
  
  输出: 30
  
  解释: 
  
  回合 1: 你可以得到 5 分，和为：5。
  
  回合 2: 你可以得到 2 分，和为：7。
  
  操作 1: 回合 2 的数据无效，所以和为 5。
  
  回合 3: 你可以得到 10 分（回合 2 的数据已经被移除了），和为：15。
  
  回合 4: 你可以得到 5 + 10 = 15 分，和为：30。
  
  样例2:

输入: ["5","-2","4","C","D","9","+","+"]
输出: 27
解释: 
  
  回合 1: 你可以得到 5 分，和为：5。
  
  回合 2: 你可以得到 -2 分，和为：3。
  
  回合 3: 你可以得到 4 分，和为：7。
  
  操作 1: 回合 3的数据无效，所以和为 3。
  
  回合 4: 你可以得到 -4 分(回合 3的数据已经 被移除了)，和为：-1。
  
  回合 5: 你可以得到 9 分，和为：8。
  
  回合 6: 你可以得到 -4 + 9 = 5 分，和为：13。
  
  回合 7: 你可以得到 9 + 5 = 14 分，和为27。
  
  注意事项
  
  输入列表的大小在 1 到 1000 之间。
  
  列表中的整数大小在 -30000 到 30000 之间。
  
  ```c++
  class Solution {
public:
    /**
     * @param ops: the list of operations
     * @return:  the sum of the points you could get in all the rounds
     */
    int calPoints(vector<string> &ops) {
        // Write your code here
        stack<int> stk;
        int sum = 0;
        for (int i = 0; i < ops.size(); i++) {
            if(ops[i] == "+"){
                int points1 = stk.top(); stk.pop();
                int points2 = stk.top();
                stk.push(points1);
                stk.push(points1 + points2);
                sum  += stk.top();
            }
            else if(ops[i] == "D"){
                stk.push(stk.top()*2);
                sum += stk.top();
            }
            else if(ops[i] == "C"){
                sum -= stk.top();
                stk.pop();
            }
            else{
                stk.push(stoi(ops[i]));
                sum += stk.top();
            }
            }
            return sum;
        }
};
  ```