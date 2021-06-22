# RabbitMQ vs Kafka
---

- 카프카와 RabbitMQ는 설계적으로 다르게 접근하였으며, 매우 다른 문제를 푼다. (반면에 많은 사람들이 두 가지를 비슷하게 보고 비교한다.)

    While the two solutions take very different approaches architecturally and can solve very different problems, many find themselves comparing them for overlapping solutions

- 각 서비스들이 서로 통신할 수 있어야 하는 서비스들이 더욱더 많아지는 분산 환경에 의해 늘어남에 따라 RabbitMQ와 Kafka는 각서비스들의 통신을 편리하게 할 수 있는 서비스들이 인기를 끌고 있다.

    In an increasingly distributed environment where more and more services need to communicate with each other, RabbitMQ and Kafka have both come to be popular services that facilitate that communication.

- RabbitMQ는 요약하면 "오픈 소스 분산 메시지 브로커"이다. Erlang으로 만들어졌으며 그것은 복잡한 라우팅 시나리오에서 효율적인 메시지 전달을 제공한다. RabbitMQ 브로커는 분산될 수 있으며 서버 장애나 네트워크 문제에도 신뢰할 수 있도록 설정할 수 있다.

    RabbitMQ is often summarized as an “open source distributed message broker.” Written in Erlang, it facilitates the efficient delivery of messages in complex routing scenarios. Initially built around the popular AMQP protocol, it’s also highly compatible with existing technologies, while its capabilities can be expanded through plug-ins enabled on the server. RabbitMQ brokers can be distributed and configured to be reliable in case of network or server failure.

- 반면에 Kafka는 "분산된 이벤트 스트리밍 플랫폼"이라고 표현된다. 유연한 라우팅에 초점을 맞추기 보다는, 원시 처리량을 용이하게 제공합니다. Scala와 Java로 작성되었으며 Kafka는 메시지가 영구적으로 디스크에 저장되는 로그의 끝에만 쓰이며 클라이언트는 로그로부터 읽고 고를 수 있는 "분산된 더할 수만 있는 로그"라는 아이디어를 구현했다. 게다가 Kafka 클러스터는 높은 가용성을 제공하며 분산될 수 있으며 여러 서버에 걸쳐 클러스터링을 할 수 있다.

    Apache Kafka, on the other hand, is described as a “distributed event streaming platform.” Rather than focusing on flexible routing, it instead facilitates raw throughput. Written in Scala and Java, Kafka builds on the idea of a “distributed append-only log” where messages are written to the end of a log that’s persisted to disk, and clients can choose where they begin reading from that log. Likewise, Kafka clusters can be distributed and clustered across multiple servers for a higher degree of availability.

- Kafka와 RabbitMQ는 동일한 서비스는 아니지만, 많은 사람들이 메시지 옵션에 대해 이 두가지로 좁히며 어떤 것이 더 나은 지 궁금해한다. 이렇게 고민하는 것보다는 각 서비스들이 어떤 것에 뛰어야 하는지에 초점을 맞추고, 두가지의 차이점을 분석하고, 내가 만드는 서비스에게 더 적합한 것을 골라야 된다. 두 서비스 중 벗어나더라도 더 적합한 것을 고려해야 한다.

    While they’re not the same service, many often narrow down their messaging options to these two, but are left wondering which of them is better. I’ve long believed that’s not the correct question to ask. Instead, you want to focus on what each service excels at, analyze their differences, and then decide which of the two best fits your use case. Even outside of the features of either service, you should also take into consideration the skills needed to operate the services and the developer communities around them.

- RabbitMQ의 메시지 브로커는 특정한 라우팅이 필요하며 메시지당 보증이 필요한 경우에 특화되어 있으며, Kafka의 append-only log는 개발자가 스트림 history에 접근할 수 있도록 해주고 더 많은 스트리밍 프로세스를 전달하도록 해주는데 특화되어있다.

    RabbitMQ’s message broker design excelled in use cases that had specific routing needs and per-message guarantees, whereas Kafka’s append-only log allowed developers access to the stream history and more direct stream processing.

- RabbitMQ는 kafka와 비슷하게 append-only log를 모델링하는 새로운 데이터 구조를 도입하고 있다. AMQP 프로토콜과 호환되지만, 이진 기반 스트림 프로토콜에도 도입될 것이다.

    While RabbitMQ will continue to offer its traditional queue model, it will also introduce a new data structure modeling an append-only log, with non-destructive consuming semantics. This new data structure will work much like Kafka’s persistent log, and will be an exciting addition for RabbitMQ users looking to expand their streaming use case. And while this feature will be compatible with the AMQP protocol, it will also introduce a binary-based stream protocol.

- RabbitMQ는 벤치 마크 방법, 클러스터 사이즈 같은 방법을 통해 어떻게 리소스의 성능을 극대화 할지 제공한다. Kafka 역시 production에서 kafka를 운영하는 법에 대한 좋은 가이드를 제공한다.
