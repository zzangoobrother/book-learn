## ArrayList
자바 배열을 사용하여 요소를 저장하는 List 인터페이스를 구현한 클래스

MyArrayList.java
````java
import java.util.Arrays;
import java.util.Collection;
import java.util.Iterator;
import java.util.List;
import java.util.ListIterator;

public class MyArrayList<T> implements List<T> {

  int size;
  private T[] array;

  public MyArrayList() {
    array = (T[]) new Object[10];
    size = 0;
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
  public boolean contains(Object o) {
    return indexOf(o) != -1;
  }

  @Override
  public Iterator<T> iterator() {
    T[] copy = Arrays.copyOf(array, size);
    return Arrays.asList(copy).iterator();
  }

  @Override
  public Object[] toArray() {
    return new Object[0];
  }

  @Override
  public <T1> T1[] toArray(T1[] a) {
    return null;
  }

  @Override
  public boolean add(T element) {
    if (size >= array.length) {
      T[] bigger = (T[]) new Object[array.length * 2];
      System.arraycopy(array, 0, bigger, 0, array.length);
      array = bigger;
    }

    array[size] = element;
    size++;

    return true;
  }

  @Override
  public void add(int index, T element) {
    if (index < 0 || index > size) {
      throw new IndexOutOfBoundsException();
    }

    add(element);

    for (int i = size - 1; i > index; i--) {
      array[i] = array[i-1];
    }

    array[index] = element;
  }

  @Override
  public boolean addAll(Collection<? extends T> c) {
    boolean flag = false;
    for (T element : c) {
      flag |= add(element);
    }
    return flag;
  }

  @Override
  public boolean addAll(int index, Collection<? extends T> c) {
    return false;
  }

  @Override
  public boolean remove(Object o) {
    int index = indexOf(o);
    if (index == -1) {
      return false;
    }
    return o == remove(index);
  }

  @Override
  public T remove(int index) {
    T element = get(index);
    for (int i = index; i < size-1; i++) {
      array[i] = array[i+1];
    }
    size--;

    return element;
  }

  @Override
  public boolean removeAll(Collection<?> c) {
    boolean flag = false;
    for (Object object : c) {
      flag |= remove(object);
    }
    return false;
  }

  @Override
  public boolean containsAll(Collection<?> c) {
    for (Object element : c) {
      if (!contains(element)) {
        return false;
      }
    }
    return true;
  }

  @Override
  public boolean retainAll(Collection<?> c) {
    return false;
  }

  @Override
  public void clear() {
    size = 0;
  }

  @Override
  public T get(int index) {
    if (index < 0 || index >= size) {
      throw new IndexOutOfBoundsException();
    }
    return array[index];
  }

  @Override
  public T set(int index, T element) {
    T old = get(index);
    array[index] = element;
    return old;
  }

  @Override
  public int indexOf(Object o) {
    for (int i = 0; i < size; i++) {
      if (equals(o, array[i])) {
        return i;
      }
    }

    return -1;
  }

  private boolean equals(Object target, Object element) {
    if (target == null) {
      return element == null;
    } else {
      return target.equals(element);
    }
  }

  @Override
  public int lastIndexOf(Object o) {
    for (int i = size - 1; i >= 0; i--) {
      if (equals(o, array[i])) {
        return i;
      }
    }
    return -1;
  }

  @Override
  public ListIterator<T> listIterator() {
    T[] copy = Arrays.copyOf(array, size);
    return Arrays.asList(copy).listIterator();
  }

  @Override
  public ListIterator<T> listIterator(int index) {
    T[] copy = Arrays.copyOf(array, size);
    return Arrays.asList(copy).listIterator(index);
  }

  @Override
  public List<T> subList(int fromIndex, int toIndex) {
    if (fromIndex < 0 || toIndex >= size || fromIndex > toIndex) {
      throw new IndexOutOfBoundsException();
    }
    T[] copy = Arrays.copyOfRange(array, fromIndex, toIndex);
    return Arrays.asList(copy);
  }
}
````

#### 소스 설명
- add(T element)
배열에 여분의 공간이 없으면 더 큰 배열을 만들어 요소 위에 복사합니다. 그리고 size를 늘립니다.

- get(int index)
get 메소드에서는 인덱스 범위를 체크하고 벗어나면 예외를 던진다는 것 입니다.

- set(int index, T element)
set 메소드에서 명시적으로 배열의 범위를 검사하지 않습니다. 인덱스가 유효하지 않으면 예외를 던지는 get 메소드를 호출합니다.
