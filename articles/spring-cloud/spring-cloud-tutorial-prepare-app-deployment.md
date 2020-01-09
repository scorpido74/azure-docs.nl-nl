---
title: 'Zelf studie: een lente toepassing voorbereiden voor implementatie in azure lente Cloud'
description: In deze zelf studie gaat u een Java lente-toepassing voorbereiden voor implementatie.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 6e35430713a3dbc8317944fed1180432a2083676
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461594"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Een Java-lente toepassing voorbereiden voor implementatie in azure lente Cloud

Deze Quick Start laat zien hoe u een bestaande Java-toepassing voor de cloud kunt voorbereiden voor implementatie in azure lente-Cloud. Als het goed is geconfigureerd, biedt Azure lente Cloud krachtige services voor het bewaken, schalen en bijwerken van uw Java-lente-Cloud toepassing.

## <a name="java-runtime-version"></a>Java runtime-versie

Alleen veer-en Java-toepassingen kunnen worden uitgevoerd in azure lente-Cloud.

Azure lente-Cloud ondersteunt Java 8 en Java 11. De hosting omgeving bevat de nieuwste versie van Azul Zulu OpenJDK voor Azure. Zie [install the jdk](https://docs.microsoft.com/azure/java/jdk/java-jdk-install)(Engelstalig) voor meer informatie over Azul Zulu openjdk voor Azure.

## <a name="spring-boot-and-spring-cloud-versions"></a>Lente-boot-en lente-Cloud versies

Azure lente-Cloud ondersteunt alleen veer boot-apps. Deze versie 2,0 en versie 2,1 van de lente boot wordt ondersteund. De volgende tabel geeft een overzicht van de ondersteunde veer boot-en lente-Cloud combinaties:

Spring boot-versie | Lente-Cloud versie
---|---
2.0 | Finchley. RELEASE
2.1 | Greenwich. RELEASE

Controleer of uw pom. XML-bestand de juiste Spring-en lente-Cloud afhankelijkheden heeft, op basis van uw Spring boot-versie.

### <a name="dependencies-for-spring-boot-version-20"></a>Afhankelijkheden voor Spring boot versie 2,0

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

### <a name="dependencies-for-spring-boot-version-21"></a>Afhankelijkheden voor Spring boot versie 2,1

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

Azure lente-Cloud host en beheert lente-Cloud onderdelen voor u. Voor beelden van dergelijke onderdelen zijn lente Cloud Service Registry en lente-Cloud configuratie server. Neem de Azure veer cloud-client bibliotheek op in uw afhankelijkheden om communicatie met uw Azure lente-Cloud service-exemplaar toe te staan.

De volgende tabel geeft een lijst van de juiste Azure lente-Cloud versies voor uw app die een lente-boot-en lente-Cloud gebruiken.

Spring boot-versie | Lente-Cloud versie | Azure lente-Cloud versie
---|---|---
2.0 | Finchley. RELEASE | 2.0
2.1 | Greenwich. RELEASE | 2.1

Neem een van de volgende afhankelijkheden op in het bestand pom. XML. Selecteer de afhankelijkheid waarvan de Azure lente-Cloud versie overeenkomt met uw eigen.

### <a name="dependency-for-azure-spring-cloud-version-20"></a>Afhankelijkheid voor Azure lente Cloud versie 2,0

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Afhankelijkheid voor Azure lente Cloud versie 2,1

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Andere vereiste afhankelijkheden

Uw toepassing moet de volgende afhankelijkheden bevatten om de ingebouwde functies van Azure lente Cloud in te scha kelen. Deze insluiting zorgt ervoor dat uw toepassing zichzelf correct configureert voor elk onderdeel.  

### <a name="service-registry-dependency"></a>Afhankelijkheid van service register

Als u de beheerde Azure Service Registry-service wilt gebruiken, neemt u de `spring-cloud-starter-netflix-eureka-client` afhankelijkheid op in het bestand pom. XML, zoals hier wordt weer gegeven:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Het eind punt van de service register server wordt automatisch geïnjecteerd als omgevings variabelen met uw app. Toepassingen kunnen zich bij de service register server registreren en andere afhankelijke micro services ontdekken.

### <a name="distributed-configuration-dependency"></a>Afhankelijkheid van gedistribueerde configuratie

Als u gedistribueerde configuratie wilt inschakelen, neemt u de volgende `spring-cloud-config-client` afhankelijkheid op in de sectie afhankelijkheden van het bestand pom. XML:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Geef `spring.cloud.config.enabled=false` niet op in de configuratie van uw Boots trap. Anders werkt de toepassing niet meer met de configuratie server.

### <a name="metrics-dependency"></a>Afhankelijkheid van metrische gegevens

Neem de `spring-boot-starter-actuator` afhankelijkheid op in de sectie afhankelijkheden van het bestand pom. XML, zoals hier wordt weer gegeven:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metrische gegevens worden periodiek opgehaald uit de JMX-eind punten. U kunt de metrische gegevens visualiseren met behulp van de Azure Portal.

### <a name="distributed-tracing-dependency"></a>Afhankelijkheid van gedistribueerde tracering

Neem de volgende `spring-cloud-starter-sleuth` en `spring-cloud-starter-zipkin` afhankelijkheden op in het gedeelte met afhankelijkheden van het bestand pom. XML:

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

 U moet ook een Azure-toepassing Insights-exemplaar inschakelen om te werken met uw Azure lente-Cloud service-exemplaar. Lees de [zelf studie over gedistribueerde tracering](spring-cloud-tutorial-distributed-tracing.md) voor meer informatie over het gebruik van Application Insights met Azure lente Cloud.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw Java lente-toepassing kunt configureren voor implementatie in azure lente-Cloud. Ga verder met de volgende zelf studie voor meer informatie over het instellen van een configuratie Server-exemplaar.

> [!div class="nextstepaction"]
> [Meer informatie over het instellen van een configuratie Server-exemplaar](spring-cloud-tutorial-config-server.md)

Meer voor beelden zijn beschikbaar op GitHub: [Azure lente-Cloud voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
