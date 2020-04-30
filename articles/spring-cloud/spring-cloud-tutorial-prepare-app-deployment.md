---
title: 'Instructies: een Java-lente toepassing voorbereiden voor implementatie in azure lente Cloud'
description: In dit onderwerp maakt u een Java-lente toepassing voor implementatie in azure lente-Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 16cee333d52765755b732c4de4dd8a6e092a130d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731183"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Een Java-lente toepassing voorbereiden voor implementatie in azure lente Cloud

In dit onderwerp wordt beschreven hoe u een bestaande Java-toepassing voorbereidt voor implementatie naar Azure lente-Cloud. Als het goed is geconfigureerd, biedt Azure lente Cloud krachtige services voor het bewaken, schalen en bijwerken van uw Java-lente-Cloud toepassing.

Andere voor beelden wordt uitgelegd hoe u een toepassing implementeert in azure lente-Cloud wanneer het POM-bestand wordt geconfigureerd. 
* [App starten met behulp van de Azure Portal](spring-cloud-quickstart-launch-app-portal.md)
* [App starten met Azure CLI](spring-cloud-quickstart-launch-app-cli.md)

In dit artikel worden de vereiste afhankelijkheden beschreven en wordt uitgelegd hoe u deze toevoegt aan het POM-bestand.

## <a name="java-runtime-version"></a>Java runtime-versie

Alleen veer-en Java-toepassingen kunnen worden uitgevoerd in azure lente-Cloud.

Azure lente-Cloud ondersteunt Java 8 en Java 11. De hosting omgeving bevat de nieuwste versie van Azul Zulu OpenJDK voor Azure. Zie [install the jdk](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)(Engelstalig) voor meer informatie over Azul Zulu openjdk voor Azure.

## <a name="spring-boot-and-spring-cloud-versions"></a>Lente-boot-en lente-Cloud versies

Als u een bestaande Spring boot-toepassing wilt voorbereiden voor implementatie in azure lente-Cloud, neemt u de voor-en achterliggende Cloud afhankelijkheden op in het POM-bestand van de toepassing, zoals wordt weer gegeven in de volgende secties.

De Azure lente-Cloud ondersteunt alleen veer boot-apps van veer boot versie 2,1 of versie 2,2. De volgende tabel geeft een overzicht van de ondersteunde veer boot-en lente-Cloud combinaties:

Spring boot-versie | Lente-Cloud versie
---|---
2.1 | Greenwich. RELEASE
2,2 | Hoxton. RELEASE

### <a name="dependencies-for-spring-boot-version-21"></a>Afhankelijkheden voor Spring boot versie 2,1

Voor Spring boot versie 2,1 voegt u de volgende afhankelijkheden toe aan het POM-bestand van de toepassing.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Afhankelijkheden voor Spring boot versie 2,2

Voor Spring boot versie 2,2 voegt u de volgende afhankelijkheden toe aan het POM-bestand van de toepassing.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure veer cloud client-afhankelijkheid

Azure lente-Cloud hosts en beheert lente-Cloud onderdelen. De onderdelen omvatten veer-Cloud service register en lente-Cloud configuratie server. Neem de Azure veer cloud-client bibliotheek op in uw afhankelijkheden om communicatie met uw Azure lente-Cloud service-exemplaar toe te staan.

De volgende tabel geeft een lijst van de juiste Azure lente-Cloud versies voor uw app die een lente-boot-en lente-Cloud gebruiken.

Spring boot-versie | Lente-Cloud versie | Azure lente-Cloud versie
---|---|---
2.1 | Greenwich. RELEASE | 2.1
2,2 | Hoxton. RELEASE | 2,2

Neem een van de volgende afhankelijkheden op in het bestand pom. XML. Selecteer de afhankelijkheid waarvan de Azure lente-Cloud versie overeenkomt met uw eigen.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Afhankelijkheid voor Azure lente Cloud versie 2,1

Voor Spring boot versie 2,1 voegt u de volgende afhankelijkheden toe aan het POM-bestand van de toepassing.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Afhankelijkheid voor Azure lente Cloud versie 2,2

Voor Spring boot versie 2,2 voegt u de volgende afhankelijkheden toe aan het POM-bestand van de toepassing.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Andere vereiste afhankelijkheden

Uw toepassing moet de volgende afhankelijkheden bevatten om de ingebouwde functies van Azure lente Cloud in te scha kelen. Deze insluiting zorgt ervoor dat uw toepassing zichzelf correct configureert voor elk onderdeel.

### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient aantekening

Voeg de volgende aantekening toe aan de bron code van de toepassing.
```java
@EnableDiscoveryClient
```
Zie bijvoorbeeld de piggymetrics-toepassing uit eerdere voor beelden:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="service-registry-dependency"></a>Afhankelijkheid van service register

Als u de beheerde Azure Service Registry-service wilt gebruiken `spring-cloud-starter-netflix-eureka-client` , neemt u de afhankelijkheid op in het bestand pom. XML, zoals hier wordt weer gegeven:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Het eind punt van de service register server wordt automatisch geïnjecteerd als omgevings variabelen met uw app. Toepassingen kunnen zich bij de service register server registreren en andere afhankelijke micro services ontdekken.

### <a name="distributed-configuration-dependency"></a>Afhankelijkheid van gedistribueerde configuratie

Als u gedistribueerde configuratie wilt inschakelen `spring-cloud-config-client` , neemt u de volgende afhankelijkheden op in het gedeelte afhankelijkheden van het bestand pom. XML:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Geef `spring.cloud.config.enabled=false` niets op in de configuratie van uw Boots trap. Anders werkt de toepassing niet meer met de configuratie server.

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

Neem de volgende `spring-cloud-starter-sleuth` en `spring-cloud-starter-zipkin` afhankelijkheden op in het gedeelte afhankelijkheden van het bestand pom. XML:

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

## <a name="see-also"></a>Zie ook
* [Toepassings logboeken en metrische gegevens analyseren](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [Uw configuratieserver instellen](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Gedistribueerde tracering gebruiken met Azure veer Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Hand leiding voor veer Snelstartgids](https://spring.io/quickstart)
* [Spring boot-documentatie](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Volgende stappen

In dit onderwerp hebt u geleerd hoe u uw Java lente-toepassing kunt configureren voor implementatie in azure lente-Cloud. Zie het volgende artikel voor meer informatie over het instellen van een configuratie Server-exemplaar.

> [!div class="nextstepaction"]
> [Meer informatie over het instellen van een configuratie Server-exemplaar](spring-cloud-tutorial-config-server.md)

Meer voor beelden zijn beschikbaar op GitHub: [Azure lente-Cloud voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
