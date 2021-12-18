## List 비교
<table>
  <th>구분</th>
  <th>ArrayList</th>
  <th>MyLinkedList</th>
  <th>LinkedList</th>
  <tr>
    <td>add(끝)</td>
    <td>1</td>
    <td>n</td>
    <td>1</td>
  </tr>
  
  <tr>
    <td>add(시작)</td>
    <td>n</td>
    <td>1</td>
    <td>1</td>
  </tr>
  
  <tr>
    <td>add(일반적으로)</td>
    <td>n</td>
    <td>n</td>
    <td>n</td>
  </tr>
  
  <tr>
    <td>get/set</td>
    <td>1</td>
    <td>n</td>
    <td>n</td>
  </tr>
  
  <tr>
    <td>indexOf/lastIndexOf</td>
    <td>n</td>
    <td>n</td>
    <td>n</td>
  </tr>
  
  <tr>
    <td>isEmpty/size</td>
    <td>1</td>
    <td>1</td>
    <td>1</td>
  </tr>
  
  <tr>
    <td>remove(끝)</td>
    <td>1</td>
    <td>n</td>
    <td>1</td>
  </tr>
  
  <tr>
    <td>remove(시작)</td>
    <td>n</td>
    <td>1</td>
    <td>1</td>
  </tr>
  
  <tr>
    <td>remove(일반적으로)</td>
    <td>n</td>
    <td>n</td>
    <td>n</td>
  </tr>
</table>

이중 연결 리스트는 ArrayList 보다 시작 요소에 추가하고 삭제하는 연산이 좋습니다. 끝에 요소를 추가하고 제거하는 연산은 ArrayList와 동일합니다.
ArrayList의 이점은 get/set 입니다.
응용 프로그램이 get/set에 의존한다면 ArrayList를 선택하고, 실행시간이 시작이나 끝 근처 요소를 추가하거나 제거하는 연산에 의존한다면
LinkedList가 좋습니다.
