---
title: Gebruik de Azure-opslagemulator voor ontwikkeling en testen | Microsoft Documenten
description: De Azure-opslagemulator biedt een gratis lokale ontwikkelomgeving voor het ontwikkelen en testen van uw Azure Storage-toepassingen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1f13efeb5c2ebcb8b23dea6f9ae997039972f089
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72428316"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>De Azure-opslagemulator gebruiken voor ontwikkeling en testen

De Microsoft Azure-opslagemulator is een hulpmiddel dat de azure blob-, wachtrij- en tabelservices emuleert voor lokale ontwikkelingsdoeleinden. U uw toepassing lokaal testen op de opslagservices zonder een Azure-abonnement te maken of kosten te maken. Als u tevreden bent over de manier waarop uw toepassing werkt in de emulator, schakelt u over naar het gebruik van een Azure-opslagaccount in de cloud.

## <a name="get-the-storage-emulator"></a>Download de opslagemulator

De opslagemulator is beschikbaar als onderdeel van de [Microsoft Azure SDK.](https://azure.microsoft.com/downloads/) U ook de opslag emulator installeren met behulp van de [standalone installer](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (direct download). Als u de opslagemulator wilt installeren, moet u beheerdersbevoegdheden op uw computer hebben.

De opslagemulator draait momenteel alleen op Windows. Als u een opslag emulator voor Linux, een optie is de gemeenschap onderhouden, open-source opslag emulator [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Gegevens die zijn gemaakt in één versie van de opslagemulator zijn niet gegarandeerd toegankelijk wanneer ze een andere versie gebruiken. Als u uw gegevens voor de lange termijn moet blijven gebruiken, raden we u aan deze gegevens op te slaan in een Azure-opslagaccount, in plaats van in de opslagemulator.
> 
> De opslagemulator is afhankelijk van specifieke versies van de OData-bibliotheken. Het vervangen van de OData-DLL's die door de opslagemulator worden gebruikt door andere versies wordt niet ondersteund en kan onverwacht gedrag veroorzaken. Elke versie van OData die door de opslagservice wordt ondersteund, kan echter worden gebruikt om aanvragen naar de emulator te verzenden.

## <a name="how-the-storage-emulator-works"></a>Hoe de opslagemulator werkt

De opslagemulator maakt gebruik van een lokale Microsoft SQL Server 2012 Express LocalDB-instantie om Azure-opslagservices te emuleren. U ervoor kiezen om de opslagemulator te configureren om toegang te krijgen tot een lokaal exemplaar van SQL Server in plaats van het LocalDB-exemplaar. Zie de sectie [Start en initialiseer de opslagemulator](#start-and-initialize-the-storage-emulator) later in dit artikel voor meer informatie.

De opslagemulator maakt verbinding met SQL Server of LocalDB met Windows-verificatie.

Er bestaan enkele verschillen in functionaliteit tussen de opslagemulator en Azure-opslagservices. Zie de sectie [Verschillen tussen de opslagemulator en Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) later in dit artikel voor meer informatie over deze verschillen.

## <a name="start-and-initialize-the-storage-emulator"></a>Start en initialiseer de opslagemulator

Ga als eerste over de startemulator voor Azure-opslag:

1. Selecteer de knop **Start** of druk op de **Windows-toets.**
2. Begin `Azure Storage Emulator`met typen .
3. Selecteer de emulator in de lijst met weergegeven toepassingen.

Wanneer de opslagemulator wordt gestart, verschijnt er een opdrachtpromptvenster. U dit consolevenster gebruiken om de opslagemulator te starten en te stoppen. U ook gegevens wissen, status krijgen en de emulator initialiseren via de opdrachtprompt. Zie de verwijzingssectie Voor de [opdrachtregel van de opslagemulator](#storage-emulator-command-line-tool-reference) later in dit artikel.

> [!NOTE]
> De Azure-opslagemulator wordt mogelijk niet correct gestart als een andere opslagemulator, zoals Azurite, op het systeem wordt uitgevoerd.

Wanneer de emulator wordt gestart, ziet u een pictogram in het systeemvak op de taakbalk van Windows.

Wanneer u het venster Opdrachtprompt voor opslagemulator sluit, blijft de opslagemulator worden uitgevoerd. Als u het venster Van de Opslagemulator-console opnieuw wilt weergeven, volgt u de voorgaande stappen alsof u de opslagemulator start.

De eerste keer dat u de opslagemulator uitvoert, wordt de lokale opslagomgeving voor u geïnitialiseerd. Het initialisatieproces maakt een database in LocalDB en reserveert HTTP-poorten voor elke lokale opslagservice.

De opslagemulator is standaard `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`geïnstalleerd op .

> [!TIP]
> U de [Microsoft Azure Storage Explorer](https://storageexplorer.com) gebruiken om te werken met emulatorbronnen voor lokale opslag. Zoek naar '(Emulator - Standaardpoorten) (Sleutel)" onder 'Lokale & gekoppeld' in de bronnenstructuur van Storage Explorer nadat u de opslagemulator hebt geïnstalleerd en gestart.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Initialiseer de opslagemulator om een andere SQL-database te gebruiken

U het opdrachtregelgereedschap voor opslagemulatorgebruiken om de opslagemulator te initialiseren om een andere SQL-databaseinstantie dan de standaard instantie LocalDB aan te wijzen:

1. Open het venster van de Opslagemulatorconsole zoals beschreven in het [startscherm en initialiseer de sectie opslagemulator.](#start-and-initialize-the-storage-emulator)
1. Typ in het consolevenster de `<SQLServerInstance>` volgende opdracht, waar is de naam van de SQL Server-instantie. Als u LocalDB `(localdb)\MSSQLLocalDb` wilt gebruiken, geeft u op als SQL Server-instantie.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   U ook de volgende opdracht gebruiken, die de emulator aanstuurt om de standaard SQL Server-instantie te gebruiken:

   `AzureStorageEmulator.exe init /server .`

   U ook de volgende opdracht gebruiken, waarmee de database opnieuw wordt geïnitialt naar de standaardinstantie LocalDB:

   `AzureStorageEmulator.exe init /forceCreate`

Zie Naslaginformatie over de [opdrachtopdracht opslagemulator.](#storage-emulator-command-line-tool-reference)

> [!TIP]
> U de [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) gebruiken om uw SQL Server-exemplaren te beheren, inclusief de LocalDB-installatie. Geef in het dialoogvenster SMSS `(localdb)\MSSQLLocalDb` Connect to **Server** op in het veld **Server:** maak verbinding met het instantie LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Aanvragen verifiëren tegen de opslagemulator

Zodra u de opslagemulator hebt geïnstalleerd en gestart, u uw code ertegen testen. Elk verzoek dat u doet tegen de opslagemulator moet worden geautoriseerd, tenzij het een anoniem verzoek is. U aanvragen tegen de opslagemulator autoriseren met behulp van Shared Key-verificatie of met een gedeelde toegangshandtekening (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Autoriseren met referenties gedeelde sleutel

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Zie [Azure Storage-verbindingstekenreeksen configureren](../storage-configure-connection-string.md)voor meer informatie over verbindingstekenreeksen .

### <a name="authorize-with-a-shared-access-signature"></a>Autoriseren met een handtekening voor gedeelde toegang

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Sommige Azure-opslagclientbibliotheken, zoals de Xamarin-bibliotheek, ondersteunen alleen verificatie met een SAS-token (Shared Access Signature). U het SAS-token maken met [Storage Explorer](https://storageexplorer.com/) of een andere toepassing die verificatie van gedeelde sleutels ondersteunt.

U ook een SAS-token genereren met Azure PowerShell. In het volgende voorbeeld wordt een SAS-token gegenereerd met volledige machtigingen voor een blobcontainer:

1. Installeer Azure PowerShell als u dat nog niet hebt gedaan (het gebruik van de nieuwste versie van de Azure PowerShell-cmdlets wordt aanbevolen). Zie [Azure PowerShell installeren en configureren](/powershell/azure/install-Az-ps)voor installatie-instructies.
2. Open Azure PowerShell en voer de `CONTAINER_NAME` volgende opdrachten uit, vervangen door een naam van uw keuze:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

De resulterende uri met gedeelde toeganghandtekening voor de nieuwe container moet vergelijkbaar zijn met:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

De gedeelde toegangshandtekening die met dit voorbeeld is gemaakt, is één dag geldig. De handtekening verleent volledige toegang (lezen, schrijven, verwijderen, lijst) aan blobs in de container.

Zie Beperkte toegang tot Azure [Storage-bronnen verlenen met behulp van gedeelde toegangshandtekeningen (SAS)](storage-sas-overview.md)voor meer informatie over handtekeningen voor gedeelde toegang.

## <a name="addressing-resources-in-the-storage-emulator"></a>Bronnen in de opslagemulator aanpakken

De serviceeindpunten voor de opslagemulator verschillen van de eindpunten voor een Azure-opslagaccount. De lokale computer doet geen domeinnaamresolutie, waarbij de eindpunten voor opslagemulators lokale adressen moeten zijn.

Wanneer u een resource in een Azure-opslagaccount aanbiedt, gebruikt u het volgende schema. De accountnaam maakt deel uit van de URI-hostnaam en de resource die wordt geadresseerd, maakt deel uit van het URI-pad:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

De volgende URI is bijvoorbeeld een geldig adres voor een blob in een Azure-opslagaccount:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Omdat de lokale computer geen domeinnaamresolutie doet, maakt de accountnaam deel uit van het URI-pad in plaats van de hostnaam. Gebruik de volgende URI-indeling voor een bron in de opslagemulator:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Het volgende adres kan bijvoorbeeld worden gebruikt voor toegang tot een blob in de opslagemulator:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

De serviceeindpunten voor de opslagemulator zijn:

* Blob-service:`http://127.0.0.1:10000/<account-name>/<resource-path>`
* Wachtrijservice:`http://127.0.0.1:10001/<account-name>/<resource-path>`
* Tabelservice:`http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Het aanpakken van de account secundair met RA-GRS

Vanaf versie 3.1 ondersteunt de opslagemulator georedundante replicatie (RA-GRS) met leestoegang. U hebt toegang tot de secundaire locatie door -secundair aan de accountnaam toe te schrijven. Het volgende adres kan bijvoorbeeld worden gebruikt voor toegang tot een blob met behulp van het alleen-lezen secundaire in de opslagemulator:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Gebruik de opslagclientbibliotheek voor .NET-versie 3.2 of hoger voor programmatische toegang tot het secundaire middel met de opslagemulator. Zie de [Microsoft Azure Storage Client Library voor .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) voor meer informatie.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Referentie voor opdrachtregelvoor opslagemulator

Vanaf versie 3.0 wordt een consolevenster weergegeven wanneer u de Opslagemulator start. Gebruik de opdrachtregel in het consolevenster om de emulator te starten en te stoppen. U ook vragen naar status en andere bewerkingen uitvoeren vanaf de opdrachtregel.

> [!NOTE]
> Als u de Microsoft Azure compute emulator hebt geïnstalleerd, wordt een systeemladepictogram weergegeven wanneer u de Opslagemulator start. Klik met de rechtermuisknop op het pictogram om een menu te onthullen dat een grafische manier biedt om de Storage Emulator te starten en te stoppen.
>
>

### <a name="command-line-syntax"></a>De syntaxis van opdrachtregel

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opties

Typ `/help` bij de opdrachtprompt om een lijst met opties te zien.

| Optie | Beschrijving | Opdracht | Argumenten |
| --- | --- | --- | --- |
| **Begin** |Start de opslagemulator op. |`AzureStorageEmulator.exe start [-inprocess]` |*-Reprocess:* Start de emulator in het huidige proces in plaats van een nieuw proces te maken. |
| **Stoppen** |Stopt de opslagemulator. |`AzureStorageEmulator.exe stop` | |
| **Status** |Hiermee wordt de status van de opslagemulator afgedrukt. |`AzureStorageEmulator.exe status` | |
| **Wissen** |Wist de gegevens in alle services die op de opdrachtregel zijn opgegeven. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob:* Wist blobgegevens. <br/>*wachtrij:* maakt wachtrijgegevens gewist. <br/>*tabel*: Wist tabelgegevens. <br/>*alles*: Wist alle gegevens in alle diensten. |
| **Init** |Doet eenmalige initialisatie om de emulator in te stellen. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-serverserverName\instanceName:* Hiermee geeft u de server op die de SQL-instantie host. <br/>*-sqlinstance instanceName:* hiermee geeft u de naam op van de SQL-instantie die moet worden gebruikt in de standaardserverinstantie. <br/>*-forcecreate*: Krachten creatie van de SQL-database, zelfs als deze al bestaat. <br/>*-skipcreate*: Slaat het maken van de SQL-database over. Dit heeft voorrang op -forcecreate.<br/>*-reserveports*: Pogingen om de HTTP-poorten die aan de services zijn gekoppeld te reserveren.<br/>*-unreserveports*: Pogingen om reserveringen voor de HTTP-poorten die aan de services zijn gekoppeld, te verwijderen. Dit heeft voorrang op -reserveports.<br/>*-inprocess*: Voert initialisatie uit in het huidige proces in plaats van een nieuw proces te spawnen. Het huidige proces moet worden gestart met verhoogde machtigingen als u poortreserveringen wijzigt. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Verschillen tussen de opslagemulator en Azure Storage

Omdat de opslagemulator een lokale geëmuleerde omgeving is, zijn er verschillen tussen het gebruik van de emulator en een Azure-opslagaccount in de cloud:

* De opslagemulator ondersteunt slechts één vast account en een bekende verificatiesleutel.
* De opslagemulator is geen schaalbare opslagservice en ondersteunt geen groot aantal gelijktijdige clients.
* Zoals beschreven in [Adresseringsbronnen in de opslagemulator,](#addressing-resources-in-the-storage-emulator)worden resources anders aangepakt in de opslagemulator ten opzichte van een Azure-opslagaccount. Het verschil is omdat domeinnaamresolutie beschikbaar is in de cloud, maar niet op de lokale computer.
* Vanaf versie 3.1 ondersteunt het opslagemulatoraccount georedundante replicatie (RA-GRS) met leestoegang. In de emulator hebben alle accounts RA-GRS ingeschakeld en is er nooit vertraging tussen de primaire en secundaire replica's. De statistieken van Blob-service statistieken, wachtrijservicestatistieken en statistieken van tabelservicestatistieken worden ondersteund op `LastSyncTime` het secundaire account en geven altijd de waarde van het antwoordelement als de huidige tijd terug volgens de onderliggende SQL-database.
* De eindpunten voor de bestandsservice en de SMB-protocolservice worden momenteel niet ondersteund in de opslagemulator.
* Als u een versie van de opslagservices gebruikt die niet door de emulator wordt ondersteund, retourneert de emulator een VersionNotSupportedByEmulator-fout (HTTP-statuscode 400 - Bad Request).

### <a name="differences-for-blob-storage"></a>Verschillen voor Blob-opslag

De volgende verschillen zijn van toepassing op Blob-opslag in de emulator:

* De opslagemulator ondersteunt alleen blobformaten tot 2 GB.
* De maximale lengte van een blobnaam in de opslagemulator is 256 tekens, terwijl de maximale lengte van een blobnaam in Azure Storage 1024 tekens is.
* Incrementele kopie maakt het mogelijk snapshots van overschreven blobs te kopiëren, waardoor een fout op de service wordt geretourneerd.
* Get Page Ranges Diff werkt niet tussen gekopieerde momentopnamen met Incrementele kopieerblob.
* Een Put Blob-bewerking kan slagen tegen een blob die bestaat in de opslagemulator met een actieve lease, zelfs als de lease-id niet is opgegeven in de aanvraag.
* Blob-bewerkingen toevoegen worden niet ondersteund door de emulator. Als u een bewerking op een toevoegende blob probeert, wordt een FeatureNotSupportedByEmulator-fout (HTTP-statuscode 400 - Bad Request) geretourneerd.

### <a name="differences-for-table-storage"></a>Verschillen voor tabelopslag

De volgende verschillen zijn van toepassing op tabelopslag in de emulator:

* Datumeigenschappen in de tabelservice in de opslagemulator ondersteunen alleen het bereik dat wordt ondersteund door SQL Server 2005 (ze moeten later zijn dan 1 januari 1753). Alle data voor 1 januari 1753 worden in deze waarde gewijzigd. De nauwkeurigheid van datums is beperkt tot de precisie van SQL Server 2005, wat betekent dat datums nauwkeurig zijn tot 1/300ste van een seconde.
* De opslagemulator ondersteunt partitiesleutel- en rijsleuteleigenschapwaarden van minder dan 512 bytes per stuk. De totale grootte van de accountnaam, tabelnaam en belangrijke eigenschapsnamen samen mag niet meer dan 900 bytes bedragen.
* De totale grootte van een rij in een tabel in de opslagemulator is beperkt tot minder dan 1 MB.
* In de opslagemulator ondersteunen `Edm.Guid` eigenschappen `Edm.Binary` van `Equal (eq)` gegevenstype of ondersteunen alleen de en `NotEqual (ne)` vergelijkingsoperatoren in queryfiltertekenreeksen.

### <a name="differences-for-queue-storage"></a>Verschillen voor wachtrijopslag

Er zijn geen verschillen die specifiek zijn voor wachtrijopslag in de emulator.

## <a name="storage-emulator-release-notes"></a>Opslag emulator release notes

### <a name="version-510"></a>Versie 5.10

* De opslagemulator weigert versie 2019-07-07 van de opslagservices op blob-, wachtrij- en tabelserviceeindpunten niet.

### <a name="version-59"></a>Versie 5.9

* De opslagemulator weigert versie 2019-02-02 van de opslagservices op blob-, wachtrij- en tabelserviceeindpunten niet.

### <a name="version-58"></a>Versie 5.8

* De opslagemulator weigert versie 2018-11-09 van de opslagservices op blob-, wachtrij- en tabelserviceeindpunten niet.

### <a name="version-57"></a>Versie 5.7

* Fixed a bug that would cause a crash if logging was enabled.

### <a name="version-56"></a>Versie 5.6

* De opslagemulator ondersteunt nu versie 2018-03-28 van de opslagservices op blob-, wachtrij- en tabelserviceeindpunten.

### <a name="version-55"></a>Versie 5.5

* De opslagemulator ondersteunt nu versie 2017-11-09 van de opslagservices op blob-, wachtrij- en tabelserviceeindpunten.
* Er is ondersteuning toegevoegd voor de eigenschap **Blob Created,** waarmee de creatietijd van de blob wordt geretourneerd.

### <a name="version-54"></a>Versie 5.4

* Om de stabiliteit van de installatie te verbeteren, probeert de emulator niet langer poorten te reserveren bij de installatietijd. Als u poortreserveringen wilt, gebruikt u de optie *-reservepoorten* van de **opdracht init** om deze op te geven.

### <a name="version-53"></a>Versie 5.3

* De opslagemulator ondersteunt nu versie 2017-07-29 van de opslagservices op blob-, wachtrij- en tabelserviceeindpunten.

### <a name="version-52"></a>Versie 5.2

* De opslagemulator ondersteunt nu versie 2017-04-17 van de opslagservices op blob-, wachtrij- en tabelserviceeindpunten.
* Fixed a bug where table property values waren not being properly coded.

### <a name="version-51"></a>Versie 5.1

* Fixed a bug where the `DataServiceVersion` storage emulator was returning the header in some responses where the service was not.

### <a name="version-50"></a>Versie 5.0

* De installer van de opslagemulator controleert niet meer op bestaande MSSQL- en .NET Framework-installaties.
* De opslag emulator installer maakt niet langer de database als onderdeel van de installatie. Database wordt nog steeds gemaakt indien nodig als onderdeel van het opstarten.
* Het maken van gegevens vereist geen hoogte meer.
* Poortreserveringen zijn niet langer nodig voor het opstarten.
* Hiermee worden de `init` `-reserveports` volgende opties `-unreserveports` toegevoegd aan `-skipcreate`: (vereist hoogte), (vereist hoogte), .
* De optie Gebruikersinterface van De Emulator van de Opslag op het systeemdienbladpictogram lanceert nu de opdracht-lijninterface. De oude GUI is niet meer beschikbaar.
* Sommige DLL's zijn verwijderd of hernoemd.

### <a name="version-46"></a>Versie 4.6

* De opslagemulator ondersteunt nu versie 2016-05-31 van de opslagservices op blob-, wachtrij- en tabelserviceeindpunten.

### <a name="version-45"></a>Versie 4.5

* Fixed a bug that caused installation and initialization to fail when the backing database is renamed.

### <a name="version-44"></a>Versie 4.4

* De opslagemulator ondersteunt nu versie 2015-12-11 van de opslagservices op blob-, wachtrij- en tabelserviceeindpunten.
* De opslag emulator garbage collection van blob gegevens is nu efficiënter bij het omgaan met grote aantallen blobs.
* Fixed a bug that caused container ACL XML to be validated iets differently from how the storage service does it.
* Fixed a bug that sometimes caused max and min DateTime values to be reported in the incorrect time zone.

### <a name="version-43"></a>Versie 4.3

* De opslagemulator ondersteunt nu versie 2015-07-08 van de opslagservices op blob-, wachtrij- en tabelserviceeindpunten.

### <a name="version-42"></a>Versie 4.2

* De opslagemulator ondersteunt nu versie 2015-04-05 van de opslagservices op blob-, wachtrij- en tabelserviceeindpunten.

### <a name="version-41"></a>Versie 4.1

* De opslagemulator ondersteunt nu versie 2015-02-21 van de opslagservices op blob-, wachtrij- en tabelserviceeindpunten. Het ondersteunt niet de nieuwe Functies van De Blob van het toevoegen.
* De emulator retourneert nu een zinvolle foutmelding voor niet-ondersteunde versies van opslagservices. We raden u aan de nieuwste versie van de emulator te gebruiken. Als u een VersionNotSupportedByEmulator-fout krijgt (HTTP-statuscode 400 - Bad Request), downloadt u de nieuwste versie van de emulator.
* Fixed a bug wherein a race condition caused table entity data to be incorrect during concurrent merge operations.

### <a name="version-40"></a>Versie 4.0

* De uitvoerbare opslagemulator is omgedoopt tot *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versie 3.2

* De opslagemulator ondersteunt nu versie 2014-02-14 van de opslagservices op blob-, wachtrij- en tabelserviceeindpunten. Eindpunten van bestandsservice worden momenteel niet ondersteund in de opslagemulator. Zie [Versioning voor de Azure Storage Services](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) voor meer informatie over versie 2014-02-14.

### <a name="version-31"></a>Versie 3.1

* Read-access geo-redundante opslag (RA-GRS) wordt nu ondersteund in de opslagemulator. De `Get Blob Service Stats` `Get Queue Service Stats`, `Get Table Service Stats` , en API's worden ondersteund voor het account secundaire en zal altijd de waarde van de LastSyncTime reactie element als de huidige tijd volgens de onderliggende SQL-database. Gebruik de opslagclientbibliotheek voor .NET-versie 3.2 of hoger voor programmatische toegang tot het secundaire middel met de opslagemulator. Zie de Microsoft Azure Storage Client Library for .NET Reference voor meer informatie.

### <a name="version-30"></a>Versie 3.0

* De Azure-opslagemulator wordt niet langer verzonden in hetzelfde pakket als de compute emulator.
* De grafische gebruikersinterface van de opslagemulator is afgeschaft. Het is vervangen door een scriptbare command-line interface. Zie Naslaginformatie over de opdrachtregelinterface voor meer informatie over de opdrachtregelinterface. De grafische interface blijft aanwezig in versie 3.0, maar kan alleen worden geopend wanneer de Compute Emulator is geïnstalleerd door met de rechtermuisknop op het systeemladepictogram te klikken en de Emulator-gebruikersinterface van Opslag weer te geven.
* Versie 2013-08-15 van de Azure-opslagservices wordt nu volledig ondersteund. (Voorheen werd deze versie alleen ondersteund door Storage Emulator versie 2.2.1 Preview.)

## <a name="next-steps"></a>Volgende stappen

* Evalueer de cross-platform, community-maintained open-source storage emulator [Azurite.](https://github.com/arafato/azurite) 
* [Azure Storage-voorbeelden met .NET](../storage-samples-dotnet.md) bevatten koppelingen naar verschillende codevoorbeelden die u gebruiken bij het ontwikkelen van uw toepassing.
* U de [Microsoft Azure Storage Explorer](https://storageexplorer.com) gebruiken om te werken met bronnen in uw cloudopslagaccount en in de opslagemulator.
