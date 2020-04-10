---
title: On-premises SQL Server Integration Services -taken (SSIS)-taken migreren naar Azure Data Factory
description: In dit artikel wordt beschreven hoe u SQL Server Integration Services (SSIS)-taken migreert naar Azure Data Factory-pijplijnen/activiteiten/triggers met SQL Server Management Studio.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: ee51be1d994c3b81765266e95c48d321a2f43b14
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80989439"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>SQL Server Agent-taken migreren naar ADF met SSMS

Wanneer [u on-premises SQL Server Integration Services (SSIS)-workloads migreert naar SSIS in ADF](scenario-ssis-migration-overview.md), nadat SSIS-pakketten zijn gemigreerd, u batchmigratie uitvoeren van SQL Server Agent-taken met taakstaptype SQL Server Integration Services-pakket naar Azure Data Factory (ADF)-pijplijnen/activiteiten/planningstriggers via de **Wizard SSMS-taakmigratie**van SQL Server Management Studio (SSMS).

In het algemeen kan de **wizard SSIS-taakmigratie** voor geselecteerde SQL-agenttaken met toepasselijke taakstaptypen:

- on-premises SSIS-pakketlocatie te kaarten waar naar de pakketten worden gemigreerd, die toegankelijk zijn voor SSIS in ADF.
    > [!NOTE]
    > Pakketlocatie van bestandssysteem wordt alleen ondersteund.
- migreer toepasselijke taken met toepasselijke taakstappen naar overeenkomstige ADF-resources zoals hieronder:

|SQL Agent-taakobject  |ADF-bron  |Opmerkingen|
|---------|---------|---------|
|SQL Agent, taak|Pijpleiding     |De naam van de pijplijn wordt *gegenereerd voor \<taaknaam>*. <br> <br> Ingebouwde agenttaken zijn niet van toepassing: <li> Onderhoudstaak voor SSIS-server <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|SSIS-taakstap|SSIS-pakketactiviteit uitvoeren|<li> De naam van \<de activiteit wordt stapnaam>. <li> Proxy-account dat wordt gebruikt in taakstap wordt gemigreerd als Windows-verificatie van deze activiteit. <li> *Uitvoeringsopties* behalve *32-bits runtime* gebruiken die in taakstap is gedefinieerd, worden genegeerd in migratie. <li> *Verificatie* gedefinieerd in taakstap wordt genegeerd in migratie.|
|schedule      |trigger voor schema        |De naam van de planningstrigger wordt *gegenereerd voor \<de naam van de planning>*. <br> <br> Onderstaande opties in SQL Agent-taakschema worden genegeerd in migratie: <li> Interval op het tweede niveau. <li> *Automatisch starten wanneer SQL Server Agent wordt gestart* <li> *Start wanneer de CPU's niet actief worden* <li> *weekdag* en *weekenddag*<time zone> <br> Hieronder staan de verschillen nadat sql-agent-taakschema is gemigreerd naar adf-planningtrigger: <li> ADF-planning Trigger volgende run is onafhankelijk van de uitvoeringsstatus van de antecedent triggered run. <li> ADF Schedule Trigger recidiefconfiguratie verschilt van dagelijkse frequentie in SQL-agent taak.|

- Azure Resource Manager-sjablonen (ARM) genereren in de lokale uitvoermap en direct of later handmatig implementeren in de gegevensfabriek. Zie [Microsoft.DataFactory-brontypen](https://docs.microsoft.com/azure/templates/microso.ft.datafactory/allversions)voor meer informatie over ADF Resource Manager-sjablonen.

## <a name="prerequisites"></a>Vereisten

De functie die in dit artikel wordt beschreven, vereist SQL Server Management Studio versie 18.5 of hoger. Zie SQL Server Management Studio [(SSMS) downloaden voor](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)de nieuwste versie van SSMS.

## <a name="migrate-ssis-jobs-to-adf"></a>SSIS-taken migreren naar ADF

1. Selecteer in SSMS in Object Explorer SQL Server Agent, selecteer Taken en klik met de rechtermuisknop en selecteer **SSIS-taken migreren naar ADF**.
![Menu](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Meld u aan in Azure, selecteer Azure-abonnement, gegevensfabriek en integratieruntime. Azure Storage is optioneel, dat wordt gebruikt in de toewijzingsstap voor pakketlocatie als SSIS-taken die moeten worden gemigreerd, SSIS-bestandssysteempakketten hebben.
![Menu](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Breng de paden van SSIS-pakketten en configuratiebestanden in SSIS-taken in kaart met doelpaden waargemigreerde pijplijnen toegang toe hebben. In deze toewijzingsstap u het volgende doen:

    1. Selecteer een bronmap en voeg **vervolgens Toewijzing toe**.
    1. Pad van bronmap bijwerken. Geldige paden zijn mappaden of bovenliggende mappaden van pakketten.
    1. Pad van doelmap bijwerken. Standaard is relatief pad naar het standaardopslagaccount, dat is geselecteerd in stap 1.
    1. Een geselecteerde toewijzing verwijderen via **Toewijzing verwijderen**.
![stap2](media/how-to-migrate-ssis-job-ssms/step2.png)
![stap2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Selecteer toepasselijke taken om te migreren en configureer de instellingen van de *bijbehorende uitgevoerde SSIS-pakketactiviteit*.

    - *Standaardinstelling*is standaard van toepassing op alle geselecteerde stappen. Zie *Het tabblad Instellingen* voor de [activiteit SSIS-pakket uitvoeren](how-to-invoke-ssis-package-ssis-activity.md) wanneer de pakketlocatie *Bestandssysteem (pakket)* is voor meer informatie over elke eigenschap.
    ![stap3-1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Stapinstelling*, instelling configureren voor een geselecteerde stap.
        
        **Standaardinstelling toepassen:** standaard is geselecteerd. Schakel de optie niet uit om alleen de instelling voor geselecteerde stap te configureren.  
        Zie *Het tabblad Instellingen* voor de [activiteit SSIS-pakket uitvoeren](how-to-invoke-ssis-package-ssis-activity.md) wanneer de locatie van het pakket *bestandssysteem (pakket)* is, voor meer informatie over andere eigenschappen.
    ![stap3-2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Arm-sjabloon genereren en implementeren.
    1. Selecteer of voer het uitvoerpad in voor de ARM-sjablonen van de gemigreerde ADF-pijplijnen. Map wordt automatisch gemaakt als deze niet bestaat.
    2. Selecteer de optie **ARM-sjablonen implementeren in uw gegevensfabriek:**
        - Standaard is niet geselecteerd. U gegenereerde ARM-sjablonen later handmatig implementeren.
        - Selecteer om gegenereerde ARM-sjablonen rechtstreeks naar de gegevensfabriek te implementeren.
    ![stap4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Migreren en controleer de resultaten.
![stap5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Volgende stappen

[Pijplijn uitvoeren en bewaken](how-to-invoke-ssis-package-ssis-activity.md)
