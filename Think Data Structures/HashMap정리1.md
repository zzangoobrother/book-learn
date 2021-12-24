## HashMap 정리1
````java
import java.util.ArrayList;
import java.util.Collection;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;

public class MyLinearMap<K, V> implements Map<K, V> {

  private List<Entry> entries = new ArrayList<>();

  public class Entry implements Map.Entry<K, V> {
    private K key;
    private V value;

    public Entry(K key, V value) {
      this.key = key;
      this.value = value;
    }

    @Override
    public K getKey() {
      return key;
    }

    @Override
    public V getValue() {
      return value;
    }

    @Override
    public V setValue(V value) {
      this.value = value;
      return value;
    }
  }

  @Override
  public int size() {
    return entries.size();
  }

  @Override
  public boolean isEmpty() {
    return entries.isEmpty();
  }

  @Override
  public boolean containsKey(Object key) {
    return findEntry(key) != null;
  }

  @Override
  public boolean containsValue(Object value) {
    for (Entry entry : entries) {
      if (equals(value, entry.getValue())) {
        return true;
      }
    }
    return false;
  }

  @Override
  public V get(Object key) {
    Entry entry = findEntry(key);
    if (entry == null) {
      return null;
    }
    return entry.getValue();
  }

  @Override
  public V put(K key, V value) {
    Entry entry = findEntry(key);
    if (entry == null) {
      entries.add(new Entry(key, value));
      return null;
    } else {
      V oldValue = entry.getValue();
      entry.setValue(value);
      return oldValue;
    }
  }

  @Override
  public V remove(Object key) {
    Entry entry = findEntry(key);
    if (entry == null) {
      return null;
    } else {
      V value = entry.getValue();
      entries.remove(entry);
      return value;
    }
  }

  private Entry findEntry(Object target) {
    for (Entry entry : entries) {
      if (equals(target, entry.getKey())) {
        return entry;
      }
    }
    return null;
  }

  private boolean equals(Object target, Object obj) {
    if (target == null) {
      return obj == null;
    }
    return target.equals(obj);
  }

  @Override
  public void putAll(Map<? extends K, ? extends V> m) {
    for (Map.Entry<? extends K, ? extends V> entry : m.entrySet()) {
      put(entry.getKey(), entry.getValue());
    }
  }

  @Override
  public void clear() {
    entries.clear();
  }

  @Override
  public Set<K> keySet() {
    Set<K> set = new HashSet<>();
    for (Entry entry : entries) {
      set.add(entry.getKey());
    }
    return set;
  }

  @Override
  public Collection<V> values() {
    Set<V> set = new HashSet<>();
    for (Entry entry : entries) {
      set.add(entry.getValue());
    }
    return set;
  }

  @Override
  public Set<Map.Entry<K, V>> entrySet() {
    return null;
  }
}
````
지금 구현된 클래스는 Entry 객체들을 담은 ArrayList entries를 이용합니다.
여기 구현된 메서드들의 핵심은 모두 실행 시간이 선현이라는 것입니다.
entry의 개수가 작다면 쓸 만하지만 개선의 여지가 있습니다.
- 커다란 List 저장하는 대신 다수의 작은 리스트로 쪼갭니다. 각 키에 대해 해시 코드를 사용하여 어느 리스트를 사용할지 선택합니다.
- 작은 리스트를 사용하는 것이 더 빠르지만, 증가 차수를 개선하지는 못합니다. 여전히 선형 시간입니다. 리스트의 개수를 늘려서 
  리스트당 entry 개수를 제한할 수 있다면 결과는 상수 시간 맵이 됩니다.
위 해결 방법을 하나씩 적용해 나가겠습니다.
