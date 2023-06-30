# LeetCode每日一题

## 链表

### 2023/6/2

给你两个 非空 链表来代表两个非负整数。数字最高位位于链表开始位置。它们的每个节点只存储一位数字。将这两数相加会返回一个新的链表。

你可以假设除了数字 0 之外，这两个数字都不会以零开头。

思路：

因为相加是从低位向高位相加，因此使用栈的数据结构对两个链表先处理。后来相加的时候先低位加，添加链表的时候使用头插法

~~~ java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        Stack<Integer> stack1 = new Stack<>();
        Stack<Integer> stack2 = new Stack<>();
        while (l1 != null) {
            stack1.push(l1.val);
            l1 = l1.next;
        }
        while (l2 != null) {
            stack2.push(l2.val);
            l2 = l2.next;
        }
        
        int carry = 0;
        ListNode head = null;
        while (!stack1.isEmpty() || !stack2.isEmpty() || carry > 0) {
            int sum = carry;
            sum += stack1.isEmpty()? 0: stack1.pop();
            sum += stack2.isEmpty()? 0: stack2.pop();
            ListNode node = new ListNode(sum % 10);
            node.next = head;
            head = node;
            carry = sum / 10;
        }
        return head;
    }
}
~~~

### 2023/6/4

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

![image-20230604131435360](C:\Users\18485\AppData\Roaming\Typora\typora-user-images\image-20230604131435360.png)

维护一个最小堆优先级队列，堆里元素为ListNode，优先级由ListNode.val来判断

~~~ java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {

        if (lists.length == 0) {
            return null;
        }

        ListNode dummyHead = new ListNode(0);
        ListNode curr = dummyHead;
        PriorityQueue<ListNode> pq = new PriorityQueue<>(new Comparator<ListNode>() {
            @Override
            public int compare(ListNode o1, ListNode o2) {
                return o1.val - o2.val;
            }
        });

        for (ListNode list : lists) {
            if (list == null) {
                continue;
            }
            pq.add(list);
        }

        while (!pq.isEmpty()) {
            ListNode nextNode = pq.poll();
            curr.next = nextNode;
            curr = curr.next;
            if (nextNode.next != null) {
                pq.add(nextNode.next);
            }
        }
        return dummyHead.next;
    }
}
~~~

给你一个链表的头节点 head 和一个特定值 x ，请你对链表进行分隔，使得所有 小于 x 的节点都出现在 大于或等于 x 的节点之前。

你应当 保留 两个分区中每个节点的初始相对位置。

维护两个链表。一个存小于，另一个存大于等于

~~~ java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode partition(ListNode head, int x) {
        ListNode dummyHead1 = new ListNode(0);
        ListNode dummyHead2 = new ListNode(0);
        ListNode node1 = dummyHead1;
        ListNode node2 = dummyHead2;
        while (head != null) {
            if (head.val < x) {
                node1.next = head;
                node1 = node1.next;
            } else {
                node2.next = head;
                node2 = node2.next;
            }
            head = head.next;
        }
        node1.next = dummyHead2.next;
        node2.next = null;
        return dummyHead1.next;
    }
}
~~~

## 哈希

### 2023/6/4

给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

请你设计并实现时间复杂度为 O(n) 的算法解决此问题。

首先将所有元素丢入到一个集合中，然后遍历集合中的元素，如果不包含它前一个元素，就开始计算，否则则跳出循环

~~~ java
class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> num_set = new HashSet<Integer>();
        for (int num : nums) {
            num_set.add(num);
        }

        int longestStreak = 0;

        for (int num : num_set) {
            if (!num_set.contains(num - 1)) {
                int currentNum = num;
                int currentStreak = 1;

                while (num_set.contains(currentNum + 1)) {
                    currentNum += 1;
                    currentStreak += 1;
                }

                longestStreak = Math.max(longestStreak, currentStreak);
            }
        }

        return longestStreak;
    }
}
~~~

### 2023/6/25

给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回 *该数组中和为 `k` 的连续子数组的个数* 。

思路：

通过建立前缀和，如果两个前缀和相差为k，则有解。通过hash实现查找。

~~~java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int count = 0,pre = 0;
        int length = nums.length;
        Map<Integer,Integer>map = new HashMap<>();
        map.put(0,1);//value为出现的次数
        for(int i = 0;i < lenght;i++){
            pre += nums[i];
            if(map.containKey(pre-k)){
                count += map.get(pre - k);
            }
            map.put(pre,map.getOrDefault(pre,0)+1);
        }
        return count;
    }
}
~~~

## 栈

### 2023/6/29 30

#### 单调栈

nums1 中数字 x 的 下一个更大元素 是指 x 在 nums2 中对应位置 右侧 的 第一个 比 x 大的元素。

给你两个 没有重复元素 的数组 nums1 和 nums2 ，下标从 0 开始计数，其中nums1 是 nums2 的子集。

对于每个 0 <= i < nums1.length ，找出满足 nums1[i] == nums2[j] 的下标 j ，并且在 nums2 确定 nums2[j] 的 下一个更大元素 。如果不存在下一个更大元素，那么本次查询的答案是 -1 。

返回一个长度为 nums1.length 的数组 ans 作为答案，满足 ans[i] 是如上所述的 下一个更大元素 。

思路：

利用单调栈法，从后向前遍历nums2数组。在遍历的同时栈中为单调递增的且比当前元素大，如果当前栈为空就像hashmap中压入-1。

~~~java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Stack<Integer>stack = new Stack<Integer>();
        Map<Integer,Integer>map = new HashMap<>();
        int length = nums2.length;
        for(int i = length - 1;i >=0 ;i--){
            int nums = nums2[i];
            while(!stack.isEmpty()&&stack.peek()<num){
                stack.pop();
            }
            map.put(nums,stack.isEmpty()?-1:stack.peek());
            stack.push(num);
        }
        int res[] = new int[nums1.length];
        for(int i = 0 ;i < nums1.length;i++){
            res[i] = map.get(nums[i]);
        }
        return res;
    }
}
~~~

#### [84. 柱状图中最大的矩形](https://leetcode.cn/problems/largest-rectangle-in-histogram/)

给定 *n* 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

求在该柱状图中，能够勾勒出来的矩形的最大面积。

思路：

遍历高度数组中的每一个元素，求出按每个元素当高度的面积。需算出每个元素的边界。边界是找到左右两边首次第于该高度的数组下标。通过单调栈法维护一个由栈顶单调递减的栈，找到首次低于其的下标。

~~~java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int n = heights.length;
        int[] left = new int[n];//计算左边界
        int[] right = new int[n];//计算右边界
        Deque<Integer> stack = new ArrayDeque<>();
        for(int i = 0; i < n; i++){
            while(!stack.isEmpty()&&heights[stack.peek()]>=heights[i]){
                stack.pop();
            }
            left[i] = stack.isEmpty()?-1:stack.peek();
            stack.push(i);
        }
        stack.clear();
         for(int i = n - 1; i >= 0 ; i--){
            while(!stack.isEmpty()&&heights[stack.peek()]>=heights[i]){
                stack.pop();
            }
            right[i] = stack.isEmpty()?n:stack.peek();
            stack.push(i);
        }
        int result = 0;
        for(int i = 0; i < n;i++){
            result = Math.max(result,(right[i] - left[i] - 1)*heights[i]);
        }
        return result;
    }
}
~~~

#### [739. 每日温度](https://leetcode.cn/problems/daily-temperatures/)

难度中等

给定一个整数数组 `temperatures` ，表示每天的温度，返回一个数组 `answer` ，其中 `answer[i]` 是指对于第 `i` 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。

~~~java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int length = temperatures.length;
        int res[] = new int[length];
        int i;
        Deque<Integer> stack = new ArrayDeque<>();
        for( i = length - 1; i >= 0;i--){
            int temperature = temperatures[i];
            while(!stack.isEmpty() && (temperature >= temperatures[stack.peek()])){
                stack.pop();
            }
            if(stack.isEmpty()){
                res[i] = 0;
            }else{
                res[i] = stack.peek() - i;
            }
            stack.push(i);
        } 
        return res;
    }
}
~~~

#### [42. 接雨水](https://leetcode.cn/problems/trapping-rain-water/)

给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

![image-20230630200328868](C:\Users\18485\AppData\Roaming\Typora\typora-user-images\image-20230630200328868.png)

思路：

维护一个单调栈，为从栈顶到栈底单调递增。如果外部元素大于栈顶元素，说明可以形成接雨水的地方。

~~~java
class Solution {
    public int trap(int[] height) {
        int ans = 0;
        int length = height.length;
        Deque<Integer> stack = new LinkedList<Integer>();
        for(int i = 0 ; i < length;i++){
            while(!stack.isEmpty()&&height[stack.peek()]<height){
                int height = stack.pop();
                if(stack.isEmpty()){
                    break;
                }
                int left = stack.peek();
                int currWidth = i - left - 1;
                int currHeight = Math.min(height[left], height[i]) - height[top];
                ans += currWidth * currHeight;
            }
            stack.push(i);
        }
        return ans;
    }
}
~~~

