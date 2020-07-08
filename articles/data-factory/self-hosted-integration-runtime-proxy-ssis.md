---
title: Een zelf-hostende Integration runtime configureren als proxy voor SSIS
description: Meer informatie over het configureren van een zelf-hostende Integration runtime als een proxy voor een Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 4cb5b84f3889dcf4e0f28d525afb42cfeac5b54c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81605499"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Een zelf-hostende IR configureren als proxy voor een Azure-SSIS IR in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u SQL Server Integration Services (SSIS)-pakketten uitvoert op een Azure-SSIS Integration Runtime (Azure-SSIS IR) in Azure Data Factory met een zelf-hostende Integration runtime (zelf-hostende IR) die is geconfigureerd als proxy. 

Met deze functie kunt u on-premises toegang krijgen tot gegevens zonder dat u [uw Azure-SSIS IR hoeft te koppelen aan een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). De functie is handig wanneer uw bedrijfs netwerk een configuratie te complex heeft of een beleid dat te beperkend is voor u om uw Azure-SSIS IR te injecteren.

Met deze functie wordt een SSIS-gegevens stroom taak met een on-premises gegevens bron gesplitst in twee faserings taken: 
* De eerste taak, die wordt uitgevoerd op uw zelf-hostende IR, verplaatst eerst gegevens van de on-premises gegevens bron naar een faserings gebied in uw Azure Blob-opslag.
* De tweede taak, die wordt uitgevoerd op uw Azure-SSIS IR, verplaatst gegevens van het faserings gebied naar de gewenste gegevens bestemming.

Met andere voor delen en mogelijkheden van deze functie kunt u bijvoorbeeld uw zelf-hostende IR instellen in regio's die nog niet door een Azure-SSIS IR worden ondersteund, en het open bare statische IP-adres van uw zelf-hostende IR toestaan op de firewall van uw gegevens bronnen.

## <a name="prepare-the-self-hosted-ir"></a>De zelf-hostende IR voorbereiden

Als u deze functie wilt gebruiken, maakt u eerst een data factory en stelt u een Azure-SSIS IR in. Als u dit nog niet hebt gedaan, volgt u de instructies in [een Azure-SSIS IR instellen](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Vervolgens stelt u uw zelf-hostende IR in op dezelfde data factory waar uw Azure-SSIS IR is ingesteld. Zie hiervoor [een zelf-hostende IR maken](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

Ten slotte downloadt en installeert u de nieuwste versie van de zelf-hostende IR, evenals de extra Stuur Programma's en runtime op uw on-premises machine of virtuele Azure-machine (VM), als volgt:
- Down load en installeer de nieuwste versie van de [zelf-hostende IR](https://www.microsoft.com/download/details.aspx?id=39717).
- Als u Object Linking and Embedding data base (OLEDB)-connectors in uw pakketten gebruikt, downloadt en installeert u de relevante OLEDB-Stuur Programma's op dezelfde computer waarop uw zelf-hostende IR is geïnstalleerd, als u dat nog niet hebt gedaan.  

  Als u de eerdere versie van het OLEDB-stuur programma voor SQL Server (SQL Server Native Client [SQLNCLI]) gebruikt, [downloadt u de 64-bits versie](https://www.microsoft.com/download/details.aspx?id=50402).  

  Als u de nieuwste versie van OLEDB-stuur programma voor SQL Server (MSOLEDBSQL) gebruikt, [downloadt u de 64-bits versie](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Als u OLEDB-Stuur Programma's gebruikt voor andere database systemen, zoals PostgreSQL, MySQL, Oracle, enzovoort, kunt u de 64-bits versies downloaden van hun websites.
- Als u dit nog niet hebt gedaan, [downloadt en installeert u de 64-bits versie van de Visual C++ (VC) runtime](https://www.microsoft.com/download/details.aspx?id=40784) op dezelfde computer waarop uw zelf-hostende IR is geïnstalleerd.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>De aan Azure Blob Storage gekoppelde service voorbereiden voor fase ring

Als u dit nog niet hebt gedaan, maakt u een aan Azure Blob Storage gekoppelde service op hetzelfde data factory waar uw Azure-SSIS IR is ingesteld. Zie hiervoor [een Azure Data Factory-gekoppelde service maken](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Zorg ervoor dat u het volgende doet:
- Selecteer **Azure Blob Storage**voor **gegevens opslag**.  
- Voor **verbinden via Integration runtime**selecteert u **AutoResolveIntegrationRuntime** (niet uw Azure-SSIS IR en uw zelf-hostende IR), omdat we de standaard Azure IR gebruiken om toegangs referenties voor uw Azure Blob Storage op te halen.
- Selecteer voor **verificatie methode** **account sleutel**, **SAS-URI**of service- **Principal**.  

    >[!TIP]
    >Als u de **Service-Principal** -methode selecteert, geeft u de Service-Principal ten minste een rol voor *Storage BLOB data Inzender*   . Zie [Azure Blob Storage-connector](connector-azure-blob-storage.md#linked-service-properties)voor meer informatie.

![De aan Azure Blob Storage gekoppelde service voorbereiden voor fase ring](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Een Azure-SSIS IR met uw zelf-hostende IR configureren als proxy

Als u uw zelf-hostende IR-en Azure Blob Storage-gekoppelde service voor fase ring hebt voor bereid, kunt u nu uw nieuwe of bestaande Azure-SSIS IR configureren met de zelf-hostende IR als proxy in uw data factory-portal of-app. Voordat u dit doet, moet u, als uw bestaande Azure-SSIS IR al wordt uitgevoerd, stoppen en opnieuw starten.

1. In het deel venster **Setup van Integration runtime** gaat u verder met de secties **algemene instellingen** en **SQL-instellingen** door **volgende**te selecteren. 

1. Ga als volgt te werk in de sectie **Geavanceerde instellingen** :

   1. Selecteer het selectie vakje **zelf-hostende Integration runtime als proxy voor uw Azure-SSIS Integration runtime instellen** . 

   1. Selecteer in de vervolg keuzelijst **zelf-hostende Integration runtime** uw bestaande zelf-hostende IR als proxy voor de Azure-SSIS IR.

   1. Selecteer in de vervolg keuzelijst **faserings opslag gekoppelde service** uw bestaande Azure Blob Storage-gekoppelde service of maak een nieuwe voor fase ring.

   1. Geef in het vak **pad voor gefaseerde installatie** een BLOB-container op in het geselecteerde Azure Blob Storage-account of laat het veld leeg om een standaard waarde te gebruiken voor fase ring.

   1. Selecteer **Doorgaan**.

   ![Geavanceerde instellingen met een zelf-hostende IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

U kunt uw nieuwe of bestaande Azure-SSIS IR ook configureren met de zelf-hostende IR als een proxy met behulp van Power shell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>SSIS-pakketten inschakelen om verbinding te maken via proxy

Door de nieuwste SSDT met SSIS projects-extensie voor Visual Studio of een zelfstandig installatie programma te gebruiken, kunt u een nieuwe `ConnectByProxy` eigenschap vinden die is toegevoegd in OLEDB-of plat file Connection-beheer.
* [De SSDT met SSIS projects-extensie voor Visual Studio downloaden](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Het zelfstandige installatie programma downloaden](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Wanneer u nieuwe pakketten ontwerpt die gegevens stroom taken met OLEDB-of platte bestands bronnen bevatten, waarmee u toegang krijgt tot data bases of bestanden op locatie, kunt u deze eigenschap inschakelen door deze in te stellen op *True* in het deel venster **Eigenschappen** van de relevante verbindings beheer Programma's.

![De eigenschap ConnectByProxy inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

U kunt deze eigenschap ook inschakelen wanneer u bestaande pakketten uitvoert, zonder dat u deze hand matig hoeft te wijzigen.  Er zijn twee opties:
- **Optie A**: open, Rebuild en implementeer het project met de pakketten met de meest recente SSDT om uit te voeren op uw Azure-SSIS IR. U kunt de eigenschap vervolgens inschakelen door deze in te stellen op *True* voor de relevante verbindings beheer Programma's. Wanneer er pakketten worden uitgevoerd vanaf SSMS, worden deze verbindings beheer weer gegeven op het tabblad **verbindings beheer** van het pop-upvenster **pakket uitvoeren** .

  ![ConnectByProxy property2 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  U kunt de eigenschap ook inschakelen door deze in te stellen op *True* voor de relevante verbindings beheer die wordt weer gegeven op het tabblad **verbindings beheer** van de [activiteit uitvoeren SSIS-pakket](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) wanneer deze pakketten in Data Factory pijp lijnen worden uitgevoerd.
  
  ![ConnectByProxy property3 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Optie B:** Implementeer het project met de pakketten opnieuw om het uit te voeren op uw SSIS-IR. U kunt de eigenschap vervolgens inschakelen door het pad naar de eigenschap op te geven, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` en deze in te stellen op *True* als eigenschaps onderdrukking op het tabblad **Geavanceerd** van het pop-upvenster **pakket uitvoeren** wanneer u pakketten uit SSMS uitvoert.

  ![ConnectByProxy property4 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  U kunt de eigenschap ook inschakelen door het pad naar de eigenschap op te geven, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` en deze in te stellen op *True* als eigenschaps onderdrukking op het tabblad **Eigenschappen overschrijft** van de [activiteit uitvoering van SSIS-pakket](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) wanneer u pakketten uitvoert in Data Factory pijp lijnen.
  
  ![ConnectByProxy property5 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Fouten opsporen in de eerste en tweede faserings taken

Op uw zelf-hostende IR vindt u de runtime-Logboeken in de map *C:\ProgramData\SSISTelemetry* en de uitvoerings logboeken van de eerste fase ring van taken in de map *C:\ProgramData\SSISTelemetry\ExecutionLog* .  U kunt de uitvoerings logboeken van de tweede faserings taken vinden in uw SSISDB of opgegeven logboek registratie paden, afhankelijk van of u uw pakketten opslaat in SSISDB of bestands systeem, bestands shares of Azure Files. U kunt ook de unieke Id's van de eerste faserings taken vinden in de uitvoerings logboeken van de tweede faserings taken. 

![Unieke ID van de eerste faserings taak](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Windows-verificatie gebruiken in faserings taken

Als voor de faserings taken op uw zelf-hostende IR Windows-verificatie is vereist, [configureert u uw SSIS-pakketten voor gebruik van dezelfde Windows-verificatie](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Uw faserings taken worden aangeroepen met het zelf-hostende IR-service account (standaard*NT SERVICE\DIAHostService*) en uw gegevens archieven worden geopend met het Windows-verificatie account. Voor beide accounts moet een bepaald beveiligings beleid worden toegewezen. Op de zelf-hostende IR-computer gaat u naar **lokaal**  >  **beleid lokale beleids regels**  >  **toewijzing van gebruikers rechten**en gaat u als volgt te werk:

1. Wijs de *geheugen quota voor het aanpassen van een proces* toe en *Vervang een token beleid op proces niveau* naar het zelf-hostende IR-service account. Dit moet automatisch gebeuren wanneer u uw zelf-hostende IR installeert met het standaard service account. Als dat niet het geval is, moet u dit beleid hand matig toewijzen. Als u een ander service account gebruikt, wijst u hieraan hetzelfde beleid toe.

1. Wijs het *Aanmelden als service* beleid toe aan het Windows-verificatie account.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Facturering voor de eerste en tweede staging-taken

De eerste faserings taken die worden uitgevoerd op uw zelf-hostende IR, worden afzonderlijk in rekening gebracht, net zoals activiteiten voor gegevens verplaatsing die worden uitgevoerd op een zelf-hostende IR. Dit is opgegeven in het artikel [Azure Data Factory gegevens pijplijn](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

De tweede faserings taken die op uw Azure-SSIS IR worden uitgevoerd, worden niet afzonderlijk in rekening gebracht, maar uw actieve Azure-SSIS IR wordt gefactureerd zoals is opgegeven in het artikel [Azure-SSIS IR prijzen](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="enabling-tls-12"></a>TLS 1.2 inschakelen

Als u een krachtig crypto grafie/veiliger netwerk protocol (TLS 1,2) moet gebruiken en oudere SSL/TLS-versies op uw zelf-hostende IR wilt uitschakelen, kunt u het *Main. cmd* -script downloaden en uitvoeren dat u kunt vinden in de map *CustomSetupScript/UserScenarios/TLS 1,2* van de open bare preview-container.  Met [Azure Storage Explorer](https://storageexplorer.com/)kunt u verbinding maken met onze open bare preview-container door de volgende SAS-URI in te voeren:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Huidige beperkingen

- Alleen gegevensstroom taken met Open Database Connectivity (ODBC)/OLEDB/Flat-bestands bronnen of OLEDB-bestemming worden momenteel ondersteund. 
- Er worden alleen Azure Blob Storage-gekoppelde services ondersteund die zijn geconfigureerd met de *account sleutel*, *Shared Access Signature (SAS) URI*of *Service-Principal* -verificatie.
- *ParameterMapping* in OLEDB-bron wordt nog niet ondersteund. Als tijdelijke oplossing gebruikt u *SQL-opdracht van variabele* als de *AccessMode* en gebruikt u de *expressie* om uw variabelen/para meters in een SQL-opdracht in te voegen. Zie het pakket *ParameterMappingSample. dtsx* dat u kunt vinden in de map *SelfHostedIRProxy/beperkingen* van onze open bare preview-container. Met Azure Storage Explorer kunt u verbinding maken met onze open bare preview-container door de bovenstaande SAS-URI in te voeren.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw zelf-hostende IR hebt geconfigureerd als proxy voor uw Azure-SSIS IR, kunt u uw pakketten implementeren en uitvoeren voor toegang tot on-premises gegevens als uitvoering van SSIS-pakket activiteiten in Data Factory-pijp lijnen. Zie [SSIS-pakketten uitvoeren als uitvoering van SSIS-pakket activiteiten in Data Factory pijp lijnen](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)voor meer informatie.
