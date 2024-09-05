思路：**双指针法**，如果要删除倒数第n个节点，让fast移动**n+1**步，然后让fast和slow同时移动，直到fast指向链表末尾，删掉slow所指向的节点就可以了。

![[Pasted image 20240126222317.png]]

fast一定要移动**n+1**位，因为之后fast和slow同时移动时**slow才能指向删除的上一个节点**。

![[Pasted image 20240126222327.png]]
![[Pasted image 20240126222337.png]]![[Pasted image 20240126222349.png]]

#### C版本
```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
struct ListNode* removeNthFromEnd(struct ListNode* head, int n) {
    struct ListNode *dummy, *fast, *slow, *temp;
    dummy = (struct ListNode*)malloc(sizeof(struct ListNode));
    dummy->next = head;
    fast = dummy;
    slow = dummy;
    while(n+1){
        fast = fast->next;
        n--;
    }
    while(fast){
        fast = fast->next;
        slow = slow->next;
    }
    temp = slow->next;
    slow->next = temp->next;
    free(temp);
    return dummy->next;
}
```


#### Java版本

注：
- 一定要注意最后返回的节点是头结点，最好不要移动dummy虚拟头节点；
- 两个while不要合并，合并后结束条件不一样，可能会有空指针异常

```java
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
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode slow = dummy;
        ListNode fast = dummy;
        while(n >= 0){
            fast = fast.next;
            n--;
        }
        while(fast != null){ // 两个while不要合并，合并后结束条件不一样
            fast = fast.next;
            slow = slow.next;
        }
        slow.next = slow.next.next;
        return dummy.next; // 不要移动dummy，不然返回会有问题
    }
}
```
