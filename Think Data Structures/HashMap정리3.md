## HashMap정리3
````java
import java.util.List;
import java.util.Map;

public class MyHashMap<K, V> extends MyBetterMap<K, V> implements Map<K, V> {

  private static final double FACTOR = 1.0;
  private int size = 0;

  public MyHashMap(int k) {
    super(k);
  }

  @Override
  public void clear() {
    super.clear();
    size = 0;
  }

  @Override
  public V put(K key, V value) {
    MyLinearMap<K, V> map = chooseMap(key);
    size -= map.size();
    V oldValue = map.put(key, value);
    size += map.size();

    if (size() > maps.size() * FACTOR) {
      size = 0;
      rehash();
    }
    return oldValue;
  }

  @Override
  public V remove(Object key) {
    MyLinearMap<K, V> map = chooseMap(key);
    size -= map.size();
    V oldValue = map.remove(key);
    size += map.size();
    return oldValue;
  }

  @Override
  public int size() {
    return size;
  }

  private void rehash() {
    List<MyLinearMap<K, V>> oldMaps = maps;

    int newK = maps.size() * 2;
    makeMaps(newK);

    for (MyLinearMap<K, V> map : oldMaps) {
      for (Map.Entry<K, V> entry : map.getEntries()) {
        put(entry.getKey(), entry.getValue());
      }
    }
  }
}
````

기존 HashMap정리2에 작성된 코드는 선형 실행 시간을 가진다. HashMap정리3에서는 상수 시간을 가지게 수정한다.
엔트리의 전체 개수 n, maps.size 메서드를 호출하면 내장된 맵의 개수 k 이다. 
n/k > 1의 의미는 하위 맵당 엔트리의 개수가 임계치를 초과함을 뜻하며, 이때 rehash메서드를 호출하여 엔트리의 전체 개수가 두배가 된다.
size 인스턴스 변수를 추가한 이유는 기존 코드에서 size를 구하기 위해 maps의 하위 맵의 갯수를 for문을 통해 구하기 때문에 사용하였습니다.
