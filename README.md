# LeetcodeDaily
记录刷leetcode的日常
先从简单难度开始按标签刷起。

## 关于栈的题目
栈（Stack）是后进先出的列表，常用操作有push（添加元素)、peek（返回栈顶元素）、pop（弹出栈顶元素）、isEmpty（判读栈是否为空）。

### 496. 下一个更大元素I
给定两个没有重复元素的数组 nums1 和 nums2 ，其中nums1 是 nums2 的子集。找到 nums1 中每个元素在 nums2 中的下一个比其大的值。
nums1 中数字 x 的下一个更大元素是指 x 在 nums2 中对应位置的右边的第一个比 x 大的元素。如果不存在，对应位置输出-1。

解法：
通过Stack、HashMap解决
先遍历大数组nums2，首先将第一个元素入栈；
继续遍历，当当前元素小于栈顶元素时，继续将它入栈；当当前元素大于栈顶元素时，栈顶元素出栈，此时应将该出栈的元素与当前元素形成key-value键值对，存入HashMap中；
当遍历完nums2后，得到nums2中元素所对应的下一个更大元素的hash表；
遍历nums1的元素在hashMap中去查找‘下一个更大元素’，当找不到时则为-1。

```Java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        int[] res = new int[nums1.length];
        Stack<Integer> stack = new Stack<>();
        Map<Integer, Integer> map = new HashMap<>();
        
        for (int num : nums2) {
            while(!stack.isEmpty() && stack.peek() < num){
                map.put(stack.pop(), num);
            }
            stack.push(num);
        }
        
        for (int i = 0; i < nums1.length; i++) {
            res[i] = map.getOrDefault(nums1[i], -1);   
        }
        
        return res;
    }
}
```

### 682. 棒球比赛
你现在是棒球比赛记录员。
给定一个字符串列表，每个字符串可以是以下四种类型之一：
* 整数（一轮的得分）：直接表示您在本轮中获得的积分数。
* "+"（一轮的得分）：表示本轮获得的得分是前两轮有效 回合得分的总和。
* "D"（一轮的得分）：表示本轮获得的得分是前一轮有效 回合得分的两倍。
* "C"（一个操作，这不是一个回合的分数）：表示您获得的最后一个有效 回合的分数是无效的，应该被移除。

每一轮的操作都是永久性的，可能会对前一轮和后一轮产生影响。
你需要返回你在所有回合中得分的总和。

解法：
让我们在处理数据时保持栈上每个有效回合的值。栈是理想的，因为我们只处理涉及最后或倒数第二轮的操作。

```Java
class Solution {
    public int calPoints(String[] ops) {
        Stack<Integer> stack = new Stack();

        for(String op : ops) {
            if (op.equals("+")) {
                int top = stack.pop();
                int newtop = top + stack.peek();
                stack.push(top);
                stack.push(newtop);
            } else if (op.equals("C")) {
                stack.pop();
            } else if (op.equals("D")) {
                stack.push(2 * stack.peek());
            } else {
                stack.push(Integer.valueOf(op));
            }
        }

        int ans = 0;
        for(int score : stack) ans += score;
        return ans;
    }
}
```

### 232. 用栈实现队列
使用栈实现队列的下列操作：

push(x) -- 将一个元素放入队列的尾部。
pop() -- 从队列首部移除元素。
peek() -- 返回队列首部的元素。
empty() -- 返回队列是否为空。

解法：
用两个栈来s1, s2来实现，push时将元素压入s1，pop时若s2不为空，返回s2.pop()，否则将s1的元素全弹入s2，此时s2栈顶元素就是最先加入的元素。peek操作与pop相似。用一个变量size记录队列的元素个数，size等于0时栈为空。

```Java
class MyQueue {
    int size;
    Stack<Integer> stack1;
    Stack<Integer> stack2;
    /** Initialize your data structure here. */
    public MyQueue() {
        size = 0;
        stack1 = new Stack<>();
        stack2 = new Stack<>();
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        stack1.push(x);
        size++;
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        if (!stack2.isEmpty()) {
            size--;
            return stack2.pop();
        } else {
            while (!stack1.isEmpty()) {
                stack2.push(stack1.pop());
            }
            size--;
            return stack2.pop(); 
        }
               
    }
    
    /** Get the front element. */
    public int peek() {
       if (!stack2.isEmpty()) {
            return stack2.peek();
        } else {
            while (!stack1.isEmpty()) {
                stack2.push(stack1.pop());
            }
            return stack2.peek(); 
        }
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return size == 0;
    }
}
```

