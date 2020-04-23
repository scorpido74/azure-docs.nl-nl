---
title: Azure Data Lake Storage Gen1 overzicht in HDInsight
description: Overzicht van Data Lake Storage Gen1 in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 15d7f0621ffbf883d267d389fb634a13aa927430
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873353"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>Azure Data Lake Storage Gen1 overzicht in HDInsight

Azure Data Lake Storage Gen1 is een bedrijfsbrede hyperscale repository voor big data analytische workloads. Met Azure Data Lake u gegevens vastleggen van elke grootte, type en innamesnelheid. En op één plek voor operationele en verkennende analyses.

Toegang tot Data Lake Storage Gen1 vanuit Hadoop (beschikbaar met een HDInsight-cluster) met behulp van de WebHDFS-compatibele REST API's. Data Lake Storage Gen1 is ontworpen om analyses op de opgeslagen gegevens mogelijk te maken en is afgestemd op prestaties in scenario's voor gegevensanalyse. Gen1 bevat de mogelijkheden die essentieel zijn voor gebruikscases voor bedrijven in de echte wereld. Deze mogelijkheden omvatten beveiliging, beheerbaarheid, aanpassingsvermogen, betrouwbaarheid en beschikbaarheid.

Zie het gedetailleerde overzicht van Azure Data [Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)voor meer informatie over Azure Data Lake Storage Gen1.

De belangrijkste mogelijkheden van Data Lake Storage Gen1 zijn het volgende.

## <a name="compatibility-with-hadoop"></a>Compatibiliteit met Hadoop

Data Lake Storage Gen1 is een Apache Hadoop bestandssysteem compatibel met HDFS en Hadoop omgeving.  HDInsight-toepassingen of -services die gebruik maken van de WebHDFS API kunnen eenvoudig worden geïntegreerd met Data Lake Storage Gen1. Data Lake Storage Gen1 onthult ook een WebHDFS-compatibele REST-interface voor toepassingen.

Gegevens die zijn opgeslagen in Data Lake Storage Gen1 kunnen eenvoudig worden geanalyseerd met behulp van Hadoop analytische frameworks. Frameworks zoals MapReduce of Hive. Azure HDInsight-clusters kunnen worden ingericht en geconfigureerd om rechtstreeks toegang te krijgen tot gegevens die zijn opgeslagen in Data Lake Storage Gen1.

## <a name="unlimited-storage-petabyte-files"></a>Onbeperkte opslag, bestanden ter grootte van petabytes

Data Lake Storage Gen1 biedt onbeperkte opslag en is geschikt voor het opslaan van verschillende soorten gegevens voor analytics. Het legt geen beperkingen op aan accountgroottes of bestandsgroottes. Of de hoeveelheid gegevens die in een datalake kunnen worden opgeslagen. Afzonderlijke bestanden variëren in grootte van kilobytes tot petabytes, waardoor Data Lake Storage Gen1 een geweldige keuze is om elk type gegevens op te slaan. Gegevens worden blijvend opgeslagen door meerdere kopieën te maken. En er zijn geen grenzen aan hoe lang de gegevens kunnen worden opgeslagen in het datalake.

## <a name="performance-tuning-for-big-data-analytics"></a>Prestatieafstemming voor big data-analyses

Data Lake Storage Gen1 is ontworpen voor analytische systemen. Systemen die enorme doorvoer vereisen om grote hoeveelheden gegevens op te vragen en te analyseren. Het datalake verspreidt delen van een bestand over verschillende afzonderlijke opslagservers. Wanneer u gegevens analyseert, verbetert deze instelling de leesdoorvoer wanneer het bestand parallel wordt gelezen.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Gereedheid voor ondernemingen: zeer beschikbaar en veilig

Data Lake Storage Gen1 biedt standaard beschikbaarheid en betrouwbaarheid. Gegevensactiva worden blijvend opgeslagen: overbodige kopieën beschermen tegen onverwachte storingen. Ondernemingen kunnen Data Lake Storage Gen1 in hun oplossingen gebruiken als een belangrijk onderdeel van hun bestaande dataplatform.

Data Lake Storage Gen1 biedt ook beveiliging op bedrijfsniveau voor opgeslagen gegevens. Zie [Gegevens beveiligen in Azure Data Lake Storage Gen1](#data-security-in-data-lake-storage-gen1)voor meer informatie.

## <a name="flexible-data-structures"></a>Flexibele gegevensstructuren

Data Lake Storage Gen1 kan alle gegevens opslaan in zijn oorspronkelijke formaat, zoals het is, zonder voorafgaande transformaties. Data Lake Storage Gen1 vereist geen schema dat moet worden gedefinieerd voordat de gegevens worden geladen. Het individuele analytische kader interpreteert de gegevens en definieert een schema op het moment van de analyse. Data Lake Storage Gen1 kan gestructureerde gegevens verwerken. En semigestructureerde en ongestructureerde gegevens.

Data Lake Storage Gen1 containers voor gegevens zijn in wezen mappen en bestanden. U werkt op de opgeslagen gegevens met behulp van SDKs, de Azure-portal en Azure PowerShell. Gegevens die met deze interfaces en containers in de winkel worden geplaatst, kunnen elk gegevenstype opslaan. Data Lake Storage Gen1 verwerkt geen speciale gegevens op basis van het type gegevens.

## <a name="data-security-in-data-lake-storage-gen1"></a>Gegevensbeveiliging in Data Lake Storage Gen1

Data Lake Storage Gen1 gebruikt Azure Active Directory voor verificatie en gebruikt toegangscontrolelijsten (ACL's) om de toegang tot uw gegevens te beheren.

| **Functie** | **Beschrijving** |
| --- | --- |
| Verificatie |Data Lake Storage Gen1 integreert met Azure Active Directory (Azure AD) voor identiteits- en toegangsbeheer voor alle gegevens die zijn opgeslagen in Data Lake Storage Gen1. Dankzij de integratie profiteert Data Lake Storage Gen1 van alle Azure AD-functies. Deze functies omvatten: multifactorauthenticatie, voorwaardelijke toegang en op rollen gebaseerd toegangsbeheer. Ook het gebruik van toepassingen monitoring, security monitoring en waarschuwingen, enzovoort. Data Lake Storage Gen1 ondersteunt het OAuth 2.0-protocol voor verificatie binnen de REST-interface. Zie [Verificatie in Azure Data Lake Storage Gen1 met Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Toegangsbeheer |Data Lake Storage Gen1 biedt toegangscontrole door posix-achtige machtigingen te ondersteunen die worden blootgesteld door het WebHDFS-protocol. ACL's kunnen worden ingeschakeld voor de hoofdmap, submappen en afzonderlijke bestanden. Zie [Toegangscontrole in Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md)voor meer informatie over hoe ACL's werken in de context van Data Lake Storage Gen1. |
| Versleuteling |Data Lake Storage Gen1 biedt ook versleuteling voor gegevens die in het account zijn opgeslagen. U geeft de versleutelingsinstellingen op tijdens het maken van een Data Lake Storage Gen1-account. U ervoor kiezen om uw gegevens te versleutelen of te kiezen voor geen versleuteling. Zie [Versleuteling in Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md)voor meer informatie. Zie [Aan de slag met Azure Data Lake Storage Gen1 met behulp van de Azure-portal](../data-lake-store/data-lake-store-get-started-portal.md)voor instructies over het bieden van een versleutelingsgerelateerde configuratie. |

Zie Gegevens beveiligen die zijn opgeslagen in [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md)voor meer informatie over het beveiligen van gegevens in Data Lake Storage Gen1.

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Toepassingen die compatibel zijn met Data Lake Storage Gen1

Data Lake Storage Gen1 is compatibel met de meeste open-source componenten in de Hadoop-omgeving. Het kan ook goed worden geïntegreerd in andere Azure-services.  Volg de onderstaande links voor meer informatie over hoe Data Lake Storage Gen1 kan worden gebruikt, zowel met open-source componenten als andere Azure-services.

* Zie [Open-source big data-toepassingen die werken met Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md).
* Zie [Azure Data Lake Storage Gen1 integreren met andere Azure-services](../data-lake-store/data-lake-store-integrate-with-other-services.md) om te begrijpen hoe u Data Lake Storage Gen1 gebruiken met andere Azure-services om een breder scala aan scenario's mogelijk te maken.
* Zie [Azure Data Lake Storage Gen1 gebruiken voor vereisten voor big data.](../data-lake-store/data-lake-store-data-scenarios.md)

## <a name="data-lake-storage-gen1-file-system-adl"></a>Gegevens lake storage Gen1-bestandssysteem (adl://)

In Hadoop-omgevingen hebt u toegang tot Data Lake Storage Gen1 via het nieuwe bestandssysteem, het AzureDataLakeFilesystem (adl://). De prestaties van toepassingen `adl://` en services die worden gebruikt, kunnen worden geoptimaliseerd op manieren die momenteel niet beschikbaar zijn in WebHDFS. Als gevolg hiervan krijgt u de flexibiliteit om ofwel gebruik te maken van de beste prestaties met behulp van de aanbevolen adl://. Of onderhoud bestaande code door de WebHDFS API rechtstreeks te blijven gebruiken. Azure HDInsight maakt optimaal gebruik van het AzureDataLakeFilesystem om de beste prestaties te leveren op Data Lake Storage Gen1.

Krijg toegang tot uw gegevens in Data Lake Storage Gen1 met de volgende URI:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Zie [Acties die beschikbaar zijn op de opgeslagen gegevens](../data-lake-store/data-lake-store-get-started-portal.md#properties)voor meer informatie over hoe u toegang krijgt tot de gegevens in Data Lake Storage Gen1.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Kennismaking met Azure Storage](../storage/common/storage-introduction.md)
* [Overzicht van Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)