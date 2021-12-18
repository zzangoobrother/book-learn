## LinkedList
구현한 LinkedList는 실제 java에서 제공하는 LinkedList 처럼 prev와 next가 아닌
next만 구현 하였습니다.

````java
import java.util.Collection;
import java.util.Iterator;
import java.util.List;
import java.util.ListIterator;

public class MyLinkedList<E> implements List<E> {

  private class Node {
    public E data;
    public Node next;

    public Node(E data) {
      this.data = data;
      this.next = null;
    }

    public Node(E data, Node next) {
      this.data = data;
      this.next = next;
    }

    @Override
    public String toString() {
      return "Node{" +
          "data=" + data +
          ", next=" + next +
          '}';
    }
  }

  private int size;
  private Node head;

  public MyLinkedList() {
    head = null;
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
  public Iterator<E> iterator() {
    return null;
  }

  @Override
  public Object[] toArray() {
    Object[] array = new Object[size];
    int i = 0;
    for (Node node = head; node != null; node = node.next) {
      array[i] = node.data;
      i++;
    }
    return array;
  }

  @Override
  public <T> T[] toArray(T[] a) {
    return null;
  }

  @Override
  public boolean add(E e) {
    if (head == null) {
      head = new Node(e);
    } else {
      Node node = head;
      for ( ; node.next != null; node = node.next) {

      }
      node.next = new Node(e);
    }
    size++;
    return true;
  }

  @Override
  public void add(int index, E element) {
    if (index == 0) {
      head = new Node(element, head);
    } else {
      Node node = getNode(index);
      node.next = new Node(element, node.next);
    }
    size++;
  }

  private Node getNode(int index) {
    if (index < 0 || index >= size) {
      throw new IndexOutOfBoundsException();
    }

    Node node = head;
    for (int i = 0; i < index; i++) {
      node = node.next;
    }

    return node;
  }

  @Override
  public boolean remove(Object o) {
    int index = indexOf(o);
    if (index == -1) {
      return false;
    }
    remove(index);
    return true;
  }

  @Override
  public E remove(int index) {
    E element = get(index);
    if (index == 0) {
      head = head.next;
    } else {
      Node node = getNode(index);
      node.next = node.next.next;
    }
    size--;
    return element;
  }

  @Override
  public boolean containsAll(Collection<?> c) {
    for (Object obj : c) {
      if (!contains(obj)) {
        return false;
      }
    }
    return true;
  }

  @Override
  public boolean addAll(Collection<? extends E> c) {
    boolean flag = false;
    for (E element : c) {
      flag |= add(element);
    }
    return flag;
  }

  @Override
  public boolean addAll(int index, Collection<? extends E> c) {
    return false;
  }

  @Override
  public boolean removeAll(Collection<?> c) {
    boolean flag = false;
    for (Object obj : c) {
      flag |= remove(obj);
    }
    return flag;
  }

  @Override
  public boolean retainAll(Collection<?> c) {
    return false;
  }

  @Override
  public void clear() {
    head = null;
    size = 0;
  }

  @Override
  public E get(int index) {
    Node node = getNode(index);
    return node.data;
  }

  @Override
  public E set(int index, E element) {
    Node node = getNode(index);
    E old = node.data;
    node.data = element;
    return old;
  }

  @Override
  public int indexOf(Object o) {
    Node node = head;
    for (int i = 0; i < size; i++) {
      if (equals(o, node.data)) {
        return i;
      }
      node = node.next;
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
    Node node = head;
    int index = -1;
    for (int i = 0; i < size; i++) {
      if (equals(o, node.data)) {
        index = i;
      }
      node = node.next;
    }
    return index;
  }

  @Override
  public ListIterator<E> listIterator() {
    return null;
  }

  @Override
  public ListIterator<E> listIterator(int index) {
    return null;
  }

  @Override
  public List<E> subList(int fromIndex, int toIndex) {
    if (fromIndex < 0 || toIndex >= size || fromIndex > toIndex) {
      throw new IndexOutOfBoundsException();
    }
    int i = 0;
    MyLinkedList<E> list = new MyLinkedList<>();
    for (Node node = head; node != null; node = node.next) {
      if (i >= fromIndex && i <= toIndex) {
        list.add(node.data);
      }
      i++;
    }
    return list;
  }
}
````

#### 소스설명
- add(E e)
add 메서드는 for 문으로 리스트에 있는 노드를 순회합니다. 순회하여 마지막 노드에 추가합니다.

- indexOf(Object o)
보통 다음 노드가 null 인지 아닌지 확인을 해야 하지만, 리스트 끝까지 가면 반복문이 종료되어 안전합니다. 목적 값을 못 찾으면 -1을 반환합니다.


#### 가비지 컬렉션
ArrayList 클래스에서는 필요하면 배열이 늘어나지만 결코 줄어들지 않았습니다. 배열은 가비지 컬렉션을 하지 않고 리스트 자체가 파괴될 때까지 가비지 컬렉션 대상이 아닙니다.
하지만 연결리스트는 요소를 제거하면 리스트 크기가 줄어들고 사용하지 않는 노드는 즉시 가비지 컬렉션이 될 수 있습니다.
clear() 메서드를 보면 head 변수를 null로 만들면 첫 번째 노드에 대한 참조를 제거합니다. 해당 노드에 대한 참조가 없다면 가비지 컬렉션을 합니다.
두번째 노드에 대한 참조가 제거되고 이것도 가비지 컬렉션이 됩니다. 이 절차는 모든 노드가 가비지 컬렉션할 때까지 계속 됩니다.
