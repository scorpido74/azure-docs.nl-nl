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
ms.date: 08/27/2020
ms.openlocfilehash: 78ec233e618511c748ed9f51b97161eddc5e8308
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707523"
---
# <a name="create-a-synapse-workspace"></a>Een Synapse-werkruimte maken

In deze zelfstudie leert u hoe u een Synapse-werkruimte, een SQL-pool en een Apache Spark-pool maakt. 

## <a name="create-a-synapse-workspace"></a>Een Synapse-werkruimte maken

1. Open de [Azure-portal](https://portal.azure.com) en zoek bovenin naar **Synapse**.
1. Selecteer in de zoekresultaten onder **Services** de optie **Azure Synapse Analytics (voorbeeld van werkruimten)** .
1. Selecteer **Toevoegen** om een werkruimte te maken.
1. Kies in **Basisinstellingen** een naam voor de werkruimte. In deze zelfstudie gebruiken we **myworkspace**.
1. U hebt een ADLSGEN2-account nodig om een werkruimte te maken. Het meest eenvoudige is om een nieuw account te maken. Als u een bestaand account opnieuw wilt gebruiken, moet u een aantal extra configuratiestappen uitvoeren. 
1. OPTIE 1 Een nieuw ADLSGEN2-account maken 
    1. Navigeer naar **Data Lake Storage Gen 2 selecteren**. 
    1. Klik op **Nieuwe maken** en voer de naam **contosolake** in.
    1. Klik op **Bestandssysteem** en voer de naam **users** in.
1. OPTIE 2 Een bestaand ADLSGEN2-account gebruiken. Zie de instructies in **Een ADLSGEN2-opslagaccount voorbereiden** onderaan dit document.
1. De Azure Synapse-werkruimte gebruikt dit opslagaccount als primair opslagaccount en de container als opslag voor werkruimtegegevens. In de werkruimte worden gegevens opgeslagen in Apache Spark-tabellen. Het slaat Apache Spark-toepassingslogboeken op in een map met de naam **/synapse/workspacename**.
1. Selecteer **Beoordelen en maken** > **Maken**. Uw werkruimte is binnen een paar minuten klaar.


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

## <a name="preparing-a-adlsgen2-storage-account"></a>Een ADLSGEN2-opslagaccount voorbereiden

### <a name="perform-the-following-steps-before-you-create-your-workspace"></a>Voer de volgende stappen uit VOORDAT u uw werkruimte maakt

1. Open [Azure Portal](https://portal.azure.com).
1. Navigeer naar uw bestaande opslagaccount
1. Selecteer **Toegangsbeheer (IAM)** in het linkerdeelvenster. 
1. Wijs vervolgens de volgende rollen toe of controleer of deze al zijn toegewezen:
    * Wijs uzelf toe aan de rol **Eigenaar**.
    * Wijs uzelf toe aan de rol **Eigenaar van opslagblobgegevens**.
1. Selecteer in het navigatievenster links de optie **Containers** en maak een container.
1. U kunt de container een naam geven. In dit document gebruiken we de naam **users**.
1. Accepteer de standaardinstelling **Openbaar toegangsniveau** en selecteer vervolgens **Maken**.

### <a name="perform-the-following-steps-after-you-create-your-workspace"></a>Voer de volgende stappen uit NADAT u uw werkruimte hebt gemaakt

Configureer toegang tot het opslagaccount vanuit uw werkruimte. Beheerde identiteiten voor uw Azure Synapse-werkruimte hebben mogelijk al toegang tot het opslagaccount. Voer de volgende stappen uit om dat te controleren:

1. Open de [Azure-portal](https://portal.azure.com) en open het primaire opslagaccount dat u hebt gekozen voor uw werkruimte.
1. Selecteer **Toegangsbeheer (IAM)** in het linkerdeelvenster.
1. Wijs vervolgens de volgende rollen toe of controleer of ze al zijn toegewezen. We gebruiken dezelfde naam voor de werkruimte-id en de werkruimtenaam.
    * Wijs voor de rol **Bijdrager voor opslagblobgegevens** in het opslagaccount **myworkspace** toe als werkruimte-id.
    * Wijs **myworkspace** als werkruimtenaam toe.

1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Analyseren met behulp van een SQL-pool](get-started-analyze-sql-pool.md)
