## HashMap정리2
````java
import java.util.ArrayList;
import java.util.Collection;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;

public class MyBetterMap<K, V> implements Map<K, V> {

  private List<MyLinearMap<K, V>> maps;

  public MyBetterMap(int k) {
    makeMaps(k);
  }

  private void makeMaps(int k) {
    maps = new ArrayList<>(k);
    for (int i = 0; i < k; i++) {
      maps.add(new MyLinearMap<K, V>());
    }
  }

  @Override
  public int size() {
    int total = 0;
    for (MyLinearMap<K, V> map : maps) {
      total += map.size();
    }
    return total;
  }

  @Override
  public boolean isEmpty() {
    return size() == 0;
  }

  @Override
  public boolean containsKey(Object key) {
    MyLinearMap<K, V> map = chooseMap(key);
    return map.containsKey(key);
  }

  @Override
  public boolean containsValue(Object value) {
    for (MyLinearMap<K, V> map : maps) {
      if (map.containsValue(value)) {
        return true;
      }
    }
    return false;
  }

  @Override
  public V get(Object key) {
    MyLinearMap<K, V> map = chooseMap(key);
    return map.get(key);
  }

  @Override
  public V put(K key, V value) {
    MyLinearMap<K, V> map = chooseMap(key);
    return map.put(key, value);
  }

  private MyLinearMap<K, V> chooseMap(Object key) {
    int index = key != null ? Math.abs(key.hashCode()) % maps.size() : 0;
    return maps.get(index);
  }

  @Override
  public V remove(Object key) {
    MyLinearMap<K, V> map = chooseMap(key);
    return map.remove(key);
  }

  @Override
  public void putAll(Map<? extends K, ? extends V> m) {
    for (Map.Entry<? extends K, ? extends V> entry : m.entrySet()) {
      put(entry.getKey(), entry.getValue());
    }
  }

  @Override
  public void clear() {
    for (int i = 0; i < maps.size(); i++) {
      maps.get(i).clear();
    }
  }

  @Override
  public Set<K> keySet() {
    Set<K> set = new HashSet<>();
    for (MyLinearMap<K, V> map : maps) {
      set.addAll(map.keySet());
    }
    return set;
  }

  @Override
  public Collection<V> values() {
    Set<V> set = new HashSet<>();
    for (MyLinearMap<K, V> map : maps) {
      set.addAll(map.values());
    }
    return set;
  }

  @Override
  public Set<Entry<K, V>> entrySet() {
    return null;
  }
}
````

내장된 맵에 따라 키를 나누어 각 맵의 entry 개수를 줄여 속도를 빠르게 합니다.
해쉬합수를 사용하여 key 저장하고 조회 합니다.
하지만 아직 선형 실행시간을 가집니다.

그리고 많은 객체가 동일한 해시 코드를 갖는다면 결국 같은 하위 맵으로 몰립니다.
어떤 하위 맵에 다른 맵보다 많은 entry가 있으면 성능은 줄어들게 됩니다.
그래서 해시 함수의 목표 중 하나는 균등해야 한다는 것입니다.
즉, 일정 범위에 있는 어떤 값으로 고루 퍼지도록 해시 코드를 생성해야 합니다.
