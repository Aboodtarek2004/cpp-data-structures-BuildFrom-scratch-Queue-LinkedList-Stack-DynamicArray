# 📚 Data Structure Project (C++)

Template-based data structures implemented from scratch in C++20.  
This README gives a detailed explanation of each class in the project.

## 🧭 Class Map

- 🔗 `clsDblLinkedList<T>`: base linked-list container
- 🧩 `clsDynamicArray<T>`: base dynamic-array container
- 🚶 `clsMyQueue<T>`: queue adapter over `clsDblLinkedList<T>`
- 📦 `clsMyQueueArr<T>`: queue adapter over `clsDynamicArray<T>`
- 🗂️ `clsMyStack<T>`: stack adapter by inheriting `clsMyQueue<T>`
- 🧱 `clsMyStackArr<T>`: stack adapter by inheriting `clsMyQueueArr<T>`

---

## 🔗 `clsDblLinkedList.h` (Doubly Linked List)

### ✅ Purpose
Stores elements in nodes connected both forward (`next`) and backward (`prev`).

### 🧱 Internal Design
- `Node` contains:
  - `T value`
  - `Node* next`
  - `Node* prev`
- Class state:
  - `Node* head`
  - `int _Size`

### ⚙️ Main Operations
- `InsertAtBeginning(value)`: insert as first node. `O(1)`
- `InsertAtEnd(value)`: traverse to last node, then append. `O(n)`
- `InsertAfter(Node*, value)`: insert after a given node. `O(1)`
- `InsertAfter(index, value)`: find node by index, then insert. `O(n)`
- `DeleteFirstNode()`: remove head. `O(1)`
- `DeleteLastNode()`: traverse to node before tail, then remove. `O(n)`
- `DeleteNode(Node*&)`: remove a specific node pointer. `O(1)` once pointer is known
- `Find(value)`: linear search. `O(n)`
- `GetNode(index)`: linear index access. `O(n)`
- `GetItem(index)`: returns node value via `GetNode`. `O(n)`
- `UpdateItem(index, newValue)`: update by index. `O(n)`
- `Reverse()`: swaps `next/prev` links for all nodes. `O(n)`
- `Clear()`: repeatedly deletes first node. `O(n)`
- `Size()`, `IsEmpty()`: `O(1)`

### 📝 Behavior Notes
- Best at frequent inserts/deletes near the front or around known nodes.
- No tail pointer is stored, so back insert/delete require traversal.
- The class is generic (`template <class T>`) and works for any copyable type.

### ⚠️ Current-Code Caveats
- `InsertAfter(Node*, value)` assumes the node pointer is valid (no null check).
- `GetItem(index)` returns `NULL` for invalid index, which is unsafe for non-pointer `T`.
- In `DeleteLastNode()`, when there is exactly one node, `_Size` is not decremented.
- `PrintList()` does `delete Current;` after loop (harmless because `Current` is null, but unnecessary).

---

## 🧩 `clsDynamicArray.h` (Dynamic Array)

### ✅ Purpose
Stores elements in a contiguous memory block and manually resizes by re-allocation.

### 🧱 Internal Design
- `int _Size`: current number of elements
- `T* OriginalArray`: active storage
- `T* _TempArray`: temporary buffer during resize/insert/delete/reverse

### ⚙️ Main Operations
- `SetItem(index, value)`: assign by index. `O(1)`
- `GetItem(index)`: read by index. `O(1)`
- `Resize(newSize)`: allocates new block, copies min(old,new). `O(n)`
- `InsertAt(index, value)`: reallocates and shifts right. `O(n)`
- `InsertAtBeginning(value)`: calls `InsertAt(0, value)`. `O(n)`
- `InsertAtEnd(value)`: calls `InsertAt(_Size, value)`. `O(n)`
- `InsertBefore(index, value)`: inserts before given index. `O(n)`
- `InsertAfter(index, value)`: inserts after given index. `O(n)`
- `DeleteItemAt(index)`: reallocates and shifts left. `O(n)`
- `DeleteFirstItem()`: delete at index 0. `O(n)`
- `DeleteLastItem()`: delete at index `_Size - 1`. `O(n)`
- `DeleteItem(value)`: find then delete. `O(n)`
- `Find(value)`: linear search. `O(n)`
- `Reverse()`: copies into reversed buffer. `O(n)`
- `Clear()`: resets to size 0
- `Size()`, `IsEmpty()`: `O(1)`

### 📝 Behavior Notes
- Random access is fast (`O(1)`), but insertion/deletion is expensive (`O(n)`).
- This implementation reallocates on many operations, so it is educational but not optimized like `std::vector`.

### ⚠️ Current-Code Caveats
- `InsertAt` signature uses `T index` (should typically be `int`/`size_t`).
- `SetItem` checks `index >= _Size` but not `index < 0`.
- `GetItem` has no bounds validation.
- `InsertAfter` uses `InsertAt(_Size - 1, value)` when index is out of range (not typical "append-after-end" behavior).

---

## 🚶 `clsMyQueue.h` (Queue via Linked List)

### ✅ Purpose
Provides queue API (FIFO) by wrapping `clsDblLinkedList<T>`.

### 🧱 Internal Design
- Protected member: `clsDblLinkedList<T> _MyList`

### ⚙️ Queue Methods
- `push(item)`: enqueue using `_MyList.InsertAtEnd(item)`
- `pop()`: dequeue using `_MyList.DeleteFirstNode()`
- `front()`: first element (`GetItem(0)`)
- `back()`: last element (`GetItem(Size()-1)`)
- `Size()`, `IsEmpty()`, `Print()`

### ➕ Extra Utility Methods
- `GetItem(index)`, `UpdateItem(index, value)`
- `InsertAfter(index, value)`
- `InsertAtFront(value)`, `InsertAtBack(value)`
- `Reverse()`, `Clear()`

### ⏱️ Complexity Summary
- `push`: `O(n)` (because linked list has no tail pointer)
- `pop`: `O(1)`
- `front`: `O(1)`
- `back`: `O(n)` (index-based traversal)

---

## 📦 `clsMyQueueArr.h` (Queue via Dynamic Array)

### ✅ Purpose
Provides queue API (FIFO) by wrapping `clsDynamicArray<T>`.

### 🧱 Internal Design
- Protected member: `clsDynamicArray<T> _MyList`

### ⚙️ Queue Methods
- `push(item)`: enqueue using `_MyList.InsertAtEnd(item)`
- `pop()`: dequeue using `_MyList.DeleteFirstItem()`
- `front()`, `back()`, `Size()`, `IsEmpty()`, `Print()`

### ➕ Extra Utility Methods
- `GetItem(index)`, `UpdateItem(index, value)`
- `InsertAfter(index, value)`
- `InsertAtFront(value)`, `InsertAtBack(value)`
- `Reverse()`, `Clear()`

### ⏱️ Complexity Summary
- `push`: `O(n)` (insert-at-end reallocates in current dynamic array design)
- `pop`: `O(n)` (delete-first shifts elements)
- `front`: `O(1)`
- `back`: `O(1)`

---

## 🗂️ `clsMyStack.h` (Stack via Linked-List Queue Inheritance)

### ✅ Purpose
Implements LIFO stack behavior by inheriting from `clsMyQueue<T>`.

### 🔄 How It Works
- Overrides `push(item)` to insert at front:
  - `clsMyQueue<T>::_MyList.InsertAtBeginning(item)`
- Reuses inherited `pop()` (which removes front) to match LIFO
- Adds:
  - `Top()`: same as front (top of stack)
  - `Bottom()`: same as back

### ⏱️ Complexity Summary
- `push`: `O(1)`
- `pop`: `O(1)`
- `Top`: `O(1)`
- `Bottom`: `O(n)`

### 📝 Note
Because it inherits from queue, queue-style methods are still available (like `InsertAtBack`, `Reverse`, etc.).

---

## 🧱 `clsMyStackArr.h` (Stack via Array-Queue Inheritance)

### ✅ Purpose
Implements LIFO stack behavior by inheriting from `clsMyQueueArr<T>`.

### 🔄 How It Works
- Overrides `push(item)` to insert at front of dynamic array.
- Reuses inherited queue `pop()` (delete first) so most recent element is removed first.
- Adds:
  - `Top()`: first element
  - `Bottom()`: last element

### ⏱️ Complexity Summary
- `push`: `O(n)` (insert at beginning shifts/reallocates)
- `pop`: `O(n)` (delete first shifts/reallocates)
- `Top`: `O(1)`
- `Bottom`: `O(1)`

---

## 🛠️ Build & Run

### Visual Studio
1. Open `Data_Structure_Project.vcxproj`
2. Choose `Debug`/`Release` and `x64`/`Win32`
3. Build (`Ctrl + Shift + B`)
4. Run (`Ctrl + F5`)

### MSBuild
```powershell
msbuild Data_Structure_Project.vcxproj /t:Build /p:Configuration=Debug /p:Platform=x64
```
Built as a learning-first data structures project 🧠
