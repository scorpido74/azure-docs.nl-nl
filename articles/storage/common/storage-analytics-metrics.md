---
title: Azure Storage Analytics-metrische gegevens (klassiek)
description: Meer informatie over het gebruik van metrische gegevens in Azure Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 897ae1fa474de8726ed0caa1def162a00e142dbe
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514775"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Storage Analytics-metrische gegevens (klassiek)

Opslaganalyse kunt metrische gegevens opslaan die geaggregeerde trans actie-statistieken en capaciteitgegevens over aanvragen voor een opslag service bevatten. Trans acties worden gerapporteerd op het niveau van de API-bewerking en op het niveau van de opslag service, en de capaciteit wordt gerapporteerd op het niveau van de opslag service. Metrische gegevens kunnen worden gebruikt voor het analyseren van het gebruik van opslag Services, het diagnosticeren van problemen met aanvragen die zijn gedaan voor de opslag service en voor het verbeteren van de prestaties van toepassingen die gebruikmaken van een service.  

 Opslaganalyse metrische gegevens zijn standaard ingeschakeld voor nieuwe opslag accounts. U kunt metrische gegevens configureren in de [Azure Portal](https://portal.azure.com/); Zie [een opslag account bewaken in de Azure Portal](/azure/storage/storage-monitor-storage-account)voor meer informatie. U kunt Opslaganalyse ook via een programma inschakelen via de REST API of de client bibliotheek. Gebruik de bewerkingen voor het instellen van service-eigenschappen om Opslaganalyse in te scha kelen voor elke service.  

> [!NOTE]
> Er zijn Opslaganalyse metrische gegevens beschikbaar voor de blob-, wachtrij-, tabel-en bestands Services.
> Opslaganalyse metrische gegevens zijn nu klassieke metrische gegevens. Micro soft raadt u [aan metrische opslag gegevens te gebruiken in azure monitor](storage-metrics-in-azure-monitor.md) in plaats van Opslaganalyse metrische gegevens.

## <a name="transaction-metrics"></a>Metrische gegevens van trans actie  
 Een robuuste set gegevens wordt vastgelegd in elk uur of per minuut intervallen voor elke opslag service en de aangevraagde API-bewerking, met inbegrip van binnenkomend/uitgaand, Beschik baarheid, fouten en gecategoriseerde aanvraag percentages. U ziet een volledige lijst met transactie gegevens in het onderwerp [Opslaganalyse metrische gegevens tabel schema](/rest/api/storageservices/storage-analytics-metrics-table-schema) .  

 Transactie gegevens worden op twee niveaus vastgelegd: het service niveau en het API-bewerkings niveau. Op service niveau worden statistieken met een samen vatting van alle aangevraagde API-bewerkingen elk uur naar een tabel entiteit geschreven, zelfs als er geen aanvragen naar de service zijn verzonden. Bij het API-bewerkings niveau worden statistieken alleen naar een entiteit geschreven als de bewerking binnen dat uur is aangevraagd.  

 Als u bijvoorbeeld een **GetBlob** -bewerking op uw BLOB service uitvoert, worden met Opslaganalyse metrische gegevens de aanvraag geregistreerd en wordt deze in de geaggregeerde data voor zowel de BLOB service als de **GetBlob** -bewerking meegenomen. Als er echter geen **GetBlob** -bewerking wordt aangevraagd tijdens het uur, wordt een entiteit niet naar de *$MetricsTransactionsBlob* geschreven voor die bewerking.  

 Metrische gegevens over trans acties worden vastgelegd voor gebruikers aanvragen en-aanvragen van Opslaganalyse zichzelf. Aanvragen van Opslaganalyse voor het schrijven van Logboeken en tabel entiteiten worden bijvoorbeeld vastgelegd.

## <a name="capacity-metrics"></a>Metrische gegevens over capaciteit  

> [!NOTE]
>  Op dit moment zijn capaciteits gegevens alleen beschikbaar voor de Blob service.

 Capaciteits gegevens worden dagelijks vastgelegd voor de Blob service van een opslag account en er worden twee tabel entiteiten geschreven. Eén entiteit biedt statistieken voor gebruikers gegevens en de andere bevat statistieken over de `$logs` BLOB-container die wordt gebruikt door Opslaganalyse. De tabel *$MetricsCapacityBlob* bevat de volgende statistieken:  

- **Capaciteit**: de hoeveelheid opslag die wordt gebruikt door de BLOB service van het opslag account, in bytes.  
- **ContainerCount**: het aantal BLOB-containers in de BLOB service van het opslag account.  
- **ObjectCount**: het aantal doorgevoerde en niet-toegezegde blok-of pagina-blobs in de BLOB service van het opslag account.  

  Zie [Opslaganalyse tabel met metrische](/rest/api/storageservices/storage-analytics-metrics-table-schema)gegevens voor meer informatie over de metrische gegevens van de capaciteit.  

## <a name="how-metrics-are-stored"></a>Hoe metrische gegevens worden opgeslagen  

 Alle metrische gegevens voor elk van de opslag services worden opgeslagen in drie tabellen die zijn gereserveerd voor die service: één tabel voor transactie gegevens, een tabel voor informatie over de minuut transactie en een andere tabel voor capaciteits informatie. Trans actie-informatie en notulen van trans acties bestaan uit aanvraag-en respons gegevens, en capaciteits informatie bestaat uit gebruiks gegevens voor opslag. De metrische gegevens van het uur, de metrische gegevens over minuten en de capaciteit voor de Blob service van een opslag account zijn toegankelijk in tabellen die worden genoemd, zoals beschreven in de volgende tabel.  

|Niveau metrische gegevens|Tabel namen|Ondersteund voor versies|  
|-------------------|-----------------|----------------------------|  
|Metrische gegevens per uur, primaire locatie|-$MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-$MetricsTransactionsQueue|Versies ouder dan 2013-08-15. Hoewel deze namen nog steeds worden ondersteund, is het raadzaam om te scha kelen naar het gebruik van de tabellen die hieronder worden weer gegeven.|  
|Metrische gegevens per uur, primaire locatie|-$MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-$MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Alle versies. Ondersteuning voor metrische gegevens van bestands service is alleen beschikbaar in versie 2015-04-05 en hoger.|  
|Metrische gegevens over minuten, primaire locatie|-$MetricsMinutePrimaryTransactionsBlob<br />-$MetricsMinutePrimaryTransactionsTable<br />-$MetricsMinutePrimaryTransactionsQueue<br />-$MetricsMinutePrimaryTransactionsFile|Alle versies. Ondersteuning voor metrische gegevens van bestands service is alleen beschikbaar in versie 2015-04-05 en hoger.|  
|Metrische gegevens per uur, secundaire locatie|-$MetricsHourSecondaryTransactionsBlob<br />-$MetricsHourSecondaryTransactionsTable<br />-$MetricsHourSecondaryTransactionsQueue|Alle versies. Geo-redundante replicatie met lees toegang moet zijn ingeschakeld.|  
|Metrische gegevens over minuten, secundaire locatie|-$MetricsMinuteSecondaryTransactionsBlob<br />-$MetricsMinuteSecondaryTransactionsTable<br />-$MetricsMinuteSecondaryTransactionsQueue|Alle versies. Geo-redundante replicatie met lees toegang moet zijn ingeschakeld.|  
|Capaciteit (alleen Blob service)|$MetricsCapacityBlob|Alle versies.|  

 Deze tabellen worden automatisch gemaakt wanneer Opslaganalyse is ingeschakeld voor een opslag service-eind punt. Ze worden geopend via de naam ruimte van het opslag account, bijvoorbeeld: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. De metrische tabellen worden niet weer gegeven in een vermelding en moeten rechtstreeks via de tabel naam worden geopend.  

## <a name="enable-metrics-using-the-azure-portal"></a>Metrische gegevens inschakelen met behulp van de Azure Portal
Voer de volgende stappen uit om metrische gegevens in te scha kelen in de [Azure Portal](https://portal.azure.com):

1. Ga naar uw opslagaccount.
1. Selecteer **Diagnostische instellingen (klassiek)** in het **menu** venster.
1. Zorg ervoor dat de **status** is ingesteld **op aan**.
1. Selecteer de metrische gegevens voor de services die u wilt bewaken.
1. Geef een Bewaar beleid op om aan te geven hoe lang metrieken en logboek gegevens moeten worden bewaard.
1. Selecteer **Opslaan**.

Met de [Azure Portal](https://portal.azure.com) kunt u op dit moment geen metrische gegevens over minuten configureren in uw opslag account. u moet metrische gegevens over de minuut inschakelen met behulp van Power shell of via een programma.

## <a name="enable-storage-metrics-using-powershell"></a>Metrische opslag gegevens inschakelen met behulp van Power shell  
U kunt Power shell op uw lokale machine gebruiken om metrische opslag gegevens te configureren in uw opslag account met behulp van de Azure PowerShell cmdlet **Get-AzStorageServiceMetricsProperty** om de huidige instellingen op te halen en de cmdlet  **Stel-AzStorageServiceMetricsProperty** in om de huidige instellingen te wijzigen.  

De cmdlets die metrische opslag gegevens regelen, gebruiken de volgende para meters:  

* **Service**type, mogelijke waarde zijn **BLOB**, **wachtrij**, **tabel**en **bestand**.
* **MetricsType**, mogelijke waarden zijn **uur** en **minuut**.  
* **MetricsLevel**, mogelijke waarden zijn:
* **Geen**: schakelt bewaking uit.
* **Service**: verzamelt metrische gegevens, zoals binnenkomend/uitgaand, Beschik baarheid, latentie en succes percentages, die worden geaggregeerd voor de blob-, wachtrij-, tabel-en bestands Services.
* **ServiceAndApi**: als aanvulling op de metrische gegevens van de service, verzamelt dezelfde verzameling metrische gegevens voor elke opslag bewerking in de API van de Azure Storage-service.

Met de volgende opdracht worden bijvoorbeeld de metrische gegevens over minuten voor de BLOB-service in uw opslag account met de Bewaar periode ingesteld op vijf dagen: 

> [!NOTE]
> Bij deze opdracht wordt ervan uitgegaan dat u bent aangemeld bij uw Azure-abonnement met behulp van de `Connect-AzAccount` opdracht.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Vervang de waarde van de tijdelijke aanduiding `<resource-group-name>` door de naam van uw resource groep.
        
* Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.



Met de volgende opdracht worden het huidige metrische gegevens niveau en de retentie dagen voor de BLOB-service in uw standaard-opslag account opgehaald:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Zie voor informatie over het configureren van de Azure PowerShell-cmdlets voor het werken met uw Azure-abonnement en het selecteren van het standaard opslag account dat moet worden gebruikt: [Azure PowerShell installeren en configureren](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Metrische opslag gegevens via een programma inschakelen  
Naast het gebruik van de Azure Portal of de Azure PowerShell-cmdlets voor het beheren van metrische gegevens voor opslag, kunt u ook een van de Azure Storage-Api's gebruiken. Als u bijvoorbeeld een .NET-taal gebruikt, kunt u de Storage-client bibliotheek gebruiken.  

De klassen **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**en **CloudFileClient** hebben allemaal methoden als **SetServiceProperties** en **SetServicePropertiesAsync** die een  **ServiceProperties** -object als een para meter. U kunt het **ServiceProperties** -object gebruiken voor het configureren van metrische gegevens over opslag. Het volgende C# code fragment toont bijvoorbeeld hoe u het niveau van metrische gegevens en de retentie dagen voor de metrische gegevens van de wachtrij per uur wijzigt:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Zie [Storage-client bibliotheek voor .net](https://msdn.microsoft.com/library/azure/mt347887.aspx)voor meer informatie over het gebruik van een .net-taal voor het configureren van metrische gegevens voor opslag.  

Zie [Opslaganalyse inschakelen en configureren](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)voor algemene informatie over het configureren van metrische gegevens voor opslag met behulp van de rest API.  

##  <a name="viewing-storage-metrics"></a>Metrische opslag gegevens weer geven  
Nadat u Opslaganalyse metrische gegevens hebt geconfigureerd om uw opslag account te bewaken, Opslaganalyse registreert u de metrische gegevens in een set bekende tabellen in uw opslag account. U kunt grafieken configureren voor het weer geven van metrische gegevens per uur in de [Azure Portal](https://portal.azure.com):

1. Navigeer naar uw opslag account in de [Azure Portal](https://portal.azure.com).
1. Selecteer **metrische gegevens (klassiek)** in de **menu** -Blade van de service waarvan u de metrische gegevens wilt weer geven.
1. Klik op de grafiek die u wilt configureren.
1. Selecteer in de Blade **grafiek bewerken** het **tijds bereik**, het **grafiek type**en de metrische gegevens die u wilt weer geven in de grafiek.

In de sectie **bewaking (klassiek)** van de menu-Blade van uw opslag account in de Azure Portal, kunt u [waarschuwings regels](#metrics-alerts)configureren, zoals het verzenden van e-mail waarschuwingen om u te waarschuwen wanneer een specifieke metriek een bepaalde waarde bereikt.

Als u de metrische gegevens wilt downloaden voor lange termijn opslag of als u ze lokaal wilt analyseren, moet u een hulp programma gebruiken of code schrijven om de tabellen te lezen. U moet de metrische gegevens over minuten voor analyse downloaden. De tabellen worden niet weer gegeven als u alle tabellen in uw opslag account vermeld, maar u kunt ze rechtstreeks op naam openen. Veel hulp middelen voor opslag bladeren zijn op de hoogte van deze tabellen en u kunt ze direct weer geven (Zie [Azure Storage-client hulpprogramma's](/azure/storage/storage-explorers) voor een lijst met beschik bare hulpprogram ma's).

||||  
|-|-|-|  
|**Metrische gegevens**|**Tabel namen**|**Opmerkingen**|  
|Metrische gegevens per uur|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|In versies voorafgaand aan 2013-08-15 werden deze tabellen aangeduid als:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Metrische gegevens voor de bestands service zijn beschikbaar vanaf versie 2015-04-05.|  
|Metrische gegevens over minuten|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Kan alleen worden ingeschakeld met Power shell of via een programma.<br /><br /> Metrische gegevens voor de bestands service zijn beschikbaar vanaf versie 2015-04-05.|  
|Capaciteit|$MetricsCapacityBlob|Alleen Blob service.|  

U kunt de volledige details van de schema's voor deze tabellen vinden op [Opslaganalyse metrische tabel schema](/rest/api/storageservices/storage-analytics-metrics-table-schema). In de onderstaande voor beelden wordt slechts een subset van de beschik bare kolommen weer gegeven, maar u ziet een aantal belang rijke functies van de manier waarop metrische gegevens van opslag deze metrische gegevens opslaan:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Timestamp**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Beschikbaarheid**|**AverageE2ELatency**|**Averageserverlatency aan**|**PercentSuccess**|  
|20140522T1100|gebruiker Hele|2014-05-22T11:01:16.7650250 Z|7|7|4003|46801|100|104,4286|6,857143|100|  
|20140522T1100|gebruiker QueryEntities|2014-05-22T11:01:16.7640250 Z|5|5|2694|45951|100|143,8|7,8|100|  
|20140522T1100|gebruiker QueryEntity|2014-05-22T11:01:16.7650250 Z|1|1|538|633|100|3|3|100|  
|20140522T1100|gebruiker UpdateEntity|2014-05-22T11:01:16.7650250 Z|1|1|771|217|100|9|6|100|  

In dit voor beeld wordt de metrische gegevens voor de partitie sleutel gebruikt voor de omzetting van tijden van minuten. De rij sleutel identificeert het type informatie dat in de rij wordt opgeslagen en dit bestaat uit twee delen van gegevens, het toegangs type en het type aanvraag:  

-   Het toegangs type is een **gebruiker** of **systeem**, waarbij de **gebruiker** verwijst naar alle gebruikers aanvragen naar de opslag service, en het **systeem** verwijst naar aanvragen van Opslaganalyse.  

-   Het aanvraag type is **alle** in dat geval een samenvattings regel, of identificeert de specifieke API, zoals **QueryEntity** of **UpdateEntity**.  

In de bovenstaande voorbeeld gegevens worden alle records voor één minuut weer gegeven (vanaf 11: am), dus het aantal aanvragen voor **QueryEntities** plus het aantal **QueryEntity** -aanvragen plus het aantal aanvragen voor **UpdateEntity** dat tot zeven is opgeteld. Dit is de totaal weer gegeven voor de **gebruiker: alle** rijen. Op dezelfde manier kunt u de gemiddelde end-to-end-latentie 104,4286 voor de **gebruiker afleiden: alle** rijen door te berekenen ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Waarschuwingen voor metrische gegevens
U kunt het beste waarschuwingen instellen in de [Azure Portal](https://portal.azure.com) zodat u automatisch op de hoogte wordt gebracht van belang rijke wijzigingen in het gedrag van uw opslag Services. Als u een opslag Verkenner-hulp programma gebruikt om deze metrische gegevens in een indeling met scheidings tekens te downloaden, kunt u micro soft Excel gebruiken voor het analyseren van de gegevens. Zie [Azure Storage-client hulpprogramma's](/azure/storage/storage-explorers) voor een lijst met beschik bare opslag Verkenner-hulpprogram ma's. U kunt waarschuwingen configureren op de Blade **waarschuwing (klassiek)** , toegankelijk onder **bewaking (klassiek)** op de Blade van het menu voor opslag accounts.

> [!IMPORTANT]
> Er kan een vertraging optreden tussen een opslag gebeurtenis en wanneer de metrische gegevens van het overeenkomstige uur of minuut worden vastgelegd. In het geval van een minuut meet, kunnen enkele minuten aan gegevens tegelijk worden geschreven. Dit kan leiden tot trans acties van voor gaande minuten die in de trans actie voor de huidige minuut worden geaggregeerd. Als dit gebeurt, heeft de waarschuwings service mogelijk niet alle beschik bare metrische gegevens voor het geconfigureerde waarschuwings interval. Dit kan ertoe leiden dat er onverwacht waarschuwingen worden geactiveerd.
>

## <a name="accessing-metrics-data-programmatically"></a>Toegang tot metrische gegevens via een programma  
De volgende vermelding toont voorbeeld C# code die de metrische gegevens van de minuut voor een bereik van minuten opent en de resultaten weergeeft in een console venster. In het code voorbeeld wordt gebruikgemaakt van de Azure Storage-client bibliotheek versie 4. x of hoger, die de klasse **CloudAnalyticsClient** bevat waarmee de toegang tot de metrische tabellen in de opslag wordt vereenvoudigd.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>Facturering op metrische opslag gegevens  
Voor het schrijven van aanvragen voor het maken van tabel entiteiten voor metrische gegevens worden kosten in rekening gebracht tegen de standaard tarieven die van toepassing zijn op alle Azure Storage bewerkingen.  

Het lezen en verwijderen van aanvragen van metrische gegevens door een client is ook te factureren tegen standaard tarieven. Als u een Bewaar beleid voor gegevens hebt geconfigureerd, worden er geen kosten in rekening gebracht wanneer Azure Storage oude metrische gegevens verwijdert. Als u Analytics-gegevens verwijdert, worden er echter kosten in rekening gebracht voor de verwijderings bewerkingen.  

De capaciteit die wordt gebruikt door de tabellen met metrische gegevens is ook Factureerbaar. U kunt het volgende gebruiken om een schatting te maken van de hoeveelheid capaciteit die wordt gebruikt voor het opslaan van metrische gegevens:  

-   Als u elk uur een service gebruikt voor elke API in elke service, wordt er elk uur in de tabellen met metrische trans acties een aantal 148KB gegevens opgeslagen als u zowel service-als API-niveau-samen vatting hebt ingeschakeld.  
-   Als in een service binnen elk uur elke API in de service wordt gebruikt, wordt er elk uur in de 12KB van de metrische gegevens opgeslagen. vervolgens wordt er een overzicht gegeven van de waarden van de metrieke trans acties als u alleen op service niveau hebt ingeschakeld.  
-   De capaciteits tabel voor blobs heeft twee rijen die elke dag worden toegevoegd, vooropgesteld dat u zich hebt aangemeld voor Logboeken. Dit betekent dat elke dag, de grootte van deze tabel, met Maxi maal ongeveer 300 bytes wordt verhoogd.

## <a name="next-steps"></a>Volgende stappen
* [Een opslag account bewaken](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Tabel schema voor metrische gegevens van Opslaganalyse](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Geregistreerde bewerkingen en status berichten Opslaganalyse](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Opslaganalyse logboek registratie](storage-analytics-logging.md)
