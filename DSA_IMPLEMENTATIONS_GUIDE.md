# 🔧 DSA Implementations Guide

## Essential Data Structures to Implement Before Starting Your Journey

> **Purpose:** Build muscle memory and deep understanding by implementing these data structures from scratch. This foundation will make your DSA journey much smoother.

---

## 📋 Table of Contents

1. [Implementation Strategy](#-implementation-strategy)
2. [Basic Data Structures](#-basic-data-structures)
3. [Intermediate Data Structures](#-intermediate-data-structures)
4. [Advanced Data Structures](#-advanced-data-structures)
5. [Implementation Checklist](#-implementation-checklist)
6. [Testing Your Implementations](#-testing-your-implementations)

---

## 🎯 Implementation Strategy

### How to Approach Each Data Structure

For **each data structure**, implement in this order:

1. ✅ **Understand the concept** - What problem does it solve?
2. ✅ **Draw it on paper** - Visualize with examples
3. ✅ **Write pseudo-code** - Plan before coding
4. ✅ **Implement from scratch** - No copy-paste
5. ✅ **Test with examples** - Use multiple test cases
6. ✅ **Analyze complexity** - Time and space for each operation
7. ✅ **Compare with STL** - See how C++ implements it

### Timeline

- **Week 1:** Basic DS (Arrays, Linked Lists, Stacks, Queues)
- **Week 2:** Intermediate DS (Trees, BST, Heaps, Hash Tables)
- **Week 3:** Advanced DS (Tries, Segment Trees, Union-Find)

---

## 🟢 BASIC DATA STRUCTURES

### 1. Dynamic Array (Vector Implementation)

**What to Implement:**
```
Operations:
├── Constructor/Destructor
├── push_back(value)         - Add element at end
├── pop_back()               - Remove last element
├── at(index)                - Access element with bounds checking
├── operator[]               - Direct access
├── size()                   - Get current size
├── capacity()               - Get allocated capacity
├── resize(newSize)          - Change size
├── reserve(newCapacity)     - Pre-allocate memory
├── clear()                  - Remove all elements
├── empty()                  - Check if empty
└── insert(index, value)     - Insert at position
```

**Key Concepts to Understand:**
- Dynamic memory allocation
- Amortized O(1) for push_back
- When and how resizing happens (typically double capacity)
- Difference between size and capacity

**Implementation Template:**
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
    
    int size() { return current; }
    int getCapacity() { return capacity; }
    
    int at(int index) {
        if (index < 0 || index >= current) {
            throw std::out_of_range("Index out of bounds");
        }
        return arr[index];
    }
    
    ~Vector() {
        delete[] arr;
    }
};
```

**Complexity Analysis:**
| Operation | Time Complexity | Space |
|-----------|----------------|-------|
| push_back | O(1) amortized | O(1) |
| pop_back | O(1) | O(1) |
| at/[] | O(1) | O(1) |
| insert | O(n) | O(1) |

---

### 2. Singly Linked List

**What to Implement:**
```
Operations:
├── insertAtHead(value)      - Add at beginning
├── insertAtTail(value)      - Add at end
├── insertAtPosition(pos, val) - Insert at index
├── deleteAtHead()           - Remove first node
├── deleteAtTail()           - Remove last node
├── deleteAtPosition(pos)    - Remove at index
├── search(value)            - Find element
├── reverse()                - Reverse the list
├── getMiddle()              - Find middle element
├── detectCycle()            - Check for cycle
├── display()                - Print all elements
└── size()                   - Get length
```

**Implementation Template:**
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
    
    // Insert at head - O(1)
    void insertAtHead(int val) {
        Node* newNode = new Node(val);
        newNode->next = head;
        head = newNode;
        length++;
    }
    
    // Insert at tail - O(n)
    void insertAtTail(int val) {
        Node* newNode = new Node(val);
        if (!head) {
            head = newNode;
            length++;
            return;
        }
        
        Node* temp = head;
        while (temp->next) {
            temp = temp->next;
        }
        temp->next = newNode;
        length++;
    }
    
    // Delete at head - O(1)
    void deleteAtHead() {
        if (!head) return;
        Node* temp = head;
        head = head->next;
        delete temp;
        length--;
    }
    
    // Reverse - O(n)
    void reverse() {
        Node* prev = nullptr;
        Node* curr = head;
        Node* next = nullptr;
        
        while (curr) {
            next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        head = prev;
    }
    
    // Find middle (slow-fast pointer) - O(n)
    int getMiddle() {
        if (!head) throw std::runtime_error("List is empty");
        
        Node* slow = head;
        Node* fast = head;
        
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow->data;
    }
    
    // Detect cycle (Floyd's algorithm) - O(n)
    bool detectCycle() {
        if (!head) return false;
        
        Node* slow = head;
        Node* fast = head;
        
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast) return true;
        }
        return false;
    }
    
    // Display - O(n)
    void display() {
        Node* temp = head;
        while (temp) {
            cout << temp->data << " -> ";
            temp = temp->next;
        }
        cout << "NULL\n";
    }
    
    int size() { return length; }
    
    // Destructor
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

**Key Patterns to Master:**
- **Two Pointer Technique** (slow-fast)
- **Reversal** (iterative and recursive)
- **Cycle Detection** (Floyd's algorithm)

---

### 3. Doubly Linked List

**What to Implement:**
```
Operations:
├── insertAtHead(value)
├── insertAtTail(value)
├── insertAtPosition(pos, val)
├── deleteAtHead()
├── deleteAtTail()
├── deleteAtPosition(pos)
├── reverse()
├── displayForward()
└── displayBackward()
```

**Implementation Template:**
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
    
    // Insert at head - O(1)
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
    
    // Insert at tail - O(1)
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
    
    // Delete at head - O(1)
    void deleteAtHead() {
        if (!head) return;
        DNode* temp = head;
        head = head->next;
        if (head) head->prev = nullptr;
        else tail = nullptr;
        delete temp;
        length--;
    }
    
    // Delete at tail - O(1)
    void deleteAtTail() {
        if (!tail) return;
        DNode* temp = tail;
        tail = tail->prev;
        if (tail) tail->next = nullptr;
        else head = nullptr;
        delete temp;
        length--;
    }
    
    // Reverse - O(n)
    void reverse() {
        DNode* curr = head;
        DNode* temp = nullptr;
        
        while (curr) {
            temp = curr->prev;
            curr->prev = curr->next;
            curr->next = temp;
            curr = curr->prev;
        }
        
        if (temp) head = temp->prev;
        
        // Swap head and tail
        temp = head;
        head = tail;
        tail = temp;
    }
    
    void displayForward() {
        DNode* temp = head;
        while (temp) {
            cout << temp->data << " <-> ";
            temp = temp->next;
        }
        cout << "NULL\n";
    }
    
    void displayBackward() {
        DNode* temp = tail;
        while (temp) {
            cout << temp->data << " <-> ";
            temp = temp->prev;
        }
        cout << "NULL\n";
    }
};
```

**Use Case:** LRU Cache, Browser history

---

### 4. Stack

**What to Implement:**
```
Operations:
├── push(value)              - Add element
├── pop()                    - Remove top element
├── top()/peek()             - View top element
├── isEmpty()                - Check if empty
├── size()                   - Get number of elements
└── clear()                  - Remove all elements

Implement using:
├── Array-based
└── Linked List-based
```

**Array-based Implementation:**
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
    
    // Push - O(1)
    void push(int val) {
        if (top >= capacity - 1) {
            throw std::overflow_error("Stack Overflow");
        }
        arr[++top] = val;
    }
    
    // Pop - O(1)
    void pop() {
        if (top < 0) {
            throw std::underflow_error("Stack Underflow");
        }
        top--;
    }
    
    // Peek - O(1)
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

**Linked List-based Implementation:**
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

**Applications to Implement:**
- Valid Parentheses checker
- Infix to Postfix conversion
- Evaluate Postfix expression
- Next Greater Element

---

### 5. Queue

**What to Implement:**
```
Operations:
├── enqueue(value)           - Add element
├── dequeue()                - Remove front element
├── front()                  - View front element
├── isEmpty()                - Check if empty
├── size()                   - Get number of elements
└── clear()                  - Remove all elements

Implement:
├── Simple Queue (Array)
├── Simple Queue (Linked List)
└── Circular Queue (Array)
```

**Array-based Queue:**
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
    
    // Enqueue - O(1)
    void enqueue(int val) {
        if (count == capacity) {
            throw std::overflow_error("Queue Overflow");
        }
        rear = (rear + 1) % capacity;
        arr[rear] = val;
        count++;
    }
    
    // Dequeue - O(1)
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

**Circular Queue:**
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

**Deque (Double-ended Queue):**
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

---

## 🟡 INTERMEDIATE DATA STRUCTURES

### 6. Binary Tree

**What to Implement:**
```
Structure:
└── TreeNode (val, left, right)

Operations:
├── insert(value)            - Add node
├── inorder()                - Left → Root → Right
├── preorder()               - Root → Left → Right
├── postorder()              - Left → Right → Root
├── levelOrder()             - BFS traversal
├── height()                 - Max depth
├── size()                   - Count nodes
├── search(value)            - Find node
├── isBalanced()             - Check if balanced
└── diameter()               - Longest path
```

**Implementation:**
```cpp
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class BinaryTree {
private:
    TreeNode* root;
    
    // Helper for inorder
    void inorderHelper(TreeNode* node) {
        if (!node) return;
        inorderHelper(node->left);
        cout << node->val << " ";
        inorderHelper(node->right);
    }
    
    // Helper for height
    int heightHelper(TreeNode* node) {
        if (!node) return 0;
        return 1 + max(heightHelper(node->left), heightHelper(node->right));
    }
    
    // Helper for size
    int sizeHelper(TreeNode* node) {
        if (!node) return 0;
        return 1 + sizeHelper(node->left) + sizeHelper(node->right);
    }
    
public:
    BinaryTree() : root(nullptr) {}
    
    // Level order insertion
    void insert(int val) {
        TreeNode* newNode = new TreeNode(val);
        if (!root) {
            root = newNode;
            return;
        }
        
        queue<TreeNode*> q;
        q.push(root);
        
        while (!q.empty()) {
            TreeNode* curr = q.front();
            q.pop();
            
            if (!curr->left) {
                curr->left = newNode;
                return;
            } else {
                q.push(curr->left);
            }
            
            if (!curr->right) {
                curr->right = newNode;
                return;
            } else {
                q.push(curr->right);
            }
        }
    }
    
    // Inorder traversal - O(n)
    void inorder() {
        inorderHelper(root);
        cout << endl;
    }
    
    // Preorder traversal - O(n)
    void preorder(TreeNode* node) {
        if (!node) return;
        cout << node->val << " ";
        preorder(node->left);
        preorder(node->right);
    }
    
    // Postorder traversal - O(n)
    void postorder(TreeNode* node) {
        if (!node) return;
        postorder(node->left);
        postorder(node->right);
        cout << node->val << " ";
    }
    
    // Level order traversal - O(n)
    void levelOrder() {
        if (!root) return;
        
        queue<TreeNode*> q;
        q.push(root);
        
        while (!q.empty()) {
            int levelSize = q.size();
            for (int i = 0; i < levelSize; i++) {
                TreeNode* curr = q.front();
                q.pop();
                cout << curr->val << " ";
                
                if (curr->left) q.push(curr->left);
                if (curr->right) q.push(curr->right);
            }
            cout << endl;
        }
    }
    
    int height() { return heightHelper(root); }
    int size() { return sizeHelper(root); }
    
    TreeNode* getRoot() { return root; }
};
```

---

### 7. Binary Search Tree (BST)

**What to Implement:**
```
Operations:
├── insert(value)            - Add node maintaining BST property
├── search(value)            - Find node
├── delete(value)            - Remove node
├── findMin()                - Minimum value
├── findMax()                - Maximum value
├── inorder()                - Sorted traversal
├── isValidBST()             - Validate BST
├── kthSmallest(k)           - Find kth smallest
└── lowestCommonAncestor()   - Find LCA
```

**Implementation:**
```cpp
class BST {
private:
    TreeNode* root;
    
    // Insert helper - O(h)
    TreeNode* insertHelper(TreeNode* node, int val) {
        if (!node) return new TreeNode(val);
        
        if (val < node->val) {
            node->left = insertHelper(node->left, val);
        } else if (val > node->val) {
            node->right = insertHelper(node->right, val);
        }
        return node;
    }
    
    // Search helper - O(h)
    bool searchHelper(TreeNode* node, int val) {
        if (!node) return false;
        if (node->val == val) return true;
        if (val < node->val) return searchHelper(node->left, val);
        return searchHelper(node->right, val);
    }
    
    // Find minimum - O(h)
    TreeNode* findMin(TreeNode* node) {
        while (node && node->left) {
            node = node->left;
        }
        return node;
    }
    
    // Delete helper - O(h)
    TreeNode* deleteHelper(TreeNode* node, int val) {
        if (!node) return nullptr;
        
        if (val < node->val) {
            node->left = deleteHelper(node->left, val);
        } else if (val > node->val) {
            node->right = deleteHelper(node->right, val);
        } else {
            // Node with one child or no child
            if (!node->left) {
                TreeNode* temp = node->right;
                delete node;
                return temp;
            } else if (!node->right) {
                TreeNode* temp = node->left;
                delete node;
                return temp;
            }
            
            // Node with two children
            TreeNode* temp = findMin(node->right);
            node->val = temp->val;
            node->right = deleteHelper(node->right, temp->val);
        }
        return node;
    }
    
    // Validate BST helper
    bool isValidBSTHelper(TreeNode* node, long minVal, long maxVal) {
        if (!node) return true;
        if (node->val <= minVal || node->val >= maxVal) return false;
        return isValidBSTHelper(node->left, minVal, node->val) &&
               isValidBSTHelper(node->right, node->val, maxVal);
    }
    
public:
    BST() : root(nullptr) {}
    
    void insert(int val) {
        root = insertHelper(root, val);
    }
    
    bool search(int val) {
        return searchHelper(root, val);
    }
    
    void remove(int val) {
        root = deleteHelper(root, val);
    }
    
    int findMinValue() {
        TreeNode* minNode = findMin(root);
        return minNode ? minNode->val : -1;
    }
    
    bool isValidBST() {
        return isValidBSTHelper(root, LONG_MIN, LONG_MAX);
    }
    
    // Inorder gives sorted sequence
    void inorder(TreeNode* node) {
        if (!node) return;
        inorder(node->left);
        cout << node->val << " ";
        inorder(node->right);
    }
    
    TreeNode* getRoot() { return root; }
};
```

**Key Property:** Inorder traversal of BST gives sorted sequence

---

### 8. Min Heap & Max Heap

**What to Implement:**
```
Operations:
├── insert(value)            - Add element
├── extractMin()/extractMax() - Remove root
├── getMin()/getMax()        - View root
├── heapify()                - Convert array to heap
├── decreaseKey(i, newVal)   - Update key
├── delete(i)                - Delete element at index
├── size()                   - Get number of elements
└── isEmpty()                - Check if empty
```

**Min Heap Implementation:**
```cpp
class MinHeap {
private:
    vector<int> heap;
    
    int parent(int i) { return (i - 1) / 2; }
    int leftChild(int i) { return 2 * i + 1; }
    int rightChild(int i) { return 2 * i + 2; }
    
    // Heapify up - O(log n)
    void heapifyUp(int i) {
        while (i > 0 && heap[parent(i)] > heap[i]) {
            swap(heap[i], heap[parent(i)]);
            i = parent(i);
        }
    }
    
    // Heapify down - O(log n)
    void heapifyDown(int i) {
        int minIndex = i;
        int left = leftChild(i);
        int right = rightChild(i);
        
        if (left < heap.size() && heap[left] < heap[minIndex]) {
            minIndex = left;
        }
        if (right < heap.size() && heap[right] < heap[minIndex]) {
            minIndex = right;
        }
        
        if (i != minIndex) {
            swap(heap[i], heap[minIndex]);
            heapifyDown(minIndex);
        }
    }
    
public:
    MinHeap() {}
    
    // Insert - O(log n)
    void insert(int val) {
        heap.push_back(val);
        heapifyUp(heap.size() - 1);
    }
    
    // Extract minimum - O(log n)
    int extractMin() {
        if (heap.empty()) {
            throw std::underflow_error("Heap is empty");
        }
        
        int minVal = heap[0];
        heap[0] = heap.back();
        heap.pop_back();
        
        if (!heap.empty()) {
            heapifyDown(0);
        }
        return minVal;
    }
    
    // Get minimum - O(1)
    int getMin() {
        if (heap.empty()) {
            throw std::underflow_error("Heap is empty");
        }
        return heap[0];
    }
    
    // Build heap from array - O(n)
    void buildHeap(vector<int>& arr) {
        heap = arr;
        for (int i = heap.size() / 2 - 1; i >= 0; i--) {
            heapifyDown(i);
        }
    }
    
    int size() { return heap.size(); }
    bool isEmpty() { return heap.empty(); }
    
    void display() {
        for (int val : heap) {
            cout << val << " ";
        }
        cout << endl;
    }
};
```

**Max Heap Implementation:**
```cpp
class MaxHeap {
private:
    vector<int> heap;
    
    int parent(int i) { return (i - 1) / 2; }
    int leftChild(int i) { return 2 * i + 1; }
    int rightChild(int i) { return 2 * i + 2; }
    
    void heapifyUp(int i) {
        while (i > 0 && heap[parent(i)] < heap[i]) {
            swap(heap[i], heap[parent(i)]);
            i = parent(i);
        }
    }
    
    void heapifyDown(int i) {
        int maxIndex = i;
        int left = leftChild(i);
        int right = rightChild(i);
        
        if (left < heap.size() && heap[left] > heap[maxIndex]) {
            maxIndex = left;
        }
        if (right < heap.size() && heap[right] > heap[maxIndex]) {
            maxIndex = right;
        }
        
        if (i != maxIndex) {
            swap(heap[i], heap[maxIndex]);
            heapifyDown(maxIndex);
        }
    }
    
public:
    void insert(int val) {
        heap.push_back(val);
        heapifyUp(heap.size() - 1);
    }
    
    int extractMax() {
        if (heap.empty()) throw std::underflow_error("Heap is empty");
        
        int maxVal = heap[0];
        heap[0] = heap.back();
        heap.pop_back();
        
        if (!heap.empty()) heapifyDown(0);
        return maxVal;
    }
    
    int getMax() {
        if (heap.empty()) throw std::underflow_error("Heap is empty");
        return heap[0];
    }
    
    bool isEmpty() { return heap.empty(); }
    int size() { return heap.size(); }
};
```

**Applications:**
- Priority Queue
- Heap Sort
- K largest/smallest elements
- Median of stream

---

### 9. Hash Table

**What to Implement:**
```
Operations:
├── insert(key, value)       - Add key-value pair
├── get(key)                 - Retrieve value
├── remove(key)              - Delete key-value pair
├── contains(key)            - Check if key exists
├── size()                   - Get number of pairs
└── clear()                  - Remove all pairs

Collision Handling:
├── Chaining (Linked List)
└── Open Addressing (Linear Probing)
```

**Separate Chaining Implementation:**
```cpp
class HashNode {
public:
    int key;
    int value;
    HashNode* next;
    
    HashNode(int k, int v) : key(k), value(v), next(nullptr) {}
};

class HashMap {
private:
    static const int TABLE_SIZE = 100;
    HashNode** table;
    
    // Hash function
    int hashFunction(int key) {
        return key % TABLE_SIZE;
    }
    
public:
    HashMap() {
        table = new HashNode*[TABLE_SIZE];
        for (int i = 0; i < TABLE_SIZE; i++) {
            table[i] = nullptr;
        }
    }
    
    // Insert - O(1) average
    void insert(int key, int value) {
        int hash = hashFunction(key);
        HashNode* newNode = new HashNode(key, value);
        
        if (!table[hash]) {
            table[hash] = newNode;
        } else {
            // Check if key exists
            HashNode* curr = table[hash];
            HashNode* prev = nullptr;
            
            while (curr) {
                if (curr->key == key) {
                    curr->value = value; // Update value
                    delete newNode;
                    return;
                }
                prev = curr;
                curr = curr->next;
            }
            prev->next = newNode;
        }
    }
    
    // Get - O(1) average
    int get(int key) {
        int hash = hashFunction(key);
        HashNode* curr = table[hash];
        
        while (curr) {
            if (curr->key == key) {
                return curr->value;
            }
            curr = curr->next;
        }
        throw std::runtime_error("Key not found");
    }
    
    // Remove - O(1) average
    void remove(int key) {
        int hash = hashFunction(key);
        HashNode* curr = table[hash];
        HashNode* prev = nullptr;
        
        while (curr) {
            if (curr->key == key) {
                if (prev) {
                    prev->next = curr->next;
                } else {
                    table[hash] = curr->next;
                }
                delete curr;
                return;
            }
            prev = curr;
            curr = curr->next;
        }
    }
    
    // Contains - O(1) average
    bool contains(int key) {
        int hash = hashFunction(key);
        HashNode* curr = table[hash];
        
        while (curr) {
            if (curr->key == key) return true;
            curr = curr->next;
        }
        return false;
    }
    
    ~HashMap() {
        for (int i = 0; i < TABLE_SIZE; i++) {
            HashNode* curr = table[i];
            while (curr) {
                HashNode* next = curr->next;
                delete curr;
                curr = next;
            }
        }
        delete[] table;
    }
};
```

**Open Addressing (Linear Probing):**
```cpp
class HashMapLinearProbing {
private:
    static const int TABLE_SIZE = 100;
    int* keys;
    int* values;
    bool* occupied;
    
    int hashFunction(int key) {
        return key % TABLE_SIZE;
    }
    
public:
    HashMapLinearProbing() {
        keys = new int[TABLE_SIZE];
        values = new int[TABLE_SIZE];
        occupied = new bool[TABLE_SIZE];
        
        for (int i = 0; i < TABLE_SIZE; i++) {
            occupied[i] = false;
        }
    }
    
    void insert(int key, int value) {
        int hash = hashFunction(key);
        int i = hash;
        
        while (occupied[i]) {
            if (keys[i] == key) {
                values[i] = value;
                return;
            }
            i = (i + 1) % TABLE_SIZE;
            if (i == hash) {
                throw std::overflow_error("Hash table is full");
            }
        }
        
        keys[i] = key;
        values[i] = value;
        occupied[i] = true;
    }
    
    int get(int key) {
        int hash = hashFunction(key);
        int i = hash;
        
        while (occupied[i]) {
            if (keys[i] == key) {
                return values[i];
            }
            i = (i + 1) % TABLE_SIZE;
            if (i == hash) break;
        }
        throw std::runtime_error("Key not found");
    }
    
    ~HashMapLinearProbing() {
        delete[] keys;
        delete[] values;
        delete[] occupied;
    }
};
```

---

## 🔴 ADVANCED DATA STRUCTURES

### 10. Graph (Adjacency List & Matrix)

**What to Implement:**
```
Representations:
├── Adjacency List
└── Adjacency Matrix

Operations:
├── addVertex()
├── addEdge(u, v)            - Undirected
├── addDirectedEdge(u, v)    - Directed
├── removeEdge(u, v)
├── hasEdge(u, v)
├── BFS(start)               - Breadth-first traversal
├── DFS(start)               - Depth-first traversal
├── printGraph()
└── getNeighbors(vertex)
```

**Adjacency List Implementation:**
```cpp
class Graph {
private:
    int vertices;
    vector<vector<int>> adjList;
    
public:
    Graph(int v) : vertices(v) {
        adjList.resize(v);
    }
    
    // Add undirected edge - O(1)
    void addEdge(int u, int v) {
        adjList[u].push_back(v);
        adjList[v].push_back(u);
    }
    
    // Add directed edge - O(1)
    void addDirectedEdge(int u, int v) {
        adjList[u].push_back(v);
    }
    
    // BFS - O(V + E)
    void BFS(int start) {
        vector<bool> visited(vertices, false);
        queue<int> q;
        
        visited[start] = true;
        q.push(start);
        
        while (!q.empty()) {
            int v = q.front();
            q.pop();
            cout << v << " ";
            
            for (int neighbor : adjList[v]) {
                if (!visited[neighbor]) {
                    visited[neighbor] = true;
                    q.push(neighbor);
                }
            }
        }
        cout << endl;
    }
    
    // DFS - O(V + E)
    void DFSUtil(int v, vector<bool>& visited) {
        visited[v] = true;
        cout << v << " ";
        
        for (int neighbor : adjList[v]) {
            if (!visited[neighbor]) {
                DFSUtil(neighbor, visited);
            }
        }
    }
    
    void DFS(int start) {
        vector<bool> visited(vertices, false);
        DFSUtil(start, visited);
        cout << endl;
    }
    
    // Print graph
    void printGraph() {
        for (int i = 0; i < vertices; i++) {
            cout << i << ": ";
            for (int neighbor : adjList[i]) {
                cout << neighbor << " ";
            }
            cout << endl;
        }
    }
};
```

**Weighted Graph:**
```cpp
class WeightedGraph {
private:
    int vertices;
    vector<vector<pair<int, int>>> adjList; // {neighbor, weight}
    
public:
    WeightedGraph(int v) : vertices(v) {
        adjList.resize(v);
    }
    
    void addEdge(int u, int v, int weight) {
        adjList[u].push_back({v, weight});
        adjList[v].push_back({u, weight});
    }
    
    void addDirectedEdge(int u, int v, int weight) {
        adjList[u].push_back({v, weight});
    }
    
    void printGraph() {
        for (int i = 0; i < vertices; i++) {
            cout << i << ": ";
            for (auto [neighbor, weight] : adjList[i]) {
                cout << "(" << neighbor << "," << weight << ") ";
            }
            cout << endl;
        }
    }
};
```

---

### 11. Trie (Prefix Tree)

**What to Implement:**
```
Operations:
├── insert(word)             - Add word
├── search(word)             - Check if word exists
├── startsWith(prefix)       - Check if prefix exists
├── delete(word)             - Remove word
└── countWordsWithPrefix()   - Count words with prefix
```

**Implementation:**
```cpp
class TrieNode {
public:
    TrieNode* children[26];
    bool isEndOfWord;
    int wordCount;
    
    TrieNode() {
        isEndOfWord = false;
        wordCount = 0;
        for (int i = 0; i < 26; i++) {
            children[i] = nullptr;
        }
    }
};

class Trie {
private:
    TrieNode* root;
    
public:
    Trie() {
        root = new TrieNode();
    }
    
    // Insert - O(L) where L is word length
    void insert(string word) {
        TrieNode* curr = root;
        
        for (char c : word) {
            int index = c - 'a';
            if (!curr->children[index]) {
                curr->children[index] = new TrieNode();
            }
            curr = curr->children[index];
        }
        
        curr->isEndOfWord = true;
        curr->wordCount++;
    }
    
    // Search - O(L)
    bool search(string word) {
        TrieNode* curr = root;
        
        for (char c : word) {
            int index = c - 'a';
            if (!curr->children[index]) {
                return false;
            }
            curr = curr->children[index];
        }
        
        return curr->isEndOfWord;
    }
    
    // Starts with prefix - O(L)
    bool startsWith(string prefix) {
        TrieNode* curr = root;
        
        for (char c : prefix) {
            int index = c - 'a';
            if (!curr->children[index]) {
                return false;
            }
            curr = curr->children[index];
        }
        
        return true;
    }
    
    // Delete word - O(L)
    bool deleteWord(TrieNode* curr, string word, int index) {
        if (index == word.length()) {
            if (!curr->isEndOfWord) return false;
            curr->isEndOfWord = false;
            return isEmpty(curr);
        }
        
        int charIndex = word[index] - 'a';
        if (!curr->children[charIndex]) return false;
        
        bool shouldDeleteChild = deleteWord(curr->children[charIndex], word, index + 1);
        
        if (shouldDeleteChild) {
            delete curr->children[charIndex];
            curr->children[charIndex] = nullptr;
            return !curr->isEndOfWord && isEmpty(curr);
        }
        
        return false;
    }
    
    bool isEmpty(TrieNode* node) {
        for (int i = 0; i < 26; i++) {
            if (node->children[i]) return false;
        }
        return true;
    }
    
    void deleteWord(string word) {
        deleteWord(root, word, 0);
    }
};
```

**Applications:**
- Autocomplete
- Spell checker
- IP routing
- Prefix matching

---

### 12. Union-Find (Disjoint Set Union)

**What to Implement:**
```
Operations:
├── makeSet(x)               - Create singleton set
├── find(x)                  - Find representative
├── union(x, y)              - Merge two sets
├── connected(x, y)          - Check if in same set
└── countSets()              - Number of disjoint sets

Optimizations:
├── Path Compression
└── Union by Rank/Size
```

**Implementation with Path Compression & Union by Rank:**
```cpp
class UnionFind {
private:
    vector<int> parent;
    vector<int> rank;
    int components;
    
public:
    UnionFind(int n) : components(n) {
        parent.resize(n);
        rank.resize(n, 0);
        
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
    }
    
    // Find with path compression - O(α(n)) amortized
    int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]); // Path compression
        }
        return parent[x];
    }
    
    // Union by rank - O(α(n)) amortized
    bool unite(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        
        if (rootX == rootY) return false;
        
        // Union by rank
        if (rank[rootX] < rank[rootY]) {
            parent[rootX] = rootY;
        } else if (rank[rootX] > rank[rootY]) {
            parent[rootY] = rootX;
        } else {
            parent[rootY] = rootX;
            rank[rootX]++;
        }
        
        components--;
        return true;
    }
    
    // Check if connected - O(α(n)) amortized
    bool connected(int x, int y) {
        return find(x) == find(y);
    }
    
    int countComponents() {
        return components;
    }
};
```

**Union by Size (Alternative):**
```cpp
class UnionFindBySize {
private:
    vector<int> parent;
    vector<int> size;
    
public:
    UnionFindBySize(int n) {
        parent.resize(n);
        size.resize(n, 1);
        
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
    }
    
    int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]);
        }
        return parent[x];
    }
    
    bool unite(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        
        if (rootX == rootY) return false;
        
        // Union by size (attach smaller to larger)
        if (size[rootX] < size[rootY]) {
            parent[rootX] = rootY;
            size[rootY] += size[rootX];
        } else {
            parent[rootY] = rootX;
            size[rootX] += size[rootY];
        }
        
        return true;
    }
    
    int getSize(int x) {
        return size[find(x)];
    }
};
```

**Applications:**
- Kruskal's MST algorithm
- Cycle detection in undirected graphs
- Network connectivity
- Image processing (connected components)

---

### 13. Segment Tree

**What to Implement:**
```
Operations:
├── build(arr)               - Build tree from array
├── query(l, r)              - Range query (sum/min/max)
├── update(index, value)     - Point update
└── rangeUpdate(l, r, val)   - Range update (lazy)
```

**Range Sum Query Implementation:**
```cpp
class SegmentTree {
private:
    vector<int> tree;
    int n;
    
    void buildTree(vector<int>& arr, int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];
        } else {
            int mid = (start + end) / 2;
            buildTree(arr, 2*node, start, mid);
            buildTree(arr, 2*node+1, mid+1, end);
            tree[node] = tree[2*node] + tree[2*node+1];
        }
    }
    
    void updateTree(int node, int start, int end, int idx, int val) {
        if (start == end) {
            tree[node] = val;
        } else {
            int mid = (start + end) / 2;
            if (idx <= mid) {
                updateTree(2*node, start, mid, idx, val);
            } else {
                updateTree(2*node+1, mid+1, end, idx, val);
            }
            tree[node] = tree[2*node] + tree[2*node+1];
        }
    }
    
    int queryTree(int node, int start, int end, int l, int r) {
        if (r < start || end < l) return 0; // Out of range
        
        if (l <= start && end <= r) return tree[node]; // Completely in range
        
        // Partial overlap
        int mid = (start + end) / 2;
        int leftSum = queryTree(2*node, start, mid, l, r);
        int rightSum = queryTree(2*node+1, mid+1, end, l, r);
        return leftSum + rightSum;
    }
    
public:
    SegmentTree(vector<int>& arr) {
        n = arr.size();
        tree.resize(4 * n);
        buildTree(arr, 1, 0, n-1);
    }
    
    // Update element at index - O(log n)
    void update(int idx, int val) {
        updateTree(1, 0, n-1, idx, val);
    }
    
    // Range sum query [l, r] - O(log n)
    int query(int l, int r) {
        return queryTree(1, 0, n-1, l, r);
    }
};
```

---

## ✅ Implementation Checklist

### Week 1: Basic DS
- [ ] Dynamic Array (Vector)
- [ ] Singly Linked List
- [ ] Doubly Linked List
- [ ] Stack (Array & LL based)
- [ ] Queue (Array & LL based)
- [ ] Circular Queue
- [ ] Deque

### Week 2: Intermediate DS
- [ ] Binary Tree
- [ ] Binary Search Tree
- [ ] Min Heap
- [ ] Max Heap
- [ ] Hash Table (Chaining)
- [ ] Hash Table (Open Addressing)

### Week 3: Advanced DS
- [ ] Graph (Adjacency List)
- [ ] Graph (Adjacency Matrix)
- [ ] Trie
- [ ] Union-Find (DSU)
- [ ] Segment Tree
- [ ] Fenwick Tree (Optional)

---

## 🧪 Testing Your Implementations

### Create Test Cases for Each DS

For **every** data structure, write test cases that check:

1. **Basic Operations**
   - Insert/Add elements
   - Delete/Remove elements
   - Search/Find elements
   - Update elements

2. **Edge Cases**
   - Empty structure
   - Single element
   - Full capacity (for fixed-size DS)
   - Duplicate elements
   - Invalid operations (e.g., pop from empty stack)

3. **Complex Scenarios**
   - Large datasets
   - Random operations
   - Stress testing

### Example Test Template

```cpp
void testStack() {
    Stack s;
    
    // Test 1: Basic push/pop
    s.push(10);
    s.push(20);
    assert(s.top() == 20);
    s.pop();
    assert(s.top() == 10);
    
    // Test 2: Empty check
    s.pop();
    assert(s.isEmpty() == true);
    
    // Test 3: Exception handling
    try {
        s.pop(); // Should throw
        assert(false);
    } catch (std::underflow_error& e) {
        assert(true);
    }
    
    cout << "All stack tests passed!" << endl;
}
```

---

## 📊 Complexity Reference Table

| Data Structure | Insert | Delete | Search | Access |
|---------------|--------|--------|--------|--------|
| Array | O(n) | O(n) | O(n) | O(1) |
| Linked List | O(1)* | O(1)* | O(n) | O(n) |
| Stack | O(1) | O(1) | O(n) | O(n) |
| Queue | O(1) | O(1) | O(n) | O(n) |
| Binary Tree | O(n) | O(n) | O(n) | O(n) |
| BST | O(log n)** | O(log n)** | O(log n)** | O(log n)** |
| Min/Max Heap | O(log n) | O(log n) | O(n) | O(1)*** |
| Hash Table | O(1)**** | O(1)**** | O(1)**** | O(1)**** |
| Trie | O(L) | O(L) | O(L) | - |
| Union-Find | O(α(n)) | - | - | O(α(n)) |
| Segment Tree | O(n) | - | O(log n) | O(log n) |

*At head/tail  
**Average case, O(n) worst case  
***Get min/max only  
****Average case, O(n) worst case

---

## 💡 Tips for Implementation Practice

1. **Code from scratch** - Don't copy-paste
2. **Use pen and paper first** - Draw before coding
3. **Write comments** - Explain your logic
4. **Test thoroughly** - Multiple test cases
5. **Compare with STL** - See how C++ does it
6. **Time yourself** - Aim to implement each DS in 30-45 mins
7. **Redo weekly** - Repetition builds mastery
8. **Explain to others** - Teaching reinforces learning

---

## 🎯 Success Criteria

You're ready to start your DSA journey when you can:

✅ Implement any basic DS in 15-20 minutes  
✅ Implement any intermediate DS in 30-45 minutes  
✅ Explain time/space complexity of all operations  
✅ Identify which DS to use for a given problem  
✅ Debug your implementations independently  
✅ Write clean, readable code with proper memory management  

---

## 📚 Additional Resources

### For Visual Learning
- **VisuAlgo** - Visualize data structures
- **CS50 Shorts** - Harvard's DS videos
- **Abdul Bari** - Excellent YouTube explanations

### For Practice
- Implement each DS in a separate file
- Create a personal library of implementations
- Keep a notes file with key insights

### Next Steps
Once you've mastered these implementations, you're ready to:
1. Start Phase 0 of the main DSA roadmap
2. Begin solving problems on LeetCode
3. Participate in contests

---

**Remember: The goal isn't to memorize code, but to understand the mechanics so deeply that you can recreate any DS from first principles.**

Good luck with your implementations! 🚀
