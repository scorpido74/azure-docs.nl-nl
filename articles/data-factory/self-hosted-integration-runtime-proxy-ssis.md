---
title: Zelf-hostende Integration runtime configureren als proxy voor SSIS in Azure Data Factory
description: Meer informatie over het configureren van zelf-Hostende Integration Runtime als proxy voor Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 178628db11b95fbd345e94111ebf15809da3fc35
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684300"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Zelf-Hostende IR configureren als proxy voor Azure-SSIS IR in ADF
In dit artikel wordt beschreven hoe u SQL Server Integration Services (SSIS)-pakketten uitvoert op Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) met een zelf-Hostende IR die is geconfigureerd als proxy.  Met deze functie kunt u toegang krijgen tot gegevens on-premises zonder [uw Azure-SSIS IR aan een virtueel netwerk te koppelen](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  Dit is handig wanneer uw bedrijfs netwerk een overmatig complexe configuratie/beperkend beleid heeft waarmee u uw Azure-SSIS IR kunt injecteren.

Met deze functie wordt uw pakket met een gegevens stroom taak met een on-premises gegevens bron gesplitst in twee faserings taken: de eerste die wordt uitgevoerd op uw zelf-Hostende IR, verplaatst eerst gegevens van de on-premises gegevens bron naar een faserings gebied in uw Azure Blob Storage. de tweede die op uw Azure-SSIS IR wordt uitgevoerd, verplaatst gegevens uit het faserings gebied naar de gewenste gegevens bestemming.

Deze functie biedt ook andere voor delen/mogelijkheden, omdat u hiermee uw zelf-Hostende IR kunt inrichten in regio's die nog niet worden ondersteund door Azure-SSIS IR, het open bare statische IP-adres van uw zelf-Hostende IR toestaan op de firewall van uw gegevens bronnen, enzovoort.

## <a name="prepare-self-hosted-ir"></a>Zelf-Hostende IR voorbereiden
Als u deze functie wilt gebruiken, moet u eerst een ADF maken en uw Azure-SSIS IR daar inrichten als u dit nog niet hebt gedaan, door te volgen [hoe u een Azure-SSIS IR artikel inricht](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) .

U moet vervolgens uw zelf-Hostende IR inrichten onder dezelfde ADF als uw Azure-SSIS IR is ingericht door te volgen van het [maken van een zelf-hostend IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) -artikel.

Ten slotte moet u de nieuwste versie van zelf-Hostende IR en de volgende extra Stuur Programma's en runtime op uw on-premises machine/Azure virtual machine (VM) downloaden en installeren:
- Down load en installeer de nieuwste [versie van zelf](https://www.microsoft.com/download/details.aspx?id=39717)-hostende IR.
- Als u OLEDB-connectors in uw pakketten gebruikt, moet u de relevante OLEDB-Stuur Programma's downloaden en installeren op dezelfde computer waarop zelf-Hostende IR wordt geïnstalleerd als u dit nog niet hebt gedaan.  Als u de eerdere versie van OLEDB-stuur programma voor SQL Server (SQLNCLI) gebruikt, kunt u [hier](https://www.microsoft.com/download/details.aspx?id=50402)de 64-bits versie downloaden.  Als u de nieuwste versie van OLEDB-stuur programma voor SQL Server (MSOLEDBSQL) gebruikt, kunt u [hier](https://www.microsoft.com/download/details.aspx?id=56730)de 64-bits versie downloaden.  Als u OLEDB-Stuur Programma's gebruikt voor andere database systemen, zoals PostgreSQL, MySQL, Oracle, etc., kunt u de 64-bits versie downloaden vanaf hun respectieve websites.
- Down load en Installeer Visual C++ (VC) runtime op dezelfde computer waarop zelf-hostende IR wordt geïnstalleerd als u dit nog niet hebt gedaan.  U kunt de 64-bits versie [hier](https://www.microsoft.com/download/details.aspx?id=40784)downloaden.

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Azure Blob Storage-gekoppelde service voorbereiden voor fase ring
Maak een gekoppelde Azure Blob Storage-service onder dezelfde ADF als uw Azure-SSIS IR is ingericht, als u dit nog niet hebt gedaan, door het artikel [een gekoppelde ADF-service te maken](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) .  Controleer het volgende:
- **Azure Blob Storage** is geselecteerd voor **gegevens opslag**
- **AutoResolveIntegrationRuntime** is geselecteerd voor **verbinden via Integration runtime**
- De **account sleutel**/**SAS URI**/**Service-Principal** is geselecteerd voor **verificatie methode**

![Azure Blob Storage-gekoppelde service voorbereiden voor fase ring](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Azure-SSIS IR configureren met een zelf-Hostende IR als proxy
Als u uw zelf-Hostende IR-en Azure Blob Storage gekoppelde service voor fase ring hebt voor bereid, kunt u nu uw nieuwe/bestaande Azure-SSIS IR configureren met een zelf-Hostende IR als proxy op de ADF-Portal/-app.  Als uw bestaande Azure-SSIS IR wordt uitgevoerd, stopt u deze voordat u dit doet en start u het vervolgens opnieuw.

Schakel op de pagina **Geavanceerde instellingen** het selectie vakje **Integration runtime voor zelf-hosten instellen als proxy voor uw Azure-SSIS Integration runtime** in, selecteer uw zelf-hostende IR-en Azure Blob Storage gekoppelde service voor fase ring en geef een BLOB op de naam van de container voor het **pad voor tijdelijke bestanden** .

![Azure-SSIS IR configureren met een zelf-Hostende IR als proxy](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-settings-ssisir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>SSIS-pakketten inschakelen om verbinding te maken via proxy
Met de meest recente SSDT met SSIS projects-extensie voor Visual Studio die [hier](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) kan worden gedownload of als een zelfstandig installatie programma dat [hier](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)kan worden gedownload, vindt u een nieuwe **ConnectByProxy** -eigenschap die is toegevoegd in OLEDB/ Verbindings beheer van platte bestanden.  

Bij het ontwerpen van nieuwe pakketten met gegevens stroom taken met OLEDB/platte bestands bronnen om toegang te krijgen tot data bases/bestanden op locatie, kunt u deze eigenschap inschakelen door deze in te stellen op **True** in het deel venster Eigenschappen van relevante verbindings beheer.

![De eigenschap ConnectByProxy inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

U kunt deze eigenschap ook inschakelen wanneer u bestaande pakketten uitvoert zonder deze hand matig te wijzigen.  Er zijn twee opties:
- Het project met de pakketten openen, opnieuw samen stellen en opnieuw implementeren met de nieuwste SSDT om uit te voeren op uw Azure-SSIS IR: de eigenschap kan vervolgens worden ingeschakeld door deze in te stellen op **True** voor de relevante verbindings beheer die worden weer gegeven op de **verbinding** Het tabblad managers van het pop-upvenster pakket uitvoeren tijdens het uitvoeren van pakketten van SSMS.

  ![ConnectByProxy property2 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  De eigenschap kan ook worden ingeschakeld door deze in te stellen op **True** voor de relevante verbindings beheer die wordt weer gegeven op het tabblad **verbindings beheer** van de [activiteit uitvoeren SSIS-pakket](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) voor het uitvoeren van pakketten in ADF-pijp lijnen.
  
  ![ConnectByProxy property3 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Implementeer het project met de pakketten die moeten worden uitgevoerd op uw SSIS-IR: de eigenschap kan vervolgens worden ingeschakeld door het pad naar de eigenschap op te geven, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`en het in te stellen op **True** als eigenschaps overschrijving op het tabblad **Geavanceerd** van het pop-upvenster pakket uitvoeren. bij het uitvoeren van pakketten van SSMS.

  ![ConnectByProxy property4 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  De eigenschap kan ook worden ingeschakeld door het pad naar de eigenschap op te geven, te `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`en deze in te stellen op **True** als eigenschaps onderdrukking op het tabblad **Eigenschappen** van [uitvoering van SSIS-pakket](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) voor het uitvoeren van pakketten in ADF-pijp lijnen.
  
  ![ConnectByProxy property5 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Fouten opsporen in de eerste en tweede faserings taken
Op uw zelf-Hostende IR vindt u de runtime-Logboeken in `C:\ProgramData\SSISTelemetry` map en de uitvoerings logboeken van de eerste fase ring van taken in `C:\ProgramData\SSISTelemetry\ExecutionLog` map.  U kunt de uitvoerings logboeken van de tweede staging-taken vinden in uw SSISDB of opgegeven logboek registratie paden, afhankelijk van of u uw pakketten opslaat in SSISDB of bestands systeem/bestands shares/Azure Files.  De unieke Id's van de eerste tijdelijke taken kunnen ook worden gevonden in de uitvoerings logboeken van de tweede faserings taken, bijvoorbeeld 

![Unieke ID van de eerste faserings taak](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Facturering voor de eerste en tweede staging-taken
De eerste faserings taken die worden uitgevoerd op uw zelf-Hostende IR, worden afzonderlijk gefactureerd op dezelfde manier als de activiteiten voor gegevens verplaatsing die worden uitgevoerd op zelf-Hostende IR, worden gefactureerd zoals is opgegeven in het artikel met de prijzen voor de [ADF-gegevens pijplijn](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

De tweede faserings taken die op uw Azure-SSIS IR worden uitgevoerd, worden niet afzonderlijk in rekening gebracht, maar uw actieve Azure-SSIS IR wordt gefactureerd zoals is opgegeven in het artikel [Azure-SSIS IR prijzen](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Huidige beperkingen

- Alleen OLEDB/plat file Connection-beheer en OLEDB/plat-bestands bronnen worden momenteel ondersteund. 
- Alleen voor Azure Blob Storage gekoppelde services die zijn geconfigureerd met de **account sleutel**/**SAS URI**/**Service-Principal** -verificatie worden momenteel ondersteund.
- Alleen zelf-Hostende IR die is ingericht onder dezelfde ADF als waar uw Azure-SSIS IR is ingericht, wordt momenteel ondersteund.
- Het gebruik van SSIS-para meters/-variabelen in de eigenschappen van OLEDB/plat bestands bronnen en verbindings beheer wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen
Wanneer u uw zelf-Hostende IR als proxy voor uw Azure-SSIS IR hebt geconfigureerd, kunt u uw pakketten implementeren en uitvoeren om toegang te krijgen tot gegevens die on-premises worden uitgevoerd als de uitvoering van SSIS-pakket activiteiten in ADF-pijp lijnen. Zie [SSIS-pakketten uitvoeren als uitvoeren SSIS-pakket activiteiten in ADF-pijp lijnen ](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).