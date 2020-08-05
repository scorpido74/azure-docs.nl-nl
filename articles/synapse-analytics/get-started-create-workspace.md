---
title: 'Zelfstudie: een Synapse-werkruimte maken'
description: In deze zelfstudie leert u hoe u een Synapse-werkruimte, een SQL-pool en een Apache Spark-pool maakt.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: b4d48dcc8f09ae8e2ec3bb198f8864de1c945682
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093539"
---
# <a name="create-a-synapse-workspace"></a>Een Synapse-werkruimte maken

In deze zelfstudie leert u hoe u een Synapse-werkruimte, een SQL-pool en een Apache Spark-pool maakt. 

## <a name="prepare-a-storage-account"></a>Een opslagaccount voorbereiden

1. Open de [Azure Portal](https://portal.azure.com).
1. Maak een nieuw opslagaccount met de volgende instellingen:

    |Tabblad|Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|---|
    |Basisbeginselen|**Naam van opslagaccount**| U kunt een willekeurige naam opgeven.| In dit document gebruiken we de naam **contosolake**.|
    |Basisbeginselen|**Type account**| **StorageV2** ||
    |Basisbeginselen|**Locatie**|Kies een willekeurige locatie.| We raden u aan uw Azure Synapse Analytics-werkruimte en Azure Data Lake Storage Gen2-account in dezelfde regio te bewaren.|
    |Geavanceerd|**Data Lake Storage Gen2**|**Ingeschakeld**| Azure Synapse werkt alleen met opslagaccounts waarvoor deze instelling is ingeschakeld.|
    |||||

1. Nadat u het opslagaccount hebt gemaakt, selecteert u **Toegangsbeheer (IAM)** in het linkerdeelvenster. Wijs vervolgens de volgende rollen toe of zorg ervoor dat deze al zijn toegewezen:
    * Wijs uzelf toe aan de rol **Eigenaar**.
    * Wijs uzelf toe aan de rol **Eigenaar van opslagblobgegevens**.
1. Selecteer in het navigatievenster links de optie **Containers** en maak een container.
1. U kunt de container een willekeurige naam geven. In dit document noemen we de container **users**.
1. Accepteer de standaardinstelling **Openbaar toegangsniveau** en selecteer vervolgens **Maken**.

In de volgende stap configureert u de Azure Synapse-werkruimte voor het gebruik van dit opslagaccount als primair opslagaccount en de container voor het opslaan van werkruimtegegevens. In de werkruimte worden gegevens opgeslagen in Apache Spark-tabellen. Het slaat Apache Spark-toepassingslogboeken op in een map met de naam **/synapse/workspacename**.

## <a name="create-a-synapse-workspace"></a>Een Synapse-werkruimte maken

1. Open de [Azure-portal](https://portal.azure.com) en zoek bovenin naar **Synapse**.
1. Selecteer in de zoekresultaten onder **Services** de optie **Azure Synapse Analytics (voorbeeld van werkruimten)** .
1. Selecteer **Toevoegen** om een werkruimte te maken met behulp van deze instellingen:

    |Tabblad|Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|---|
    |Basisbeginselen|**Werkruimtenaam**|U kunt deze werkruimte elke gewenste naam geven.| In dit document gebruiken we **myworkspace**.|
    |Basisbeginselen|**Regio**|Maak deze gelijk aan de regio van het opslagaccount.|

1. Selecteer onder **Data Lake Storage Gen 2 selecteren** het account en de container die u eerder hebt gemaakt.
1. Selecteer **Beoordelen en maken** > **Maken**. Uw werkruimte is binnen een paar minuten klaar.

## <a name="verify-access-to-the-storage-account"></a>De toegang tot het opslagaccount controleren

Beheerde identiteiten voor uw Azure Synapse-werkruimte hebben mogelijk al toegang tot het opslagaccount. Voer de volgende stappen uit om dat te controleren:

1. Open de [Azure-portal](https://portal.azure.com) en open het primaire opslagaccount dat u hebt gekozen voor uw werkruimte.
1. Selecteer **Toegangsbeheer (IAM)** in het linkerdeelvenster.
1. Wijs vervolgens de volgende rollen toe of controleer of ze al zijn toegewezen. We gebruiken dezelfde naam voor de werkruimte-id en de werkruimtenaam.
    * Wijs voor de rol **Bijdrager voor opslagblobgegevens** in het opslagaccount **myworkspace** toe als werkruimte-id.
    * Wijs **myworkspace** als werkruimtenaam toe.

1. Selecteer **Opslaan**.

## <a name="open-synapse-studio"></a>Synapse Studio openen

Wanneer uw Azure Synapse-werkruimte is gemaakt, kunt u Synapse Studio op twee manieren openen:

* Open de Synapse-werkruimte in de [Azure-portal](https://portal.azure.com). Selecteer bovenaan de sectie **Overzicht** de optie **Synapse Studio starten**.
* Ga naar `https://web.azuresynapse.net` en meld u aan bij uw werkruimte.

## <a name="create-a-sql-pool"></a>Een SQL-pool maken

1. Selecteer in Synapse Studio in het linkerdeelvenster de optie **Beheren** > **SQL-pools**.
1. Selecteer **Nieuw** en voer deze instellingen in:

    |Instelling | Voorgestelde waarde | 
    |---|---|---|
    |**Naam van SQL-pool**| **SQLDB1**|
    |**Prestatieniveau**|**DW100C**|
    |||

1. Selecteer **Beoordelen en maken** > **Maken**. Uw SQL-pool is binnen een paar minuten klaar. Uw SQL-pool is gekoppeld aan een SQL-pooldatabase die ook **SQLDB1** heet.

Een SQL-pool verbruikt factureerbare resources zolang deze worden uitgevoerd. U kunt de pool later onderbreken om de kosten te verlagen.

## <a name="create-an-apache-spark-pool"></a>Een Apache Spark-pool maken

1. Selecteer in Synapse Studio in het linkerdeelvenster **Beheren** > **Apache Spark-pools**.
1. Selecteer **Nieuw** en voer deze instellingen in:

    |Instelling | Voorgestelde waarde | 
    |---|---|---|
    |**Naam van Apache Spark-pool**|**Spark1**
    |**Knooppuntgrootte**| **Klein**|
    |**Aantal knooppunten**| Stel het minimum in op 3 en het maximum op 3|

1. Selecteer **Beoordelen en maken** > **Maken**. Uw Apache Spark-pool is binnen een paar seconden gereed.

> [!NOTE]
> Ondanks de naam is een Apache Spark-pool niet hetzelfde als een SQL-pool. Dit zijn slechts enkele basismetagegevens die u gebruikt om de Azure Synapse-werkruimte te vertellen hoe met Apache Spark moet worden gewerkt.

Omdat ze uit metagegevens bestaan, kunnen Spark-pools niet worden gestart of gestopt.

Wanneer u een Apache Spark-activiteit uitvoert in Azure Synapse, geeft u een Spark-pool op die moet worden gebruikt. De pool informeert Azure Synapse hoeveel Apache Spark-resources moeten worden gebruikt. U betaalt alleen voor de resources die u gebruikt. Wanneer u de pool niet meer actief gebruikt, treedt er automatisch een time-out op in de resources en worden ze gerecycled.

> [!NOTE]
> Apache Spark-databases worden onafhankelijk van Apache Spark-pools gemaakt. Een werkruimte heeft altijd een Apache Spark-database met de naam **standaard**. U kunt extra Apache Spark-databases maken.

## <a name="the-sql-on-demand-pool"></a>De SQL on-demand-pool

Elke werkruimte wordt geleverd met een vooraf gemaakte pool met de naam **SQL on-demand**. Deze pool kan niet worden verwijderd. Met de pool SQL on-demand kunt u met SQL werken zonder dat u een SQL-pool hoeft te maken of beheren in Azure Synapse.

In tegenstelling tot de andere soorten pools is facturering voor SQL on-demand gebaseerd op de hoeveelheid gegevens die zijn gescand om de query uit te voeren en niet op het aantal resources dat wordt gebruikt om de query uit te voeren.

* SQL on-demand heeft ook zijn eigen SQL on-demand-databases die onafhankelijk van elke SQL on-demand-pool bestaan.
* Een werkruimte heeft altijd precies één SQL on-demand-pool met de naam **SQL on-demand**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Analyseren met behulp van een SQL-pool](get-started-analyze-sql-pool.md)
