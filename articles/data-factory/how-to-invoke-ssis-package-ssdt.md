---
title: SSIS-pakketten uitvoeren vanaf SSDT
description: Meer informatie over het uitvoeren van SSIS-pakketten in Azure vanuit SSDT.
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
ms.openlocfilehash: 5f21623af9b89bbb020063dfb72f7b60e65a6ebe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927712"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSIS-pakketten uitvoeren in Azure vanuit SSDT
In dit artikel wordt de functie beschreven van SQL Server Integration Services(SSIS)-projecten (Azure-enabled) op SQL Server Data Tools (SSDT), waarmee u pakketten uitvoeren op Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF).  U deze functie gebruiken om uw bestaande SSIS-pakketten te testen voordat u ze & shift/migreren naar Azure optilt of om nieuwe SSIS-pakketten te ontwikkelen die in Azure worden uitgevoerd.

Met deze functie u een nieuwe Azure-SSIS IR maken of een bestaande koppelen aan SSIS-projecten en vervolgens uw pakketten erop uitvoeren.  We ondersteunen het uitvoeren van pakketten die moeten worden geïmplementeerd in de SSIS-catalogus (SSISDB) in projectimplementatiemodel en pakketten die moeten worden geïmplementeerd in bestandssystemen/bestandsshares/Azure-bestanden in het implementatiemodel voor pakketten. 

## <a name="prerequisites"></a>Vereisten
Om deze functie te gebruiken, u downloaden en installeren van de nieuwste SSDT met SSIS Projects extensie voor Visual Studio van [hier](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) of als een standalone installer vanaf [hier](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Azure-enable SSIS-projecten
Op SSDT u nieuwe Azure-enabled SSIS-projecten maken met de sjabloon **Integration Services Project (Azure-Enabled).**

![Nieuw Azure-enabled SSIS-project](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

U ook uw bestaande SSIS-projecten inschakelen door met de rechtermuisknop op het projectknooppunt in het deelvenster Solution Explorer van SSDT te klikken om een menu te pop-upen en vervolgens het **menu-item met Azure-ingeschakeld** te selecteren.

![Bestaand SSIS-project voor Azure inschakelen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Voor Azure-inschakelen van uw bestaande SSIS-projecten moet u instellen dat de doelserverversie de nieuwste is die wordt ondersteund door Azure-SSIS IR, dat momenteel **SQL Server 2017**is, dus als u dit nog niet hebt gedaan, verschijnt er een dialoogvenster om dit te doen.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Azure-projecten verbinden met SSIS in Azure Data Factory
Door uw Azure-projecten te koppelen aan SSIS in ADF, u uw pakketten uploaden naar Azure-bestanden en deze uitvoeren op Azure-SSIS IR.  Volg hiervoor de volgende stappen:

1. Klik met de rechtermuisknop op het menu-item Project of **Gekoppeld azure resources** in het deelvenster Solution Explorer van SSDT om een menu op te starten en selecteer het menu-item Verbinding maken met **SSIS in Azure Data Factory** om het SSIS te starten in de wizard **ADF-verbinding**.

   ![Verbinding maken met SSIS in ADF](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. Bekijk op de pagina **SSIS in ADF-inleiding** de inleiding en klik op **Volgende** om door te gaan.

   ![SSIS in ADF-inleiding](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Selecteer op de pagina **Selecteer SSIS IR op ADF** uw bestaande ADF en Azure-SSIS IR om pakketten uit te voeren of nieuwe pakketten te maken als u die niet hebt.
   - Als u uw bestaande Azure-SSIS IR wilt selecteren, selecteert u eerst het relevante Azure-abonnement en ADF.
   - Als u uw bestaande ADF selecteert zonder Azure-SSIS IR, klikt u op de knop **SSIS IR maken** om een nieuwe adf-portal/-app te maken.
   - Als u uw bestaande Azure-abonnement selecteert zonder ADF, klikt u op de knop **SSIS IR maken** om de **wizard Integratieruntime-creatie**te starten, waar u de locatie en het voorvoegsel invoeren om automatisch een nieuwe Azure Resource Group, Data Factory en SSIS IR te maken, genoemd in het volgende patroon: **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![SSIS IR selecteren in ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Selecteer **op** de pagina Azure Storage selecteren uw bestaande Azure Storage-account om pakketten te uploaden naar Azure-bestanden of een nieuwe te maken als u die niet hebt.
   - Als u uw bestaande Azure Storage-account wilt selecteren, selecteert u eerst het relevante Azure-abonnement.
   - Als u hetzelfde Azure-abonnement selecteert als uw Azure-SSIS IR zonder Azure Storage-account, klikt u op de knop **Azure-opslag maken** voor ons om automatisch een nieuw abonnement namens u te maken op dezelfde locatie als uw Azure-SSIS IR, genoemd door een voorvoegsel van uw Azure-SSIS IR-naam en de aanmaakdatum te combineren.
   - Als u een ander Azure-abonnement selecteert zonder Een Azure Storage-account, klikt u op de knop **Azure-opslag maken** om een nieuw azure-abonnement te maken.
   
   ![Azure Storage selecteren](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Klik op de **knop Verbinding maken** om de verbinding te voltooien.  We geven uw geselecteerde Azure-SSIS IR- en Azure Storage-account weer onder het knooppunt **Linked Azure Resources** in het deelvenster Solution Explorer van SSDT.  We vernieuwen ook de status van uw Azure-SSIS IR, terwijl u deze beheren door met de rechtermuisknop op het knooppunt te klikken om een menu te verschijnen en vervolgens het **menu-item Start\Stop\Beheren** te selecteren dat u naar adf-portal/app brengt om dit te doen.

## <a name="execute-ssis-packages-in-azure"></a>SSIS-pakketten uitvoeren in Azure
### <a name="starting-package-executions"></a>Uitvoeringen van het startpakket
Nadat u uw projecten hebt aangesloten op SSIS in ADF, u pakketten uitvoeren op Azure-SSIS IR.  U hebt twee opties om pakketuitvoeringen te starten:
-  Klik op de knop **Start** op de werkbalk SSDT om een menu af te zetten en het **menu-item Uitvoeren in Azure te** selecteren 

   ![Uitvoeren in Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Klik met de rechtermuisknop op het pakketknooppunt in het deelvenster Solution Explorer van SSDT om een menu op te starten en het pakket uitvoeren in Azure-menu-item **te** selecteren.

   ![Pakket uitvoeren in Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Voor het uitvoeren van uw pakketten in Azure moet u een azure-SSIS IR hebben, dus als uw Azure-SSIS IR is gestopt, verschijnt er een dialoogvenster om het te starten.  Zonder aangepaste insteltijd moet dit proces binnen 5 minuten zijn voltooid, maar kan het ongeveer 20 - 30 minuten duren voordat Azure-SSIS IR een virtueel netwerk heeft.  Nadat u uw pakketten in Azure hebt uitgevoerd, u uw Azure-SSIS IR stoppen om de bedrijfskosten te beheren door met de rechtermuisknop op het knooppunt in het deelvenster Solution Explorer van SSDT te klikken om een menu te verschijnen en vervolgens het **menu-item Start\Stop\Manage** te selecteren dat u naar adf-portal/app brengt om dit te doen.

### <a name="checking-package-execution-logs"></a>Logboeken voor pakketuitvoering controleren
Wanneer u de uitvoering van het pakket start, maken we het logboek op en weerin het voortgangsvenster van SSDT.  Voor een langlopend pakket zullen we het logboek periodiek bijwerken met de minuten.  U de uitvoering van uw pakket stoppen door op de knop **Stoppen** op de werkbalk SSDT te klikken die het onmiddellijk zal annuleren.  U ook tijdelijk vinden van de log ruwe gegevens `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`in haar Universal Naming Convention (UNC) pad:, maar we zullen het opruimen na een dag.

### <a name="switching-package-protection-level"></a>Schakelpakketbeveiligingsniveau
Het uitvoeren van SSIS-pakketten in Azure biedt geen ondersteuning **voor EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey-beveiligingsniveaus.**  Als uw pakketten hiermee zijn geconfigureerd, schakelen we ze dus tijdelijk over naar **encryptsensitiveWithPassword**/**EncryptAllWithPassword**, respectievelijk met willekeurig gegenereerde wachtwoorden wanneer we uw pakketten uploaden naar Azure Files voor uitvoering op uw Azure-SSIS IR.

> [!NOTE]
> Als uw pakketten Uitvoerpakkettaken bevatten die verwijzen naar andere pakketten die zijn geconfigureerd met **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey-beveiligingsniveaus,** moet u die andere pakketten handmatig opnieuw configureren om **respectievelijk EncryptSensitiveWithPassword**/**EncryptAllWithPassword**te gebruiken voordat u uw pakketten uitvoert.

Als uw pakketten al zijn geconfigureerd met **EncryptSensitiveWithPassword**/**EncryptAllWithPassword-beveiligingsniveaus,** blijven ze ongewijzigd, maar gebruiken we nog steeds willekeurig gegenereerde wachtwoorden wanneer we uw pakketten uploaden naar Azure-bestanden voor uitvoering op uw Azure-SSIS IR.

### <a name="using-package-configuration-file"></a>Pakketconfiguratiebestand gebruiken
Als u pakketconfiguratiebestanden in het pakketimplementatiemodel gebruikt om variabele waarden te wijzigen tijdens uitvoering, uploaden we deze bestanden met uw pakketten automatisch naar Azure-bestanden voor uitvoering op uw Azure-SSIS IR.

### <a name="using-execute-package-task"></a>Pakkettaak uitvoeren gebruiken
Als uw pakketten Pakkettaken uitvoeren bevatten die verwijzen naar andere pakketten die zijn opgeslagen op lokale bestandssystemen, moet u de volgende aanvullende instellingen uitvoeren:

1. Upload de andere pakketten naar Azure Files onder hetzelfde Azure Storage-account dat is verbonden met uw projecten en ontvang hun nieuwe UNC-pad, bijvoorbeeld.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Het bestandspad van die andere pakketten vervangen in bestandsverbindingsbeheer van pakkettaken uitvoeren door hun nieuwe UNC-pad
   - Als uw machine met SSDT geen toegang heeft tot het nieuwe UNC-pad, u het bestandspad wijzigen in het deelvenster Eigenschappen van Bestandsverbindingsbeheer
   - U ook een variabele voor het bestandspad gebruiken om de juiste waarde toe te wijzen tijdens het uitvoeren van

Als uw pakketten Pakkettaken uitvoeren bevatten die verwijzen naar andere pakketten in hetzelfde project, is er geen extra installatie nodig.

## <a name="next-steps"></a>Volgende stappen
Zodra u tevreden bent met het uitvoeren van uw pakketten in Azure vanuit SSDT, u ze implementeren en uitvoeren als SSIS-pakketactiviteiten uitvoeren in ADF-pijplijnen, zie [SSIS-pakketten uitvoeren als SSIS-pakketactiviteiten uitvoeren in ADF-pijplijnen.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
