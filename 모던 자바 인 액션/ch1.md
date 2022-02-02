## 자바 8, 9, 10, 11 무슨 일이 일어나고 있는가?

#### 메서드와 람다를 일급 시민으로
기존 파일 필터링 방식
````java
File[] hiddenFiles = new File(".").listFiles(new FileFilter() {
  public boolean accept(File file) {
    return file.isHidden();
  }
});
````

FileFilter 객체로 isHidden 메서드를 감싼 다음에 File.listFiles 메서드로 전달해야 한다.

자바 8 스타일
````java
File[] hiddenFiles = new File(".").listFiles(File::isHidden);
````

문법을 이용해서 직접 isHidden 함수를 listFiles 메서드로 전달할 수 있다.

#### 코드 넘겨주기
Apple 클래스와 getColor 메서드, Apple 리스트를 포함하는 변수 inventory가 있다.
모든 녹색 사과를  선택해서 리스트를 반환 하는 프로그램을 구현한다.
````java
public static List<Apple> filterFreenApples(List<Apple> inventory) {
  List<Apple> result = new ArrayList<>();
  
  for (Apple apple : inventory) {
    if (GREEN.equals(apple.getColor())) {
      result.add(apple);
    }
  }
  return result;
}
````
누군가 사과 무게를 150그램 이상 필터링을 하고 싶다고 한다.
````java
public static List<Apple> filterFreenApples(List<Apple> inventory) {
  List<Apple> result = new ArrayList<>();
  
  for (Apple apple : inventory) {
    if (apple.getWeight() > 150) {
      result.add(apple);
    }
  }
  return result;
}
````
위 두개의 소스는 단 한 줄만 다르다. 중복으로 구성된 코드가 많다. 자바 8에 맞게 구현해보자
````java
java.util.function
public interface Predicate<T> {
  boolean test(T t);
}

public static boolean isGreenApple(Apple apple) {
  return GREEN.equals(apple.getColor());
}

public static boolean isHeavyApple(Apple apple) {
  return apple.getWeight() > 150
}

static List<Apple> filterApples(List<Apple> inventory, Predicate<Apple> p) {
  List<Apple> result = new ArrayList<>();
  for (Apple apple : inventory) {
    if (p.test(apple)) {
      result.add(apple);
    }
  }
  return result;
}

filterApples(inventory, Apple::isGreenApple);
filterApples(inventory, Apple::isHeavyApple);
````

#### 메서드 전달에서 람다로
메서드를 값으로 전달하는 것은 유용하지만 한두 번 사용하는 메서드를 정의하는 것은 귀찮은 일이다.
람다로 구현하자

````java
filterApples(inventory, (Apple a) -> GREEN.equals(a.getColor()) );
filterApples(inventory, (Apple a) -> a.getWeight() > 150 );
filterApples(inventory, (Apple a) -> a.getWeight() < 80 || RED.equals(a.getColor()) );
````
#### 스트림
````java
Map<Currency, List<Transaction>> transactionByCurrencies = new HashMap<>();
for (Transaction transaction : transactions) {
  if (transaction.getPrice() > 1000) {
    Currency currency = transaction.getCurrency();
    List<Transaction> transactionsForCurrency = transactionByCurrencies.get(currency);
    if (transactionsForCurrency == null) {
      transactionsForCurrency = new ArrayList<>();
      transactionByCurrencies.put(currency, transactionsForCurrency);
    }
    
    transactionsForCurrency.add(transaction);
  }
}
````
위 코드는 중첩된 제어 흐름 문장이 많아 한 번에 이해하기 어렵다.

````java
import static java.util.stream.Collectors.groupingBy;
Map<Currency, List<Transaction>> transactionByCurrencies = 
    transactions.stream()
              .filter((Transaction t) -> t.getPrice() > 1000)
              .collect(groupingBy(Transaction::getCurrency));
````
