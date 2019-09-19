---
title: Verbinding maken met Azure Media Services v3 API-Java
description: Meer informatie over het maken van verbinding met Media Services v3 API met Java.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: 3f5e45bb84ca4fc46ccf1f3f3ab86d43c7c03cab
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122877"
---
# <a name="connect-to-media-services-v3-api---java"></a>Verbinding maken met Media Services v3 API-Java

Dit artikel laat u zien hoe u verbinding kunt maken met de Azure Media Services v3 Java SDK met behulp van de aanmeldings methode voor de Service-Principal.

In dit artikel wordt de Visual Studio-code gebruikt voor het ontwikkelen van de voor beeld-app.

## <a name="prerequisites"></a>Vereisten

- Volg het [schrijven van Java met Visual Studio code](https://code.visualstudio.com/docs/java/java-tutorial) om te installeren:

   - JDK
   - Apache Maven
   - Java-uitbreidings pakket
- Zorg ervoor dat u `JAVA_HOME` - `PATH` en omgevings variabelen hebt ingesteld.
- [Een Azure Media Services-account maken](create-account-cli-how-to.md). Zorg ervoor dat u de naam van de resource groep en de naam van het Media Services account vergeet.
- Volg de stappen in het onderwerp [toegangs-api's](access-api-cli-how-to.md) . Noteer de abonnements-ID, toepassings-ID (client-ID), de verificatie sleutel (geheim) en de Tenant-ID die u in een latere stap nodig hebt.

Ook controleren:

- [Java in Visual Studio code](https://code.visualstudio.com/docs/languages/java)
- [Java-project beheer in VS code](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Bekijk [naam conventies](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Een Maven-project maken

Open een opdracht regel programma en `cd` naar een map waar u het project wilt maken.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Wanneer u de opdracht uitvoert, worden `pom.xml`de `App.java`-, en andere bestanden gemaakt. 

## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

1. Open in Visual Studio code de map waar uw project zich bevindt
1. Zoek en open de`pom.xml`
1. De vereiste afhankelijkheden toevoegen

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Verbinding maken met de Java-client

1. Open het `App.java` bestand onder `src\main\java\com\azure\ams` en controleer of het pakket aan de bovenkant is opgenomen:

    ```java
    package com.azure.ams;
    ```
1. Voeg onder de instructie package de volgende import instructies toe:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Als u de Active Directory referenties wilt maken die u nodig hebt om aanvragen te doen, voegt u de volgende code toe aan de methode Main van de app-klasse en stelt u de waarden in die u hebt ontvangen van [Access-api's](access-api-cli-how-to.md):
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Voer de app uit.

## <a name="see-also"></a>Zie ook

- [Media Services concepten](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Naslaginformatie over Java](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Volgende stappen

U kunt nu entiteiten `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` toevoegen en bewerken.

Zie de [Java SDK](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) -voor beelden opslag plaats voor meer code voorbeelden.
