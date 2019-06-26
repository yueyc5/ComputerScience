[227. 用栈模拟汉诺塔问题](#https://www.lintcode.com/problem/mock-hanoi-tower-by-stacks/solution)
在经典的汉诺塔问题中，有 3 个塔和 N 个可用来堆砌成塔的不同大小的盘子。要求盘子必须按照从小到大的顺序从上往下堆 （如，任意一个盘子，其必须堆在比它大的盘子上面）。同时，你必须满足以下限制条件：

(1) 每次只能移动一个盘子。
(2) 每个盘子从堆的顶部被移动后，只能置放于下一个堆中。
(3) 每个盘子只能放在比它大的盘子上面。

请写一段程序，实现将第一个堆的盘子移动到最后一个堆中。

样例
样例1:

输入: 3
输出: 
towers[0]: []
towers[1]: []
towers[2]: [2,1,0]
样例2:

输入: 10
输出:
towers[0]: []
towers[1]: []
towers[2]: [9,8,7,6,5,4,3,2,1,0]
####答案解析：汉诺塔问题的解决方案如下：

####以目标杆为中介，从起始杆将1至n−1号盘移至中间杆  
####将起始杆里的第n号盘移动到目标杆
####以起始杆为中介，从中间杆将1至n−1号盘移至目标杆  
####这一个流程形成了一个递归过程，而我们要做的就是要利用栈去模拟这一个过程。栈具有后进先出的性质，这个和汉诺塔里塔的移动是一样的。

```c++
class Tower {
private:
    stack<int> disks;
public:
    /*
    * @param i: An integer from 0 to 2
    */
    Tower(int i) 
    {
        // create three towers
    }

    /*
     * @param d: An integer
     * @return: nothing
     */
    void add(int d) {
        // Add a disk into this tower
        if (!disks.empty() && disks.top() <= d)
        {
            printf("Error placing disk %d", d);
        } else {
            disks.push(d);
        }
    }
    /*
     * @param t: a tower
     * @return: nothing
     */
    void moveTopTo(Tower &t)
    {
        // Move the top disk of this tower to the top of t.
        int one = disks.top();
         disks.pop();
         t.add(one);
    }

    /*
     * @param n: An integer
     * @param destination: a tower
     * @param buffer: a tower
     * @return: nothing
     */
    void moveDisks(int n, Tower &destination, Tower &buffer)
    {
        // Move n Disks from this tower to destination by buffer tower
        if(n == 1)
        {
            moveTopTo(destination);
        }
        else
        {
            moveDisks(n -1 , buffer, destination);
            moveTopTo(destination);
            buffer.moveDisks(n -1 , destination, *this);
        }
    }
    /*
     * @return: Disks
     */
    stack<int> getDisks() 
    {
        // write your code here
        return disks;
    }
};

/**
 * Your Tower object will be instantiated and called as such:
 * vector<Tower> towers;
 * for (int i = 0; i < 3; i++) towers.push_back(Tower(i));
 * for (int i = n - 1; i >= 0; i--) towers[0].add(i);
 * towers[0].moveDisks(n, towers[2], towers[1]);
 * print towers[0], towers[1], towers[2]
*/
```
