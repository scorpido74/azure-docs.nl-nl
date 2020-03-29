---
title: Azure Data Lake Analytics U-SQL-taken plannen met SSIS
description: Meer informatie over het gebruik van SQL Server Integration Services om U-SQL-taken te plannen met inline script of uit U-SQL-querybestanden.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 0650fcc5023ac57b193fa23b0dedf65113fd64e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672897"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>U-SQL-taken plannen met SQL Server Integration Services (SSIS)

In dit document leert u hoe u-SQL-taken kunnen worden georkestreerd en gemaakt met SQL Server Integration Service (SSIS). 

## <a name="prerequisites"></a>Vereisten

[Azure Feature Pack for Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) biedt de Azure Data Lake [Analytics-taak](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) en de [Azure Data Lake Analytics Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) waarmee u verbinding maken met de Azure Data Lake Analytics-service. Als u deze taak wilt gebruiken, controleert u of u het:

- [SQL Server Data Tools (SSDT) downloaden en installeren voor Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Azure Feature Pack voor Integratieservices (SSIS) installeren](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Azure Data Lake Analytics- taak

Met de taak Azure Data Lake Analytics kunnen gebruikers U-SQL-taken indienen bij het Azure Data Lake Analytics-account. 

[Meer informatie over het configureren van Azure Data Lake Analytics-taak](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Azure Data Lake Analytics-taak in SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

U het U-SQL-script van verschillende plaatsen ophalen met behulp van ingebouwde ssis-functies en -taken, hieronder laten scenario's zien hoe u de U-SQL-scripts configureren voor verschillende gebruikerscases.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Scenario 1-Gebruik inline script oproep tvfs en opgeslagen procs

Configureer **SourceType** in Azure Data Lake Analytics-taakeditor als **DirectInput**en plaats de U-SQL-instructies in **USQLStatement**.

Voor eenvoudig onderhoud en codebeheer u bijvoorbeeld alleen korte U-SQL-scripts als inline-scripts plaatsen, waarbij u bestaande tabelwaardefuncties en opgeslagen procedures in uw U-SQL-databases aanroepen. 

![Inline U-SQL-script bewerken in SSIS-taak](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Gerelateerd artikel: [Hoe parameter door te geven aan opgeslagen procedures](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Scenario 2-Gebruik U-SQL-bestanden in Azure Data Lake Store

U u-SQL-bestanden ook gebruiken in de Azure Data Lake Store met **azure data lake store-bestandssysteemtaak** in Azure Feature Pack. Deze aanpak stelt u in staat om de scripts die zijn opgeslagen in de cloud te gebruiken.

Volg onderstaande stappen om de verbinding tussen Azure Data Lake Store File System Task en Azure Data Lake Analytics Task in te stellen.

### <a name="set-task-control-flow"></a>Taakbesturingsstroom instellen

Voeg in de ontwerpweergave van het SSIS-pakket een **Azure Data Lake Store-bestandssysteemtaak,** een **Foreach Loop-container** en een **Azure Data Lake Analytics-taak** toe in de Foreach Loop-container. De Azure Data Lake Store File System Task helpt u-SQL-bestanden in uw ADLS-account te downloaden naar een tijdelijke map. De Foreach Loop Container en de Azure Data Lake Analytics-taak helpen om elk U-SQL-bestand onder de tijdelijke map in te dienen bij het Azure Data Lake Analytics-account als een U-SQL-taak.

![U-SQL-bestanden gebruiken in Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Azure Data Lake Store-bestandssysteemtaak configureren

1. **Stel bewerking** in **op CopyFromADLS**.
2. **AzureDataLakeConnection**instellen , meer informatie over [Azure Data Lake Store Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. **AzureDataLakeDirectory**instellen . Wijs de map aan die uw U-SQL-scripts opslaat. Gebruik een relatief pad dat relatief is ten opzichte van de hoofdmap van het Azure Data Lake Store-account.
4. **Stel Doel** in op een map die de gedownloade U-SQL-scripts in de cache opslaat. Dit mappad wordt gebruikt in Foreach Loop Container voor U-SQL-taakindiening. 

![Azure Data Lake Store-bestandssysteemtaak configureren](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Meer informatie over azure data lake store-bestandssysteemtaak](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017).

### <a name="configure-foreach-loop-container"></a>Foreach Loop Container configureren

1. Stel **op de** pagina Verzameling **enumerator** in op **Foreach File Enumerator**.

2. Stel **Map** in onder **de configuratiegroep Enumerator** op de tijdelijke map met de gedownloade U-SQL-scripts.

3. Bestanden **Files** instellen onder **enumeratorconfiguratie** zodat `*.usql` de luscontainer alleen `.usql`de bestanden vangt die eindigen met .

    ![Foreach Loop Container configureren](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. Voeg op de pagina **Variabele toewijzingen** een door de gebruiker gedefinieerde variabele toe om de bestandsnaam voor elk U-SQL-bestand te krijgen. Stel de **index** in op 0 om de bestandsnaam te krijgen. Definieer in dit voorbeeld `User::FileName`een variabele met de naam . Deze variabele wordt gebruikt om dynamisch u-SQL-scriptbestandsverbinding te krijgen en u-SQL-taaknaam in te stellen in Azure Data Lake Analytics Task.

    ![Foreach Loop Container configureren om bestandsnaam te krijgen](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Azure Data Lake Analytics-taak configureren 

1. **SourceType** instellen op **Bestandsverbinding**.

2. **Bestandsverbinding** instellen op de bestandsverbinding die verwijst naar de bestandsobjecten die zijn geretourneerd vanuit Foreach Loop Container.
    
    Ga als volgt te werk om deze bestandsverbinding te maken:

   1. Kies ** \<Nieuwe verbinding...>** in de fileconnection-instelling.
   2. Stel **het type gebruik** in op Bestaand **bestand**en stel het **bestand** in op het bestandspad van een bestaand bestand.

       ![Foreach Loop Container configureren](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. Klik in de weergave **Verbindingsbeheer** met de rechtermuisknop op de zojuist gemaakte bestandsverbinding en kies **Eigenschappen**.

   4. Vouw in het venster **Eigenschappen** **expressies**uit en stel **verbindingstekenreeks** in op `@[User::FileName]`de variabele die is gedefinieerd in foreach loopcontainer, bijvoorbeeld .

       ![Foreach Loop Container configureren](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. **Stel AzureDataLakeAnalyticsConnection** in op het Azure Data Lake Analytics-account waaraan u taken wilt verzenden. Meer informatie over [Azure Data Lake Analytics Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017).

4. Andere taakconfiguraties instellen. [Meer weten?](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017)

5. Gebruik **Expressies** om de naam van u-SQL dynamisch in te stellen:

    1. Voeg op de pagina **Expressies** een nieuw expressiesleutelsleutelpaar toe voor **JobName**.
    2. Stel de waarde voor JobName in op de variabele die `@[User::FileName]`is gedefinieerd in bijvoorbeeld Foreach Loop Container.
    
        ![SSIS-expressie configureren voor U-SQL-taaknaam](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Scenario 3-Gebruik U-SQL-bestanden in Azure Blob Storage

U U-SQL-bestanden gebruiken in Azure Blob Storage met **Azure Blob Download Taak** in Azure Feature Pack. Deze aanpak stelt u in staat om de scripts in de cloud te gebruiken.

De stappen zijn vergelijkbaar met [Scenario 2: U-SQL-bestanden gebruiken in Azure Data Lake Store.](#scenario-2-use-u-sql-files-in-azure-data-lake-store) Wijzig de Azure Data Lake Store-bestandssysteemtaak in Azure Blob-downloadtaak. [Meer informatie over de downloadtaak van Azure Blob](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

De controlestroom is als hieronder.

![U-SQL-bestanden gebruiken in Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Scenario 4-Gebruik U-SQL-bestanden op de lokale machine

Naast het gebruik van U-SQL-bestanden die in de cloud zijn opgeslagen, u ook bestanden gebruiken op uw lokale machine of bestanden die zijn geïmplementeerd met uw SSIS-pakketten.

1. Klik met de rechtermuisknop op **Verbindingsmanagers** in het SSIS-project en kies **Nieuwe Verbindingsbeheer**.

2. Selecteer **Bestandstype** en klik op **Toevoegen...**.

3. Stel **het gebruikstype** in **op Bestaand bestand**en stel het **bestand** in op het bestand op de lokale machine.

    ![Bestandsverbinding toevoegen aan het lokale bestand](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Voeg **Azure Data Lake Analytics-taak** toe en:
    1. **SourceType** instellen op **Bestandsverbinding**.
    2. **Bestandsverbinding** instellen op de bestandsverbinding die zojuist is gemaakt.

5. Andere configuraties voor Azure Data Lake Analytics-taak voltooien.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Scenario 5-Gebruik U-SQL-instructie in SSIS-variabele

In sommige gevallen moet u mogelijk de U-SQL-instructies dynamisch genereren. U **SSIS-variabele** met **SSIS-expressie** en andere SSIS-taken, zoals Script-taak, gebruiken om u te helpen de U-SQL-instructie dynamisch te genereren.

1. Open het gereedschap Variabelenvenster via **Het menu van ssis-> variabelen** op het hoogste niveau.

2. Voeg een SSIS-variabele toe en stel de waarde direct in of gebruik **Expressie** om de waarde te genereren.

3. Voeg **Azure Data Lake Analytics-taak** toe en:
    1. **SourceType** instellen op **Variabel**.
    2. **Stel SourceVariable** in op de SSIS-variabele die zojuist is gemaakt.

4. Andere configuraties voor Azure Data Lake Analytics-taak voltooien.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Scenario 6-Pass-parameters naar U-SQL-script

In sommige gevallen u de u-SQL-variabele waarde dynamisch instellen in het U-SQL-script. **Parametertoewijzingsfunctie** in Azure Data Lake Analytics-taak helpt bij dit scenario. Er zijn meestal twee typische gebruikersgevallen:

- Stel de invoer- en uitvoerbestandspadvariabelen dynamisch in op basis van de huidige datum en tijd.
- Stel de parameter in voor opgeslagen procedures.

[Meer informatie over het instellen van parameters voor het U-SQL-script](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Volgende stappen

- [SSIS-pakketten uitvoeren in Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Azure Feature Pack for Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [U-SQL-taken plannen met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)
