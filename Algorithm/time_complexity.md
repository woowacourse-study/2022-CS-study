# 시간 복잡도

- Big O

- Big Theta

- Big Omega

위와 같은 표기 법들이 있으나, 보통 `Big O` 표기를 사용한다

정의 자체는 직관적으로 이해하는 것이 어렵지는 않지만 조금 더 구체적으로 파악하기엔 어려움이 있어, 여러가지를 찾아보았다

# 예를 들어...

배열 5개에서 5를 찾는 경우

5, 3, 4, 1, 2

이 경우 1번째에,

Omega(1): 하한선 (lower bound)

1, 2, 3, 4, 5

이 경우 5번째만에

O(5): 상한선 (upper bound)

![image](https://user-images.githubusercontent.com/66164361/189555574-69120943-9a2d-4929-8b99-2709a33ce4df.png)


# 점근적 표기법

- 문제를 해결하는 데 걸리는 시간을 점근적으로 묘사하는 것

- 중요하지 않은 항과 상수 계수를 제거하여 알고리즘의 실행 시간에 중요한 부분인 성장률에 집중할 수 있게 표기한 것
  - ex) `aN + b` 에서 `O(N)`

- 데이터와 시간과의 관계가 어떤 함수 형태에 근접해 있는지 분석

![image](https://user-images.githubusercontent.com/66164361/189535141-d944d292-0633-4139-b315-bb84de3fcaf0.png)

## 이런게 왜 있을까??

- 컴퓨터 마다 수행시간이 모두 다르다

- 같은 컴퓨터일지라도 수행시간이 다를 수 있다

## 왜 하필 Big O를 쓸까?

- 일반적으로 upper bound(상한, 최악의 경우)만 알아도 충분하기 때문에!

- 사람들이 많이 쓰니깐 !

- 키보드엔 오메가랑 쎼타가 없으니깐!

# 분류

![image](https://user-images.githubusercontent.com/66164361/189537389-6444b053-581f-46cc-9ecd-cab3777162b5.png)

- `logN`, `nLogN`의 경우 현실세계에서 이상적인 알고리즘이라고 평가 받는다 ! (`P`)

- 반면 2^n, n!의 경우, 우리가 풀 수 없는 알고리즘 ! (`NP`)
  - 소인수 분해
    - 21은 3과 7의 소수의 곱으로 이루어진다
  - 이것을 이용한 것이 `RSA` 알고리즘 (공개키-비공개키)

![image](https://user-images.githubusercontent.com/66164361/189557166-8b2eb950-8305-4ea4-ae8f-d1060dc1ba43.png)

## P-NP

다항시간 내 등 충분히 풀 수 있는 복잡도를 P,
그게 아닌 복잡도(지수함수, N! 등)을 NP라고 한다면,

NP를 P 시간 내에 풀 수 있다를 증명하면 된다

- 수학계의 밀레니엄
- 소수 등을 이용한 암호화 체계 등 무력화됨 (공인인증 등)


## 상수 시간: O(1)

- 데이터가 아무리 많아도 항상 같은 시간이 소요되는 것
- 시간 복잡도가 가장 빠르다
- ex) `java HashMap` (최악의 경우 `logN`)

## 로그 시간 : O(Log N)

- 데이터가 많아져도 탐색 시간은 그보다 훨씬 작게 걸린다
- ex) `이진 트리 탐색`

### 로그의 밑에 관해서

![image](https://user-images.githubusercontent.com/66164361/189556023-30c7b0a0-e033-4b3a-8779-8427243b6504.png)

위와 같은 이유로 인해 로그의 밑에 예민할 필요 없다!

## 선형 시간: O(N)

- 입력되는 데이터에 비례하여 시간이 증가하는 경우
- ex) java의 `Linked List`

## 선형 로그 시간: O(N Log N)

- 데이터가 많아질 수록 완만하게 늘어난다
- 보편적으로 사용되는 정렬 알고리즘
- 퀵, 병합, 힙 정렬 등

## N제곱(quadratic complexity): O(N^2)

입력되는 데이터의 제곱에 비례하여 커지는 경우

- ex) 2중 루프, 삽입, 선택, 버블 정렬

# 예시

> ex1

```java
for(N) {
}

for (M) {
}
```

`O(N+M)` or `O(max(N,M))`

> ex2

```java
for(N) {
    for (M) {
    }
}
```

`O(N*M)`

> ex3

```java
for(N) {
    for(N) {
        for(50_000)
    }
}
```

**O(N^3) 이 아니다!**  
50_000*N^2... 즉 O(N^2)

# 참고

> 중간 바보도 배우는 알고리즘
> https://www.youtube.com/watch?v=Chcl71vEkRg

> `Big-O` 계산법에 대해 일목요연하게 정리가 잘 되어 있다
> https://joycestudios.tistory.com/63

> 테코톡
> https://www.youtube.com/watch?v=IEH3YA2Nn4Q  

> 쉬운코드: 알고 설명
> https://www.youtube.com/watch?v=tTFoClBZutw

> P-NP
> https://www.youtube.com/watch?v=QkSW24mUxN8
