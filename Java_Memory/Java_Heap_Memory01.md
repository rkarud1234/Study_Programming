# Heap 메모리 확인방법
힙메모리 사용이 지속적으로 증가하고있다.   
메모리 누수가 아닌지 점검이 필요해보임  

가능한 모니터링 방법들   

- VisualVM
- Heap Dump
- Prometheus&Grafana (yml수정이 필요함)
- jmx_exporter
- Spring Boot Actuator

성능 테스트 시 full GC? minor-gc? major gc?     

## JVM과 Heap 메모리 영역

JAVA는 자바 개발 도구인 JDK를 이용해 개발된 프로그램이 JRE에 의해 가상 컴퓨터인 JVM에서 구동된다.   
![image](https://github.com/rkarud1234/TIL/assets/84266499/bab72768-79b6-4da3-904c-66a2645687cc)     
https://docs.oracle.com/javase/8/docs/technotes/guides/desc_jdk_structure.html

### JVM이란?

JVM은 하나의 가상 컴퓨터라고 생각하면 편하다.   
실제 컴퓨터처럼 명령어의 집합을 가지고 있으며, JVM이 띄워진 실제 컴퓨터의 메모리를 조작하는 기능을 수행한다.   

JVM은 Java 자체에 대해서는 알지 못하고, `.class` 파일 형식만을 알고 있다.   
`.class` 에는 JVM의 명령어나 바이트코드 등과 같은 정보들이 포함되어있다.   
작성한 `java` 언어는 컴파일러를 통해 JVM의 어셈블리어로 변환된 뒤, JVM이라는 가상 컴퓨터 머신 위에서 실행되는 것   

그래서 `kotlin` , `groovy` 등의 언어들 또한 `.class` 로 컴파일이 가능하기 때문에, JVM기반의 언어라고 할 수 있는 것이다.   

### JVM의 메모리 관리 방식

JVM은 실제 물리 머신에서 특정한 메모리 크기를 할당받는다. 그리고 그 내부에서 나름대로의 메모리 관리 전략을 사용하면서 조정을 하게 된다.   

JVM 메모리 영역 중에 JVM이 관리할 수 있는 영역이 Heap Memory 영역, JVM이 물리적 머신에서 차지하고 있는 영역이지만, 직접적으로 관리할 수 없는, 물리 머신의 OS에 의해 조절되는 메모리 영역이 Non-Heap Memory, Off-Heap Memory, Native Memory라는 영역이다.   

### Heap Space 구조

![image](https://github.com/rkarud1234/TIL/assets/84266499/39ae1886-5711-4a7f-93f7-bb4472480d47)   

- Virtual : 예약되기만 하고 실제 물리 메모리에는 할당되지 않은 크기
- young(generation) 영역
    - EDEN : 객체 최초 생성 시 들어가는 영역
    - Survivor0/1 : 두 개의 영역으로 이루어지며, 항상 하나는 비어있고 하나는 채워짐

JVM에는 기본적으로 객체들이 저장되며, 기동 시 설정한 -Xms 크기에서 시작되어 -Xmx 크기까지 커질 수 있다.    
힙 영역 사이즈는 JVM 실행 옵션으로 줄 수 있다.    

- 현재 실행 옵션 :  `-Xms2048m -Xmx2048m`    

이 힙 공간에 객체를 쌓아가다가, 객체 도달 불가능한 쓰레기 상태가 되면 Garbage Colleter에 의해 정리됨    

💡 **Garbage Collect 방식**   
가장 간단한 방법은 모든 객체에 대해서 매번 도달 가능한 상태인지 체크하는 방식 (= O(N))   
스캔 방식도 비효율적이며, 객체 수가 많아질수록 시간도 정비례해서 증가하기 때문에 대규모 어플리케이션에는 적절하지 않은 방식이다.   
JVM은 다양한 가비지 콜렉션 방식을 결합한 generational collection이라는 방식을 사용   

<br/>

**generational collection 방식이란?**    
비어있는 Survivor을 제외한 young 영역이 전부 차면, JVM이 멈추고(suspend) stop-the-world 상태로 들어간다.   
EDEN과 Survivor를 검토해 살아있는 녀석을 비어있던 Survivor로 옮긴다. 이를 minorGC라고 한다.   
minorGC를 할 때, young에서 오래 남아있던 녀석들은 old로 옮기는데, 이 작업을 반복하다가 Old 영역도 가득 차게 되면 정리해주는 작업이 필요하다. 이를 majorGC라고 하며, 관련 오브젝트가 많기 때문에 minorGC보다 오래걸린다.    

### Non-Heap Space 구조
![image](https://github.com/rkarud1234/TIL/assets/84266499/cef67f00-cbfb-4873-9ba2-c78f105fe7f1)   

참고링크 : https://juneyr.dev/jvm-basics   

## 모니터링 환경 구축

### Prometheus 기반 환경 설정 - actuator

Prometheus 사용하려면 우선 actuator와 micrometer(prometheus)를 활성화하여 metric을 수집해야 한다.   

💡 **Spring Boot Actuator란?**    
Actuator는 스프링 부트가 제공하는 기능으로 지표 수집, 추적, 감사 등의 모니터링을 쉽게 할 수 있는 다양한 편의 기능을 제공한다. 또한 최근 유행하는 마이크로미터, 프로메테우스, 그라파나 같은 다양한 모니터링 시스템과 매우 쉽게 연동할 수 있는 기능도 제공한다.   

다양한 Endpoint나 설정에 관한 설명은 공식 문서에서 확인 가능 : [Spring Boot Actuator Docs](https://docs.spring.io/spring-boot/docs/current/actuator-api/htmlsingle/)   

**활성화 방법**   

1. `build.gradle` 에 의존성 추가
    
```
// build.gradle
    
// actuator 의존성 추가
implementation 'org.springframework.boot:spring-boot-starter-actuator'
    
// prometheus metric 수집용 의존성 추가
implementation 'io.micrometer:micrometer-registry-prometheus'
```
    
2. `application.yml` 에 endpoint 설정    
    
    현재 `Spring Security FilterChain` 에 의해서 접근제어가 설정되어있으니, 기본 url로는 접근 안됨.    
    접근할 수 있도록 `/actuator` endpoint를 열어주자    
    
    외부 접근은 안되도록 url에서 `/api` 는 제거하고, 포트는 8081로 변경    
    
    이거 말고도 `management` 옵션을 통해 다양한 설정을 할 수 있음    
    
```yaml
## actuator 설정
management:
  server:
    port: 8081
  endpoints:
    web:
      base-path: "/actuator"
```    
3. `SecurityConfig` 에서 해당 url을 열어준다. (추후에 올릴때는 기존 로컬용 ACL 사용할 예정)     
    
    ```java
    // test가 끝나면 localhost용 SecurityFilterChain과 동일한 조건으로 설정한다.
    .antMatchers("/actuator/**").permitAll()
    ```
    

![image](https://github.com/rkarud1234/TIL/assets/84266499/6e4ca325-0885-45e0-a227-fdb3c1c6aaa4)     


위와 같이 접근 가능해짐     

기본적으로는 아래의 `endpoint` 들이 제공된다.    

http://localhost:8081/actuator 결과
```json
{
   "_links":{
      "self":{
         "href":"http://localhost:8081/actuator",
         "templated":false
      },
      "health-path":{
         "href":"http://localhost:8081/actuator/health/{*path}",
         "templated":true
      },
      "health":{
         "href":"http://localhost:8081/actuator/health",
         "templated":false
      }
   }
}
```

`endpoints` 를 더 열어주면 더 다양한 정보를 확인 가능

```yaml
endpoints:
    web:
      exposure:
        include: "*"
```

사용 가능한 endpoints 중에서 쓸 수도 있을법한 것들만 좀 정리를 해보았음

나머지는 지금 필요없거나 너무 많은 정보들을 담고있는 것 같음 

- `/actuator/caches` : `CacheManager`에서 관리되는 캐싱 객체들을 출력. `CacheManager` 에 대해서 조사가 더 필요할 듯 함
`actuator` 를 통해서 캐시 삭제도 할 수 있는 것 같음
- `/actuator/health` : 헬스 체크용
- `/actuator/heapdump`
- `/actuator/threaddump`

일단 `actuator` 의 `heapdump` 와 `prometheus metric` 의 차이는 추후에 보도록 하고, `actuator-prometheus metric` 을 `prometheus` 프로세스에서도 수집할 수 있도록 설정해주자

**설정 방법**

`prometheus.yml` 파일에 수집할 `metric` 의 `endpoint` 정보를 추가로 등록해준다.

```yaml
scrape_configs:
  - job_name : "spring-boot-actuator"
    static_configs:
      - targets: ["localhost:8081"]
    # 별도의 metrics_path를 지정해주지 않으면 /metrics가 deafult endpoint가 됨
    metrics_path: "/actuator/prometheus"
```

```bash
# reload 해준다.
brew services reload prometheus
```

이후 `prometheus` 실행 포트인 9090으로 접근하면 확인 가능    
![image](https://github.com/rkarud1234/TIL/assets/84266499/21eae97c-e1f9-46d6-8b99-7ba057583b3e)    

Grafana에서는 여러 가지 dashboard를 import해보았음   

- [SpringBoot APM Dashboard](https://grafana.com/grafana/dashboards/12900-springboot-apm-dashboard/)
- [JVM(Micrometer)](https://grafana.com/grafana/dashboards/4701-jvm-micrometer/)
- [JVM(Actuator)](https://grafana.com/grafana/dashboards/9568-jvm-actuator/)

근데 Prometheus 기반의 data들은 역시 통계 기록하기는 좋지만,,, 원인 분석하기에는 부실한 데이터임    
아래처럼 JVM 사용률 추이로 보기 좋게 되어있음    

![image](https://github.com/rkarud1234/TIL/assets/84266499/e984397f-5e77-4cc4-b82e-b89958374a64)    

일단 api 요청이 들어오면 Heap 사용률이 늘어나는건 알겠으나, 원인 분석을 위해서는 추가적인 정보가 필요해보임    

### Heap Dump 생성

Heap Dump파일을 생성해보자    

OpenJDK 설치 시 함께 설치되는 jmap을 이용해 Heap Dump를 뜰 수 있다.    
Heap Dump는 부하가 크기 때문에 서비스 중인 어플리케이션에 영향이 있을 수 있음    
우선 로컬에 해보자   

```bash
# PID 찾기
$ ps -eF | grep java
$ jps -mlv

# Thread Dump
$ jstack -l  <pid> > <file-path>
$ kill -3 <pid>

# Heap Dump
$ lsof -i:8080    # pid 찾기
$ jmap -dump:live,format=b,file=heapdump.hprof <PID>
```

맨 마지막에 있는 `jmap -dump` 명령어를 실행시켜보면 루트 디렉토리에 `heapdump.hprof` 라는 파일이 생기게되는데, `InteliJ` 를 통해서 해당 파일을 열어보면 이렇게 확인할수가 있다.     

![image](https://github.com/rkarud1234/TIL/assets/84266499/d020bf47-8c27-4d28-a76e-e3db7eb128d0)

이제 여기있는 내용들을 바탕으로 어디서 메모리를 못놓고 잡아먹는중인지 확인해봐야함     
이어서 `Heap Dump` 분석을 해보도록 하자

[Heap Dump 분석하기](https://blog.yevgnenll.me/posts/heap-dump-out-of-memory)      

### JVM 관련

- [GraalVM은 뭐고, OpenJ9, AdoptOpenJDK, Azul JDK는 뭐지?](https://redminit.tistory.com/entry/why-use-graalvm)
- [HotSpot JVM](https://velog.io/@aki/HotSpot-JVM)
