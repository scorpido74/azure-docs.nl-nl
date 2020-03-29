---
title: Blobs voor opslagblokken op apparaten - Azure IoT Edge | Microsoft Documenten
description: Begrijp gelaagdheid en time-to-live-functies, bekijk ondersteunde blob-opslagbewerkingen en maak verbinding met uw blob-opslagaccount.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bea00f429f31f2be62ee6a9c00f88873c595d94c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509815"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Gegevens opslaan aan de rand met Azure Blob Storage op IoT Edge

Azure Blob Storage on IoT Edge biedt een [blokblob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) en [sluit blobopslagoplossing](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) aan de rand toe. Een blob-opslagmodule op uw IoT Edge-apparaat gedraagt zich als een Azure-blobservice, behalve dat de blobs lokaal worden opgeslagen op uw IoT Edge-apparaat. U hebt toegang tot uw blobs met dezelfde Azure storage SDK-methoden of blob API-aanroepen die u al gewend bent. In dit artikel worden de concepten met betrekking tot Azure Blob Storage op IoT Edge-container uitgelegd waarmee een blobservice wordt uitgevoerd op uw IoT Edge-apparaat.

Deze module is handig in scenario's:

* waar gegevens lokaal moeten worden opgeslagen totdat ze kunnen worden verwerkt of overgedragen naar de cloud. Deze gegevens kunnen video's, afbeeldingen, financiële gegevens, ziekenhuisgegevens of andere ongestructureerde gegevens zijn.
* wanneer apparaten zich op een plaats met beperkte connectiviteit bevinden.
* wanneer u de gegevens lokaal efficiënt wilt verwerken om toegang te krijgen tot de gegevens met een lage latentie, zodat u zo snel mogelijk reageren op noodsituaties.
* wanneer u de bandbreedtekosten wilt verlagen en wilt voorkomen dat terabytes aan gegevens naar de cloud worden overgebracht. U de gegevens lokaal verwerken en alleen de verwerkte gegevens naar de cloud verzenden.

Bekijk de video voor een snelle introductie
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Deze module wordt geleverd met **deviceToCloudUpload** en **deviceAutoDelete** functies.

**deviceToCloudUpload** is een configureerbare functionaliteit. Met deze functie worden de gegevens van uw lokale blobopslag automatisch geüpload naar Azure met intermitterende ondersteuning voor internetverbinding. Hiermee u:

* De functie DeviceToCloudUpload in- en uitschakelen.
* Kies de volgorde waarin de gegevens naar Azure worden gekopieerd, zoals NewestFirst of OldestFirst.
* Geef het Azure Storage-account op waarop u uw gegevens wilt uploaden.
* Geef de containers op die u naar Azure wilt uploaden. Met deze module u zowel bron- als doelcontainernamen opgeven.
* Kies de mogelijkheid om de blobs onmiddellijk te verwijderen, nadat de upload naar cloudopslag is voltooid
* Doe volledige blob `Put Blob` uploaden (met behulp `Put Block`van `Put Block List` `Append Block` de werking) en blok niveau uploaden (met behulp van , en bewerkingen).

Deze module maakt gebruik van block level upload, wanneer uw blob bestaat uit blokken. Hier volgen enkele van de meest voorkomende scenario's:

* Uw toepassing werkt een aantal blokken van een eerder geüploade blokblob bij of voegt nieuwe blokken toe aan een toevoegende blob, deze module uploadt alleen de bijgewerkte blokken en niet de hele blob.
* De module is het uploaden van blob en internetverbinding verdwijnt, wanneer de verbinding is weer terug het uploadt alleen de resterende blokken en niet de hele blob.

Als er een onverwachte procesbeëindiging (zoals stroomuitval) optreedt tijdens het uploaden van een blob, worden alle blokken die verschuldigd waren voor het uploaden opnieuw geüpload zodra de module weer online komt.

**deviceAutoDelete** is een configureerbare functionaliteit. Met deze functie worden uw blobs automatisch uit de lokale opslag verwijderd wanneer de opgegeven duur (gemeten in minuten) verloopt. Hiermee u:

* De functie ApparaatAutoDelete in- en uitschakelen.
* Geef de tijd in minuten op (deleteAfterMinutes) waarna de blobs automatisch worden verwijderd.
* Kies de mogelijkheid om de blob te behouden terwijl deze wordt geüpload als de waarde deleteAfterMinutes verloopt.

## <a name="prerequisites"></a>Vereisten

Een Azure IoT Edge-apparaat:

* U uw ontwikkelmachine of een virtuele machine als Een IoT Edge-apparaat gebruiken door de stappen in de quickstart voor [Linux-](quickstart-linux.md) of [Windows-apparaten](quickstart.md)te volgen.

* Raadpleeg [azure IoT Edge-ondersteunde systemen](support.md#operating-systems) voor een lijst met ondersteunde besturingssystemen en architecturen. De Azure Blob Storage op IoT Edge-module ondersteunt de volgende architecturen:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (preview)

Cloudresources:

Een standaard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload en deviceAutoDelete eigenschappen

Gebruik de gewenste eigenschappen van de module om **deviceToCloudUploadProperties** en **deviceAutoDeleteProperties**in te stellen. Gewenste eigenschappen kunnen tijdens de implementatie worden ingesteld of later worden gewijzigd door de moduletwee te bewerken zonder dat u opnieuw hoeft te worden geïmplementeerd. We raden u aan de `reported configuration` `configurationValidation` "Module Twin" te controleren op en om ervoor te zorgen dat waarden correct worden gepropageerd.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

De naam van `deviceToCloudUploadProperties`deze instelling is . Als u de IoT Edge-simulator gebruikt, stelt u de waarden in op de gerelateerde omgevingsvariabelen voor deze eigenschappen, die u vinden in de uitlegsectie.

| Eigenschap | Mogelijke waarden | Uitleg |
| ----- | ----- | ---- |
| uploadenOp | de waarde True, false | Standaard `false` ingesteld op. Als u de functie wilt inschakelen, `true`stelt u dit veld in op . <br><br> Omgevingsvariabele:`deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NieuwsteEerste, OudsteEerste | Hiermee u de volgorde kiezen waarin de gegevens naar Azure worden gekopieerd. Standaard `OldestFirst` ingesteld op. De volgorde wordt bepaald door de laatste gewijzigde tijd van Blob. <br><br> Omgevingsvariabele:`deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`is een verbindingstekenreeks waarmee u het opslagaccount opgeven waarop u uw gegevens wilt uploaden. Opgeven `Azure Storage Account Name` `Azure Storage Account Key`, `End point suffix`, . Voeg het juiste eindpuntsuffix van Azure toe waar gegevens worden geüpload, het varieert voor Global Azure, Government Azure en Microsoft Azure Stack. <br><br> U hier de SAS-verbindingstekenreeks voor Azure Storage opgeven. Maar je moet deze eigenschap bijwerken wanneer deze verloopt. <br><br> Omgevingsvariabele:`deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| opslagContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Hiermee u de containernamen opgeven die u naar Azure wilt uploaden. Met deze module u zowel bron- als doelcontainernamen opgeven. Als u de naam van de doelcontainer niet opgeeft, `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`wordt de containernaam automatisch toegewezen als . U sjabloontekenreeksen maken voor de naam van doelcontainers en de kolom mogelijke waarden bekijken. <br>* %h -> IoT Hub Name (3-50 tekens). <br>* %d -> IoT Edge Device ID (1 tot 129 tekens). <br>* %m -> Modulenaam (1 tot 64 tekens). <br>* %c -> Broncontainernaam (3 tot 63 tekens). <br><br>De maximale grootte van de containernaam is 63 tekens, terwijl de naam van de doelcontainer automatisch wordt toewijzen als de grootte van de container groter is dan 63 tekens, elke sectie (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) wordt bijsnijden tot 15 Tekens. <br><br> Omgevingsvariabele:`deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | de waarde True, false | Standaard `false` ingesteld op. Wanneer deze is `true`ingesteld op , wordt de gegevens automatisch verwijderd wanneer het uploaden naar cloudopslag is voltooid. <br><br> **LET OP:** Als u toevoegende blobs gebruikt, verwijdert deze instelling toevoegende blobs uit lokale opslag na een geslaagde upload en mislukken toekomstige bewerkingen van het toevoegen van blokken aan die blobs. Gebruik deze instelling met de nodige voorzichtigheid, schakel dit niet in als uw toepassing onregelmatige toe-appendbewerkingen uitvoert of geen continue toe-toe-zoals-operations ondersteunt<br><br> Omgevingsvariabele: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`. |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties DeviceAutoDeleteProperties deviceAutoDeleteProperties deviceAuto

De naam van `deviceAutoDeleteProperties`deze instelling is . Als u de IoT Edge-simulator gebruikt, stelt u de waarden in op de gerelateerde omgevingsvariabelen voor deze eigenschappen, die u vinden in de uitlegsectie.

| Eigenschap | Mogelijke waarden | Uitleg |
| ----- | ----- | ---- |
| deleteOn | de waarde True, false | Standaard `false` ingesteld op. Als u de functie wilt inschakelen, `true`stelt u dit veld in op . <br><br> Omgevingsvariabele:`deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Geef de tijd in minuten op. De module verwijdert automatisch uw blobs uit de lokale opslag wanneer deze waarde verloopt. <br><br> Omgevingsvariabele:`deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| behoudenWhileUploading | de waarde True, false | Standaard is ingesteld `true`op , en het zal de blob te behouden terwijl het uploaden naar cloud opslag als deleteAfterMinutes verloopt. U deze `false` instellen op en het verwijdert de gegevens zodra deleteAfterMinutes verloopt. Opmerking: Om deze eigenschap te laten werken, moet uploaden worden ingesteld op true.  <br><br> **LET OP:** Als u toevoegende blobs gebruikt, verwijdert deze instelling toevoegende blobs uit lokale opslag wanneer de waarde verloopt en zullen toekomstige bewerkingen voor het toevoegen van blokken aan die blobs mislukken. U ervoor zorgen dat de vervaldatumwaarde groot genoeg is voor de verwachte frequentie van toebrengenbewerkingen die door uw toepassing worden uitgevoerd.<br><br> Omgevingsvariabele:`deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>SMB-share gebruiken als lokale opslag

U SMB-share als uw lokale opslagpad leveren wanneer u Windows-container van deze module implementeert op Windows-host.

Zorg ervoor dat het SMB-aandeel en het IoT-apparaat zich in wederzijds vertrouwde domeinen bevinden.

U kunt `New-SmbGlobalMapping` de opdracht PowerShell uitvoeren om het aandeel SMB lokaal in kaart te brengen op het IoT-apparaat waarop Windows wordt uitgevoerd.

Hieronder vindt u de configuratiestappen:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Bijvoorbeeld:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

Deze opdracht gebruikt de referenties om te verifiëren met de externe SMB-server. Breng vervolgens het pad voor het delen op afstand in kaart met G: stationsletter (kan elke andere beschikbare stationsletter zijn). Het IoT-apparaat heeft nu het gegevensvolume toegewezen aan een pad op de G: station.

Zorg ervoor dat de gebruiker in IoT-apparaat kan lezen/schrijven naar het externe SMB-aandeel.

Voor uw implementatie `<storage mount>` kan de waarde **van G:/ContainerData:C:/BlobRoot**zijn.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Directorytoegang verlenen aan containergebruiker op Linux

Als u [volumemount](https://docs.docker.com/storage/volumes/) hebt gebruikt voor opslag in uw create-opties voor Linux-containers, hoeft u geen extra stappen te ondernemen, maar als u [bindmount](https://docs.docker.com/storage/bind-mounts/) hebt gebruikt, zijn deze stappen vereist om de service correct uit te voeren.

Volgens het principe van de minste bevoegdheid om de toegangsrechten voor gebruikers te beperken tot absolute minimummachtigingen die ze nodig hebben om hun werk uit te voeren, bevat deze module een gebruiker (naam: absie, ID: 11000) en een gebruikersgroep (naam: absie, ID: 11000). Als de container wordt gestart als **root** (standaardgebruiker is **root),** wordt onze service gestart als de **absie-gebruiker** met lage bevoegdheden.

Dit gedrag maakt de configuratie van de machtigingen op hostpad bindt cruciaal voor de service om correct te werken, anders zal de service crashen met toegang geweigerdfouten. Het pad dat wordt gebruikt in directorybinding moet toegankelijk zijn voor de containergebruiker (bijvoorbeeld absie 11000). U de containergebruiker toegang verlenen tot de map door de onderstaande opdrachten op de host uit te voeren:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Bijvoorbeeld:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Als u de service als gebruiker anders dan **absie**moet uitvoeren, u uw aangepaste gebruikersnaam opgeven in createOptions onder eigenschap 'Gebruiker' in uw implementatiemanifest. In dat geval moet u standaard- `0`of hoofdgroep-id gebruiken.

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

Geef de containergebruiker nu toegang tot de map

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Logboekbestanden configureren

Zie deze aanbevolen procedures voor productie voor informatie over het configureren van [logboekbestanden](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)voor uw module.

## <a name="connect-to-your-blob-storage-module"></a>Verbinding maken met uw blob-opslagmodule

U de accountnaam en de accountsleutel die u voor uw module hebt geconfigureerd, gebruiken om toegang te krijgen tot de blob-opslag op uw IoT Edge-apparaat.

Geef uw IoT Edge-apparaat op als het blob-eindpunt voor alle opslagaanvragen die u eraan inbrengt. U [een verbindingstekenreeks maken voor een expliciet opslageindpunt](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) met behulp van de apparaatgegevens van IoT Edge en de accountnaam die u hebt geconfigureerd.

* Voor modules die worden geïmplementeerd op hetzelfde apparaat als waar de Azure Blob Storage on `http://<module name>:11002/<account name>`IoT Edge-module wordt uitgevoerd, is het blob-eindpunt: .
* Voor modules of toepassingen die op een ander apparaat worden uitgevoerd, moet u het juiste eindpunt voor uw netwerk kiezen. Kies afhankelijk van uw netwerkinstelling een eindpuntindeling, zodat het gegevensverkeer van uw externe module of toepassing het apparaat kan bereiken waarop de Azure Blob Storage op IoT Edge-module wordt uitgevoerd. Het blob-eindpunt voor dit scenario is een van:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Snelstartvoorbeelden voor Azure Blob Storage

De Azure Blob Storage-documentatie bevat voorbeeldcode snel starten in verschillende talen. U deze voorbeelden uitvoeren om Azure Blob Storage op IoT Edge te testen door het blob-eindpunt te wijzigen om verbinding te maken met uw lokale blobopslagmodule.

De volgende snelstartvoorbeelden maken gebruik van talen die ook worden ondersteund door IoT Edge, zodat u ze implementeren als IoT Edge-modules naast de blob-opslagmodule:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Versies vóór V2.1 van de Python SDK hebben een bekend probleem waarbij de module de creatietijd van blobs niet teruggeeft. Vanwege dat probleem werken sommige methoden zoals lijstblobs niet. Als tijdelijke oplossing stelt u de API-versie op de blobclient expliciet in op '2017-04-17'. Voorbeeld:`block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Voorbeeld van Blob toevoegen](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [OK](../storage/blobs/storage-quickstart-blobs-go.md)
* [Php](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Verbinding maken met uw lokale opslag met Azure Storage Explorer

U [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken om verbinding te maken met uw lokale opslagaccount.

1. Azure Storage Explorer downloaden en installeren

1. Verbinding maken met Azure Storage met een verbindingstekenreeks

1. Verbindingstekenreeks opgeven:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Ga door de stappen om verbinding te maken.

1. Container maken in uw lokale opslagaccount

1. Begin met het uploaden van bestanden als Blobs blokkeren of Blobs toevoegen.
   > [!NOTE]
   > Deze module biedt geen ondersteuning voor paginablobs.

1. U ervoor kiezen om uw Azure-opslagaccounts ook in Storage Explorer te verbinden. Deze configuratie biedt u één weergave voor zowel uw lokale opslagaccount als azure-opslagaccount

## <a name="supported-storage-operations"></a>Ondersteunde opslagbewerkingen

Blob-opslagmodules op IoT Edge gebruiken de Azure Storage SDKs en komen overeen met de 2017-04-17-versie van de Azure Storage API voor blokblobeindpunten.

Omdat niet alle Azure Blob Storage-bewerkingen worden ondersteund door Azure Blob Storage op IoT Edge, wordt in deze sectie de status van elk gedeelte weergegeven.

### <a name="account"></a>Account

Ondersteund:

* Containers weergeven

Unsupported:

* Blobservice-eigenschappen opmaken en instellen
* Preflight blob-aanvraag
* Ontvang blobservicestatistieken
* Accountgegevens opvragen

### <a name="containers"></a>Containers

Ondersteund:

* Container maken en verwijderen
* Containereigenschappen en metagegevens ophalen
* Blobs vermelden
* Container ACL in- en instellen
* Containermetagegevens instellen

Unsupported:

* Leasecontainer

### <a name="blobs"></a>Blobs

Ondersteund:

* Blob plaatsen, krijgen en verwijderen
* Blob-eigenschappen openen en instellen
* Blob-metagegevens openen en instellen

Unsupported:

* Blob leasen
* Momentopname maken van blob
* Blob kopiëren en afbreken
* Blob verwijderen ongedaan maken
* Blob-laag instellen

### <a name="block-blobs"></a>Blok-blobs

Ondersteund:

* Zet blok
* Bloklijst plaatsen en krijgen

Unsupported:

* Blok van URL plaatsen

### <a name="append-blobs"></a>Toevoeg-blobs

Ondersteund:

* Aanhangselblok

Unsupported:

* Blok toevoegen aan URL

## <a name="event-grid-on-iot-edge-integration"></a>Event Grid op IoT Edge-integratie

> [!CAUTION]
> De integratie met Event Grid op IoT Edge is in preview

Deze Azure Blob Storage on IoT Edge-module biedt nu integratie met Event Grid op IoT Edge. Zie de zelfstudie voor gedetailleerde informatie over deze integratie [om de modules te implementeren, gebeurtenissen te publiceren en de levering van gebeurtenissen te verifiëren.](../event-grid/edge/react-blob-storage-events-locally.md)

## <a name="release-notes"></a>Releaseopmerkingen

Hier zijn de [release notes in docker hub](https://hub.docker.com/_/microsoft-azure-blob-storage) voor deze module

## <a name="feedback"></a>Feedback

Uw feedback is belangrijk voor ons om deze module en de functies ervan nuttig en gebruiksvriendelijk te maken. Deel uw feedback en laat ons weten hoe we kunnen verbeteren.

U ons bereiken opabsiotfeedback@microsoft.com

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren van Azure Blob Storage op IoT Edge](how-to-deploy-blob.md)

Blijf op de hoogte van recente updates en aankondigingen in het [Azure Blob Storage on IoT Edge-blog](https://aka.ms/abs-iot-blogpost)
