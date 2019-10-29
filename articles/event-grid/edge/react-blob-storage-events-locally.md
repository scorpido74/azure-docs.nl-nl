---
title: Reageren op gebeurtenissen van Blob Storage-module-Azure Event Grid IoT Edge | Microsoft Docs
description: Reageren op gebeurtenissen van Blob Storage-module
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 10/02/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d61dc9d49053cb8a125362ac492f354fb64b79a5
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992169"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Zelf studie: reageren op Blob Storage gebeurtenissen in IoT Edge (preview-versie)

In dit artikel wordt beschreven hoe u lokaal reageert op het maken van blobs en het verwijderen van blobs op IoT Edge met behulp van Event Grid.

Veelvoorkomende gebeurtenissen in Blob Storage zijn onder andere afbeeldings-of video verwerking, zoek indexering of een op bestanden gebaseerde werk stroom. Het asynchroon uploaden van bestanden is een uitstekend geschikt voor gebeurtenissen. Als de wijzigingen niet worden doorgevoerd, maar voor uw scenario een onmiddellijke reactie nodig is, kan de architectuur op basis van gebeurtenissen bijzonder efficiënt zijn.

Zie voor een overzicht van de Azure-Blob Storage op IoT Edge [azure Blob Storage op IOT Edge](../../iot-edge/how-to-store-data-blob.md) en de bijbehorende functies.

> [!WARNING]
> Azure Blob Storage op IoT Edge integratie met Event Grid is in Preview

Als u deze zelf studie wilt volt ooien, hebt u het volgende nodig:

* **Azure-abonnement** : Maak een [gratis account](https://azure.microsoft.com/free) als u er nog geen hebt. 
* **Azure IOT hub en IOT edge apparaat** : Volg de stappen in de Quick start voor [Linux](../../iot-edge/quickstart-linux.md) -of [Windows-apparaten](../../iot-edge/quickstart.md) als u er nog geen hebt.

## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge-module implementeren

Er zijn verschillende manieren om modules op een IoT Edge apparaat te implementeren en ze werken allemaal voor Azure Event Grid op IoT Edge. In dit artikel worden de stappen beschreven voor het implementeren van Event Grid op IoT Edge van de Azure Portal.

>[!NOTE]
> In deze zelf studie implementeert u de module Event Grid zonder persistentie. Dit betekent dat alle onderwerpen en abonnementen die u in deze zelf studie maakt, worden verwijderd wanneer u de module opnieuw implementeert. Raadpleeg de volgende artikelen voor meer informatie over het instellen van persistentie: [behoud de status in Linux](persist-state-linux.md) of [persistent in Windows](persist-state-windows.md). Voor werk belastingen wordt u aangeraden de Event Grid module met persistentie te installeren.


### <a name="select-your-iot-edge-device"></a>Uw IoT Edge-apparaat selecteren

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Navigeer naar uw IoT Hub.
1. Selecteer **IOT Edge** in het menu van het gedeelte **Automatic Device Management** . 
1. Klik op de ID van het doel apparaat in de lijst met apparaten
1. Selecteer **Modules instellen**. Laat de pagina geopend. U gaat verder met de stappen in de volgende sectie.

### <a name="configure-a-deployment-manifest"></a>Een implementatie manifest configureren

Een implementatie manifest is een JSON-document waarin wordt beschreven welke modules moeten worden geïmplementeerd, hoe gegevens stromen tussen de modules en gewenste eigenschappen van de module apparaatdubbels. De Azure Portal bevat een wizard die u helpt bij het maken van een implementatie manifest, in plaats van het JSON-document hand matig te bouwen.  Er zijn drie stappen: **modules toevoegen**, **routes opgeven**en de **implementatie controleren**.

### <a name="add-modules"></a>Modules toevoegen

1. Selecteer in de sectie **implementatie modules** de optie **toevoegen**
1. Selecteer **IOT Edge module** uit de typen modules in de vervolg keuzelijst.
1. Geef de naam, de afbeelding en de opties voor het maken van de container van de container op:

   * **Naam**: eventgridmodule
   * **Afbeeldings-URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opties**voor het maken van containers:

    ```json
        {
          "Env": [
           "inbound:serverAuth:tlsPolicy=enabled",
            "inbound:clientAuth:clientCert:enabled=false",
            "outbound:webhook:httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Klik op **Opslaan**.
 1. Ga door naar de volgende sectie om de module Azure Functions toe te voegen

    >[!IMPORTANT]
    > In deze zelf studie implementeert u de Event Grid-module voor het toestaan van HTTP/HTTPs-aanvragen, client verificatie uitgeschakeld en HTTP-abonnees toestaan. Voor werk belastingen wordt u aangeraden alleen HTTPs-aanvragen en abonnees in te scha kelen met client verificatie ingeschakeld. Zie [beveiliging en verificatie](security-authentication.md)voor meer informatie over het veilig configureren van Event grid module.
    

## <a name="deploy-azure-function-iot-edge-module"></a>Azure function IoT Edge-module implementeren

In deze sectie wordt beschreven hoe u de Azure Functions IoT-module implementeert, die als Event Grid-abonnee zou fungeren waaraan gebeurtenissen kunnen worden geleverd.

>[!IMPORTANT]
>In deze sectie gaat u een voor beeld van een op Azure-functie gebaseerde Abonneer module implementeren. Het kan natuurlijk een aangepaste IoT-module zijn die kan Luis teren naar HTTP POST-aanvragen.

### <a name="add-modules"></a>Modules toevoegen

1. Selecteer opnieuw **toevoegen** in de sectie **implementatie modules** . 
1. Selecteer **IOT Edge module** uit de typen modules in de vervolg keuzelijst.
1. Geef de naam, de afbeelding en de opties voor het maken van de container op van de container:

   * **Naam**: abonnee
   * **Afbeeldings-URI**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber-azfunc:latest`
   * **Opties**voor het maken van containers:

       ```json
            {
              "HostConfig": {
                "PortBindings": {
                  "80/tcp": [
                    {
                      "HostPort": "8080"
                    }
                  ]
                }
              }
            }
       ```

1. Klik op **Opslaan**.
1. Ga door naar de volgende sectie om de Azure Blob Storage-module toe te voegen

> [!NOTE]
> Blob Storage-module publiceert gebeurtenissen met behulp van HTTP. Controleer of de module Event Grid HTTP-en HTTPS-aanvragen toestaat met de volgende configuratie: `inbound:serverAuth:tlsPolicy=enabled`.

## <a name="deploy-azure-blob-storage-module"></a>Azure Blob Storage-module implementeren

In deze sectie wordt beschreven hoe u de Azure Blob Storage-module implementeert, die zou fungeren als Event Grid Publisher-publicatie-BLOB gemaakt en verwijderde gebeurtenissen.

### <a name="add-modules"></a>Modules toevoegen

1. Selecteer in de sectie **implementatie modules** de optie **toevoegen**
2. Selecteer **IOT Edge module** uit de typen modules in de vervolg keuzelijst.
3. Geef de naam, de afbeelding en de opties voor het maken van de container op van de container:

   * **Naam**: azureblobstorageoniotedge
   * **Afbeeldings-URI**: mcr.Microsoft.com/Azure-Blob-Storage:1.2.2-Preview
   * **Opties**voor het maken van containers:

```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
```

4. Werk de JSON bij die u hebt gekopieerd met de volgende gegevens:

   - Vervang `<your storage account name>` door een naam die u kunt onthouden. Account namen moeten 3 tot 24 tekens lang zijn, met kleine letters en cijfers. Geen spaties.

   - Vervang `<your storage account key>` door een base64-sleutel van 64-bytes. U kunt een sleutel met hulpprogram ma's zoals [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64)genereren. U gebruikt deze referenties om toegang te krijgen tot de Blob-opslag van andere modules.

   - Vervang `<event grid module name>` door de naam van uw Event Grid-module.
   - Vervang `<storage mount>` volgens het besturings systeem van de container.
     - Voor Linux-containers, **mijn volume:/blobroot**
     - Voor Windows-containers is**mijn volume: C:/BlobRoot**

5. Klik op **Opslaan**.
6. Klik op **volgende** om door te gaan naar de sectie routes

 ### <a name="setup-routes"></a>Installatie routes

Behoud de standaard routes en selecteer **volgende** om door te gaan naar de sectie beoordeling

### <a name="review-deployment"></a>Implementatie controleren

1. In het gedeelte beoordeling ziet u het JSON-implementatie manifest dat is gemaakt op basis van uw selecties in de vorige sectie. Controleer of de volgende vier modules worden weer geven: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **Subscriber** en **azureblobstorageoniotedge** die allemaal worden geïmplementeerd.
2. Controleer uw implementatie gegevens en selecteer vervolgens **verzenden**.

## <a name="verify-your-deployment"></a>Uw implementatie verifiëren

1. Nadat u de implementatie hebt verzonden, keert u terug naar de pagina IoT Edge van uw IoT-hub.
2. Selecteer het **IOT edge apparaat** waarop de implementatie is gericht om de details ervan te openen.
3. Controleer in de details van het apparaat of de eventgridmodule-, Subscriber-en azureblobstorageoniotedge-modules worden weer gegeven **in de implementatie** en worden **gerapporteerd door het apparaat**.

   Het kan even duren voordat de module op het apparaat is gestart en vervolgens weer aan IoT Hub is gemeld. Vernieuw de pagina om de bijgewerkte status weer te geven.

## <a name="publish-created-and-deleted-events"></a>Gemaakte en verwijderde gebeurtenissen publiceren

1. Deze module maakt automatisch onderwerp **MicrosoftStorage**. Controleer of deze bestaat
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Voorbeelduitvoer:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

2. Abonnees kunnen zich registreren voor gebeurtenissen die naar een onderwerp worden gepubliceerd. Als u een gebeurtenis wilt ontvangen, moet u een Event Grid-abonnement maken voor **MicrosoftStorage** -onderwerp.
    1. Maak blobsubscription. json met de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > De eigenschap **endpointType** geeft aan dat de abonnee een **webhook**is.  De **endpointUrl** geeft de URL aan waar de abonnee naar gebeurtenissen luistert. Deze URL komt overeen met de Azure function-voor beeld die u eerder hebt geïmplementeerd.

    2. Voer de volgende opdracht uit om een abonnement voor het onderwerp te maken. Controleer of de HTTP-status code is `200 OK`.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
    ```

    3. Voer de volgende opdracht uit om het abonnement te controleren dat is gemaakt. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
    ```

    Voorbeelduitvoer:

    ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

2. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) downloaden en [verbinding maken met uw lokale opslag](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Gebeurtenis levering verifiëren

### <a name="verify-blobcreated-event-delivery"></a>Levering van BlobCreated-gebeurtenis verifiëren

1. Upload bestanden als blok-blobs naar de lokale opslag van Azure Storage Explorer. de module publiceert automatisch Create-gebeurtenissen. 
2. Bekijk de logboeken van de abonnee voor het maken van een gebeurtenis. Volg de stappen om [de gebeurtenis levering te controleren](pub-sub-events-webhook-local.md#verify-event-delivery)

    Voorbeeld uitvoer:

    ```json
            Received event data [
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
          ]
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Levering van BlobDeleted-gebeurtenis verifiëren

1. Blobs verwijderen uit de lokale opslag met behulp van Azure Storage Explorer en de module publiceert automatisch delete-gebeurtenissen. 
2. Bekijk de gebeurtenis logboeken voor het verwijderen van abonnees. Volg de stappen om [de gebeurtenis levering te controleren](pub-sub-events-webhook-local.md#verify-event-delivery)

    Voorbeeld uitvoer:
    
    ```json
            Received event data [
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
          ]
    ```

Gefeliciteerd! U hebt de zelf studie voltooid. De volgende secties bevatten informatie over de gebeurtenis eigenschappen.

### <a name="event-properties"></a>Gebeurtenis eigenschappen

Hier ziet u de lijst met ondersteunde gebeurtenis eigenschappen en hun typen en beschrijvingen. 

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | string | Volledige bronpad naar de bron van de gebeurtenis. Dit veld kan niet worden geschreven. Event Grid levert deze waarde. |
| Onderwerp | string | Het door de uitgever gedefinieerde pad naar het gebeurtenis onderwerp. |
| Type | string | Een van de geregistreerde gebeurtenis typen voor deze gebeurtenis bron. |
| eventTime | string | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| id | string | De unieke id voor de gebeurtenis. |
| gegevens | object | Gebeurtenis gegevens van Blob-opslag. |
| dataVersion | string | De schema versie van het gegevens object. De uitgever definieert de schema versie. |
| metadataVersion | string | De schema versie van de meta gegevens van de gebeurtenis. Event Grid definieert het schema van de eigenschappen op het hoogste niveau. Event Grid levert deze waarde. |

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| api | string | De bewerking die de gebeurtenis heeft geactiveerd. Dit kan een van de volgende waarden zijn: <ul><li>BlobCreated-toegestane waarden zijn: `PutBlob` en `PutBlockList`</li><li>BlobDeleted-toegestane waarden zijn `DeleteBlob`, `DeleteAfterUpload` en `AutoDelete`. <p>De gebeurtenis `DeleteAfterUpload` wordt gegenereerd wanneer de blob automatisch wordt verwijderd, omdat de gewenste eigenschap deleteAfterUpload is ingesteld op True. </p><p>Er wordt een `AutoDelete` gebeurtenis gegenereerd wanneer de blob automatisch wordt verwijderd, omdat de gewenste eigenschaps waarde voor deleteAfterMinutes is verlopen.</p></li></ul>|
| clientRequestId | string | een aanvraag-id van de client voor de bewerking van de opslag-API. Deze id kan worden gebruikt om te correleren Azure Storage Diagnostische logboeken met behulp van het veld ' client-request-id ' in de logboeken, en kan worden verschaft in client aanvragen via de header ' x-MS-Client-Request-id '. Zie [logboek indeling](/rest/api/storageservices/storage-analytics-log-format)voor meer informatie. |
| requestId | string | Door de service gegenereerde aanvraag-id voor de bewerking van de opslag-API. Kan worden gebruikt om te correleren Azure Storage Diagnostische logboeken met behulp van het veld aanvraag-id-header in de logboeken en wordt geretourneerd van het initiëren van de API-aanroep in de header x-MS-Request-id. Zie de [logboek indeling](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | De waarde die u kunt gebruiken om bewerkingen voorwaardelijk uit te voeren. |
| Invoer | string | Het opgegeven inhouds type voor de blob. |
| contentLength | geheel getal | De grootte van de BLOB in bytes. |
| BlobType | string | Het type blob. Geldige waarden zijn ' BlockBlob ' of ' PageBlob '. |
| url | string | Het pad naar de blob. <br>Als de client gebruikmaakt van een BLOB REST API, heeft de URL deze structuur: *\<storage-account naam \>. blob.core.windows.net/\<container naam \> / \<file naam \>* . <br>Als de client een Data Lake Storage REST API gebruikt, heeft de URL deze structuur: *\<storage-account naam \>. dfs.core.windows.net/\<file-systeem naam \> / \<file naam \>* . |


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen in de Blob Storage-documentatie:

- [Blob Storage gebeurtenissen filteren](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Aanbevolen procedures voor het gebruiken van Blob Storage gebeurtenissen](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

In deze zelf studie hebt u gebeurtenissen gepubliceerd door blobs in een Azure-Blob Storage te maken of verwijderen. Raadpleeg de andere zelf studies voor meer informatie over het door sturen van gebeurtenissen naar de Cloud (Azure Event hub of Azure IoT Hub): 

- [Gebeurtenissen door sturen naar Azure Event Grid](forward-events-event-grid-cloud.md)
- [Gebeurtenissen door sturen naar Azure IoT Hub](forward-events-iothub.md)