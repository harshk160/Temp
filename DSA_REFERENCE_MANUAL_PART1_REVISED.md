# 📘 Data Structures Implementation Reference Manual

**A Complete Reference Guide for C++ Implementations**

*Print this document for quick reference during coding sessions*

---

## Table of Contents

### Part 1: Basic Data Structures
1. Dynamic Array (Vector)
2. Singly Linked List
3. Doubly Linked List
4. Stack
5. Queue

### Part 2: Tree Data Structures
6. Binary Tree
7. Binary Search Tree
8. Min Heap
9. Max Heap

### Part 3: Advanced Data Structures
10. Hash Table](#10-hash-table)
11. Graph](#11-graph)
12. Trie (Prefix Tree)
13. Union-Find (Disjoint Set)
14. Segment Tree

### Part 4: Quick Reference
- Complexity Cheat Sheet
- When to Use Which DS
- Common Patterns
---
---

# PART 1: BASIC DATA STRUCTURES

---

## 1. Dynamic Array (Vector)

### Overview

**What is it?**
A dynamic array is a resizable array that automatically grows when more space is needed. Unlike static arrays with fixed size, dynamic arrays can expand to accommodate new elements.

**Why use it?**
- ✅ Random access in O(1) time
- ✅ Automatic memory management
- ✅ Cache-friendly (contiguous memory)
- ✅ Versatile for most general-purpose needs

**Real-world analogy:**
Think of a parking lot that can add more spaces when full. Initially you have 4 spots, but when all are filled, you double to 8 spots.

**Visual Representation:**
```
Initial state (capacity = 4, size = 3):
┌────┬────┬────┬────┐
│ 10 │ 20 │ 30 │ __ │
└────┴────┴────┴────┘
  0    1    2    3

After pushing 40 (capacity = 4, size = 4):
┌────┬────┬────┬────┐
│ 10 │ 20 │ 30 │ 40 │  ← FULL!
└────┴────┴────┴────┘

After pushing 50 (resize triggered, capacity = 8, size = 5):
┌────┬────┬────┬────┬────┬────┬────┬────┐
│ 10 │ 20 │ 30 │ 40 │ 50 │ __ │ __ │ __ │
└────┴────┴────┴────┴────┴────┴────┴────┘
```

---

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
    int operator[](int index);
    int size();
    int getCapacity();
    void clear();
    bool empty();
};
```

**Member Variables Explained:**
- `arr`: Points to the actual array in heap memory
- `capacity`: How many elements the array CAN hold before resizing
- `current`: How many elements are ACTUALLY stored

**Key Difference:**
```
Size     = Number of elements stored
Capacity = Total space allocated

Example: size=3, capacity=8
         [10][20][30][__][__][__][__][__]
          ↑ ↑ ↑ ↑─── stored elements (size=3)
          └─────────────────────────────┘
                  allocated space (capacity=8)
```

---

### Operation 1: Constructor

**Purpose:** Initialize an empty vector with initial capacity.

**Code:**
```cpp
Vector() {
    arr = new int[1];      // Allocate initial space for 1 element
    capacity = 1;          // Initial capacity
    current = 0;           // No elements yet (empty)
}
```

**What happens in memory:**
```
Before constructor:
arr → ?  (uninitialized pointer)

After constructor:
                  ┌────┐
arr ────────────→ │ __ │  (1 slot allocated in heap)
                  └────┘
capacity = 1
current = 0
```

**Why start with capacity = 1?**
- Minimal memory footprint initially
- Will double as needed (1 → 2 → 4 → 8 → 16...)
- Amortized O(1) insertion

**Time Complexity:** O(1)  
**Space Complexity:** O(1)

---

### Operation 2: Push Back (Add Element)

**Purpose:** Add an element to the end of the vector. Resize if capacity is full.

**Code:**
```cpp
void push_back(int data) {
    // Step 1: Check if resize is needed
    if (current == capacity) {
        // Create new array with double capacity
        int* temp = new int[2 * capacity];
        
        // Copy all elements to new array
        for (int i = 0; i < capacity; i++) {
            temp[i] = arr[i];
        }
        
        // Free old array memory
        delete[] arr;
        
        // Update capacity and pointer
        capacity *= 2;
        arr = temp;
    }
    
    // Step 2: Add new element at current position
    arr[current] = data;
    current++;
}
```

**Detailed Explanation:**

**Scenario 1: Space Available (No Resize)**
```
Before: capacity=4, size=2
┌────┬────┬────┬────┐
│ 10 │ 20 │ __ │ __ │
└────┴────┴────┴────┘
             ↑
      Insert 30 here

After: capacity=4, size=3
┌────┬────┬────┬────┐
│ 10 │ 20 │ 30 │ __ │
└────┴────┴────┴────┘
                  ↑
           Ready for next
```

**Scenario 2: Resize Required**
```
Before: capacity=4, size=4 (FULL!)
┌────┬────┬────┬────┐
│ 10 │ 20 │ 30 │ 40 │
└────┴────┴────┴────┘

Insert 50:

Step 1: Create temp array (capacity = 8)
        ┌────┬────┬────┬────┬────┬────┬────┬────┐
temp →  │ __ │ __ │ __ │ __ │ __ │ __ │ __ │ __ │
        └────┴────┴────┴────┴────┴────┴────┴────┘

Step 2: Copy existing elements
        ┌────┬────┬────┬────┬────┬────┬────┬────┐
temp →  │ 10 │ 20 │ 30 │ 40 │ __ │ __ │ __ │ __ │
        └────┴────┴────┴────┴────┴────┴────┴────┘

Step 3: Delete old array
        [10][20][30][40] ← deleted from heap

Step 4: Update pointer and capacity
        ┌────┬────┬────┬────┬────┬────┬────┬────┐
arr →   │ 10 │ 20 │ 30 │ 40 │ __ │ __ │ __ │ __ │
        └────┴────┴────┴────┴────┴────┴────┴────┘
        capacity = 8

Step 5: Add new element
        ┌────┬────┬────┬────┬────┬────┬────┬────┐
arr →   │ 10 │ 20 │ 30 │ 40 │ 50 │ __ │ __ │ __ │
        └────┴────┴────┴────┴────┴────┴────┴────┘
        current = 5
```

**Why double the capacity?**

Let's compare different growth strategies:

```
Strategy 1: Increase by 1
Insertions: 1000
Resizes needed: 1000
Total copies: 1+2+3+...+1000 = 500,500 ❌ O(n²)

Strategy 2: Double capacity
Insertions: 1000
Resizes needed: ~10 (1→2→4→8→16→32→64→128→256→512→1024)
Total copies: 1+2+4+8+...+512 = 1023 ✅ O(n)

Doubling gives amortized O(1) per insertion!
```

**Time Complexity:**
- Best case: O(1) - space available
- Worst case: O(n) - resize needed (copy all elements)
- **Amortized: O(1)** - resize happens infrequently

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

**Detailed Explanation:**

We simply decrement the `current` counter. The actual value remains in memory but is considered "not part of the vector" anymore.

**Visual:**
```
Before pop_back: size=4
┌────┬────┬────┬────┐
│ 10 │ 20 │ 30 │ 40 │
└────┴────┴────┴────┘
                  ↑
          current points here

After pop_back: size=3
┌────┬────┬────┬────┐
│ 10 │ 20 │ 30 │ 40 │ ← 40 still in memory!
└────┴────┴────┴────┘
             ↑
      current=3 (excludes index 3)
```

**Why don't we actually delete the element?**

1. **Performance:** No need to write zeros or deallocate
2. **Simplicity:** Just decrement counter
3. **Will be overwritten:** Next push_back will use that slot

**What if we pop from empty vector?**
```cpp
Vector v;  // Empty, current=0
v.pop_back();  // Does nothing (current stays 0)

Better implementation with error checking:
void pop_back() {
    if (current == 0) {
        throw std::underflow_error("Vector is empty");
    }
    current--;
}
```

**Time Complexity:** O(1)  
**Space Complexity:** O(1)

---

### Operation 4: Access Element (at)

**Purpose:** Access element at a specific index with bounds checking.

**Code:**
```cpp
int at(int index) {
    // Bounds checking
    if (index < 0 || index >= current) {
        throw std::out_of_range("Index out of bounds");
    }
    return arr[index];
}
```

**Detailed Explanation:**

The `at()` function provides **safe** array access with error checking.

**Visual:**
```
Vector: size=4, capacity=8
┌────┬────┬────┬────┬────┬────┬────┬────┐
│ 10 │ 20 │ 30 │ 40 │ __ │ __ │ __ │ __ │
└────┴────┴────┴────┴────┴────┴────┴────┘
  0    1    2    3    4    5    6    7
  ↑              ↑    ↑─────────────────┘
Valid         Valid  Invalid (beyond current)

at(2) → Returns 30 ✅
at(5) → Throws exception ❌ (index >= current)
at(-1) → Throws exception ❌ (negative index)
```

**Difference between at() and operator[]:**

```cpp
// at() - Safe, with bounds checking
int val = v.at(10);  // Throws exception if out of bounds

// operator[] - Fast, NO bounds checking
int val = v[10];     // Undefined behavior if out of bounds!

When to use each:
- at(): When safety is important (user input, untrusted indices)
- []:   When you know index is valid (in tight loops for speed)
```

**Time Complexity:** O(1) - direct memory access  
**Space Complexity:** O(1)

---

### Operation 5: Operator[] (Array Subscript)

**Purpose:** Direct array access without bounds checking (faster).

**Code:**
```cpp
int operator[](int index) {
    return arr[index];
}
```

**When to use:**
```cpp
// Safe but slower
for (int i = 0; i < v.size(); i++) {
    int x = v.at(i);  // Checks bounds every time
}

// Fast but assumes valid index
for (int i = 0; i < v.size(); i++) {
    int x = v[i];  // No bounds check - faster!
}
```

**Time Complexity:** O(1)  
**Space Complexity:** O(1)

---

### Operation 6: Get Size

**Purpose:** Return the number of elements currently in the vector.

**Code:**
```cpp
int size() {
    return current;
}
```

**Visual:**
```
Vector: capacity=8, current=3
┌────┬────┬────┬────┬────┬────┬────┬────┐
│ 10 │ 20 │ 30 │ __ │ __ │ __ │ __ │ __ │
└────┴────┴────┴────┴────┴────┴────┴────┘
  └──────┬──────┘
      size() = 3 (NOT 8!)
```

**Common Mistake:**
```cpp
Vector v;
v.push_back(10);
v.push_back(20);

// WRONG!
for (int i = 0; i < v.getCapacity(); i++) {  // Iterates 8 times!
    // Accesses garbage values
}

// CORRECT!
for (int i = 0; i < v.size(); i++) {  // Iterates 2 times
    // Only accesses valid elements
}
```

**Time Complexity:** O(1)  
**Space Complexity:** O(1)

---

### Operation 7: Get Capacity

**Purpose:** Return the total allocated space.

**Code:**
```cpp
int getCapacity() {
    return capacity;
}
```

**Size vs Capacity Example:**
```
After several operations:
┌────┬────┬────┬────┬────┬────┬────┬────┐
│ 10 │ 20 │ 30 │ __ │ __ │ __ │ __ │ __ │
└────┴────┴────┴────┴────┴────┴────┴────┘

size() = 3       (3 elements stored)
capacity() = 8   (can hold 8 before resize)

Space wasted = capacity - size = 5 slots
```

**Time Complexity:** O(1)  
**Space Complexity:** O(1)

---

### Operation 8: Check Empty

**Purpose:** Check if vector contains no elements.

**Code:**
```cpp
bool empty() {
    return current == 0;
}
```

**Usage:**
```cpp
Vector v;

if (v.empty()) {
    cout << "Vector is empty\n";  // This prints
}

v.push_back(10);

if (!v.empty()) {
    cout << "Vector has elements\n";  // This prints
}
```

**Time Complexity:** O(1)  
**Space Complexity:** O(1)

---

### Operation 9: Clear

**Purpose:** Remove all elements from the vector.

**Code:**
```cpp
void clear() {
    current = 0;  // Reset size to 0
    // Note: capacity remains unchanged
}
```

**Visual:**
```
Before clear: size=5, capacity=8
┌────┬────┬────┬────┬────┬────┬────┬────┐
│ 10 │ 20 │ 30 │ 40 │ 50 │ __ │ __ │ __ │
└────┴────┴────┴────┴────┴────┴────┴────┘

After clear: size=0, capacity=8
┌────┬────┬────┬────┬────┬────┬────┬────┐
│ 10 │ 20 │ 30 │ 40 │ 50 │ __ │ __ │ __ │ ← Data still here!
└────┴────┴────┴────┴────┴────┴────┴────┘
But current=0, so all elements "invisible"
```

**Why keep capacity?**
- If you refill the vector, no reallocation needed
- Common pattern: repeatedly fill, process, clear, refill

**Time Complexity:** O(1)  
**Space Complexity:** O(1)

---

### Operation 10: Destructor

**Purpose:** Free allocated memory when vector is destroyed.

**Code:**
```cpp
~Vector() {
    delete[] arr;  // Free the dynamic array
}
```

**What happens:**
```
When vector goes out of scope:

{
    Vector v;
    v.push_back(10);
    v.push_back(20);
}  ← v goes out of scope here

Destructor automatically called:
1. delete[] arr frees heap memory
2. Prevents memory leak
```

**Without Destructor (MEMORY LEAK!):**
```cpp
void badFunction() {
    Vector* v = new Vector();
    v->push_back(10);
    // Forgot to delete v!
}  // Memory leaked! arr stays in heap forever
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
    // Constructor
    Vector() {
        arr = new int[1];
        capacity = 1;
        current = 0;
    }
    
    // Add element at end
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
    
    // Remove last element
    void pop_back() {
        if (current > 0) {
            current--;
        }
    }
    
    // Access with bounds checking
    int at(int index) {
        if (index < 0 || index >= current) {
            throw std::out_of_range("Index out of bounds");
        }
        return arr[index];
    }
    
    // Access without bounds checking
    int operator[](int index) {
        return arr[index];
    }
    
    // Get number of elements
    int size() {
        return current;
    }
    
    // Get allocated capacity
    int getCapacity() {
        return capacity;
    }
    
    // Check if empty
    bool empty() {
        return current == 0;
    }
    
    // Remove all elements
    void clear() {
        current = 0;
    }
    
    // Destructor
    ~Vector() {
        delete[] arr;
    }
};
```

---

### Usage Example

```cpp
int main() {
    Vector v;
    
    // Add elements
    v.push_back(10);
    v.push_back(20);
    v.push_back(30);
    
    cout << "Size: " << v.size() << endl;        // 3
    cout << "Capacity: " << v.getCapacity() << endl;  // 4
    
    // Access elements
    cout << "Element at index 1: " << v.at(1) << endl;  // 20
    
    // Iterate
    for (int i = 0; i < v.size(); i++) {
        cout << v[i] << " ";  // 10 20 30
    }
    cout << endl;
    
    // Remove last
    v.pop_back();
    cout << "After pop: " << v.size() << endl;  // 2
    
    // Clear
    v.clear();
    cout << "After clear: " << v.size() << endl;  // 0
    cout << "Empty? " << (v.empty() ? "Yes" : "No") << endl;  // Yes
    
    return 0;
}  // Destructor automatically called here
```

---

### Complexity Summary

| Operation | Time Complexity | Space Complexity | Notes |
|-----------|----------------|------------------|-------|
| Constructor | O(1) | O(1) | Allocates initial capacity |
| push_back | O(1)* | O(1) | *Amortized |
| pop_back | O(1) | O(1) | Just decrements size |
| at | O(1) | O(1) | With bounds checking |
| operator[] | O(1) | O(1) | No bounds checking |
| size | O(1) | O(1) | Returns counter |
| capacity | O(1) | O(1) | Returns capacity |
| empty | O(1) | O(1) | Checks if size=0 |
| clear | O(1) | O(1) | Resets size counter |
| Destructor | O(1) | O(1) | Frees memory |

---

### Common Use Cases

✅ **Use Vector When:**
- You need random access to elements
- You mostly add/remove from the end
- You don't know the size in advance
- Cache-friendly performance matters
- Need automatic memory management

❌ **Don't Use Vector When:**
- Frequent insertions/deletions in the middle (use LinkedList)
- Need O(1) insertion at front (use Deque)
- Memory is very limited (size + capacity overhead)
- Fixed size known at compile time (use array)

---

### Key Takeaways

1. **Dynamic Sizing:** Automatically grows as needed
2. **Doubling Strategy:** Capacity doubles each resize for amortized O(1)
3. **Size vs Capacity:** Size = elements stored, Capacity = total space
4. **Memory Management:** Must free memory in destructor
5. **Trade-off:** Wastes some space for fast amortized insertion

---
---

## 2. Singly Linked List

### Overview

**What is it?**
A linear data structure where each element (node) contains data and a pointer to the next node. Elements are NOT stored contiguously in memory.

**Why use it?**
- ✅ O(1) insertion/deletion at head
- ✅ No resizing needed (unlike array)
- ✅ Efficient memory usage for unknown size
- ✅ Foundation for stacks, queues, graphs
- ✅ Dynamic size without wasted capacity

**Real-world analogy:**
Think of a treasure hunt where each clue points to the next location. You can only go forward, and finding clue #5 means following clues 1→2→3→4→5.

**Visual Representation:**
```
Head → [10|●]→[20|●]→[30|●]→[40|NULL]
        ┌──┬──┐
        │10│●─┤ ← Node structure
        └──┴──┘
        data next
```

---

### Structure Definition

```cpp
// Node structure
class Node {
public:
    int data;       // The value stored
    Node* next;     // Pointer to next node
    
    // Constructor
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
    
    // Insertion
    void insertAtHead(int val);
    void insertAtTail(int val);
    void insertAtPosition(int pos, int val);
    
    // Deletion
    void deleteAtHead();
    void deleteAtTail();
    void deleteAtPosition(int pos);
    
    // Utilities
    void reverse();
    int getMiddle();
    bool detectCycle();
    bool search(int val);
    void display();
    int size();
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

**Visual:**
```
Empty list:
head → NULL

Memory state:
No nodes allocated yet
Just one pointer (head) set to NULL
```

**Time Complexity:** O(1)  
**Space Complexity:** O(1)

---

### Operation 2: Insert at Head

**Purpose:** Add a new node at the beginning of the list.

**Code:**
```cpp
void insertAtHead(int val) {
    // Step 1: Create new node
    Node* newNode = new Node(val);
    
    // Step 2: Point new node to current head
    newNode->next = head;
    
    // Step 3: Update head to new node
    head = newNode;
    
    length++;
}
```

**Detailed Step-by-Step Explanation:**

**Initial State:**
```
Existing list:
head → [10|●]→[20|●]→[30|NULL]

We want to insert 5 at the head
```

**Step 1: Create new node**
```
newNode → [5|NULL]

head → [10|●]→[20|●]→[30|NULL]

Two separate entities:
- newNode points to new node with data=5
- head still points to old first node
```

**Step 2: Point newNode to current head**
```
newNode → [5|●]
            ↓
          [10|●]→[20|●]→[30|NULL]
           ↑
          head

Now newNode->next points to the old first node
```

**Step 3: Update head**
```
          [5|●]→[10|●]→[20|●]→[30|NULL]
           ↑
          head
          newNode

head now points to newNode
List is: 5 → 10 → 20 → 30 → NULL
```

**Why is this O(1)?**
```
We DON'T traverse the list!
Just update two pointers:
1. newNode->next = head   ← One assignment
2. head = newNode          ← One assignment

No loops, no traversal → O(1)
```

**Comparison with Array:**
```
Array insertion at front:
[10][20][30][40]
Insert 5 at front:
Need to shift ALL elements right!
[__][10][20][30][40]
  ↑
[5][10][20][30][40]

Cost: O(n) - must shift n elements

Linked List:
Just change pointers → O(1)!
```

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

**Detailed Step-by-Step Explanation:**

**Scenario 1: Empty List**
```
Before: head → NULL

insertAtTail(10):

Step 1: Create newNode
newNode → [10|NULL]

Step 2: Check if head is NULL → YES!
head → [10|NULL]
       newNode

Done! List is now: 10 → NULL
```

**Scenario 2: Non-Empty List**
```
Before: head → [10|●]→[20|●]→[30|NULL]

insertAtTail(40):

Step 1: Create newNode
newNode → [40|NULL]

Step 2: head is not NULL, so traverse:
temp starts at head
temp → [10|●]→[20|●]→[30|NULL]

Iteration 1: temp->next != NULL → move forward
temp → [10|●]→[20|●]→[30|NULL]
                ↑
              temp

Iteration 2: temp->next != NULL → move forward
temp → [10|●]→[20|●]→[30|NULL]
                        ↑
                      temp

Iteration 3: temp->next == NULL → stop!
temp is at last node [30]

Step 3: Link last node to newNode
head → [10|●]→[20|●]→[30|●]→[40|NULL]
                        ↑     ↑
                      temp  newNode

Done! List is: 10 → 20 → 30 → 40 → NULL
```

**Why is this O(n)?**
```
Must traverse ENTIRE list to find last node:
[10] → [20] → [30] → [40] → NULL
 ↓      ↓      ↓      ↓
step1  step2  step3  found!

n nodes → n steps to traverse → O(n)
```

**Optimization: Keep Tail Pointer**
```cpp
class LinkedList {
private:
    Node* head;
    Node* tail;  // ← Add this!
};

void insertAtTail(int val) {
    Node* newNode = new Node(val);
    if (!head) {
        head = tail = newNode;
    } else {
        tail->next = newNode;
        tail = newNode;
    }
    length++;
}
// Now O(1) instead of O(n)!
```

**Time Complexity:** O(n) without tail pointer, O(1) with tail pointer  
**Space Complexity:** O(1)

---

### Operation 4: Insert at Position

**Purpose:** Insert a node at a specific position in the list.

**Code:**
```cpp
void insertAtPosition(int pos, int val) {
    // Position 0 is head
    if (pos == 0) {
        insertAtHead(val);
        return;
    }
    
    // Check if position is valid
    if (pos < 0 || pos > length) {
        throw std::out_of_range("Invalid position");
    }
    
    // Create new node
    Node* newNode = new Node(val);
    
    // Traverse to position-1
    Node* temp = head;
    for (int i = 0; i < pos - 1; i++) {
        temp = temp->next;
    }
    
    // Insert new node
    newNode->next = temp->next;
    temp->next = newNode;
    
    length++;
}
```

**Example:**
```
List: 10 → 20 → 40 → NULL
Insert 30 at position 2

Step 1: Traverse to position 1
temp → [10|●]→[20|●]→[40|NULL]
                ↑
            position 1

Step 2: Create and link new node
newNode → [30|●]
            ↓
        [20|●]  [40|NULL]

Step 3: Link temp to newNode
[10|●]→[20|●]→[30|●]→[40|NULL]

Result: 10 → 20 → 30 → 40 → NULL
```

**Time Complexity:** O(n) - traverse to position  
**Space Complexity:** O(1)

---

### Operation 5: Delete at Head

**Purpose:** Remove the first node from the list.

**Code:**
```cpp
void deleteAtHead() {
    // Check if list is empty
    if (!head) {
        return;  // or throw exception
    }
    
    // Save reference to current head
    Node* temp = head;
    
    // Move head to next node
    head = head->next;
    
    // Free memory of old head
    delete temp;
    
    length--;
}
```

**Detailed Step-by-Step:**
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
        [X]  [20|●]→[30|NULL]
              ↑
             head

Memory at [10] is freed and returned to OS
```

**Why save temp first?**
```
WRONG (Memory Leak):
head = head->next;  // Lost reference to [10]!
delete head;        // Deletes [20], not [10]!

CORRECT:
Node* temp = head;  // Save reference to [10]
head = head->next;  // Move head to [20]
delete temp;        // Free [10]
```

**Edge Case: Deleting Last Node**
```
Before: head → [10|NULL]

After delete:
head → NULL  (empty list)
```

**Time Complexity:** O(1)  
**Space Complexity:** O(1)

---

### Operation 6: Delete at Tail

**Purpose:** Remove the last node from the list.

**Code:**
```cpp
void deleteAtTail() {
    if (!head) return;
    
    // If only one node
    if (!head->next) {
        delete head;
        head = nullptr;
        length--;
        return;
    }
    
    // Traverse to second-last node
    Node* temp = head;
    while (temp->next->next != nullptr) {
        temp = temp->next;
    }
    
    // Delete last node
    delete temp->next;
    temp->next = nullptr;
    length--;
}
```

**Why O(n) for Singly Linked List?**
```
To delete last node, need to update second-last node's next

head → [10|●]→[20|●]→[30|NULL]
                ↑     ↑
           need to    want to
           reach here delete this

Must traverse to second-last → O(n)

In Doubly Linked List:
tail->prev->next = nullptr
delete tail
tail = tail->prev
→ O(1) because we have prev pointer!
```

**Time Complexity:** O(n)  
**Space Complexity:** O(1)

---

### Operation 7: Reverse the List

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

**Detailed Visualization:**

**Initial:**
```
prev=NULL  curr=head           next=NULL
           ↓
NULL      [10|●]→[20|●]→[30|NULL]
```

**Iteration 1:**
```
Step 1: next = curr->next
prev   curr   next
NULL   [10]   [20|●]→[30|NULL]

Step 2: curr->next = prev
NULL←[10]   [20|●]→[30|NULL]
      ↑     ↑
     curr  next

Step 3: prev = curr, curr = next
      prev  curr
NULL←[10]   [20|●]→[30|NULL]
```

**Iteration 2:**
```
Step 1: next = curr->next
      prev  curr  next
NULL←[10]   [20]  [30|NULL]

Step 2: curr->next = prev
NULL←[10]←[20]   [30|NULL]

Step 3: prev = curr, curr = next
           prev  curr
NULL←[10]←[20]   [30|NULL]
```

**Iteration 3:**
```
Step 1: next = curr->next
           prev  curr  next
NULL←[10]←[20]   [30]  NULL

Step 2: curr->next = prev
NULL←[10]←[20]←[30]   NULL

Step 3: prev = curr, curr = next
                prev  curr
NULL←[10]←[20]←[30]   NULL
```

**Final:**
```
curr = NULL → loop ends
head = prev

head → [30|●]→[20|●]→[10|NULL]

Reversed!
```

**Time Complexity:** O(n) - visit each node once  
**Space Complexity:** O(1) - only 3 pointers

---

### Operation 8: Find Middle (Slow-Fast Pointer)

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

**Why This Works (Tortoise and Hare Algorithm):**

When fast reaches the end, slow is at the middle!

**Example with ODD number of nodes (5):**
```
Initial:
slow
fast
 ↓
[1]→[2]→[3]→[4]→[5]→NULL

After iteration 1:
slow moves 1, fast moves 2
   slow
        fast
    ↓    ↓
[1]→[2]→[3]→[4]→[5]→NULL

After iteration 2:
       slow
                fast
        ↓        ↓
[1]→[2]→[3]→[4]→[5]→NULL

After iteration 3:
            slow
                     fast
             ↓        ↓
[1]→[2]→[3]→[4]→[5]→NULL
             ↑
          MIDDLE!

fast reaches NULL → stop
slow is at middle node (3)
```

**Example with EVEN number of nodes (4):**
```
Initial:
slow/fast
 ↓
[1]→[2]→[3]→[4]→NULL

After iteration 1:
   slow
        fast
    ↓    ↓
[1]→[2]→[3]→[4]→NULL

After iteration 2:
       slow
             fast
        ↓     ↓
[1]→[2]→[3]→[4]→NULL
        ↑
    2nd middle (returns 3)

fast->next is NULL → stop
Returns 2nd of 2 middle nodes
```

**Why not just count length/2?**
```
Method 1: Count then traverse
- First traversal: count nodes → O(n)
- Second traversal: go to middle → O(n/2)
- Total: O(n) + O(n/2) = O(3n/2)

Method 2: Slow-Fast pointers
- Single traversal → O(n/2)
- Total: O(n/2)

Slow-fast is more efficient!
```

**Time Complexity:** O(n)  
**Space Complexity:** O(1)

---

### Operation 9: Detect Cycle (Floyd's Algorithm)

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

**How It Works:**

**Case 1: No Cycle**
```
[1]→[2]→[3]→[4]→NULL

slow and fast will never meet
fast reaches NULL → no cycle

Timeline:
Start:  slow=[1], fast=[1]
Step 1: slow=[2], fast=[3]
Step 2: slow=[3], fast=NULL
→ Loop ends, return false
```

**Case 2: Cycle Exists**
```
[1]→[2]→[3]→[4]
     ↑        ↓
     [6]←[5]←┘

Timeline:
Start:  slow=[1], fast=[1]
Step 1: slow=[2], fast=[3]
Step 2: slow=[3], fast=[5]
Step 3: slow=[4], fast=[2]  (fast wrapped around)
Step 4: slow=[5], fast=[4]
Step 5: slow=[6], fast=[6]  ← MEET!
→ Return true
```

**Why They Must Meet if Cycle Exists:**

```
Think of it as a circular track:
- Slow runner: 1 lap/minute
- Fast runner: 2 laps/minute

Fast runner is catching up at 1 lap/minute
Eventually fast will "lap" slow
When they're in same cycle, they MUST meet!

Mathematical proof:
- Distance between them decreases by 1 each step
- When distance = 0 → they meet
- This WILL happen in finite steps
```

**Time Complexity:** O(n)  
**Space Complexity:** O(1)

---

### Operation 10: Search

**Purpose:** Find if a value exists in the list.

**Code:**
```cpp
bool search(int val) {
    Node* temp = head;
    
    while (temp != nullptr) {
        if (temp->data == val) {
            return true;
        }
        temp = temp->next;
    }
    
    return false;
}
```

**Visual:**
```
List: [10]→[20]→[30]→[40]→NULL
Search for 30:

Step 1: temp=[10], 10≠30, move
Step 2: temp=[20], 20≠30, move
Step 3: temp=[30], 30=30, found! Return true

Search for 50:
Step 1-4: Check all nodes
Step 5: temp=NULL, not found, return false
```

**Time Complexity:** O(n) - worst case check all nodes  
**Space Complexity:** O(1)

---

### Operation 11: Display

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

**Visual:**
```
List: head → [10|●]→[20|●]→[30|NULL]

Traversal:
temp = head → print 10 → 
temp = temp->next → print 20 → 
temp = temp->next → print 30 → 
temp = temp->next → temp=NULL → print NULL

Output: 10 → 20 → 30 → NULL
```

**Time Complexity:** O(n)  
**Space Complexity:** O(1)

---

### Operation 12: Get Size

**Purpose:** Return the number of nodes in the list.

**Code:**
```cpp
int size() {
    return length;
}
```

**Alternative (without length variable):**
```cpp
int size() {
    int count = 0;
    Node* temp = head;
    while (temp != nullptr) {
        count++;
        temp = temp->next;
    }
    return count;
}
// O(n) time vs O(1) with length variable
```

**Time Complexity:** O(1) with length variable, O(n) without  
**Space Complexity:** O(1)

---

### Operation 13: Destructor

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

**Why We Need This:**
```
Each node is dynamically allocated:
Node* newNode = new Node(val);  ← Allocates heap memory

If we don't delete:
{
    LinkedList list;
    list.insertAtHead(10);
    list.insertAtHead(20);
}  ← list goes out of scope

Without destructor: MEMORY LEAK!
Nodes [10] and [20] stay in heap forever

With destructor:
All nodes properly freed
Memory returned to OS
```

**Process:**
```
List: [10]→[20]→[30]→NULL

Step 1: curr=[10], next=[20], delete [10]
Step 2: curr=[20], next=[30], delete [20]
Step 3: curr=[30], next=NULL, delete [30]
Step 4: curr=NULL, loop ends

All memory freed!
```

**Time Complexity:** O(n) - delete each node  
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
        } else {
            Node* temp = head;
            while (temp->next) temp = temp->next;
            temp->next = newNode;
        }
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
    
    bool search(int val) {
        Node* temp = head;
        while (temp) {
            if (temp->data == val) return true;
            temp = temp->next;
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
    
    int size() { return length; }
    
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
| Insert at Tail | O(n)* | O(1) |
| Delete at Head | O(1) | O(1) |
| Delete at Tail | O(n) | O(1) |
| Reverse | O(n) | O(1) |
| Find Middle | O(n) | O(1) |
| Detect Cycle | O(n) | O(1) |
| Search | O(n) | O(1) |
| Display | O(n) | O(1) |

*O(1) with tail pointer

---

### Key Patterns Used

**1. Two Pointer Technique (Slow-Fast)**
- Finding middle element
- Detecting cycles
- Finding nth node from end

**2. Three Pointer Reversal (prev, curr, next)**
- Reversing the list
- Reversing in groups of k

**3. Runner Technique**
- Fast pointer moves 2x speed of slow
- Detects cycles, finds middle

---

### Comparison: Array vs Linked List

| Feature | Array | Linked List |
|---------|-------|-------------|
| Access | O(1) | O(n) |
| Insert at front | O(n) | O(1) |
| Insert at end | O(1)* | O(n)** |
| Delete at front | O(n) | O(1) |
| Memory | Contiguous | Scattered |
| Cache | Friendly | Unfriendly |
| Overhead | None | Pointer per node |

*Amortized for dynamic array  
**O(1) with tail pointer

---

### Common Use Cases

✅ **Use Linked List When:**
- Frequent insertions/deletions at beginning
- Don't need random access
- Size is unknown or changes frequently
- Implementing stacks, queues, graphs
- Memory fragmentation is acceptable

❌ **Don't Use Linked List When:**
- Need random access (use array/vector)
- Memory overhead is a concern
- Cache performance is critical
- Need to access middle elements frequently

---

### Key Takeaways

1. **No Contiguous Memory:** Nodes scattered in memory
2. **Pointer-Based:** Each node points to next
3. **O(1) Head Operations:** Insert/delete at head is fast
4. **O(n) Traversal:** Must follow pointers sequentially
5. **Memory Management:** Must manually free all nodes
6. **Two Pointers:** Powerful technique for many problems

---

*[Document continues with Doubly Linked List, Stack, and Queue...]*

*Due to length, I'll pause here. Would you like me to continue with the remaining sections?*

---

# COMPLEXITY CHEAT SHEET

## Time Complexity Overview

| Data Structure | Access | Search | Insert | Delete | Space |
|---------------|--------|--------|--------|--------|-------|
| **Array** | O(1) | O(n) | O(n) | O(n) | O(n) |
| **Dynamic Array** | O(1) | O(n) | O(1)* | O(n) | O(n) |
| **Singly Linked List** | O(n) | O(n) | O(1)** | O(1)** | O(n) |
| **Doubly Linked List** | O(n) | O(n) | O(1)** | O(1)** | O(n) |
| **Stack** | O(n) | O(n) | O(1) | O(1) | O(n) |
| **Queue** | O(n) | O(n) | O(1) | O(1) | O(n) |

*Amortized  
**At head/tail with proper pointers

---

## WHEN TO USE WHICH DATA STRUCTURE

### Dynamic Array / Vector
✅ **Use when:**
- Need fast random access (O(1))
- Mostly adding to end
- Know approximate size
- Cache performance matters

❌ **Avoid when:**
- Frequent insertions in middle
- Need O(1) front insertion

---

### Linked List
✅ **Use when:**
- Frequent insertions/deletions
- Don't need random access
- Size unknown and variable
- Implementing other DS (stacks, queues)

❌ **Avoid when:**
- Need fast random access
- Cache performance critical
- Memory overhead is concern

---

### Stack
✅ **Use when:**
- LIFO order needed
- Expression evaluation
- Backtracking
- Function call management
- Undo/redo functionality

❌ **Avoid when:**
- Need FIFO order
- Need random access

---

### Queue
✅ **Use when:**
- FIFO order needed
- BFS traversal
- Scheduling tasks
- Buffering
- Level-order processing

❌ **Avoid when:**
- Need LIFO order
- Need random access

---

## COMMON PATTERNS

### Two Pointer
```cpp
// Opposite ends
int left = 0, right = n - 1;
while (left < right) {
    if (condition) left++;
    else right--;
}

// Slow-Fast
Node *slow = head, *fast = head;
while (fast && fast->next) {
    slow = slow->next;
    fast = fast->next->next;
}
```

### Sliding Window
```cpp
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

---

**END OF PART 1 (REVISED)**

---

**Revision Summary:**
✅ Fixed all code errors
✅ Added better visual diagrams
✅ Improved step-by-step explanations
✅ Added "Why?" sections
✅ Consistent formatting
✅ More examples and edge cases
✅ Print-friendly layout

**Would you like me to:**
1. Continue with Parts 2 & 3 (Trees, Hash Tables, Graphs, etc.)?
2. Add more practice problems?
3. Create a separate "Quick Reference Card" (1-2 pages)?
