###[1109. Dota2 参议院](#https://www.lintcode.com/problem/dota2-senate/leaderboard)
在Dota2的世界中，有两个党派：Radiant和Dire。
Dota2参议院由来自两党的参议员组成。现在，参议院想要对Dota2游戏的变化做出决定。对此变化的投票是基于回合的。在每一回合中，每位参议员都可以行使以下两项权利之一：

禁止一位参议员的权利：
一个参议员可以让另一位参议员在这次以及随后的所有回合中失去所有投票权。

宣布胜利：
如果这位参议员发现仍然有投票权的参议员都来自同一方，他可以宣布胜利并做出关于比赛变化的决定。

给出代表每个参议员所属党派的字符串。字符“R”和“D”分别代表Radiant方和Dire方。然后，如果有n个参议员，则给定字符串的大小将为n。

基于回合的过程从给定顺序的第一个参议员到最后一个参议员开始。此程序将持续到投票结束。在这个过程中，所有失去投票权的参议员都将被跳过。

假设每个参议员足够聪明并且将为自己的政党发挥最佳策略，你需要预测哪一方最终宣布胜利并使Dota2比赛进行改变。输出应为Radiant或Dire。

样例
样例 1:

输入: 
RD
输出: 
Radiant

解释:
第一个参议员来自Radiant ，他可以禁止下一个参议员在第一轮的权利。
第二个参议员不能再行使任何权利，因为他的权利已经被禁止了。
在第二轮选举中，第一个参议员可以宣布胜利，因为他是参议院中唯一可以投票的人。
样例 2:

输入: 
RDD
输出:
Dire

解释: 
第一位参议员来自Radiant，他可以在第一回合中禁止下一位参议员的权利。
由于他的权利被禁止，第二位参议员不再行使任何权利。
第三位参议员来自Dire，他可以在第一回合禁止第一位参议员的权利。
在第二回合中，第三位参议员可以宣布胜利，因为他是参议院中唯一可以投票的人。
注意事项
给出的字符串长度范围为[1, 10,000]。

####算法思路：我们可以用两个队列queue，把各自阵营的位置存入不同的队列里面，然后进行循环，每次从两个队列各取一个位置出来，看其大小关系，小的那个说明在前面，就可以把后面的那个Ban掉，所以我们要把小的那个位置要加回队列里面，但是不能直接加原位置，因为下一轮才能再轮到他来Ban，所以我们要加上一个n，再排入队列。这样当某个队列为空时，推出循环，我们返回不为空的那个阵营，参见代码如下：
```c++
class Solution {
public:
    /**
     * @param senate: a string
     * @return: return a string
     */
    string predictPartyVictory(string &senate) {
        // write your code here
       deque<int> rDq, dDq;
        for (int i = 0; i < senate.size(); i++) {
            senate[i] == 'R' ? rDq.push_back(i) : dDq.push_back(i);
        }
        while(!rDq.empty() && !dDq.empty()){
            int r = rDq.front(); rDq.pop_front();
            int d = dDq.front(); dDq.pop_front();
            r < d ?  rDq.push_back(r + senate.size()) : dDq.push_back(d +senate.size());
        }
      return  rDq.empty() ?   "Dire" :"Radiant";
    }
};
```