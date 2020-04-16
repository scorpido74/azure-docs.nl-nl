---
title: Runtime azure-SSIS-integratie plannen
description: In dit artikel wordt beschreven hoe u de runtime voor het starten en stoppen van Azure-SSIS-integratie inplannen met Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 8/2/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 39f758b779e7c4935feab2424be16b829db8e46b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399519"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Azure-SSIS Integration Runtime gepland starten en stoppen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u de start en het stoppen van Azure-SSIS Integration Runtime (IR) inplant met behulp van Azure Data Factory (ADF). Azure-SSIS IR is ADF compute resource die is gebruikt voor het uitvoeren van SQL Server Integration Services (SSIS)-pakketten. Het uitvoeren van Azure-SSIS IR heeft een kosten verbonden aan het. Daarom wilt u uw IR meestal alleen uitvoeren wanneer u SSIS-pakketten in Azure moet uitvoeren en uw IR moet stoppen wanneer u deze niet meer nodig hebt. U de ADF-gebruikersinterface (UI)/app of Azure PowerShell gebruiken om uw IR handmatig te [starten of te stoppen).](manage-azure-ssis-integration-runtime.md)

U ook webactiviteiten maken in ADF-pijplijnen om uw IR op schema te starten/te stoppen, bijvoorbeeld om deze 's ochtends te starten voordat u uw dagelijkse ETL-workloads uitvoert en deze 's middags stopt nadat ze zijn voltooid.  U ook een SSIS-pakketactiviteit uitvoeren tussen twee webactiviteiten die uw IR starten en stoppen, zodat uw IR op aanvraag start/stopt, net op tijd voor/na uw pakketuitvoering. Zie [Een SSIS-pakket uitvoeren met ssis-pakketactiviteit uitvoeren in het ADF-pijplijnartikel](how-to-invoke-ssis-package-ssis-activity.md) voor meer informatie over activiteiten in het SSIS-pakket uitvoeren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten
Als u uw Azure-SSIS IR nog niet hebt ingericht, moet u deze inrichten door instructies in de zelfstudie te [volgen.](tutorial-create-azure-ssis-runtime-portal.md) 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>ADF-pijplijnen maken en plannen die Azure-SSIS IR starten en of stoppen
In deze sectie ziet u hoe u webactiviteiten in ADF-pijplijnen gebruiken om uw Azure-SSIS IR op schema te starten/stoppen of te beginnen & op aanvraag te stoppen. Wij begeleiden u bij het maken van drie pijpleidingen: 

1. De eerste pijplijn bevat een webactiviteit waarmee uw Azure-SSIS IR wordt gestart. 
2. De tweede pijplijn bevat een webactiviteit die uw Azure-SSIS IR stopt.
3. De derde pijplijn bevat een activiteit uitvoeren SSIS-pakket die is geketend tussen twee webactiviteiten die uw Azure-SSIS IR starten/stoppen. 

Nadat u deze pijplijnen hebt gemaakt en getest, u een planningstrigger maken en deze koppelen aan elke pijplijn. De planningstrigger definieert een planning voor het uitvoeren van de bijbehorende pijplijn. 

U bijvoorbeeld twee triggers maken, de eerste wordt dagelijks om 6 uur 's 6 uur uitgevoerd en gekoppeld aan de eerste pijplijn, terwijl de tweede wordt uitgevoerd om dagelijks om 18.00 uur en gekoppeld aan de tweede pijplijn.  Op deze manier heeft u elke dag een periode tussen 6.00 en 18.00 uur wanneer uw IR wordt uitgevoerd, klaar om uw dagelijkse ETL-workloads uit te voeren.  

Als u een derde trigger maakt die dagelijks om middernacht wordt uitgevoerd en die is gekoppeld aan de derde pijplijn, wordt die pijplijn elke dag om middernacht uitgevoerd, wordt uw IR gestart vlak voor de uitvoering van het pakket, vervolgens het uitvoeren van uw pakket en onmiddellijk stoppen van uw IR net na de uitvoering van het pakket, zodat uw IR niet werkeloos wordt uitgevoerd.

### <a name="create-your-adf"></a>Uw ADF maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).    
2. Klik op **Nieuw** in het linkermenu en klik vervolgens op **Gegevens en analyses** en **Data Factory**. 
   
   ![Nieuw -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Typ **myAzureSsisDataFactory** voor **naam**op de pagina **Nieuwe gegevensfabriek** . 
      
   ![Pagina Nieuwe gegevensfactory](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   De naam van uw ADF moet wereldwijd uniek zijn. Als u de volgende fout ontvangt, wijzigt u de naam van uw ADF (bijvoorbeeld uw naamMyAzureSsisDataFactory) en probeert u deze opnieuw te maken. Zie [artikel Gegevensfabriek - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor ADF-artefacten.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Selecteer uw **Azure-abonnement** waaronder u uw ADF wilt maken. 
5. Ga **voor Resourcegroep**naar een van de volgende stappen:
     
   - Selecteer **Bestaande gebruiken**en selecteer een bestaande resourcegroep in de vervolgkeuzelijst. 
   - Selecteer **Nieuw maken**en voer de naam van uw nieuwe resourcegroep in.   
         
   Zie [Resourcegroepen gebruiken om uw Azure-bronnenartikel te beheren](../azure-resource-manager/management/overview.md) voor meer informatie over resourcegroepen.
   
6. Selecteer **V2** voor **versie**.
7. Selecteer **bij Locatie**een van de locaties die worden ondersteund voor ADF-creatie in de vervolgkeuzelijst.
8. Selecteer **Vastmaken aan dashboard**.     
9. Klik **op Maken**.
10. Op het Azure-dashboard ziet u de volgende tegel met status: **Gegevensfabriek implementeren**. 

    ![tegel met de status 'gegevensfactory implementeren'](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Nadat de aanmaak is voltooid, u uw ADF-pagina zien zoals hieronder weergegeven.
   
    ![Startpagina van de gegevensfactory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Klik **op Auteur &-monitor** om de ADF-gebruikersinterface/-app op een apart tabblad te starten.

### <a name="create-your-pipelines"></a>Maak uw pijplijnen

1. Selecteer in De pagina Laten **beginnen** de optie **Pijplijn maken**. 

   ![Pagina Aan de slag](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. Vouw in **de gereedschapsset Activiteiten** het menu **Algemeen** uit en sleep & een **webactiviteit** op het oppervlak van de pijplijnontwerper neer te zetten. Wijzig in het tabblad **Algemeen** van het venster activiteitseigenschappen de activiteitsnaam in **startMyIR**. Ga naar het tabblad **Instellingen** en doe de volgende acties.

    1. Voer **voor URL**de volgende URL voor REST API in waarmee `{subscriptionId}` `{resourceGroupName}`Azure-SSIS IR wordt gestart, waarbij u, de `{factoryName}`werkelijke waarden voor uw IR, vervangt en `{integrationRuntimeName}` de werkelijke waarden voor uw IR vervangt: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` U ook de bron-id van uw IR kopiëren & plakken vanaf de controlepagina op ADF UI/app om het volgende deel van de bovenstaande URL te vervangen:`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ADF SSIS IR-bron-id](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. Selecteer **POST**voor **Methode**. 
    3. Voor **lichaam,** voer `{"message":"Start my IR"}`. 
    4. Zie [Beheerde identiteit voor het](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) artikel Gegevensfabriek voor **verificatie**, selecteer **MSI** om de beheerde identiteit voor uw ADF te gebruiken voor meer informatie.
    5. Voer **voor** `https://management.azure.com/`Resource .
    
       ![AdF-webactiviteitsschema SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Kloon de eerste pijplijn om een tweede te maken, waarbij de activiteitsnaam wordt gewijzigd om **MyIR** te stoppen en de volgende eigenschappen te vervangen.

    1. Voer **voor URL**de volgende URL voor REST API in waarmee `{subscriptionId}` `{resourceGroupName}`Azure-SSIS IR wordt gestopt, waarbij de `{factoryName}` `{integrationRuntimeName}` werkelijke waarden voor uw IR worden vervangen door de werkelijke waarden:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. Voor **lichaam,** voer `{"message":"Stop my IR"}`. 

4. Maak een derde pijplijn, sleep & een **Activiteit SSIS-pakket uitvoeren** van **activiteiten** op het oppervlak van de pijplijnontwerper te laten vallen en configureer deze volgens de instructies in [Een SSIS-pakket aanroepen met de activiteit SSIS-pakket uitvoeren in het ADF-artikel.](how-to-invoke-ssis-package-ssis-activity.md)  U ook een **activiteit Opgeslagen procedure** gebruiken en deze configureren volgens de instructies in Een [SSIS-pakket aanroepen met behulp van Activiteit Opgeslagen procedure in het ADF-artikel.](how-to-invoke-ssis-package-stored-procedure-activity.md)  Chain vervolgens de activiteit SSIS-pakket/opgeslagen procedure uitvoeren tussen twee webactiviteiten die uw IR starten/stoppen, vergelijkbaar met die webactiviteiten in de eerste/tweede pijplijn.

   ![ADF-webactiviteit op aanvraag SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Wijs de beheerde identiteit voor uw ADF een **inzenderrol** toe aan zichzelf, zodat webactiviteiten in de pijplijnen REST API kunnen aanroepen om Azure-SSIS IRs te starten/stoppen die daarin zijn ingericht.  Klik op uw ADF-pagina in Azure-portal op **Toegangsbeheer (IAM),** klik **op + Roltoewijzing toevoegen**en voer vervolgens op **Roltoewijzingsblad toevoegen** de volgende acties uit.

    1. Selecteer Voor **Rol** **de optie Bijdrager**. 
    2. Selecteer Azure AD-gebruiker, -groep of serviceprincipal **voor Toegang toewijzen**tot , selecteer **Azure AD-gebruiker, -groep of serviceprincipal**. 
    3. Zoek **voor Selecteren**naar uw ADF-naam en selecteer deze. 
    4. Klik op **Opslaan**.
    
   ![AdF-taaktaak voor beheerde identiteitsrollen](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Valideer uw ADF en alle pijplijninstellingen door op **Alles valideren/valideren** op de werkbalk fabriek/pijplijn te klikken. Sluit **Fabrieks-/pijplijnvalidatieuitvoer** **>>** door op knop te klikken.  

   ![Pijplijn valideren](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Test uw pijplijnen uit te voeren

1. Selecteer **Testrun** op de werkbalk voor elke pijplijn en zie **Uitvoervenster** in het onderste deelvenster. 

   ![Testrun](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Als u de derde pijplijn wilt testen, start u SQL Server Management Studio (SSMS). Ga in het venster **Verbinding maken met server** de volgende acties uit. 

    1. Voer **voor Servernaam** ** &lt;de naam&gt;van de Azure SQL Database-server in.database.windows.net**.
    2. Selecteer **Opties >>**.
    3. Selecteer **SSISDB** **voor Verbinding maken met database**.
    4. Selecteer **Verbinden**. 
    5. **Integratieservicescatalogi** -> **ssisdb** -> uw map -> **projecten** -> uw SSIS-project -> **Pakketten**uit. 
    6. Klik met de rechtermuisknop op het opgegeven SSIS-pakket om -> **alle uitvoeringen**uit te voeren en selecteer -> **Standaardrapporten**rapporten. **Reports** 
    7. Controleer of het is uitgevoerd. 

   ![SSIS-pakket uitvoeren controleren](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Uw pijplijnen plannen

Nu uw pijplijnen werken zoals u had verwacht, u triggers maken om ze uit te voeren op opgegeven cadans. Zie [De pijplijn activeren in een planningsartikel](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) voor meer informatie over het koppelen van triggers aan pijplijnen.

1. Selecteer op de werkbalk van de pijplijn De optie **Activeren** en selecteer **Nieuw/Bewerken**. 

   ![Trigger -> Nieuw/Bewerken](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. Selecteer in het deelvenster **Triggers toevoegen** de optie **+ Nieuw**.

   ![Triggers toevoegen - Nieuw](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. Ga in het deelvenster **Nieuwe trigger** de volgende acties uitvoeren: 

    1. Voer voor **Naam**een naam in voor de trigger. In het volgende voorbeeld is **Dagelijks uitvoeren** de triggernaam. 
    2. Selecteer Voor **Tekst**de optie **Planning**. 
    3. Voer **voor begindatum (UTC)** een begindatum en -tijd in in UTC. 
    4. Voer voor **Herhaling**een cadans in voor de trigger. In het volgende voorbeeld is het **eenmaal Dagelijks.** 
    5. Selecteer Voor **Einde** **Geen einde** of voer een einddatum en -tijd in nadat u Op **datum hebt**geselecteerd . 
    6. Selecteer **Geactiveerd** om de trigger direct nadat u de hele ADF-instellingen hebt gepubliceerd, te activeren. 
    7. Selecteer **Next**.

   ![Trigger -> Nieuw/Bewerken](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Controleer op de pagina **Parameters activeren** elke waarschuwing en selecteer **Voltooien**. 
5. Publiceer de hele ADF-instellingen door **Alles publiceren** te selecteren op de werkbalk van de fabriek. 

   ![Alles publiceren](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Uw pijplijnen en triggers in Azure-portal bewaken

1. Als u triggerruns en pijplijnuitvoeringen wilt controleren, gebruikt u het tabblad **Monitor** links van de ADF-gebruikersinterface/-app. Zie [Het pijplijnartikel controleren](quickstart-create-data-factory-portal.md#monitor-the-pipeline) voor gedetailleerde stappen.

   ![Pijplijnuitvoeringen](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Als u de activiteitsruns wilt weergeven die zijn gekoppeld aan een pijplijnrun, selecteert u de eerste koppeling **(Activiteitsuitvoeringen weergeven)** in de kolom **Acties.** Voor de derde pijplijn ziet u drie activiteitsruns, één voor elke geketende activiteit in de pijplijn (webactiviteit om uw IR, opgeslagen procedureactiviteit om uw pakket uit te voeren en webactiviteit om uw IR te stoppen). Als u de pijplijnweeruitvoeringen opnieuw wilt weergeven, selecteert u bovenaan de koppeling **Pijplijnen.**

   ![Uitvoering van activiteiten](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Als u de triggerruns wilt weergeven, selecteert u **Trigger Runs** in de vervolgkeuzelijst onder **Pijplijnuitvoeringen** bovenaan. 

   ![Triggeruitvoeringen](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Uw pijplijnen en triggers bewaken met PowerShell

Gebruik scripts zoals de volgende voorbeelden om uw pijplijnen en triggers te controleren.

1. Krijg de status van een pijplijnrun.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Krijg informatie over een trigger.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Krijg de status van een trigger run.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Azure Automation-runbook maken en plannen waarmee Azure-SSIS IR wordt gestart/gestopt

In deze sectie leert u Azure Automation-runbook maken waarmee PowerShell-script wordt uitgevoerd, waarbij uw Azure-SSIS IR volgens een planning wordt gestart/gestopt.  Dit is handig wanneer u extra scripts wilt uitvoeren voor/na het starten/stoppen van uw IR voor pre/post-processing.

### <a name="create-your-azure-automation-account"></a>Uw Azure Automation-account maken

Als u nog geen Azure Automation-account hebt, maakt u er een door de instructies in deze stap te volgen. Zie Een azure [automation-accountartikel maken](../automation/automation-quickstart-create-account.md) voor gedetailleerde stappen. Als onderdeel van deze stap maakt u een **Azure Run As-account** (een serviceprincipal in uw Azure Active Directory) en wijst u deze een **inzenderrol** toe in uw Azure-abonnement. Zorg ervoor dat het hetzelfde abonnement is dat uw ADF bevat met Azure SSIS IR. Azure Automation gebruikt dit account om te verifiëren bij Azure Resource Manager en om uw resources te gebruiken. 

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Momenteel wordt ADF UI/app alleen ondersteund in microsoft edge- en Google Chrome-webbrowsers.
2. Meld u aan bij de [Azure-portal](https://portal.azure.com/).    
3. Selecteer **Nieuw** in het linkermenu, selecteer **Monitoring + Beheer**en selecteer **Automatisering**. 

   ![Nieuwe -> Monitoring + Management -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. Ga in het deelvenster **Automatiseringsaccount toevoegen** de volgende acties uit.

    1. Voer voor **Naam**een naam in voor uw Azure Automation-account. 
    2. Selecteer **bij Abonnement**het abonnement met uw ADF met Azure-SSIS IR. 
    3. Selecteer **Voor resourcegroep** **Nieuw maken** om een nieuwe resourcegroep te maken of Bestaand **gebruiken** om een bestaande groep te selecteren. 
    4. Selecteer **voor Locatie**een locatie voor uw Azure Automation-account. 
    5. Bevestig **Azure Run As-account maken** als **Ja**. Er wordt een serviceprincipal gemaakt in uw Azure Active Directory en een **bijdrager** toegewezen aan uw Azure-abonnement.
    6. Selecteer **Vastmaken aan dashboard** om het permanent weer te geven in het Azure-dashboard. 
    7. Selecteer **Maken**. 

   ![Nieuwe -> Monitoring + Management -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. U ziet de implementatiestatus van uw Azure Automation-account in azure-dashboard en meldingen. 
    
   ![Automatisering implementeren](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. U ziet de startpagina van uw Azure Automation-account nadat deze is gemaakt. 

   ![Startpagina voor automatisering](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>ADF-modules importeren

1. Selecteer **Modules** in de sectie **GEDEELDE RESOURCES** in het linkermenu en controleer of u **Az.DataFactory** + **Az.Profile** in de lijst met modules hebt.

   ![Controleer de vereiste modules](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Als u geen **Az.DataFactory**hebt, gaat u naar de PowerShell Gallery for [Az.DataFactory-module,](https://www.powershellgallery.com/packages/Az.DataFactory/)selecteert **u Implementeren in Azure Automation,** selecteert u uw Azure Automation-account en selecteert u **OK**. Ga terug naar de weergave **van modules** in de sectie **GEDEELDE RESOURCES** in het linkermenu en wacht tot u de module **STATUS** van **Az.DataFactory** hebt gewijzigd in **Beschikbaar.**

    ![De module Gegevensfabriek verifiëren](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Als u geen **Az.Profile hebt,** gaat u naar de module PowerShell-galerie voor [Az.Profile,](https://www.powershellgallery.com/packages/Az.profile/)selecteert **u Implementeren in Azure Automation,** selecteert u uw Azure Automation-account en selecteert u **OK**. Ga terug naar de weergave **van modules** in de sectie **GEDEELDE RESOURCES** in het linkermenu en wacht tot u de **status** van de module **Az.Profile** hebt gewijzigd in **Beschikbaar**.

    ![De profielmodule verifiëren](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Uw PowerShell-runbook maken

In de volgende sectie vindt u stappen voor het maken van een PowerShell-runbook. Het script dat aan uw runbook is gekoppeld, start/stopt Azure-SSIS IR op basis van de opdracht die u opgeeft voor de parameter **OPERATION.** In deze sectie worden niet de volledige details gegeven voor het maken van een runbook. Zie [Een runbook-artikel maken](../automation/automation-quickstart-create-runbook.md) voor meer informatie.

1. Ga naar het tabblad **Runbooks** en selecteer **+ Voeg een runbook toe** vanaf de werkbalk. 

   ![Knop Een runbook toevoegen](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Selecteer **Een nieuw runbook maken** en voer de volgende acties uit: 

    1. Voer Voor **Naam** **StartStopAzureSsisRuntime**in .
    2. Selecteer **PowerShell** **voor runbook-type**.
    3. Selecteer **Maken**.
    
   ![Knop Een runbook toevoegen](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Kopieer & plak het volgende PowerShell-script naar het scriptvenster van de runbook. Sla uw runbook op en publiceer deze met de knoppen **Opslaan** en **publiceren** op de werkbalk. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![PowerShell-runbook bewerken](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Test uw runbook door de knop **Start** op de werkbalk te selecteren. 

   ![Knop Runbook starten](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. Voer in het deelvenster **Runbook starten** de volgende acties uit: 

    1. Voer voor naam van **resourcegroep**de naam in van de resourcegroep met uw ADF met Azure-SSIS IR. 
    2. Voer voor de naam van **de gegevensfabriek**de naam van uw ADF in met Azure-SSIS IR. 
    3. Voer voor **AZURESSISNAME**de naam Azure-SSIS IR in. 
    4. Voer **START**in voor **BEWERKING**. 
    5. Selecteer **OK**.  

   ![Runbook-venster starten](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. **Selecteer** uitvoertegel in het taakvenster. Wacht in het uitvoervenster op het bericht **##### Voltooid #####** nadat u **##### Starting ##### ziet.** Het starten van Azure-SSIS IR duurt ongeveer 20 minuten. Sluit **het venster Taak** en ga terug naar het venster **Runbook.**

   ![Azure SSIS IR - gestart](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Herhaal de vorige twee stappen met **STOP** als waarde voor **BEWERKING**. Start uw runbook opnieuw door de knop **Start** op de werkbalk te selecteren. Voer de namen van uw resourcegroep, ADF en Azure-SSIS IR in. Voer **STOP**in voor **BEWERKING**. Wacht in het uitvoervenster op het bericht **##### Voltooid #####** nadat u **##### Stopt ##### ziet.** Het stoppen van Azure-SSIS IR duurt niet zo lang als het starten ervan. Sluit **het venster Taak** en ga terug naar het venster **Runbook.**

8. U uw runbook ook activeren via een webhook die kan worden gemaakt door het **menu-item Webhooks** te selecteren of op een schema dat kan worden gemaakt door het **menu-item Schema's** te selecteren zoals hieronder is aangegeven.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Schema's maken voor uw runbook om Azure-SSIS IR te starten/stoppen

In de vorige sectie hebt u uw Azure Automation-runbook gemaakt waarmee Azure-SSIS IR kan worden gestart of gestopt. In deze sectie maakt u twee schema's voor uw runbook. Wanneer u het eerste schema configureert, geeft u **START** for **OPERATION**op. Op dezelfde manier geeft u bij het configureren van de tweede **functie STOP** op voor **BEWERKING**. Zie [Een planningsartikel maken](../automation/shared-resources/schedules.md#creating-a-schedule) voor gedetailleerde stappen om schema's te maken.

1. Selecteer In het venster **Runbook** de optie **Schema's**en selecteer **+ Een schema toevoegen** op de werkbalk. 

   ![Azure SSIS IR - gestart](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. Voer in het deelvenster **Runbook plannen** de volgende acties uit: 

    1. Selecteer **Een planning koppelen aan uw runbook**. 
    2. Selecteer **Een nieuw schema maken**.
    3. Voer in het deelvenster **Nieuwe planning** **dagelijks IR starten** in voor **Naam**. 
    4. Voer **bij Start**een tijd in die een paar minuten na de huidige tijd ligt. 
    5. Selecteer **Terugkerende**optie Voor **Herhaling**. 
    6. Voer voor **Elke Recur** **1** in en selecteer **Dag**. 
    7. Selecteer **Maken**. 

   ![Planning voor Azure SSIS IR start](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Schakel over naar het tabblad **Parameters en voer instellingen uit.** Geef de namen resourcegroep, ADF en Azure-SSIS IR op. Voer VOOR **BEWERKING** **START** in en selecteer **OK**. Selecteer **opnieuw OK** om de planning op de pagina **Schema's** van uw runbook weer te geven. 

   ![Schema voor het staren naar de Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Herhaal de vorige twee stappen om dagelijks een schema met de naam **Stop IR te**maken. Voer een tijd in die ten minste 30 minuten na de opgegeven tijd voor **het dagelijks schema starten van IR** is opgegeven. Voer VOOR **BEWERKING** **STOPPEN** in en selecteer **OK**. Selecteer **opnieuw OK** om de planning op de pagina **Schema's** van uw runbook weer te geven. 

5. Selecteer In het venster **Runbook** de optie **Taken** in het linkermenu. U ziet de vacatures die zijn gemaakt door uw schema's op de opgegeven tijdstippen en hun statussen. U de taakgegevens, zoals de uitvoer, zien die vergelijkbaar zijn met wat u hebt gezien nadat u uw runbook hebt getest. 

   ![Schema voor het staren naar de Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Nadat u klaar bent met testen, schakelt u uw schema's uit door ze te bewerken. Selecteer **Schema's** in het linkermenu, selecteer **IR dagelijks starten/IR stoppen dagelijks**en selecteer **Nee** voor **Ingeschakeld**. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende blogpost:
-   [Uw ETL/ELT-workflows moderniseren en uitbreiden met SSIS-activiteiten in ADF-pijplijnen](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Raadpleeg de volgende artikelen uit de SSIS-documentatie: 

- [Deploy, run, and monitor an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) (SSIS-pakket implementeren, uitvoeren en bewaken in Azure)   
- [Connect to SSIS catalog on Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) (Verbinding maken met een SSIS-catalogus in Azure)
- [Schedule package execution on Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) (Pakketuitvoering plannen in Azure)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Verbinding maken met on-premises gegevensbronnen met Windows-verificatie)
