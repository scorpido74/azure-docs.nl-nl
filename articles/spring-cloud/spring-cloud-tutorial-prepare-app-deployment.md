---
title: Een lente toepassing voorbereiden voor implementatie in azure lente Cloud | Microsoft Docs
description: In deze Quick Start hebt u een Java lente-toepassing voor bereid voor implementatie.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: 98d9f3f656cff84cec8d223ed535255157155bd2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039005"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Zelfstudie: Een Java-lente toepassing voorbereiden voor implementatie in azure lente Cloud

Deze Quick Start laat zien hoe u een bestaande Java-toepassing voor de cloud kunt voorbereiden voor implementatie in azure lente-Cloud.  Een goede configuratie van Azure veer Cloud biedt krachtige services voor het bewaken, schalen en bijwerken van uw lente-Cloud toepassing. 

## <a name="java-runtime-version"></a>Java runtime-versie

Alleen veer-en Java-toepassingen kunnen worden uitgevoerd in azure lente-Cloud.

Zowel Java 8 als Java 11 worden ondersteund. De hostomgeving bevat de meest recente Azul Zulu OpenJDK voor Azure. Raadpleeg [dit artikel](https://docs.microsoft.com/azure/java/jdk/java-jdk-install) voor meer informatie over Azul Zulu openjdk voor Azure. 

## <a name="spring-boot-and-spring-cloud-versions"></a>Lente-boot-en lente-Cloud versies

Alleen veer boot-apps worden ondersteund in azure lente-Cloud. De installatie van zowel de lente-opstart 2,0 als de 2,1 wordt ondersteund. In onderstaande tabel worden ondersteunde combi Naties van Spring-en lente-Clouds weer gegeven.

Spring boot-versie | Lente-Cloud versie
---|---
2,0. x | Finchley. RELEASE
2.1. x | Greenwich. RELEASE

Controleer of uw `pom.xml`-bestand de lente-opstart-en lente-Cloud afhankelijkheden heeft die zijn gebaseerd op uw versie.

### <a name="version-20"></a>Versie 2,0:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>Versie 2,1:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure veer cloud client-afhankelijkheid

Azure lente-Cloud host en beheert lente-Cloud onderdelen voor u, zoals het lente-Cloud service register en de lente-Cloud configuratie server. Neem de client bibliotheek van de Azure lente-Cloud op in uw afhankelijkheden om communicatie met uw Azure lente-Cloud service-exemplaar toe te staan.

In de volgende tabel ziet u de juiste versies van de bron-app voor Spring boot/lente.

Spring boot-versie | Lente-Cloud versie | Azure lente-Cloud versie
---|---|---
2,0. x | Finchley. RELEASE | 2.0.0-MOMENT OPNAME
2.1. x | Greenwich. RELEASE | 2.1.0-MOMENT OPNAME

Neem dit fragment op in uw `pom.xml` met de juiste Azure lente-Cloud versie in de ' dependency ':

```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
    
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="other-required-dependencies"></a>Andere vereiste afhankelijkheden

Uw toepassing moet de volgende afhankelijkheden bevatten om de ingebouwde functies van Azure lente Cloud in te scha kelen. Zo zorgt u ervoor dat uw toepassing zichzelf correct configureert voor elk onderdeel.  

### <a name="service-registry"></a>Service register

Als u de beheerde Azure Service Registry-service wilt gebruiken, neemt u `spring-cloud-starter-netflix-eureka-client` op in `POM.xml`, zoals hieronder wordt weer gegeven.

Het eind punt van de service register server wordt automatisch geïnjecteerd als omgevings variabelen met uw app. Toepassingen kunnen zichzelf registreren bij de service register server en andere afhankelijke micro services ontdekken.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>Gedistribueerde configuratie

Als u gedistribueerde configuratie wilt inschakelen, neemt u `spring-cloud-config-client` op in het gedeelte afhankelijkheden van uw `pom.xml`.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Geef `spring.cloud.config.enabled=false` niet op in de Boots trap-configuratie, omdat hierdoor wordt voor komen dat de toepassing werkt met de configuratie server.

### <a name="metrics"></a>Metrische gegevens

Neem `spring-boot-starter-actuator` op in het gedeelte met afhankelijkheden van uw pom. XML. Metrische gegevens worden regel matig opgehaald uit de JMX-eind punten en kunnen worden gevisualiseerd met behulp van de Azure Portal.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>Gedistribueerde tracering

Neem `spring-cloud-starter-sleuth` en `spring-cloud-starter-zipkin` op in het gedeelte afhankelijkheden van uw pom. XML zoals hieronder. U moet ook een Azure-app Insights-exemplaar inschakelen om te werken met uw Azure lente-Cloud service-exemplaar. Meer informatie over het inschakelen van app Insights met Azure veer Cloud [hier](spring-cloud-tutorial-distributed-tracing.md)

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw Java lente-toepassing kunt configureren voor implementatie in azure lente-Cloud.  Ga verder met de volgende zelf studie als u wilt weten hoe u de configuratie server inschakelt.

> [!div class="nextstepaction"]
> [Meer informatie over het instellen van uw configuratie server](spring-cloud-tutorial-config-server.md).

