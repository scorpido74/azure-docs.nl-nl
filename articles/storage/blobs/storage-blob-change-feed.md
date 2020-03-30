---
title: Feed wijzigen in Azure Blob Storage (Preview) | Microsoft Documenten
description: Meer informatie over feedlogboeken wijzigen in Azure Blob Storage en hoe u deze gebruiken.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: ac111b06d578a0e9af8581ef2e8caeccfc4a291e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536884"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Feedondersteuning wijzigen in Azure Blob Storage (Voorbeeld)

Het doel van de wijzigingsfeed is om transactielogboeken te verstrekken van alle wijzigingen die zich voordoen in de blobs en de blobmetagegevens in uw opslagaccount. De wijziging feed biedt **geordende,** **gegarandeerd,** **duurzaam,** **onveranderlijke**, **read-only** log van deze wijzigingen. Clienttoepassingen kunnen deze logboeken op elk gewenst moment lezen, in streaming of in batchmodus. Met de wijzigingsfeed u efficiënte en schaalbare oplossingen bouwen die gebeurtenissen die zich voordoen in uw Blob Storage-account verwerken tegen lage kosten.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

De [wijzigingsfeed](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) wordt opgeslagen als blobs in een speciale container in uw opslagaccount tegen standaard [blob-prijskosten.](https://azure.microsoft.com/pricing/details/storage/blobs/) U de bewaarperiode van deze bestanden beheren op basis van uw vereisten (Zie de [voorwaarden](#conditions) van de huidige release). Wijzigingsgebeurtenissen worden toegevoegd aan de wijzigingsfeed als records in de [Specificatie apache Avro-indeling:](https://avro.apache.org/docs/1.8.2/spec.html) een compact, snel, binair formaat dat rijke gegevensstructuren met inline-schema biedt. Deze indeling wordt veel gebruikt in het Hadoop-ecosysteem, Stream Analytics en Azure Data Factory.

U deze logboeken asynchroon, stapsgewijs of volledig verwerken. Elk aantal clienttoepassingen kan de wijzigingsfeed onafhankelijk lezen, parallel en in hun eigen tempo. Analytics-toepassingen zoals [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) of [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) kunnen logboeken rechtstreeks gebruiken als Avro-bestanden, waarmee u ze verwerken tegen lage kosten, met een hoge bandbreedte en zonder dat u een aangepaste toepassing hoeft te schrijven.

Feedondersteuning wijzigen is zeer geschikt voor scenario's die gegevens verwerken op basis van objecten die zijn gewijzigd. Toepassingen kunnen bijvoorbeeld:

  - Werk een secundaire index bij, synchroniseer met een cache, zoekmachine of andere scenario's voor inhoudsbeheer.
  
  - Haal inzichten en statistieken voor bedrijfsanalyses uit, op basis van wijzigingen die in uw objecten optreden, op een streamingmanier of in batchmodus.
  
  - Sla wijzigingen in uw objecten op, controleer en analyseer deze gedurende elke periode voor beveiliging, naleving of intelligentie voor bedrijfsgegevensbeheer.

  - Bouw oplossingen voor het back-ups maken, spiegelen of repliceren van objectstatus in uw account voor disaster management of compliance.

  - Maak verbonden toepassingspijplijnen die reageren op het wijzigen van gebeurtenissen of het plannen van uitvoeringen op basis van een gemaakt of gewijzigd object.

> [!NOTE]
> Change feed biedt een duurzaam, geordend logboekmodel van de wijzigingen die zich voordoen in een blob. Wijzigingen worden geschreven en beschikbaar gesteld in uw wijzigingsfeedlogboek binnen een paar minuten na de wijziging. Als uw toepassing veel sneller op gebeurtenissen moet reageren, u in plaats daarvan [blobopslaggebeurtenissen](storage-blob-event-overview.md) gebruiken. [Blob Storage Events](storage-blob-event-overview.md) biedt realtime eenmalige gebeurtenissen waarmee uw Azure-functies of -toepassingen snel kunnen reageren op wijzigingen die zich voordoen in een blob. 

## <a name="enable-and-disable-the-change-feed"></a>De wijzigingsfeed in- en uitschakelen

U moet de wijzigingsfeed van uw opslagaccount inschakelen om wijzigingen vast te leggen en vast te leggen. Schakel de wijzigingsfeed uit om het vastleggen van wijzigingen te stoppen. U wijzigingen in- en uitschakelen met Azure Resource Manager-sjablonen in Portal of Powershell.

Hier zijn een paar dingen om in gedachten te houden wanneer u de change feed inschakelt.

- Er is slechts één wijzigingsfeed voor de blobservice in elk opslagaccount en wordt opgeslagen in de **$blobchangefeed** container.

- Wijzigingen maken, bijwerken en verwijderen worden alleen vastgelegd op blobserviceniveau.

- De wijzigingsfeed legt *alle* wijzigingen vast voor alle beschikbare gebeurtenissen die op het account plaatsvinden. Clienttoepassingen kunnen gebeurtenistypen naar behoefte filteren. (Zie de [voorwaarden](#conditions) van de huidige release).

- Alleen GPv2- en Blob-opslagaccounts kunnen de feed Wijzigen inschakelen. Premium BlockBlobStorage-accounts en hiërarchische naamruimte-accounts worden momenteel niet ondersteund. GPv1-opslagaccounts worden niet ondersteund, maar kunnen zonder downtime worden geüpgraded naar GPv2, zie [Upgrade naar een GPv2-opslagaccount](../common/storage-account-upgrade.md) voor meer informatie.

> [!IMPORTANT]
> De wijzigingsfeed is openbaar en is beschikbaar in de regio's **Westcentralus** en **Westus2.** Zie de [voorwaarden](#conditions) sectie van dit artikel. Zie het gedeelte [Uw abonnement registreren](#register) van dit artikel om u in te schrijven voor het voorbeeld. U moet uw abonnement registreren voordat u de wijzigingsfeed op uw opslagaccounts inschakelen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Wijzigfeed inschakelen voor uw opslagaccount met azure portal:

1. Selecteer uw opslagaccount in de [Azure-portal.](https://portal.azure.com/) 

2. Navigeer naar de optie **Gegevensbescherming** onder **Blob-service**.

3. Klik **op Ingeschakeld** onder **Blob-feed wijzigen**

4. Kies de knop **Opslaan** om de instellingen voor gegevensbescherming te bevestigen

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Change feed inschakelen met PowerShell:

1. Installeer de nieuwste PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Sluit en open de Powershell-console opnieuw.

3. Installeer de previewmodule **az.storage.**

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Meld u met de `Connect-AzAccount` opdracht aan bij uw Azure-abonnement en volg de aanwijzingen op het scherm om te verifiëren.

   ```powershell
   Connect-AzAccount
   ```

5. Schakel wijzigingsfeed in voor uw opslagaccount.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Sjabloon](#tab/template)
Gebruik een Azure Resource Manager-sjabloon om de feed wijzigen op uw bestaande opslagaccount in te schakelen via azure-portal:

1. Kies in de Azure-portal de optie **Een resource maken**.

2. In **Marketplace doorzoeken** typt u **sjabloonimplementatie**. Druk vervolgens op **ENTER**.

3. Kies **[Een aangepaste sjabloon implementeren](https://portal.azure.com/#create/Microsoft.Template)** en kies Vervolgens Uw eigen sjabloon maken in de **editor**.

4. Plak in de sjablooneditor de volgende json. Vervang de tijdelijke plaatsaanduiding `<accountName>` door de naam van uw opslagaccount.

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
    
5. Kies de knop **Opslaan,** geef de brongroep van het account op en kies de knop **Aankoop** om de sjabloon te implementeren en de wijzigingsfeed in te schakelen.

---

## <a name="consume-the-change-feed"></a>De wijzigingsfeed consumeren

De wijzigingsfeed produceert verschillende metagegevens en logboekbestanden. Deze bestanden bevinden zich in de **$blobchangefeed** container van het opslagaccount. 

> [!NOTE]
> In de huidige versie is de **$blobchangefeed** container niet zichtbaar in Azure Storage Explorer of de Azure-portal. U momenteel de $blobchangefeed container niet zien wanneer u ListContainers API aanroept, maar u de ListBlobs API rechtstreeks op de container aanroepen om de blobs te zien.

Uw clienttoepassingen kunnen de wijzigingsfeed gebruiken met behulp van de blob-feedprocessorbibliotheek die is geleverd met de SDK van de feedprocessor wijzigen. 

Zie [Feedlogboeken voor proceswijzigingen in Azure Blob Storage](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Inzicht in wijzigingsfeedorganisatie

<a id="segment-index"></a>

### <a name="segments"></a>Segmenten

De wijzigingsfeed is een logboek van wijzigingen die zijn georganiseerd in *segmenten* **per uur,** maar die om de paar minuten worden toegevoegd en bijgewerkt. Deze segmenten worden alleen gemaakt wanneer er blobwijzigingsgebeurtenissen zijn die in dat uur plaatsvinden. Hierdoor kan uw clienttoepassing wijzigingen gebruiken die binnen specifieke tijdsbereiken plaatsvinden zonder dat u door het hele logboek hoeft te zoeken. Zie de specificaties [voor](#specifications)meer informatie.

Een beschikbaar uursegment van de wijzigingsfeed wordt beschreven in een manifestbestand dat de paden naar de feedbestanden voor dat segment aangeeft. De lijst `$blobchangefeed/idx/segments/` van de virtuele directory toont deze segmenten besteld door de tijd. Het pad van het segment beschrijft het begin van het uurtijdbereik dat het segment vertegenwoordigt. U die lijst gebruiken om de segmenten van logboeken te filteren die voor u interessant zijn.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> Deze `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` wordt automatisch gemaakt wanneer u de wijzigingsfeed inschakelt. U dit bestand veilig negeren. Het is een altijd leeg initialisatiebestand. 

Het segmentmanifestbestand`meta.json`( ) toont het pad van de `chunkFilePaths` feedbestanden voor wijzigingen voor dat segment in de eigenschap. Hier is een voorbeeld van een segment manifest bestand.

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
> De `$blobchangefeed` container wordt pas weergegeven nadat u de functie Feed wijzigen in uw account hebt ingeschakeld. U moet een paar minuten wachten nadat u de wijzigingsfeed hebt ingeschakeld voordat u de blobs in de container weergeven. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Gebeurtenisrecords wijzigen

De feedbestanden wijzigen bevatten een reeks gebeurtenisrecords voor wijzigingen. Elke gebeurtenisrecord komt overeen met één wijziging in een afzonderlijke blob. De records worden geserialiseerd en naar het bestand geschreven met behulp van de Apache Avro-formaatspecificatie. [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) De records kunnen worden gelezen met behulp van de Avro file format specificatie. Er zijn verschillende bibliotheken beschikbaar om bestanden in die indeling te verwerken.

Feedbestanden wijzigen worden `$blobchangefeed/log/` in de virtuele map opgeslagen als [toevoegende blobs.](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) Het eerste feedbestand wijzigen onder `00000` elk pad heeft `00000.avro`in de bestandsnaam (bijvoorbeeld ). De naam van elk volgend logboekbestand dat aan dat `00001.avro`pad is toegevoegd, wordt met 1 verhoogd (Bijvoorbeeld: ).

Hier is een voorbeeld van wijzigingsgebeurtenisrecord van het feedbestand wijzigen dat is geconverteerd naar Json.

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

Zie [Azure Event Grid-gebeurtenisschema voor Blob Storage voor](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)een beschrijving van elke eigenschap.

> [!NOTE]
> De feedbestanden voor wijzigingen voor een segment worden niet onmiddellijk weergegeven nadat een segment is gemaakt. De duur van de vertraging is binnen het normale interval van het publiceren van latentie van de wijzigingsfeed die zich binnen een paar minuten van de wijziging bevindt.

<a id="specifications"></a>

## <a name="specifications"></a>Specificaties

- Gebeurtenissen records wijzigen worden alleen toegevoegd aan de wijzigingsfeed. Zodra deze records zijn toegevoegd, ze zijn onveranderlijk en record-positie is stabiel. Clienttoepassingen kunnen hun eigen controlepunt behouden op de leespositie van de wijzigingsfeed.

- Gebeurtenisrecords wijzigen worden toegevoegd binnen een paar minuten na de wijziging. Clienttoepassingen kunnen ervoor kiezen om records te gebruiken omdat ze worden toegevoegd voor streamingtoegang of in bulk op een ander moment.

- Gebeurtenisrecords wijzigen worden geordend op wijzigingsvolgorde **per blob**. Volgorde van wijzigingen in blobs is niet gedefinieerd in Azure Blob Storage. Alle wijzigingen in een voorafgaand segment zijn vóór wijzigingen in volgende segmenten.

- Gebeurtenisrecords wijzigen worden geserialiseerd in het logboekbestand met behulp van de specificatie van het [Apache Avro 1.8.2-formaat.](https://avro.apache.org/docs/1.8.2/spec.html)

- Gebeurtenisrecords wijzigen `eventType` waarvan de `Control` waarde interne systeemrecords heeft en geen wijziging in objecten in uw account weergeven. Je die gegevens veilig negeren.

- Waarden in `storageDiagnonstics` de property bag zijn alleen voor intern gebruik en niet ontworpen voor gebruik door uw toepassing. Uw toepassingen mogen geen contractuele afhankelijkheid van die gegevens hebben. U deze eigenschappen veilig negeren.

- De tijd die door het segment wordt vertegenwoordigd is **benaderend** met grenzen van 15 minuten. Dus om het verbruik van alle records binnen een bepaalde tijd te garanderen, verbruikt u het opeenvolgende vorige en volgende uursegment.

- Elk segment kan een `chunkFilePaths` ander aantal hebben als gevolg van interne partitionering van de logboekstroom om publicatiedoorvoer te beheren. De logbestanden `chunkFilePath` in elk zijn gegarandeerd te bevatten wederzijds exclusieve blobs, en kan worden verbruikt en verwerkt in parallel zonder schending van het bestellen van wijzigingen per blob tijdens de iteratie.

- De segmenten beginnen `Publishing` in status. Zodra de toepassing van de records aan het `Finalized`segment is voltooid, zal het . Logbestanden in elk segment dat is gedateerd `LastConsumable` na `$blobchangefeed/meta/Segments.json` de datum van de eigenschap in het bestand, mogen niet worden verbruikt door uw toepassing. Hier is een voorbeeld `LastConsumable`van `$blobchangefeed/meta/Segments.json` de eigenschap in een bestand:

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

## <a name="register-your-subscription-preview"></a>Uw abonnement registreren (Voorbeeld)

Omdat de wijzigingsfeed alleen in een openbare preview is, moet u uw abonnement registreren om de functie te gebruiken.

### <a name="register-by-using-powershell"></a>Registreren met PowerShell

Voer de volgende opdrachten uit in een PowerShell-console:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Registreren met Azure CLI

Voer in Azure Cloud Shell de volgende opdrachten uit:

```azurecli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Voorwaarden en bekende problemen (Preview)

In deze sectie worden bekende problemen en voorwaarden beschreven in de huidige openbare preview van de wijzigingsfeed. 
- Voor preview moet u [eerst uw abonnement registreren](#register) voordat u de wijzigingsfeed voor uw opslagaccount in de regio's Westcentralus of Westus2 inschakelen. 
- De wijzigingsfeed legt alleen bewerkingen voor het maken, bijwerken, verwijderen en kopiëren vast. Metagegevensupdates worden momenteel niet vastgelegd in preview.
- Gebeurtenisrecords wijzigen voor een enkele wijziging kan meerdere keer worden weergegeven in uw wijzigingsfeed.
- U de levensduur van feedlogbestanden voor wijzigingen nog niet beheren door er een op tijd gebaseerd bewaarbeleid op in te stellen en u de blobs niet verwijderen 
- De `url` eigenschap van het logboekbestand is momenteel altijd leeg.
- De `LastConsumable` eigenschap van het bestand segments.json vermeldt niet het allereerste segment dat de wijzigingsfeed afrondt. Dit probleem treedt pas op nadat het eerste segment is afgerond. Alle volgende segmenten na het eerste uur `LastConsumable` worden nauwkeurig vastgelegd in de eigenschap.
- U momenteel de **$blobchangefeed** container niet zien wanneer u ListContainers API aanroept en de container niet wordt weergegeven op Azure portal of Storage Explorer
- Opslagaccounts die eerder een [failoveraccount](../common/storage-disaster-recovery-guidance.md) hebben gestart, kunnen problemen hebben met het logboekbestand dat niet wordt weergegeven. Eventuele toekomstige failovers van een account kunnen ook van invloed zijn op het logboekbestand tijdens de preview.

## <a name="faq"></a>Veelgestelde vragen

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Wat is het verschil tussen Feed wijzigen en logboekregistratie voor Storage Analytics?
Analytics-logboeken hebben records van alle bewerkingen die worden gelezen, geschreven, opgenomen en verwijderd met geslaagde en mislukte aanvragen voor alle bewerkingen. Analytics logs zijn best-effort en geen bestelling is gegarandeerd.

Change feed is een oplossing die transactionele logboek van succesvolle mutaties of wijzigingen in uw account, zoals blob creatie, wijziging, en verwijderingen biedt. Change feed garandeert dat alle gebeurtenissen worden geregistreerd en weergegeven in de volgorde van succesvolle wijzigingen per blob, zodat u geen ruis hoeft uit een enorm volume leesbewerkingen of mislukte aanvragen te filteren. Change feed is fundamenteel ontworpen en geoptimaliseerd voor applicatieontwikkeling die bepaalde garanties vereist.

### <a name="should-i-use-change-feed-or-storage-events"></a>Moet ik feed- of opslaggebeurtenissen wijzigen gebruiken?
U gebruikmaken van beide functies zoals Feed wijzigen en [Blob-opslaggebeurtenissen](storage-blob-event-overview.md) bieden dezelfde informatie met dezelfde betrouwbaarheidsgarantie voor de levering, met als belangrijkste verschil de latentie, het bestellen en opslaan van gebeurtenisrecords. Change feed publiceert records in het logboek binnen enkele minuten na de wijziging en garandeert ook de volgorde van de wijzigingsbewerkingen per blob. Opslaggebeurtenissen worden in realtime gepusht en worden mogelijk niet besteld. Feedgebeurtenissen wijzigen worden blijvend opgeslagen in uw opslagaccount als alleen-lezen stabiele logboeken met uw eigen gedefinieerde bewaring, terwijl opslaggebeurtenissen van voorbijgaande aard zijn om door de gebeurtenishandler te worden verbruikt, tenzij u ze expliciet opslaat. Met change feed kunnen een willekeurig aantal van uw toepassingen de logboeken op hun eigen gemak gebruiken met blob-API's of SDK's. 

## <a name="next-steps"></a>Volgende stappen

- Zie een voorbeeld van hoe u de wijzigingsfeed lezen met behulp van een .NET-clienttoepassing. Zie [Feedlogboeken voor proceswijzigingen in Azure Blob Storage](storage-blob-change-feed-how-to.md).
- Meer informatie over hoe je in realtime op gebeurtenissen reageren. Zie [Reageren op blobopslaggebeurtenissen](storage-blob-event-overview.md)
- Meer informatie over gedetailleerde logboekregistratiegegevens voor zowel geslaagde als mislukte bewerkingen voor alle aanvragen. Zie [Logboekregistratie voor Azure Storage-analyses](../common/storage-analytics-logging.md)
