# leetcode题解
leetcode部分题目题解

## 栈的题目
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

每一轮的操作都是永久性的，可能会对前一轮和后一轮产生影响。你需要返回你在所有回合中得分的总和。

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

* push(x) -- 将一个元素放入队列的尾部。
* pop() -- 从队列首部移除元素。
* peek() -- 返回队列首部的元素。
* empty() -- 返回队列是否为空。

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
### 225. 用队列实现栈
使用队列实现栈的下列操作：  
* push(x) -- 元素 x 入栈  
* pop() -- 移除栈顶元素  
* top() -- 获取栈顶元素  
* empty() -- 返回栈是否为空  

解法：  
用两个队列q1，q2实现，当有新元素要push时，将q2元素进入q1，再进入新元素，再把暂时存储在q1的元素进入q2。

```Java
class MyStack {
    Queue<Integer> q1;
    Queue<Integer> q2;
    /** Initialize your data structure here. */
    public MyStack() {
        q1 = new LinkedList<>();
        q2 = new LinkedList<>();
    }
    
    /** Push element x onto stack. */
    public void push(int x) {
       while(!q2.isEmpty()) {
            q1.offer(q2.poll());
        }
        q2.offer(x);
        while(!q1.isEmpty()) {
            q2.offer(q1.poll());
        }
    }
    
    /** Removes the element on top of the stack and returns that element. */
    public int pop() {
        return q2.poll();
    }
    
    /** Get the top element. */
    public int top() {
        return q2.peek();
    }
    
    /** Returns whether the stack is empty. */
    public boolean empty() {
        return q2.isEmpty();
    }
}
```
### 155. 最小栈
设计一个支持 push，pop，top 操作，并能在常数时间内检索到最小元素的栈。

* push(x) -- 将元素 x 推入栈中。
* pop() -- 删除栈顶的元素。
* top() -- 获取栈顶元素。
* getMin() -- 检索栈中的最小元素。

解法：
用两个栈，一个栈正常操作，另一个栈存储当前最小值。

```Java
class MinStack {
    Stack<Integer> s1;
    Stack<Integer> s2;
    /** initialize your data structure here. */
    public MinStack() {
        s1 = new Stack<>();
        s2 = new Stack<>();
    }
    
    public void push(int x) {
        s1.push(x);
        if (s2.isEmpty() || x < s2.peek()) {
            s2.push(x);
        } else {
            s2.push(s2.peek());
        }
    }
    
    public void pop() {
        s1.pop();
        s2.pop();
    }
    
    public int top() {
        return s1.peek();
    }
    
    public int getMin() {
        return s2.peek();
    }
}
```
### 844. 比较含退格的字符串
给定 S 和 T 两个字符串，当它们分别被输入到空白的文本编辑器后，判断二者是否相等，并返回结果。 # 代表退格字符。

解法：
遇到“#”便弹栈。
```Java
class Solution {
    public boolean backspaceCompare(String S, String T) {
        Stack<Character> ss = new Stack<Character>();
        Stack<Character> ts = new Stack<Character>();
        
        for(int i = 0; i < S.length(); i++) {
            if(S.charAt(i) != '#')
                ss.push(S.charAt(i));      
            else if(!ss.isEmpty())
                  ss.pop(); 
        }
        
        for(int i = 0; i < T.length(); i++){
            if(T.charAt(i) != '#')
                ts.push(T.charAt(i)); 
            else if(!ts.isEmpty())
                ts.pop();
        }
        
        return ss.equals(ts);
    }
}
```

### 20. 有效的括号
给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。  
解法：
初始化栈 S。  
依次次处理表达式的每个括号。  
如果遇到开括号，我们只需将其推到栈上即可。这意味着我们将稍后处理它，让我们简单地转到前面的 子表达式。  
如果我们遇到一个闭括号，那么我们检查栈顶的元素。如果栈顶的元素是一个 相同类型的 左括号，那么我们将它从栈中弹出并继续处理。否则，这意味着表达式无效。  
如果到最后我们剩下的栈中仍然有元素，那么这意味着表达式无效。  

```Java
class Solution {
    public boolean isValid(String s) {
        Map<Character, Character> map = new HashMap<>();
        map.put(')', '(');
        map.put(']', '[');
        map.put('}', '{');
        
        Stack<Character> stack = new Stack<>();
        
        for (int i = 0; i < s.length(); i++) {
            if (map.containsKey(s.charAt(i))) {
                if (stack.isEmpty()) return false;
                else if (map.get(s.charAt(i)) != stack.peek()) return false;
                else stack.pop();
            } else {
                stack.push(s.charAt(i));
            }
        }
        
        return stack.isEmpty();
    }
}
```

## 堆的题目
目前还不太懂，先跳过。

## 贪心算法

### 944. 删列造序

给定由 N 个小写字母字符串组成的数组 A，其中每个字符串长度相等。

选取一个删除索引序列，对于 A 中的每个字符串，删除对应每个索引处的字符。 所余下的字符串行从上往下读形成列。

比如，有 A = ["abcdef", "uvwxyz"]，删除索引序列 {0, 2, 3}，删除后 A 为["bef", "vyz"]， A 的列分别为["b","v"], ["e","y"], ["f","z"]。（形式上，第 n 列为 [A[0][n], A[1][n], ..., A[A.length-1][n]]）。

假设，我们选择了一组删除索引 D，那么在执行删除操作之后，A 中所剩余的每一列都必须是 非降序 排列的，然后请你返回 D.length 的最小可能值。

解法：

需要把未排序的列全删除。
```Java
class Solution {
    public int minDeletionSize(String[] A) {
        int res = 0;
        for (int i = 0; i < A[0].length(); i++) {
            for (int j = 0; j < A.length-1; j++){
                if (A[j].charAt(i) > A[j+1].charAt(i)) {
                    res++;
                    break;
                }
            }
        }

        return res;
    }
}
```

### 122. 买股票的最佳时机II

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

解法：

只要当天价格比前一天的高，就把差价给加上。

```Java
class Solution {
    public int maxProfit(int[] prices) {
        int max = 0;
        for (int i = 1; i < prices.length; i++) {
            if (prices[i] > prices[i-1]) {
                max += (prices[i] - prices[i-1]);
            }
        }
        return max;
    }
}
```

### 860. 柠檬树找钱
在柠檬水摊上，每一杯柠檬水的售价为 5 美元。

顾客排队购买你的产品，（按账单 bills 支付的顺序）一次购买一杯。

每位顾客只买一杯柠檬水，然后向你付 5 美元、10 美元或 20 美元。你必须给每个顾客正确找零，也就是说净交易是每位顾客向你支付 5 美元。

注意，一开始你手头没有任何零钱。

如果你能给每位顾客正确找零，返回 true ，否则返回 false 。

解法：

情景模拟，记录5、10美元的数量，收到20美元优先找10美元，当5美元找完后返回false。

```Java
class Solution {
    public boolean lemonadeChange(int[] bills) {
        int five = 0, ten = 0;
        for (int bill: bills) {
            if (bill == 5)
                five++;
            else if (bill == 10) {
                if (five == 0) return false;
                five--;
                ten++;
            } else {
                if (five > 0 && ten > 0) {
                    five--;
                    ten--;
                } else if (five >= 3) {
                    five -= 3;
                } else {
                    return false;
                }
            }
        }

        return true;
    }
}
```

### 455. 分发饼干

假设你是一位很棒的家长，想要给你的孩子们一些小饼干。但是，每个孩子最多只能给一块饼干。对每个孩子 i ，都有一个胃口值 gi ，这是能让孩子们满足胃口的饼干的最小尺寸；并且每块饼干 j ，都有一个尺寸 sj 。如果 sj >= gi ，我们可以将这个饼干 j 分配给孩子 i ，这个孩子会得到满足。你的目标是尽可能满足越多数量的孩子，并输出这个最大数值。

解法：

对饼干尺寸进行排序，优先给孩子满足胃口的最小的饼干。

```Java
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        int res = 0;
        boolean[] given = new boolean[s.length];
        Arrays.sort(s);
        
        for (int i = 0; i < g.length; i++) {
            for (int j = 0; j < s.length; j++) {
                if (s[j] >= g[i] && !given[j]) {
                    res++;
                    given[j] = true;
                    break;
                }
            }
        }
        
        return res;
    }
}
```

### 874.行走机器人

机器人在一个无限大小的网格上行走，从点 (0, 0) 处开始出发，面向北方。该机器人可以接收以下三种类型的命令：

* -2：向左转 90 度
* -1：向右转 90 度
* 1 <= x <= 9：向前移动 x 个单位长度
在网格上有一些格子被视为障碍物。

第 i 个障碍物位于网格点  (obstacles[i][0], obstacles[i][1])

如果机器人试图走到障碍物上方，那么它将停留在障碍物的前一个网格方块上，但仍然可以继续该路线的其余部分。

返回从原点到机器人的最大欧式距离的平方。

解法：

情景模拟，用两个数组dx，dy来控制行走方向，障碍物位置用集合存储来方便查找。

```Java
class Solution {
    public int robotSim(int[] commands, int[][] obstacles) {
        int[] dx = new int[]{0, 1, 0, -1};
        int[] dy = new int[]{1, 0, -1, 0};
        int x = 0, y = 0, di = 0;

        // Encode obstacles (x, y) as (x+30000) * (2^16) + (y+30000)
        Set<Long> obstacleSet = new HashSet();
        for (int[] obstacle: obstacles) {
            long ox = (long) obstacle[0] + 30000;
            long oy = (long) obstacle[1] + 30000;
            obstacleSet.add((ox << 16) + oy);
        }

        int ans = 0;
        for (int cmd: commands) {
            if (cmd == -2)  //left
                di = (di + 3) % 4;
            else if (cmd == -1)  //right
                di = (di + 1) % 4;
            else {
                for (int k = 0; k < cmd; ++k) {
                    int nx = x + dx[di];
                    int ny = y + dy[di];
                    long code = (((long) nx + 30000) << 16) + ((long) ny + 30000);
                    if (!obstacleSet.contains(code)) {
                        x = nx;
                        y = ny;
                        ans = Math.max(ans, x*x + y*y);
                    }
                }
            }
        }

        return ans;
    }
}
```

## 排序

### 349.两个数组的交集

给定两个数组，编写一个函数来计算它们的交集。

解法：

没看出来怎么用排序解。用字典存num1的元素，键的值为1。再遍历nums2，键值--，若键值小于1说明该键在nums2中出现了。

```Java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Map<Integer, Integer>  map = new HashMap<>();
        for (int num:nums1) {
            map.put(num, 1);
        }
        
        List<Integer> nums = new ArrayList<>();
        for (int num:nums2) {
            if (map.containsKey(num) && map.get(num) > 0) {
                map.put(num, map.get(num)-1);
                nums.add(num);
            }
        }
        
        int[] res = new int[nums.size()];
        for (int i = 0; i < nums.size(); i++) {
            res[i] = nums.get(i);
        }
        
        return res;
    }
}
```

### 350. 两个数组的交集II

和上提一样，只是多了个统计次数。

```Java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int num:nums1) {
            if (map.containsKey(num)) {
                map.put(num, map.get(num)+1);
            } else {
                map.put(num, 1);
            }
        }
        
        List<Integer> nums = new ArrayList<>();
        for (int num:nums2) {
            if (map.containsKey(num) && map.get(num) > 0) {
                map.put(num, map.get(num)-1);
                nums.add(num);
            }
        }
        
        int[] res = new int[nums.size()];
        for (int i = 0; i < nums.size(); i++) {
            res[i] = nums.get(i);
        }
        
        return res;
    }
}
```
### 有效的字母异位词

给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的一个字母异位词。字母异为词就是包含的字母一样，顺序可以不同。

解法：

先判断两个词长度是否相同，再对字母进行排序后比较。

```Java
class Solution {
    public boolean isAnagram(String s, String t) {
        if(s.length() != t.length()) return false;
        char[] as = s.toCharArray();
        char[] ts = t.toCharArray();
        Arrays.sort(as);
        Arrays.sort(ts);
        return Arrays.equals(as, ts);
    }
}
```

## 关于树的题目

树的题目有点多，有得刷了。
### 104.二叉树的最大深度

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

解法：

利用递归，树的高度等于左子树的高度加1或右子树的高度加1。叶节点高度为1。

```Java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null)
            return 0;
        if (root.left == null && root.right == null)
            return 1;
        return Math.max(1 + maxDepth(root.left), 1 + maxDepth(root.right));
    }
}
```

### 226.翻转二叉树

翻转一棵二叉树。

示例：

输入：
     4
   /   \
  2     7
 / \   / \
1   3 6   9
输出：
     4
   /   \
  7     2
 / \   / \
9   6 3   1

解法：

递归。翻转后的二叉树的左子树是右子树的翻转二叉树，右子树是左子树的翻转二叉树。

```Java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null)
            return root;
        if (root.left == null && root.right == null)
            return root;
        TreeNode left = root.left;
        TreeNode right = root.right;
        
        root.left = invertTree(right);
        root.right = invertTree(left);
        
        return root;
    }
}
```

### 589.n叉树的前序遍历

给定一个 N 叉树，返回其节点值的后序遍历。

解法：

用栈，先从右开始把孩子存入栈中，之后栈顶元素便是最左边的孩子，弹出栈顶元素并将其孩子入栈。重复直到栈空。

```Java
class Solution {
    public List<Integer> preorder(Node root) {
        List<Integer> res = new ArrayList<Integer>();
        if (root == null)
            return res;
        
        Stack<Node> stack = new Stack<>();  
        stack.push(root);
        
        while (!stack.isEmpty()) {
            Node cur = stack.pop();
            res.add(cur.val);
            if (!cur.children.isEmpty()) {
                for (int i = cur.children.size()-1; i >= 0; i--) {
                    stack.push(cur.children.get(i));
                }
            }
        }
        
        return res;
    }
}
```

### 700.二叉树中的搜索

给定二叉搜索树（BST）的根节点和一个值。 你需要在BST中找到节点值等于给定值的节点。 返回以该节点为根的子树。 如果节点不存在，则返回 NULL。

例如，

给定二叉搜索树:

        4
       / \
      2   7
     / \
    1   3

和值: 2
你应该返回如下子树:

      2     
     / \   
    1   3
    
    
解法：

利用二叉搜索树节点左孩子值小于节点，右孩子节点值大于节点的性质来加快效率。

```Java
class Solution {
    public TreeNode searchBST(TreeNode root, int val) {
        if (root == null) {
            return null;
        }

        if (root.val == val) {
            return root;
        } else if (root.val > val) { // 在左子树中查找
            return searchBST(root.left, val);
        } else { // 在右子树中查找
            return searchBST(root.right, val);
        }
    }
}
```
