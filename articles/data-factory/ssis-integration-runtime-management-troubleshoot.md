---
title: 'Problemen met SSIS Integration Runtime Management in Azure Data Factory oplossen '
description: Dit artikel bevat richt lijnen voor probleem oplossing voor beheer problemen van SSIS Integration Runtime (SSIS IR)
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 3452fc2274eb646acb19c0e6a203ebadcb81cad5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684021"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Problemen met SSIS Integration Runtime Management in Azure Data Factory oplossen

Dit artikel bevat richt lijnen voor probleem oplossing voor beheer problemen in Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), ook wel SSIS IR genoemd.

## <a name="overview"></a>Overzicht

Als u een probleem ondervindt bij het inrichten of ongedaan maken van de SSIS-IR, ziet u een fout bericht in de Microsoft Azure Data Factory portal of een fout die is geretourneerd door een Power shell-cmdlet. De fout wordt altijd weer gegeven in de indeling van een fout code met een gedetailleerd fout bericht.

Als de fout code InternalServerError is, heeft de service tijdelijke problemen. Voer de bewerking later opnieuw uit. Als een nieuwe poging niet helpt, neemt u contact op met het ondersteunings team van Azure Data Factory.

Anders kunnen drie belang rijke externe afhankelijkheden fouten veroorzaken: een Azure SQL Database Server of een beheerd exemplaar, een aangepast installatie script en een configuratie van een virtueel netwerk.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problemen met de Azure SQL Database Server of het beheerde exemplaar

Er is een Azure SQL Database-server of een beheerd exemplaar vereist als u SSIS IR inricht met een SSIS-catalogusdatabase. SSIS IR moet toegang hebben tot de Azure SQL Database-server of het beheerde exemplaar. Daarnaast moet het account van de Azure SQL Database-server of het beheerde exemplaar gemachtigd zijn om een SSIS-catalogusdatabase (SSISDB) te maken. Als er een fout optreedt, wordt een foutcode met een gedetailleerd SQL-uitzonderingsbericht weergegeven in de Data Factory-portal. Gebruik de informatie in de volgende lijst om de problemen met de foutcodes op te lossen.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

U kunt dit probleem tegenkomen wanneer u een nieuwe SSIS IR inricht of wanneer IR wordt uitgevoerd. Als u deze fout tijdens het inrichten van IR ondervindt, kunt u een gedetailleerd SqlException-bericht ontvangen in het foutbericht dat op een van de volgende problemen wijst:

* Een probleem met een netwerkverbinding. Controleer of de hostnaam van SQL Server of het beheerde exemplaar toegankelijk is. Controleer ook of een firewall of netwerkbeveiligingsgroep (NSG) de toegang voor SSIS IR tot de server blokkeert.
* De aanmelding is mislukt tijdens SQL-verificatie. Met het opgegeven account kan niet bij de SQL Server-database worden aangemeld. Geef het juiste gebruikersaccount op.
* De aanmelding is mislukt tijdens de verificatie van Microsoft Azure Active Directory (Azure AD) (beheerde identiteit). Voeg de beheerde identiteit van uw factory toe aan een AAD-groep en zorg ervoor dat de beheerde identiteit toegangsmachtigingen heeft voor de server van de catalogusdatabase.
* Time-out van verbinding. Deze fout wordt altijd veroorzaakt door een beveiligingsconfiguratie. U wordt aangeraden dat u:
  1. Maak een nieuwe virtuele machine.
  1. Voeg de virtuele machine toe aan dezelfde Microsoft Azure Virtual Network van IR als IR zich in een virtueel netwerk bevindt.
  1. Installeer SSMS en controleer de status van de Azure SQL Database-Server of het beheerde exemplaar.

Voor andere problemen lost u het probleem op dat wordt weergegeven in het uitgebreide SQL-uitzonderingsfoutbericht. Als u nog steeds problemen ondervindt, neemt u contact op met het ondersteuningsteam van de Azure SQL Database-server of het beheerde exemplaar.

Als u de fout ziet wanneer IR wordt uitgevoerd, verhinderen de wijzigingen aan de netwerkbeveiligingsgroep of firewall waarschijnlijk dat het SSIS IR-werkknooppunt toegang heeft tot de Azure SQL Database-server of het beheerde exemplaar. Deblokkeer het SSIS IR-werkknooppunt zodat het toegang heeft tot de Azure SQL Database-server of het beheerde exemplaar.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Dit type fout bericht kan er als volgt uitzien: ' de data base ' SSISDB ' heeft het quotum voor de grootte bereikt. Partitioneer of verwijder gegevens, verwijder indexen of Raadpleeg de documentatie voor mogelijke oplossingen. " 

De mogelijke oplossingen zijn:
* Verg root de quotum grootte van uw SSISDB.
* De configuratie van SSISDB wijzigen om de grootte te verkleinen met:
   * De retentie periode en het aantal project versies verminderen.
   * De retentie periode van het logboek te verminderen.
   * Het standaard niveau van het logboek wijzigen.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Deze fout betekent dat de Azure SQL Database-server of het beheerde exemplaar al een SSISDB heeft en dat deze door een andere IR wordt gebruikt. U moet een andere Azure SQL Database-server of een ander beheerd exemplaar opgeven, of de bestaande SSISDB verwijderen en de nieuwe IR opnieuw opstarten.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Deze fout kan een van de volgende oorzaken hebben:

* Het gebruikersaccount dat is geconfigureerd voor de SSIS IR beschikt niet over de machtiging om de database te maken. U kunt de gebruiker machtigen om de database te maken.
* Er treedt een time-out op tijdens het maken van de database, zoals een time-out tijdens uitvoering of een time-out voor de werking van de database. U kunt de bewerking later opnieuw uitvoeren. Als u daarna nog steeds problemen ondervindt, neemt u contact op met het ondersteuningsteam van de Azure SQL Database-server of het beheerde exemplaar.

Voor andere problemen controleert u het foutbericht van SQL-uitzondering en lost u het probleem op dat wordt vermeld in de foutdetails. Als u nog steeds problemen ondervindt, neemt u contact op met het ondersteuningsteam van de Azure SQL Database-server of het beheerde exemplaar.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Dit type fout bericht ziet er als volgt uit: ' ongeldige object naam ' Catalog. catalog_properties '. In dit geval hebt u al een Data Base met de naam SSISDB, maar deze is niet gemaakt door SSIS IR, of de Data Base bevindt zich in een ongeldige status die wordt veroorzaakt door fouten in de laatste SSIS-IR-inrichting. U kunt de bestaande database met de naam SSISDB verwijderen, maar u kunt ook een nieuwe Azure SQL Database-server of een beheerd exemplaar voor de IR configureren.

## <a name="custom-setup-issues"></a>Aangepaste installatie problemen

Aangepaste installatie biedt een interface voor het toevoegen van uw eigen installatiestappen tijdens het inrichten of opnieuw configureren van uw SSIS IR. Zie [Installatie aanpassen voor Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) voor meer informatie.

Zorg ervoor dat uw container alleen de benodigde aangepaste installatiebestanden bevat. Alle bestanden in de container worden gedownload naar het SSIS IR-werkknooppunt. Het is raadzaam om het aangepaste installatiescript op een lokale machine te testen om eventuele problemen met het uitvoeren van scripts op te lossen voordat u het script uitvoert in SSIS IR.

De aangepaste container voor het installatiescript wordt gecontroleerd terwijl IR wordt uitgevoerd, omdat SSIS IR regelmatig wordt bijgewerkt. Voor deze update is toegang tot de container vereist om het aangepaste installatiescript te downloaden en opnieuw te installeren. Het proces controleert ook of de container toegankelijk is en of het bestand maim.cmd bestaat.

Voor elke fout die aangepaste installatie omvat, ziet u een CustomSetupScriptFailure-fout code met subcode zoals CustomSetupScriptBlobContainerInaccessible of CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Deze fout betekent dat SSIS IR geen toegang heeft tot uw Azure Blob-container voor aangepaste installatie. Zorg ervoor dat de SAS-URI van de container bereikbaar is en niet is verlopen.

Stop de IR als deze wordt uitgevoerd, configureer de IR opnieuw met de nieuwe aangepaste installatiecontainer SAS URI en start de IR opnieuw op.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Deze fout betekent dat SSIS IR geen aangepast installatiescript (main.cmd) kan vinden in de Blob-container. Zorg ervoor dat main.cmd in de container aanwezig is. Dit is het invoerpunt voor de aangepaste installatie.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Deze fout betekent dat de uitvoering van het aangepaste installatiescript (main.cmd) is mislukt. Voer het script eerst op de lokale machine uit of controleer de logboeken voor de uitvoering van de aangepaste installatie in uw Blob-container.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Deze fout wijst op een time-out voor het uitvoeren van een script voor een aangepaste installatie. Zorg ervoor dat het script op de achtergrond kan worden uitgevoerd en dat er geen interactieve invoer nodig is, en dat uw blobcontainer alleen de benodigde aangepaste installatiebestanden bevat. Het is raadzaam om het script eerst op de lokale computer te testen. Controleer ook de logboeken voor de uitvoering van de aangepaste installatie in uw Blob-container. De maximale periode voor aangepaste installatie is 45 minuten voordat er een time-out optreedt. De maximale periode omvat de tijd voor het downloaden van alle bestanden uit uw container en het installeren ervan op SSIS IR. Als u een langere periode nodig hebt, dient u een ondersteuningsticket in.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Deze fout betekent dat de poging om de logboeken voor de uitvoering van de aangepaste installatie in uw Blob-container te uploaden, is mislukt. Dit probleem treedt op omdat SSIS IR geen schrijfmachtigingen heeft voor uw Blob-container of vanwege opslag- of netwerkproblemen. Als de aangepaste installatie is geslaagd, is deze fout niet van invloed op een SSIS-functie, maar de logboeken ontbreken. Als de aangepaste installatie mislukt met een andere fout en het logboek niet wordt geüpload, wordt deze fout eerst gerapporteerd zodat het logboek voor analyse kan worden geüpload. Nadat dit probleem is opgelost, worden er ook meer specifieke problemen gerapporteerd. Als dit probleem na een nieuwe poging niet wordt opgelost, neemt u contact op met het Azure Data Factory-ondersteuningsteam.

## <a name="virtual-network-configuration"></a>Configuratie van virtueel netwerk

Als u SSIS IR toevoegt aan Azure Virtual Network, maakt SSIS IR gebruik van het virtuele netwerk onder dit gebruikersabonnement. Zie [Azure-SSIS Integration Runtime toevoegen aan een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) voor meer informatie.

Wanneer er een probleem optreedt dat is gerelateerd aan een virtueel netwerk, ziet u een van de volgende fouten.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Deze fout kan om verschillende redenen optreden. Raadpleeg de secties [Forbidden](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) en [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) om de problemen op te lossen.

### <a name="forbidden"></a>Verboden

Dit soort fout kan er als volgt uitzien: "SubnetId is niet ingeschakeld voor het huidige account. De resource provider micro soft. batch is niet geregistreerd onder hetzelfde abonnement van VNet. "

Deze details geven aan dat Azure Batch geen toegang heeft tot uw virtuele netwerk. Registreer de Microsoft.Batch-resourceprovider onder hetzelfde abonnement als Virtual Network.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Dit soort fout lijkt mogelijk op een van de volgende: 

- "Het opgegeven VNet bestaat niet of de batch-service heeft er geen toegang toe."
- "Het opgegeven subnet xxx bestaat niet."

Deze fouten geven aan dat het virtuele netwerk niet bestaat, de Azure Batch-service geen toegang tot het VNET heeft, of dat het opgegeven subnet niet bestaat.) Zorg ervoor dat het virtuele netwerk en het subnet bestaan, en dat Azure Batch er toegang toe heeft.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Dit type fout bericht kan er als volgt uitzien: ' kan geen Integration Runtime inrichten in VNet. Als de DNS-server-of NSG-instellingen zijn geconfigureerd, controleert u of de DNS-server toegankelijk is en of NSG juist is geconfigureerd. "

In deze situatie hebt u waarschijnlijk een aangepaste configuratie van de DNS-server of NSG-instellingen, waardoor de Azure-servernaam die is vereist voor SSIS IR, niet kan worden omgezet of weergegeven. Zie [Virtual Network-configuratie voor SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) voor meer informatie. Als u nog steeds problemen ondervindt, neemt u contact op met het ondersteuningsteam van Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR wordt regelmatig automatisch bijgewerkt. Er wordt een nieuwe Azure Batch-pool gemaakt tijdens de upgrade, en de oude Azure Batch-pool wordt verwijderd. Ook worden aan Virtual Network gerelateerde resources voor de oude pool verwijderd, en de nieuwe aan Virtual Network gerelateerde resources worden gemaakt onder uw abonnement. Deze fout geeft aan dat het verwijderen van aan Virtual Network gerelateerde resources voor de oude pool is mislukt vanwege een verwijderingsvergrendeling op het niveau van het abonnement of de resourcegroep. Aangezien de klant de verwijderingsvergrendeling beheert en instelt, moet in dit geval de klant de verwijderingsvergrendeling verwijderen.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Als het inrichten van SSIS IR mislukt, worden alle gemaakte resources verwijderd. Als er echter sprake is van een verwijderingsvergrendeling voor de resource op het niveau van het abonnement of de resourcegroep, worden Virtual Network-resources niet verwijderd zoals verwacht. Verwijder de verwijderingsvergrendeling en start de IR opnieuw om deze fout te herstellen.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Als u SSIS IR stopt, worden alle aan Virtual Network gerelateerde resources verwijderd. Verwijderen kan echter mislukken als er sprake is van een verwijderingsvergrendeling voor de resource op het niveau van het abonnement of de resourcegroep. Ook hier wordt de verwijderingsvergrendeling beheerd en ingesteld door de klant. Daarom moet deze de verwijderingsvergrendeling verwijderen en SSIS IR opnieuw stoppen.

### <a name="nodeunavailable"></a>NodeUnavailable

Deze fout treedt op als IR actief is, en duidt erop dat IR is beschadigd. Deze fout wordt altijd veroorzaakt door een wijziging in de DNS-server of NSG-configuratie, waardoor SSIS IR geen verbinding kan maken met een noodzakelijke service. Omdat de configuratie van DNS-server en NSG wordt beheerd door de klant, moet deze de blokkerende problemen aan de eigen zijde oplossen. Zie [Virtual Network-configuratie voor SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) voor meer informatie. Als u nog steeds problemen ondervindt, neemt u contact op met het ondersteuningsteam van Azure Data Factory.
