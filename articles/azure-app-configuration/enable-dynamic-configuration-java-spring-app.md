---
title: Dynamische configuratie gebruiken in een app voor Spring Boot
titleSuffix: Azure App Configuration
description: Meer informatie over het dynamisch bijwerken van configuratiegegevens voor springboot-apps
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 37c832e3b6d1430da0b45558c9632f0486a7233b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79216760"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Zelfstudie: Dynamische configuratie gebruiken in een Java Spring-app

De clientbibliotheek App Configuration Spring Boot ondersteunt het updaten van een set configuratie-instellingen op aanvraag, zonder dat een toepassing opnieuw wordt opgestart. De clientbibliotheek caches elke instelling om te voorkomen dat te veel oproepen naar de configuratie op te slaan. De vernieuwingsbewerking werkt de waarde pas bij nadat de in de cache opgeslagen waarde is verlopen, zelfs niet wanneer de waarde is gewijzigd in het configuratiearchief. De standaardvervaldatum voor elke aanvraag is 30 seconden. Het kan worden overschreven indien nodig.

U controleren op bijgewerkte `AppConfigurationRefresh`instellingen `refreshConfigurations()` op aanvraag door te bellen 's methode.

U ook het `spring-cloud-azure-appconfiguration-config-web` pakket gebruiken, waarbij `spring-web` u afhankelijk is van geautomatiseerde vernieuwing.

## <a name="use-automated-refresh"></a>Geautomatiseerd vernieuwen gebruiken

Als u automatisch vernieuwen wilt gebruiken, begint u met een app voor het opstarten van de lente die gebruikmaakt van app-configuratie, zoals de app die u maakt door de [snelstart voor het voorjaar voor app-configuratie te](quickstart-java-spring-app.md)volgen.

Open vervolgens het *bestand pom.xml* in een `<dependency>` teksteditor en voeg een voor `spring-cloud-azure-appconfiguration-config-web`.

**Voorjaarswolk 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Voorjaarswolk 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Sla het bestand op en bouw en voer uw toepassing uit zoals gewoonlijk.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de app Spring Boot ingeschakeld om de configuratie-instellingen van app-configuratie dynamisch te vernieuwen. Ga door naar de volgende zelfstudie voor meer informatie over het gebruik van een door Azure beheerde identiteit om de toegang tot app-configuratie te stroomlijnen.

> [!div class="nextstepaction"]
> [Beheerde identiteitsintegratie](./howto-integrate-azure-managed-service-identity.md)
