---
title: Logboekregistratie van Azure Opslaganalyse
description: Meer informatie over het registreren van gegevens over aanvragen die zijn gedaan tegen Azure Storage.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5b94a97f1286e1273300014e4eef140be412436b
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637185"
---
# <a name="azure-storage-analytics-logging"></a>Logboekregistratie van Azure Opslaganalyse

Opslaganalyse registreert gedetailleerde informatie over geslaagde en mislukte aanvragen bij een opslagservice. Deze informatie kan worden gebruikt voor het bewaken van afzonderlijke aanvragen en voor het vaststellen van problemen met een opslagservice. Aanvragen worden op een best-effort basis geregistreerd.

 Logboekregistratie van Opslaganalyse is niet standaard ingeschakeld voor uw opslagaccount. U het inschakelen in de [Azure-portal;](https://portal.azure.com/) Zie Een [opslagaccount controleren in de Azure-portal](/azure/storage/storage-monitor-storage-account)voor meer informatie. U Storage Analytics ook programmatisch inschakelen via de REST API of de clientbibliotheek. Gebruik de [eigenschappen van Blob-service,](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API) [Service-eigenschappen voor wachtrijservice](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)en [Uitvoeren van tabelservice-eigenschappen](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) om Storage Analytics voor elke service in te schakelen.

 Logboekvermeldingen worden alleen gemaakt als er aanvragen zijn ingediend tegen het eindpunt van de service. Als een opslagaccount bijvoorbeeld activiteit heeft in het Blob-eindpunt, maar niet in de eindpunten tabel of wachtrij, worden alleen logboeken met betrekking tot de Blob-service gemaakt.

> [!NOTE]
>  Logboekregistratie voor opslaganalyse is momenteel alleen beschikbaar voor de blob-, wachtrij- en tabelservices. Premium-opslagaccounts worden echter niet ondersteund.

## <a name="requests-logged-in-logging"></a>Aanvragen die zijn aangemeld bij het aanmelden
### <a name="logging-authenticated-requests"></a>Geverifieerde aanvragen vastleggen

 De volgende typen geverifieerde aanvragen worden geregistreerd:

- Geslaagde aanvragen
- Mislukte aanvragen, inclusief time-out-, beperkings-, netwerk-, autorisatiefouten en overige fouten
- Aanvragen waarvoor een SAS (Shared Access Signature) of OAuth is gebruikt, inclusief geslaagde en mislukte aanvragen
- Aanvragen voor analysegegevens

  Verzoeken van Storage Analytics zelf, zoals het maken of verwijderen van logboeken, worden niet geregistreerd. Een volledige lijst met de geregistreerde gegevens wordt gedocumenteerd in de onderwerpen [Storage Analytics Logged Operations en Status Messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) en Storage Analytics Log [Format.](/rest/api/storageservices/storage-analytics-log-format)

### <a name="logging-anonymous-requests"></a>Anonieme aanvragen registreren

 De volgende typen anonieme verzoeken worden geregistreerd:

- Geslaagde aanvragen
- Serverfouten
- Time-outfouten voor zowel client als server
- Mislukte GET-aanvragen met foutcode 304 (Niet gewijzigd)

  Alle andere mislukte anonieme verzoeken worden niet geregistreerd. Een volledige lijst met de geregistreerde gegevens wordt gedocumenteerd in de onderwerpen [Storage Analytics Logged Operations en Status Messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) en Storage Analytics Log [Format.](/rest/api/storageservices/storage-analytics-log-format)

## <a name="how-logs-are-stored"></a>Hoe logboeken worden opgeslagen

Alle logboeken worden opgeslagen in blokblobs in een container met de naam `$logs`, die automatisch wordt gemaakt wanneer Storage Analytics is ingeschakeld voor een opslagaccount. De `$logs` container bevindt zich in de naamruimte blob `http://<accountname>.blob.core.windows.net/$logs`van het opslagaccount, bijvoorbeeld: . Deze container kan niet worden verwijderd nadat Storage Analytics is ingeschakeld, hoewel de inhoud ervan kan worden verwijderd. Als u uw gereedschap voor het browsen op opslag gebruikt om rechtstreeks naar de container te navigeren, ziet u alle blobs die uw logboekregistratiegegevens bevatten.

> [!NOTE]
>  De `$logs` container wordt niet weergegeven wanneer een containervermeldingwordt uitgevoerd, zoals de bewerking Containers lijst. Het moet direct toegankelijk zijn. U bijvoorbeeld de bewerking Lijstblobs gebruiken om `$logs` toegang te krijgen tot de blobs in de container.

Als aanvragen worden geregistreerd, uploadt Storage Analytics tussentijdse resultaten als blokken. Periodiek zal Storage Analytics deze blokken vastleggen en beschikbaar maken als een blob. Het kan tot een uur duren voordat logboekgegevens in de blobs in de **$logs** container worden weergegeven, omdat de frequentie waarmee de opslagservice de logschrijvers doorspoelt. Dubbele records kunnen bestaan voor logboeken die in hetzelfde uur zijn gemaakt. U bepalen of een record een duplicaat is door het **requestId-** en **operationeelnummer te** controleren.

Als u een groot aantal logboekgegevens met meerdere bestanden per uur hebt, u de blobmetagegevens gebruiken om te bepalen welke gegevens het logboek bevat door de blobmetagegevensvelden te onderzoeken. Dit is ook handig omdat er soms een vertraging kan optreden terwijl gegevens naar de logboekbestanden worden geschreven: de blob-metagegevens geven een nauwkeurigere indicatie van de blob-inhoud dan de blobnaam.

Met de meeste tools voor het browsen op opslag u de metagegevens van blobs bekijken. u deze informatie ook lezen met PowerShell of programmatisch. Het volgende PowerShell-fragment is een voorbeeld van het filteren van de lijst met logboekblobs op naam om een tijd op te geven, en door metagegevens om alleen die logboeken te identificeren die **schrijfbewerkingen** bevatten.  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Zie [Blobresources](https://msdn.microsoft.com/library/azure/hh452233.aspx) en [Eigenschappen en metagegevens voor blobbronnen](https://msdn.microsoft.com/library/azure/dd179404.aspx)instellen en ophalen voor blobbronnen programmatisch opsommen voor informatie over blobs.  

### <a name="log-naming-conventions"></a>Log naamgevingconventies

 Elk logboek wordt in de volgende indeling geschreven:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 In de volgende tabel wordt elk kenmerk in de logboeknaam beschreven:

|Kenmerk|Beschrijving|
|---------------|-----------------|
|`<service-name>`|De naam van de opslagservice. Bijvoorbeeld: `blob`, `table`, of`queue`|
|`YYYY`|Het viercijferige jaar voor het logboek. Bijvoorbeeld: `2011`|
|`MM`|De tweecijferige maand voor het logboek. Bijvoorbeeld: `07`|
|`DD`|De tweecijferige dag voor het logboek. Bijvoorbeeld: `31`|
|`hh`|Het tweecijferige uur dat het beginuur voor de logboeken aangeeft, in de 24-uurs UTC-indeling. Bijvoorbeeld: `18`|
|`mm`|Het tweecijferige getal dat de beginminuut voor de logboeken aangeeft. **Let op:**  Deze waarde wordt niet ondersteund in de huidige versie van `00`Storage Analytics en de waarde ervan zal altijd zijn.|
|`<counter>`|Een nulteller met zes cijfers die het aantal logboekblobs aangeeft dat in een uur tijd voor de opslagservice is gegenereerd. Deze teller `000000`begint bij . Bijvoorbeeld: `000001`|

 Het volgende is een volledige voorbeeldlogboeknaam die de bovenstaande voorbeelden combineert:

 `blob/2011/07/31/1800/000001.log`

 Het volgende is een voorbeeld URI die kan worden gebruikt om toegang te krijgen tot de bovenstaande log:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Wanneer een opslagaanvraag wordt geregistreerd, correleert de resulterende logboeknaam met het uur waarop de gevraagde bewerking is voltooid. Als een GetBlob-aanvraag bijvoorbeeld is voltooid om 18:30 uur op 7/31/2011, wordt het logboek geschreven met het volgende voorvoegsel:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metagegevens van logboeken

 Alle logboekblobs worden opgeslagen met metagegevens die kunnen worden gebruikt om te bepalen welke logboekgegevens de blob bevat. In de volgende tabel wordt elk kenmerk met metagegevens beschreven:

|Kenmerk|Beschrijving|
|---------------|-----------------|
|`LogType`|Beschrijft of het logboek informatie bevat met betrekking tot het lezen, schrijven of verwijderen van bewerkingen. Deze waarde kan één type of een combinatie van alle drie bevatten, gescheiden door komma's.<br /><br /> Voorbeeld 1:`write`<br /><br /> Voorbeeld 2:`read,write`<br /><br /> Voorbeeld 3:`read,write,delete`|
|`StartTime`|De vroegste tijd van een vermelding in `YYYY-MM-DDThh:mm:ssZ`het logboek, in de vorm van . Bijvoorbeeld: `2011-07-31T18:21:46Z`|
|`EndTime`|De laatste tijd van een vermelding in `YYYY-MM-DDThh:mm:ssZ`het logboek, in de vorm van . Bijvoorbeeld: `2011-07-31T18:22:09Z`|
|`LogVersion`|De versie van de logboekindeling.|

 In de volgende lijst worden met volledige voorbeeldmetagegevens weergegeven met behulp van de bovenstaande voorbeelden:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Opslaglogboekregistratie inschakelen

U Opslaglogboekregistratie inschakelen met Azure-portal, PowerShell en SDK's voor opslag.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Opslaglogboekregistratie inschakelen met de Azure-portal  

Gebruik in de Azure-portal het hoofd van de **diagnostische instellingen (klassiek)** om storagelogging te beheren, toegankelijk vanuit het gedeelte **Monitoring (klassiek)** van het menublad van een **opslagaccount.**

U de opslagservices opgeven die u wilt registreren en de bewaartermijn (in dagen) voor de aangemelde gegevens.  

### <a name="enable-storage-logging-using-powershell"></a>Opslaglogboekregistratie inschakelen met PowerShell  

 U PowerShell op uw lokale machine gebruiken om opslaglogboekregistratie in uw opslagaccount te configureren met behulp van de Azure PowerShell-cmdlet **Get-AzureStorageServiceLoggingProperty** om de huidige instellingen op te halen en de eigenschap cmdlet **Set-AzureStorageServiceLoggingOm** de huidige instellingen te wijzigen.  

 De cmdlets die Storage Logging beheren, gebruiken een parameter **LoggingOperations** die een tekenreeks is die een door komma's gescheiden lijst met aanvraagtypen bevat om in te loggen. De drie mogelijke aanvraagtypen worden **gelezen,** **geschreven**en **verwijderd**. Als u logboekregistratie wilt uitschakelen, gebruikt u de waarde **geen** voor de parameter **LoggingOperations.**  

 De volgende opdracht schakelt over bij het aanmelden voor het lezen, schrijven en verwijderen van aanvragen in de wachtrijservice in uw standaardopslagaccount met bewaarinstellingen ingesteld op vijf dagen:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 Met de volgende opdracht wordt logboekregistratie voor de tabelservice in uw standaardopslagaccount uitgeschakeld:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Zie: [Azure PowerShell installeren en configureren](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)voor informatie over het configureren van de Azure PowerShell-cmdlets om met uw Azure-abonnement te werken en hoe u het standaardopslagaccount selecteren dat moet worden gebruikt.  

### <a name="enable-storage-logging-programmatically"></a>Logboekregistratie van opslag programmatisch inschakelen  

 Naast het gebruik van de Azure-portal of de Azure PowerShell-cmdlets om opslaglogboekregistratie te beheren, u ook een van de Azure Storage API's gebruiken. Als u bijvoorbeeld een .NET-taal gebruikt, u de opslagclientbibliotheek gebruiken.  

 De klassen **CloudBlobClient,** **CloudQueueClient**en **CloudTableClient** hebben allemaal methoden zoals **SetServiceProperties** en **SetServicePropertiesAsync** waarmee een object **ServiceProperties** als parameter wordt gebruikt. U het object **ServiceProperties** gebruiken om Opslaglogboekregistratie te configureren. In het volgende C#-fragment ziet u bijvoorbeeld hoe u wijzigen wat er is geregistreerd en hoe de bewaarperiode voor wachtrijlogboekregistratie wordt gewijzigd:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Zie [Naslaginformatie](https://msdn.microsoft.com/library/azure/dn261237.aspx)over opslagclientbibliotheek voor meer informatie over het gebruik van een .NET-taal voor het configureren van opslaglogboekregistratie.  

 Zie [Storage Analytics inschakelen en configureren](https://msdn.microsoft.com/library/azure/hh360996.aspx)voor algemene informatie over het configureren van storagelogging met de REST API.  

## <a name="download-storage-logging-log-data"></a>Logboekgegevens voor logboekregistratie voor opslag downloaden

 Als u uw logboekgegevens wilt bekijken en analyseren, moet u de blobs met de logboekgegevens waarin u geïnteresseerd bent, downloaden naar een lokale machine. Met veel tools voor het browsen op opslag u blobs downloaden van uw opslagaccount. U het Azure Storage-team ook gebruik maken van de command-line Azure Copy Tool [AzCopy](storage-use-azcopy-v10.md) om uw logboekgegevens te downloaden.  
 
>[!NOTE]
> De `$logs` container is niet geïntegreerd met Event Grid, dus u ontvangt geen meldingen wanneer logboekbestanden worden geschreven. 

 Om ervoor te zorgen dat u de loggegevens downloadt waarin u geïnteresseerd bent en om te voorkomen dat u meer dan eens dezelfde loggegevens downloadt:  

-   Gebruik de datum- en tijdnaamgevingsconventie voor blobs met logboekgegevens om bij te houden welke blobs u al hebt gedownload voor analyse om te voorkomen dat dezelfde gegevens meer dan één keer opnieuw worden gedownload.  

-   Gebruik de metagegevens op de blobs met logboekgegevens om de specifieke periode te identificeren waarvoor de blob loggegevens bevat om de exacte blob te identificeren die u moet downloaden.  

Zie Aan de slag [met AzCopy](storage-use-azcopy-v10.md) om aan de slag te gaan met AzCopy 

In het volgende voorbeeld ziet u hoe u de logboekgegevens voor de wachtrijservice downloaden voor de uren die op 20 mei 2014 om 09:00 uur, 10 uur en 11 uur 's 11 uur 's nachts beginnen.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Zie [Specifieke bestanden downloaden](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files)voor meer informatie over het downloaden van specifieke bestanden.

Wanneer u uw logboekgegevens hebt gedownload, u de logboekvermeldingen in de bestanden bekijken. Deze logboekbestanden maken gebruik van een afgebakende tekstindeling die veel hulpprogramma's voor logboeklezen kunnen ontleden, waaronder Microsoft Message Analyzer (zie voor meer informatie de handleiding [Monitoring, Diagnosing en Troubleshooting Microsoft Azure Storage).](storage-monitoring-diagnosing-troubleshooting.md) Verschillende tools hebben verschillende faciliteiten voor het opmaken, filteren, sorteren, advertentie zoeken naar de inhoud van uw logbestanden. Zie De bestandsindeling en -inhoud van [storage analytics log](/rest/api/storageservices/storage-analytics-log-format) en opgeslagen analyses voor meer informatie over de bestandsindeling en -inhoud van het logboek voor opslaglogboeken . [Storage Analytics Logged Operations and Status Messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)

## <a name="next-steps"></a>Volgende stappen

* [Opslaganalyselogboekindeling](/rest/api/storageservices/storage-analytics-log-format)
* [Opgeslagen analyses geregistreerde bewerkingen en statusberichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Storage Analytics Metrics (klassiek)](storage-analytics-metrics.md)
