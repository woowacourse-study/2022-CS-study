# JVM

> 누구든 JVM의 규격(`Specification`)을 만족하는 '무언가'를 만든다면,  
> 그 `무언가`는 `JVM`이다.  

- 사실 JVM 자체는 구현체가 아닌 하나의 표준이다 !  
- 이를 구현한 Vendor사는 오라클, IBM, azul등 이 있다.  

```
The Java Virtual Machine is the cornerstone of the Java platform. It is the component of the technology responsible for its hardware-and operating system-independence, the small size of its compiled code, and its ability to protect users from malicious programs.

Java Virtual Machine은 Java 플랫폼의 초석입니다. 하드웨어 및 운영 체제와 독립적으로 가집니다.
```

사실 저기 있는 `무언가` 는 `SW`뿐만 아니라 **`HW`** 도 될 수 있다~!!

![image](https://user-images.githubusercontent.com/66164361/192105302-66190fcb-6700-482a-adcc-b86deca96140.png)

---

![image](https://user-images.githubusercontent.com/66164361/192105334-88f7d890-bdc1-42cb-b158-88243481ca13.png)

> 의외로 `JIT`랑 `GC`가 그림에 없다 !

```
To implement the Java Virtual Machine correctly, you need only be able to read the class file format and correctly perform the operations specified therein.

Java 가상 머신을 올바르게 구현하려면, 당신은 단지 class 파일 형식을 읽고 스펙에 지정된 작업을 올바르게 수행할 수만 있으면 됩니다.

Implementation details that are not part of the Java Virtual Machine's specification would unnecessarily constrain the creativity of implementors. 

현 JVM 규격에 명시되어 있지 않ㄴ은 구현과 관련된 상세 사항들은 구현자의 창의성을 저해하는 불필요한 제약이 될 수 있습니다.

For example, the memory layout of run-time data areas, the garbage-collection algorithm used, and any internal optimization of the Java Virtual Machine instructions (for example, translating them into machine code) are left to the discretion of the implementor.

예를 들자면, 데이터 영역의 메모리 레이아웃이나 Garbage-Collection에 사용되는 알고리즘, 그리고 JVM 명령어 실행과 관련된 내부의 최적화 (e.g. 기계어로 번역하는 과정)에 관한 것들은 구현자의 재량으로 남겨두겠습니다.

```

# 참고
> https://github.com/binghe819/TIL/blob/master/JAVA/JVM/jvm_structure.md  
> [10분 테코톡] 🎹 김김의 JVM Specification  https://www.youtube.com/watch?v=6reapO0gLPs  
> [10분 테코톡] 어썸오의 JVM Memory Layout  https://www.youtube.com/watch?v=GU254H0N93Y  
> [10분 테코톡] 🎅무민의 JVM Stack & Heap  https://www.youtube.com/watch?v=UzaGOXKVhwU  
> extension://bfdogplmndidlpjfhoijckpakkdjkkil/pdf/viewer.html?file=https%3A%2F%2Fcr.openjdk.java.net%2F~iris%2Fse%2F17%2FlatestSpec%2Fjava-se-17-jvms-fr-diffs.pdf  
> 