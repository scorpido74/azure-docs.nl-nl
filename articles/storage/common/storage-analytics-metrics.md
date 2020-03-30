---
title: Azure Storage Analytics-statistieken (Klassiek)
description: Meer informatie over het gebruik van statistieken in Azure Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 897ae1fa474de8726ed0caa1def162a00e142dbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268403"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Storage Analytics-statistieken (Klassiek)

Storage Analytics kan statistieken opslaan die geaggregeerde transactiestatistieken en capaciteitsgegevens over aanvragen voor een opslagservice bevatten. Transacties worden gerapporteerd op zowel api-bewerkingsniveau als op het niveau van de opslagservice en de capaciteit wordt gerapporteerd op het niveau van de opslagservice. Statistiekengegevens kunnen worden gebruikt om het gebruik van opslagservices te analyseren, problemen met aanvragen tegen de opslagservice te diagnosticeren en de prestaties van toepassingen die een service gebruiken te verbeteren.  

 Storage Analytics-statistieken zijn standaard ingeschakeld voor nieuwe opslagaccounts. U metrische gegevens configureren in de [Azure-portal;](https://portal.azure.com/) Zie Een [opslagaccount controleren in de Azure-portal](/azure/storage/storage-monitor-storage-account)voor meer informatie. U Storage Analytics ook programmatisch inschakelen via de REST API of de clientbibliotheek. Gebruik de bewerkingen Serviceeigenschappen instellen om Storage Analytics voor elke service in te schakelen.  

> [!NOTE]
> Storage Analytics-statistieken zijn beschikbaar voor de services Blob, Queue, Table en File.
> Storage Analytics-statistieken zijn nu Klassieke statistieken. Microsoft raadt aan [om storagemetrics te](storage-metrics-in-azure-monitor.md) gebruiken in Azure Monitor in plaats van Storage Analytics-statistieken.

## <a name="transaction-metrics"></a>Metrische gegevens voor transacties  
 Een robuuste set gegevens wordt geregistreerd met intervallen per uur of op minuten voor elke opslagservice en de gevraagde API-bewerking, inclusief binnendringen/uitgang, beschikbaarheid, fouten en gecategoriseerde aanvraagpercentages. U een volledige lijst met transactiegegevens bekijken in het onderwerp [Schema voor statistieken van Storage Analytics.](/rest/api/storageservices/storage-analytics-metrics-table-schema)  

 Transactiegegevens worden op twee niveaus geregistreerd: het serviceniveau en het API-bewerkingsniveau. Op serviceniveau worden statistieken met een samenvatting van alle gevraagde API-bewerkingen elk uur naar een tabelentiteit geschreven, zelfs als er geen aanvragen aan de service zijn gedaan. Op het niveau van de API-bewerking worden statistieken alleen naar een entiteit geschreven als de bewerking binnen dat uur is aangevraagd.  

 Als u bijvoorbeeld een **GetBlob-bewerking** uitvoert op uw Blob-service, worden de aanvraag voor Storage Analytics en deze opgenomen in de geaggregeerde gegevens voor zowel de Blob-service als de **GetBlob-bewerking.** Als er echter gedurende het uur geen **GetBlob-bewerking** wordt aangevraagd, wordt een entiteit niet naar de *$MetricsTransactionsBlob* voor die bewerking geschreven.  

 Transactiestatistieken worden geregistreerd voor zowel gebruikersverzoeken als verzoeken van Storage Analytics zelf. Er worden bijvoorbeeld aanvragen van Storage Analytics om logboeken en tabelentiteiten te schrijven geregistreerd.

## <a name="capacity-metrics"></a>Metrische capaciteitsgegevens  

> [!NOTE]
>  Momenteel zijn capaciteitsstatistieken alleen beschikbaar voor de Blob-service.

 Capaciteitsgegevens worden dagelijks geregistreerd voor de Blob-service van een opslagaccount en er worden twee tabelentiteiten geschreven. De ene entiteit biedt statistieken voor gebruikersgegevens `$logs` en de andere bevat statistieken over de blobcontainer die wordt gebruikt door Storage Analytics. De *tabel $MetricsCapacityBlob* bevat de volgende statistieken:  

- **Capaciteit:** de hoeveelheid opslagruimte die wordt gebruikt door de Blob-service van het opslagaccount in bytes.  
- **ContainerCount:** het aantal blobcontainers in de Blob-service van het opslagaccount.  
- **ObjectCount:** het aantal toegewezen en niet-vastgelegde blok- of paginablobs in de Blob-service van het opslagaccount.  

  Zie [Tabelschema voor opslaganalysestatistieken voor](/rest/api/storageservices/storage-analytics-metrics-table-schema)meer informatie over de capaciteitsstatistieken.  

## <a name="how-metrics-are-stored"></a>Hoe statistieken worden opgeslagen  

 Alle metrische gegevens voor elk van de opslagservices worden opgeslagen in drie tabellen die voor die service zijn gereserveerd: één tabel voor transactiegegevens, één tabel voor minieme transactiegegevens en een andere tabel voor capaciteitsinformatie. Transactie- en minutentransactiegegevens bestaan uit aanvraag- en antwoordgegevens en capaciteitsinformatie bestaat uit opslaggebruiksgegevens. Uurstatistieken, minutenstatistieken en capaciteit voor de Blob-service van een opslagaccount zijn toegankelijk in tabellen met de naam die zijn beschreven in de volgende tabel.  

|Statistiekenniveau|Tabelnamen|Ondersteund voor versies|  
|-------------------|-----------------|----------------------------|  
|Uurstatistieken, primaire locatie|- $MetricsTransactionsBlob<br />- $MetricsTransactionsTable<br />- $MetricsTransactionsQueue|Alleen versies vóór 2013-08-15. Hoewel deze namen nog steeds worden ondersteund, is het raadzaam om over te schakelen naar het gebruik van de onderstaande tabellen.|  
|Uurstatistieken, primaire locatie|- $MetricsHourPrimaryTransactionsBlob<br />- $MetricsHourPrimaryTransactionsTable<br />- $MetricsHourPrimaryTransactionsQueue<br />- $MetricsHourPrimaryTransactionsFile|Alle versies. Ondersteuning voor bestandsservicestatistieken is alleen beschikbaar in versie 2015-04-05 en hoger.|  
|Minutenstatistieken, primaire locatie|- $MetricsMinutePrimaryTransactionsBlob<br />- $MetricsMinutePrimaryTransactionsTable<br />- $MetricsMinutePrimaryTransactionsQueue<br />- $MetricsMinutePrimaryTransactionsFile|Alle versies. Ondersteuning voor bestandsservicestatistieken is alleen beschikbaar in versie 2015-04-05 en hoger.|  
|Uurstatistieken, secundaire locatie|- $MetricsHourSecondaryTransactionsBlob<br />- $MetricsHourSecondaryTransactionsTable<br />- $MetricsHourSecondaryTransactionsQueue|Alle versies. Georedundante replicatie met leestoegang moet zijn ingeschakeld.|  
|Minutenstatistieken, secundaire locatie|- $MetricsMinuteSecondaryTransactionsBlob<br />- $MetricsMinuteSecondaryTransactionsTable<br />- $MetricsMinuteSecondaryTransactionsQueue|Alle versies. Georedundante replicatie met leestoegang moet zijn ingeschakeld.|  
|Capaciteit (alleen Blob-service)|$MetricsCapacityBlob|Alle versies.|  

 Deze tabellen worden automatisch gemaakt wanneer Storage Analytics is ingeschakeld voor een eindpunt voor de opslagservice. Ze zijn toegankelijk via de naamruimte van het `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`opslagaccount, bijvoorbeeld: . De statistiekentabellen worden niet weergegeven in een aanbiedingsbewerking en moeten rechtstreeks toegankelijk zijn via de tabelnaam.  

## <a name="enable-metrics-using-the-azure-portal"></a>Statistieken inschakelen met de Azure-portal
Volg deze stappen om statistieken in te schakelen in de [Azure-portal:](https://portal.azure.com)

1. Ga naar uw opslagaccount.
1. Selecteer **Diagnostische instellingen (klassiek)** in het **menuvenster.**
1. Controleer of **status** is ingesteld **op Aan**.
1. Selecteer de statistieken voor de services die u wilt controleren.
1. Geef een bewaarbeleid op om aan te geven hoe lang het bewaren van statistieken en logboekgegevens duurt.
1. Selecteer **Opslaan**.

Met de [Azure-portal](https://portal.azure.com) u momenteel geen minieme statistieken configureren in uw opslagaccount. u moet minutenstatistieken inschakelen met PowerShell of programmatisch.

## <a name="enable-storage-metrics-using-powershell"></a>Opslagstatistieken inschakelen met PowerShell  
U PowerShell op uw lokale machine gebruiken om opslagstatistieken in uw opslagaccount te configureren met behulp van de Azure PowerShell-cmdlet **Get-AzStorageServiceMetricsProperty** om de huidige instellingen op te halen en de eigenschap van cmdlet **Set-AzStorageServiceMetricsom** de huidige instellingen te wijzigen.  

De cmdlets die storagemetrics beheren, gebruiken de volgende parameters:  

* **ServiceType**, mogelijke waarde zijn **Blob,** **Queue**, **Table**en **File**.
* **MetricsType**, mogelijke waarden zijn **Uur** en **Minuut**.  
* **MetricsLevel**, mogelijke waarden zijn:
* **Geen**: Schakelt de bewaking uit.
* **Service:** verzamelt statistieken zoals ingress/uitgang, beschikbaarheid, latentie en succespercentages, die worden samengevoegd voor de blob-, wachtrij-, tabel- en bestandsservices.
* **ServiceAndApi:** Verzamelt naast de Service-statistieken dezelfde set statistieken voor elke opslagbewerking in de Azure Storage Service-service-API.

De volgende opdracht schakelt bijvoorbeeld minutenstatistieken in voor de blobservice in uw opslagaccount met de bewaarperiode ingesteld op vijf dagen: 

> [!NOTE]
> Met deze opdracht wordt ervan uitgegaan dat u `Connect-AzAccount` zich met de opdracht hebt aangemeld bij uw Azure-abonnement.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Vervang `<resource-group-name>` de tijdelijke aanduidingswaarde door de naam van uw resourcegroep.
        
* Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.



Met de volgende opdracht worden het huidige uurstatistiekenniveau en bewaardagen opgehaald voor de blobservice in uw standaardopslagaccount:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Zie: [Azure PowerShell installeren en configureren](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)voor informatie over het configureren van de Azure PowerShell-cmdlets om met uw Azure-abonnement te werken en hoe u het standaardopslagaccount selecteren dat moet worden gebruikt.  

## <a name="enable-storage-metrics-programmatically"></a>Opslagstatistieken programmatisch inschakelen  
Naast het gebruik van de Azure-portal of de Azure PowerShell-cmdlets om opslagstatistieken te beheren, u ook een van de Azure Storage API's gebruiken. Als u bijvoorbeeld een .NET-taal gebruikt, u de opslagclientbibliotheek gebruiken.  

De klassen **CloudBlobClient,** **CloudQueueClient,** **CloudTableClient**en **CloudFileClient** hebben allemaal methoden zoals **SetServiceProperties** en **SetServicePropertiesAsync** waarmee een object **ServiceProperties** als parameter wordt gebruikt. U het object **ServiceProperties** gebruiken om opslagstatistieken te configureren. In het volgende C#-fragment ziet u bijvoorbeeld hoe u het niveau van de statistieken en bewaardagen voor de wachtrijstatistieken per uur wijzigen:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Zie [Opslagclientbibliotheek voor .NET voor](https://msdn.microsoft.com/library/azure/mt347887.aspx)meer informatie over het gebruik van een .NET-taal voor het configureren van opslagstatistieken.  

Zie [Storage Analytics inschakelen en configureren](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)voor algemene informatie over het configureren van opslagstatistieken met behulp van de REST-API.  

##  <a name="viewing-storage-metrics"></a>Opslagstatistieken weergeven  
Nadat u Storage Analytics-statistieken hebt geconfigureerd om uw opslagaccount te controleren, registreert Storage Analytics de statistieken in een reeks bekende tabellen in uw opslagaccount. U grafieken configureren om uurstatistieken weer te geven in de [Azure-portal:](https://portal.azure.com)

1. Navigeer naar uw opslagaccount in de [Azure-portal.](https://portal.azure.com)
1. Selecteer **Statistieken (klassiek)** in het **menublad** voor de service waarvan u de statistieken wilt weergeven.
1. Klik op de grafiek die u wilt configureren.
1. Selecteer in het **blad Grafiek bewerken** het **gebiedtijd,** **het grafiektype en**de statistieken die u in de grafiek wilt weergeven.

In het gedeelte **Monitoring (klassiek)** van het menublad van uw opslagaccount in de [Azure-portal](#metrics-alerts)u waarschuwingsregels configureren, zoals het verzenden van e-mailwaarschuwingen om u te waarschuwen wanneer een specifieke statistiek een bepaalde waarde bereikt.

Als u de statistieken voor langdurige opslag wilt downloaden of lokaal wilt analyseren, moet u een hulpprogramma gebruiken of code schrijven om de tabellen te lezen. U moet de minieme statistieken downloaden voor analyse. De tabellen worden niet weergegeven als u alle tabellen in uw opslagaccount vermeldt, maar u ze rechtstreeks op naam openen. Veel hulpprogramma's voor het browsen op opslagzijn op de hoogte van deze tabellen en stellen u in staat deze rechtstreeks te bekijken (zie [Azure Storage Client Tools](/azure/storage/storage-explorers) voor een lijst met beschikbare hulpprogramma's).

||||  
|-|-|-|  
|**Statistieken**|**Tabelnamen**|**Opmerkingen**|  
|Uurstatistieken|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|In versies vóór 2013-08-15 stonden deze tabellen bekend als:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Statistieken voor de bestandsservice zijn beschikbaar vanaf versie 2015-04-05.|  
|Minutenstatistieken|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Kan alleen worden ingeschakeld met PowerShell of programmatisch.<br /><br /> Statistieken voor de bestandsservice zijn beschikbaar vanaf versie 2015-04-05.|  
|Capaciteit|$MetricsCapacityBlob|Alleen blobservice.|  

De volledige details van de schema's voor deze tabellen vindt u in [het Schema van De Statistieken van Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema). In de onderstaande voorbeeldrijen worden slechts een subset van de beschikbare kolommen weergegeven, maar worden enkele belangrijke kenmerken van de manier waarop opslagstatistieken deze statistieken opslaan, weergegeven:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Timestamp**|**TotalRequests**|**TotalBillableRequests TotalBillableRequests TotalBillableRequests TotalBill**|**TotalIngress**|**TotalEgress**|**Beschikbaarheid**|**AverageE2ELatency**|**AverageServerLatency**|**ProcentSucces**|  
|20140522T1100|gebruiker; Alle|2014-05-22T1:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|gebruiker; Queryentiteiten|2014-05-22T1:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|gebruiker; QueryEntiteit|2014-05-22T1:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|gebruiker; Entiteit bijwerken|2014-05-22T1:01:16.7650250Z|1|1|771|217|100|9|6|100|  

In dit voorbeeld worden met de gegevens van de minuut statistieken gebruikt de partitiesleutel de tijd op minutenresolutie. De rijsleutel identificeert het type informatie dat in de rij is opgeslagen en deze bestaat uit twee stukjes informatie, het toegangstype en het type aanvraag:  

-   Het toegangstype is **een gebruiker** of **systeem,** waarbij **de gebruiker** verwijst naar alle gebruikersverzoeken naar de opslagservice en het **systeem** verwijst naar verzoeken van Storage Analytics.  

-   Het aanvraagtype is **in** welk geval het een overzichtsregel is of de specifieke API zoals **QueryEntity** of **UpdateEntity**identificeert.  

De bovenstaande voorbeeldgegevens tonen alle records voor één minuut (vanaf 11:00 uur), dus het aantal **queryentiteitaanvragen** plus het aantal **QueryEntity-aanvragen** plus het aantal **UpdateEntity-aanvragen** optellen tot zeven, het totaal dat op de gebruiker wordt **weergegeven:Alle** rij. Op dezelfde manier u de gemiddelde end-to-end latentie 104.4286 op de **gebruiker afleiden:Alle** rij door te berekenen (((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Statistieken waarschuwingen
U moet overwegen waarschuwingen in te stellen in de [Azure-portal,](https://portal.azure.com) zodat u automatisch op de hoogte wordt gesteld van belangrijke wijzigingen in het gedrag van uw opslagservices. Als u een hulpprogramma voor storage explorer gebruikt om deze metrische gegevens in een beperkte indeling te downloaden, u Microsoft Excel gebruiken om de gegevens te analyseren. Zie [Azure Storage Client Tools](/azure/storage/storage-explorers) voor een lijst met beschikbare hulpprogramma's voor storage explorer. U waarschuwingen configureren in het **waarschuwingsblad (klassiek)** blad, toegankelijk onder **Monitoring (klassiek)** in het menublad van het opslagaccount.

> [!IMPORTANT]
> Er kan een vertraging optreden tussen een opslaggebeurtenis en wanneer de bijbehorende gegevens per uur of minuut worden geregistreerd. In het geval van minieme statistieken kunnen enkele minuten gegevens tegelijk worden geschreven. Dit kan ertoe leiden dat transacties van eerdere minuten worden samengevoegd in de transactie voor de huidige minuut. Wanneer dit gebeurt, beschikt de waarschuwingsservice mogelijk niet over alle beschikbare metrische gegevens voor het geconfigureerde waarschuwingsinterval, wat kan leiden tot onverwacht afschieten van waarschuwingen.
>

## <a name="accessing-metrics-data-programmatically"></a>Programmatisch toegang tot metrische gegevens  
In de volgende vermelding wordt voorbeeld C#-code weergegeven die toegang heeft tot de minutenstatistieken gedurende een aantal minuten en de resultaten in een consolevenster weergeeft. Het codevoorbeeld maakt gebruik van de versie 4.x of hoger van azure storageclientbibliotheek, die de klasse **CloudAnalyticsClient** bevat, waarmee de toegang tot de metrische tabellen in opslag wordt vereenvoudigd.  

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

## <a name="billing-on-storage-metrics"></a>Facturering op opslagstatistieken  
Schrijf aanvragen om tabelentiteiten voor statistieken te maken, worden in rekening gebracht tegen de standaardtarieven die van toepassing zijn op alle Azure Storage-bewerkingen.  

Lees- en verwijderverzoeken van metrische gegevens door een klant zijn ook factureerbaar tegen standaardtarieven. Als u een beleid voor gegevensbewaring hebt geconfigureerd, worden er geen kosten in rekening gebracht wanneer Azure Storage oude metrische gegevens verwijdert. Als u echter analysegegevens verwijdert, wordt uw account in rekening gebracht voor de verwijderingsbewerkingen.  

De capaciteit die wordt gebruikt door de metrische tabellen is ook factureerbaar. U het volgende gebruiken om de hoeveelheid capaciteit te schatten die wordt gebruikt voor het opslaan van metrische gegevens:  

-   Als elk uur een service elke API in elke service gebruikt, wordt elk uur ongeveer 148 KB aan gegevens opgeslagen in de transactietabellen met statistieken als u zowel het overzicht op service- als API-niveau hebt ingeschakeld.  
-   Als binnen elk uur een service elke API in de service gebruikt, wordt elk uur ongeveer 12 KB aan gegevens opgeslagen in de tabellen voor metrische gegevensals u alleen een samenvatting op serviceniveau hebt ingeschakeld.  
-   De capaciteitstabel voor blobs heeft elke dag twee rijen toegevoegd, op voorwaarde dat u zich hebt aangemeld voor logboeken. Dit houdt in dat elke dag de grootte van deze tabel met maximaal 300 bytes toeneemt.

## <a name="next-steps"></a>Volgende stappen
* [Een opslagaccount controleren](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Tabelschema voor statistieken voor opslaganalyse](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Opgeslagen analyses geregistreerde bewerkingen en statusberichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Logboekregistratie van Opslaganalyse](storage-analytics-logging.md)
