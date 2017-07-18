# 链表有环

1\) 判断有环：设定两个指针slow、fast，从头指针开始，每次分别前进1步、2步。如存在环，则两者相遇；如不存在环，fast遇到NULL退出。

2\) 环的长度：追赶法两次碰撞，之间走过的步数。

3\) 环的连接点：碰撞点p到连接点的距离=头指针到连接点的距离，因此，分别从碰撞点、头指针开始走，相遇的那个点就是连接点。

证明：当fast与slow相遇时，show肯定没有走完链表，而fast已经在还里走了n（n&gt;= 1）圈。假设slow走了s步，那么fast走了2s步。fast的步数还等于s走的加上环里转的n圈，所以有：2s = s + nr。因此，s = nr。设整个链表长为L，入口据相遇点X，起点到入口的距离为a。因为slow指针并没有走完一圈，所以：a + x = s，带入第一步的结果，有：a + x = nr = \(n-1\)r + r = \(n-1\)r + L - a；即：a = \(n-1\)r + L -a -x;

这说明：从头结点到入口的距离，等于转了\(n-1\)圈以后，相遇点到入口的距离。因此，我们可以在链表头、相遇点各设一个指针，每次各走一步，两个指针必定相遇，且相遇第一点为环入口点。

4\) 两个链表是否相交：两个链表首尾相连，判断是否有环。

```
public class LinkLoop {

    static class Node {
        int value;
        Node next;
        public Node(int value) {
            this.value = value;
        }
    }

    public static boolean hasLoop(Node node) {
        Node fast = node;
        Node slow = node;
        while (fast.next != null && fast.next.next != null) {
            fast = fast.next.next;
            slow = slow.next;

            if (fast == slow) {
                return true;
            }
        }

        return false;
    }

    public static int getLoopLength(Node node) {
        if (!hasLoop(node)) {
            return 0;
        }
        Node fast = node;
        Node slow = node;
        boolean begin = false;
        boolean again = false;
        int length = 0;

        while (fast.next != null && fast.next.next != null) {
            fast = fast.next.next;
            slow = slow.next;

            if (begin) {
                length++;
            }

            if (fast == slow) {
                if (!again) {
                    again = true;
                    begin = true;
                } else {
                    break;
                }
            }
        }

        return length;
    }

    public static Node getLoopEntrance(Node node) {
        if (!hasLoop(node)) {
            return null;
        }
        Node fast = node;
        Node slow = node;
        while (fast.next != null && fast.next.next != null) {
            fast = fast.next.next;
            slow = slow.next;

            if (fast == slow) {
                break;
            }
        }

        if (fast == null || fast.next == null) {
            return null;
        }

        slow = node;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }

        return slow;
    }

    public static void main(String[] args) {
         Node n1 = new Node(1);  
         Node n2 = new Node(3);  
         Node n3 = new Node(6);  
         Node n4 = new Node(4);  
         Node n5 = new Node(5);  
         Node n6 = new Node(10); 
         n1.next = n2;  
         n2.next = n3;  
         n3.next = n4;  
         n4.next = n5;  
         n5.next = n6;  
         n6.next = n5;  
         boolean hasLoop = hasLoop(n1);
         int length = getLoopLength(n1);
         Node node = getLoopEntrance(n1);
         System.out.println(hasLoop);
         System.out.println(length);
         if (node != null) {
             System.out.println(node.value);
         }
    }
}
```



