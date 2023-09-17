# LinkList

[toc]

___

## Double pointers trick

### Leecode 21: [merge-two-sorted-lists](https://leetcode.cn/problems/merge-two-sorted-lists/)

> Related trick: virtual node to avoid bounding check when dealing with link lists. Usually used when a new link list is created.

*Python version:*

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
    """
    The merge process of merge sort in link list implementation
    """
    # create a vitual head node, avoiding null check for the lists
    head=ListNode()
    res=head
    # double pointers tranvers, put the smaller one to res
    while list1 != None and list2 !=None:
        if list1.val < list2.val:
            res.next=list1
            list1=list1.next

        else:
            res.next=list2
            list2=list2.next
        res=res.next
    # put the rest to res
    res.next=list2 if list1==None else list1
    
    return head.next
```

*C++ version:*

```C++

/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */

ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
    // create a vitual head node, avoiding null check for the lists
    ListNode* head= new ListNode();
    ListNode* res = head;

    // double pointers tranvers, put the smaller one to res
    while (list1 != nullptr && list2 != nullptr){
        if (list1->val < list2->val){
            res->next = list1;
            list1 = list1->next;
        }

        else{
            res->next = list2;
            list2 = list2->next;
        }
        res = res->next;
    }

    // put the rest to res
    res->next=(list1==nullptr ? list2 : list1);
    return head->next;
}
```
___

### Leecode 86: [patition-list](https://leetcode.cn/problems/partition-list/description/)

> If we are utilizing the very node (instead of a copy) of the old list, it is recommended to cut every node from the old list to avoid loops.

*python version*

```python
def partition(self, head: Optional[ListNode], x: int) -> Optional[ListNode]:
       
    small= ListNode()
    res=small # Record the head of the small link list as the reurn node

    large=ListNode()
    conn=large  # Record the node to connect the two link list

    while head !=None:
        if head.val < x:
            small.next=head
            small=small.next
        else:
            large.next=head
            large=large.next

        # wrong!
        # head=head.next

        # cut very tranversed node from the old list in case there is a loop
        t=head.next
        head.next=None
        head=t

    # connect two lists
    small.next=conn.next

    return res.next 
```

```c++
ListNode* partition(ListNode* head, int x) {
    ListNode* small=new ListNode();
    ListNode* res=small;
    ListNode* large=new ListNode();
    ListNode* conn=large;


    while (head!=nullptr){
        if (head->val < x){
            small->next=head;
            small=small->next;
        }

        else{
            large->next =head;
            large=large->next;
        }

        // cut the node and head moves on to the next node
        ListNode * temp=head->next;
        head->next=nullptr;
        head=temp;
    };

    // connect the two lists
    small->next=conn->next;

    return res->next;
}
```

___

### Leecode 23: [merge-k-sorted-lists](https://leetcode.cn/problems/merge-k-sorted-lists/description/)

> Using min heap (implemented in priority queue) to get the smallest one of the k values.

*python version*

```python
import heapq
def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:

    # define how to compare ListNode
    ListNode.__lt__=lambda a,b: a.val<b.val

    # create the virtual node
    res=ListNode()
    head=res # for return
    # build the min heap with priority queue
    pq=[]
    for h in lists:
        if h:
            heapq.heappush(pq,h)
    
    while pq:
        # pop the smallest one
        t=heapq.heappop(pq)
        res.next=t

        # res moves on
        res=t

        # push the next node into pq
        if t.next:
            heapq.heappush(pq,t.next)
    
    return head.next
```

*c++ version*
It is notable that in C++, priority_queue is by default a Max Heap. But priority_queue supports a constructor that requires two extra arguments to make it min-heap. 

`priority_queue< Type, Container, Function> name (lambda_function that given two variable returns a bool value)`

```c++
#include <queue>
using namespace std;
ListNode* mergeKLists(vector<ListNode*>& lists) {
    // create a virtual node
    ListNode* head=new ListNode();
    ListNode* res=head;

    // min heap: the key is using lambda function
    priority_queue <ListNode*, vector<ListNode*>, function<bool(ListNode* a, ListNode* b)>  > pq(
        [](ListNode* a, ListNode* b){
            return a->val>b->val;
        }
    );

    for (ListNode* h : lists){
        if (h){
            pq.push(h); 
        }
    }

    while (!pq.empty()){
        ListNode* t=pq.top();
        pq.pop();
        res->next=t;
        res=res->next;
        if (t->next!=nullptr){
            pq.push(t->next);
        }
    }

    return head->next;

}
```

Time complex: We need $O(log(k))$ to get the smallest one in a min-heap (heapify), and we need to get N (the total number of nodes) of the smallest one. So the final complex is $O(Nlog(k))$.
___

### Leecode 19 [remove-nth-node-from-end-of-list](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

The inconvenience of link list is that we don't know the length just given the head node. Under this situation, double pointers trick helps.

> Double pointers: let p1 moves n nodes ahead, and be careful with the bounding check (when n==length of the LinkList). Also, to delete the n-th node, we actually need to find the  previous node of it.

*python version*

```python
def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:

    # double pointer:p1,p2
    p1=head
    p2=head # the node after p2 is the very one we want to delete
    

    # p1 moves k steps ahead
    for i in range(n+1):
        # bounding check, if n=sz
        if (p1==None):
            # is to delete the head node
            return head.next

        p1=p1.next

    # p1,p2 move synchronize
    while p1:
        p1=p1.next   
        p2=p2.next

    # delete the n-th node
    p2.next=p2.next.next

    return head
```

*c++ version*

```c++
ListNode* removeNthFromEnd(ListNode* head, int n) {
    // doouble pointers: p1,p2

    ListNode* p1=head;
    ListNode* p2=head; // the previous node of the node we want to delete

    // p1 moves n+1 steps ahead

    for (int i=0; i<n+1;i++){
        if (p1==nullptr){
            return head->next;
        }

        p1=p1->next;
    }

    while (p1!=nullptr){
        p1=p1->next;
        p2=p2->next;
    }

    p2->next=p2->next->next;

    return head;

}


// Or using the virtual node trick

ListNode* removeNthFromEnd(ListNode* head, int n) {
    // doouble pointers: p1,p2

    ListNode* p1=new ListNode(); // using the virtual node trick
    ListNode* res=p1;
    p1->next=head;
    ListNode* p2=p1; // the previous node of the node we want to delete
    
    // p1 moves n+1 steps ahead

    for (int i=0; i<n+1;i++){
        // if (p1==nullptr){
        //     return head->next;
        // } 

        p1=p1->next;
    }

    // move synchronize

    while (p1!=nullptr){
        p1=p1->next;
        p2=p2->next;
    }

    p2->next=p2->next->next;

    // virtual node trick
    return res->next;
}
```
___

### Leecode 876 [middle-of-the-linked-list](middle-of-the-linked-list)

*Python version*

```python
def middleNode(self, head: Optional[ListNode]) -> Optional[ListNode]:
    # double pointer trick
    fast=head
    slow=head

    # fast pointer moves two steps and slow pointer moves one steps
    while fast!=None and fast.next !=None:
        slow=slow.next
        fast=fast.next.next

    return slow
```

*c++ version*

```c+=
 ListNode* middleNode(ListNode* head) {

    ListNode* fast=head;
    ListNode* slow=head;

    while (fast!=nullptr && fast->next !=nullptr){
        fast=fast->next->next;
        slow=slow->next;
    }

    return slow;

}
```

