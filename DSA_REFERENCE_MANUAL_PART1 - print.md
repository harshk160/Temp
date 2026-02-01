# 📘 Data Structures Implementation Reference Manual

**A Complete Reference Guide for C++ Implementations**

*Print this document for quick reference during coding sessions*

---

## Table of Contents

### Part 1: Basic Data Structures
1. [Dynamic Array (Vector)](#1-dynamic-array-vector)
2. [Singly Linked List](#2-singly-linked-list)
3. [Doubly Linked List](#3-doubly-linked-list)
4. [Stack](#4-stack)
5. [Queue](#5-queue)

### Part 2: Tree Data Structures
6. [Binary Tree](#6-binary-tree)
7. [Binary Search Tree](#7-binary-search-tree)
8. [Min Heap](#8-min-heap)
9. [Max Heap](#9-max-heap)

### Part 3: Advanced Data Structures
10. [Hash Table](#10-hash-table)
11. [Graph](#11-graph)
12. [Trie (Prefix Tree)](#12-trie-prefix-tree)
13. [Union-Find (Disjoint Set)](#13-union-find-disjoint-set)
14. [Segment Tree](#14-segment-tree)

### Part 4: Quick Reference
- [Complexity Cheat Sheet](#complexity-cheat-sheet)
- [When to Use Which DS](#when-to-use-which-data-structure)
- [Common Patterns](#common-patterns)

---
---

# PART 1: BASIC DATA STRUCTURES

---

## 1. Dynamic Array (Vector)

### Overview

**What is it?**
A dynamic array is a resizable array that automatically grows when more space is needed. Unlike static arrays with fixed size, dynamic arrays can expand to accommodate new elements.

**Why use it?**
- Random access in O(1) time
- Automatic memory management
- Cache-friendly (contiguous memory)
- Versatile for most general-purpose needs

**Real-world analogy:**
Think of a parking lot that can add more spaces when full. Initially you have 10 spots, but when all are filled, you build 10 more.

**Visual Representation:**
```
Initial state (capacity = 4, size = 3):
[10][20][30][__]
 0   1   2   3

After pushing 40 (capacity = 4, size = 4):
[10][20][30][40]
 0   1   2   3

After pushing 50 (resize triggered, capacity = 8, size = 5):
[10][20][30][40][50][__][__][__]
 0   1   2   3   4   5   6   7
```

### Structure Definition

```cpp
class Vector {
private:
    int* arr;           // Pointer to dynamic array
    int capacity;       // Total allocated space
    int current;        // Number of elements currently stored

public:
    Vector();           // Constructor
    ~Vector();          // Destructor
    void push_back(int data);
    void pop_back();
    int at(int index);
    int size();
    int getCapacity();
    void clear();
    bool empty();
};
```

**Explanation:**
- `arr`: Points to the actual array in heap memory
- `capacity`: How many elements the array can hold before resizing
- `current`: How many elements are actually stored

---

### Operation 1: Constructor

**Purpose:** Initialize an empty vector with initial capacity.

**Code:**
```cpp
Vector() {
    arr = new int[1];      // Allocate initial space
    capacity = 1;          // Initial capacity
    current = 0;           // No elements yet
}
```

**Explanation:**
1. Allocate memory for 1 element initially
2. Set capacity to 1
3. Set current size to 0 (empty)

**Memory State:**
```
Before:  [Nothing allocated]
After:   [__]  (capacity=1, size=0)
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 2: Push Back (Add Element)

**Purpose:** Add an element to the end of the vector. Resize if needed.

**Code:**
```cpp
void push_back(int data) {
    // Check if resize is needed
    if (current == capacity) {
        // Create new array with double capacity
        int* temp = new int[2 * capacity];
        
        // Copy all elements to new array
        for (int i = 0; i < capacity; i++) {
            temp[i] = arr[i];
        }
        
        // Free old array
        delete[] arr;
        
        // Update capacity and pointer
        capacity *= 2;
        arr = temp;
    }
    
    // Add new element
    arr[current] = data;
    current++;
}
```

**Explanation (Step-by-Step):**

**Scenario 1: Space available**
```
Before: [10][20][__][__]  (capacity=4, size=2)
                ↑
         Insert 30 here

After:  [10][20][30][__]  (capacity=4, size=3)
```

**Scenario 2: Resize needed**
```
Before: [10][20][30][40]  (capacity=4, size=4) - FULL!
Insert 50:

Step 1: Create new array (capacity = 8)
        [__][__][__][__][__][__][__][__]

Step 2: Copy existing elements
        [10][20][30][40][__][__][__][__]

Step 3: Delete old array and add new element
        [10][20][30][40][50][__][__][__]  (capacity=8, size=5)
```

**Why double the capacity?**
Doubling ensures amortized O(1) time. If we only increased by 1, we'd resize on every insertion!

**Time Complexity:**
- Best/Average case: O(1) - just add element
- Worst case: O(n) - when resize happens
- Amortized: O(1) - resize happens infrequently

**Space Complexity:** O(n)

---

### Operation 3: Pop Back (Remove Last Element)

**Purpose:** Remove the last element from the vector.

**Code:**
```cpp
void pop_back() {
    if (current > 0) {
        current--;
    }
}
```

**Explanation:**
Simply decrement the size counter. We don't actually delete the element from memory - just mark it as "not part of the vector" by reducing `current`.

**Visual:**
```
Before: [10][20][30][40]  (size=4)
                      ↑
                   Remove this

After:  [10][20][30][40]  (size=3)
                      ↑
              This is now "garbage"
              (ignored, will be overwritten)
```

**Why don't we delete the element?**
For efficiency! The value at index 3 still exists in memory, but it's ignored. When we push_back again, it gets overwritten.

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 4: Access Element (at)

**Purpose:** Access element at a specific index with bounds checking.

**Code:**
```cpp
int at(int index) {
    if (index < 0 || index >= current) {
        throw std::out_of_range("Index out of bounds");
    }
    return arr[index];
}
```

**Explanation:**
1. Check if index is valid (0 ≤ index < size)
2. If invalid, throw exception
3. If valid, return element at that position

**Visual:**
```
Array: [10][20][30][40]  (size=4)
        0   1   2   3

at(2) → Returns 30 ✓
at(5) → Throws exception (index >= 4) ✗
at(-1) → Throws exception (negative index) ✗
```

**Time Complexity:** O(1) - direct memory access
**Space Complexity:** O(1)

---

### Operation 5: Get Size

**Purpose:** Return the number of elements currently in the vector.

**Code:**
```cpp
int size() {
    return current;
}
```

**Explanation:**
Simply return the `current` counter which tracks how many elements we've added.

**Example:**
```
Vector: [10][20][30][__][__][__]
         0   1   2   3   4   5
         
size() returns 3 (not 6!)
capacity is 6
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 6: Get Capacity

**Purpose:** Return the total allocated space (not the number of elements).

**Code:**
```cpp
int getCapacity() {
    return capacity;
}
```

**Explanation:**
Returns how many elements the vector CAN hold before needing to resize.

**Size vs Capacity:**
```
[10][20][30][__][__][__][__][__]
 
size() = 3       (3 elements stored)
capacity() = 8   (can hold 8 before resize)
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 7: Destructor

**Purpose:** Free allocated memory when vector is destroyed.

**Code:**
```cpp
~Vector() {
    delete[] arr;
}
```

**Explanation:**
Called automatically when vector goes out of scope. Prevents memory leaks by freeing the dynamically allocated array.

**What happens without destructor?**
```
Memory leak! The array stays in memory even after program ends.

Before destructor:
Heap: [10][20][30][40][50][__][__][__] ← arr points here

After destructor:
Heap: [freed memory] ← arr deleted, memory returned to OS
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Complete Implementation

```cpp
class Vector {
private:
    int* arr;
    int capacity;
    int current;

public:
    Vector() {
        arr = new int[1];
        capacity = 1;
        current = 0;
    }
    
    void push_back(int data) {
        if (current == capacity) {
            int* temp = new int[2 * capacity];
            for (int i = 0; i < capacity; i++) {
                temp[i] = arr[i];
            }
            delete[] arr;
            capacity *= 2;
            arr = temp;
        }
        arr[current] = data;
        current++;
    }
    
    void pop_back() {
        if (current > 0) current--;
    }
    
    int at(int index) {
        if (index < 0 || index >= current) {
            throw std::out_of_range("Index out of bounds");
        }
        return arr[index];
    }
    
    int size() { return current; }
    int getCapacity() { return capacity; }
    bool empty() { return current == 0; }
    
    ~Vector() {
        delete[] arr;
    }
};
```

---

### Complexity Summary

| Operation | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Constructor | O(1) | O(1) |
| push_back | O(1) amortized | O(1) |
| pop_back | O(1) | O(1) |
| at / [] | O(1) | O(1) |
| size | O(1) | O(1) |
| capacity | O(1) | O(1) |

---

### Common Use Cases

✅ **Use Vector When:**
- You need random access to elements
- You mostly add/remove from the end
- You don't know the size in advance
- You want cache-friendly performance

❌ **Don't Use Vector When:**
- Frequent insertions/deletions in the middle
- Need O(1) insertion at front
- Memory is very limited (capacity overhead)

---
---

## 2. Singly Linked List

### Overview

**What is it?**
A linear data structure where each element (node) contains data and a pointer to the next node. Elements are NOT stored contiguously in memory.

**Why use it?**
- O(1) insertion/deletion at head
- No resizing needed (unlike array)
- Efficient memory usage for unknown size
- Foundation for many other DS (stacks, queues, graphs)

**Real-world analogy:**
Think of a treasure hunt where each clue points to the next location. You can only go forward, and finding clue #5 means following clues 1→2→3→4→5.

**Visual Representation:**
```
Head → [10|●]→[20|●]→[30|●]→[40|NULL]
        data next
        
Each box is a node:
┌─────┬─────┐
│ 10  │  ●  │ → points to next node
└─────┴─────┘
 data   next pointer
```

### Structure Definition

```cpp
// Node structure
class Node {
public:
    int data;       // The value stored
    Node* next;     // Pointer to next node
    
    Node(int val) : data(val), next(nullptr) {}
};

// Linked List class
class LinkedList {
private:
    Node* head;     // Pointer to first node
    int length;     // Number of nodes

public:
    LinkedList();
    ~LinkedList();
    void insertAtHead(int val);
    void insertAtTail(int val);
    void deleteAtHead();
    void reverse();
    int getMiddle();
    bool detectCycle();
    void display();
};
```

---

### Operation 1: Constructor

**Purpose:** Initialize an empty linked list.

**Code:**
```cpp
LinkedList() {
    head = nullptr;    // No nodes yet
    length = 0;        // Size is 0
}
```

**Explanation:**
Start with head pointing to NULL, indicating an empty list.

**Visual:**
```
Empty list:
head → NULL
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 2: Insert at Head

**Purpose:** Add a new node at the beginning of the list.

**Code:**
```cpp
void insertAtHead(int val) {
    // Create new node
    Node* newNode = new Node(val);
    
    // Point new node to current head
    newNode->next = head;
    
    // Update head to new node
    head = newNode;
    
    length++;
}
```

**Explanation (Step-by-Step):**

**Step 1: Create new node**
```
newNode → [15|NULL]
head → [10|●]→[20|●]→[30|NULL]
```

**Step 2: Point newNode to current head**
```
newNode → [15|●]
            ↓
head → [10|●]→[20|●]→[30|NULL]
```

**Step 3: Update head**
```
head → [15|●]→[10|●]→[20|●]→[30|NULL]
        ↑
     newNode
```

**Why is this O(1)?**
We don't traverse the list! Just update two pointers.

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 3: Insert at Tail

**Purpose:** Add a new node at the end of the list.

**Code:**
```cpp
void insertAtTail(int val) {
    Node* newNode = new Node(val);
    
    // If list is empty, new node becomes head
    if (!head) {
        head = newNode;
        length++;
        return;
    }
    
    // Traverse to last node
    Node* temp = head;
    while (temp->next != nullptr) {
        temp = temp->next;
    }
    
    // Link last node to new node
    temp->next = newNode;
    length++;
}
```

**Explanation (Step-by-Step):**

**Scenario 1: Empty list**
```
Before: head → NULL
Insert 10:
After:  head → [10|NULL]
```

**Scenario 2: Non-empty list**
```
Before: head → [10|●]→[20|●]→[30|NULL]
Insert 40:

Step 1: Traverse to last node
        temp starts at head
        temp → [10|●]→[20|●]→[30|NULL]
                                 ↑
                              stops here (next is NULL)

Step 2: Create new node
        newNode → [40|NULL]

Step 3: Link last node to new node
        head → [10|●]→[20|●]→[30|●]→[40|NULL]
                              temp  newNode
```

**Why is this O(n)?**
Must traverse entire list to find the last node.

**Optimization:** Keep a tail pointer to make this O(1)!

**Time Complexity:** O(n) - must traverse to end
**Space Complexity:** O(1)

---

### Operation 4: Delete at Head

**Purpose:** Remove the first node from the list.

**Code:**
```cpp
void deleteAtHead() {
    // Check if list is empty
    if (!head) return;
    
    // Save reference to current head
    Node* temp = head;
    
    // Move head to next node
    head = head->next;
    
    // Free memory of old head
    delete temp;
    
    length--;
}
```

**Explanation (Step-by-Step):**

```
Before: head → [10|●]→[20|●]→[30|NULL]

Step 1: temp = head
        temp
         ↓
        [10|●]→[20|●]→[30|NULL]
         ↑
        head

Step 2: head = head->next
        temp
         ↓
        [10|●]  [20|●]→[30|NULL]
                 ↑
                head

Step 3: delete temp
        [deleted]  [20|●]→[30|NULL]
                    ↑
                   head
```

**Why save temp?**
We need a reference to free the memory! If we just did `head = head->next`, the old head would be unreachable (memory leak).

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 5: Reverse the List

**Purpose:** Reverse the direction of all links.

**Code:**
```cpp
void reverse() {
    Node* prev = nullptr;
    Node* curr = head;
    Node* next = nullptr;
    
    while (curr != nullptr) {
        // Save next node
        next = curr->next;
        
        // Reverse current node's pointer
        curr->next = prev;
        
        // Move pointers one position ahead
        prev = curr;
        curr = next;
    }
    
    // Update head to last node
    head = prev;
}
```

**Explanation (Step-by-Step):**

**Initial state:**
```
prev = NULL
curr = head → [10|●]→[20|●]→[30|NULL]
next = NULL
```

**Iteration 1:**
```
Step 1: next = curr->next
prev=NULL  curr           next
           ↓              ↓
          [10|●]→[20|●]→[30|NULL]

Step 2: curr->next = prev
prev=NULL  curr           next
    ←──────[10]  [20|●]→[30|NULL]
                  ↑
             broken link

Step 3: Move pointers
           prev  curr      next
            ↓     ↓         ↓
NULL←[10]  [20|●]→[30|NULL]
```

**Iteration 2:**
```
Step 1: next = curr->next
           prev  curr      next
            ↓     ↓         ↓
NULL←[10]  [20|●]→[30|NULL]

Step 2: curr->next = prev
           prev  curr      next
            ↓     ↓         ↓
NULL←[10]←[20]   [30|NULL]

Step 3: Move pointers
                  prev    curr  next
                   ↓       ↓     ↓
NULL←[10]←[20]   [30|NULL]
```

**Iteration 3:**
```
Step 1: next = curr->next
                  prev    curr  next
                   ↓       ↓     ↓
NULL←[10]←[20]   [30|●]   NULL

Step 2: curr->next = prev
                  prev    curr  next
                   ↓       ↓     ↓
NULL←[10]←[20]←[30]       NULL

Step 3: Move pointers
                         prev  curr
                          ↓     ↓
NULL←[10]←[20]←[30]      NULL
```

**Final: Update head**
```
head → [30|●]→[20|●]→[10|NULL]
```

**Time Complexity:** O(n) - visit each node once
**Space Complexity:** O(1) - only 3 pointers

---

### Operation 6: Find Middle (Slow-Fast Pointer)

**Purpose:** Find the middle node of the list efficiently.

**Code:**
```cpp
int getMiddle() {
    if (!head) {
        throw std::runtime_error("List is empty");
    }
    
    Node* slow = head;
    Node* fast = head;
    
    // Move slow by 1, fast by 2
    while (fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;
    }
    
    return slow->data;
}
```

**Explanation (Tortoise and Hare Algorithm):**

**Why does this work?**
When fast reaches the end, slow is at the middle!

**Example with odd number of nodes (5 nodes):**
```
Initial:
slow
fast
 ↓
[1]→[2]→[3]→[4]→[5]→NULL

After 1st iteration:
   slow
        fast
    ↓    ↓
[1]→[2]→[3]→[4]→[5]→NULL

After 2nd iteration:
       slow
                fast
        ↓        ↓
[1]→[2]→[3]→[4]→[5]→NULL

After 3rd iteration:
            slow
                     fast
             ↓        ↓
[1]→[2]→[3]→[4]→[5]→NULL
             ↑
          Middle!
```

**Example with even number of nodes (4 nodes):**
```
Initial:
slow/fast
 ↓
[1]→[2]→[3]→[4]→NULL

After 1st iteration:
   slow
        fast
    ↓    ↓
[1]→[2]→[3]→[4]→NULL

After 2nd iteration:
       slow
             fast
        ↓     ↓
[1]→[2]→[3]→[4]→NULL
        ↑
     Middle (2nd of 2 middle nodes)
```

**Time Complexity:** O(n) - traverse list once
**Space Complexity:** O(1) - only 2 pointers

---

### Operation 7: Detect Cycle (Floyd's Algorithm)

**Purpose:** Determine if the list has a cycle (loop).

**Code:**
```cpp
bool detectCycle() {
    if (!head) return false;
    
    Node* slow = head;
    Node* fast = head;
    
    while (fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;
        
        // If they meet, there's a cycle
        if (slow == fast) {
            return true;
        }
    }
    
    return false;
}
```

**Explanation:**

**Case 1: No cycle**
```
[1]→[2]→[3]→[4]→NULL

slow and fast will never meet
fast reaches NULL → no cycle
```

**Case 2: Cycle exists**
```
[1]→[2]→[3]→[4]
     ↑        ↓
     [6]←[5]←┘

Initial:
slow/fast
 ↓
[1]→[2]→[3]→[4]
     ↑        ↓
     [6]←[5]←┘

After iterations:
Eventually slow and fast will meet in the cycle!

Why? Fast moves 2x speed of slow.
If there's a cycle, fast will "lap" slow.
```

**Mathematical proof:**
- Let cycle length = C
- Fast catches up to slow at rate of 1 node per iteration
- Will definitely meet within C iterations

**Time Complexity:** O(n)
**Space Complexity:** O(1)

---

### Operation 8: Display List

**Purpose:** Print all elements in the list.

**Code:**
```cpp
void display() {
    Node* temp = head;
    
    while (temp != nullptr) {
        cout << temp->data << " → ";
        temp = temp->next;
    }
    
    cout << "NULL\n";
}
```

**Explanation:**
Traverse from head to end, printing each node's data.

**Visual:**
```
Traversal path:
head
 ↓
[10]→[20]→[30]→NULL
 ↓    ↓    ↓
print print print

Output: 10 → 20 → 30 → NULL
```

**Time Complexity:** O(n)
**Space Complexity:** O(1)

---

### Operation 9: Destructor

**Purpose:** Free all nodes to prevent memory leaks.

**Code:**
```cpp
~LinkedList() {
    Node* curr = head;
    
    while (curr != nullptr) {
        Node* next = curr->next;
        delete curr;
        curr = next;
    }
}
```

**Explanation:**
Must delete each node individually.

**Process:**
```
Initial: head → [10|●]→[20|●]→[30|NULL]

Step 1: Save next, delete current
curr=10, next=20
delete [10]
    [deleted]→[20|●]→[30|NULL]

Step 2: Move to next
curr=20, next=30
delete [20]
    [deleted]→[deleted]→[30|NULL]

Step 3: Continue until NULL
All nodes deleted
```

**Time Complexity:** O(n)
**Space Complexity:** O(1)

---

### Complete Implementation

```cpp
class Node {
public:
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class LinkedList {
private:
    Node* head;
    int length;
    
public:
    LinkedList() : head(nullptr), length(0) {}
    
    void insertAtHead(int val) {
        Node* newNode = new Node(val);
        newNode->next = head;
        head = newNode;
        length++;
    }
    
    void insertAtTail(int val) {
        Node* newNode = new Node(val);
        if (!head) {
            head = newNode;
            length++;
            return;
        }
        Node* temp = head;
        while (temp->next) temp = temp->next;
        temp->next = newNode;
        length++;
    }
    
    void deleteAtHead() {
        if (!head) return;
        Node* temp = head;
        head = head->next;
        delete temp;
        length--;
    }
    
    void reverse() {
        Node *prev = nullptr, *curr = head, *next = nullptr;
        while (curr) {
            next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        head = prev;
    }
    
    int getMiddle() {
        if (!head) throw std::runtime_error("List is empty");
        Node *slow = head, *fast = head;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow->data;
    }
    
    bool detectCycle() {
        if (!head) return false;
        Node *slow = head, *fast = head;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast) return true;
        }
        return false;
    }
    
    void display() {
        Node* temp = head;
        while (temp) {
            cout << temp->data << " → ";
            temp = temp->next;
        }
        cout << "NULL\n";
    }
    
    ~LinkedList() {
        Node* curr = head;
        while (curr) {
            Node* next = curr->next;
            delete curr;
            curr = next;
        }
    }
};
```

---

### Complexity Summary

| Operation | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Insert at Head | O(1) | O(1) |
| Insert at Tail | O(n) | O(1) |
| Delete at Head | O(1) | O(1) |
| Reverse | O(n) | O(1) |
| Find Middle | O(n) | O(1) |
| Detect Cycle | O(n) | O(1) |
| Display | O(n) | O(1) |

---

### Key Patterns

**1. Two Pointer Technique (Slow-Fast)**
- Finding middle
- Detecting cycle
- Finding nth node from end

**2. Reversal Pattern**
- Three pointers: prev, curr, next
- Used in: reverse list, reverse in groups

**3. Dummy Node Pattern**
- Simplifies edge cases
- Used in: merge lists, partition lists

---

### Common Use Cases

✅ **Use Linked List When:**
- Frequent insertions/deletions at beginning
- Don't need random access
- Size is unknown or changes frequently
- Implementing stacks, queues, or graphs

❌ **Don't Use Linked List When:**
- Need random access (use array/vector)
- Memory overhead is concern (extra pointer per node)
- Cache performance is critical

---
---

## 3. Doubly Linked List

### Overview

**What is it?**
A linked list where each node has TWO pointers: one to the next node and one to the previous node. This allows bidirectional traversal.

**Why use it?**
- Traverse in both directions
- O(1) deletion if node pointer is given
- O(1) insertion at both ends (with tail pointer)
- Better for implementing deques and LRU cache

**Real-world analogy:**
Think of a two-way street where you can travel forward or backward. Each intersection knows both the previous and next intersection.

**Visual Representation:**
```
      ┌──────────────────┐
NULL ←│prev│10│next│→│prev│20│next│→│prev│30│next│→ NULL
      └──────────────────┘
      head                               tail
```

### Structure Definition

```cpp
class DNode {
public:
    int data;
    DNode* prev;    // Pointer to previous node
    DNode* next;    // Pointer to next node
    
    DNode(int val) : data(val), prev(nullptr), next(nullptr) {}
};

class DoublyLinkedList {
private:
    DNode* head;
    DNode* tail;
    int length;

public:
    DoublyLinkedList();
    void insertAtHead(int val);
    void insertAtTail(int val);
    void deleteAtHead();
    void deleteAtTail();
    void reverse();
    void displayForward();
    void displayBackward();
};
```

---

### Operation 1: Insert at Head

**Purpose:** Add new node at the beginning.

**Code:**
```cpp
void insertAtHead(int val) {
    DNode* newNode = new DNode(val);
    
    // If list is empty
    if (!head) {
        head = tail = newNode;
    } else {
        // Link new node to current head
        newNode->next = head;
        head->prev = newNode;
        head = newNode;
    }
    length++;
}
```

**Explanation (Step-by-Step):**

**Case 1: Empty list**
```
Before: head = NULL, tail = NULL

After inserting 10:
        head/tail
           ↓
NULL ← [10] → NULL
```

**Case 2: Non-empty list**
```
Before:
        head                     tail
         ↓                        ↓
NULL ← [10] ↔ [20] ↔ [30] → NULL

Insert 5:

Step 1: Create newNode
newNode → [5|NULL|NULL]

Step 2: Link newNode.next to head
newNode → [5|NULL|●]
                   ↓
          [10] ↔ [20] ↔ [30]

Step 3: Link head.prev to newNode
          ●
          ↓
newNode → [5] → [10] ↔ [20] ↔ [30]

Step 4: Update head
        head
         ↓
NULL ← [5] ↔ [10] ↔ [20] ↔ [30] → NULL
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 2: Insert at Tail

**Purpose:** Add new node at the end.

**Code:**
```cpp
void insertAtTail(int val) {
    DNode* newNode = new DNode(val);
    
    if (!tail) {
        head = tail = newNode;
    } else {
        tail->next = newNode;
        newNode->prev = tail;
        tail = newNode;
    }
    length++;
}
```

**Explanation:**

```
Before:
        head                     tail
         ↓                        ↓
NULL ← [10] ↔ [20] ↔ [30] → NULL

Insert 40:

Step 1: Create newNode
newNode → [40|NULL|NULL]

Step 2: Link tail.next to newNode
        [10] ↔ [20] ↔ [30] → [40]

Step 3: Link newNode.prev to tail
        [10] ↔ [20] ↔ [30] ↔ [40]

Step 4: Update tail
                              tail
                               ↓
NULL ← [10] ↔ [20] ↔ [30] ↔ [40] → NULL
```

**Advantage over Singly Linked List:**
This is O(1) because we have a tail pointer! In singly linked list without tail, this would be O(n).

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 3: Delete at Head

**Purpose:** Remove the first node.

**Code:**
```cpp
void deleteAtHead() {
    if (!head) return;
    
    DNode* temp = head;
    head = head->next;
    
    if (head) {
        head->prev = nullptr;
    } else {
        tail = nullptr;  // List became empty
    }
    
    delete temp;
    length--;
}
```

**Explanation:**

**Case 1: Single node**
```
Before:
        head/tail
           ↓
NULL ← [10] → NULL

After delete:
head = NULL, tail = NULL
```

**Case 2: Multiple nodes**
```
Before:
        head
         ↓
NULL ← [10] ↔ [20] ↔ [30] → NULL

Step 1: temp = head
        temp
         ↓
NULL ← [10] ↔ [20] ↔ [30] → NULL
         ↑
        head

Step 2: head = head->next
        temp         head
         ↓            ↓
NULL ← [10] ← [20] ↔ [30] → NULL

Step 3: head->prev = NULL
        temp    head
         ↓       ↓
        [10]  NULL ← [20] ↔ [30] → NULL

Step 4: delete temp
              head
               ↓
        NULL ← [20] ↔ [30] → NULL
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 4: Delete at Tail

**Purpose:** Remove the last node.

**Code:**
```cpp
void deleteAtTail() {
    if (!tail) return;
    
    DNode* temp = tail;
    tail = tail->prev;
    
    if (tail) {
        tail->next = nullptr;
    } else {
        head = nullptr;  // List became empty
    }
    
    delete temp;
    length--;
}
```

**Explanation:**

```
Before:
                         tail
                          ↓
NULL ← [10] ↔ [20] ↔ [30] → NULL

Step 1: temp = tail
                         temp
                          ↓
NULL ← [10] ↔ [20] ↔ [30] → NULL

Step 2: tail = tail->prev
                    tail temp
                     ↓    ↓
NULL ← [10] ↔ [20] ↔ [30]

Step 3: tail->next = NULL
                    tail temp
                     ↓    ↓
NULL ← [10] ↔ [20] → NULL [30]

Step 4: delete temp
                    tail
                     ↓
NULL ← [10] ↔ [20] → NULL
```

**Advantage over Singly Linked List:**
O(1) deletion at tail! In singly linked list, we need O(n) to find the second-last node.

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 5: Reverse

**Purpose:** Reverse the direction of all links.

**Code:**
```cpp
void reverse() {
    DNode* curr = head;
    DNode* temp = nullptr;
    
    // Swap prev and next for all nodes
    while (curr) {
        temp = curr->prev;
        curr->prev = curr->next;
        curr->next = temp;
        curr = curr->prev;  // Move to next (which is now prev)
    }
    
    // Swap head and tail
    if (temp) head = temp->prev;
    
    temp = head;
    head = tail;
    tail = temp;
}
```

**Explanation:**

```
Initial:
        head                     tail
         ↓                        ↓
NULL ← [10] ↔ [20] ↔ [30] → NULL

Iteration 1 (curr = 10):
Swap 10's prev and next:
NULL → [10] ← [20] ↔ [30] → NULL
       prev  next

Iteration 2 (curr = 20):
Swap 20's prev and next:
NULL → [10] → [20] ← [30] → NULL

Iteration 3 (curr = 30):
Swap 30's prev and next:
NULL → [10] → [20] → [30] ← NULL

Finally, swap head and tail:
                    head        tail
                     ↓           ↓
NULL ← [30] ↔ [20] ↔ [10] → NULL
```

**Time Complexity:** O(n)
**Space Complexity:** O(1)

---

### Operation 6: Display Forward

**Purpose:** Print list from head to tail.

**Code:**
```cpp
void displayForward() {
    DNode* temp = head;
    
    while (temp) {
        cout << temp->data << " ↔ ";
        temp = temp->next;
    }
    cout << "NULL\n";
}
```

**Time Complexity:** O(n)
**Space Complexity:** O(1)

---

### Operation 7: Display Backward

**Purpose:** Print list from tail to head.

**Code:**
```cpp
void displayBackward() {
    DNode* temp = tail;
    
    while (temp) {
        cout << temp->data << " ↔ ";
        temp = temp->prev;
    }
    cout << "NULL\n";
}
```

**Advantage:**
Can traverse backwards without extra space! Not possible in singly linked list.

**Time Complexity:** O(n)
**Space Complexity:** O(1)

---

### Complete Implementation

```cpp
class DNode {
public:
    int data;
    DNode* prev;
    DNode* next;
    DNode(int val) : data(val), prev(nullptr), next(nullptr) {}
};

class DoublyLinkedList {
private:
    DNode* head;
    DNode* tail;
    int length;
    
public:
    DoublyLinkedList() : head(nullptr), tail(nullptr), length(0) {}
    
    void insertAtHead(int val) {
        DNode* newNode = new DNode(val);
        if (!head) {
            head = tail = newNode;
        } else {
            newNode->next = head;
            head->prev = newNode;
            head = newNode;
        }
        length++;
    }
    
    void insertAtTail(int val) {
        DNode* newNode = new DNode(val);
        if (!tail) {
            head = tail = newNode;
        } else {
            tail->next = newNode;
            newNode->prev = tail;
            tail = newNode;
        }
        length++;
    }
    
    void deleteAtHead() {
        if (!head) return;
        DNode* temp = head;
        head = head->next;
        if (head) head->prev = nullptr;
        else tail = nullptr;
        delete temp;
        length--;
    }
    
    void deleteAtTail() {
        if (!tail) return;
        DNode* temp = tail;
        tail = tail->prev;
        if (tail) tail->next = nullptr;
        else head = nullptr;
        delete temp;
        length--;
    }
    
    void displayForward() {
        DNode* temp = head;
        while (temp) {
            cout << temp->data << " ↔ ";
            temp = temp->next;
        }
        cout << "NULL\n";
    }
    
    void displayBackward() {
        DNode* temp = tail;
        while (temp) {
            cout << temp->data << " ↔ ";
            temp = temp->prev;
        }
        cout << "NULL\n";
    }
};
```

---

### Complexity Summary

| Operation | Time | Space |
|-----------|------|-------|
| Insert at Head | O(1) | O(1) |
| Insert at Tail | O(1) | O(1) |
| Delete at Head | O(1) | O(1) |
| Delete at Tail | O(1) | O(1) |
| Display Forward | O(n) | O(1) |
| Display Backward | O(n) | O(1) |

---

### Comparison: Singly vs Doubly

| Feature | Singly | Doubly |
|---------|--------|--------|
| Memory per node | 1 pointer | 2 pointers |
| Backward traversal | ❌ No | ✅ Yes |
| Delete at tail | O(n) | O(1) |
| Insert at tail | O(n)* | O(1) |
| Space overhead | Lower | Higher |

*Without tail pointer

---

### Common Use Cases

✅ **Use Doubly Linked List When:**
- Need bidirectional traversal
- Implementing LRU Cache
- Implementing deque (double-ended queue)
- Need O(1) deletion from both ends
- Browser history (back/forward)

❌ **Use Singly Linked List When:**
- Memory is very limited
- Only need forward traversal
- Implementing simple stack/queue

---
---

## 4. Stack

### Overview

**What is it?**
A linear data structure that follows the LIFO (Last In, First Out) principle. Elements can only be added or removed from one end called the "top".

**Why use it?**
- Function call management (recursion)
- Expression evaluation
- Undo/Redo operations
- Backtracking algorithms
- Syntax parsing

**Real-world analogy:**
Think of a stack of plates in a cafeteria. You can only add or remove plates from the top. The last plate placed is the first one to be taken.

**Visual Representation:**
```
        Top
         ↓
        [30]  ← push/pop happen here
        [20]
        [10]
      ─────── Bottom
```

### Structure Definition

```cpp
class Stack {
private:
    int* arr;
    int top;        // Index of top element
    int capacity;   // Maximum size

public:
    Stack(int size = 100);
    ~Stack();
    void push(int val);
    void pop();
    int peek();
    bool isEmpty();
    int size();
};
```

---

### Operation 1: Constructor

**Purpose:** Initialize an empty stack with given capacity.

**Code:**
```cpp
Stack(int size = 100) {
    arr = new int[size];
    capacity = size;
    top = -1;  // -1 indicates empty stack
}
```

**Explanation:**
- Allocate array of given size
- Set `top = -1` to indicate no elements
- When `top = -1`, stack is empty
- When `top = capacity-1`, stack is full

**Visual:**
```
Initial state (capacity = 5):
top = -1

Array: [__][__][__][__][__]
        0   1   2   3   4
        ↑
      top is -1 (no elements)
```

**Time Complexity:** O(1)
**Space Complexity:** O(n) where n is capacity

---

### Operation 2: Push (Add Element)

**Purpose:** Add an element to the top of the stack.

**Code:**
```cpp
void push(int val) {
    // Check for overflow
    if (top >= capacity - 1) {
        throw std::overflow_error("Stack Overflow");
    }
    
    // Increment top and add element
    arr[++top] = val;
}
```

**Explanation (Step-by-Step):**

**Example: Pushing 10, 20, 30**

```
Initial (empty):
top = -1
Array: [__][__][__][__][__]

After push(10):
top = 0
Array: [10][__][__][__][__]
        ↑
       top

After push(20):
top = 1
Array: [10][20][__][__][__]
             ↑
            top

After push(30):
top = 2
Array: [10][20][30][__][__]
                  ↑
                 top
```

**Why ++top and not top++?**
```cpp
arr[++top] = val;  // ✓ Correct: increment first, then use

// Equivalent to:
top = top + 1;
arr[top] = val;

// Wrong:
arr[top++] = val;  // ✗ Would use old value of top, then increment
```

**Overflow Check:**
```
If capacity = 5:
top = 0,1,2,3,4 are valid
top = 5 would be overflow!

That's why: top >= capacity - 1 → overflow
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 3: Pop (Remove Element)

**Purpose:** Remove and return the top element.

**Code:**
```cpp
void pop() {
    // Check for underflow
    if (top < 0) {
        throw std::underflow_error("Stack Underflow");
    }
    
    // Just decrement top
    top--;
}
```

**Explanation:**

```
Before pop():
top = 2
Array: [10][20][30][__][__]
                  ↑
                 top

After pop():
top = 1
Array: [10][20][30][__][__]
             ↑    ↑
            top  "garbage" (ignored)
```

**Why don't we delete the element?**
We just decrement `top`! The value at index 2 still exists but is considered "not in the stack". When we push again, it will be overwritten.

**Underflow Example:**
```
Empty stack:
top = -1

Calling pop() → UNDERFLOW ERROR!
Can't pop from empty stack.
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 4: Peek/Top (View Top Element)

**Purpose:** Return the top element without removing it.

**Code:**
```cpp
int peek() {
    if (top < 0) {
        throw std::underflow_error("Stack is Empty");
    }
    
    return arr[top];
}
```

**Explanation:**

```
Stack state:
top = 2
Array: [10][20][30][__][__]
                  ↑
                 top

peek() returns 30 (doesn't modify stack)

After peek():
Stack unchanged:
top = 2
Array: [10][20][30][__][__]
                  ↑
                 top
```

**Difference from pop():**
```
peek() - View top, don't remove    (read-only)
pop()  - Remove top                (modification)
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 5: isEmpty

**Purpose:** Check if stack has no elements.

**Code:**
```cpp
bool isEmpty() {
    return top < 0;
}
```

**Explanation:**

```
When top = -1:
Array: [__][__][__][__][__]
Stack is empty → isEmpty() returns true

When top >= 0:
Array: [10][20][__][__][__]
Stack has elements → isEmpty() returns false
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 6: Size

**Purpose:** Return the number of elements in stack.

**Code:**
```cpp
int size() {
    return top + 1;
}
```

**Explanation:**

```
top = -1 → size = 0  (empty)
top = 0  → size = 1  (one element)
top = 1  → size = 2  (two elements)
top = n  → size = n+1

Example:
Array: [10][20][30][__][__]
                  ↑
              top = 2

size() = 2 + 1 = 3 elements
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Stack Using Linked List (Alternative Implementation)

**When to use?**
- Don't know maximum size in advance
- Want dynamic sizing
- Don't want to waste memory

**Code:**
```cpp
class StackLL {
private:
    Node* top;
    int count;
    
public:
    StackLL() : top(nullptr), count(0) {}
    
    void push(int val) {
        Node* newNode = new Node(val);
        newNode->next = top;
        top = newNode;
        count++;
    }
    
    void pop() {
        if (!top) throw std::underflow_error("Stack Underflow");
        Node* temp = top;
        top = top->next;
        delete temp;
        count--;
    }
    
    int peek() {
        if (!top) throw std::underflow_error("Stack is Empty");
        return top->data;
    }
    
    bool isEmpty() { return top == nullptr; }
    int size() { return count; }
};
```

**Visual:**
```
Push operations:

After push(10):
top → [10|NULL]

After push(20):
top → [20]→[10|NULL]

After push(30):
top → [30]→[20]→[10|NULL]

Pop operation:
Removes 30:
top → [20]→[10|NULL]
```

**Comparison: Array vs Linked List**

| Feature | Array | Linked List |
|---------|-------|-------------|
| Fixed size | ✅ Yes | ❌ No (dynamic) |
| Memory waste | Possible | None |
| Overflow check | Needed | Not needed |
| Memory per element | Just data | Data + pointer |
| Cache performance | Better | Worse |

---

### Application 1: Valid Parentheses

**Problem:** Check if string has balanced parentheses.

**Code:**
```cpp
bool isValidParentheses(string s) {
    stack<char> st;
    
    for (char c : s) {
        if (c == '(' || c == '{' || c == '[') {
            st.push(c);
        } else {
            if (st.empty()) return false;
            
            char top = st.top();
            if ((c == ')' && top != '(') ||
                (c == '}' && top != '{') ||
                (c == ']' && top != '[')) {
                return false;
            }
            st.pop();
        }
    }
    
    return st.empty();
}
```

**Example:**
```
Input: "({[]})"

Step 1: '(' → push '('
Stack: ['(']

Step 2: '{' → push '{'
Stack: ['(', '{']

Step 3: '[' → push '['
Stack: ['(', '{', '[']

Step 4: ']' → top is '[' ✓ → pop
Stack: ['(', '{']

Step 5: '}' → top is '{' ✓ → pop
Stack: ['(']

Step 6: ')' → top is '(' ✓ → pop
Stack: []

Result: Stack empty → VALID ✓
```

---

### Application 2: Next Greater Element

**Problem:** For each element, find next greater element to the right.

**Code:**
```cpp
vector<int> nextGreaterElement(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> st;
    
    // Traverse from right to left
    for (int i = n - 1; i >= 0; i--) {
        // Pop smaller elements
        while (!st.empty() && st.top() <= arr[i]) {
            st.pop();
        }
        
        // Top of stack is next greater
        if (!st.empty()) {
            result[i] = st.top();
        }
        
        // Push current element
        st.push(arr[i]);
    }
    
    return result;
}
```

**Example:**
```
Input: [4, 5, 2, 10, 8]

i=4 (val=8):
Stack: [8]
result: [-1, -1, -1, -1, -1]

i=3 (val=10):
Pop 8 (8 < 10)
Stack: [10]
result: [-1, -1, -1, -1, -1]

i=2 (val=2):
10 > 2, so next greater = 10
Stack: [10, 2]
result: [-1, -1, 10, -1, -1]

i=1 (val=5):
Pop 2 (2 < 5)
10 > 5, so next greater = 10
Stack: [10, 5]
result: [-1, 10, 10, -1, -1]

i=0 (val=4):
5 > 4, so next greater = 5
Stack: [10, 5, 4]
result: [5, 10, 10, -1, -1]
```

---

### Complete Implementation

```cpp
class Stack {
private:
    int* arr;
    int top;
    int capacity;

public:
    Stack(int size = 100) {
        arr = new int[size];
        capacity = size;
        top = -1;
    }
    
    void push(int val) {
        if (top >= capacity - 1) {
            throw std::overflow_error("Stack Overflow");
        }
        arr[++top] = val;
    }
    
    void pop() {
        if (top < 0) {
            throw std::underflow_error("Stack Underflow");
        }
        top--;
    }
    
    int peek() {
        if (top < 0) {
            throw std::underflow_error("Stack is Empty");
        }
        return arr[top];
    }
    
    bool isEmpty() { return top < 0; }
    int size() { return top + 1; }
    
    ~Stack() { delete[] arr; }
};
```

---

### Complexity Summary

| Operation | Time | Space |
|-----------|------|-------|
| Push | O(1) | O(1) |
| Pop | O(1) | O(1) |
| Peek | O(1) | O(1) |
| isEmpty | O(1) | O(1) |
| Size | O(1) | O(1) |

---

### Common Use Cases

✅ **Use Stack When:**
- Function call management (recursion)
- Expression evaluation (infix, postfix)
- Undo/Redo functionality
- Backtracking problems
- Depth-First Search
- Parsing (syntax checking, compilers)

❌ **Don't Use Stack When:**
- Need to access middle elements
- Need FIFO order (use queue)
- Need random access

---

### Key Takeaways

1. **LIFO Principle:** Last In, First Out
2. **Top Pointer:** Always points to last element
3. **Two Implementations:** Array (fixed size) vs Linked List (dynamic)
4. **Three Main Operations:** Push, Pop, Peek (all O(1))
5. **Applications:** Recursion, expression parsing, backtracking

---
---

## 5. Queue

### Overview

**What is it?**
A linear data structure that follows the FIFO (First In, First Out) principle. Elements are added at the rear and removed from the front.

**Why use it?**
- BFS traversal in trees/graphs
- Scheduling (CPU, disk, printer)
- Buffering (IO buffers, pipes)
- Real-world lines (ticket counters, call centers)

**Real-world analogy:**
Think of a line at a movie theater. The first person in line is the first to buy tickets. New people join at the back of the line.

**Visual Representation:**
```
 Front                           Rear
   ↓                              ↓
  [10] → [20] → [30] → [40]
   ↑                              ↑
 dequeue                       enqueue
(remove here)                 (add here)
```

### Structure Definition

```cpp
class Queue {
private:
    int* arr;
    int front;      // Index of front element
    int rear;       // Index of rear element
    int capacity;   // Maximum size
    int count;      // Current number of elements

public:
    Queue(int size = 100);
    ~Queue();
    void enqueue(int val);
    void dequeue();
    int getFront();
    bool isEmpty();
    int size();
};
```

---

### Operation 1: Constructor

**Purpose:** Initialize an empty queue.

**Code:**
```cpp
Queue(int size = 100) {
    arr = new int[size];
    capacity = size;
    front = 0;
    rear = -1;
    count = 0;
}
```

**Explanation:**
- Allocate array of given capacity
- `front = 0`: Points to first element
- `rear = -1`: Initially no elements
- `count = 0`: Empty queue

**Visual:**
```
Initial state (capacity = 5):

Array: [__][__][__][__][__]
        ↑   
      front=0, rear=-1, count=0
```

**Time Complexity:** O(1)
**Space Complexity:** O(n)

---

### Operation 2: Enqueue (Add Element)

**Purpose:** Add an element to the rear of the queue.

**Code:**
```cpp
void enqueue(int val) {
    // Check if full
    if (count == capacity) {
        throw std::overflow_error("Queue Overflow");
    }
    
    // Circular increment of rear
    rear = (rear + 1) % capacity;
    arr[rear] = val;
    count++;
}
```

**Explanation (Step-by-Step):**

**Linear Queue Problem:**
```
After some operations:
Array: [__][__][10][20][30]
                 ↑       ↑
              front=2  rear=4

Problem: Can't add more even though front is empty!
This is "false overflow"
```

**Circular Queue Solution:**
```
Use modulo to wrap around:
rear = (rear + 1) % capacity

Example with capacity = 5:

Enqueue 10:
rear = (- 1 + 1) % 5 = 0
Array: [10][__][__][__][__]
        ↑
     front=0, rear=0

Enqueue 20:
rear = (0 + 1) % 5 = 1
Array: [10][20][__][__][__]
        ↑    ↑
     front rear

Enqueue 30, 40, 50:
Array: [10][20][30][40][50]
        ↑               ↑
     front            rear=4

After dequeue twice:
Array: [__][__][30][40][50]
                 ↑       ↑
              front=2  rear=4

Enqueue 60:
rear = (4 + 1) % 5 = 0  ← Wraps around!
Array: [60][__][30][40][50]
        ↑    ↑
      rear front=2

Enqueue 70:
rear = (0 + 1) % 5 = 1
Array: [60][70][30][40][50]
             ↑   ↑
           rear front=2
```

**Visual of Circular Nature:**
```
Think of array as a circle:

       [0]
    [4]   [1]
    [3]   [2]

When rear = 4, next position is 0 (wraps around)
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 3: Dequeue (Remove Element)

**Purpose:** Remove and return the front element.

**Code:**
```cpp
void dequeue() {
    // Check if empty
    if (count == 0) {
        throw std::underflow_error("Queue Underflow");
    }
    
    // Circular increment of front
    front = (front + 1) % capacity;
    count--;
}
```

**Explanation:**

```
Before dequeue:
Array: [60][70][30][40][50]
                 ↑
              front=2, count=5

After dequeue:
front = (2 + 1) % 5 = 3
Array: [60][70][30][40][50]
                     ↑
         front=3, count=4
         (30 is now "garbage")

After another dequeue:
front = (3 + 1) % 5 = 4
Array: [60][70][30][40][50]
                         ↑
              front=4, count=3
```

**Why use count?**
```
Without count:

Full queue:
front=2, rear=1
[60][70][30][40][50]
      ↑   ↑
    rear front

Empty queue:
front=2, rear=1  ← Same as full!
[__][__][__][__][__]
      ↑   ↑
    rear front

Can't distinguish! That's why we need count.
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 4: Get Front

**Purpose:** View the front element without removing.

**Code:**
```cpp
int getFront() {
    if (count == 0) {
        throw std::underflow_error("Queue is Empty");
    }
    
    return arr[front];
}
```

**Explanation:**

```
Queue state:
Array: [60][70][30][40][50]
                 ↑
              front=2

getFront() returns 30
Queue unchanged
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 5: isEmpty

**Purpose:** Check if queue is empty.

**Code:**
```cpp
bool isEmpty() {
    return count == 0;
}
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Operation 6: Size

**Purpose:** Return number of elements in queue.

**Code:**
```cpp
int size() {
    return count;
}
```

**Time Complexity:** O(1)
**Space Complexity:** O(1)

---

### Circular Queue Implementation

**Purpose:** Specialized implementation with different approach.

**Code:**
```cpp
class CircularQueue {
private:
    int* arr;
    int front, rear;
    int capacity;
    
public:
    CircularQueue(int k) {
        arr = new int[k];
        capacity = k;
        front = -1;
        rear = -1;
    }
    
    bool enqueue(int value) {
        if (isFull()) return false;
        
        if (isEmpty()) front = 0;
        rear = (rear + 1) % capacity;
        arr[rear] = value;
        return true;
    }
    
    bool dequeue() {
        if (isEmpty()) return false;
        
        if (front == rear) {
            // Last element
            front = rear = -1;
        } else {
            front = (front + 1) % capacity;
        }
        return true;
    }
    
    int Front() {
        return isEmpty() ? -1 : arr[front];
    }
    
    int Rear() {
        return isEmpty() ? -1 : arr[rear];
    }
    
    bool isEmpty() {
        return front == -1;
    }
    
    bool isFull() {
        return (rear + 1) % capacity == front;
    }
    
    ~CircularQueue() { delete[] arr; }
};
```

**How isFull works:**
```
Example: capacity = 5

Full condition:
Array: [10][20][30][40][50]
        ↑               ↑
      front=0        rear=4

Next rear would be: (4+1) % 5 = 0 = front
So: (rear + 1) % capacity == front means FULL
```

---

### Deque (Double-Ended Queue)

**What is it?**
A queue where you can add/remove from BOTH ends.

**Code:**
```cpp
class Deque {
private:
    int* arr;
    int front, rear;
    int capacity;
    int size;
    
public:
    Deque(int cap) : capacity(cap), front(-1), rear(0), size(0) {
        arr = new int[capacity];
    }
    
    bool pushFront(int val) {
        if (isFull()) return false;
        
        if (front == -1) {
            front = rear = 0;
        } else if (front == 0) {
            front = capacity - 1;
        } else {
            front--;
        }
        arr[front] = val;
        size++;
        return true;
    }
    
    bool pushRear(int val) {
        if (isFull()) return false;
        
        if (front == -1) {
            front = rear = 0;
        } else {
            rear = (rear + 1) % capacity;
        }
        arr[rear] = val;
        size++;
        return true;
    }
    
    bool popFront() {
        if (isEmpty()) return false;
        
        if (front == rear) {
            front = -1;
            rear = -1;
        } else {
            front = (front + 1) % capacity;
        }
        size--;
        return true;
    }
    
    bool popRear() {
        if (isEmpty()) return false;
        
        if (front == rear) {
            front = -1;
            rear = -1;
        } else if (rear == 0) {
            rear = capacity - 1;
        } else {
            rear--;
        }
        size--;
        return true;
    }
    
    int getFront() { return isEmpty() ? -1 : arr[front]; }
    int getRear() { return isEmpty() ? -1 : arr[rear]; }
    bool isEmpty() { return front == -1; }
    bool isFull() { return size == capacity; }
    
    ~Deque() { delete[] arr; }
};
```

**Visual:**
```
Deque allows operations at both ends:

pushFront(10):
[10][__][__][__][__]
 ↑                ↑
front           rear

pushRear(20):
[10][__][__][__][20]
 ↑                ↑
front           rear

pushFront(5):
[10][__][__][__][20]
          ↑         ↑
        front      rear
Actually:
[20][__][__][__][10][5]  ← wraps around
          ↑         ↑  ↑
        rear        front
```

---

### Application: BFS in Graphs

**Code:**
```cpp
void BFS(vector<vector<int>>& graph, int start) {
    int n = graph.size();
    vector<bool> visited(n, false);
    queue<int> q;
    
    visited[start] = true;
    q.push(start);
    
    while (!q.empty()) {
        int v = q.front();
        q.pop();
        cout << v << " ";
        
        for (int neighbor : graph[v]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
        }
    }
}
```

**Example:**
```
Graph:
    0 --- 1
    |     |
    2 --- 3

BFS from 0:

Queue: [0]
Visit 0, add neighbors 1,2
Queue: [1, 2]

Dequeue 1, add neighbor 3
Queue: [2, 3]

Dequeue 2 (no new neighbors)
Queue: [3]

Dequeue 3
Queue: []

Order visited: 0 → 1 → 2 → 3
```

---

### Complete Implementation

```cpp
class Queue {
private:
    int* arr;
    int front, rear;
    int capacity;
    int count;

public:
    Queue(int size = 100) {
        arr = new int[size];
        capacity = size;
        front = 0;
        rear = -1;
        count = 0;
    }
    
    void enqueue(int val) {
        if (count == capacity) {
            throw std::overflow_error("Queue Overflow");
        }
        rear = (rear + 1) % capacity;
        arr[rear] = val;
        count++;
    }
    
    void dequeue() {
        if (count == 0) {
            throw std::underflow_error("Queue Underflow");
        }
        front = (front + 1) % capacity;
        count--;
    }
    
    int getFront() {
        if (count == 0) {
            throw std::underflow_error("Queue is Empty");
        }
        return arr[front];
    }
    
    bool isEmpty() { return count == 0; }
    int size() { return count; }
    
    ~Queue() { delete[] arr; }
};
```

---

### Complexity Summary

| Operation | Time | Space |
|-----------|------|-------|
| Enqueue | O(1) | O(1) |
| Dequeue | O(1) | O(1) |
| getFront | O(1) | O(1) |
| isEmpty | O(1) | O(1) |
| Size | O(1) | O(1) |

---

### Common Use Cases

✅ **Use Queue When:**
- BFS traversal
- Level-order tree traversal
- Scheduling tasks (CPU, printer)
- Buffering (streams, IO)
- Handling asynchronous requests

❌ **Don't Use Queue When:**
- Need LIFO order (use stack)
- Need access to middle elements
- Need to remove from middle

---

### Key Takeaways

1. **FIFO Principle:** First In, First Out
2. **Circular Implementation:** Prevents false overflow
3. **Two Pointers:** Front and rear
4. **Count Variable:** Distinguish empty/full
5. **Variants:** Simple, Circular, Deque, Priority

---

*[Due to length limitations, I'll create a second file for the remaining data structures (Part 2: Trees and Part 3: Advanced DS). Would you like me to continue?]*

---

# COMPLEXITY CHEAT SHEET

## All Data Structures - Time Complexity

| Data Structure | Access | Search | Insert | Delete | Space |
|---------------|--------|--------|--------|--------|-------|
| **Array** | O(1) | O(n) | O(n) | O(n) | O(n) |
| **Dynamic Array** | O(1) | O(n) | O(1)* | O(n) | O(n) |
| **Singly Linked List** | O(n) | O(n) | O(1)** | O(1)** | O(n) |
| **Doubly Linked List** | O(n) | O(n) | O(1)** | O(1)** | O(n) |
| **Stack** | O(n) | O(n) | O(1) | O(1) | O(n) |
| **Queue** | O(n) | O(n) | O(1) | O(1) | O(n) |
| **Binary Tree** | O(n) | O(n) | O(n) | O(n) | O(n) |
| **BST (Balanced)** | O(log n) | O(log n) | O(log n) | O(log n) | O(n) |
| **BST (Unbalanced)** | O(n) | O(n) | O(n) | O(n) | O(n) |
| **Min/Max Heap** | O(1)*** | O(n) | O(log n) | O(log n) | O(n) |
| **Hash Table** | - | O(1)**** | O(1)**** | O(1)**** | O(n) |
| **Trie** | O(L) | O(L) | O(L) | O(L) | O(ALPHABET × N × L) |
| **Graph (List)** | - | O(V+E) | O(1) | O(E) | O(V+E) |
| **Segment Tree** | O(log n) | O(log n) | O(n) | - | O(n) |
| **Union-Find** | O(α(n)) | - | O(α(n)) | - | O(n) |

*Amortized  
**At head/tail  
***Get min/max only  
****Average case; O(n) worst case  
L = length of string/word  
α(n) = inverse Ackermann function (effectively constant)

---

## WHEN TO USE WHICH DATA STRUCTURE

### Array / Vector
✅ **Use when:**
- Need random access
- Know approximate size
- Cache performance matters
- Mostly reading data

❌ **Avoid when:**
- Frequent insertions in middle
- Size unknown and highly variable
- Need O(1) front insertion

---

### Linked List
✅ **Use when:**
- Frequent insertions/deletions
- Don't need random access
- Implementing stacks/queues
- Size unknown

❌ **Avoid when:**
- Need fast random access
- Cache performance critical
- Memory overhead is concern

---

### Stack
✅ **Use when:**
- LIFO order needed
- Function calls / recursion
- Expression evaluation
- Backtracking
- Undo/redo functionality

❌ **Avoid when:**
- Need FIFO
- Need random access

---

### Queue
✅ **Use when:**
- FIFO order needed
- BFS traversal
- Scheduling
- Buffering
- Level-order processing

❌ **Avoid when:**
- Need LIFO
- Need random access

---

### Binary Search Tree
✅ **Use when:**
- Need sorted data
- Frequent search/insert/delete
- Range queries
- Finding min/max

❌ **Avoid when:**
- Data mostly unsorted
- Need O(1) operations
- Balanced tree not maintained

---

### Heap
✅ **Use when:**
- Need min/max quickly
- Priority queue
- K largest/smallest
- Median finding

❌ **Avoid when:**
- Need sorted order
- Frequent arbitrary access

---

### Hash Table
✅ **Use when:**
- Fast lookup needed
- Insert/delete/search in O(1)
- Counting frequencies
- Caching

❌ **Avoid when:**
- Need sorted order
- Memory is very limited
- Keys are not hashable

---

### Graph
✅ **Use when:**
- Modeling relationships
- Network problems
- Path finding
- Connected components

❌ **Avoid when:**
- Simple linear relationships
- No connections between elements

---

### Trie
✅ **Use when:**
- Prefix matching
- Autocomplete
- Spell checker
- IP routing

❌ **Avoid when:**
- Limited alphabet
- Memory is concern
- Simple string search

---

## COMMON PATTERNS

### Two Pointer
```cpp
// Opposite ends
int left = 0, right = n - 1;
while (left < right) {
    // Process
    if (condition) left++;
    else right--;
}

// Slow-Fast
int slow = 0;
for (int fast = 0; fast < n; fast++) {
    if (condition) {
        swap(arr[slow++], arr[fast]);
    }
}
```

### Sliding Window
```cpp
// Fixed size
int sum = 0;
for (int i = 0; i < k; i++) sum += arr[i];
int maxSum = sum;
for (int i = k; i < n; i++) {
    sum += arr[i] - arr[i-k];
    maxSum = max(maxSum, sum);
}

// Variable size
int left = 0, maxLen = 0;
for (int right = 0; right < n; right++) {
    // Add arr[right]
    while (invalid_condition) {
        // Remove arr[left]
        left++;
    }
    maxLen = max(maxLen, right - left + 1);
}
```

### Binary Search
```cpp
int left = 0, right = n - 1;
while (left <= right) {
    int mid = left + (right - left) / 2;
    if (arr[mid] == target) return mid;
    else if (arr[mid] < target) left = mid + 1;
    else right = mid - 1;
}
```

---

**END OF PART 1**

*This reference manual continues in Part 2 with Tree Data Structures and Part 3 with Advanced Data Structures. Each part is designed for printing and quick reference.*

---

**Page Count Guide:**
- Part 1 (Basic DS): ~40-50 pages
- Part 2 (Trees): ~30-40 pages
- Part 3 (Advanced DS): ~30-40 pages
- Total: ~100-130 pages when printed

**Printing Tips:**
1. Print double-sided to save paper
2. Use 3-hole punch for binder
3. Consider printing different parts in different colors
4. Add page dividers between major sections
5. Keep in a sturdy binder for long-term use
