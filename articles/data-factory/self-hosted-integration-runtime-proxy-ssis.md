---
title: Een zelf gehoste runtime voor integratie configureren als proxy voor SSIS
description: Meer informatie over het configureren van een zelf gehoste runtime voor integratie als proxy voor een runtime voor Azure-SSIS-integratie.
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
ms.openlocfilehash: ecfdf2a11f31c18064be9a607f2bb3938d26e661
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414897"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Een zelf gehoste IR configureren als proxy voor een Azure-SSIS IR in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u SQL Server Integration Services (SSIS)-pakketten uitvoert op een Azure-SSIS Integration Runtime (Azure-SSIS IR) in Azure Data Factory met een zelf gehoste runtime (self-hosted IR) die is geconfigureerd als proxy. 

Met deze functie hebt u on-premises toegang tot gegevens zonder dat [u uw Azure-SSIS IR hoeft aan te sluiten bij een virtueel netwerk.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) De functie is handig wanneer uw bedrijfsnetwerk een configuratie heeft die te complex is of een beleid dat te beperkend is om uw Azure-SSIS IR erin te injecteren.

Met deze functie wordt elke SSIS-gegevensstroomtaak met een on-premises gegevensbron opgesplitst in twee faseringstaken: 
* De eerste taak, die wordt uitgevoerd op uw zelf gehoste IR, verplaatst eerst gegevens van de on-premises gegevensbron naar een faseringsgebied in uw Azure Blob-opslag.
* De tweede taak, die wordt uitgevoerd op uw Azure-SSIS IR, verplaatst vervolgens gegevens van het faseringsgebied naar de beoogde gegevensbestemming.

Met andere voordelen en mogelijkheden van deze functie u bijvoorbeeld uw zelf gehoste IR instellen in regio's die nog niet worden ondersteund door een Azure-SSIS IR, en het openbare statische IP-adres van uw zelfgehoste IR toestaan op de firewall van uw gegevensbronnen.

## <a name="prepare-the-self-hosted-ir"></a>Bereid de zelfgehoste IR voor

Als u deze functie wilt gebruiken, maakt u eerst een gegevensfabriek en stelt u er een Azure-SSIS IR in. Als u dit nog niet hebt gedaan, volgt u de instructies in [Het instellen van een Azure-SSIS IR.](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

Vervolgens stelt u uw zelf gehoste IR in in dezelfde gegevensfabriek waar uw Azure-SSIS IR is ingesteld. Zie Hiervoor [een zelfgehoste IR maken.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)

Ten slotte downloadt en installeert u de nieuwste versie van de zelf gehoste IR, evenals de extra stuurprogramma's en runtime, op uw on-premises machine of Azure virtual machine (VM), als volgt:
- Download en installeer de nieuwste versie van de [zelf gehoste IR.](https://www.microsoft.com/download/details.aspx?id=39717)
- Als u OLEDB-connectors (Object Linking and Embedding Database) in uw pakketten gebruikt, download en installeer t u de relevante OLEDB-stuurprogramma's op dezelfde machine waar uw zelfgehoste IR is geïnstalleerd, als u dat nog niet hebt gedaan.  

  Als u de eerdere versie van het OLEDB-stuurprogramma voor SQL Server (SQL Server Native Client [SQLNCLI] gebruikt, [downloadt u de 64-bits versie.](https://www.microsoft.com/download/details.aspx?id=50402)  

  Als u de nieuwste versie van OLEDB-stuurprogramma voor SQL Server (MSOLEDBSQL) gebruikt, [downloadt u de 64-bits versie.](https://www.microsoft.com/download/details.aspx?id=56730)  
  
  Als u OLEDB-stuurprogramma's gebruikt voor andere databasesystemen, zoals PostgreSQL, MySQL, Oracle, enzovoort, u de 64-bits versies van hun websites downloaden.
- Als u dit nog niet hebt gedaan, [download en installeert u de 64-bits versie van Visual C++ (VC) runtime](https://www.microsoft.com/download/details.aspx?id=40784) op dezelfde machine waar uw zelf gehoste IR is geïnstalleerd.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>De Azure Blob-opslagservice voorbereiden voor fasering

Als u dit nog niet hebt gedaan, maakt u een Azure Blob-opslagservice in dezelfde gegevensfabriek waar uw Azure-SSIS IR is ingesteld. Zie Hiervoor [een azure-service met gegevensfabriek maken.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) Zorg ervoor dat u het volgende doet:
- Selecteer **Azure Blob Storage**voor **Gegevensarchief**.  
- Selecteer **AutoResolveIntegrationRuntime** (niet uw Azure-SSIS IR of uw zelfgehoste IR) **voor Verbinding maken via de runtime**van integratie, omdat we de standaard Azure IR gebruiken om toegangsreferenties voor uw Azure Blob Storage op te halen.
- Selecteer **Accountsleutel**, **SAS URI**of **Service Principal**voor **verificatiemethode**.  

    >[!TIP]
    >Als u de **methode Service Principal** selecteert, verleent u de serviceprincipal ten minste een rol *opslagblobgegevensbijdrager.*  Raadpleeg Azure [Blob-opslagconnector](connector-azure-blob-storage.md#linked-service-properties)voor meer informatie.

![De Azure Blob-opslagservice voorbereiden voor fasering](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Een Azure-SSIS IR configureren met uw zelfgehoste IR als proxy

Nadat u uw zelf gehoste IR- en Azure Blob-opslagservice hebt voorbereid voor fasering, u nu uw nieuwe of bestaande Azure-SSIS IR configureren met de zelf gehoste IR als proxy in uw portal of app voor gegevensfabrieken. Voordat u dit doet, echter, als uw bestaande Azure-SSIS IR al actief is, stop het en start het opnieuw.

1. Sla in het installatievenster **voor de beheertijd van integratie** de secties Algemene **instellingen** en **SQL-instellingen over** door **Volgende te**selecteren. 

1. Ga in de sectie **Geavanceerde instellingen** als volgt te werk:

   1. Schakel het selectievakje **Runtime voor zelfgehoste integratie instellen in als proxy voor uw Azure-SSIS-integratieruntime.** 

   1. Selecteer in de vervolgkeuzelijst **Runtime Self-Hosted Integration** uw bestaande zelf gehoste IR als proxy voor de Azure-SSIS IR.

   1. Selecteer in de vervolgkeuzelijst **Met tijdelijke opslag gekoppelde service** uw bestaande Azure Blob-opslagservice of maak een nieuwe service voor fasering.

   1. Geef in het vak **Pad met fasering** een blobcontainer op in het geselecteerde Azure Blob-opslagaccount of laat deze leeg om een standaardcontainer te gebruiken voor fasering.

   1. Selecteer **Doorgaan**.

   ![Geavanceerde instellingen met een zelf gehoste IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

U uw nieuwe of bestaande Azure-SSIS IR ook configureren met de zelf gehoste IR als proxy met PowerShell.

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

Door gebruik te maken van de nieuwste SSDT met SSIS Projects extensie `ConnectByProxy` voor Visual Studio of een standalone installer, u een nieuwe eigenschap vinden die is toegevoegd in OLEDB of Flat File connection managers.
* [Download de SSDT met SSIS Projects-extensie voor Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Download het zelfstandige installatieprogramma](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Wanneer u nieuwe pakketten ontwerpt die gegevensstroomtaken bevatten met OLEDB- of Flat File-bronnen, waarmee u on-premises databases of bestanden openen, u deze eigenschap inschakelen door deze in te stellen op *True* in het deelvenster **Eigenschappen** van de relevante verbindingsmanagers.

![ConnectByProxy inschakelen, eigenschap](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

U deze eigenschap ook inschakelen wanneer u bestaande pakketten uitvoert, zonder dat u deze één voor één handmatig hoeft te wijzigen.  Er zijn twee opties:
- **Optie A:** Open, herbouw en implementeer het project met deze pakketten met de nieuwste SSDT om te worden uitgevoerd op uw Azure-SSIS IR. U de eigenschap vervolgens inschakelen door deze in te stellen op *True* voor de relevante verbindingsmanagers. Wanneer ze pakketten uitvoeren vanuit SSMS, worden deze verbindingsmanagers weergegeven op het tabblad **Verbindingsmanagers** van het pop-upvenster **Pakket uitvoeren.**

  ![ConnectByProxy inschakelen, eigenschap2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  U de eigenschap ook inschakelen door deze in te stellen op *True* voor de relevante verbindingsmanagers die worden weergegeven op het tabblad **Verbindingsmanagers** van [SSIS-pakketactiviteit uitvoeren](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) wanneer ze pakketten uitvoeren in Data Factory-pijplijnen.
  
  ![ConnectByProxy inschakelen, eigenschap3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Optie B:** Implementeer het project met deze pakketten opnieuw om op uw SSIS IR uit te voeren. U de eigenschap vervolgens inschakelen `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`door het eigenschappenpad op te geven en deze in te stellen op *True* als overschrijven op het tabblad **Geavanceerd** van het pop-upvenster **Pakket uitvoeren** wanneer u pakketten uitvoert vanaf SSMS.

  ![ConnectByProxy inschakelen, eigenschap4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  U de eigenschap ook inschakelen `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`door het eigenschappenpad op te geven en deze in te stellen op *True* als een eigenschapsoverschrijving op het tabblad **Overschrijft van** [Uitvoeren SSIS-pakketactiviteit](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) wanneer u pakketten uitvoert in Data Factory-pijplijnen.
  
  ![ConnectByProxy inschakelen, eigenschap5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>De eerste en tweede faseringstaken debuggen

Op uw zelf gehoste IR vindt u de runtime-logboeken in de map *C:\ProgramData\SSISTelemetry* en de uitvoeringslogboeken van de eerste faseringstaken in de map *C:\ProgramData\SSISTelemetry\ExecutionLog.*  U de uitvoeringslogboeken van tweede faseringstaken vinden in uw SSISDB of opgegeven logboekpaden, afhankelijk van of u uw pakketten opslaat in SSISDB of bestandssysteem, bestandsshares of Azure-bestanden. U ook de unieke id's van eerste faseringstaken vinden in de uitvoeringslogboeken van tweede faseringstaken. 

![Unieke id van de eerste faseringstaak](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Windows-verificatie gebruiken in faseringstaken

Als de tijdelijke taken op uw zelfgehoste IR Windows-verificatie vereisen, [configureert u uw SSIS-pakketten om dezelfde Windows-verificatie te gebruiken.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15) 

Uw faseringstaken worden standaard aangeroepen met het zelf gehoste IR-serviceaccount *(NT SERVICE\DIAHostService*, en uw gegevensopslag wordt geopend met het Windows-verificatieaccount. Voor beide accounts moet bepaalde beveiligingsbeleid aan hen worden toegewezen. Ga op de zelfgehoste IR-machine naar De**toewijzing voor gebruikersrechten**voor lokaal **beleid** > voor**lokaal beleid** > en ga als volgt te werk:

1. Wijs de *geheugenquota aanpassen voor een proces* toe en Vervang een *tokenbeleid op procesniveau* aan het zelf gehoste IR-serviceaccount. Dit moet automatisch gebeuren wanneer u uw zelf gehoste IR installeert met het standaardserviceaccount. Als dit niet het zo is, wijst u dit beleid handmatig toe. Als u een ander serviceaccount gebruikt, wijst u hetzelfde beleid toe.

1. Wijs *het aanmeldingsbeleid toe als servicebeleid* aan het Windows-verificatieaccount.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Facturering voor de eerste en tweede faseringstaken

De eerste staging-taken die op uw zelfgehoste IR worden uitgevoerd, worden afzonderlijk gefactureerd, net zoals alle activiteiten voor gegevensverkeer die worden uitgevoerd op een zelf gehoste IR, worden gefactureerd. Dit is opgegeven in het prijsartikel [over de prijzen van azure data factory-gegevenspijplijn.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

De tweede faseringstaken die worden uitgevoerd op uw Azure-SSIS IR worden niet afzonderlijk gefactureerd, maar uw actieve Azure-SSIS IR wordt gefactureerd zoals opgegeven in het [azure-SSIS IR-prijsartikel.](https://azure.microsoft.com/pricing/details/data-factory/ssis/)

## <a name="enabling-tls-12"></a>TLS 1.2 inschakelen

Als u een sterk cryptografie/veiliger netwerkprotocol (TLS 1.2) moet gebruiken en oudere SSL/TLS-versies op uw zelfgehoste IR moet uitschakelen, u het *main.cmd-script* downloaden en uitvoeren dat kan worden gevonden in de map *CustomSetupScript/UserScenarios/TLS 1.2* van onze openbare preview-container.  Met [Azure Storage Explorer](https://storageexplorer.com/)u verbinding maken met onze openbare preview-container door de volgende SAS URI in te voeren:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Huidige beperkingen

- Alleen gegevensstroomtaken met ODBC (Open Database Connectivity)/OLEDB/Flat File-bronnen of OLEDB-bestemming worden momenteel ondersteund. 
- Alleen Azure Blob-opslagservices die zijn geconfigureerd met *accountsleutel,* *SAS-uri (Shared Access Signature)* of *Service Principal-verificatie* worden momenteel ondersteund.
- *ParameterMapping* in OLEDB Source wordt nog niet ondersteund. Gebruik SQL *Command From Variable* als *AccessMode* en gebruik *Expressie* als tijdelijke oplossing om uw variabelen/parameters in een SQL-opdracht in te voegen. Zie ter illustratie het *parametermappingsample.dtsx-pakket* dat te vinden is in de map *SelfHostedIRProxy/Limitations* van onze openbare preview-container. Met Azure Storage Explorer u verbinding maken met onze openbare preview-container door de bovenstaande SAS URI in te voeren.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw zelf gehoste IR hebt geconfigureerd als proxy voor uw Azure-SSIS IR, u uw pakketten implementeren en uitvoeren om on-premises toegang te krijgen tot gegevens als SSIS-pakketactiviteiten uitvoeren in Data Factory-pijplijnen. Zie [SSIS-pakketten uitvoeren als SSIS-pakketactiviteiten uitvoeren in Data Factory-pijplijnen](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)voor meer informatie.
