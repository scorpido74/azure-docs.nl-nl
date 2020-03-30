---
title: Service-to-service authenticatie - Data Lake Storage Gen1 – Java SDK
description: Meer informatie over het implementeren van service-to-service met Azure Data Lake Storage Gen1 met Azure Active Directory met Java
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f355da7cd9c035b4ed0845bbd374a93bfb4a7350
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904543"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Service-to-service-verificatie met Azure Data Lake Storage Gen1 met Java

> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK gebruiken](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-service-to-service-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

In dit artikel vindt u meer informatie over het gebruik van de Java SDK om service-to-service-verificatie uit te brengen met Azure Data Lake Storage Gen1. Verificatie van eindgebruikers met Data Lake Storage Gen1 met Java SDK wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Maak een Azure Active Directory "Web"-toepassing**. U moet de stappen in [service-to-service-verificatie met Data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md)hebben voltooid met Azure Active Directory .

* [Maven.](https://maven.apache.org/install.html) In deze zelfstudie wordt Maven gebruikt voor build- en projectafhankelijkheden. Hoewel het mogelijk is om te bouwen zonder gebruik te maken van een buildsysteem zoals Maven of Gradle, maken deze systemen het veel gemakkelijker om afhankelijkheden te beheren.

* (Optioneel) Een IDE zoals [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) of [Eclipse](https://www.eclipse.org/downloads/) of iets dergelijks.

## <a name="service-to-service-authentication"></a>Verificatie van service-tot-service

1. Maak een Maven-project met behulp van [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) vanaf de opdrachtregel of met behulp van een IDE. [Hier](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html) vindt u instructies over het maken van een Java-project met behulp van IntelliJ. [Hier](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm) vindt u instructies over het maken van een Java-project met behulp van Eclipse.

2. Voeg de volgende afhankelijkheden toe aan het **pom.xml**-bestand in Maven. Voeg het volgende fragment toe voor de ** \<tag /project>:**

        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>

    De eerste afhankelijkheid is het gebruik van de`azure-data-lake-store-sdk`Data Lake Storage Gen1 SDK ( ) van de maven repository. De tweede afhankelijkheid dient om op te geven welk framework voor logboekregistratie (`slf4j-nop`) voor deze toepassing moet worden gebruikt. De Data Lake Storage Gen1 SDK maakt gebruik van [slf4j](https://www.slf4j.org/) logging gevel, waarmee u kiezen uit een aantal populaire logging frameworks, zoals log4j, Java logging, logback, enz., of geen logging. In dit voorbeeld wordt logboekregistratie uitgeschakeld. Daarom wordt de binding **slf4j-nop** gebruikt. [Hier](https://www.slf4j.org/manual.html#projectDep) vindt u andere opties voor logboekregistratie voor uw toepassing.

3. Voeg de volgende importinstructies toe aan uw toepassing.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. Gebruik het volgende fragment in uw Java-toepassing om token te verkrijgen voor de Active `AccessTokenProvider` Directory-webtoepassing `ClientCredsTokenProvider`die u eerder hebt gemaakt met behulp van een van de subklassen van (in het volgende voorbeeld wordt het volgende voorbeeld gebruikt). De tokenprovider slaat de referenties die worden gebruikt voor het ophalen van het token, op in de cache en vernieuwt het token automatisch als het bijna is verlopen. Het is mogelijk om uw eigen `AccessTokenProvider` subklassen van dus tokens worden verkregen door uw klantcode te maken. Voor nu, laten we gewoon gebruik maken van de een die in de SDK.

    Vervang **FILL-IN-HERE** met de daadwerkelijke waarden voor de Azure Active Directory-webtoepassing.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

De Data Lake Storage Gen1 SDK biedt handige methoden waarmee u de beveiligingstokens beheren die nodig zijn om met het Data Lake Storage Gen1-account te praten. Dit zijn echter niet de enige methoden die met SDK kunnen worden gebruikt. U kunt elke andere methode voor het verkrijgen van een token gebruiken. Zo kunt u de [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) gebruiken, of uw persoonlijke code.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u verificatie van eindgebruikers gebruiken om te verifiëren met Data Lake Storage Gen1 met Java SDK. U nu kijken naar de volgende artikelen die praten over hoe u de Java SDK gebruiken om te werken met Data Lake Storage Gen1.

* [Gegevensbewerkingen op Data Lake Storage Gen1 met Java SDK](data-lake-store-get-started-java-sdk.md)
