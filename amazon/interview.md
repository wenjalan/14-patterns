## LRU Cache
```java
class LRUCache {
    
    // a node in the cache, double linked list
    class Node {
        Node prev;
        Node next;
        int key;
        int val;
        Node(int key, int val) {
            this.key = key;
            this.val = val;
        }
    }
    
    // a table to store [key, Node]
    private HashMap<Integer, Node> cache = new HashMap<>();
    
    // first and last node sentinels
    private Node head;
    private Node tail;
    
    // the capacity of the cache
    private int capacity;
    
    // the size of the cache
    private int size;

    // const
    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.size = 0;
        head = new Node(-1, -1);
        tail = new Node(-2, -1);
        head.next = tail;
        tail.prev = head;
    }
    
    // returns the value given a key
    // returns -1 if no such key exists
    public int get(int key) {
        // System.out.println("get: [" + key + "]");
        // return -1 if key doesn't exist
        if (!cache.containsKey(key)) {
            return -1;
        }
        
        // otherwise move value to front and return value
        Node n = cache.get(key);
        // System.out.println("\t" + n.val);
        moveToFront(n);
        return n.val;
    }
    
    // puts a new value in the cache
    public void put(int key, int value) {
        System.out.println("put: [" + key + ", " + value + "]");
        // if it exists already, update value and move
        if (cache.containsKey(key)) {
            // get the node, update its value, and move to front
            Node n = cache.get(key);
            n.val = value;
            moveToFront(n);
        }
        // if it doesn't exist, create new node and move to front
        else {
            Node n = new Node(key, value);
            n.next = head.next;
            n.prev = head;
            head.next = n;
            n.next.prev = n;
            cache.put(key, n);
            size++;
            
            // ensure capacity
            ensureCapacity();
        }
        // print();
    }
    
    // moves a node to the front of the list
    private void moveToFront(Node n) {
        // remove node
        Node prev = n.prev;
        Node next = n.next;
        prev.next = next;
        next.prev = prev;
        
        // move to front
        Node t = head.next;
        t.prev = n;
        head.next = n;
        n.prev = head;
        n.next = t;
    }
    
    // ensures the LRU contains at most capacity elements
    private void ensureCapacity() {
        // remove elements from back of list while there are too many
        while (size > capacity) {
            // the node to remove
            Node n = tail.prev;
            if (cache.remove(n.key) == null) throw new IllegalStateException("No such key " + n.key + " in cache");
            n.prev.next = tail;
            tail.prev = n.prev;
            size--;
        }
    }
    
    private void print() {
        Node n = head.next;
        while (n != tail) {
            System.out.print(n.val + "-");
            n = n.next;
        }
        System.out.println();
        System.out.println("cache.size(): " + cache.size());
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
 ```