---
title: Feed wijzigen in Azure Blob Storage (preview) | Microsoft Docs
description: Meer informatie over wijzigingen in feed-Logboeken in Azure Blob Storage en hoe u deze kunt gebruiken.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 07123fd5701e9041ff377ea5309cf1291e737ca6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693616"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Ondersteuning voor feed wijzigen in Azure Blob Storage (preview-versie)

Het doel van de wijzigings feed is het bieden van transactie logboeken van alle wijzigingen die plaatsvinden in de blobs en de BLOB-meta gegevens in uw opslag account. De wijzigings feed biedt **besteld**, **gegarandeerd**, **duurzaam**, **onveranderbaar**, **alleen-lezen** logboek van deze wijzigingen. Client toepassingen kunnen deze logboeken op elk gewenst moment in streaming of in de batch modus lezen. Met de wijzigings feed kunt u efficiënte en schaal bare oplossingen bouwen die wijzigings gebeurtenissen verwerken die in uw Blob Storage-account tegen lage kosten optreden.

> [!NOTE]
> De wijzigings feed bevindt zich in de open bare preview en is beschikbaar in de regio's **westcentralus** en **westus2** . Zie de sectie [voor waarden](#conditions) in dit artikel. Zie de sectie [uw abonnement registreren](#register) in dit artikel voor meer informatie over het inschrijven van de preview-versie.

De wijzigings feed wordt opgeslagen als [blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) in een speciale container in uw opslag account tegen standaard [prijs voor blobs](https://azure.microsoft.com/pricing/details/storage/blobs/) . U kunt de Bewaar periode van deze bestanden beheren op basis van uw vereisten (Zie de [voor waarden](#conditions) van de huidige versie). Wijzigings gebeurtenissen worden toegevoegd aan de wijzigings feed als records in de [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) Format-specificatie: een compacte, snelle en binaire indeling die voorziet in uitgebreide gegevens structuren met inline-schema's. Deze indeling wordt veel gebruikt in het Hadoop-ecosysteem, het Stream Analytics en het Azure Data Factory.

U kunt deze logboeken asynchroon, incrementeel of volledig verwerken. Een wille keurig aantal client toepassingen kan onafhankelijk van de wijzigings feed, parallel en in hun eigen tempo lezen. Analyse toepassingen zoals [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) of [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) kunnen Logboeken rechtstreeks gebruiken als Avro-bestanden, waarmee u ze kunt verwerken tegen lage kosten, met een hoge band breedte en zonder dat u een aangepaste toepassing hoeft te schrijven.

De ondersteuning voor het wijzigen van feeds is heel geschikt voor scenario's waarin gegevens worden verwerkt op basis van objecten die zijn gewijzigd. Toepassingen kunnen bijvoorbeeld:

  - Een secundaire index bijwerken, synchroniseren met een cache, zoek machine of andere scenario's voor inhouds beheer.
  
  - De inzichten en metrische gegevens van Business Analytics extra heren, op basis van wijzigingen die plaatsvinden in uw objecten, hetzij in een streaming-of in batch modus.
  
  - Bewaar, Controleer en analyseer wijzigingen in uw objecten, gedurende een bepaalde periode, voor beveiliging, naleving of intelligentie voor ondernemings gegevens beheer.

  - Ontwikkel oplossingen voor het maken van een back-up, het spie gelen of repliceren van object status in uw account voor nood beheer of naleving.

  - Bouw verbonden toepassings pijplijnen die reageren op wijzigings gebeurtenissen of het plannen van uitvoeringen op basis van een gemaakt of gewijzigd object.

> [!NOTE]
> [Blob Storage gebeurtenissen](storage-blob-event-overview.md) biedt realtime eenmalige gebeurtenissen waarmee uw Azure functions of toepassingen kunnen reageren op wijzigingen die zich voordoen aan een blob. De wijzigings feed biedt een duurzaam, geordend logboek model van de wijzigingen. Wijzigingen in uw wijzigings feed worden in een bestelling van een paar minuten van de wijziging beschikbaar gesteld in uw wijzigings feed. Als uw toepassing sneller moet reageren op gebeurtenissen, kunt u in plaats daarvan [Blob Storage gebeurtenissen](storage-blob-event-overview.md) gebruiken. Met Blob Storage gebeurtenissen kunnen uw Azure Functions of toepassingen afzonderlijke gebeurtenissen in realtime reageren.

## <a name="enabling-and-disabling-the-change-feed"></a>De wijzigings feed in-en uitschakelen

U moet de wijzigings feed inschakelen om te beginnen met het vastleggen van wijzigingen. Schakel de wijzigings feed uit om het vastleggen van wijzigingen te stoppen. U kunt wijzigingen in-en uitschakelen met behulp van Azure Resource Manager sjablonen in de portal of Power shell.

### <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

De sjabloon implementeren met behulp van Azure Portal:

1. Kies in het Azure Portal **een resource maken**.

2. In **Marketplace doorzoeken** typt u **sjabloonimplementatie**. Druk vervolgens op **ENTER**.

3. Kies **Sjabloonimlementatie**, kies **maken**en kies vervolgens **uw eigen sjabloon bouwen in de editor**.

5. Plak in de sjabloon editor de volgende JSON. Vervang de tijdelijke plaatsaanduiding `<accountName>` door de naam van uw opslagaccount.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [{
        "type": "Microsoft.Storage/storageAccounts/blobServices",
        "apiVersion": "2019-04-01",
        "name": "<accountName>/default",
        "properties": {
            "changeFeed": {
            "enabled": true
            }
        } 
     }]
}
```
4. Kies de knop **Opslaan** , geef de resource groep van het account op en kies vervolgens de knop **aanschaffen** om de wijzigings feed in te scha kelen.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

De sjabloon implementeren met behulp van Power shell:

1. Installeer de meest recente PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Sluit de Power shell-console en open deze opnieuw.

3. Installeer de module **AZ. Storage** preview.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Meld u aan bij uw Azure-abonnement met de `Connect-AzAccount`-opdracht en volg de instructies op het scherm om te verifiëren.

   ```powershell
   Connect-AzAccount
   ```

5. Schakel feed voor wijziging in voor uw opslag account.

   ```powershell
   Update-AzStorageBlobServiceProperty -ResourceGroupName -StorageAccountName -EnableChangeFeed $true
   ```

---

Hier volgen enkele dingen die u moet onthouden wanneer u de wijzigings feed inschakelt.

- Er is slechts één wijzigings feed voor de BLOB-service in elk opslag account. 

- Wijzigingen worden alleen vastgelegd op het niveau van de BLOB-service.

- De wijzigings feed legt *alle* wijzigingen vast voor alle beschik bare gebeurtenissen die op het account plaatsvinden. Client toepassingen kunnen gebeurtenis typen filteren zoals vereist. (Zie de [voor waarden](#conditions) van de huidige versie).

- Accounts met een hiërarchische naam ruimte worden niet ondersteund.

## <a name="consuming-the-change-feed"></a>De wijzigings feed gebruiken

De wijzigings feed produceert verschillende meta gegevens en logboek bestanden. Deze bestanden bevinden zich in de **$blobchangefeed** container van het opslag account. 

>[!NOTE]
> In de huidige release is de **$blobchangefeed** -container niet zichtbaar in Storage Explorer of de Azure Portal. 

Uw client toepassingen kunnen de wijzigings feed gebruiken met behulp van de processor bibliotheek voor het wijzigen van de blob die wordt meegeleverd met de SDK. 

Zie [Logboeken voor feed changes in Azure Blob Storage verwerken](storage-blob-change-feed-how-to.md).

## <a name="understanding-change-feed-organization"></a>Meer informatie over veranderingen in de feed

<a id="segment-index"></a>

### <a name="segments"></a>Segmenten

De wijzigings feed is een logboek met wijzigingen die zijn ingedeeld in **uren** *segmenten* (Zie [specificaties](#specifications)). Hierdoor kan uw client toepassing wijzigingen gebruiken die binnen bepaalde Peri Oden optreden zonder dat het hele logboek moet worden doorzocht.

Een beschikbaar segment van de wijzigings feed wordt beschreven in een manifest bestand dat de paden specificeert naar de wijzigings bestanden voor dat segment. In de lijst van de virtuele directory `$blobchangefeed/idx/segments/` worden deze segmenten weer gegeven op tijd. Het pad van het segment beschrijft het begin van het uur tijd bereik dat het segment vertegenwoordigt. (Zie de [specificaties](#specifications)). U kunt deze lijst gebruiken om de segmenten van de logboeken te filteren die voor u van belang zijn.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> De `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` wordt automatisch gemaakt wanneer u de wijzigings feed inschakelt. U kunt dit bestand negeren. Het is altijd leeg. 

In het manifest bestand van het segment (`meta.json`) wordt het pad weer gegeven van de wijzigings bestanden voor dat segment in de eigenschap `chunkFilePaths`. Hier volgt een voor beeld van een segment manifest bestand.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> Als u de containers in uw opslag account vermeldt, wordt de `$blobchangefeed`-container alleen weer gegeven nadat u de functie voor het wijzigen van de feed hebt ingeschakeld voor uw account. U moet een paar minuten wachten nadat u de feed voor wijzigingen hebt ingeschakeld voordat u de container kunt zien.

<a id="log-files"></a>

### <a name="change-event-records"></a>Gebeurtenis records wijzigen

De Change feed-bestanden bevatten een reeks wijzigings gebeurtenis records. Elke wijzigings gebeurtenis record komt overeen met één wijziging in een afzonderlijke blob. De records worden geserialiseerd en naar het bestand geschreven met behulp van de [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) Format-specificatie. De records kunnen worden gelezen met behulp van de Avro-bestands indeling. Er zijn verschillende bibliotheken beschikbaar voor het verwerken van bestanden in die indeling.

Wijzigingen in de feed worden opgeslagen in de virtuele `$blobchangefeed/log/`-map als [toevoeg-blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). Het eerste invoer bestand onder elk pad heeft `00000` in de bestands naam (bijvoorbeeld `00000.avro`). De naam van elk volgende logboek bestand dat aan het pad wordt toegevoegd, wordt verhoogd met 1 (bijvoorbeeld: `00001.avro`).

Hier volgt een voor beeld van het wijzigen van de gebeurtenis record van het wijzigingslog bestand dat is geconverteerd naar JSON.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}

```
Zie [Azure Event grid-gebeurtenis schema voor Blob Storage](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)voor een beschrijving van elke eigenschap.

> [!NOTE]
> De wijzigings bestanden voor een segment worden niet direct weer gegeven nadat een segment is gemaakt. De lengte van de vertraging ligt binnen het normale interval van de publicatie latentie van de wijzigings feed binnen een paar minuten van de wijziging.

<a id="specifications"></a>

## <a name="specifications"></a>Specificaties

- Records met wijzigings gebeurtenissen worden alleen toegevoegd aan de wijzigings feed. Zodra deze records zijn toegevoegd, zijn ze onveranderbaar en is de positie van de record stabiel. Client toepassingen kunnen hun eigen controle punt behouden op de Lees positie van de wijzigings feed.

- Wijzigings gebeurtenis records worden toegevoegd binnen een volg orde van enkele minuten van de wijziging. Client toepassingen kunnen ervoor kiezen om records te gebruiken wanneer ze worden toegevoegd voor het streamen van toegang of bulksgewijs op een wille keurig moment.

- Wijzigings gebeurtenis records worden geordend op basis van de wijzigings volgorde **per BLOB**. De volg orde van wijzigingen in blobs is niet gedefinieerd in Azure Blob Storage. Alle wijzigingen in een voor gaande segment zijn vóór eventuele wijzigingen in volgende segmenten.

- Wijzigings gebeurtenis records worden geserialiseerd in het logboek bestand met behulp van de [Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) -indeling.

- Gebeurtenis records wijzigen waarbij de `eventType` een waarde heeft van `Control` interne systeem records zijn en geen wijziging in de objecten in uw account weer spie gelen. U moet ze negeren.

- Waarden in de `storageDiagnonstics` eigenschappen verzameling zijn alleen voor intern gebruik en zijn niet bedoeld voor gebruik door uw toepassing. Uw toepassingen mogen geen contractuele afhankelijkheid op die gegevens hebben.

- De tijd die door het segment wordt weer gegeven, is bij **benadering** met een limiet van 15 minuten. Om ervoor te zorgen dat alle records binnen een opgegeven periode worden verbruikt, verbruikt u het opeenvolgende vorige en volgende uur segment.

- Elk segment kan een verschillend aantal `chunkFilePaths`hebben. Dit wordt veroorzaakt door het interne partitioneren van de logboek stroom voor het beheren van de door Voer van de publicatie. De logboek bestanden in elke `chunkFilePath` zijn gegarandeerd dat ze wederzijds exclusieve blobs bevatten en kunnen worden gebruikt en gelijktijdig worden verwerkt zonder dat de volg orde van wijzigingen per BLOB tijdens de herhaling wordt geschonden.

- De segmenten beginnen bij de `Publishing` status. Zodra het toevoegen van de records aan het segment is voltooid, wordt het `Finalized`. De logboek bestanden in een segment waarvan de datum na de date van de eigenschap `LastConsumable` in het `$blobchangefeed/meta/Segments.json` bestand valt, mogen niet worden gebruikt door uw toepassing. Hier volgt een voor beeld van de eigenschap `LastConsumable`in een `$blobchangefeed/meta/Segments.json`-bestand:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>Uw abonnement registreren (preview-versie)

Omdat de wijzigings feed alleen in de open bare preview is, moet u uw abonnement registreren om de functie te gebruiken.

### <a name="register-by-using-powershell"></a>Registreren met behulp van Power shell

Voer de volgende opdrachten uit in een Power shell-console:

   ```powershell
   Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
   ```
### <a name="register-by-using-azure-cli"></a>Registreren met behulp van Azure CLI

Voer de volgende opdrachten uit in Azure Cloud Shell:

```cli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Voor waarden en bekende problemen (preview-versie)

In deze sectie worden bekende problemen en voor waarden in de huidige open bare preview van de wijzigings feed beschreven.

- In de wijzigings feed worden alleen bewerkingen voor maken, bijwerken, verwijderen en kopiëren gemaakt.
- Het wijzigen van gebeurtenis records voor één wijziging kan meermaals voor komen in uw wijzigings feed.
- U kunt de levens duur van wijzigingslog bestand bestanden nog niet beheren door op tijd gebaseerd Bewaar beleid in te stellen.
- De eigenschap `url` van het logboek bestand is altijd leeg.
- De eigenschap `LastConsumable` van het bestand segmenten. json vermeldt niet het eerste segment dat de wijzigings feed is voltooid. Dit probleem treedt pas op nadat het eerste segment is voltooid. Alle volgende segmenten na het eerste uur worden nauw keurig vastgelegd in de eigenschap `LastConsumable`.

## <a name="next-steps"></a>Volgende stappen

- Bekijk een voor beeld van hoe u de wijzigings feed kunt lezen met behulp van een .NET-client toepassing. Zie [Logboeken voor feed changes in Azure Blob Storage verwerken](storage-blob-change-feed-how-to.md).
- Meer informatie over het reageren op gebeurtenissen in realtime. Zie [reageren op Blob Storage gebeurtenissen](storage-blob-event-overview.md)
