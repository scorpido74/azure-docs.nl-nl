---
title: Eigenschappen van de agent en de hub-module apparaatdubbels-Azure IoT Edge
description: Controleer de specifieke eigenschappen en hun waarden voor de edgeAgent en edgeHub moduledubbels
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4684daf2a1095a40c478170be37edcae788868ef
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284835"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Eigenschappen van de IoT Edge agent en IoT Edge hub-module apparaatdubbels

De IoT Edge-agent en IoT Edge-hub zijn twee modules die de IoT Edge runtime vormen. Zie voor meer informatie over de verantwoordelijkheden van elke runtime module [inzicht in de Azure IOT Edge runtime en de bijbehorende architectuur](iot-edge-runtime.md).

Dit artikel bevat de gewenste eigenschappen en de gerapporteerde eigenschappen van de runtime-moduledubbels. Zie informatie over het [implementeren van modules en het tot stand brengen van routes in IOT Edge](module-composition.md)voor meer informatie over het implementeren van modules op IOT edge apparaten.

Een module met dubbele inhoud:

* **Gewenste eigenschappen**. De back-end van de oplossing kan gewenste eigenschappen instellen en de module kan deze lezen. De module kan ook meldingen ontvangen over wijzigingen in de gewenste eigenschappen. Gewenste eigenschappen worden samen met gerapporteerde eigenschappen gebruikt voor het synchroniseren van module configuratie of-voor waarden.

* **Gerapporteerde eigenschappen**. De module kan gerapporteerde eigenschappen instellen en de back-end van de oplossing kan deze lezen en er query's op uitvoeren. Gerapporteerde eigenschappen worden samen met de gewenste eigenschappen gebruikt voor het synchroniseren van module configuratie of-voor waarden.

## <a name="edgeagent-desired-properties"></a>EdgeAgent gewenste eigenschappen

De module voor de IoT Edge-agent heet `$edgeAgent` en coördineert de communicatie tussen de IoT Edge agent die op een apparaat wordt uitgevoerd en IoT Hub. De gewenste eigenschappen worden ingesteld bij het toepassen van een manifest van de implementatie op een specifiek apparaat als onderdeel van de implementatie van een één-apparaat of op schaal.

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| schemaVersion | Moet "1.0" | Ja |
| runtime.type | Moet "docker" | Ja |
| runtime.settings.minDockerVersion | Ingesteld op de minimaal vereiste door deze implementatie manifest Docker-versie | Ja |
| runtime.settings.loggingOptions | Een stringified-JSON met de registratie opties voor de container van de IoT Edge agent. [Opties voor docker-logboek registratie](https://docs.docker.com/engine/admin/logging/overview/) | Nee |
| runtime.settings.registryCredentials<br>. {registryId} .username | De gebruikersnaam van het containerregister. De gebruikersnaam is voor Azure Container Registry, gewoonlijk de naam van het containerregister.<br><br> Register referenties zijn nodig voor installatie kopieën van een persoonlijke module. | Nee |
| runtime.settings.registryCredentials<br>. {registryId} .password | Het wachtwoord voor het containerregister. | Nee |
| runtime.settings.registryCredentials<br>. {registryId} .address | Het adres van het containerregister. Voor Azure Container Registry is het adres doorgaans *{Registry name}. azurecr. io*. | Nee |  
| systemModules.edgeAgent.type | Moet "docker" | Ja |
| systemModules.edgeAgent.settings.image | De URI van de afbeelding van de IoT Edge agent. De IoT Edge agent kan momenteel niet worden bijgewerkt. | Ja |
| systemModules.edgeAgent.settings<br>.createOptions | Een stringified-JSON met de opties voor het maken van de IoT Edge agent-container. [Opties voor het maken van docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nee |
| systemModules.edgeAgent.configuration.id | De ID van de implementatie die deze module wordt geïmplementeerd. | IoT Hub stelt deze eigenschap in wanneer het manifest wordt toegepast met behulp van een implementatie. Geen deel uitmaakt van een manifest van de implementatie. |
| systemModules.edgeHub.type | Moet "docker" | Ja |
| systemModules.edgeHub.status | Moet worden 'running' | Ja |
| systemModules.edgeHub.restartPolicy | 'Altijd' moet | Ja |
| systemModules.edgeHub.settings.image | De URI van de afbeelding van de IoT Edge hub. | Ja |
| systemModules.edgeHub.settings<br>.createOptions | Een stringified-JSON met de opties voor het maken van de IoT Edge hub-container. [Opties voor het maken van docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nee |
| systemModules.edgeHub.configuration.id | De ID van de implementatie die deze module wordt geïmplementeerd. | IoT Hub stelt deze eigenschap in wanneer het manifest wordt toegepast met behulp van een implementatie. Geen deel uitmaakt van een manifest van de implementatie. |
| modules. {moduleId} .version | Een gebruiker gedefinieerde tekenreeks voor de versie van deze module. | Ja |
| modules. {moduleId} .type | Moet "docker" | Ja |
| modules. {moduleId} .status | {"uitvoeren" \| "gestopt"} | Ja |
| modules. {moduleId} .restartPolicy | {' Never ' \| ' on-failure ' \| ' on-outstatus ' \| ' Always '} | Ja |
| modules. {moduleId}. imagePullPolicy | {' on-Create ' \| ' Never '} | Nee |
| modules.{moduleId}.settings.image | De URI naar de installatiekopie van de module. | Ja |
| modules. {moduleId}.settings.createOptions | Een stringified JSON met de opties voor het maken van de module-container. [Opties voor het maken van docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nee |
| modules. {moduleId}.configuration.id | De ID van de implementatie die deze module wordt geïmplementeerd. | IoT Hub stelt deze eigenschap in wanneer het manifest wordt toegepast met behulp van een implementatie. Geen deel uitmaakt van een manifest van de implementatie. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent gerapporteerde eigenschappen

De gerapporteerde eigenschappen van de IoT Edge agent bevatten drie belang rijke informatie:

1. De status van de toepassing van de laatst gezien gewenste eigenschappen;
2. De status van de modules die momenteel worden uitgevoerd op het apparaat, zoals gerapporteerd door de IoT Edge-agent; maar
3. Een kopie van de gewenste eigenschappen die momenteel wordt uitgevoerd op het apparaat.

De kopie van de huidige gewenste eigenschappen is handig om te weten of het apparaat de meest recente implementatie heeft toegepast of nog steeds een eerder implementatie manifest uitvoert.

> [!NOTE]
> De gerapporteerde eigenschappen van de IoT Edge-agent zijn nuttig, omdat er query's kunnen worden uitgevoerd met de [IOT hub query taal](../iot-hub/iot-hub-devguide-query-language.md) om de status van implementaties op schaal te onderzoeken. Zie voor meer informatie over het gebruik van de IoT Edge agent-eigenschappen voor de status [IOT Edge implementaties voor één apparaat of op schaal begrijpen](module-deployment-monitoring.md).

De volgende tabel bevat geen informatie die is opgehaald uit de gewenste eigenschappen.

| Eigenschap | Beschrijving |
| -------- | ----------- |
| lastDesiredVersion | Dit geheel getal verwijst naar de laatste versie van de gewenste eigenschappen die worden verwerkt door de IoT Edge agent. |
| lastDesiredStatus.code | Deze status code verwijst naar de laatste gewenste eigenschappen die worden weer gegeven door de IoT Edge agent. Toegestane waarden: `200` geslaagd, `400` ongeldige configuratie, `412` ongeldige schema versie, `417` de gewenste eigenschappen leeg zijn, `500` mislukt |
| lastDesiredStatus.description | Beschrijving van de status |
| de apparaatstatus | `healthy` als de runtime status van alle modules `running` of `stopped`is, `unhealthy` anders |
| configurationHealth. .health {deploymentId} | `healthy` als de runtime status van alle modules die zijn ingesteld door de implementatie {deploymentId} `running` of `stopped`is, `unhealthy` anders |
| runtime.platform.OS | Rapportage van het besturingssysteem op het apparaat |
| runtime.platform.architecture | Rapportage van de architectuur van de CPU op het apparaat |
| systemModules.edgeAgent.runtimeStatus | De gerapporteerde status van IoT Edge agent: {' running ' \| ' beschadigd '} |
| systemModules.edgeAgent.statusDescription | De tekst beschrijving van de gerapporteerde status van de IoT Edge agent. |
| systemModules.edgeHub.runtimeStatus | Status van IoT Edge hub: {"uitvoeren" \| "\|" is gestopt "\|" uitstel "\|" beschadigde "} |
| systemModules.edgeHub.statusDescription | De tekst beschrijving van de status van IoT Edge hub als deze beschadigd is. |
| systemModules.edgeHub.exitCode | De afsluit code die wordt gerapporteerd door de IoT Edge hub-container als de container wordt afgesloten |
| systemModules.edgeHub.startTimeUtc | Tijdstip waarop IoT Edge hub voor het laatst is gestart |
| systemModules.edgeHub.lastExitTimeUtc | Tijdstip waarop IoT Edge hub voor het laatst is afgesloten |
| systemModules.edgeHub.lastRestartTimeUtc | Tijdstip waarop IoT Edge hub voor het laatst opnieuw is opgestart |
| systemModules.edgeHub.restartCount | Aantal keren dat die deze module opnieuw als onderdeel van het beleid voor opnieuw opstarten opgestart is. |
| modules.{moduleId}.runtimeStatus | Status van de module: {"uitvoeren" \| "gestopt" \| "Failed" \| "uitstel \|" is beschadigd "} |
| modules. {moduleId} .statusDescription | De tekst beschrijving van de status van de module indien beschadigd. |
| modules.{moduleId}.exitCode | De afsluit code die wordt gerapporteerd door de module container als de container wordt afgesloten |
| modules.{moduleId}.startTimeUtc | Tijdstip waarop de module voor het laatst is gestart |
| modules.{moduleId}.lastExitTimeUtc | Tijd wanneer de module laatst afgesloten |
| modules.{moduleId}.lastRestartTimeUtc | Tijd wanneer de module voor het laatst is gestart |
| modules. {moduleId} .restartCount | Aantal keren dat die deze module opnieuw als onderdeel van het beleid voor opnieuw opstarten opgestart is. |

## <a name="edgehub-desired-properties"></a>EdgeHub gewenste eigenschappen

De module voor de IoT Edge hub wordt `$edgeHub` genoemd en coördineert de communicatie tussen de IoT Edge hub die op een apparaat wordt uitgevoerd en IoT Hub. De gewenste eigenschappen worden ingesteld bij het toepassen van een manifest van de implementatie op een specifiek apparaat als onderdeel van de implementatie van een één-apparaat of op schaal.

| Eigenschap | Beschrijving | In het manifest van de implementatie vereist |
| -------- | ----------- | -------- |
| schemaVersion | Moet "1.0" | Ja |
| routes.{routeName} | Een teken reeks die een IoT Edge hub-route vertegenwoordigt. Zie voor meer informatie [routes declareren](module-composition.md#declare-routes). | Het `routes`-element kan aanwezig zijn, maar leeg zijn. |
| storeAndForwardConfiguration.timeToLiveSecs | De tijd in seconden dat IoT Edge hub berichten houdt als de verbinding met de routerings eindpunten is verbroken, of IoT Hub of een lokale module. De waarde kan elk positief geheel getal zijn. | Ja |

## <a name="edgehub-reported-properties"></a>EdgeHub gerapporteerde eigenschappen

| Eigenschap | Beschrijving |
| -------- | ----------- |
| lastDesiredVersion | Dit geheel getal verwijst naar de laatste versie van de gewenste eigenschappen die worden verwerkt door de IoT Edge hub. |
| lastDesiredStatus.code | De status code verwijst naar de laatste gewenste eigenschappen die worden weer gegeven door de IoT Edge hub. Toegestane waarden: `200` geslaagd, `400` ongeldige configuratie, `500` mislukt |
| lastDesiredStatus.description | De tekst beschrijving van de status. |
| -clients. {apparaat of moduleId} .status | De status van dit apparaat of de module. Mogelijke waarden {"Connected" \| "unconnected"}. Alleen module-id's kunnen zich in niet-verbonden status. Downstream-apparaten die verbinding maken met IoT Edge hub worden alleen weer gegeven wanneer ze zijn verbonden. |
| -clients. {apparaat of moduleId} .lastConnectTime | De laatste keer dat het apparaat of de module is verbonden. |
| -clients. {apparaat of moduleId} .lastDisconnectTime | De laatste keer dat het apparaat of de module is losgekoppeld. |

## <a name="next-steps"></a>Volgende stappen

Zie [begrijpen hoe IOT Edge modules kunnen worden gebruikt, geconfigureerd en opnieuw worden gebruikt](module-composition.md)voor meer informatie over het gebruik van deze eigenschappen voor het samen stellen van implementatie manifesten.
