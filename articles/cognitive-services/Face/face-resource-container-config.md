---
title: Containers configureren - Gezicht
titleSuffix: Azure Cognitive Services
description: De runtime-omgeving van de `docker run` Face-container is geconfigureerd met de opdrachtargumenten. Er zijn zowel de vereiste als de optionele instellingen.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2f608843e27b79d02697df8e2a7f2aba6695e10a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878422"
---
# <a name="configure-face-docker-containers"></a>Face Docker-containers configureren

De runtime-omgeving van de `docker run` **Face-container** is geconfigureerd met de opdrachtargumenten. Deze container heeft verschillende vereiste instellingen, samen met een paar optionele instellingen. Er zijn verschillende [voorbeelden](#example-docker-run-commands) van de opdracht beschikbaar. De containerspecifieke instellingen zijn de factureringsinstellingen. 

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) , en instellingen worden samen gebruikt, en u moet geldige waarden voor alle drie van hen; anders start uw container niet. Zie Facturering voor meer informatie over het gebruik van deze configuratie-instellingen om een container te [instantiëren.](face-how-to-install-containers.md#billing)

## <a name="apikey-configuration-setting"></a>ApiKey-configuratie-instelling

De `ApiKey` instelling geeft de Azure-bronsleutel op die wordt gebruikt om factureringsgegevens voor de container bij te houden. U moet een waarde opgeven voor de ApiKey en de waarde moet [`Billing`](#billing-configuration-setting) een geldige sleutel zijn voor de resource Cognitive _Services_ die is opgegeven voor de configuratie-instelling.

Deze instelling is te vinden op de volgende plaats:

* Azure-portal: **Resourcebeheer van cognitive services,** onder **toetsen**

## <a name="applicationinsights-setting"></a>Instelling ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Instelling voor factureringsconfiguratie

De `Billing` instelling geeft het eindpunt URI op van de _resource Cognitive Services_ op Azure die wordt gebruikt om factureringsgegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldig eindpunt URI zijn voor een _resource cognitive services_ op Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling is te vinden op de volgende plaats:

* Azure-portal: overzicht **van cognitieve services,** gelabeld`Endpoint`

Vergeet niet _Face_ om de Face-routering toe te voegen aan het eindpunt URI zoals in het voorbeeld wordt weergegeven. 

|Vereist| Name | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Ja| `Billing` | Tekenreeks | Factureringseindpunt URI. Zie het verzamelen van [vereiste parameters](face-how-to-install-containers.md#gathering-required-parameters)voor meer informatie over het verkrijgen van de factureringuri. Zie [Aangepaste subdomeinnamen voor cognitieve services voor](../cognitive-services-custom-subdomains.md)meer informatie en een volledige lijst met regionale eindpunten. |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>CloudAI-configuratie-instellingen

De configuratie-instellingen `CloudAI` in de sectie bieden containerspecifieke opties die uniek zijn voor uw container. De volgende instellingen en objecten worden ondersteund `CloudAI` voor de gezichtscontainer in de sectie

| Name | Gegevenstype | Beschrijving |
|------|-----------|-------------|
| `Storage` | Object | Het opslagscenario dat wordt gebruikt door de Face-container. Zie Scenario-instellingen voor `Storage` [opslagscenario's](#storage-scenario-settings) voor meer informatie over opslagscenario's en bijbehorende instellingen voor het object |

### <a name="storage-scenario-settings"></a>Instellingen voor opslagscenario's

De Face-container slaat blob-, cache-, metagegevens- en wachtrijgegevens op, afhankelijk van wat er wordt opgeslagen. Trainingsindexen en resultaten voor een groep grote personen worden bijvoorbeeld opgeslagen als blobgegevens. De Face-container biedt twee verschillende opslagscenario's bij interactie met en opslaan van dit soort gegevens:

* Geheugen  
  Alle vier de soorten gegevens worden opgeslagen in het geheugen. Ze zijn niet verspreid, noch zijn ze hardnekkig. Als de Face-container wordt gestopt of verwijderd, worden alle gegevens die voor die container zijn opgeslagen, vernietigd.  
  Dit is het standaardopslagscenario voor de Face-container.
* Azure  
  De Face-container maakt gebruik van Azure Storage en Azure Cosmos DB om deze vier soorten gegevens te distribueren naar permanente opslag. Blob- en wachtrijgegevens worden verwerkt door Azure Storage. Metagegevens en cachegegevens worden verwerkt door Azure Cosmos DB. Als de Face-container wordt gestopt of verwijderd, blijven alle gegevens die in de opslag voor die container zijn opgeslagen in Azure Storage en Azure Cosmos DB.  
  De resources die worden gebruikt door het Azure-opslagscenario hebben de volgende aanvullende vereisten
  * De Azure Storage-bron moet de storageV2-accountsoort gebruiken
  * De Azure Cosmos DB-bron moet de API van Azure Cosmos DB voor MongoDB gebruiken

De opslagscenario's en bijbehorende `Storage` configuratie-instellingen `CloudAI` worden beheerd door het object, onder de configuratiesectie. De volgende configuratie-instellingen `Storage` zijn beschikbaar in het object:

| Name | Gegevenstype | Beschrijving |
|------|-----------|-------------|
| `StorageScenario` | Tekenreeks | Het opslagscenario dat door de container wordt ondersteund. De volgende waarden zijn beschikbaar<br/>`Memory`- Standaardwaarde. Container maakt gebruik van niet-persistente, niet-gedistribueerde en in-memory opslag, voor single-node, tijdelijk gebruik. Als de container wordt gestopt of verwijderd, wordt de opslag voor die container vernietigd.<br/>`Azure`- Container gebruikt Azure-resources voor opslag. Als de container wordt gestopt of verwijderd, blijft de opslag voor die container bestaan.|
| `ConnectionStringOfAzureStorage` | Tekenreeks | De verbindingstekenreeks voor de Azure Storage-bron die door de container wordt gebruikt.<br/>Deze instelling is `Azure` alleen van `StorageScenario` toepassing als deze is opgegeven voor de configuratie-instelling. |
| `ConnectionStringOfCosmosMongo` | Tekenreeks | De MongoDB-verbindingstekenreeks voor de Azure Cosmos DB-bron die door de container wordt gebruikt.<br/>Deze instelling is `Azure` alleen van `StorageScenario` toepassing als deze is opgegeven voor de configuratie-instelling. |

Met de volgende opdracht wordt bijvoorbeeld het Azure-opslagscenario opgegeven en worden voorbeeldverbindingstekenreeksen opgegeven voor de Azure Storage- en Cosmos DB-resources die worden gebruikt om gegevens voor de Face-container op te slaan.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Het opslagscenario wordt afzonderlijk behandeld van invoerbevestigingen en uitvoerbevestigingen. U een combinatie van deze functies opgeven voor één container. Met de volgende opdracht definieert u bijvoorbeeld `D:\Output` een Docker-bindingsbevestiging aan de map op de hostmachine als de uitvoerbevestiging, waarna een container uit de afbeelding van de Face-container wordt geinstantt, waarbij logboekbestanden in de JSON-indeling worden opgeslagen in de uitvoerberg. De opdracht geeft ook het Azure-opslagscenario op en biedt voorbeeldverbindingstekenreeksen voor de Azure Storage- en Cosmos DB-resources die worden gebruikt om gegevens op te slaan voor de Face-container.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Eula-instelling

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Vloeiende instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Instellingen voor Http-proxyreferenties

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Montage-instellingen

Gebruik bindingsbevestigingen om gegevens van en naar de container te lezen en te schrijven. U een invoermount of uitvoerbevestiging `--mount` opgeven door de optie op te geven in de opdracht [Docker run.](https://docs.docker.com/engine/reference/commandline/run/)

De Face-containers gebruiken geen invoer- of uitvoerbevestigingen om trainings- of servicegegevens op te slaan. 

De exacte syntaxis van de hostmountlocatie is afhankelijk van het hostbesturingssysteem. Bovendien is de locatie van de [hostcomputer](face-how-to-install-containers.md#the-host-computer)mogelijk niet toegankelijk vanwege een conflict tussen machtigingen die worden gebruikt door het Docker-serviceaccount en de locatiemachtigingen voor de hostmount. 

|Optioneel| Name | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Niet toegestaan| `Input` | Tekenreeks | Gezichtscontainers gebruiken dit niet.|
|Optioneel| `Output` | Tekenreeks | Het doel van de uitgangsberg. De standaardwaarde is `/output`. Dit is de locatie van de logs. Dit geldt ook voor containerlogboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voorbeeld van opdrachten voor docker-uitgevoerd 

In de volgende voorbeelden worden de configuratie-instellingen `docker run` gebruikt om te illustreren hoe u opdrachten schrijven en gebruiken.  Eenmaal uitgevoerd, de container blijft draaien totdat u [het stopt.](face-how-to-install-containers.md#stop-the-container)

* **Line-continuation teken**: De Docker commando's in `\`de volgende secties gebruiken de back slash, als een lijn voortzetting teken. Vervang of verwijder dit op basis van de vereisten van uw hostbesturingssysteem. 
* **Argumentvolgorde:** Wijzig de volgorde van de argumenten niet, tenzij u zeer vertrouwd bent met Docker-containers.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Opmaak of voorbeeld |
|-------------|-------|---|
| **{API_KEY}** | De eindpuntsleutel van `Face` de bron `Face` op de pagina Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | De waarde van het factureringseindpunt is beschikbaar op de pagina Azure-overzicht. `Face`| Zie [het verzamelen van vereiste parameters](face-how-to-install-containers.md#gathering-required-parameters) voor expliciete voorbeelden. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula` `Billing`opties `ApiKey` en de opties moeten worden opgegeven om de container uit te voeren; Anders start de container niet.  Zie [Facturering voor](face-how-to-install-containers.md#billing)meer informatie.
> De ApiKey-waarde **Key** is de `Cognitive Services` sleutel van de pagina Azure Resource-sleutels. 

## <a name="face-container-docker-examples"></a>Voorbeelden van Face container Docker

De volgende Voorbeelden van Docker zijn voor de gezichtscontainer. 

### <a name="basic-example"></a>Basisvoorbeeld 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Voorbeeld van logboekregistratie 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Volgende stappen

* Controleren [hoe u containers installeert en uitvoert](face-how-to-install-containers.md)
