# AMQP vs. MQTT

AMQP와 MQTT 모두 TCP/IP 기반의 바이너리 메시징 프로토콜이다.
두 프로토콜 모두 IoT 애플리케이션에 적합하지만, 서로 다른 요구사항에 최적화되어있다.

MQTT는 일반적으로 수천 개의 임베디드 장치가 있는 high-latency, low-bandwidth 환경에 적합하다.
반면 AMQP의 경우 맞춤화된 요구사항이 있는 대규모 전사적 애플리케이션 환경에 더 적합하다.

> ❓ **바이너리 프로토콜과 텍스트 프로토콜**
> - 바이너리 프로토콜
   0과 1의 이진 형식으로 데이터를 인코딩합니다. 이진 데이터는 더 작고 빠르게 전송할 수 있기 때문에,
   이진 프로토콜은 성능이 중요한 응용 프로그램에서 사용됩니다. 주로 TCP/IP 프로토콜과 함께 
   사용됩니다.
   ex) AMQP, MQTT, Apache kafka, Thrift …
> 
> - 텍스트 프로토콜
   사람이 읽을 수 있는 문자열 형식으로 데이터를 인코딩합니다. 텍스트 프로토콜은 이진 프로토콜에
   비해 유연하고 호환성이 높지만, 데이터의 크기가 더 크고 처리 속도가 느릴 수 있습니다. 
   ex) HTTP, SMTP, IMAP, FTP …

## AMQP(Advanced Message Queuing Protocol)

AMQP는 뱅킹 서비스를 위해 고안된 MQ의 일종이다.

이전에도 상용화된 MQ들은 많았지만, 대부분 플랫폼 종속적인 제품들이었기 때문에 이기종가 메시지를 교환하기 위해서는 메시지 포맷 컨버전을 위한 메시지 브릿지를 이용하거나(속도 저하 발생), 시스템 자체를 통일해야 하는 불편함과 비효율성이 있었다.(속도 및 응답성의 저하는 금융쪽에서 특히 치명적인 약점으로 작용할 수 있음)
이러한 기존의 MQ의 약점들을 보완하기 위해 나온 것이 바로 AMQP이다.

즉, AMQP는 서로 다른 시스템간에 (비용/기술/시간적인 측면에서) 최대한 효율적인 방법으로 메시지를 교환하기 위한 MQ 프로토콜이다.

은행 산업에서 사용하기 위해 고안되었다는 기원 덕분에 AMQP는 MQTT에 비해 더 안정적인 대기열, 유연한 라우팅, 다양한 유형의 메시징, 보안 및 트랜잭션을 지원한다.

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/226266853-9dbaae78-48c6-4101-9a4c-b13628896303.png" width="40%" height="40%"/>
</br>      
   [AMQP 패킷의 구조]
</div>
                                  

## MQTT(Message Queuing Telementry Transport)

IoT 환경에서의 경량 메시징을 위해 만들어진 프로토콜이다.

개발을 위한 옵션은 제한적이나 리소스를 효율적으로 사용하기 위한 여러 가지 유용한 기능을 제공한다.
MQTT는 응답 시간이 빠르고 오버헤드가 최소화되며 원활한 데이터 전송을 보장하고 낮은 대역폭을 사용하기 때문에 AMQP에 비해 리소스 사용률이 적습니다.

즉, MQTT는 제한적인 환경에서 많은 장치들에게 적은 리소스로 메시지를 교환하기 위한 프로토콜이다.

MQTT의 패킷은 다음과 같은 세 파트로 구성된다.

- Fixed header(Mandatory): 2~5byte only
- Variable header(Optional): 가변 사이즈
- Payload(Optional): 최대 256MB. 명령이나 데이터 값을 보유하고 있다.


<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/226267333-eec21dd3-5e11-4585-805a-f104d2cb239b.png" width="70%" height="70%"/>
</br>      
   [MQTT 패킷의 구조]
</div>

<br>
<br>

효율성, 안정성, 확장성, 보안 등의 요구사항에 따라 적합한 프로토콜을 골라야 한다.

|  | AMQP | MQTT |
| --- | --- | --- |
| Messaging scenarios | 다양한 메시징 시나리오가 존재 | 간단한 메시징 시나리오만 가능하다. |
| Framing | 버퍼 지향 접근법 | 스트림 지향 접근법 |
| Transactions | 서로 다른 트랜잭션 시나리오 지원 | Limited/basic only |
| Connection Security | 포괄적인 보안 지원 | 기본적인 보안 지원 |
| User Security | 포괄적인 사용자 보안 및 인증 | 사용자 인증이 존재하지만 약함 |
| MetaData | 메타데이터를 통해 라우팅, 메시징 등의 기타 기능이 가능 | 메타데이터 없음 |
| QoS | 2레벨 | 3레벨 |
| Client-Server | 대칭 관계 | 비대칭 관계 |
| Connection redirectioin | Multiple ways to redirect messages | Limited |
| Scalability | Scalable | very Scalable |
| Extensibility | 프로토콜 내장 | Not naturally extensible |

> ❓ **버퍼 지향 접근법과 스트림 지향 접근법**
> - 버퍼 지향 접근법(Buffer-oriented approach)
   데이터를 읽어들일 때 한 번에 전체 파일을 읽어들이는 것이 아니라, 일정한 크기의 버퍼를 사용하여
   조금씩 읽어들이는 방식이다.
   버퍼에 데이터를 일시적으로 저장하고 한 번에 처리하기 때문에 처리 속도가 빠르고, 버퍼 크기에
   따라 메모리 사용량이 다르다.
> 
> - 스트림 지향 접근법(Stream-oriented approach)
   데이터를 연속적인 스트림으로 처리하는 방식이다.
   버퍼 지향 접근법에 비해서 속도는 상대적으로 느리지만 필요한 만큼 데이터를 처리하기 때문에 
   메모리 사용량을 최소화 할 수 있다.

### 결론

MQTT는 센서 네트워크, 낮은 컴퓨팅 장치, 낮은 대역폭 네트워크에 더 나은 선택이며 기본 수준의 보안이 필요합니다. 반면 기업 비즈니스 시스템, 신뢰할 수 있는 네트워크, 서로 다른 메시징 요구 사항 및 엄격한 보안 요구 사항의 경우 AMQP가 더 나은 선택입니다.

<br>

참고링크    
- [Macrometa : AMQP vs. MQTT](https://www.macrometa.com/iot-infrastructure/amqp-vs-mqtt)
