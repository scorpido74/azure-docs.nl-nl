---
title: Azure Monitor HTTP-API voor gegevensverzamelaar | Microsoft Documenten
description: U de AZURE Monitor HTTP Data Collector API gebruiken om POST JSON-gegevens toe te voegen aan een Log Analytics-werkruimte vanaf elke client die de REST API kan aanroepen. In dit artikel wordt beschreven hoe u de API gebruiken en worden voorbeelden gegeven van het publiceren van gegevens met behulp van verschillende programmeertalen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/01/2019
ms.openlocfilehash: f12e9e90b99a055945c34398ff5351334c344253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666749"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Logboekgegevens naar Azure Monitor verzenden met de HTTP Data Collector API (public preview)
In dit artikel ziet u hoe u de HTTP Data Collector API gebruiken om logboekgegevens naar Azure Monitor te verzenden vanuit een REST API-client.  Het beschrijft hoe u gegevens opmaken die door uw script of toepassing zijn verzameld, deze in een aanvraag opnemen en die aanvraag laten geautoriseerd door Azure Monitor.  Voorbeelden zijn beschikbaar voor PowerShell, C#en Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> De Azure Monitor HTTP Data Collector API bevindt zich in een openbare preview.

## <a name="concepts"></a>Concepten
U de HTTP Data Collector API gebruiken om logboekgegevens naar een Log Analytics-werkruimte in Azure Monitor te verzenden vanuit elke client die een REST-API kan aanroepen.  Dit kan een runbook in Azure Automation zijn die beheergegevens verzamelt van Azure of een andere cloud, of het kan een alternatief beheersysteem zijn dat Azure Monitor gebruikt om logboekgegevens te consolideren en te analyseren.

Alle gegevens in de werkruimte Log Analytics worden opgeslagen als record bij een bepaald recordtype.  U maakt uw gegevens op om naar de HTTP Data Collector API te verzenden als meerdere records in JSON.  Wanneer u de gegevens indient, wordt een afzonderlijke record gemaakt in de opslagplaats voor elke record in de aanvraagpayload.


![OVERZICHT HTTP Data Collector](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Een aanvraag maken
Als u de HTTP Data Collector API wilt gebruiken, maakt u een POST-verzoek met de gegevens die u wilt verzenden in JavaScript Object Notatie (JSON).  In de volgende drie tabellen worden de kenmerken weergegeven die voor elke aanvraag vereist zijn. We beschrijven elk attribuut later in het artikel in meer detail.

### <a name="request-uri"></a>Aanvraag-URI
| Kenmerk | Eigenschap |
|:--- |:--- |
| Methode |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Inhoudstype |application/json |

### <a name="request-uri-parameters"></a>URI-parameters aanvragen
| Parameter | Beschrijving |
|:--- |:--- |
| CustomerID |De unieke id voor de werkruimte Log Analytics. |
| Resource |De naam van de API-bron: /api/logs. |
| API-versie |De versie van de API te gebruiken met deze aanvraag. Momenteel is het 2016-04-01. |

### <a name="request-headers"></a>Aanvraagheaders
| Header | Beschrijving |
|:--- |:--- |
| Autorisatie |De autorisatiehandtekening. Later in het artikel u lezen hoe u een HMAC-SHA256-header maakt. |
| Log-type |Geef het recordtype op van de gegevens die worden verzonden. Kan alleen letters, cijfers en underscore (_) bevatten en mag niet meer dan 100 tekens bevatten. |
| x-ms-datum |De datum waarop de aanvraag is verwerkt, in RFC 1123-indeling. |
| x-ms-AzureResourceId | Resource-id van de Azure-bron waar de gegevens aan moeten worden gekoppeld. Hiermee wordt de [eigenschap _ResourceId](log-standard-properties.md#_resourceid) gevuld en kunnen de gegevens worden opgenomen in [resourcecontextquery's.](design-logs-deployment.md#access-mode) Als dit veld niet is opgegeven, worden de gegevens niet opgenomen in query's in resourcecontext. |
| tijdgegenereerd-veld | De naam van een veld in de gegevens die de tijdstempel van het gegevensitem bevat. Als u een veld opgeeft, wordt de inhoud ervan gebruikt voor **TimeGenerated**. Als dit veld niet is opgegeven, is de standaardinstelling voor **TimeGenerated** de tijd dat het bericht wordt ingenomen. De inhoud van het berichtveld moet het ISO 8601-formaat YYYY-MM-DDThh:mm:ssZ volgen. |

## <a name="authorization"></a>Autorisatie
Elk verzoek om de AZURE Monitor HTTP Data Collector API te gebruiken, moet een autorisatiekop bevatten. Als u een aanvraag wilt verifiëren, moet u de aanvraag ondertekenen met de primaire of de secundaire sleutel voor de werkruimte die de aanvraag indient. Geef die handtekening vervolgens door als onderdeel van het verzoek.   

Hier is de indeling voor de autorisatiekoptekst:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* is de unieke id voor de werkruimte Log Analytics. *Handtekening* is een [Op hash gebaseerde Message Authentication Code (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) die is opgebouwd uit het verzoek en vervolgens wordt berekend met behulp van het [SHA256-algoritme.](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx) Vervolgens codeer je het met Base64-codering.

Gebruik deze indeling om de **handtekeningtekenreeks van SharedKey** te coderen:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Hier is een voorbeeld van een handtekeningtekenreeks:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Wanneer u de handtekeningtekenreeks hebt, codeert u deze met het HMAC-SHA256-algoritme op de utf-8-gecodeerde tekenreeks en codeert u het resultaat als Base64. Gebruik deze indeling:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

De voorbeelden in de volgende secties hebben een voorbeeldcode waarmee u een autorisatiekoptekst maken.

## <a name="request-body"></a>Aanvraagbody
De hoofdtekst van de boodschap moet in JSON staan. Het moet een of meer records bevatten met de eigenschapsnaam en waardeparen in de volgende indeling. De naam van de eigenschap kan alleen letters, cijfers en underscore (_) bevatten.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

U meerdere records samen batchin één aanvraag met behulp van de volgende indeling. Alle records moeten hetzelfde recordtype zijn.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Recordtype en eigenschappen
U definieert een aangepast recordtype wanneer u gegevens verzendt via de Azure Monitor HTTP Data Collector API. Momenteel u geen gegevens schrijven naar bestaande recordtypen die zijn gemaakt door andere gegevenstypen en -oplossingen. Azure Monitor leest de binnenkomende gegevens en maakt vervolgens eigenschappen die overeenkomen met de gegevenstypen van de waarden die u invoert.

Elke aanvraag voor de API voor gegevensverzamelaar moet een **logboektekst** bevatten met de naam voor het recordtype. Het achtervoegsel **_CL** wordt automatisch toegevoegd aan de naam die u invoert om deze te onderscheiden van andere logboektypen als een aangepast logboek. Als u bijvoorbeeld de naam **MyNewRecordType**invoert, maakt Azure Monitor een record met het type **MyNewRecordType_CL**. Dit helpt ervoor te zorgen dat er geen conflicten zijn tussen door gebruikers gemaakte typenamen en die welke worden verzonden in huidige of toekomstige Microsoft-oplossingen.

Azure Monitor voegt een achtervoegsel toe aan de naam van de eigenschap om het gegevenstype van een eigenschap te identificeren. Als een eigenschap een null-waarde bevat, wordt de eigenschap niet opgenomen in die record. In deze tabel vindt u het type eigenschapsgegevens en het bijbehorende achtervoegsel:

| Type eigenschappengegevens | Achtervoegsel |
|:--- |:--- |
| Tekenreeks |_s |
| Booleaans |_b |
| Double |_d |
| Datum/tijd |_t |
| GUID (opgeslagen als tekenreeks) |_g |

Het gegevenstype dat Azure Monitor voor elke eigenschap gebruikt, is afhankelijk van of het recordtype voor de nieuwe record al bestaat.

* Als het recordtype niet bestaat, maakt Azure Monitor een nieuwe met de conclusie van het JSON-type om het gegevenstype voor elke eigenschap voor de nieuwe record te bepalen.
* Als het recordtype wel bestaat, probeert Azure Monitor een nieuwe record te maken op basis van bestaande eigenschappen. Als het gegevenstype voor een eigenschap in de nieuwe record niet overeenkomt en niet kan worden geconverteerd naar het bestaande type, of als de record een eigenschap bevat die niet bestaat, maakt Azure Monitor een nieuwe eigenschap met het relevante achtervoegsel.

Met deze inzending wordt bijvoorbeeld een record gemaakt met drie eigenschappen, **number_d,** **boolean_b**en **string_s:**

![Voorbeeldrecord 1](media/data-collector-api/record-01.png)

Als u vervolgens dit volgende item hebt ingediend, met alle waarden die zijn opgemaakt als tekenreeksen, worden de eigenschappen niet gewijzigd. Deze waarden kunnen worden geconverteerd naar bestaande gegevenstypen:

![Voorbeeldrecord 2](media/data-collector-api/record-02.png)

Maar als u vervolgens deze volgende indiening hebt gedaan, zou Azure Monitor de nieuwe eigenschappen **maken die boolean_d** en **string_d.** Deze waarden kunnen niet worden geconverteerd:

![Voorbeeldrecord 3](media/data-collector-api/record-03.png)

Als u vervolgens de volgende vermelding hebt ingediend voordat het recordtype is gemaakt, maakt Azure Monitor een record met drie eigenschappen, **number_s,** **boolean_s**en **string_s**. In dit item wordt elk van de oorspronkelijke waarden opgemaakt als een tekenreeks:

![Voorbeeldrecord 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Gereserveerde eigenschappen
De volgende eigenschappen zijn gereserveerd en mogen niet worden gebruikt in een aangepast recordtype. U ontvangt een foutmelding als uw payload een van deze eigendomsnamen bevat.

- tenant

## <a name="data-limits"></a>Gegevenslimieten
Er zijn enkele beperkingen rond de gegevens die zijn geplaatst op de API voor het verzamelen van Azure-monitorgegevens.

* Maximaal 30 MB per post naar Azure Monitor Data Collector API. Dit is een groottelimiet voor één bericht. Als de gegevens van één bericht van meer dan 30 MB, moet u de gegevens opsplitsen tot kleinere segmenten en ze tegelijkertijd verzenden.
* Maximaal 32 KB-limiet voor veldwaarden. Als de veldwaarde groter is dan 32 KB, worden de gegevens afgekapt.
* Aanbevolen maximum aantal velden voor een bepaald type is 50. Dit is een praktische grens vanuit het oogpunt van bruikbaarheid en zoekervaring.  
* Een tabel in een werkruimte Log Analytics ondersteunt slechts maximaal 500 kolommen (een veld in dit artikel genoemd). 
* Het maximum aantal tekens voor de kolomnaam is 500.

## <a name="return-codes"></a>Retourcodes
De HTTP-statuscode 200 betekent dat de aanvraag is ontvangen voor verwerking. Dit geeft aan dat de bewerking is voltooid.

In deze tabel vindt u de volledige set statuscodes waarmee de service kan retourneren:

| Code | Status | Foutcode | Beschrijving |
|:--- |:--- |:--- |:--- |
| 200 |OK | |Het verzoek is met succes geaccepteerd. |
| 400 |Slecht verzoek |InactiefKlant |De werkruimte is gesloten. |
| 400 |Slecht verzoek |Ongeldige apiversie |De API-versie die u hebt opgegeven, is niet herkend door de service. |
| 400 |Slecht verzoek |Ongeldige Klantid |De opgegeven werkruimte-id is ongeldig. |
| 400 |Slecht verzoek |OngeldigDataFormat |Ongeldig JSON is ingediend. De antwoordinstantie bevat mogelijk meer informatie over het oplossen van de fout. |
| 400 |Slecht verzoek |OngeldigLogType |Het opgegeven logboektype bevatte speciale tekens of numerieke tekens. |
| 400 |Slecht verzoek |Missingapiversie |De API-versie is niet opgegeven. |
| 400 |Slecht verzoek |MissingContentType MissingContentType |Het inhoudstype is niet opgegeven. |
| 400 |Slecht verzoek |MissingLogType MissingLogType |Het vereiste type waardelogboek is niet opgegeven. |
| 400 |Slecht verzoek |Niet-ondersteundeContentType |Het inhoudstype is niet ingesteld op **toepassing/json**. |
| 403 |Verboden |Ongeldig autorisatie |De service kan de aanvraag niet verifiëren. Controleer of de werkruimte-id en verbindingssleutel geldig zijn. |
| 404 |Niet gevonden | | De opgegeven URL is onjuist of het verzoek is te groot. |
| 429 |Te veel aanvragen | | De service heeft een groot aantal gegevens van uw account. Probeer het verzoek later opnieuw. |
| 500 |Interne serverfout |Niet-opgegevenFout |Er is een interne fout opgetreden in de service. Probeer het verzoek opnieuw. |
| 503 |Service niet beschikbaar |ServiceNiet beschikbaar |De service is momenteel niet beschikbaar om aanvragen te ontvangen. Probeer uw verzoek opnieuw. |

## <a name="query-data"></a>Querygegevens
Als u gegevens wilt opvragen die zijn ingediend door de AZURE Monitor HTTP Data Collector API, zoekt u naar records met **Type** die gelijk is aan de **logtypewaarde** die u hebt opgegeven, toegevoegd aan **_CL**. Als u bijvoorbeeld **MyCustomLog**hebt gebruikt, retourneert `MyCustomLog_CL`u alle records met .

## <a name="sample-requests"></a>Voorbeeldaanvragen
In de volgende secties vindt u voorbeelden van het indienen van gegevens naar de Azure Monitor HTTP Data Collector API met behulp van verschillende programmeertalen.

Ga voor elk voorbeeld de volgende stappen uit om de variabelen voor de autorisatiekop tekst in te stellen:

1. Zoek in de Azure-portal uw log analytics-werkruimte.
2. Selecteer **Geavanceerde instellingen** en vervolgens Verbonden **bronnen**.
2. Selecteer rechts van **workspace-id**het kopieerpictogram en plak de id vervolgens als de waarde van de variabele **Klant-id.**
3. Selecteer rechts van **primaire sleutel**het kopieerpictogram en plak de id vervolgens als de waarde van de variabele **Gedeelde sleutel.**

U ook de variabelen voor het logboektype en de JSON-gegevens wijzigen.

### <a name="powershell-sample"></a>Voorbeeld van PowerShell
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C#-voorbeeld
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Python 2-voorbeeld
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```
## <a name="alternatives-and-considerations"></a>Alternatieven en overwegingen
Hoewel de API voor gegevensverzamelaar de meeste van uw behoeften moet dekken om gegevens in openbare vorm in Azure-logboeken te verzamelen, zijn er gevallen waarin een alternatief nodig kan zijn om een aantal beperkingen van de API te overwinnen. Al uw opties zijn als volgt, belangrijke overwegingen opgenomen:

| Alternatieve | Beschrijving | Het meest geschikt voor |
|---|---|---|
| [Aangepaste gebeurtenissen:](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties)Native SDK-gebaseerde opname in Application Insights | Application Insights, meestal geinstrumenteerd via een SDK binnen uw toepassing, biedt u de mogelijkheid om aangepaste gegevens te verzenden via aangepaste gebeurtenissen. | <ul><li> Gegevens die binnen uw toepassing worden gegenereerd, maar niet door SDK worden opgehaald via een van de standaardgegevenstypen (aanvragen, afhankelijkheden, uitzonderingen, enzovoort).</li><li> Gegevens die het vaakst gecorreleerd zijn met andere toepassingsgegevens in Application Insights </li></ul> |
| Api voor gegevensverzamelaar in Azure-monitorlogboeken | De Data Collector API in Azure Monitor Logs is een volledig open manier om gegevens in te nemen. Alle gegevens die zijn opgemaakt in een JSON-object kunnen hier worden verzonden. Eenmaal verzonden, zal het worden verwerkt, en beschikbaar in Logs te worden gecorreleerd met andere gegevens in Logs of tegen andere Application Insights gegevens. <br/><br/> Het is vrij eenvoudig om de gegevens als bestanden te uploaden naar een Azure Blob blob, van waaruit deze bestanden worden verwerkt en geüpload naar Log Analytics. Zie [dit](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) artikel voor een voorbeeld implementatie van een dergelijke pijplijn. | <ul><li> Gegevens die niet noodzakelijkerwijs worden gegenereerd binnen een toepassing die is uitgevoerd binnen Application Insights.</li><li> Voorbeelden hiervan zijn opzoek- en feitentabellen, referentiegegevens, vooraf geaggregeerde statistieken, enzovoort. </li><li> Bedoeld voor gegevens die worden vergeleken met andere Azure Monitor-gegevens (Application Insights, andere logboekengegevenstypen, Beveiligingscentrum, Azure Monitor voor containers/VM's, enzovoort). </li></ul> |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview) | Azure Data Explorer (ADX) is het dataplatform dat application insights analytics en Azure Monitor Logs van de bevoegdheid heeft. Nu algemeen beschikbaar ("GA"), met behulp van het dataplatform in zijn ruwe vorm biedt u volledige flexibiliteit (maar die de overhead van het beheer) over het cluster (RBAC, retentiesnelheid, schema, enzovoort). ADX biedt veel [opnameopties,](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview#ingestion-methods) waaronder [CSV-, TSV- en JSON-bestanden.](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master) | <ul><li> Gegevens die niet worden gecorreleerd met andere gegevens onder Application Insights of Logs. </li><li> Gegevens die geavanceerde opname- of verwerkingsmogelijkheden vereisen, zijn momenteel niet beschikbaar in Azure Monitor-logboeken. </li></ul> |


## <a name="next-steps"></a>Volgende stappen
- Gebruik de [API voor logboekzoeken](../log-query/log-query-overview.md) om gegevens op te halen uit de werkruimte Log Analytics.

- Meer informatie over hoe [u een gegevenspijplijn maakt met de API voor gegevensverzamelaar](create-pipeline-datacollector-api.md) met behulp van de workflow Logic Apps naar Azure Monitor.
