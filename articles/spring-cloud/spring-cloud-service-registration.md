---
title: Serviceregister en detectie automatiseren
description: Meer informatie over het automatiseren van servicedetectie en -registratie met behulp van Spring Cloud Service Registry
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278861"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Ontdek en registreer uw Spring Cloud-services

Service Discovery is een belangrijke vereiste voor een architectuur op basis van microservice.  Het handmatig configureren van elke client kost tijd en introduceert de mogelijkheid van menselijke fouten.  Azure Spring Cloud Service Registry lost dit probleem op.  Eenmaal geconfigureerd, beheert een serviceregisterserver de registratie en detectie van de service voor de microservices van uw toepassing. De Service Registry-server houdt een register bij van de geïmplementeerde microservices, maakt loadbalancing aan clientzijde mogelijk en ontkoppelt serviceproviders van clients zonder afhankelijk te zijn van DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Uw toepassing registreren met springcloudserviceregister

Voordat uw toepassing serviceregistratie en -detectie kan beheren met behulp van Spring Cloud Service Registry, moeten verschillende afhankelijkheden worden opgenomen in het *pom.xml-bestand* van de toepassing.
Neem afhankelijkheden voor *spring-cloud-starter-netflix-eureka-client* en *spring-cloud-starter-azure-spring-cloud-client* op uw *pom.xml*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>De klasse op het hoogste niveau bijwerken

Tot slot voegen we een annotatie toe aan de topklasse van uw toepassing

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Het endpoint van de Spring Cloud Service Registry-server wordt geïnjecteerd als een omgevingsvariabele in uw toepassing.  Microservices kunnen zich nu registreren bij de Service Registry-server en andere afhankelijke microservices ontdekken.

Houd er rekening mee dat het enkele minuten kan duren voordat de wijzigingen van de server naar alle microservices worden doorgegeven.
