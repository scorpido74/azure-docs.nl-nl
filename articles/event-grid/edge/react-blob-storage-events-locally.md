---
title: Reageren op blobopslagmodulegebeurtenissen - Azure Event Grid IoT Edge | Microsoft Documenten
description: Reageren op gebeurtenissen in blobopslagmodulegebeurtenissen
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086601"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Zelfstudie: Reageren op blobopslaggebeurtenissen op IoT Edge (voorbeeld)
In dit artikel ziet u hoe u de Azure Blob Storage on IoT-module implementeert, die zou fungeren als uitgever van gebeurtenisrasters om gebeurtenissen over blob-creatie en blobverwijdering naar gebeurtenisraster te verzenden.  

Zie [Azure Blob Storage op IoT Edge](../../iot-edge/how-to-store-data-blob.md) en de bijbehorende functies voor een overzicht van de Azure Blob Storage op IoT Edge.

> [!WARNING]
> Azure Blob Storage on IoT Edge-integratie met gebeurtenisraster is in Preview

Om deze tutorial te voltooien, moet je:

* **Azure-abonnement** - Maak een [gratis account](https://azure.microsoft.com/free) als u er nog geen hebt. 
* **Azure IoT Hub en IoT Edge-apparaat** - Volg de stappen in de quickstart voor [Linux-](../../iot-edge/quickstart-linux.md) of [Windows-apparaten](../../iot-edge/quickstart.md) als u er nog geen hebt.

## <a name="deploy-event-grid-iot-edge-module"></a>GebeurtenisrasterIoT Edge-module implementeren

Er zijn verschillende manieren om modules te implementeren op een IoT Edge-apparaat en ze werken allemaal voor Azure Event Grid op IoT Edge. In dit artikel worden de stappen beschreven om gebeurtenisraster op IoT Edge te implementeren vanuit de Azure-portal.

>[!NOTE]
> In deze zelfstudie implementeert u de module Event Grid zonder volharding. Dit betekent dat alle onderwerpen en abonnementen die u in deze zelfstudie maakt, worden verwijderd wanneer u de module opnieuw implementeert. Zie de volgende artikelen: Status blijven bestaan [in Linux](persist-state-linux.md) of Persist status in [Windows](persist-state-windows.md)voor meer informatie over het instellen van persistentie. Voor productieworkloads raden we u aan de Event Grid-module met volharding te installeren.


### <a name="select-your-iot-edge-device"></a>Selecteer uw IoT Edge-apparaat

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Navigeer naar uw IoT-hub.
1. Selecteer **IoT Edge** in het menu in de sectie **Automatisch apparaatbeheer.** 
1. Klik op de id van het doelapparaat in de lijst met apparaten
1. Selecteer **Modules instellen**. Houd de pagina open. U gaat verder met de stappen in het volgende gedeelte.

### <a name="configure-a-deployment-manifest"></a>Een implementatiemanifest configureren

Een implementatiemanifest is een JSON-document dat beschrijft welke modules moeten worden geïmplementeerd, hoe gegevens tussen de modules stromen en de gewenste eigenschappen van de moduletweeling. De Azure-portal heeft een wizard die u door het maken van een implementatiemanifest leidt, in plaats van het JSON-document handmatig te bouwen.  Het heeft drie stappen: **Modules toevoegen,** **Routes opgeven**en **Implementatie controleren**.

### <a name="add-modules"></a>Modules toevoegen

1. Selecteer **Toevoegen** in de sectie **Implementatiemodules**
1. Selecteer **IoT Edge Module** in de typen modules in de vervolgkeuzelijst
1. Geef de opties voor het maken van containers op:

   * **Naam**: eventgridmodule
   * **Afbeelding URI**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opties voor het maken van containers:**

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
           "inbound__clientAuth__clientCert__enabled=false"
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

 1. Klik **op Opslaan**
 1. Ga verder naar de volgende sectie om de Azure Event Grid Subscriber-module toe te voegen voordat u deze samen implementeert.

    >[!IMPORTANT]
    > In deze zelfstudie leert u de module Gebeurtenisraster te implementeren om zowel HTTP/HTTP-aanvragen, clientverificatie uitgeschakeld) toe te staan. Voor productieworkloads raden we u aan alleen HTTPs-aanvragen en abonnees in te schakelen met clientverificatie ingeschakeld. Zie [Beveiliging en verificatie](security-authentication.md)voor meer informatie over het veilig configureren van de gebeurtenisrastermodule.
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>IoT Edge-module voor gebeurtenisrasterabonnees implementeren

In deze sectie ziet u hoe u een andere IoT-module implementeert die fungeert als gebeurtenishandler waaraan gebeurtenissen kunnen worden geleverd.

### <a name="add-modules"></a>Modules toevoegen

1. Selecteer opnieuw **toevoegen in** de sectie **Implementatiemodules.** 
1. Selecteer **IoT Edge Module** in de typen modules in de vervolgkeuzelijst
1. Geef de opties voor naam, afbeelding en containermaken van de container op:

   * **Naam**: abonnee
   * **Afbeelding URI**:`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Opties voor het maken van containers:** geen
1. Klik **op Opslaan**
1. Ga verder naar de volgende sectie om de Azure Blob Storage-module toe te voegen

## <a name="deploy-azure-blob-storage-module"></a>Azure Blob-opslagmodule implementeren

In deze sectie ziet u hoe u de Azure Blob Storage-module implementeert, die zou fungeren als een uitgever van het gebeurtenisraster die blobs publiceert en verwijderde gebeurtenissen publiceert.

### <a name="add-modules"></a>Modules toevoegen

1. Selecteer **Toevoegen** in de sectie **Implementatiemodules**
2. Selecteer **IoT Edge Module** in de typen modules in de vervolgkeuzelijst
3. Geef de opties voor naam, afbeelding en containermaken van de container op:

   * **Naam**: azureblobstorageoniotedge
   * **Afbeelding URI**: mcr.microsoft.com/azure-blob-storage:latest
   * **Opties voor het maken van containers:**

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

   > [!IMPORTANT]
   > - Blob Storage-module kan gebeurtenissen publiceren met zowel HTTPS als HTTP. 
   > - Als u de clientverificatie voor EventGrid hebt ingeschakeld, moet u de waarde van `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`EVENTGRID_ENDPOINT bijwerken om https toe te staan, zoals dit: .
   > - Voeg ook een `AllowUnknownCertificateAuthority=true` andere omgevingsvariabele toe aan de bovenstaande Json. Wanneer u met EventGrid via HTTPS praat, staat **AllowUnknownCertificateAuthority** de opslagmodule toe om zelfondertekende EventGrid-servercertificaten te vertrouwen.

4. Werk de JSON bij die u hebt gekopieerd met de volgende informatie:

   - Vervang `<your storage account name>` door een naam die u zich herinneren. Accountnamen moeten 3 tot 24 tekens lang zijn, met kleine letters en cijfers. Geen spaties.

   - Vervang `<your storage account key>` door een 64-byte base64-toets. U een sleutel genereren met tools zoals [GeneratePlus.](https://generate.plus/en/base64?gp_base64_base[length]=64) U gebruikt deze referenties om toegang te krijgen tot de blob-opslag van andere modules.

   - Vervang `<event grid module name>` de naam van de module Gebeurtenisraster.
   - Vervang `<storage mount>` volgens uw containerbesturingssysteem.
     - Voor **Linux-containers, mijn-volume:/blobroot**
     - Voor**Windows-containers, mijn volume:C:/BlobRoot**

5. Klik **op Opslaan**
6. Klik **op Volgende** om verder te gaan naar de sectie routes

    > [!NOTE]
    > Als u een Azure VM als randapparaat gebruikt, voegt u een inkomende poortregel toe om binnenkomend verkeer toe te staan op de hostpoorten die in deze zelfstudie worden gebruikt: 4438, 5888, 8080 en 11002. Zie Poorten openen voor [een vm voor](../../virtual-machines/windows/nsg-quickstart-portal.md)instructies voor het toevoegen van de regel.

### <a name="setup-routes"></a>Setup routes

De standaardroutes behouden en **Volgende** selecteren om door te gaan naar de controlesectie

### <a name="review-deployment"></a>Implementatie controleren

1. In de sectie beoordeling ziet u het JSON-implementatiemanifest dat is gemaakt op basis van uw selecties in de vorige sectie. Controleer of u de volgende vier modules ziet: **$edgeAgent,** **$edgeHub,** **eventgridmodule,** **abonnee** en **azureblobstorageoniotedge** die allemaal worden geïmplementeerd.
2. Controleer uw implementatiegegevens en selecteer **Verzenden**.

## <a name="verify-your-deployment"></a>Uw implementatie verifiëren

1. Nadat u de implementatie hebt verzonden, keert u terug naar de IoT Edge-pagina van uw IoT-hub.
2. Selecteer het **IoT Edge-apparaat** dat u met de implementatie hebt getarget om de details te openen.
3. Controleer in de apparaatgegevens of de eventgridmodule, abonnee- en azureblobstorageoniotedge-modules worden vermeld als **zowel opgegeven in implementatie** als gerapporteerd per **apparaat**.

   Het kan even duren voordat de module op het apparaat is gestart en vervolgens wordt gerapporteerd aan IoT Hub. Vernieuw de pagina om een bijgewerkte status te zien.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>BlobCreated- en BlobDeleted-gebeurtenissen publiceren

1. Met deze module wordt automatisch onderwerp **MicrosoftStorage aanmaakt.** Controleren of deze bestaat
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

    > [!IMPORTANT]
    > - Als de clientverificatie via De sleutel van SAS is ingeschakeld, moet de eerder opgegeven SAS-toets als koptekst worden toegevoegd. Vandaar dat de krul verzoek zal zijn:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Voor de HTTPS-stroom is de krulaanvraag:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Abonnees kunnen zich inschrijven voor evenementen die zijn gepubliceerd in een onderwerp. Als je een evenement wilt ontvangen, moet je een Event Grid-abonnement maken voor **het MicrosoftStorage-onderwerp.**
    1. Maak blobsubscription.json met de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload

       ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       >[!NOTE]
       > De eigenschap **endpointType** geeft aan dat de abonnee een **Webhook**is.  De **endpointUrl** geeft de URL op waarop de abonnee naar gebeurtenissen luistert. Deze URL komt overeen met het voorbeeld van de Azure-functie dat u eerder hebt geïmplementeerd.

    2. Voer de volgende opdracht uit om een abonnement voor het onderwerp te maken. Controleer of u de HTTP-statuscode ziet `200 OK`.

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - Als de clientverificatie via De sleutel van SAS is ingeschakeld, moet de eerder opgegeven SAS-toets als koptekst worden toegevoegd. Vandaar dat de krul verzoek zal zijn:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Voor de HTTPS-stroom is de krulaanvraag:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Voer de volgende opdracht uit om te controleren of het abonnement is gemaakt. HTTP-statuscode van 200 OK moet worden geretourneerd.

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
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - Als de clientverificatie via De sleutel van SAS is ingeschakeld, moet de eerder opgegeven SAS-toets als koptekst worden toegevoegd. Vandaar dat de krul verzoek zal zijn:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Voor de HTTPS-stroom is de krulaanvraag:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. [Azure Storage Explorer downloaden](https://azure.microsoft.com/features/storage-explorer/) en verbinden met uw lokale [opslag](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>De levering van gebeurtenissen verifiëren

### <a name="verify-blobcreated-event-delivery"></a>De weergave van blobs met blobs verifiëren

1. Upload bestanden als blokblobs naar de lokale opslag vanuit Azure Storage Explorer en de module publiceert automatisch afspraken maken. 
2. Bekijk de abonneelogboeken voor het maken van een gebeurtenis. Volg de stappen om [de gebeurtenislevering](pub-sub-events-webhook-local.md#verify-event-delivery) te verifiëren

    Voorbeelduitvoer:

    ```json
            Received Event:
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
    ```

### <a name="verify-blobdeleted-event-delivery"></a>De weergave van blobverwijderde gebeurtenissen verifiëren

1. Verwijder blobs uit de lokale opslag met Azure Storage Explorer en de module publiceert automatisch verwijdergebeurtenissen. 
2. Bekijk de abonneelogboeken voor het verwijderen van een gebeurtenis. Volg de stappen om [de gebeurtenislevering](pub-sub-events-webhook-local.md#verify-event-delivery) te verifiëren

    Voorbeelduitvoer:
    
    ```json
            Received Event:
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
    ```

Gefeliciteerd! Je hebt de tutorial voltooid. In de volgende secties vindt u meer informatie over de gebeurteniseigenschappen.

### <a name="event-properties"></a>Gebeurtenis-eigenschappen

Hier is de lijst met ondersteunde gebeurtenis-eigenschappen en hun typen en beschrijvingen. 

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledig resourcepad naar de gebeurtenisbron. Dit veld is niet schrijfbaar. Event Grid biedt deze waarde. |
| Onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | Unieke id voor de gebeurtenis. |
| data | object | Gebeurtenisgegevens voor blobopslag. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| api | tekenreeks | De bewerking die de gebeurtenis heeft geactiveerd. Het kan een van de volgende waarden zijn: <ul><li>BlobCreated - toegestane waarden `PutBlob` zijn: en`PutBlockList`</li><li>BlobDeleted - toegestane `DeleteBlob`waarden `DeleteAfterUpload` `AutoDelete`zijn en . <p>De `DeleteAfterUpload` gebeurtenis wordt gegenereerd wanneer blob automatisch wordt verwijderd omdat de gewenste eigenschap DeleteAfterUpload is ingesteld op true. </p><p>`AutoDelete`gebeurtenis wordt gegenereerd wanneer blob automatisch wordt verwijderd omdat de gewenste eigenschapswaarde van DeleteAfterMinutes is verlopen.</p></li></ul>|
| clientRequestId | tekenreeks | een door de client verstrekte aanvraag-id voor de opslag-API-bewerking. Deze ID kan worden gebruikt om te correleren met diagnostische logboeken voor Azure Storage met behulp van het veld 'client-request-id' in de logboeken en kan worden verstrekt in clientaanvragen met de koptekst 'x-ms-client-request-id'. Zie [Opmaak logboek .](/rest/api/storageservices/storage-analytics-log-format) |
| requestId | tekenreeks | Door service gegenereerde aanvraag-id voor de opslag-API-bewerking. Kan worden gebruikt om te correleren met diagnostische logboeken voor Azure Storage met behulp van het veld 'request-id-header' in de logboeken en wordt geretourneerd door het starten van API-aanroep in de koptekst 'x-ms-request-id'. Zie [Logboeknotatie](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | tekenreeks | De waarde die u gebruiken om bewerkingen voorwaardelijk uit te voeren. |
| Contenttype | tekenreeks | Het inhoudstype dat is opgegeven voor de blob. |
| inhoudLengte | geheel getal | De grootte van de blob in bytes. |
| blobtype | tekenreeks | Het type blob. Geldige waarden zijn "BlockBlob" of "PageBlob". |
| url | tekenreeks | Het pad naar de blob. <br>Als de client een Blob REST API gebruikt, heeft de url deze structuur: * \<storage-account-name\>\<.blob.core.windows.net/ containernaam\>/\<bestandsnaam\>*. <br>Als de client een Data Lake Storage REST API gebruikt, heeft de url deze structuur: * \<storage-account-name\>\<.dfs.core.windows.net/ file-system-name\>/\<file-name file-name\>*. |


## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende artikelen uit de documentatie van de Blob Storage:

- [Blob-opslaggebeurtenissen filteren](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Aanbevolen procedures voor het consumeren van Blob Storage-gebeurtenissen](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

In deze zelfstudie hebt u gebeurtenissen gepubliceerd door blobs te maken of te verwijderen in een Azure Blob-opslag. Bekijk de andere zelfstudies voor meer informatie over het doorsturen van gebeurtenissen naar de cloud (Azure Event Hub of Azure IoT Hub): 

- [Gebeurtenissen doorsturen naar Azure Event Grid](forward-events-event-grid-cloud.md)
- [Gebeurtenissen doorsturen naar Azure IoT Hub](forward-events-iothub.md)
