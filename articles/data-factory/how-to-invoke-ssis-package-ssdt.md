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
ms.openlocfilehash: c3163d414e940d843489a34f319996b1b8ed6f4a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497378"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSIS-pakketten uitvoeren in azure vanuit SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt de functie van SSIS-projecten (Azure-enabled SQL Server Integration Services) beschreven op SQL Server Data Tools (SSDT), waarmee u pakketten kunt uitvoeren op Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF).  U kunt deze functie gebruiken om uw bestaande SSIS-pakketten te testen voordat u de & Shift/migreert naar Azure of om nieuwe SSIS-pakketten te ontwikkelen om uit te voeren in Azure.

Met deze functie kunt u een nieuwe Azure-SSIS IR maken of een bestaande toevoegen aan SSIS-projecten en vervolgens uw pakketten hierop uitvoeren.  We ondersteunen het uitvoeren van pakketten die moeten worden geïmplementeerd in de SSIS-catalogus (SSISDB) in het implementatie model van het project en die moeten worden geïmplementeerd in bestands systemen/bestands shares/Azure Files in pakket implementatie model. 

## <a name="prerequisites"></a>Vereisten
Als u deze functie wilt gebruiken, moet u de meest recente SSDT met SSIS projects-extensie voor Visual Studio van [hier of als](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) zelfstandig installatie programma downloaden en [installeren.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Azure-SSIS-projecten inschakelen
### <a name="create-new-azure-enabled-ssis-projects"></a>Nieuwe Azure-SSIS-projecten maken
Op SSDT kunt u nieuwe Azure-SSIS-projecten maken met behulp van de sjabloon voor het **Integration Services-project (Azure-enabled)** .

   ![Nieuw Azure-SSIS-project](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Nadat het Azure-project is gemaakt, wordt u gevraagd verbinding te maken met SSIS in Azure Data Factory.

   ![Verbinding maken Azure-SSIS IR prompt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Als u meteen verbinding wilt maken met uw Azure-SSIS IR, raadpleegt u [verbinding maken met Azure-SSIS IR](#irconnect) voor meer informatie. U kunt ook later verbinding maken door met de rechter muisknop op het project knooppunt in Solution Explorer scherm van SSDT te klikken om een menu te openen en vervolgens de optie **verbinding maken met SSIS in azure Data Factory** onder het submenu **SSIS in azure Data Factory** te selecteren.

### <a name="azure-enable-existing-ssis-projects"></a>Azure-bestaande SSIS-projecten inschakelen
Voor bestaande SSIS-projecten kunt u Azure inschakelen door de volgende stappen uit te voeren:

1. Klik met de rechter muisknop op het project knooppunt in Solution Explorer deel venster van SSDT om een menu te openen en selecteer vervolgens de menu opdracht **Azure-project** in het submenu **SSIS in azure Data Factory** om de **wizard Azure-project**te starten.

   ![Azure-bestaand SSIS-project inschakelen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. Selecteer op de pagina **Visual Studio-configuratie selecteren** de configuratie van Visual Studio om instellingen voor het uitvoeren van het pakket toe te passen in Azure. Het is een goed idee om een nieuwe Visual Studio-configuratie voor Cloud en Azure te maken. Schakel het project in op de Cloud configuratie. Met meerdere configuraties kunt u verschillende waarden toewijzen aan de para meters op basis van de verschillende omgevingen (lokaal of in Azure). Raadpleeg dit voor [beeld](#example) voor meer informatie.

   ![Visual Studio-configuratie selecteren](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Voor Azure: door uw bestaande SSIS-projecten in te scha kelen, moet u de versie van de doel server instellen op de nieuwste, die wordt ondersteund door Azure-SSIS IR, die momenteel **SQL Server 2017**. Als u dit nog niet hebt gedaan, moet u controleren of uw pakket aanvullende onderdelen bevat die niet worden ondersteund op SQL Server 2017 en klikt u op de knop Volgende om door te gaan als er geen bezorgdheid is.

   ![Doel server versie wijzigen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Raadpleeg [verbinding maken met Azure-SSIS IR](#irconnect) om de verbinding met Azure-SSIS IR te volt ooien.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="irconnect"></a>Verbinding maken met Azure-projecten op SSIS in Azure Data Factory

Door uw Azure-projecten te koppelen aan SSIS in ADF, kunt u uw pakketten uploaden naar Azure Files en deze uitvoeren op Azure-SSIS IR. Voer hiervoor de volgende stappen uit:

1. Lees de inleiding op de pagina **SSIS in ADF** (Inleiding) en klik op de knop **volgende** om door te gaan.

   ![Introductie van SSIS in ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. Selecteer op de pagina **SSIS IR selecteren op ADF** de bestaande ADF en Azure-SSIS IR om pakketten uit te voeren of nieuwe te maken als u er nog geen hebt.
   - Als u uw bestaande Azure-SSIS IR wilt selecteren, selecteert u eerst het relevante Azure-abonnement en de ADF.
   - Als u uw bestaande ADF selecteert die geen Azure-SSIS IR heeft, klikt u op de knop **SSIS-IR maken** om een nieuwe te maken op de ADF-portal of-app.
   - Als u uw bestaande Azure-abonnement selecteert dat geen ADF heeft, klikt u op de knop **SSIS-IR maken** om de **wizard Integration runtime maken**te starten. hier kunt u de locatie en het voor voegsel voor ons opgeven om automatisch een nieuwe Azure-resource groep, Data Factory en SSIS IR te maken voor uw naam, met de naam in het volgende patroon: **YourPrefix-RG/DF/IR-YourCreationTime**.

   ![SSIS IR selecteren in ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Selecteer op de pagina **Azure Storage selecteren** uw bestaande Azure Storage account om pakketten naar Azure files te uploaden of maak een nieuwe als u er geen hebt.
   - Als u uw bestaande Azure Storage account wilt selecteren, selecteert u eerst het relevante Azure-abonnement.
   - Als u hetzelfde Azure-abonnement hebt als uw Azure-SSIS IR dat geen Azure Storage-account heeft, klikt u op de knop **Azure Storage maken** voor ons om automatisch een nieuwe te maken op dezelfde locatie als uw Azure-SSIS IR, met de naam door een voor voegsel van uw Azure-SSIS IR naam en de aanmaak datum te combi neren.
   - Als u een ander Azure-abonnement selecteert dat geen Azure Storage account heeft, klikt u op de knop **Azure Storage maken** om een nieuwe te maken op Azure Portal.

   ![Azure Storage selecteren](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Klik op de knop **verbinding maken** om de verbinding te volt ooien.  Uw geselecteerde Azure-SSIS IR en Azure Storage account worden weer gegeven onder het knoop punt **gekoppelde Azure-resources** in Solution Explorer paneel van SSDT.  We vernieuwen ook de status van uw Azure-SSIS IR, terwijl u deze kunt beheren door met de rechter muisknop op het knoop punt te klikken om een menu te openen en vervolgens het **Start\Stop\Manage** menu-item te selecteren dat u naar de ADF-Portal/-app gaat.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>SSIS-project\packages evalueren voor uitvoeringen in azure
### <a name="assess-ssis-project-or-package"></a>SSIS-project of-pakket beoordelen
Voordat u uw pakketten uitvoert in azure, kunt u uw pakket beoordelen om te evalueren of er sprake is van een mogelijke migratie blok kering of informatie die u moet kennen. 
-  U kunt alle pakketten evalueren onder project of één pakket.

   ![Project ](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)
    ![ beoordelings pakket beoordelen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

-  U kunt een evaluatie rapport ophalen om elk evaluatie probleem te controleren en elk probleem heeft een gedetailleerde beschrijving en aanbeveling. U kunt ook een evaluatie rapport exporteren als CSV-bestand. 

   ![Project resultaat beoordelen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppress-assessment-rule"></a>Beoordelings regel onderdrukken
Als u zeker weet dat een bepaalde evaluatie regel niet voor uw pakket is toegepast, kunt u ervoor kiezen deze te onderdrukken. 
-  U kunt direct klikken op de koppeling **beoordelings regel onderdrukken** in evaluatie rapport configureren.

   ![Instellingen voor onderdrukking van evaluatie regels](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  U kunt ook configureren via **Azure-instellingen**.

   ![Instellingen waarvoor Azure is ingeschakeld](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Instellingen voor het onderdrukken van evaluatie regels via Azure-instellingen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>SSIS-pakketten uitvoeren in azure
### <a name="azure-enabled-setting"></a>Instelling met Azure ingeschakeld
Voordat u uw pakketten uitvoert in azure, kunt u de uitvoerings instellingen configureren. Als u Windows-verificatie voor uw SSIS-pakketten wilt inschakelen, volgt u de onderstaande stappen:

1. Klik met de rechter muisknop op het project knooppunt in Solution Explorer deel venster van SSDT om een menu te openen en selecteer vervolgens het menu-item met **Azure-instellingen** in het submenu **SSIS in azure Data Factory** .

   ![Instellingen waarvoor Azure is ingeschakeld](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Klik op de vervolg keuzelijst **Windows-verificatie inschakelen** en kies **waar**. Klik vervolgens op de knop bewerken voor **Windows-verificatie referenties** om de referenties in te voeren.

   ![Windows-verificatie inschakelen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Geef referenties op in de editor voor **Windows-verificatie referenties** .

   ![Windows-verificatie referenties](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Pakket uitvoeringen starten
Nadat u uw projecten hebt verbonden met SSIS in ADF, kunt u pakketten uitvoeren op Azure-SSIS IR.  U hebt twee opties om pakket uitvoeringen te starten:
-  Klik op de knop Start op de werk balk van de SSDT om een menu te **openen** en de menu opdracht **uitvoeren in azure** te selecteren 

   ![Uitvoeren in azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Klik met de rechter muisknop op het knoop punt package in Solution Explorer scherm van SSDT om een menu te openen en het **pakket uitvoeren in azure** menu-item te selecteren.

   ![Pakket uitvoeren in azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Voor het uitvoeren van uw pakketten in azure moet u een actief Azure-SSIS IR hebben, dus als uw Azure-SSIS IR is gestopt, wordt er een dialoog venster weer gegeven met de pop-up om het te starten.  Als u een aangepaste instel tijd wilt uitsluiten, moet dit proces binnen vijf minuten worden voltooid, maar kan het ongeveer 20-30 minuten duren voordat Azure-SSIS IR lid wordt van een virtueel netwerk.  Nadat u uw pakketten hebt uitgevoerd in azure, kunt u uw Azure-SSIS IR voor het beheren van de lopende kosten stoppen door met de rechter muisknop op het knoop punt in Solution Explorer scherm van SSDT te klikken om een menu te openen en vervolgens de **Start\Stop\Manage** menu opdracht te selecteren die u naar de ADF-Portal/-app gaat.

### <a name="checking-package-execution-logs"></a>Uitvoer logboeken voor het pakket controleren
Wanneer u de pakket uitvoering start, zullen we het logboek opmaken en weer geven in het voortgangs venster van SSDT.  Voor een langlopend pakket wordt het logboek periodiek bijgewerkt op basis van de minuten.  U kunt de uitvoering van het pakket stoppen door te klikken op de knop **stoppen** op de werk balk van de SSDT, die deze onmiddellijk annuleert.  U kunt ook de onbewerkte gegevens in het logboek tijdelijk vinden in het UNC-pad (Universal Naming Convention): `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , maar we zullen het na één dag opschonen.

### <a name="switching-package-protection-level"></a>Niveau van pakket beveiliging overschakelen
Het uitvoeren van SSIS-pakketten in Azure biedt geen ondersteuning voor **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** -beveiligings niveaus.  Als uw pakketten echter worden geconfigureerd met deze, zullen we ze tijdelijk overschakelen naar **EncryptSensitiveWithPassword** / **EncryptAllWithPassword**, respectievelijk met wille keurig gegenereerde wacht woorden wanneer we uw pakketten uploaden naar Azure files voor uitvoering op uw Azure-SSIS IR.

> [!NOTE]
> Als uw pakketten uitvoerings pakket taken bevatten die verwijzen naar andere pakketten die zijn geconfigureerd met **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** -beveiligings niveaus, moet u die andere pakketten hand matig opnieuw configureren om **EncryptSensitiveWithPassword** / **EncryptAllWithPassword**te gebruiken voordat u de pakketten uitvoert.

Als uw pakketten al zijn geconfigureerd met **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** -beveiligings niveaus, blijven ze ongewijzigd, maar worden nog steeds gegenereerde wacht woorden gebruikt wanneer we uw pakketten uploaden naar Azure files voor uitvoering op uw Azure-SSIS IR.

### <a name="using-package-configuration-file"></a>Pakket configuratie bestand gebruiken
Als u pakket configuratie bestanden in het pakket implementatie model gebruikt om variabele waarden tijdens runtime te wijzigen, worden deze bestanden automatisch met uw pakketten naar Azure Files geüpload voor uitvoering op uw Azure-SSIS IR.

### <a name="using-execute-package-task"></a>De taak pakket uitvoeren gebruiken
Als uw pakketten uitvoerings pakket taken bevatten die verwijzen naar andere pakketten die zijn opgeslagen op lokale bestands systemen, moet u de volgende aanvullende instellingen uitvoeren:

1. Upload de andere pakketten naar Azure Files onder hetzelfde Azure Storage account dat is verbonden met uw projecten en ontvang het nieuwe UNC-pad, bijvoorbeeld`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Vervang het bestandspad van de andere pakketten in het bestands verbindings beheer van pakket taken uitvoeren met het nieuwe UNC-pad
   - Als uw computer met SSDT geen toegang krijgt tot het nieuwe UNC-pad, kunt u het bestandspad wijzigen in het deel venster Eigenschappen van bestands verbindings beheer
   - U kunt ook een variabele voor het bestandspad gebruiken om de juiste waarde toe te wijzen tijdens runtime

Als uw pakketten taken voor het uitvoeren van pakketten bevatten die verwijzen naar andere pakketten in hetzelfde project, hoeft u geen aanvullende installatie in te stellen.

## <a name="switching-package-execution-environments-with-azure-enabled-projects"></a><a name="example"></a>De pakket uitvoerings omgevingen met Azure-projecten omschakelen

Als u wilt scha kelen tussen pakket uitvoerings omgevingen met Azure-projecten, kunt u meerdere Visual Studio-configuraties maken voor het Toep assen van verschillende waarden voor specifieke para meters van de omgeving. Er is bijvoorbeeld een eenvoudig SSIS-pakket met een **bestandssysteem taak** waarmee kenmerken van het opgegeven bestand worden ingesteld, u kunt het eenvoudig migreren naar de Cloud met behulp van de volgende stappen:

1. Definieer een **filepath** van het teken reeks type. Dit is het bestandspad van het doel bestand.

   ![Pakket parameter definiëren](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Koppel de **bron verbinding** aan deze para meter. 

   ![Bron verbinding bijwerken](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Maak een nieuwe Visual Studio-configuratie voor Cloud in Visual Studio Configuration Manager.

4. Definieer de waarden voor deze para meter voor elke Visual Studio-configuratie.

   ![Parameter waarden onderdrukken](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Dit SSIS-project wordt door Azure ingeschakeld op basis van de Visual Studio-configuratie voor de Cloud.

6. Voer dit pakket uit in Azure. U kunt de omgeving eenvoudig overschakelen naar het lokale gebied door de huidige configuratie van Visual Studio te activeren.

   ![De configuratie van Visual Studio wijzigen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

## <a name="current-limitations"></a>Huidige beperkingen
-  Deze SSDT-functie biedt momenteel geen ondersteuning voor nationale Clouds.

## <a name="next-steps"></a>Volgende stappen
Zodra u tevreden bent met het uitvoeren van uw pakketten in azure van SSDT, kunt u deze implementeren en uitvoeren als run SSIS-pakket activiteiten in ADF-pijp lijnen. Zie [SSIS-pakketten uitvoeren als uitvoeren SSIS-pakket activiteiten in ADF-pijp lijnen](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
