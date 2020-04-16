---
title: Problemen met SSIS-integratie runtime-beheer oplossen
description: Dit artikel biedt richtlijnen voor het oplossen van problemen met het beheer van SSIS Integration Runtime (SSIS IR)
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 0324044d93f12f6ac6ec96ff1a31be8ee02ada41
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414703"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Problemen met SSIS-integratieruntimebeheer in Azure Data Factory oplossen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel worden richtlijnen voor het oplossen van problemen met het oplossen van problemen met beheer in Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), ook wel SSIS IR genoemd.

## <a name="overview"></a>Overzicht

Als u een probleem tegenkomt tijdens het inrichten of deprovisioning van SSIS IR, ziet u een foutbericht in de Microsoft Azure Data Factory-portal of een fout die is geretourneerd door een PowerShell-cmdlet. De fout wordt altijd weergegeven in de indeling van een foutcode met een gedetailleerd foutbericht.

Als de foutcode InternalServerError is, heeft de service tijdelijke problemen en moet u de bewerking later opnieuw proberen. Als een nieuwe poging niet helpt, neemt u contact op met het ondersteuningsteam van Azure Data Factory.

Anders kunnen drie grote externe afhankelijkheden fouten veroorzaken: een Azure SQL Database-server of beheerde instantie, een aangepast installatiescript en een virtuele netwerkconfiguratie.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL Database-server of beheerde instantieproblemen

Er is een Azure SQL Database-server of een beheerd exemplaar vereist als u SSIS IR inricht met een SSIS-catalogusdatabase. SSIS IR moet toegang hebben tot de Azure SQL Database-server of het beheerde exemplaar. Daarnaast moet het account van de Azure SQL Database-server of het beheerde exemplaar gemachtigd zijn om een SSIS-catalogusdatabase (SSISDB) te maken. Als er een fout optreedt, wordt een foutcode met een gedetailleerd SQL-uitzonderingsbericht weergegeven in de Data Factory-portal. Gebruik de informatie in de volgende lijst om de problemen met de foutcodes op te lossen.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

U kunt dit probleem tegenkomen wanneer u een nieuwe SSIS IR inricht of wanneer IR wordt uitgevoerd. Als u deze fout tijdens het inrichten van IR ondervindt, kunt u een gedetailleerd SqlException-bericht ontvangen in het foutbericht dat op een van de volgende problemen wijst:

* Een probleem met een netwerkverbinding. Controleer of de hostnaam van SQL Server of het beheerde exemplaar toegankelijk is. Controleer ook of een firewall of netwerkbeveiligingsgroep (NSG) de toegang voor SSIS IR tot de server blokkeert.
* De aanmelding is mislukt tijdens SQL-verificatie. Met het opgegeven account kan niet bij de SQL Server-database worden aangemeld. Geef het juiste gebruikersaccount op.
* De aanmelding is mislukt tijdens de verificatie van Microsoft Azure Active Directory (Azure AD) (beheerde identiteit). Voeg de beheerde identiteit van uw factory toe aan een AAD-groep en zorg ervoor dat de beheerde identiteit toegangsmachtigingen heeft voor de server van de catalogusdatabase.
* Time-out van verbinding. Deze fout wordt altijd veroorzaakt door een beveiligingsconfiguratie. U wordt aangeraden dat u:
  1. Maak een nieuwe virtuele machine.
  1. Word lid van de VM naar hetzelfde Microsoft Azure Virtual Network of IR als IR zich in een virtueel netwerk bevindt.
  1. Installeer SSMS en controleer de Azure SQL Database-server of beheerde instantiestatus.

Voor andere problemen lost u het probleem op dat wordt weergegeven in het uitgebreide SQL-uitzonderingsfoutbericht. Als u nog steeds problemen ondervindt, neemt u contact op met het ondersteuningsteam van de Azure SQL Database-server of het beheerde exemplaar.

Als u de fout ziet wanneer IR wordt uitgevoerd, verhinderen de wijzigingen aan de netwerkbeveiligingsgroep of firewall waarschijnlijk dat het SSIS IR-werkknooppunt toegang heeft tot de Azure SQL Database-server of het beheerde exemplaar. Deblokkeer het SSIS IR-werkknooppunt zodat het toegang heeft tot de Azure SQL Database-server of het beheerde exemplaar.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Zo ziet dit soort foutmeldinger eruit: "De database 'SSISDB' heeft zijn groottequotum bereikt. Gegevens partitioneren of verwijderen, indexen laten vallen of de documentatie raadplegen voor mogelijke oplossingen." 

De mogelijke oplossingen zijn:
* Verhoog de quotumgrootte van uw SSISDB.
* De configuratie van SSISDB wijzigen om de grootte te verkleinen met:
   * Het verminderen van de bewaartermijn en het aantal projectversies.
   * Het verminderen van de bewaartermijn van het logboek.
   * Het standaardniveau van het logboek wijzigen.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Deze fout betekent dat de Azure SQL Database-server of het beheerde exemplaar al een SSISDB heeft en dat deze door een andere IR wordt gebruikt. U moet een andere Azure SQL Database-server of een ander beheerd exemplaar opgeven, of de bestaande SSISDB verwijderen en de nieuwe IR opnieuw opstarten.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Deze fout kan een van de volgende oorzaken hebben:

* Het gebruikersaccount dat is geconfigureerd voor de SSIS IR beschikt niet over de machtiging om de database te maken. U kunt de gebruiker machtigen om de database te maken.
* Er treedt een time-out op tijdens het maken van de database, zoals een time-out tijdens uitvoering of een time-out voor de werking van de database. U kunt de bewerking later opnieuw uitvoeren. Als u daarna nog steeds problemen ondervindt, neemt u contact op met het ondersteuningsteam van de Azure SQL Database-server of het beheerde exemplaar.

Voor andere problemen controleert u het foutbericht van SQL-uitzondering en lost u het probleem op dat wordt vermeld in de foutdetails. Als u nog steeds problemen ondervindt, neemt u contact op met het ondersteuningsteam van de Azure SQL Database-server of het beheerde exemplaar.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Dit soort foutmelding ziet er als volgt uit: "Ongeldige objectnaam 'catalog.catalog_properties'." In deze situatie hebt u al een database met de naam SSISDB, maar deze is niet gemaakt door SSIS IR, of de database is ongeldig en wordt veroorzaakt door fouten in de laatste SSIS IR-inrichting. U kunt de bestaande database met de naam SSISDB verwijderen, maar u kunt ook een nieuwe Azure SQL Database-server of een beheerd exemplaar voor de IR configureren.

## <a name="custom-setup-issues"></a>Problemen met aangepaste instellingen

Aangepaste installatie biedt een interface voor het toevoegen van uw eigen installatiestappen tijdens het inrichten of opnieuw configureren van uw SSIS IR. Zie [Installatie aanpassen voor Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) voor meer informatie.

Zorg ervoor dat uw container alleen de benodigde aangepaste installatiebestanden bevat. Alle bestanden in de container worden gedownload naar het SSIS IR-werkknooppunt. Het is raadzaam om het aangepaste installatiescript op een lokale machine te testen om eventuele problemen met het uitvoeren van scripts op te lossen voordat u het script uitvoert in SSIS IR.

De aangepaste container voor het installatiescript wordt gecontroleerd terwijl IR wordt uitgevoerd, omdat SSIS IR regelmatig wordt bijgewerkt. Voor deze update is toegang tot de container vereist om het aangepaste installatiescript te downloaden en opnieuw te installeren. Het proces controleert ook of de container toegankelijk is en of het bestand maim.cmd bestaat.

Voor elke fout waarbij aangepaste instelling is betrokken, ziet u een customsetupscriptfoutcode met subcode zoals CustomSetupScriptBlobInaccessible of CustomSetupScriptNotFound.

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

## <a name="virtual-network-configuration"></a>Virtuele netwerkconfiguratie

Als u SSIS IR toevoegt aan Azure Virtual Network, maakt SSIS IR gebruik van het virtuele netwerk onder dit gebruikersabonnement. Zie [Azure-SSIS Integration Runtime toevoegen aan een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) voor meer informatie.

Wanneer er een probleem optreedt dat is gerelateerd aan een virtueel netwerk, ziet u een van de volgende fouten.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Deze fout kan om verschillende redenen optreden. Raadpleeg de secties [Forbidden](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) en [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) om de problemen op te lossen.

### <a name="forbidden"></a>Verboden

Dit soort fouten kan op deze lijken: "SubnetId is niet ingeschakeld voor de lopende rekening. Microsoft.Batch resource provider is niet geregistreerd onder hetzelfde abonnement van VNet."

Deze details geven aan dat Azure Batch geen toegang heeft tot uw virtuele netwerk. Registreer de Microsoft.Batch-resourceprovider onder hetzelfde abonnement als Virtual Network.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Dit soort fout lijkt mogelijk op een van de volgende: 

- "De opgegeven VNet bestaat niet, of de Batch-service heeft er geen toegang toe."
- "De opgegeven subnet xxx bestaat niet."

Deze fouten geven aan dat het virtuele netwerk niet bestaat, de Azure Batch-service geen toegang tot het VNET heeft, of dat het opgegeven subnet niet bestaat.) Zorg ervoor dat het virtuele netwerk en het subnet bestaan, en dat Azure Batch er toegang toe heeft.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Dit soort foutmeldingen kunnen er als volgt uitzien: "Kan integratieruntijd niet inVNet inrichten. Als de DNS-server- of NSG-instellingen zijn geconfigureerd, controleert u of de DNS-server toegankelijk is en NSG correct is geconfigureerd."

In deze situatie hebt u waarschijnlijk een aangepaste configuratie van de DNS-server of NSG-instellingen, waardoor de Azure-servernaam die is vereist voor SSIS IR, niet kan worden omgezet of weergegeven. Zie [Virtual Network-configuratie voor SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) voor meer informatie. Als u nog steeds problemen ondervindt, neemt u contact op met het ondersteuningsteam van Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR wordt regelmatig automatisch bijgewerkt. Er wordt een nieuwe Azure Batch-pool gemaakt tijdens de upgrade, en de oude Azure Batch-pool wordt verwijderd. Ook worden aan Virtual Network gerelateerde resources voor de oude pool verwijderd, en de nieuwe aan Virtual Network gerelateerde resources worden gemaakt onder uw abonnement. Deze fout geeft aan dat het verwijderen van aan Virtual Network gerelateerde resources voor de oude pool is mislukt vanwege een verwijderingsvergrendeling op het niveau van het abonnement of de resourcegroep. Aangezien de klant de verwijderingsvergrendeling beheert en instelt, moet in dit geval de klant de verwijderingsvergrendeling verwijderen.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Als het inrichten van SSIS IR mislukt, worden alle gemaakte resources verwijderd. Als er echter sprake is van een verwijderingsvergrendeling voor de resource op het niveau van het abonnement of de resourcegroep, worden Virtual Network-resources niet verwijderd zoals verwacht. Verwijder de verwijderingsvergrendeling en start de IR opnieuw om deze fout te herstellen.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Als u SSIS IR stopt, worden alle aan Virtual Network gerelateerde resources verwijderd. Verwijderen kan echter mislukken als er sprake is van een verwijderingsvergrendeling voor de resource op het niveau van het abonnement of de resourcegroep. Ook hier wordt de verwijderingsvergrendeling beheerd en ingesteld door de klant. Daarom moet deze de verwijderingsvergrendeling verwijderen en SSIS IR opnieuw stoppen.

### <a name="nodeunavailable"></a>NodeUnavailable

Deze fout treedt op als IR actief is, en duidt erop dat IR is beschadigd. Deze fout wordt altijd veroorzaakt door een wijziging in de DNS-server of NSG-configuratie, waardoor SSIS IR geen verbinding kan maken met een noodzakelijke service. Omdat de configuratie van DNS-server en NSG wordt beheerd door de klant, moet deze de blokkerende problemen aan de eigen zijde oplossen. Zie [Virtual Network-configuratie voor SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) voor meer informatie. Als u nog steeds problemen ondervindt, neemt u contact op met het ondersteuningsteam van Azure Data Factory.

## <a name="static-public-ip-addresses-configuration"></a>Configuratie van statische openbare IP-adressen

Wanneer u lid wordt van azure-SSIS IR naar Azure Virtual Network, u ook uw eigen statische openbare IP-adressen voor de IR meenemen, zodat de IR toegang heeft tot gegevensbronnen die de toegang tot specifieke IP-adressen beperken. Zie [Azure-SSIS Integration Runtime toevoegen aan een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) voor meer informatie.

Naast de bovenstaande virtuele netwerkproblemen u ook voldoen aan statische openbare IP-adressen-gerelateerde problemen. Controleer de volgende fouten voor hulp.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>Ongeldig PublicIPSpecified

Deze fout kan om verschillende redenen optreden wanneer u de Azure-SSIS IR start:

| Foutbericht | Oplossing|
|:--- |:--- |
| Het meegeleverde statische openbare IP-adres is al gebruikt, geef twee ongebruikte voor uw Azure-SSIS Integration Runtime. | U moet twee ongebruikte statische openbare IP-adressen selecteren of huidige verwijzingen naar het opgegeven openbare IP-adres verwijderen en vervolgens de Azure-SSIS IR opnieuw starten. |
| Het meegeleverde statische openbare IP-adres heeft geen DNS-naam, geef twee van hen dns-naam voor uw Azure-SSIS-integratieruntime. | U de DNS-naam van het openbare IP-adres instellen in azure-portal, zoals de onderstaande afbeelding laat zien. Specifieke stappen zijn als volgt: (1) Open Azure portal en ga naar de resourcepagina van dit openbare IP-adres; (2) Selecteer de sectie **Configuratie** en stel de DNS-naam in en klik op **Knop Opslaan.** (3) Start uw Azure-SSIS IR opnieuw. |
| De opgegeven VNet- en statische openbare IP-adressen voor uw Azure-SSIS-integratieruntime moeten zich op dezelfde locatie bevinden. | Volgens de vereisten van het Azure Network moeten het statische openbare IP-adres en het virtuele netwerk zich op dezelfde locatie en hetzelfde abonnement bevinden. Geef twee geldige statische openbare IP-adressen op en start de Azure-SSIS IR opnieuw. |
| Het meegeleverde statische openbare IP-adres is een basisadres, geef twee standaardstandaardadressen op voor uw Azure-SSIS Integration Runtime. | Raadpleeg [SKU's van openbaar IP-adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) voor hulp. |

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Als Azure-SSIS IR-inrichting mislukt, worden alle resources die zijn gemaakt verwijderd. Als er echter een bronvergrendeling is op het niveau van het abonnement of de resourcegroep (die uw statische openbare IP-adres bevat), worden de netwerkbronnen niet verwijderd zoals verwacht. Als u de fout wilt verhelpen, verwijdert u het vergrendelingsslot verwijderen en start u de IR opnieuw.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Wanneer u Azure-SSIS IR stopt, worden alle netwerkbronnen die zijn gemaakt in de brongroep met uw openbare IP-adres verwijderd. Verwijdering kan echter mislukken als er een bronverwijdervergrendeling is op het niveau van het abonnement of de resourcegroep (dat uw statische openbare IP-adres bevat). Verwijder het vergrendelingsslot en start de IR opnieuw.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Azure-SSIS IR wordt regelmatig automatisch bijgewerkt. Nieuwe IR-knooppunten worden gemaakt tijdens de upgrade en de oude knooppunten worden verwijderd. Ook worden de gemaakte netwerkbronnen (bijvoorbeeld de load balancer en de netwerkbeveiligingsgroep) voor de oude knooppunten verwijderd en worden de nieuwe netwerkbronnen onder uw abonnement gemaakt. Deze fout betekent dat het verwijderen van de netwerkbronnen voor de oude knooppunten is mislukt vanwege een vergrendeling bij het abonnement of de brongroep (die het statische openbare IP-adres bevat). Verwijder het vergrendelingsbericht, zodat we de oude knooppunten kunnen opruimen en het statische openbare IP-adres voor de oude knooppunten kunnen vrijgeven. Anders kan het statische openbare IP-adres niet worden vrijgegeven en kunnen we uw IR niet verder upgraden.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

Wanneer u uw eigen statische openbare IP-adressen mee wilt nemen, moeten twee openbare IP-adressen worden verstrekt. Een van hen zal worden gebruikt om de IR-knooppunten onmiddellijk te maken en een andere zal worden gebruikt tijdens de upgrade van de IR. Deze fout kan optreden wanneer het andere openbare IP-adres onbruikbaar is tijdens de upgrade. Raadpleeg [Ongeldig PublicIPSpecified](#InvalidPublicIPSpecified) voor mogelijke oorzaken.