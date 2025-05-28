好的，我们来聊聊 Java 中 `Map` 接口的基本用法。

`Map` 接口在 Java 集合框架中扮演着非常重要的角色。它不像 `Collection`（如 `List` 或 `Set`）那样存储单个元素的序列或集合，而是存储**键值对 (key-value pairs)**。你可以把它想象成一个字典或者一个电话簿，每个“键”（比如一个单词或一个人的名字）都唯一地对应一个“值”（比如单词的释义或人的电话号码）。

**`Map` 接口的核心特点：**

- **键的唯一性**: 在一个 `Map` 中，键 (key) 必须是唯一的。如果你尝试用一个已经存在的键去存入一个新的值，那么旧的值会被新的值覆盖。
- **值的可重复性**: 不同的键可以映射到相同的值。
- **通常无序**: 大多数 `Map` 的实现（如 `HashMap`）不保证键值对的存储或迭代顺序。但也有有序的实现（如 `TreeMap` 会根据键的自然顺序或指定的比较器排序，`LinkedHashMap` 会保持插入顺序）。
- **允许 `null`**: 某些 `Map` 实现（如 `HashMap`）允许键和值为 `null`（`HashMap` 允许一个 `null` 键和多个 `null` 值）。但像 `TreeMap` 这样的实现通常不允许 `null` 键（除非其比较器能处理 `null`）。

---

## `Map<K, V>` 接口的基本用法 (Common Methods)

这里的 `K` 代表键的类型 (Key type)，`V` 代表值的类型 (Value type)。

1. **添加或修改键值对:**
    
    - `V put(K key, V value)`: 将指定的键值对存入 `Map`。
        - 如果 `Map` 中之前不包含该键，则添加该键值对，并返回 `null`。
        - 如果 `Map` 中已存在该键，则用新的 `value` 替换旧的 `value`，并返回旧的 `value`。
    
    Java
    
    ```java
    Map<String, Integer> ageMap = new HashMap<>();
    Integer previousAge = ageMap.put("Alice", 30); // Alice -> 30, previousAge is null
    System.out.println("Alice's age: " + ageMap.get("Alice")); // 输出: Alice's age: 30
    System.out.println("Previous age for Alice: " + previousAge); // 输出: Previous age for Alice: null
    
    previousAge = ageMap.put("Alice", 31); // Alice's age updated to 31
    System.out.println("Alice's new age: " + ageMap.get("Alice")); // 输出: Alice's new age: 31
    System.out.println("Previous age for Alice (before update): " + previousAge); // 输出: Previous age for Alice (before update): 30
    ```
    
2. **获取值:**
    
    - `V get(Object key)`: 返回指定键所映射的值。如果 `Map` 不包含该键的映射关系，则返回 `null`。
    
    Java
    
    ```java
    Map<String, String> capitals = new HashMap<>();
    capitals.put("USA", "Washington D.C.");
    capitals.put("UK", "London");
    
    String usCapital = capitals.get("USA");
    System.out.println("Capital of USA: " + usCapital); // 输出: Capital of USA: Washington D.C.
    
    String franceCapital = capitals.get("France"); // France is not in the map
    System.out.println("Capital of France: " + franceCapital); // 输出: Capital of France: null
    ```
    
3. **删除键值对:**
    
    - `V remove(Object key)`: 如果存在一个键的映射关系，则将其从 `Map` 中移除，并返回与该键关联的旧值。如果 `Map` 不包含该键的映射关系，则返回 `null`。
    
    Java
    
    ```java
    Map<Integer, String> products = new HashMap<>();
    products.put(101, "Laptop");
    products.put(102, "Mouse");
    products.put(103, "Keyboard");
    
    String removedProduct = products.remove(102);
    System.out.println("Removed product: " + removedProduct); // 输出: Removed product: Mouse
    System.out.println("Map after removal: " + products); // 输出: Map after removal: {101=Laptop, 103=Keyboard} (顺序可能不同)
    
    String nonExistentProduct = products.remove(999);
    System.out.println("Removing non-existent product: " + nonExistentProduct); // 输出: Removing non-existent product: null
    ```
    
4. **检查是否包含指定的键或值:**
    
    - `boolean containsKey(Object key)`: 如果 `Map` 包含指定键的映射关系，则返回 `true`。
    - `boolean containsValue(Object value)`: 如果 `Map` 将一个或多个键映射到指定值，则返回 `true`。这个操作通常比 `containsKey` 效率低，因为它可能需要遍历整个 `Map`。
    
    Java
    
    ```java
    Map<String, Integer> scores = new HashMap<>();
    scores.put("Math", 95);
    scores.put("English", 88);
    
    System.out.println("Contains key 'Math': " + scores.containsKey("Math")); // 输出: Contains key 'Math': true
    System.out.println("Contains key 'Science': " + scores.containsKey("Science")); // 输出: Contains key 'Science': false
    
    System.out.println("Contains value 88: " + scores.containsValue(88)); // 输出: Contains value 88: true
    System.out.println("Contains value 70: " + scores.containsValue(70)); // 输出: Contains value 70: false
    ```
    
5. **获取 `Map` 的大小:**
    
    - `int size()`: 返回 `Map` 中的键值对数量。
    
    Java
    
    ```java
    Map<String, String> fruitsColor = new HashMap<>();
    fruitsColor.put("Apple", "Red");
    fruitsColor.put("Banana", "Yellow");
    System.out.println("Number of fruit entries: " + fruitsColor.size()); // 输出: Number of fruit entries: 2
    ```
    
6. **检查 `Map` 是否为空:**
    
    - `boolean isEmpty()`: 如果 `Map` 不包含任何键值对，则返回 `true`。
    
    Java
    
    ```java
    Map<Integer, String> emptyMap = new HashMap<>();
    System.out.println("Is emptyMap empty? " + emptyMap.isEmpty()); // 输出: Is emptyMap empty? true
    ```
    
7. **清空 `Map`:**
    
    - `void clear()`: 从此 `Map` 中移除所有映射关系。
    
    Java
    
    ```java
    Map<String, Integer> items = new HashMap<>();
    items.put("Pen", 10);
    items.put("Book", 5);
    items.clear();
    System.out.println("Items map size after clear: " + items.size()); // 输出: Items map size after clear: 0
    ```
    
8. **遍历 `Map`:** 由于 `Map` 本身不是 `Collection`，它没有直接的迭代器。但你可以通过以下方式遍历：
    
    - **遍历键集合 (Key Set):**
        
        - `Set<K> keySet()`: 返回此 `Map` 中包含的键的 `Set` 视图。你可以遍历这个 `Set`，然后通过 `get()` 方法获取每个键对应的值。
        
        Java
        
        ```java
        Map<String, Double> prices = new HashMap<>();
        prices.put("Milk", 2.5);
        prices.put("Bread", 1.8);
        prices.put("Eggs", 3.0);
        
        System.out.println("--- Iterating using keySet ---");
        for (String key : prices.keySet()) {
            Double value = prices.get(key);
            System.out.println("Key: " + key + ", Value: " + value);
        }
        // 输出 (顺序不保证):
        // Key: Eggs, Value: 3.0
        // Key: Bread, Value: 1.8
        // Key: Milk, Value: 2.5
        ```
        
    - **遍历值集合 (Values Collection):**
        
        - `Collection<V> values()`: 返回此 `Map` 中包含的值的 `Collection` 视图。如果你只关心值而不关心键，可以使用这个方法。
        
        Java
        
        ```java
        System.out.println("--- Iterating using values ---");
        for (Double priceValue : prices.values()) {
            System.out.println("Value: " + priceValue);
        }
        // 输出 (顺序不保证，可能与键的顺序对应，也可能不对应，取决于Map实现):
        // Value: 3.0
        // Value: 1.8
        // Value: 2.5
        ```
        
    - **遍历键值对集合 (Entry Set):** 这是最高效的遍历方式，因为它同时访问键和值，无需额外通过 `get()` 查询。
        
        - `Set<Map.Entry<K, V>> entrySet()`: 返回此 `Map` 中包含的映射关系的 `Set` 视图。每个元素都是一个 `Map.Entry` 对象，它有 `getKey()` 和 `getValue()` 方法。
        
        Java
        
        ```java
        System.out.println("--- Iterating using entrySet ---");
        for (Map.Entry<String, Double> entry : prices.entrySet()) {
            String key = entry.getKey();
            Double value = entry.getValue();
            System.out.println("Entry: Key=" + key + ", Value=" + value);
        }
        // 输出 (顺序不保证):
        // Entry: Key=Eggs, Value=3.0
        // Entry: Key=Bread, Value=1.8
        // Entry: Key=Milk, Value=2.5
        ```
        
    - **使用 `forEach` 和 Lambda (Java 8+):**
        
        Java
        
        ```java
        System.out.println("--- Iterating using forEach (Java 8+) ---");
        prices.forEach((key, value) -> {
            System.out.println("Key: " + key + ", Value: " + value);
        });
        // 输出 (顺序不保证):
        // Key: Eggs, Value: 3.0
        // Key: Bread, Value: 1.8
        // Key: Milk, Value: 2.5
        ```
        

---

**常用的 `Map` 实现类:**

- **`HashMap<K, V>`**:
    
    - 基于哈希表实现。
    - 提供 O(1) 平均时间复杂度的 `put` 和 `get` 操作。
    - **不保证顺序**。
    - 允许一个 `null` 键和多个 `null` 值。
    - 键需要正确实现 `hashCode()` 和 `equals()` 方法。
- **`LinkedHashMap<K, V>`**:
    
    - 继承自 `HashMap`。
    - 额外维护了一个双向链表来记录插入顺序（或者访问顺序）。
    - 迭代时会按照插入顺序（或最近访问顺序）返回键值对。
    - 性能略低于 `HashMap`，因为需要维护链表。
- **`TreeMap<K, V>`**:
    
    - 基于红黑树实现。
    - 键值对会根据键的自然顺序或者在创建 `TreeMap` 时提供的 `Comparator` 进行排序。
    - `get`, `put`, `remove` 操作的时间复杂度为 O(log n)。
    - 键必须实现 `Comparable` 接口或在构造时提供 `Comparator`。
    - 通常不允许 `null` 键（除非比较器能处理）。
- **`Hashtable<K, V>`**:
    
    - 一个旧的实现，与 `HashMap` 类似，但是它是**线程安全**的（方法是同步的）。
    - 不允许 `null` 键或 `null` 值。
    - 由于性能原因（同步开销较大），通常推荐使用 `ConcurrentHashMap` 来代替它进行并发操作。

选择哪种 `Map` 实现取决于你的具体需求：是否需要排序、是否关心插入顺序、是否需要线程安全以及对性能的要求。对于大多数通用场景，`HashMap` 是一个不错的默认选择。