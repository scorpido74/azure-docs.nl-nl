---
title: Wat is Azure Data Lake Storage Gen1? | Microsoft Docs
description: Overzicht van Data Lake Storage Gen1 (voorheen bekend als Azure Data Lake Store) en de waarde die het biedt over andere gegevens archieven
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: overview
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 2ca6ceb326a1fd6f7aaf5a9871d043377e60de83
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85508551"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Wat is Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 is een bedrijfsbrede opslag ruimte voor big data analytische werk belastingen. Met Azure Data Lake kunt u gegevens van elke grootte, type en opnamesnelheid vastleggen op één enkele locatie voor operationele en experimentele analyses.

Data Lake Storage Gen1 kan worden geopend vanuit Hadoop (beschikbaar met HDInsight-cluster) met behulp van de WebHDFS-compatibele REST-Api's. Het is ontworpen om analyses in te scha kelen voor de opgeslagen gegevens en is afgestemd op de prestaties van scenario's voor gegevens analyse. Data Lake Storage Gen1 omvat alle mogelijkheden op bedrijfs niveau: beveiliging, beheer baarheid, schaalbaarheid, betrouw baarheid en beschik baarheid.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Enkele van de belangrijkste mogelijkheden van Data Lake Storage Gen1 zijn:

### <a name="built-for-hadoop"></a>Gebouwd voor Hadoop

Data Lake Storage Gen1 is een Apache Hadoop bestands systeem dat compatibel is met Hadoop Distributed File System (HDFS) en werkt met het Hadoop-ecosysteem. Uw bestaande HDInsight-toepassingen of-services die gebruikmaken van de WebHDFS-API kunnen eenvoudig worden geïntegreerd met Data Lake Storage Gen1. Data Lake Storage Gen1 biedt ook een WebHDFS-compatibele REST-interface voor toepassingen.

U kunt eenvoudig gegevens die zijn opgeslagen in Data Lake Storage Gen1 analyseren met behulp van Hadoop analytische frameworks zoals MapReduce of Hive. U kunt Azure HDInsight-clusters inrichten en deze configureren voor directe toegang tot gegevens die zijn opgeslagen in Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Onbeperkte opslag, bestanden ter grootte van petabytes

Data Lake Storage Gen1 biedt onbeperkte opslag en kan allerlei gegevens voor analyse opslaan. Er gelden geen limieten voor account grootten, bestands grootten of de hoeveelheid gegevens die kan worden opgeslagen in een Data Lake. Afzonderlijke bestanden kunnen variëren van kilo byte tot PETA bytes. Gegevens worden opgeslagen blijvend door meerdere kopieën te maken. Er geldt geen limiet voor de tijds duur waarin de gegevens kunnen worden opgeslagen in de data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Prestaties zijn afgestemd op big data-analyses

Data Lake Storage Gen1 is gebouwd voor het uitvoeren van grootschalige analytische systemen die een enorme door voer nodig hebben om grote hoeveel heden gegevens te doorzoeken en te analyseren. De Data Lake verspreidt delen van een bestand over een aantal afzonderlijke opslagservers. Hiermee verbetert u de doorvoer wanneer het bestand in parallel wordt gelezen voor het uitvoeren van gegevensanalyse.

### <a name="enterprise-ready-highly-available-and-secure"></a>Klaar voor de onderneming: Maxi maal beschikbaar en veilig

Data Lake Storage Gen1 biedt een industrie standaard Beschik baarheid en betrouw baarheid. Uw gegevensassets worden blijvend opgeslagen door het maken van redundante exemplaren ter bescherming tegen onverwachte fouten.

Data Lake Storage Gen1 biedt ook beveiliging op ondernemings niveau voor de opgeslagen gegevens. Zie [gegevens beveiligen in azure data Lake Storage gen1](#DataLakeStoreSecurity)voor meer informatie.

### <a name="all-data"></a>Alle gegevens

Data Lake Storage Gen1 kunnen alle gegevens in de oorspronkelijke indeling opslaan zonder dat hiervoor eerdere trans formaties zijn vereist. Data Lake Storage Gen1 vereist geen schema dat moet worden gedefinieerd voordat de gegevens worden geladen, zodat het kan worden gebruikt om de gegevens te interpreteren en een schema te definiëren op het moment van de analyse. De mogelijkheid om bestanden met een wille keurige grootte en indeling op te slaan, maakt het mogelijk om met Data Lake Storage Gen1 gestructureerde, semi-gestructureerde en ongestructureerde gegevens te verwerken.

Data Lake Storage Gen1 containers voor gegevens zijn in wezen mappen en bestanden. U werkt met de opgeslagen gegevens met behulp van Sdk's, het Azure Portal en Azure Power shell. Als u uw gegevens in het archief plaatst met behulp van deze interfaces en de juiste containers gebruikt, kunt u elk type gegevens opslaan. Data Lake Storage Gen1 voert geen speciale verwerking van gegevens uit op basis van het type gegevens dat wordt opgeslagen.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Gegevens beveiligen

Data Lake Storage Gen1 maakt gebruik van Azure Active Directory (Azure AD) voor verificatie en toegangs beheer lijsten (Acl's) voor het beheren van de toegang tot uw gegevens.

| Functie | Beschrijving |
| --- | --- |
| Verificatie |Data Lake Storage Gen1 integreert met Azure AD voor identiteits-en toegangs beheer voor alle gegevens die zijn opgeslagen in Data Lake Storage Gen1. Vanwege de integratie Data Lake Storage Gen1 voor delen van alle functies van Azure AD, zoals multi-factor Authentication, voorwaardelijke toegang, op rollen gebaseerd toegangs beheer, bewaking van toepassings gebruik, beveiligings bewaking en waarschuwingen, enzovoort. Data Lake Storage Gen1 ondersteunt het OAuth 2,0-protocol voor verificatie binnen de REST-interface. Zie [Data Lake Storage gen1-verificatie](data-lakes-store-authentication-using-azure-active-directory.md).|
| Toegangsbeheer |Data Lake Storage Gen1 biedt toegangs beheer door het ondersteunen van POSIX-stijl machtigingen die beschikbaar worden gesteld door het WebHDFS-protocol. U kunt Acl's inschakelen voor de hoofdmap, submappen en afzonderlijke bestanden. Zie [toegangs beheer in data Lake Storage gen1](data-lake-store-access-control.md)voor meer informatie over de werking van acl's in de context van data Lake Storage gen1. |
| Versleuteling |Data Lake Storage Gen1 biedt ook versleuteling voor gegevens die in het account zijn opgeslagen. U geeft de versleutelings instellingen op tijdens het maken van een Data Lake Storage Gen1-account. U kunt ervoor kiezen om uw gegevens te versleutelen of te kiezen voor geen versleuteling. Zie [versleuteling in data Lake Storage gen1](data-lake-store-encryption.md)voor meer informatie. Zie [aan de slag met data Lake Storage gen1 met behulp van de Azure Portal](data-lake-store-get-started-portal.md)voor instructies over het bieden van versleutelings configuratie. |

Zie [gegevens beveiligen in azure data Lake Storage gen1](data-lake-store-secure-data.md)voor instructies voor het beveiligen van gegevens in data Lake Storage gen1.

## <a name="application-compatibility"></a>Compatibiliteit van toepassingen

Data Lake Storage Gen1 is compatibel met de meeste open source-onderdelen in het Hadoop-ecosysteem. Het integreert ook goed met andere Azure-Services. Gebruik de volgende koppelingen voor meer informatie over hoe u Data Lake Storage Gen1 kunt gebruiken met open source-onderdelen en andere Azure-Services:

- Zie [toepassingen en services die compatibel zijn met Azure data Lake Storage gen1](data-lake-store-compatible-oss-other-applications.md) voor een lijst van open-source toepassingen die interoperabel zijn met data Lake Storage gen1.
- Zie [integreren met andere Azure-Services](data-lake-store-integrate-with-other-services.md) om inzicht te krijgen in het gebruik van data Lake Storage gen1 met andere Azure-Services om een breed scala aan scenario's mogelijk te maken.
- Bekijk [scenario's voor](data-lake-store-data-scenarios.md) het gebruik van data Lake Storage gen1 om te leren hoe u data Lake Storage gen1 gebruikt in scenario's zoals het opnemen van gegevens, het verwerken van gegevens, het downloaden van gegevens en het visualiseren van gegevens.

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1 bestands systeem

U kunt toegang krijgen tot Data Lake Storage Gen1 via het bestands systeem AzureDataLakeFilesystem (adl://) in Hadoop-omgevingen (beschikbaar met HDInsight-cluster). Toepassingen en services die gebruikmaken van adl://kunnen profiteren van verdere prestatie optimalisaties die momenteel niet beschikbaar zijn in WebHDFS. Als gevolg hiervan biedt Data Lake Storage Gen1 u de flexibiliteit om gebruik te maken van de beste prestaties met de aanbevolen optie voor het gebruik van adl://of het onderhouden van bestaande code door de WebHDFS-API direct te blijven gebruiken. Azure HDInsight maakt volledig gebruik van de AzureDataLakeFilesystem om de beste prestaties op Data Lake Storage Gen1 te bieden.

U kunt toegang krijgen tot uw gegevens in Data Lake Storage Gen1 met behulp van `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net` . Zie [Eigenschappen van de opgeslagen gegevens weer geven](data-lake-store-get-started-portal.md#properties)voor meer informatie over het openen van de gegevens in data Lake Storage gen1.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met Data Lake Storage Gen1 met behulp van de Azure Portal](data-lake-store-get-started-portal.md)
- [Aan de slag met Data Lake Storage Gen1 met behulp van .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)