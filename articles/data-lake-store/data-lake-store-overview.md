---
title: Wat is Azure Data Lake Storage Gen1? | Microsoft Docs
description: Overzicht van Data Lake Storage Gen1 (voorheen bekend als Azure Data Lake Store) en de waarde die het biedt ten opzichte van andere gegevensopslag
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 99384374226fd89cfd672c6b4f851a1743db0764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67118806"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Wat is Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 is een bedrijfsbrede hyper-scale repository voor big data analytische workloads. Met Azure Data Lake kunt u gegevens van elke grootte, type en opnamesnelheid vastleggen op één enkele locatie voor operationele en experimentele analyses.

Data Lake Storage Gen1 is toegankelijk vanaf Hadoop (beschikbaar met HDInsight-cluster) met behulp van de WebHDFS-compatibele REST API's. Het is ontworpen om analyses op de opgeslagen gegevens mogelijk te maken en is afgestemd op de prestaties voor scenario's voor gegevensanalyse. Data Lake Storage Gen1 bevat alle mogelijkheden op bedrijfsniveau: beveiliging, beheerbaarheid, schaalbaarheid, betrouwbaarheid en beschikbaarheid.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Enkele van de belangrijkste mogelijkheden van Data Lake Storage Gen1 zijn de volgende.

### <a name="built-for-hadoop"></a>Gebouwd voor Hadoop

Data Lake Storage Gen1 is een Apache Hadoop-bestandssysteem dat compatibel is met Hadoop Distributed File System (HDFS) en werkt met het Hadoop-ecosysteem. Uw bestaande HDInsight-toepassingen of -services die gebruik maken van de WebHDFS API kunnen eenvoudig worden geïntegreerd met Data Lake Storage Gen1. Data Lake Storage Gen1 onthult ook een WebHDFS-compatibele REST-interface voor toepassingen.

U eenvoudig gegevens analyseren die zijn opgeslagen in Data Lake Storage Gen1 met behulp van Hadoop analytische frameworks zoals MapReduce of Hive. U Azure HDInsight-clusters inrichten en deze configureren om rechtstreeks toegang te krijgen tot gegevens die zijn opgeslagen in Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Onbeperkte opslag, bestanden ter grootte van petabytes

Data Lake Storage Gen1 biedt onbeperkte opslag en kan een verscheidenheid aan gegevens opslaan voor analyse. Het legt geen beperkingen op aan accountgroottes, bestandsgroottes of de hoeveelheid gegevens die in een gegevensmeer kunnen worden opgeslagen. Individuele bestanden kunnen variëren van kilobyte tot petabytes in grootte. Gegevens worden blijvend opgeslagen door meerdere kopieën te maken. Er is geen limiet aan de duur van de tijd waarvoor de gegevens kunnen worden opgeslagen in het gegevensmeer.

### <a name="performance-tuned-for-big-data-analytics"></a>Prestaties zijn afgestemd op big data-analyses

Data Lake Storage Gen1 is gebouwd voor het uitvoeren van grootschalige analytische systemen die een enorme doorvoer vereisen om grote hoeveelheden gegevens op te vragen en te analyseren. De Data Lake verspreidt delen van een bestand over een aantal afzonderlijke opslagservers. Hiermee verbetert u de doorvoer wanneer het bestand in parallel wordt gelezen voor het uitvoeren van gegevensanalyse.

### <a name="enterprise-ready-highly-available-and-secure"></a>Enterprise ready: zeer beschikbaar en veilig

Data Lake Storage Gen1 biedt standaard beschikbaarheid en betrouwbaarheid. Uw gegevensassets worden blijvend opgeslagen door het maken van redundante exemplaren ter bescherming tegen onverwachte fouten.

Data Lake Storage Gen1 biedt ook bedrijfsbeveiliging voor de opgeslagen gegevens. Zie [Gegevens beveiligen in Azure Data Lake Storage Gen1](#DataLakeStoreSecurity)voor meer informatie.

### <a name="all-data"></a>Alle gegevens

Data Lake Storage Gen1 kan alle gegevens opslaan in zijn oorspronkelijke formaat, zonder voorafgaande transformaties. Data Lake Storage Gen1 vereist geen schema dat moet worden gedefinieerd voordat de gegevens worden geladen, zodat het aan het individuele analytische framework wordt overgehouden om de gegevens te interpreteren en een schema te definiëren op het moment van de analyse. De mogelijkheid om bestanden van willekeurige groottes en formaten op te slaan, maakt het mogelijk voor Data Lake Storage Gen1 om gestructureerde, semi-gestructureerde en ongestructureerde gegevens te verwerken.

Data Lake Storage Gen1 containers voor gegevens zijn in wezen mappen en bestanden. U werkt op de opgeslagen gegevens met Behulp van SDKs, de Azure-portal en Azure Powershell. Als u uw gegevens met behulp van deze interfaces in de winkel plaatst en de juiste containers gebruikt, u elk type gegevens opslaan. Data Lake Storage Gen1 voert geen speciale verwerking van gegevens uit op basis van het type gegevens dat het opslaat.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Gegevens beveiligen

Data Lake Storage Gen1 gebruikt Azure Active Directory (Azure AD) voor verificatie en toegangscontrolelijsten (ACL's) om toegang tot uw gegevens te beheren.

| Functie | Beschrijving |
| --- | --- |
| Authentication |Data Lake Storage Gen1 integreert met Azure AD voor identiteits- en toegangsbeheer voor alle gegevens die zijn opgeslagen in Data Lake Storage Gen1. Vanwege de integratie profiteert Data Lake Storage Gen1 van alle Azure AD-functies, zoals multi-factor authenticatie, voorwaardelijke toegang, op rollen gebaseerdtoegangscontrole, bewaking van toepassingsgebruik, beveiligingsbewaking en waarschuwingen, enzovoort. Data Lake Storage Gen1 ondersteunt het OAuth 2.0-protocol voor verificatie binnen de REST-interface. Zie [Verificatie datalakestorage Gen1](data-lakes-store-authentication-using-azure-active-directory.md).|
| Toegangsbeheer |Data Lake Storage Gen1 biedt toegangscontrole door posix-achtige machtigingen te ondersteunen die worden blootgesteld door het WebHDFS-protocol. U ACL's inschakelen in de hoofdmap, op submappen en op afzonderlijke bestanden. Zie [Toegangscontrole in Data Lake Storage Gen1](data-lake-store-access-control.md)voor meer informatie over hoe ACL's werken in de context van Data Lake Storage Gen1. |
| Versleuteling |Data Lake Storage Gen1 biedt ook versleuteling voor gegevens die in het account zijn opgeslagen. U geeft de versleutelingsinstellingen op tijdens het maken van een Data Lake Storage Gen1-account. U ervoor kiezen om uw gegevens te versleutelen of te kiezen voor geen versleuteling. Zie [Versleuteling in Data Lake Storage Gen1](data-lake-store-encryption.md)voor meer informatie. Zie Aan de slag met Data [Lake Storage Gen1 met behulp van de Azure-portal](data-lake-store-get-started-portal.md)voor instructies over het bieden van versleutelingsgerelateerde configuratie. |

Zie Gegevens beveiligen in Azure Data Lake Storage Gen1 voor instructies over het beveiligen van gegevens in Data Lake Storage [Gen1.](data-lake-store-secure-data.md)

## <a name="application-compatibility"></a>Compatibiliteit van toepassingen

Data Lake Storage Gen1 is compatibel met de meeste open-source componenten in het Hadoop-ecosysteem. Het integreert ook goed met andere Azure-services. Als u meer wilt weten over hoe u Data Lake Storage Gen1 gebruiken met opensourcecomponenten en andere Azure-services, gebruikt u de volgende koppelingen:

- Zie [Toepassingen en services die compatibel zijn met Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) voor een lijst met open-source toepassingen die interoperabel zijn met Data Lake Storage Gen1.
- Zie [Integratie met andere Azure-services](data-lake-store-integrate-with-other-services.md) om te begrijpen hoe u Data Lake Storage Gen1 gebruiken met andere Azure-services om een breder scala aan scenario's mogelijk te maken.
- Zie [Scenario's voor het gebruik van Data Lake Storage Gen1](data-lake-store-data-scenarios.md) voor meer informatie over het gebruik van Data Lake Storage Gen1 in scenario's zoals het innemen van gegevens, het verwerken van gegevens, het downloaden van gegevens en het visualiseren van gegevens.

## <a name="data-lake-storage-gen1-file-system"></a>Gegevens Lake Storage Gen1-bestandssysteem

Data Lake Storage Gen1 is toegankelijk via het bestandssysteem AzureDataLakeFilesystem (adl://) in Hadoop-omgevingen (beschikbaar met HDInsight-cluster). Toepassingen en services die gebruik maken van adl:// kunnen profiteren van verdere prestatieoptimalisaties die momenteel niet beschikbaar zijn in WebHDFS. Als gevolg hiervan biedt Data Lake Storage Gen1 u de flexibiliteit om gebruik te maken van de beste prestaties met de aanbevolen optie om adl:// te gebruiken of bestaande code te onderhouden door de WebHDFS API rechtstreeks te blijven gebruiken. Azure HDInsight maakt volledig gebruik van het AzureDataLakeFilesystem om de beste prestaties te leveren op Data Lake Storage Gen1.

U hebt toegang tot uw gegevens `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`in Data Lake Storage Gen1 via . Zie [Eigenschappen van de opgeslagen gegevens weergeven](data-lake-store-get-started-portal.md#properties)voor meer informatie over hoe u toegang krijgt tot de gegevens in Data Lake Storage Gen1.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met Data Lake Storage Gen1 met de Azure-portal](data-lake-store-get-started-portal.md)
- [Aan de slag met Data Lake Storage Gen1 met .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)