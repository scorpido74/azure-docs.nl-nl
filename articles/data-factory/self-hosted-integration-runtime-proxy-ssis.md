---
title: Zelf-hostende Integration runtime configureren als proxy voor SSIS
description: Meer informatie over het configureren van zelf-Hostende Integration Runtime als proxy voor Azure-SSIS Integration Runtime.
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
ms.date: 12/23/2019
ms.openlocfilehash: 8754b2ef9727b5526cc9bcf756085f2f05f6a398
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552097"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Zelf-Hostende IR configureren als proxy voor Azure-SSIS IR in ADF

In dit artikel wordt beschreven hoe u SQL Server Integration Services (SSIS)-pakketten uitvoert op Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) met een zelf-Hostende IR die is geconfigureerd als proxy.  Met deze functie kunt u toegang krijgen tot gegevens on-premises zonder [uw Azure-SSIS IR aan een virtueel netwerk te koppelen](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  Het is handig wanneer uw bedrijfs netwerk een overmatig complexe configuratie/beperkend beleid heeft waarmee u uw Azure-SSIS IR kunt injecteren.

Met deze functie wordt uw pakket met een gegevens stroom taak met een on-premises gegevens bron gesplitst in twee faserings taken: de eerste die wordt uitgevoerd op uw zelf-Hostende IR, verplaatst eerst gegevens van de on-premises gegevens bron naar een faserings gebied in uw Azure Blob Storage. de tweede die op uw Azure-SSIS IR wordt uitgevoerd, verplaatst gegevens uit het faserings gebied naar de gewenste gegevens bestemming.

Deze functie biedt ook andere voor delen/mogelijkheden, omdat u hiermee uw zelf-Hostende IR kunt inrichten in regio's die nog niet worden ondersteund door Azure-SSIS IR, het open bare statische IP-adres van uw zelf-Hostende IR toestaan op de firewall van uw gegevens bronnen, enzovoort.

## <a name="prepare-self-hosted-ir"></a>Zelf-Hostende IR voorbereiden

Als u deze functie wilt gebruiken, moet u eerst een ADF maken en uw Azure-SSIS IR daar inrichten als u dit nog niet hebt gedaan, door te volgen [hoe u een Azure-SSIS IR artikel inricht](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) .

U moet vervolgens uw zelf-Hostende IR inrichten onder dezelfde ADF als uw Azure-SSIS IR is ingericht door te volgen van het [maken van een zelf-hostend IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) -artikel.

Ten slotte moet u de nieuwste versie van zelf-Hostende IR en de volgende extra Stuur Programma's en runtime op uw on-premises machine/Azure virtual machine (VM) downloaden en installeren:
- Down load en installeer de nieuwste versie van zelf-Hostende [IR.](https://www.microsoft.com/download/details.aspx?id=39717)
- Als u OLEDB-connectors in uw pakketten gebruikt, downloadt en installeert u de relevante OLEDB-Stuur Programma's op dezelfde computer waarop zelf-Hostende IR wordt geïnstalleerd als u dit nog niet hebt gedaan.  Als u de eerdere versie van OLEDB-stuur programma voor SQL Server (SQLNCLI) gebruikt, kunt u [hier](https://www.microsoft.com/download/details.aspx?id=50402)de 64-bits versie downloaden.  Als u de nieuwste versie van OLEDB-stuur programma voor SQL Server (MSOLEDBSQL) gebruikt, kunt u [hier](https://www.microsoft.com/download/details.aspx?id=56730)de 64-bits versie downloaden.  Als u OLEDB-Stuur Programma's gebruikt voor andere database systemen, zoals PostgreSQL, MySQL, Oracle, etc., kunt u de 64-bits versie downloaden vanaf hun respectieve websites.
- Down load en Installeer C++ Visual (VC) runtime op dezelfde computer waarop zelf-hostende IR wordt geïnstalleerd als u dit nog niet hebt gedaan.  U kunt de 64-bits versie [hier](https://www.microsoft.com/download/details.aspx?id=40784)downloaden.

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Azure Blob Storage-gekoppelde service voorbereiden voor fase ring

Maak een gekoppelde Azure Blob Storage-service onder dezelfde ADF als uw Azure-SSIS IR is ingericht, als u dit nog niet hebt gedaan, door het artikel [een gekoppelde ADF-service te maken](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) .  Controleer het volgende:
- **Azure Blob Storage** is geselecteerd voor **gegevens opslag**
- **AutoResolveIntegrationRuntime** is geselecteerd voor **verbinden via Integration runtime**
- De **account sleutel**/**SAS URI**/**Service-Principal** is geselecteerd voor **verificatie methode**

>[!TIP]
>Als **Service-Principal** - is geselecteerd, geeft u ten minste de **rol van BLOB data contributor-gegevens**toegang. Raadpleeg de [Azure Blob Storage-connector](connector-azure-blob-storage.md#linked-service-properties)voor meer informatie.

![Azure Blob Storage-gekoppelde service voorbereiden voor fase ring](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Azure-SSIS IR configureren met een zelf-Hostende IR als proxy

Als u uw zelf-Hostende IR-en Azure Blob Storage gekoppelde service voor fase ring hebt voor bereid, kunt u nu uw nieuwe/bestaande Azure-SSIS IR configureren met een zelf-Hostende IR als proxy op de ADF-Portal/-app.  Als uw bestaande Azure-SSIS IR actief is, stopt u het voordat u dit doet en start u het daarna opnieuw.

1. Ga in het configuratie venster voor Integration runtime naar de secties **algemene instellingen** en **SQL-instellingen** en selecteer de knop **volgende** . 

1. In het gedeelte **Geavanceerde instellingen** :

   1. Selecteer het selectie vakje **zelf-hostende Integration runtime als proxy voor uw Azure-SSIS Integration runtime instellen** . 

   1. Voor **zelf-hostende Integration runtime**selecteert u uw bestaande zelf-hostende IR als proxy voor Azure-SSIS IR.

   1. Voor **faserings opslag gekoppelde**service selecteert u uw bestaande Azure Blob Storage-gekoppelde service of maakt u een nieuwe voor fase ring.

   1. Geef voor het **tijdelijke pad**een BLOB-container op in het geselecteerde Azure Blob Storage-account of laat het leeg om een standaard waarde te gebruiken voor fase ring.

   1. Selecteer **Doorgaan**.

   ![Geavanceerde instellingen met een zelf-hostende IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>SSIS-pakketten inschakelen om verbinding te maken via proxy

Met de meest recente SSDT met SSIS projects-extensie voor Visual Studio die [hier](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) kan worden gedownload of als een zelfstandig installatie programma dat [hier](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)kan worden gedownload, vindt u een nieuwe **ConnectByProxy** -eigenschap die is toegevoegd aan OLEDB/plat file Connection-beheer.  

Bij het ontwerpen van nieuwe pakketten met gegevens stroom taken met OLEDB/platte bestands bronnen om toegang te krijgen tot data bases/bestanden op locatie, kunt u deze eigenschap inschakelen door deze in te stellen op **True** in het deel venster Eigenschappen van relevante verbindings beheer.

![De eigenschap ConnectByProxy inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

U kunt deze eigenschap ook inschakelen wanneer u bestaande pakketten uitvoert zonder deze hand matig te wijzigen.  Er zijn twee opties:
- Het project met de pakketten openen, opnieuw samen stellen en opnieuw implementeren met de nieuwste SSDT om uit te voeren op uw Azure-SSIS IR: de eigenschap kan vervolgens worden ingeschakeld door deze in te stellen op **True** voor de relevante verbindings beheer Programma's die worden weer gegeven op het tabblad **verbindings beheer** van het pop-upvenster pakket uitvoeren bij het uitvoeren van pakketten van SSMS.

  ![ConnectByProxy property2 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  De eigenschap kan ook worden ingeschakeld door deze in te stellen op **True** voor de relevante verbindings beheer die wordt weer gegeven op het tabblad **verbindings beheer** van de [activiteit uitvoeren SSIS-pakket](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) voor het uitvoeren van pakketten in ADF-pijp lijnen.
  
  ![ConnectByProxy property3 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Implementeer het project met de pakketten die moeten worden uitgevoerd op uw SSIS-IR: de eigenschap kan vervolgens worden ingeschakeld door het pad naar de eigenschap op te geven, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`en het in te stellen op **True** als eigenschaps overschrijving op het tabblad **Geavanceerd** van het pop-upvenster pakket uitvoeren bij het uitvoeren van pakketten van SSMS.

  ![ConnectByProxy property4 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  De eigenschap kan ook worden ingeschakeld door het pad naar de eigenschap op te geven, te `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`en deze in te stellen op **True** als eigenschaps onderdrukking op het tabblad **Eigenschappen** van [uitvoering van SSIS-pakket](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) voor het uitvoeren van pakketten in ADF-pijp lijnen.
  
  ![ConnectByProxy property5 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Fouten opsporen in de eerste en tweede faserings taken

Op uw zelf-Hostende IR vindt u de runtime-Logboeken in `C:\ProgramData\SSISTelemetry` map en de uitvoerings logboeken van de eerste fase ring van taken in `C:\ProgramData\SSISTelemetry\ExecutionLog` map.  U kunt de uitvoerings logboeken van de tweede staging-taken vinden in uw SSISDB of opgegeven logboek registratie paden, afhankelijk van of u uw pakketten opslaat in SSISDB of bestands systeem/bestands shares/Azure Files.  De unieke Id's van de eerste tijdelijke taken kunnen ook worden gevonden in de uitvoerings logboeken van de tweede faserings taken, bijvoorbeeld 

![Unieke ID van de eerste faserings taak](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="using-windows-authentication-in-staging-tasks"></a>Windows-verificatie gebruiken in faserings taken

Als voor uw staging-taken op zelf-Hostende IR Windows-verificatie is vereist, moet u [uw SSIS-pakketten configureren voor gebruik van dezelfde Windows-verificatie](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). Uw faserings taken worden aangeroepen met het zelf-Hostende IR-service account (`NT SERVICE\DIAHostService` standaard) en uw gegevens archieven worden geopend met het Windows-verificatie account. Voor beide accounts moet een bepaald beveiligings beleid worden toegewezen. Daarom opent u op de zelf-Hostende IR-computer `Local Security Policy` -> `Local Policies` -> `User Rights Assignment` en voert u de volgende stappen uit.
- Wijs de **geheugen quota voor het aanpassen van een proces** toe en **Vervang een token beleid op proces niveau** naar het zelf-hostende IR-service account. Dit moet automatisch worden gedaan wanneer u uw zelf-Hostende IR installeert met het standaard service account. Als u een ander service account gebruikt, wijst u hieraan hetzelfde beleid toe.
- Wijs het **Aanmelden als service** beleid toe aan het Windows-verificatie account.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Facturering voor de eerste en tweede staging-taken

De eerste faserings taken die worden uitgevoerd op uw zelf-Hostende IR, worden afzonderlijk gefactureerd op dezelfde manier als de activiteiten voor gegevens verplaatsing die worden uitgevoerd op zelf-Hostende IR, worden gefactureerd zoals is opgegeven in het artikel met de prijzen voor de [ADF-gegevens pijplijn](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

De tweede faserings taken die op uw Azure-SSIS IR worden uitgevoerd, worden niet afzonderlijk in rekening gebracht, maar uw actieve Azure-SSIS IR wordt gefactureerd zoals is opgegeven in het artikel [Azure-SSIS IR prijzen](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Huidige beperkingen

- Alleen gegevensstroom taken met ODBC/OLEDB/plat bestands verbindings beheer en ODBC/OLEDB/plat bestands bronnen worden momenteel ondersteund. 
- Alleen voor Azure Blob Storage gekoppelde services die zijn geconfigureerd met de **account sleutel**/**SAS URI**/**Service-Principal** -verificatie worden momenteel ondersteund.

## <a name="next-steps"></a>Volgende stappen

Als u uw zelf-Hostende IR als proxy voor uw Azure-SSIS IR hebt geconfigureerd, kunt u uw pakketten implementeren en uitvoeren om toegang te krijgen tot gegevens die on-premises worden uitgevoerd als de uitvoering van SSIS-pakket activiteiten in ADF-pijp lijnen. Zie [SSIS-pakketten uitvoeren als uitvoeren SSIS-pakket activiteiten in ADF-pijp lijnen](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).