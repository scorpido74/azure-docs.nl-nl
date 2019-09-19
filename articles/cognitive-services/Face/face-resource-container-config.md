---
title: Containers configureren-FACE-API
titleSuffix: Azure Cognitive Services
description: Configuratie-instellingen voor containers.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: a07f088f7f5699a2698c0ea8fb0e853b3d287572
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101958"
---
# <a name="configure-face-docker-containers"></a>Face docker-containers configureren

De omgeving voor de runtime van het **Opper vlak** wordt geconfigureerd met de `docker run` opdracht argumenten. Deze container heeft verschillende vereiste instellingen, samen met enkele optionele instellingen. Verschillende [voorbeelden](#example-docker-run-commands) van de opdracht beschikbaar zijn. De container-specifieke instellingen zijn de facturerings instellingen. 

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), en [ `Eula` ](#eula-setting) instellingen samen worden gebruikt en u moet geldige waarden opgeven voor alle drie deze, anders uw container start niet. Zie voor meer informatie over het gebruik van deze configuratie-instellingen voor het starten van een container [facturering](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey configuratie-instelling

De `ApiKey` instelling geeft u aan de Azure-resource-sleutel die wordt gebruikt voor het bijhouden van informatie over facturering voor de container. U moet een waarde opgeven voor de ApiKey en de waarde moet een geldige sleutel zijn voor de _Cognitive Services_ bron die is opgegeven [`Billing`](#billing-configuration-setting) voor de configuratie-instelling.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Cognitive Services** Resource beheer, onder **sleutels**

## <a name="applicationinsights-setting"></a>Application Insights-instelling

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Facturering van configuratie-instelling

Met `Billing` deze instelling geeft u de eindpunt-URI op van de _Cognitive Services_ resource op Azure die wordt gebruikt om de facturerings gegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldige eindpunt-URI zijn voor een _Cognitive Services_ resource in Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling bevindt zich op de volgende locatie:

* Azure Portal: **Cognitive Services** Overzicht, label`Endpoint`

Vergeet niet om het _gezichts_ routering toe te voegen aan de URI van het eind punt zoals weer gegeven in het voor beeld. 

|Vereist| Name | Gegevenstype | Description |
|--|------|-----------|-------------|
|Ja| `Billing` | Reeks | URI van de facturering-eindpunt<br><br>Voorbeeld:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>CloudAI configuratie-instellingen

De configuratie-instellingen in de `CloudAI` sectie bieden container-specifieke opties die uniek is voor de container. De volgende instellingen en objecten worden ondersteund voor de Face-container in de `CloudAI` sectie

| Name | Gegevenstype | Description |
|------|-----------|-------------|
| `Storage` | Object | De Opslagscenario wordt gebruikt door de Face-container. Voor meer informatie over scenario's voor opslag en de bijbehorende instellingen voor de `Storage` object, Zie [Opslaginstellingen scenario](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Opslaginstellingen scenario

De Face-container slaat blob, cache, metagegevens en wachtrijgegevens, afhankelijk van wat wordt opgeslagen. Training indexen en resultaten voor een grote Persoonsgroep worden als blob-gegevens opgeslagen. De Face-container biedt twee verschillende opslag-scenario's tijdens interactie met en het opslaan van deze typen gegevens:

* Geheugen  
  Alle vier typen gegevens worden opgeslagen in het geheugen. Ze niet worden gedistribueerd, noch zijn ze permanente. Als de Face-container wordt gestopt of verwijderd, worden alle gegevens in de opslag voor die container vernietigd.  
  Dit is de standaard Opslagscenario voor de Face-container.
* Azure  
  De Face-container maakt gebruik van Azure Storage en Azure Cosmos DB voor de distributie van deze vier typen gegevens over de permanente opslag. BLOB- en wachtrijservices gegevens worden verwerkt door Azure Storage. Meta gegevens en cache gegevens worden verwerkt door Azure Cosmos DB. Als de Face-container wordt gestopt of verwijderd, blijft alle gegevens in de opslag voor die container opgeslagen in Azure Storage en Azure Cosmos DB.  
  De resources die worden gebruikt door het Azure storage-scenario gelden de volgende aanvullende vereisten
  * De Azure Storage-resource moet het soort StorageV2-account gebruiken
  * De Azure Cosmos DB resource moet de Azure Cosmos DB-API voor MongoDB gebruiken

De scenario's voor opslag en de bijbehorende configuratie-instellingen worden beheerd door de `Storage` object, onder de `CloudAI` configuratiesectie. De volgende configuratie-instellingen zijn beschikbaar in de `Storage` object:

| Name | Gegevenstype | Description |
|------|-----------|-------------|
| `StorageScenario` | Reeks | De Opslagscenario wordt ondersteund door de container. De volgende waarden zijn beschikbaar<br/>`Memory` -Standaardwaarde. Container maakt gebruik van niet-permanente, niet-gedistribueerde en in het geheugen, opslag, voor gebruik met één knooppunt, tijdelijk. Als de container wordt gestopt of verwijderd, wordt de opslag voor deze container wordt vernietigd.<br/>`Azure` -Container maakt gebruik van Azure-resources voor de opslag. Als de container wordt gestopt of verwijderd, worden persistent gemaakt met de opslag voor die container.|
| `ConnectionStringOfAzureStorage` | Reeks | De verbindingsreeks voor de Azure Storage-resource die wordt gebruikt door de container.<br/>Deze instelling geldt alleen als `Azure` is opgegeven voor de `StorageScenario` configuratie-instelling. |
| `ConnectionStringOfCosmosMongo` | Reeks | De MongoDB-verbindingsreeks voor de Azure Cosmos DB-resource die wordt gebruikt door de container.<br/>Deze instelling geldt alleen als `Azure` is opgegeven voor de `StorageScenario` configuratie-instelling. |

Bijvoorbeeld de volgende opdracht Hiermee geeft u het scenario voor Azure storage en voorbeeld-verbindingsreeksen voor de Azure Storage en Cosmos DB-resources gebruikt voor het opslaan van gegevens voor de Face-container biedt.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Het Opslagscenario voor wordt afzonderlijk verwerkt van koppelingen invoer en uitvoer van koppelingen. U kunt een combinatie van deze functies voor een enkele container opgeven. De volgende opdracht wordt bijvoorbeeld gedefinieerd voor een koppelpunt van de Docker-binding aan de `D:\Output` map op de hostcomputer als het koppelpunt uitvoer wordt vervolgens een container van de containerinstallatiekopie Face opslaan van logboekbestanden in JSON-indeling voor het koppelen van de uitvoer. De opdracht ook Hiermee geeft u het scenario voor Azure storage en voorbeeld-verbindingsreeksen voor de Azure Storage en Cosmos DB-resources gebruikt voor het opslaan van gegevens voor de Face-container biedt.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Overeenkomst instelling

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd-instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Instellingen voor http-proxy referenties

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Instellingen voor koppelen

Gebruik bind koppelt om te lezen en schrijven van gegevens naar en van de container. U kunt opgeven van een koppelpunt invoer of uitvoer koppelen door op te geven de `--mount` optie in de [docker uitvoeren](https://docs.docker.com/engine/reference/commandline/run/) opdracht.

De face-containers gebruiken geen invoer-of uitvoer koppelingen om training of service gegevens op te slaan. 

De exacte syntaxis van de locatie van de host koppelen, is afhankelijk van het hostbesturingssysteem. Daarnaast is de koppel locatie van de [hostcomputer](face-how-to-install-containers.md#the-host-computer)mogelijk niet toegankelijk als gevolg van een conflict tussen de machtigingen die worden gebruikt door het docker-service account en de machtigingen voor het koppelen van de host-locatie. 

|Optioneel| Name | Gegevenstype | Description |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | Bij face-containers worden deze niet gebruikt.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitvoer-koppelpunt. De standaardwaarde is `/output`. Dit is de locatie van de logboeken. Dit omvat container Logboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voorbeeld van de docker-opdrachten uitvoeren 

De volgende voorbeelden gebruiken de configuratie-instellingen om te laten zien hoe u om te schrijven en gebruik `docker run` opdrachten.  Zodra actief is, de container blijft actief totdat u [stoppen](face-how-to-install-containers.md#stop-the-container) deze.

* **Regel voortzettings teken**: De docker-opdrachten in de volgende secties gebruiken de back slash `\`,, als een regel voortzetting teken. Vervang of verwijder deze op basis van het hostbesturingssysteem vereisten. 
* **Argument volgorde**: Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Indeling of voorbeeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpunt sleutel van de `Face` resource op de pagina `Face` Azure-sleutels. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | De waarde van het facturerings eindpunt is beschikbaar `Face` op de pagina overzicht van Azure.| Zie [vereiste para meters](face-how-to-install-containers.md#gathering-required-parameters) voor expliciete voor beelden verzamelen. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula`, `Billing`, en `ApiKey` opties moeten worden opgegeven voor het uitvoeren van de container; anders wordt de container niet start.  Zie voor meer informatie, [facturering](face-how-to-install-containers.md#billing).
> De ApiKey-waarde is de **sleutel** van de `Cognitive Services` pagina Azure-resource sleutels. 

## <a name="face-container-docker-examples"></a>Voor beelden van Face container docker

De volgende docker-voor beelden zijn voor de container face. 

### <a name="basic-example"></a>Eenvoudige voorbeeld 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Voor beeld van logboek registratie 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Volgende stappen

* Beoordeling [over het installeren en uitvoeren van containers](face-how-to-install-containers.md)
