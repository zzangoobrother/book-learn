## TreeMap 정리
````java
import java.util.Collection;
import java.util.Deque;
import java.util.HashSet;
import java.util.LinkedHashSet;
import java.util.LinkedList;
import java.util.Map;
import java.util.Set;

public class MyTreeMap<K, V> implements Map<K, V> {

  private int size = 0;
  private Node root = null;

  protected class Node {
    public K key;
    public V value;
    public Node left = null;
    public Node right = null;

    public Node(K key, V value) {
      this.key = key;
      this.value = value;
    }
  }

  @Override
  public int size() {
    return size;
  }

  @Override
  public boolean isEmpty() {
    return size == 0;
  }

  @Override
  public boolean containsKey(Object key) {
    return findNode(key) != null;
  }

  @Override
  public boolean containsValue(Object value) {
    return containsValueHelper(root, value);
  }

  private boolean containsValueHelper(Node node, Object target) {
    if (node == null) {
      return false;
    }

    if (equals(target, node.value)) {
      return true;
    }

    if (containsValueHelper(node.left, target)) {
      return true;
    }

    if (containsValueHelper(node.right, target)) {
      return true;
    }

    return false;
  }

  private boolean equals(Object target, Object obj) {
    if (target == null) {
      return obj == null;
    }
    return target.equals(obj);
  }

  @Override
  public V get(Object key) {
    Node node = findNode(key);
    if (node == null) {
      return null;
    }
    return node.value;
  }

  private Node findNode(Object target) {
    if (target == null) {
      throw new IllegalArgumentException();
    }

    @SuppressWarnings("unchecked")
    Comparable<? super K> k = (Comparable<? super K>) target;

    Node node = root;
    while (node != null) {
      int cmp = k.compareTo(node.key);
      if (cmp < 0) {
        node = node.left;
      } else if (cmp > 0) {
        node = node.right;
      } else {
        return node;
      }
    }

    return null;
  }

  @Override
  public V put(K key, V value) {
    if (key == null) {
      throw new IllegalArgumentException();
    }

    if (root == null) {
      root = new Node(key, value);
      size++;
      return null;
    }
    return putHelper(root, key, value);
  }

  private V putHelper(Node node, K key, V value) {
    Comparable<? super K> k = (Comparable<? super K>) key;
    int cmp = k.compareTo(node.key);

    if (cmp < 0) {
      if (node.left == null) {
        node.left = new Node(key, value);
        size++;
        return null;
      } else {
        return putHelper(node.left, key, value);
      }
    }

    if (cmp >0) {
      if (node.right == null) {
        node.right = new Node(key, value);
        size++;
        return null;
      } else {
        return putHelper(node.right, key, value);
      }
    }

    V oldValue = node.value;
    node.value = value;
    return oldValue;
  }

  @Override
  public V remove(Object key) {
    return null;
  }

  @Override
  public void putAll(Map<? extends K, ? extends V> m) {
    for (Map.Entry<? extends K, ? extends V> entry : m.entrySet()) {
      put(entry.getKey(), entry.getValue());
    }
  }

  @Override
  public void clear() {
    size = 0;
    root = null;
  }

  @Override
  public Set<K> keySet() {
    Set<K> set = new LinkedHashSet<>();
    addInOrder(root, set);
    return set;
  }

  private void addInOrder(Node node, Set<K> set) {
    if (node == null) {
      return;
    }
    addInOrder(node.left, set);
    set.add(node.key);
    addInOrder(node.right, set);
  }

  @Override
  public Collection<V> values() {
    Set<V> set = new HashSet<>();
    Deque<Node> stack = new LinkedList<>();
    stack.push(root);
    while (!stack.isEmpty()) {
      Node node = stack.pop();
      if (node == null) {
        continue;
      }

      set.add(node.value);
      stack.push(node.left);
      stack.push(node.right);
    }
    return set;
  }

  @Override
  public Set<Entry<K, V>> entrySet() {
    return null;
  }
}
````

- get메소드와 containsKey 메소느는 findNode 메소드를 이용한다. node의 key 크기를 비교하여 left인지 right인지 구분하여 node를 저장한다.
- putHelper메소드는 key의 크기를 비교하여 채울 공간으로 이동한다. key가 이미 트리에 있다면 기존 값을 새로운 값으로 대체한다.
- keySet메소드는 오름차순으로 left의 값부터 set에 넣고 right를 다음에 넣는다.
