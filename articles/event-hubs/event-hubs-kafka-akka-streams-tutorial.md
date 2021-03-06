---
title: Akka Streams voor Apache Kafka met Azure Eventhubs | Microsoft Docs
description: Event Hub Akka Streams verbinden met een Kafka ingeschakeld
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: f6671e05c53b8be2e06eb969adc8a4d5736aff37
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284436"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Met behulp van Akka Streams voor Apache Kafka met Eventhubs

Een van de belangrijkste voordelen van het gebruik van Apache Kafka is het ecosysteem van frameworks voor die deze verbinding met maken kan. Event-Hubs voor Kafka-functionaliteit combineert de flexibiliteit van Kafka met de schaalbaarheid, consistentie en ondersteuning van het Azure-ecosysteem.

Deze zelfstudie leert u hoe verbinding Akka stromen maken met Kafka-functionaliteit eventhubs zonder te wijzigen van uw clients protocol of uitvoeren van uw eigen clusters. Biedt ondersteuning voor Azure Event Hubs voor de Kafka [Apache Kafka, versie 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie, zorg ervoor dat u de volgende vereisten:

* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK 1.8 +)](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.
    * Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.
* [Download](http://maven.apache.org/download.cgi) en [installeren](http://maven.apache.org/install.html) een binaire Maven-archief
    * Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.
* [Git](https://www.git-scm.com/downloads)
    * Op Ubuntu kunt u `sudo apt-get install git` uitvoeren om Git te installeren.

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Een Event Hubs-naamruimte is vereist om te verzenden of ontvangen van alle Event Hubs-service. Zie [maken Kafka ingeschakeld Event Hubs](event-hubs-create-kafka-enabled.md) voor informatie over het ophalen van een Event Hubs Kafka-eindpunt. Zorg ervoor dat u het kopiëren van de Event Hubs-verbindingsreeks voor later gebruik.

## <a name="clone-the-example-project"></a>De voorbeeldproject klonen

Nu dat u een verbindingsreeks Event Hubs waarvoor Kafka is ingeschakeld hebt, kloon de opslagplaats van de Azure Event Hubs en navigeer naar de `akka` submap:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/akka
```

## <a name="akka-streams-producer"></a>Akka Streams producent

In het opgegeven Akka Streams producent voorbeeld berichten verzenden naar de Event Hubs-service.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Geef een Event Hubs Kafka-eindpunt

#### <a name="producer-applicationconf"></a>Producent application.conf

Update de `bootstrap.servers` en `sasl.jaas.config` waarden in `producer/src/main/resources/application.conf` om te leiden van de producent naar de Event Hubs Kafka-eindpunt met de juiste verificatie.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Producent uitvoeren vanaf de opdrachtregel

Voor de producent uitvoeren vanaf de opdrachtregel, genereren de JAR en vervolgens uitvoeren in Maven (of de JAR met behulp van Maven, klikt u vervolgens uitvoeren in Java door toe te voegen van de benodigde Kafka JAR(s) aan het klassepad genereren):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

De producent gestart voor het verzenden van gebeurtenissen naar de Kafka-functionaliteit event hub op onderwerp `test`, en worden de gebeurtenissen naar de stdout afgedrukt.

## <a name="akka-streams-consumer"></a>Akka Streams consumenten

In het voorbeeld opgegeven consumenten berichten ontvangen van de Kafka-functionaliteit eventhubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Geef een Event Hubs Kafka-eindpunt

#### <a name="consumer-applicationconf"></a>Consumenten application.conf

Update de `bootstrap.servers` en `sasl.jaas.config` waarden in `consumer/src/main/resources/application.conf` om te leiden van de gebruiker voor het Event Hubs Kafka-eindpunt met de juiste verificatie.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Consumenten uitvoeren vanaf de opdrachtregel

Voor de consument uitvoeren vanaf de opdrachtregel, genereren de JAR en vervolgens uitvoeren in Maven (of de JAR met behulp van Maven, klikt u vervolgens uitvoeren in Java door toe te voegen van de benodigde Kafka JAR(s) aan het klassepad genereren):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Als de Kafka-functionaliteit event hub gebeurtenissen (bijvoorbeeld, als uw producent wordt ook uitgevoerd), en vervolgens de consument begint het ontvangen van gebeurtenissen van onderwerp `test`. 

Bekijk de [Akka Streams Kafka handleiding](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) voor meer informatie over Akka Streams gedetailleerde.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Event Hubs](event-hubs-what-is-event-hubs.md)
* [Meer informatie over Eventhubs voor Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Gebruik [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) om [gebeurtenissen van Kafka on-premises te streamen naar Event Hubs waarvoor Kafka is ingeschakeld in de cloud](event-hubs-kafka-mirror-maker-tutorial.md).
* Meer informatie over het streamen naar Kafka ingeschakelde Event Hubs met behulp [systeemeigen Kafka toepassingen](event-hubs-quickstart-kafka-enabled-event-hubs.md) of [Apache Flink](event-hubs-kafka-flink-tutorial.md)
