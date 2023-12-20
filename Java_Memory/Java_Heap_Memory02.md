# Java Memory Leak 분석하기-2

https://blog.yevgnenll.me/posts/heap-dump-out-of-memory      

🌟 **OOM 발생 시 자동으로 Heap Dump**     
JVM 실행 시 이 옵션을 추가해주자     
```java 
-XX:+HeapDumpOnOutOfMemoryError
```

<br/>

- JMX + VisualVM : 둘 다 JVM 모니터링용 툴 느낌인데, 툴 다운로드 및 `IP:port` 기반으로 수집한 정보들을 분석하는거라 상용 적용 불가능해보임     
- MAT(Memory Analyzer Tool)     
- Heap Dump     
- Spring Boot Actuator + Prometheus&Grafana
    - `/actuator/caches` : `CacheManager`에서 관리되는 캐싱 객체들을 출력. `CacheManager` 에 대해서 조사가 더 필요할 듯 함
    `actuator` 를 통해서 캐시 삭제도 하고 하는 것 같음
    - `/actuator/health` : 헬스 체크용
    - `/actuator/heapdump`
    - `/actuator/threaddump`

### Heap Dump 뜨기
Heap Dump 뜨는 방법으로 `jmap` 과 `jcmd` 가 있음    
- `jmap` : GC를 동반하지 않고, 죽은 객체까지 포함하여 인스턴스의 점유율을 보여준다.     
  `jcmd` 와 같이 full GC를 발생시키고싶으면 `-live` 옵션을 붙이면 된다     
- `jcmd` : Full GC를 발생시킨 후 살아있는 인스턴스의 점유율만 보여줌

❗ **※ 주의!!**
힙 히스토그램을 생성하려면 어플리케이션에 부하가 발생한다.     
어플리케이션에서 통계정보를 받아야하기 때문이다.     
실제로 동작하는 프로덕션에서는 절대로 사용하지 말 것     


라고 하는데,,, 어떻게 상용에서 힙 히스토그램을 뜨면 좋을지 고민중

# Heap Dump 분석

## jhat
참고링크 : [Case Study - Java 메모리 누수(Memory Leak)](https://m.blog.naver.com/pcmola/222064258040)     
```bash
# Heap Dump
$ lsof -i:8080    # pid 찾기
$ jps # 이 명령어로도 pid 찾을 수 있음(ApiServerApplication)

# -dump:live : Full GC 후 dump
# format=b : binary 형식으로 파일 추출
$ jmap -dump:live,format=b,file=heapdump.hprof <PID>
```

이렇게 덤프를 뜨면 프로젝트의 루트 디렉터리에  `.hprof` 확장자의 파일이 생성되는데, 이 파일을 분석해야 함     
InteliJ를 통해서 바로 열어보는 방법도 있고, `jhat` 명령어를 통해서도 분석이 가능한 것 같다.     

```bash
jhat -J-Xmx2048m -port 7001 ./heapdump.hprof
```

이 주소로 접속해보자 : http://localhost:7001     

![image](https://github.com/rkarud1234/TIL/assets/84266499/ccf98c92-79ab-4df6-9851-e93d0e1204fd)     

이런식으로 메모리 사용중인 모든 클래스들이 나온다.     
- Other Queries > Show heap histogram 클릭
![image](https://github.com/rkarud1234/TIL/assets/84266499/c85e6c81-f757-45b3-b6d9-0148c8996d64)     

처음 5-10개의 오브젝트들이 메모리 유출을 하고 있을 가능성이 높다.     

근데 `Class [C` 는 대체 무슨 클래스인가 ? → `Character` 배열이라고 한다.     

- `Class [C` : `Character` 배열
- Boolean(Z), Byte(B), double(D), float(F), int(I), long(J), short(S)

## MAT(Memory Analyzer Tool)

메모리 분석 전용 툴     
추출한 덤프파일을 열어서 분석하는거라 상용에서 뜬 덤프로 분석 가능할 것 같음     

- 다운로드 링크 : https://eclipse.dev/mat/downloads.php
- 툴 사용법 참고 링크 : https://extsdd.tistory.com/258

Heap Dump파일을 열어보면 다음과 같다     

![image](https://github.com/rkarud1234/TIL/assets/84266499/a5a93601-a860-4dd7-80b7-6b41c013eb94)     


임시로 API 몇 개만 쏜 다음에 테스트한거라서, 원인 분석이 어려움     
상용 Heap histogram을 뜨기 전까지 다양한 시나리오의 Stress Test가 필요해보임     
(비즈니스 시나리오는 업무적인 내용이라 비밀)     
