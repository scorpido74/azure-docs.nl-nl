---
title: Verbinding maken met Azure Media Services v3 API - Java
description: In dit artikel wordt beschreven hoe u verbinding maken met De V3 API van Azure Media Services met Java.
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
ms.openlocfilehash: 6b0f21c3fa7a9c827f7201f4b899a33ea77eaf08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888492"
---
# <a name="connect-to-media-services-v3-api---java"></a>Verbinding maken met Media Services v3 API - Java

In dit artikel ziet u hoe u verbinding maken met de Azure Media Services v3 Java SDK met behulp van de aanmeldingsmethode voor serviceprincipal.

In dit artikel wordt de Visual Studio Code gebruikt om de voorbeeld-app te ontwikkelen.

## <a name="prerequisites"></a>Vereisten

- Volg [Writing Java with Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) om te installeren:

   - JDK
   - Apache Maven
   - Java-uitbreidingspakket
- Zorg ervoor `JAVA_HOME` dat `PATH` u in- en omgevingsvariabelen stelt.
- [Een Azure Media Services-account maken](create-account-cli-how-to.md). Zorg ervoor dat u de naam van de brongroep en de naam van het Media Services-account onthoudt.
- Volg de stappen in het onderwerp [Access API's.](access-api-cli-how-to.md) Neem de abonnements-ID, toepassings-ID (client-ID), de verificatiesleutel (geheim) en de tenant-id op die u in een latere stap nodig hebt.

Ook review:

- [Java in Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Java Project Management in VS-code](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> [Naamgevingsconventies bekijken](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Een Maven-project maken

Open een opdrachtregelgereedschap `cd` en een map waar u het project wilt maken.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Wanneer u de opdracht `pom.xml` `App.java`uitvoert, worden de , , en andere bestanden gemaakt. 

## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

1. Open in Visual Studio Code de map waar uw project zich bevindt
1. Zoek en open de`pom.xml`
1. De benodigde afhankelijkheden toevoegen

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

1. Open `App.java` het `src\main\java\com\azure\ams` bestand onder en zorg ervoor dat uw pakket bovenaan is opgenomen:

    ```java
    package com.azure.ams;
    ```
1. Voeg onder de pakketinstructie de volgende importinstructies toe:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Als u de Active Directory-referenties wilt maken die u nodig hebt om aanvragen in te dienen, voegt u de volgende code toe aan de hoofdmethode van de klasse App en stelt u de waarden in die u hebt gekregen van [Toegangs-API's:](access-api-cli-how-to.md)
   
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

- [Concepten van Media Services](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java-referentie](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Volgende stappen

U `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` entiteiten nu opnemen en beginnen te manipuleren.

Zie de [repo van Java SDK-voorbeelden](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) voor meer codevoorbeelden.
