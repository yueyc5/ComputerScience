[490. 栈集](#https://www.lintcode.com/problem/set-of-stacks/description)
假如你有一堆的盘子。如果你堆得太高的话，就可能会垮掉。所以，在真实的生活中，如果盘子叠到一定高度，你会重新开始堆新的一堆盘子。

实现这样的一个数据结构，我们称之为栈集，来模拟这个过程。这个栈集包含若干个栈（可以理解为若干堆的盘子），如果一个栈满了，就需要新建一个栈来装新加入的项。你需要实现栈集的两个方法，push(item) 和 pop()，让这个栈集对外表现得就像是一个栈在进行操作一样。

样例
例1:

输入：
SetOfStacks(2);  // create a SetOfStacks object with capacity = 2
push(1)
push(2)
push(4)
push(8)
push(16)
pop() // return 16
pop() // return 8
pop() // return 4
例2:

输入：
SetOfStacks(5)  
push(1)
push(2)
push(3)
push(4)
push(5)
push(32)
pop()  // return 32
push(64)
pop()  // return 64

挑战
后续问题: 为栈集实现一个popAt(int index)的方法，让他可以直接从给定的某个栈上进行pop。

```c++
class SetOfStacks {
public:
    std::vector<int> res ;
    int len ;
    /*
    * @param capacity: An inetger, capacity of sub stack
    */SetOfStacks(int capacity) {
        // do intialization if necessary
        len = capacity;
    }

    /*
     * @param v: An integer
     * @return: nothing
     */
    void push(int v) {
        // write your code here
        res.push_back(v);
    }

    /*
     * @return: An integer
     */
    int pop() {
        // write your code here
        int t = *(res.end() -1 );
        res.erase(res.end() -1 );
    return t;
    }
};
```
