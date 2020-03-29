---
title: 'Verificatie door eindgebruikers: Java met Azure Data Lake Storage Gen1 met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het bereiken van verificatie door eindgebruikers met Azure Data Lake Storage Gen1 met Azure Active Directory met Java
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8b558fca964f33d47d331e007329d1bae2626877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878098"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Verificatie van eindgebruikers met Azure Data Lake Storage Gen1 met Java
> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK gebruiken](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-end-user-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

In dit artikel vindt u meer informatie over het gebruik van de Java SDK om verificatie van eindgebruikers uit te brengen met Azure Data Lake Storage Gen1. Zie [Service-to-service-verificatie met Data Lake](data-lake-store-service-to-service-authenticate-java.md)Storage Gen1 met Java SDK met Data Lake Storage Gen1 met Java.

## <a name="prerequisites"></a>Vereisten
* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Maak een Azure Active Directory 'Native'-toepassing.** U moet de stappen in [verificatie van eindgebruikers met Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md)hebben voltooid met Azure Active Directory .

* [Maven.](https://maven.apache.org/install.html) In deze zelfstudie wordt Maven gebruikt voor build- en projectafhankelijkheden. Hoewel het mogelijk is te ontwikkelen zonder een buildsysteem als Maven of Gradle, maken deze systemen het veel eenvoudiger om afhankelijkheden te beheren.

* (Optioneel) Een IDE zoals [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) of vergelijkbaar.

## <a name="end-user-authentication"></a>Verificatie van de eindgebruiker
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
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Gebruik het volgende fragment in uw Java-toepassing om token te verkrijgen `DeviceCodeTokenProvider`voor de Active Directory-native toepassing die u eerder hebt gemaakt met behulp van de . Vervang **FILL-IN-HERE** door de werkelijke waarden voor de native Azure Directory-toepassing.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

De Data Lake Storage Gen1 SDK biedt handige methoden waarmee u de beveiligingstokens beheren die nodig zijn om met het Data Lake Storage Gen1-account te praten. Dit zijn echter niet de enige methoden die met SDK kunnen worden gebruikt. U kunt elke andere methode voor het verkrijgen van een token gebruiken. Zo kunt u de [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) gebruiken, of uw persoonlijke code.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u verificatie van eindgebruikers gebruiken om te verifiëren met Azure Data Lake Storage Gen1 met Java SDK. U nu de volgende artikelen bekijken waarin wordt gesproken over hoe u de Java SDK gebruiken om te werken met Azure Data Lake Storage Gen1.

* [Gegevensbewerkingen op Data Lake Storage Gen1 met Java SDK](data-lake-store-get-started-java-sdk.md)


