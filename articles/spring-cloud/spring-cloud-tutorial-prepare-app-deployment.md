---
title: How-to - Een Java Spring-toepassing voorbereiden voor implementatie in Azure Spring Cloud
description: In dit onderwerp bereidt u een Java Spring-toepassing voor voor op implementatie naar Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 025dbc56fb46ef5b6225d35564b8e4ac3c82e6e3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414466"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Een Java Spring-toepassing voorbereiden voor implementatie in Azure Spring Cloud

In dit onderwerp wordt uitgelegd hoe u een bestaande Java Spring-toepassing voorbereidt voor implementatie naar Azure Spring Cloud. Als azure spring cloud goed is geconfigureerd, biedt het robuuste services om uw Java Spring Cloud-toepassing te bewaken, te schalen en bij te werken.

In andere voorbeelden wordt uitgelegd hoe u een toepassing implementeert naar Azure Spring Cloud wanneer het POM-bestand is geconfigureerd. 
* [App starten met de Azure-portal](spring-cloud-quickstart-launch-app-portal.md)
* [App starten met de Azure CLI](spring-cloud-quickstart-launch-app-cli.md)

In dit artikel worden de vereiste afhankelijkheden uitgelegd en hoe u deze toevoegen aan het POM-bestand.

## <a name="java-runtime-version"></a>Java Runtime-versie

Alleen Lente/Java-toepassingen kunnen worden uitgevoerd in Azure Spring Cloud.

Azure Spring Cloud ondersteunt zowel Java 8 als Java 11. De hostingomgeving bevat de nieuwste versie van Azul Zulu OpenJDK voor Azure. Zie [De JDK installeren](https://docs.microsoft.com/azure/java/jdk/java-jdk-install)voor meer informatie over Azul Zulu OpenJDK voor Azure.

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot en Spring Cloud versies

Als u een bestaande toepassing voor springboot wilt voorbereiden voor implementatie in Azure Spring Cloud, u de afhankelijkheden Spring Boot en Spring Cloud voorbereiden in het POM-bestand van de toepassing, zoals weergegeven in de volgende secties.

Azure Spring Cloud ondersteunt alleen Spring Boot-apps, ofwel Spring Boot versie 2.1 of versie 2.2. In de volgende tabel worden de ondersteunde combinaties Spring Boot en Spring Cloud weergegeven:

Spring Boot-versie | Spring Cloud-versie
---|---
2.1 | Greenwich.RELEASE Greenwich.RELEASE
2,2 | Hoxton.RELEASE

### <a name="dependencies-for-spring-boot-version-21"></a>Afhankelijkheden voor Spring Boot versie 2.1

Voeg voor Spring Boot versie 2.1 de volgende afhankelijkheden toe aan het POM-bestand van de toepassing.

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

### <a name="dependencies-for-spring-boot-version-22"></a>Afhankelijkheden voor Spring Boot versie 2.2

Voeg voor Spring Boot versie 2.2 de volgende afhankelijkheden toe aan het POM-bestand van de toepassing.

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

## <a name="azure-spring-cloud-client-dependency"></a>Afhankelijkheid van Azure Spring Cloud-client

Azure Spring Cloud host en beheert Spring Cloud-componenten. De componenten omvatten Spring Cloud Service Registry en Spring Cloud Config Server. Neem de Azure Spring Cloud-clientbibliotheek op in uw afhankelijkheden om communicatie met uw Azure Spring Cloud-service-exemplaar toe te staan.

In de volgende tabel worden de juiste Azure Spring Cloud-versies voor uw app weergegeven die Spring Boot en Spring Cloud gebruiken.

Spring Boot-versie | Spring Cloud-versie | Azure Spring Cloud-versie
---|---|---
2.1 | Greenwich.RELEASE Greenwich.RELEASE | 2.1
2,2 | Hoxton.RELEASE | 2,2

Neem een van de volgende afhankelijkheden op in uw pom.xml-bestand. Selecteer de afhankelijkheid waarvan de Azure Spring Cloud-versie overeenkomt met die van uzelf.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Afhankelijkheid voor Azure Spring Cloud-versie 2.1

Voeg voor Spring Boot versie 2.1 de volgende afhankelijkheid toe aan het POM-bestand van de toepassing.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Afhankelijkheid voor Azure Spring Cloud-versie 2.2

Voeg voor Spring Boot versie 2.2 de volgende afhankelijkheid toe aan het POM-bestand van de toepassing.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Andere vereiste afhankelijkheden

Als u de ingebouwde functies van Azure Spring Cloud wilt inschakelen, moet uw toepassing de volgende afhankelijkheden bevatten. Deze opname zorgt ervoor dat uw toepassing zichzelf correct configureert met elk onderdeel.

### <a name="enablediscoveryclient-annotation"></a>DiscoveryClient-annotatie inschakelen

Voeg de volgende annotatie toe aan de broncode van de toepassing.
```java
@EnableDiscoveryClient
```
Zie bijvoorbeeld de piggymetrics-toepassing uit eerdere voorbeelden:
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

### <a name="service-registry-dependency"></a>Afhankelijkheid van serviceregister

Als u de beheerde Azure `spring-cloud-starter-netflix-eureka-client` Service Registry-service wilt gebruiken, neemt u de afhankelijkheid op in het pom.xml-bestand zoals hier wordt weergegeven:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Het eindpunt van de serviceregisterserver wordt automatisch geïnjecteerd als omgevingsvariabelen met uw app. Toepassingen kunnen zich registreren bij de Service Registry-server en andere afhankelijke microservices ontdekken.

### <a name="distributed-configuration-dependency"></a>Afhankelijkheid van gedistribueerde configuratie

Als u Gedistribueerde configuratie `spring-cloud-config-client` wilt inschakelen, neemt u de volgende afhankelijkheid op in het gedeelte afhankelijkheden van uw pom.xml-bestand:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Geef niet `spring.cloud.config.enabled=false` op in uw bootstrapconfiguratie. Anders werkt uw toepassing niet meer met Config Server.

### <a name="metrics-dependency"></a>Afhankelijkheid van metrische gegevens

Neem `spring-boot-starter-actuator` de afhankelijkheidssectie op in het gedeelte afhankelijkheden van uw pom.xml-bestand zoals hier wordt weergegeven:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Statistieken worden periodiek uit de JMX-eindpunten gehaald. U de statistieken visualiseren met behulp van de Azure-portal.

### <a name="distributed-tracing-dependency"></a>Afhankelijkheid van gedistribueerde tracering

Neem de `spring-cloud-starter-sleuth` `spring-cloud-starter-zipkin` volgende en afhankelijkheden op in de sectie afhankelijkheden van uw pom.xml-bestand:

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

 U moet ook een Azure Application Insights-exemplaar inschakelen om te werken met uw Azure Spring Cloud-serviceinstantie. Lees de [zelfstudie over gedistribueerde tracering](spring-cloud-tutorial-distributed-tracing.md) voor meer informatie over het gebruik van Application Insights met Azure Spring Cloud.

## <a name="see-also"></a>Zie ook
* [Toepassingslogboeken en statistieken analyseren](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [Uw configuratieserver instellen](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Gedistribueerde tracering gebruiken met Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Lente Quickstart Gids](https://spring.io/quickstart)
* [Documentatie voor springlaars](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Volgende stappen

In dit onderwerp hebt u geleerd hoe u uw Java Spring-toepassing configureert voor implementatie naar Azure Spring Cloud. Zie het volgende artikel voor meer informatie over het instellen van een Config Server-exemplaar.

> [!div class="nextstepaction"]
> [Meer informatie over het instellen van een Config Server-exemplaar](spring-cloud-tutorial-config-server.md)

Meer voorbeelden zijn beschikbaar op GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
