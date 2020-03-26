# 两数相加(Add Two Numbers)
**中文**
给出两个 **非空** 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 **逆序** 的方式存储的，并且它们的每个节点只能存储 **一位** 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 `0` 之外，这两个数都不会以 `0` 开头。

**示例：**
```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```
**English**
You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order** and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Example:**

```
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.
```

> 来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/add-two-numbers

## 力扣 (LeetCode)官方分析
**方法：初等数学**
**思路**

我们使用变量来跟踪进位，并从包含最低有效位的表头开始模拟逐位相加的过程。
![2_add_two_numbers](media/2_add_two_numbers.svg)
*对两数相加方法的可视化: `342 + 465 = 807`，每个结点都包含一个数字，并且数字按位逆序存储。*

**算法**

就像你在纸上计算两个数字的和那样，我们首先从最低有效位也就是列表 `l1` 和 `l2` 的表头开始相加。由于每位数字都应当处于 `0…9` 的范围内，我们计算两个数字的和时可能会出现 “溢出”。例如，`5 + 7 = 12`。在这种情况下，我们会将当前位的数值设置为 `2`，并将进位 `carry = 1` 带入下一次迭代。进位 `carry` 必定是 `0` 或 `1`，这是因为两个数字相加（考虑到进位）可能出现的最大和为 `9 + 9 + 1 = 19`。

**伪代码如下：**
* 将当前结点初始化为返回列表的哑结点。
* 将进位 `carry` 初始化为 `0`。
* 将 `p` 和 `q` 分别初始化为列表 `l1` 和 `l2` 的头部。
* 遍历列表 `l1` 和 `l2` 直至到达它们的尾端。
    * 将 x 设为结点 p 的值。如果 p 已经到达 l1 的末尾，则将其值设置为 0。
    * 将 `y` 设为结点 `q` 的值。如果 `q` 已经到达 `l2` 的末尾，则将其值设置为 `0`。
    * 设定 `sum = x + y + carry`。
    * 更新进位的值，`carry = sum / 10`。
    * 创建一个数值为 `(sum mod 10)`的新结点，并将其设置为当前结点的下一个结点，然后将当前结点前进到下一个结点。
    * 同时，将 `p` 和 `q` 前进到下一个结点。
* 检查 `carry = 1` 是否成立，如果成立，则向返回列表追加一个含有数字 `1` 的新结点。
* 返回哑结点的下一个结点。

请注意，我们使用哑结点来简化代码。如果没有哑结点，则必须编写额外的条件语句来初始化表头的值。

请特别注意以下情况：

| 测试用例                | 说明                        |
|:-------------------:|:-------------------------:|
| l1=[0,1]，l2=[0,1,2] | 当一个列表比另一个列表长时             |
| l1=[]，l2=[0,1]      | 当一个列表为空时，即出现空列表           |
| l1=[9,9]，l2=[1]     | 求和运算最后可能出现额外的进位，这一点很容易被遗忘 |

```
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    ListNode dummyHead = new ListNode(0);
    ListNode p = l1, q = l2, curr = dummyHead;
    int carry = 0;
    while (p != null || q != null) {
        int x = (p != null) ? p.val : 0;
        int y = (q != null) ? q.val : 0;
        int sum = carry + x + y;
        carry = sum / 10;
        curr.next = new ListNode(sum % 10);
        curr = curr.next;
        if (p != null) p = p.next;
        if (q != null) q = q.next;
    }
    if (carry > 0) {
        curr.next = new ListNode(carry);
    }
    return dummyHead.next;
}
```

**复杂度分析**

时间复杂度：`O(max(m, n))`，假设 `m` 和 `n` 分别表示 `l1` 和 `l2` 的长度，上面的算法最多重复 `max(m, n)` 次。

空间复杂度：`O(max(m, n))`， 新列表的长度最多为 `max(m,n) + 1`。

**拓展**
如果链表中的数字不是按逆序存储的呢？例如：

`(3→4→2)+(4→6→5)=8→0→7`

> 作者：LeetCode
链接：https://leetcode-cn.com/problems/add-two-numbers/solution/liang-shu-xiang-jia-by-leetcode/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## 我的答案
* **Ruby**

```
# Definition for singly-linked list.
# USAGE
# 1. generate list by array
# the_list = ListNode.generate([1,2,3])
# 2. traverse
# the_list.traverse   # => 1 -> 2 -> 3
# ListNode.generate(['a','b','c']).traverse  # => a -> b -> c

class ListNode
  attr_accessor :val, :next

  class << self
    def generate(array)
      array.map! { |v| ListNode.new(v) }
      array.length.times { |i| array[i].next = array[i+1] }
      array.first
    end
  end

  def initialize(value)
    @val = value
    @next = nil
  end

  def to_s
    "#{@val}"
  end

  def traverse
    print @val
    print ' -> ' if self.next
    self.next.traverse if self.next
  end
end

def add_two_numbers(l1, l2, carry = 0, arr = [])
  if l1 || l2 || !carry.zero?
    sum = (l1&.val || 0) + (l2&.val || 0) + carry
    carry = sum / 10
    arr << sum % 10
    add_two_numbers(l1&.next, l2&.next, carry, arr)
  else
    return arr
  end
end
```

* **Python**

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
	def addTwoNumbers(self, l1, l2):
		def add(a,b,carry):
			# 递归的终止条件是a和b都为空
			# 如果carry大于0需要返回一个进位标志
			if not (a or b):
				return ListNode(1) if carry else None
			# 如果a为空则将ListNode(0)赋给a，b同理
			a = a if a else ListNode(0)
			b = b if b else ListNode(0)
			#处理val，以及进位标志
			val = a.val + b.val + carry
			carry = 1 if val>=10 else 0
			a.val = val%10
			# 现在a的值就是两个节点相加后的和了
			# 之后再次递归计算a.next和b.next
			a.next = add(a.next,b.next,carry)
			return a
		return add(l1,l2,0)
```
