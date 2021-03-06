---
title: Kafka 에코시스템용 Azure Event Hubs로 스트리밍
description: Kafka 프로토콜 및 API를 사용하여 Event Hubs로 스트리밍합니다.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: bahariri
ms.openlocfilehash: 8ef6240d19ce1ac1b891c95ce525a8bd211a2900
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297226"
---
# <a name="stream-into-event-hubs-for-the-kafka-ecosystem"></a>Kafka 에코시스템용 Event Hubs로 스트리밍

> [!NOTE]
> 이 샘플은 [GitHub](https://github.com/Azure/azure-event-hubs)에서 사용할 수 있습니다.

이 빠른 시작에서는 프로토콜 클라이언트를 변경하거나 사용자 고유의 클러스터를 실행하지 않고 Kafka 지원 Event Hubs로 스트리밍하는 방법을 보여줍니다. 생산자와 소비자가 응용 프로그램 구성을 간단하게 변경하여 Kafka 지원 Event Hubs로 대화하는 방법을 알아봅니다. Kafka 에코시스템용 Azure Event Hubs는 [Apache Kafka 버전 1.0](https://kafka.apache.org/10/documentation.html)을 지원합니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 있어야 합니다.

* Azure 구독. 구독이 없으면 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
* [Java Development Kit(JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Maven 이진 아카이브를 [다운로드](http://maven.apache.org/download.cgi)하여 [설치](http://maven.apache.org/install.html)합니다.
* [Git](https://www.git-scm.com/)
* [Kafka 지원 Event Hubs 네임스페이스](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Event Hubs에서 Kafka로 메시지 보내고 받기

1. [Azure Event Hubs 리포지토리](https://github.com/Azure/azure-event-hubs)를 복제합니다.

2. `azure-event-hubs/samples/kafka/quickstart/producer`로 이동합니다.

3. 다음과 같이 `src/main/resources/producer.config`에서 생산자에 대한 구성 세부 정보를 업데이트합니다.

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. 생산자 코드를 실행하고 Kafka 지원 Event Hubs로 스트리밍합니다.
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. `azure-event-hubs/samples/kafka/quickstart/consumer`로 이동합니다.

6. 다음과 같이 `src/main/resources/consumer.config`에서 소비자에 대한 구성 세부 정보를 업데이트합니다.
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Kafka 지원 Event Hubs에서 Kafka 클라이언트를 사용하여 소비자 코드 및 프로세스를 실행합니다.

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Event Hubs Kafka 클러스터에 이벤트가 있는 경우 이제 소비자로부터 해당 이벤트를 수신하기 시작합니다.

## <a name="next-steps"></a>다음 단계

* [Event Hubs에 대해 알아봅니다](event-hubs-what-is-event-hubs.md).
* [Kafka 에코시스템용 Event Hubs에 대해 알아보기](event-hubs-for-kafka-ecosystem-overview.md)
* [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)를 사용하여 [온-프레미스 Kafka에서 클라우드의 Kafka 지원 Event Hubs로 이벤트를 스트림합니다.](event-hubs-kafka-mirror-maker-tutorial.md)
* [Apache Flink](event-hubs-kafka-flink-tutorial.md) 또는 [Akka 스트림](event-hubs-kafka-akka-streams-tutorial.md)을 사용하여 Kafka 지원 Event Hubs에 스트리밍하는 방법을 알아봅니다.
