---
title: Service register en detectie automatiseren
description: Meer informatie over het automatiseren van service detectie en registratie met een lente-Cloud service register
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76278861"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Uw lente-Cloud Services ontdekken en registreren

Service detectie is een belang rijke vereiste voor een architectuur op basis van micro Services.  Het configureren van elke client kost hand matig tijd en introduceert de kans op menselijke fouten.  Dit probleem wordt opgelost met het REGI ster van de Azure veer Cloud service.  Zodra de service register server is geconfigureerd, wordt de service registratie en detectie van de micro services van uw toepassing beheerd. De service register server onderhoudt een REGI ster van de geïmplementeerde micro Services, maakt taak verdeling aan de client zijde mogelijk en koppelt service providers van clients af zonder dat hiervoor DNS nodig is.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Uw toepassing registreren met een lente-Cloud service register

Voordat uw toepassing service registratie en detectie kan beheren met een lente-Cloud service register, moeten er meerdere afhankelijkheden zijn opgenomen in het *pom.xml* -bestand van de toepassing.
Neem afhankelijkheden voor *veer-Cloud-starter-Netflix-Eureka-client* en *lente-Cloud-starter-Azure-lente-cloud-client* op uw *pom.xml*

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

Ten slotte voegen we een aantekening toe aan de klasse op het hoogste niveau van uw toepassing

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

Het eind punt van de register server van de lente-Cloud service wordt als een omgevings variabele in uw toepassing ingevoegd.  Micro Services kan zich nu registreren bij de service register server en andere afhankelijke micro services ontdekken.

Houd er rekening mee dat het enkele minuten kan duren voordat de wijzigingen van de server worden door gegeven aan alle micro Services.
