###[226. 栈集II](#https://www.lintcode.com/problem/set-of-stacks-ii/description)
假如你有一堆的盘子。如果你堆得太高的话，就可能会垮掉。所以，在真实的生活中，如果盘子叠到一定高度，你会重新开始堆新的一堆盘子。

实现这样的一个数据结构，我们称之为栈集，来模拟这个过程。这个栈集包含若干个栈（可以理解为若干堆的盘子），如果一个栈满了，就需要新建一个栈来装新加入的项。你需要实现栈集的两个方法，push(item) 和 pop()，让这个栈集对外表现得就像是一个栈在进行操作一样。

另外，你还需要多实现一个popAt(index)的方法，该方法可以pop栈集中指定的栈的栈顶元素。并且，该方法执行后，除了，最后一个栈可以不满以外，其他的栈仍然需要保持是满的。

样例
样例1

输入:
SetOfStacks(2)
push(1)
push(2)
push(4)
push(8)
push(16)
popAt(0) 
popAt(0) 
pop()    

输出:
[2,4,16]

解释:
起初，堆栈0是[1,2]。 当popAt(0)时, 我们得到2然后栈变成[1,4]. 
在整个栈里，我们应该pop16。
样例2

输入:
SetOfStacks2(5)
push(1)
push(2)
push(3)
push(4)
push(5)
push(32)
pop()
push(64)
popAt(1)

输出:
[32,64]

说明:
开始栈0是[32]. 当pop的时候我们取出32然后栈变空了。
然后我们放入64，它被放在栈1，我们 popAt(1) 取出它。
挑战
如何在popAt之后只让最后一个栈不满，而其他的栈都满？并保持原有的栈集中元素应有的相对顺序？   

####题解：本题的唯一问题在于确定要出栈的是哪一个栈，我们根据help数组的大小，可以计算出满栈的个数。在这个基础上减去1就是最大的栈的下标。如果index的值比最大的下标大，那么就可以直到取得的是最后一个栈上的元素。如果小的话，直接找到相应的栈就可以了。因为每个栈的元素个数是确定的，我们又知道每个栈的索引编号，那么我们可以通过`help.begin() + index*len + len -1`找到需要出栈的元素。由于我们使用的是vector这意味着我们不需要手动维护所谓的栈空和栈满。


```c++
class SetOfStacks2 {
public:
    vector<int> help ;
    int len ;
    /*
    * @param capacity: an inetger, capacity of sub stack
    */SetOfStacks2(int capacity) {
        // do intialization if necessary
        len = capacity;
    }

    /*
     * @param v: An integer
     * @return: nothing
     */
    void push(int v) {
        // write your code here
        help.push_back(v);
    }

    /*
     * @return: An integer
     */
    int pop() {
        // write your code here
    int t = *(help.end() -1);
        help.erase(help.end() - 1);
     return t;
    }

    /*
     * @param index: The index of a specific sub-stack
     * @return: top element of the specific sub-stack
     */
    int popAt(int index) {
        // write your code here
    int number = help.size()/len -1;//point one
    if(index > number ){
         int t = *(help.end() -1);
        help.erase(help.end() - 1);
     return t;
    }
    else
    {
        int t = *(help.begin() + len * index +len -1);
         help.erase(help.begin() + len * index +len -1);
        return t;
    }
    }
};

```