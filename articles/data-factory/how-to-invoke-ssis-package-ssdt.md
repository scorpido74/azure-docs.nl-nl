---
title: SSIS-pakketten uitvoeren vanuit SSDT
description: Meer informatie over het uitvoeren van SSIS-pakketten in azure vanuit SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 11e76fea87c60ae2b56cc15d5827be6e1b2b5a01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399429"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSIS-pakketten uitvoeren in azure vanuit SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt de functie van SSIS-projecten (Azure-enabled SQL Server Integration Services) beschreven op SQL Server Data Tools (SSDT), waarmee u pakketten kunt uitvoeren op Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF).  U kunt deze functie gebruiken om uw bestaande SSIS-pakketten te testen voordat u de & Shift/migreert naar Azure of om nieuwe SSIS-pakketten te ontwikkelen om uit te voeren in Azure.

Met deze functie kunt u een nieuwe Azure-SSIS IR maken of een bestaande toevoegen aan SSIS-projecten en vervolgens uw pakketten hierop uitvoeren.  We ondersteunen het uitvoeren van pakketten die moeten worden geïmplementeerd in de SSIS-catalogus (SSISDB) in het implementatie model van het project en die moeten worden geïmplementeerd in bestands systemen/bestands shares/Azure Files in pakket implementatie model. 

## <a name="prerequisites"></a>Vereisten
Als u deze functie wilt gebruiken, moet u de meest recente SSDT met SSIS projects-extensie voor Visual Studio van [hier of als](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) zelfstandig installatie programma downloaden en [installeren.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Azure-SSIS-projecten inschakelen
Op SSDT kunt u nieuwe Azure-SSIS-projecten maken met behulp van de sjabloon voor het **Integration Services-project (Azure-enabled)** .

![Nieuw Azure-SSIS-project](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

U kunt ook uw bestaande SSIS-projecten inschakelen door met de rechter muisknop op het project knooppunt in Solution Explorer scherm van SSDT te klikken om een menu te openen en vervolgens het menu **-item met Azure-functionaliteit** te selecteren.

![Azure-bestaand SSIS-project inschakelen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Als u uw bestaande SSIS-projecten wilt inschakelen, moet u de versie van de doel server zo instellen dat deze het meest recent is als wordt ondersteund door Azure-SSIS IR, die momenteel **SQL Server 2017**, dus als u dit nog niet hebt gedaan, wordt er een dialoog venster weer gegeven met de pop-up.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Verbinding maken met Azure-projecten op SSIS in Azure Data Factory
Door uw Azure-projecten te koppelen aan SSIS in ADF, kunt u uw pakketten uploaden naar Azure Files en deze uitvoeren op Azure-SSIS IR.  Voer hiervoor de volgende stappen uit:

1. Klik met de rechter muisknop op het knoop punt project of **gekoppelde Azure-resources** in Solution Explorer scherm van SSDT om een menu te openen en selecteer het menu-item **verbinding maken met SSIS in azure Data Factory** om de **wizard SSIS in de ADF-verbinding**te starten.

   ![Verbinding maken met SSIS in ADF](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. Lees de inleiding op de pagina **SSIS in ADF** (Inleiding) en klik op de knop **volgende** om door te gaan.

   ![Introductie van SSIS in ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Selecteer op de pagina **SSIS IR selecteren op ADF** de bestaande ADF en Azure-SSIS IR om pakketten uit te voeren of nieuwe te maken als u er nog geen hebt.
   - Als u uw bestaande Azure-SSIS IR wilt selecteren, selecteert u eerst het relevante Azure-abonnement en de ADF.
   - Als u uw bestaande ADF selecteert die geen Azure-SSIS IR heeft, klikt u op de knop **SSIS-IR maken** om een nieuwe te maken op de ADF-portal of-app.
   - Als u uw bestaande Azure-abonnement selecteert dat geen ADF heeft, klikt u op de knop **SSIS-IR maken** om de **wizard Integration runtime maken**te starten. hier kunt u de locatie en het voor voegsel voor ons opgeven om automatisch een nieuwe Azure-resource groep, Data Factory en SSIS IR te maken voor uw naam, met de naam in het volgende patroon: **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![SSIS IR selecteren in ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Selecteer op de pagina **Azure Storage selecteren** uw bestaande Azure Storage account om pakketten naar Azure files te uploaden of maak een nieuwe als u er geen hebt.
   - Als u uw bestaande Azure Storage account wilt selecteren, selecteert u eerst het relevante Azure-abonnement.
   - Als u hetzelfde Azure-abonnement hebt als uw Azure-SSIS IR dat geen Azure Storage-account heeft, klikt u op de knop **Azure Storage maken** voor ons om automatisch een nieuwe te maken op dezelfde locatie als uw Azure-SSIS IR, met de naam door een voor voegsel van uw Azure-SSIS IR naam en de aanmaak datum te combi neren.
   - Als u een ander Azure-abonnement selecteert dat geen Azure Storage account heeft, klikt u op de knop **Azure Storage maken** om een nieuwe te maken op Azure Portal.
   
   ![Azure Storage selecteren](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Klik op de knop **verbinding maken** om de verbinding te volt ooien.  Uw geselecteerde Azure-SSIS IR en Azure Storage account worden weer gegeven onder het knoop punt **gekoppelde Azure-resources** in Solution Explorer paneel van SSDT.  We vernieuwen ook de status van uw Azure-SSIS IR, terwijl u deze kunt beheren door met de rechter muisknop op het knoop punt te klikken om een menu te openen en vervolgens het **Start\Stop\Manage** menu-item te selecteren dat u naar de ADF-Portal/-app gaat.

## <a name="execute-ssis-packages-in-azure"></a>SSIS-pakketten uitvoeren in azure
### <a name="starting-package-executions"></a>Pakket uitvoeringen starten
Nadat u uw projecten hebt verbonden met SSIS in ADF, kunt u pakketten uitvoeren op Azure-SSIS IR.  U hebt twee opties om pakket uitvoeringen te starten:
-  Klik op de knop Start op de werk balk van de SSDT om een menu te **openen** en de menu opdracht **uitvoeren in azure** te selecteren 

   ![Uitvoeren in azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Klik met de rechter muisknop op het knoop punt package in Solution Explorer scherm van SSDT om een menu te openen en het **pakket uitvoeren in azure** menu-item te selecteren.

   ![Pakket uitvoeren in azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Voor het uitvoeren van uw pakketten in azure moet u een actief Azure-SSIS IR hebben, dus als uw Azure-SSIS IR is gestopt, wordt er een dialoog venster weer gegeven met de pop-up om het te starten.  Als u een aangepaste instel tijd wilt uitsluiten, moet dit proces binnen vijf minuten worden voltooid, maar kan het ongeveer 20-30 minuten duren voordat Azure-SSIS IR lid wordt van een virtueel netwerk.  Nadat u uw pakketten hebt uitgevoerd in azure, kunt u uw Azure-SSIS IR voor het beheren van de lopende kosten stoppen door met de rechter muisknop op het knoop punt in Solution Explorer scherm van SSDT te klikken om een menu te openen en vervolgens de **Start\Stop\Manage** menu opdracht te selecteren die u naar de ADF-Portal/-app gaat.

### <a name="checking-package-execution-logs"></a>Uitvoer logboeken voor het pakket controleren
Wanneer u de pakket uitvoering start, zullen we het logboek opmaken en weer geven in het voortgangs venster van SSDT.  Voor een langlopend pakket wordt het logboek periodiek bijgewerkt op basis van de minuten.  U kunt de uitvoering van het pakket stoppen door te klikken op de knop **stoppen** op de werk balk van de SSDT, die deze onmiddellijk annuleert.  U kunt ook de onbewerkte gegevens in het logboek tijdelijk vinden in het UNC-pad ( `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`Universal Naming Convention):, maar we zullen het na één dag opschonen.

### <a name="switching-package-protection-level"></a>Niveau van pakket beveiliging overschakelen
Het uitvoeren van SSIS-pakketten in Azure biedt geen ondersteuning voor **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** -beveiligings niveaus.  Als uw pakketten echter worden geconfigureerd met deze, zullen we ze tijdelijk overschakelen naar **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, respectievelijk met wille keurig gegenereerde wacht woorden wanneer we uw pakketten uploaden naar Azure files voor uitvoering op uw Azure-SSIS IR.

> [!NOTE]
> Als uw pakketten uitvoerings pakket taken bevatten die verwijzen naar andere pakketten die zijn geconfigureerd met **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** -beveiligings niveaus, moet u die andere pakketten hand matig opnieuw configureren om **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**te gebruiken voordat u de pakketten uitvoert.

Als uw pakketten al zijn geconfigureerd met **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** -beveiligings niveaus, blijven ze ongewijzigd, maar worden nog steeds gegenereerde wacht woorden gebruikt wanneer we uw pakketten uploaden naar Azure files voor uitvoering op uw Azure-SSIS IR.

### <a name="using-package-configuration-file"></a>Pakket configuratie bestand gebruiken
Als u pakket configuratie bestanden in het pakket implementatie model gebruikt om variabele waarden tijdens runtime te wijzigen, worden deze bestanden automatisch met uw pakketten naar Azure Files geüpload voor uitvoering op uw Azure-SSIS IR.

### <a name="using-execute-package-task"></a>De taak pakket uitvoeren gebruiken
Als uw pakketten uitvoerings pakket taken bevatten die verwijzen naar andere pakketten die zijn opgeslagen op lokale bestands systemen, moet u de volgende aanvullende instellingen uitvoeren:

1. Upload de andere pakketten naar Azure Files onder hetzelfde Azure Storage account dat is verbonden met uw projecten en ontvang het nieuwe UNC-pad, bijvoorbeeld`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Vervang het bestandspad van de andere pakketten in het bestands verbindings beheer van pakket taken uitvoeren met het nieuwe UNC-pad
   - Als uw computer met SSDT geen toegang krijgt tot het nieuwe UNC-pad, kunt u het bestandspad wijzigen in het deel venster Eigenschappen van bestands verbindings beheer
   - U kunt ook een variabele voor het bestandspad gebruiken om de juiste waarde toe te wijzen tijdens runtime

Als uw pakketten taken voor het uitvoeren van pakketten bevatten die verwijzen naar andere pakketten in hetzelfde project, hoeft u geen aanvullende installatie in te stellen.

## <a name="next-steps"></a>Volgende stappen
Zodra u tevreden bent met het uitvoeren van uw pakketten in azure van SSDT, kunt u deze implementeren en uitvoeren als run SSIS-pakket activiteiten in ADF-pijp lijnen. Zie [SSIS-pakketten uitvoeren als uitvoeren SSIS-pakket activiteiten in ADF-pijp lijnen](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
