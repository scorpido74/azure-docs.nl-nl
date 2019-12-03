---
title: 'Zelf studie: een lente toepassing voorbereiden voor implementatie in azure lente Cloud'
description: In deze zelf studie gaat u een Java lente-toepassing voorbereiden voor implementatie.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 3c567c54f887afe9ac112970a682b9466515d949
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708735"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Zelf studie: een Java-lente toepassing voorbereiden voor implementatie in azure lente Cloud

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

Controleer of uw `pom.xml`-bestand de lente-opstart-en lente-Cloud afhankelijkheden op basis van uw versie heeft.

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
2,0. x | Finchley. RELEASE | 2,0. x
2.1. x | Greenwich. RELEASE | 2.1. x

Neem een van de onderstaande fragmenten op in uw `pom.xml`.  Selecteer het fragment waarvan de versie overeenkomt met die van uw eigen.

### <a name="version-20x"></a>Versie 2.0. x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="version-21x"></a>Versie 2.1. x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Andere vereiste afhankelijkheden

Uw toepassing moet de volgende afhankelijkheden bevatten om de ingebouwde functies van Azure lente Cloud in te scha kelen. Zo zorgt u ervoor dat uw toepassing zichzelf correct configureert voor elk onderdeel.  

### <a name="service-registry"></a>Service register

Als u de beheerde Azure Service Registry-service wilt gebruiken, neemt u `spring-cloud-starter-netflix-eureka-client` op in `POM.xml` zoals hieronder wordt weer gegeven.

Het eind punt van de service register server wordt automatisch geïnjecteerd als omgevings variabelen met uw app. Toepassingen kunnen zichzelf registreren bij de service register server en andere afhankelijke micro services ontdekken.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>Gedistribueerde configuratie

Als u gedistribueerde configuratie wilt inschakelen, neemt u `spring-cloud-config-client` op in het gedeelte met afhankelijkheden van uw `pom.xml`.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Geef `spring.cloud.config.enabled=false` niet op in Boots trap configuratie, omdat de toepassing niet meer met de configuratie server kan werken.

### <a name="metrics"></a>Metrische gegevens

Neem `spring-boot-starter-actuator` op in het gedeelte met afhankelijkheden van uw pom. XML. Metrische gegevens worden regel matig opgehaald uit de JMX-eind punten en kunnen worden gevisualiseerd met behulp van de Azure Portal.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>Gedistribueerde tracering

Neem `spring-cloud-starter-sleuth` en `spring-cloud-starter-zipkin` op in het gedeelte met afhankelijkheden van uw pom. XML zoals hieronder. U moet ook een Azure-app Insights-exemplaar inschakelen om te werken met uw Azure lente-Cloud service-exemplaar. Meer informatie over het inschakelen van app Insights met Azure veer Cloud [hier](spring-cloud-tutorial-distributed-tracing.md)

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
