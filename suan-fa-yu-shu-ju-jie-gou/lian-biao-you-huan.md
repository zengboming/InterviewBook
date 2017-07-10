# 链表有环

1\) 判断有环：设定两个指针slow、fast，从头指针开始，每次分别前进1步、2步。如存在环，则两者相遇；如不存在环，fast遇到NULL退出。

2\) 环的长度：追赶法两次碰撞，之间走过的步数。

3\) 环的连接点：碰撞点p到连接点的距离=头指针到连接点的距离，因此，分别从碰撞点、头指针开始走，相遇的那个点就是连接点。

证明：当fast与slow相遇时，show肯定没有走完链表，而fast已经在还里走了n（n&gt;= 1）圈。假设slow走了s步，那么fast走了2s步。fast的步数还等于s走的加上环里转的n圈，所以有：2s = s + nr。因此，s = nr。设整个链表长为L，入口据相遇点X，起点到入口的距离为a。因为slow指针并没有走完一圈，所以：a + x = s，带入第一步的结果，有：a + x = nr = \(n-1\)r + r = \(n-1\)r + L - a；即：a = \(n-1\)r + L -a -x;

这说明：从头结点到入口的距离，等于转了\(n-1\)圈以后，相遇点到入口的距离。因此，我们可以在链表头、相遇点各设一个指针，每次各走一步，两个指针必定相遇，且相遇第一点为环入口点。

4\) 两个链表是否相交：两个链表首尾相连，判断是否有环。

```
typedef struct Node  
{  
    int val;  
    Node *next;  
}Node,*pNode;  

public class LinkLoop {
    //双指针判断有环
    public boolean hasLoop(pNode head) {
        pNode fast = head;
        pNode slow = head;
        while(fast != NULL && fast.next != NULL) {
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) {
                return true;
            }
        }
        return false;
    }

    //计算环的长度
    public int loopLength(pNode head) {
        if (!hasLoop(head)) {
            return 0;
        }
        pNode fast = head;
        pNode slow = head;
        int length = 0;
        bool begin = false;
        bool again = false;
        while( fast != NULL && fast->next != NULL) {
            fast = fast->next->next;  
            slow = slow->next;  
            //超两圈后停止计数，挑出循环
            if(fast == slow && agian == true) {
                break; 
            }
            //超一圈后开始计数  
            if(fast == slow && agian == false) {             
                begin = true;  
                agian = true;  
            }  
            //计数  
            if(begin == true) {
                ++length;  
            }
        }
        return length;
    }
    
    //求环的入口
    public pNode findLoopEntrance(pNode head) {
        pNode fast = pHead;  
        pNode slow = pHead;  
        while( fast != NULL && fast->next != NULL) {  
            fast = fast->next->next;  
            slow = slow->next;  
            //如果有环，则fast会超过slow一圈  
            if(fast == slow)  {  
                break;  
            }  
        }  
        if(fast == NULL || fast->next == NULL)  {
            return NULL;  
        }
        slow = pHead;  
        while(slow != fast) {  
            slow = slow->next;  
            fast = fast->next;  
        }  
  
        return slow;  
    }
}
```



