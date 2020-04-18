---
title: Zelfstudie - Een zeer beschikbare toepassing bouwen met Blob-opslag
titleSuffix: Azure Storage
description: Gebruik georedundante opslag met leestoegang om uw toepassingsgegevens zeer beschikbaar te maken.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: 27f90edf84fd51e5c13bc082cfaba50e26c54780
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606027"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Zelfstudie: Een zeer beschikbare toepassing bouwen met Blob-opslag

Deze zelfstudie is deel één van een serie. In deze zelfstudie leert u hoe u uw toepassingsgegevens maximaal beschikbaar maakt in Azure.

Wanneer u deze zelfstudie hebt afgerond, hebt u een consoletoepassing die een blob uploadt en ophaalt uit een [geografisch redundant](../common/storage-redundancy.md) opslagaccount met leestoegang (RA-GRS).

RA-GRS werkt door transacties te repliceren van een primaire regio naar een secundaire regio. Dit replicatieproces zorgt ervoor dat de gegevens in de secundaire regio uiteindelijk consistent zijn. De toepassing gebruikt het patroon [Circuit Breaker](/azure/architecture/patterns/circuit-breaker) om te bepalen met welk eindpunt er verbinding moet worden gemaakt, waarbij er automatisch wordt overgeschakeld tussen eindpunten terwijl er fouten en herstelbewerkingen worden gesimuleerd.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

In deel 1 van de reeks leert u het volgende:

> [!div class="checklist"]
> * Create a storage account
> * De verbindingsreeks instellen
> * De consoletoepassing uitvoeren

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

# <a name="net"></a>[.NET](#tab/dotnet)

* Installeer [Visual Studio 2019](https://www.visualstudio.com/downloads/) met de **Azure-ontwikkelworkload.**

  ![Azure-ontwikkeling (onder Web en Cloud)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python"></a>[Python](#tab/python)

* [Python](https://www.python.org/downloads/) installeren
* [Azure Storage-SDK voor Python](https://github.com/Azure/azure-storage-python) downloaden en installeren

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

* Installeer [Node.js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Create a storage account

Een opslagaccount biedt een unieke naamruimte voor het opslaan en openen van uw Azure Storage-gegevensobjecten.

Volg deze stappen om een account voor geografisch redundante opslag met leestoegang te maken:

1. Selecteer de knop **Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Opslag** op de pagina **Nieuw**.
3. Selecteer **Storage-account: Blob, File, Table, Queue** onder **Aanbevolen**.
4. Vul het formulier voor het opslagaccount in met de informatie uit de volgende afbeelding en selecteer **Maken**:

   | Instelling       | Voorgestelde waarde | Beschrijving |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Naam** | mystorageaccount | Een unieke naam voor uw opslagaccount |
   | **Implementatiemodel** | Resource Manager  | Resource Manager bevat de nieuwste functies.|
   | **Accountsoort** | StorageV2 | Zie [Typen opslagaccounts](../common/storage-introduction.md#types-of-storage-accounts) voor meer informatie over de verschillende typen accounts |
   | **Prestaties** | Standard | Standard is voldoende voor het voorbeeldscenario. |
   | **Replicatie**| Geografisch redundante opslag met leestoegang (RA-GRS) | Dit is nodig om het voorbeeld te laten werken. |
   |**Abonnement** | uw abonnement |Zie [Abonnementen](https://account.azure.com/Subscriptions) voor meer informatie over uw abonnementen. |
   |**ResourceGroup** | myResourceGroup |Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige namen van resourcegroepen. |
   |**Locatie** | VS - oost | Kies een locatie. |

![opslagaccount maken](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Het voorbeeld downloaden

# <a name="net"></a>[.NET](#tab/dotnet)

[Download het voorbeeldproject](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) en pak (unzip) het bestand storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip uit. U kunt ook [git](https://git-scm.com/) gebruiken om een kopie van de toepassing te downloaden naar uw ontwikkelomgeving. Het voorbeeldproject bevat een consoletoepassing.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="python"></a>[Python](#tab/python)

[Download het voorbeeldproject](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) en pak (unzip) het bestand storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip uit. U kunt ook [git](https://git-scm.com/) gebruiken om een kopie van de toepassing te downloaden naar uw ontwikkelomgeving. Het voorbeeldproject bevat een basis Python-toepassing.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[Download het voorbeeldproject](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) en rits het bestand uit. U kunt ook [git](https://git-scm.com/) gebruiken om een kopie van de toepassing te downloaden naar uw ontwikkelomgeving. Het voorbeeldproject bevat een basistoepassing Node.js.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>Configureren van het voorbeeld

# <a name="net"></a>[.NET](#tab/dotnet)

In de toepassing moet u de verbindingsreeks voor uw opslagaccount opgeven. U kunt deze verbindingsreeks opslaan in een omgevingsvariabele op de lokale computer waarop de toepassing wordt uitgevoerd. Volg een van de onderstaande voorbeelden afhankelijk van uw besturingssysteem voor het maken van de omgevingsvariabele.

Ga in Azure Portal naar het nieuwe opslagaccount. Selecteer bij **Instellingen** in uw opslagaccount de optie **Toegangssleutels**. Kopieer de **verbindingsreeks** uit de primaire of secundaire sleutel. Voer een van de volgende opdrachten uit op \<basis\> van uw besturingssysteem en vervang uw verbindingstekenreeks door uw werkelijke verbindingstekenreeks. Met deze opdracht slaat u een omgevingsvariabele naar de lokale machine op. In Windows is de omgevingsvariabele pas beschikbaar wanneer u de **opdrachtprompt** of shell die u gebruikt opnieuw laadt.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python"></a>[Python](#tab/python)

In de toepassing moet u uw opslagaccountgegevens opgeven. U deze informatie opslaan in omgevingsvariabelen op de lokale machine waarop de toepassing wordt uitgevoerd. Volg een van de onderstaande voorbeelden, afhankelijk van uw besturingssysteem om de omgevingsvariabelen te maken.

Ga in Azure Portal naar het nieuwe opslagaccount. Selecteer bij **Instellingen** in uw opslagaccount de optie **Toegangssleutels**. Plak de **naam van het opslagaccount** en de \<waarden Van\> \< **de Sleutel** in de volgende opdrachten en vervang de tijdelijke aanduidingen van uw accountnaam en uw accountsleutel.\> Met deze opdracht worden de omgevingsvariabelen opgeslagen in de lokale machine. In Windows is de omgevingsvariabele pas beschikbaar wanneer u de **opdrachtprompt** of shell die u gebruikt opnieuw laadt.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Als u dit voorbeeld wilt uitvoeren, moet `.env.example` u uw opslagaccountreferenties `.env`aan het bestand toevoegen en de naam wijzigen in .

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

U deze informatie vinden in de Azure-portal door naar uw opslagaccount te navigeren en **Toegangssleutels** te selecteren in de sectie **Instellingen.**

Installeer de vereiste afhankelijkheden. Als u dit wilt doen, opent u een `npm install`opdrachtprompt, navigeert u naar de voorbeeldmap en voert u .

---

## <a name="run-the-console-application"></a>De consoletoepassing uitvoeren

# <a name="net"></a>[.NET](#tab/dotnet)

Druk in Visual Studio op **F5** of selecteer **Start** om foutopsporing van de toepassing te starten. Visual Studio herstelt automatisch ontbrekende NuGet-pakketten als dit zo is geconfigureerd. Ga naar [Installing and reinstalling packages with package restore](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) (Pakketten installeren en opnieuw installeren met pakketherstel) voor meer informatie.

Er wordt een consolevenster geopend en de toepassing wordt gestart. De toepassing uploadt de afbeelding **HelloWorld.png** vanuit de oplossing naar het opslagaccount. De toepassing controleert of de afbeelding is gerepliceerd naar het secundaire RA-GRS-eindpunt. Daarna wordt de afbeelding maximaal 999 keer gedownload. Elke lezing wordt vertegenwoordigd door een **P** of een **S**. Waar **P** het primaire eindpunt vertegenwoordigt en **S** het secundaire eindpunt vertegenwoordigt.

![Console-app die wordt uitgevoerd](media/storage-create-geo-redundant-storage/figure3.png)

In de voorbeeldcode wordt de taak `RunCircuitBreakerAsync` in het bestand `Program.cs` gebruikt om een afbeelding met behulp van de [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync)-methode te downloaden vanuit het opslagaccount. Voorafgaand aan de download wordt een [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext) gedefinieerd. De bewerkingscontext definieert de gebeurtenis-handlers. Deze worden geactiveerd wanneer een download is voltooid of wanneer een download is mislukt en opnieuw wordt uitgevoerd.

# <a name="python"></a>[Python](#tab/python)

Als u de toepassing wilt uitvoeren op een terminal of opdrachtprompt, gaat u naar de map **circuitbreaker.py** en voert u `python circuitbreaker.py` in. De toepassing uploadt de afbeelding **HelloWorld.png** vanuit de oplossing naar het opslagaccount. De toepassing controleert of de afbeelding is gerepliceerd naar het secundaire RA-GRS-eindpunt. Daarna wordt de afbeelding maximaal 999 keer gedownload. Elke lezing wordt vertegenwoordigd door een **P** of een **S**. Waar **P** het primaire eindpunt vertegenwoordigt en **S** het secundaire eindpunt vertegenwoordigt.

![Console-app die wordt uitgevoerd](media/storage-create-geo-redundant-storage/figure3.png)

In de voorbeeldcode wordt de methode `run_circuit_breaker` in het bestand `circuitbreaker.py` gebruikt om een afbeelding met behulp van de [get_blob_to_path](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.baseblobservice.baseblobservice?view=azure-python-previous#get-blob-to-path-container-name--blob-name--file-path--open-mode--wb---snapshot-none--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--lease-id-none--if-modified-since-none--if-unmodified-since-none--if-match-none--if-none-match-none--timeout-none-)-methode te downloaden vanuit het opslagaccount.

De functie Opnieuw van het opslagobject is ingesteld op een lineair beleid voor nieuwe pogingen. De functie Opnieuw bepaalt of een aanvraag opnieuw moet worden geprobeerd en geeft het aantal seconden op dat moet worden gewacht voordat opnieuw wordt geprobeerd de aanvraag uit te voeren. Stel de waarde van **retry\_to\_secondary** in op True als de aanvraag bij een volgende poging worden uitgevoerd naar het secundaire eindpunt als de eerste aanvraag naar het primaire eindpunt is mislukt. In de voorbeeldtoepassing is een aangepast beleid voor nieuwe pogingen gedefinieerd in de functie `retry_callback` van het opslagobject.

Vóór de download wordt het [retry_callback-](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient?view=azure-python) en [response_callback](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient?view=azure-python) functie van de service functie gedefinieerd. Deze functies definiëren gebeurtenis-handlers die worden geactiveerd wanneer een download is voltooid of wanneer een download is mislukt en opnieuw wordt uitgevoerd.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Als u het voorbeeld wilt uitvoeren, opent u een `node index.js`opdrachtprompt, navigeert u naar de voorbeeldmap en voert u .

Het voorbeeld maakt een container in uw Blob-opslagaccount, uploadt **HelloWorld.png** in de container en controleert vervolgens herhaaldelijk of de container en afbeelding zijn gerepliceerd naar het secundaire gebied. Na replicatie wordt u gevraagd **D** of **Q** (gevolgd door ENTER) in te voeren om te downloaden of te stoppen. Uw uitvoer moet op het volgende voorbeeld lijken:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>De voorbeeldcode begrijpen

### <a name="net"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Gebeurtenis-handler opnieuw proberen

De gebeurtenis-handler `OperationContextRetrying` wordt aangeroepen wanneer het downloaden van de afbeelding is mislukt en is ingesteld op Opnieuw proberen. Als het maximale aantal nieuwe pogingen dat in de toepassing is gedefinieerd, is bereikt, verandert de [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) van de aanvraag in `SecondaryOnly`. Deze instelling zorgt ervoor dat de toepassing de afbeelding probeert te downloaden van het secundaire eindpunt. Deze configuratie vermindert de tijd die nodig is om de afbeelding op te vragen omdat het primaire eindpunt niet oneindig opnieuw wordt geprobeerd.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Gebeurtenis-handler Aanvraag voltooid

De gebeurtenis-handler `OperationContextRequestCompleted` wordt aangeroepen wanneer het downloaden van de afbeelding is geslaagd. Als de toepassing het secundaire eindpunt gebruikt, blijft de toepassing dit eindpunt maximaal 20 keer gebruiken. Na 20 keer stelt de toepassing de [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) weer in op `PrimaryThenSecondary` en wordt het primaire eindpunt weer geprobeerd. Als een aanvraag is geslaagd, blijft de toepassing van het primaire eindpunt lezen.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

### <a name="python"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>Gebeurtenis-handler opnieuw proberen

De gebeurtenis-handler `retry_callback` wordt aangeroepen wanneer het downloaden van de afbeelding is mislukt en is ingesteld op Opnieuw proberen. Als het maximale aantal nieuwe pogingen dat in de toepassing is gedefinieerd, is bereikt, verandert de [LocationMode](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.models.locationmode?view=azure-python) van de aanvraag in `SECONDARY`. Deze instelling zorgt ervoor dat de toepassing de afbeelding probeert te downloaden van het secundaire eindpunt. Deze configuratie vermindert de tijd die nodig is om de afbeelding op te vragen omdat het primaire eindpunt niet oneindig opnieuw wordt geprobeerd.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Gebeurtenis-handler Aanvraag voltooid

De gebeurtenis-handler `response_callback` wordt aangeroepen wanneer het downloaden van de afbeelding is geslaagd. Als de toepassing het secundaire eindpunt gebruikt, blijft de toepassing dit eindpunt maximaal 20 keer gebruiken. Na 20 keer stelt de toepassing de [LocationMode](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.models.locationmode?view=azure-python) weer in op `PRIMARY` en wordt het primaire eindpunt weer geprobeerd. Als een aanvraag is geslaagd, blijft de toepassing van het primaire eindpunt lezen.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Met de Node.js V10 SDK zijn callback-handlers overbodig. In plaats daarvan maakt het voorbeeld een pijplijn die is geconfigureerd met opties voor opnieuw proberen en een secundair eindpunt. Hierdoor kan de toepassing automatisch overschakelen naar de secundaire pijplijn als deze uw gegevens niet via de primaire pijplijn bereikt.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Volgende stappen

In deel één van de serie hebt u geleerd hoe u een toepassing maximaal beschikbaar maakt met RA-GRS-opslagaccounts.

Ga naar deel twee van de serie voor informatie over hoe u een fout simuleert en uw toepassing dwingt om het secundaire RA-GRS-eindpunt te gebruiken.

> [!div class="nextstepaction"]
> [Een fout in het lezen vanuit het primaire gebied simuleren](storage-simulate-failure-ragrs-account-app.md)
