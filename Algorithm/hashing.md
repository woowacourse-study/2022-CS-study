# 해싱 (Hashing)

## 처음에 느꼈던 의문점...

HashMap은 왜 `hashCode()`, `euqals()`  둘다 구현해야만 하는 걸까??

그것에 대한 답이 오늘 여기 있다 !

## 용어 정리

![image](https://user-images.githubusercontent.com/66164361/190897944-72422e40-42bf-41fb-b233-08a3071f0f87.png)

- `key` -> `hash value`(고정 길이의 데이터)
- 이 과정을 hashing

- `hash funciton`
- A hash function is any function that can be used to map data of arbitrary size to fixed-size values.
- 임의의 길이의 데이터를 고정 길이의 데이터로 바꾸어주는 함수

![image](https://user-images.githubusercontent.com/66164361/190897960-06c9cb4b-7971-4270-950a-63cd785814d1.png)

- 해시 연산의 결과가 저장되는 곳을 `버킷(bucket)` 또는 슬롯(slot)이라고 한다.
- 데이터 개수가 N, 버킷의 사이즈가 M이라고 할 때, N/M 을 `load factor(α)`라고 한다.
  - 한 버킷에 평균 몇 개의 키가 매핑되는가를 나타내는 지표이다.
  - 대게의 경우 α가 α > 1 이 되도록 한다.

## 나눗셈법 - 해시함수

- `주소` = `입력값 % 테이블 크기`
- 어떤 값이든 테이블의 크기로 나누면 그 나머지는 절대 테이블의 크기(n)을 넘지 않는다.
- 테이블 크기를 M이라 했을 때 0 ~ M-1의 숫자가 나온다.
- 일반적으로 테이블 내 공간을 효율적으로 사용하기 위해서는 테이블 크기 M을 소수로 정하는게 좋다는 것으로 알려져 있음  
특히 2의 제곱수와 거리가 먼 소수를 사용한 해시 함수가 좋은 성능을 낸다.  
그래서 이펙티브 자바에서 String 해시 계산할 때 31을 냈었던 것인가...? (관례로 알고 있었다...)

## 해시 만들기 - 자릿수 접기

예를들어 7자리 숫자의 경우인 `8129335`의 경우  

- 8 + 1 + 2 + 9 + 3 + 3 + 5 = 31  
- 81 + 29 + 33 + 5 = 148  

- 7자리의 경우 `한자리`씩 접기를 하면 0 ~ 63의 숫자의 해시 값이 나온다
  - min: 0 + ... + 0  
  - max: 9 + ... + 9  

- `두자리`씩 접기를 하면 0 ~  306  
  - min: 00 + ... + 0  
  - max: 99 + ... + 9  

## 해시 충돌(hash collision)

- 서로 다른 키에 대해 동일한 해시 값을 가지는 것  
- 무한에 가까운 데이터(`key`)를 유한한 개수의 해시 값으로 매핑함으로써 작은 크기의 메모리로 프로세스를 관리할 수 있다.  

## 다른 자료구조와 비교

- `Binary Tree`의 경우 메모리를 효율적으로 사용하기엔 좋으나 데이터 탐색의 시간 복잡성이 `O(logN)` 이다.
- `Array`의 경우 탐색 시간이 `O(1)`이지만 메모리를 모두 할당해야 하기 때문에 공간 효율적이지 않다.

## Direct-Address Table

![image](https://user-images.githubusercontent.com/66164361/190898164-f08381cc-bb44-47ca-b6e6-4a3e1620f9f6.png)

- 키의 전체 수랑 동일한 크기의 버킷을 가진 테이블
- 전체 키가 실제 사용하는 키보다 훨씬 많은 경우 사용하지 않는 키들을 위한 공간까지 마련해야 한다  

## 해시충돌의 문제 해결

- Chaining  
- Open Addressing  

![image](https://user-images.githubusercontent.com/66164361/190908796-bb9fadb7-db79-42d7-8f13-9f900342e76c.png)


## Chaining

![image](https://user-images.githubusercontent.com/66164361/190898196-6b98fd86-5b42-434d-951b-07b9d25d1868.png)

- 버킷에 데이터가 이미 있는 경우 링크드리스트 방식처럼 노드를 추가합니다 (`Chaining`)  
- 유연하다는 장점을 가지고 있으나 메모리 문제가 있을 수 있다  

- 삽입
- `O(1)`
- 무조건 하나만 넣으면 되므로 O(1) 이다! 

- 탐색  
- `O(1+α)`  
- 실패하는 탐색(`unsuccessful search`)의 경우 (값이 있는 경우)  
    - 처음에 버킷을 찾고(`1`), 그 다음에 리스트에서 데이터를 모두 찾는다(`α`), 고로 `1 + α` 이다
- 성공하는 탐색(`successful search`)의 경우 (값이 없는 경우)
    - 최악의 경우 `1 + α`  

- 삭제
- `O(1+α)`
- 탐색과 본질적으로 같다. 우선 데이터가 있는지 탐색을 해야 하기 때문!

## Open Addressing

![image](https://user-images.githubusercontent.com/66164361/190904770-183e404f-0d6d-4568-88a7-54cc098d556f.png)

- 한 버킷당 들어갈 수 있는 엔트리(`데이터 저장 장소`)가 하나 뿐인 해시 테이블.  
- 85는 1번 자리에 들어가야 하지만 50이 이미 있기 때문에 바로 다음 버킷에 들어간다!  

- 데이터 개수가 충분히 적다면 Chaining 보다 성능이 좋지만, 데이터가 커질 수록 성능이 나빠진다
- 제곱탐사 이런 건 조사하지 않았다...! 왜냐... (우리 Chaining 기법 쓰니깐 !! 하핫)  

# in Java 

## HashTable과 HashMap  

- HashTable 은 `1.0`부터 있었던 Java API 이다
- HashMap 은 Java`2`에서 선보인 JCF에 속한 API다.
- HashMap은 HashTable과 달리 보조해시함수를 사용하고 있어서 해시 충돌이 덜 발생한다 (성능상 이점)
- HashMap은 지속적으로 개선되고 있다
- HashTable은 `1.0`, `1.1` 환경을 대상으로 구현한 Java App이 잘 동작할 수 있도록 `하위 호환성`을 제공하는 것에 가치가 있다 (`Legacy`)

## Java의 HashMap

- Open Addressing이 아닌 Chaining 방식을 사용한다 !
- 버킷에 대해 링크드 리스트가 있는 방식
- 데이터가 모두 균등하게 들어가 있다고 가정할 때 탐색에 대한 평균 기대값은 `E(N/M)` 이다. 
  - 여기서 `N`은 총 데이터의 개수, `M`의 버킷의 사이즈  

## Java8 HashMap에서의 Chaining

- Chaining 을 사용하는 것은 동일하지만  
  탐색 기대 시간이 `E(N/M)`이 아닌 `E(logN/M)`을 보장한다 !
- 이렇게 될 수 있는 이유는 데이터가 많아지면 링크드 리스트 대신 트리를 사용하기 때문 !
- 8개 부터는 트리로 변하고 6개부터는 다시 리스트로 변경된다 ! 

```java
static final int TREEIFY_THRESHOLD = 8;

static final int UNTREEIFY_THRESHOLD = 6;  
```
