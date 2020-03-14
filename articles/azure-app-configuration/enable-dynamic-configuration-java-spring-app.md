---
title: Dynamische configuratie gebruiken in een Spring boot-app
titleSuffix: Azure App Configuration
description: Meer informatie over het dynamisch bijwerken van configuratie gegevens voor Spring boot-apps
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 37c832e3b6d1430da0b45558c9632f0486a7233b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79216760"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Zelf studie: dynamische configuratie in een Java lente-app gebruiken

De configuratie-client bibliotheek voor het configureren van de hardwareconfiguratie ondersteunt het bijwerken van een set configuratie-instellingen op aanvraag, zonder dat een toepassing opnieuw moet worden opgestart. In de client bibliotheek wordt elke instelling in de cache opgeslagen om te veel aanroepen naar de configuratie opslag te voor komen. Met de vernieuwings bewerking wordt de waarde niet bijgewerkt totdat de waarde in de cache is verlopen, zelfs wanneer de waarde is gewijzigd in de configuratie opslag. De standaard verval tijd voor elke aanvraag is 30 seconden. Dit kan zo nodig worden overschreven.

U kunt op aanvraag bijgewerkte instellingen controleren door de methode `refreshConfigurations()` van `AppConfigurationRefresh`aan te roepen.

U kunt ook het `spring-cloud-azure-appconfiguration-config-web`-pakket gebruiken, dat afhankelijk is van `spring-web` voor het afhandelen van automatische vernieuwing.

## <a name="use-automated-refresh"></a>Automatisch vernieuwen gebruiken

Als u automatisch vernieuwen wilt gebruiken, start u met een Spring boot-app die gebruikmaakt van app-configuratie, zoals de app die u hebt gemaakt door de [Start Snelstartgids voor de configuratie van de lente](quickstart-java-spring-app.md)te volgen.

Open vervolgens het bestand *pom. XML* in een tekst editor en voeg een `<dependency>` toe voor `spring-cloud-azure-appconfiguration-config-web`.

**Lente Cloud 1.1. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Lente Cloud 1.2. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Sla het bestand op en voer de toepassing daarna op de gebruikelijke manier uit.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u uw Spring boot-app ingeschakeld om configuratie-instellingen dynamisch te vernieuwen vanuit de configuratie van de app. Ga verder met de volgende zelf studie als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot de app-configuratie te stroom lijnen.

> [!div class="nextstepaction"]
> [Beheerde identiteits integratie](./howto-integrate-azure-managed-service-identity.md)
