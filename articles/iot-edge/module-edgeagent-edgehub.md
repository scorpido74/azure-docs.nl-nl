---
title: Eigenschappen van de tweeling van de agent- en hubmodule - Azure IoT Edge
description: Bekijk de specifieke eigenschappen en hun waarden voor de edgeAgent- en edgeHub-moduletweeling
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4684daf2a1095a40c478170be37edcae788868ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284835"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Eigenschappen van de IoT Edge-agent en IoT Edge-hubmoduletwins

De IoT Edge-agent en de IoT Edge-hub zijn twee modules die deel uitmaken van de IoT Edge-runtime. Zie De runtime van [Azure IoT Edge en de architectuur ervan begrijpen](iot-edge-runtime.md)voor meer informatie over de verantwoordelijkheden van elke runtime-module.

Dit artikel biedt de gewenste eigenschappen en gerapporteerde eigenschappen van de runtime module tweelingen. Zie Meer informatie over het implementeren van modules op IoT Edge-apparaten voor meer informatie over het implementeren van [modules en het instellen van routes in IoT Edge.](module-composition.md)

Een moduletwin omvat:

* **Gewenste eigenschappen.** De backend van de oplossing kan de gewenste eigenschappen instellen en de module kan ze lezen. De module kan ook meldingen ontvangen van wijzigingen in de gewenste eigenschappen. Gewenste eigenschappen worden gebruikt samen met gerapporteerde eigenschappen om de configuratie of voorwaarden van de module te synchroniseren.

* **Gerapporteerde eigenschappen**. De module kan gerapporteerde eigenschappen instellen en de backend van de oplossing kan deze lezen en opvragen. Gerapporteerde eigenschappen worden samen met de gewenste eigenschappen gebruikt om de configuratie of voorwaarden van de module te synchroniseren.

## <a name="edgeagent-desired-properties"></a>EdgeAgent gewenste eigenschappen

De moduletwee voor de IoT `$edgeAgent` Edge-agent wordt aangeroepen en coördineert de communicatie tussen de IoT Edge-agent die op een apparaat en IoT Hub wordt uitgevoerd. De gewenste eigenschappen worden ingesteld bij het toepassen van een implementatiemanifest op een specifiek apparaat als onderdeel van één apparaat of op schaal implementatie.

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| schemaVersie | Moet "1.0" zijn | Ja |
| runtime.type | Moet "docker" | Ja |
| runtime.settings.minDockerVersion | Instellen op de minimale Docker-versie die vereist is voor dit implementatiemanifest | Ja |
| runtime.settings.loggingOptions runtime.settings.loggingOptions runtime.settings.loggingOptions runtime | Een gestringificeerde JSON met de logboekopties voor de IoT Edge-agentcontainer. [Docker-logboekopties](https://docs.docker.com/engine/admin/logging/overview/) | Nee |
| runtime.settings.registryCredentials<br>. {registryId}.gebruikersnaam | De gebruikersnaam van het containerregister. Voor Azure Container Registry is de gebruikersnaam meestal de registernaam.<br><br> Registerreferenties zijn nodig voor alle privémoduleafbeeldingen. | Nee |
| runtime.settings.registryCredentials<br>. {registryId}.password {registryId}.password {registryId}.password {registry | Het wachtwoord voor het containerregister. | Nee |
| runtime.settings.registryCredentials<br>. {registryId}.adres | Het adres van het containerregister. Voor Azure Container Registry is het adres meestal *{registername}.azurecr.io*. | Nee |  
| systemModules.edgeAgent.type | Moet "docker" | Ja |
| systemModules.edgeAgent.settings.image | De URI van de afbeelding van de IoT Edge-agent. Momenteel kan de IoT Edge-agent zichzelf niet bijwerken. | Ja |
| systemModules.edgeAgent.settings<br>.createOptions | Een gestringificeerde JSON met de opties voor het maken van de IoT Edge-agentcontainer. [Docker maken opties](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nee |
| systemModules.edgeAgent.configuration.id | De id van de implementatie die deze module heeft geïmplementeerd. | IoT Hub stelt deze eigenschap in wanneer het manifest wordt toegepast met behulp van een implementatie. Maakt geen deel uit van een implementatiemanifest. |
| systemModules.edgeHub.type | Moet "docker" | Ja |
| systemModules.edgeHub.status | Moet worden "running" | Ja |
| systemModules.edgeHub.restartBeleid | Moet "altijd" | Ja |
| systemModules.edgeHub.settings.image | De URI van de afbeelding van de IoT Edge-hub. | Ja |
| systemModules.edgeHub.settings<br>.createOptions | Een gestringificeerde JSON met de opties voor het maken van de IoT Edge-hubcontainer. [Docker maken opties](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nee |
| systemModules.edgeHub.configuration.id | De id van de implementatie die deze module heeft geïmplementeerd. | IoT Hub stelt deze eigenschap in wanneer het manifest wordt toegepast met behulp van een implementatie. Maakt geen deel uit van een implementatiemanifest. |
| Modules. {moduleId}.versie | Een door de gebruiker gedefinieerde tekenreeks die de versie van deze module weergeeft. | Ja |
| Modules. {moduleId}.type | Moet "docker" | Ja |
| Modules. {moduleId}.status | {"running" \| "gestopt"} | Ja |
| Modules. {moduleId}.opnieuw opstartenBeleid | {"nooit" \| "on-failure" \| "on-ongezond" \| "altijd"} | Ja |
| Modules. {moduleId}.imagePullPolicy {moduleId}.imagePullPolicy {moduleId}.imagePullPolicy {module | {"on-create" \| "nooit"} | Nee |
| Modules. {moduleId}.settings.image {moduleId}.settings.image {moduleId}.settings.image {module | De URI naar de moduleafbeelding. | Ja |
| Modules. {moduleId}.settings.createOptions {moduleId}.settings.createOptions {moduleId}.settings.createOptions {module | Een gestringificeerde JSON met de opties voor het maken van de modulecontainer. [Docker maken opties](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nee |
| Modules. {moduleId}.configuration.id | De id van de implementatie die deze module heeft geïmplementeerd. | IoT Hub stelt deze eigenschap in wanneer het manifest wordt toegepast met behulp van een implementatie. Maakt geen deel uit van een implementatiemanifest. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent gerapporteerde eigenschappen

De gemelde eigenschappen van de IoT Edge-agent bevatten drie belangrijke informatiestukken:

1. De status van de toepassing van de laatst geziengewenste eigenschappen;
2. De status van de modules die momenteel op het apparaat worden uitgevoerd, zoals gerapporteerd door de IoT Edge-agent; En
3. Een kopie van de gewenste eigenschappen die momenteel op het apparaat worden uitgevoerd.

De kopie van de huidige gewenste eigenschappen is handig om te bepalen of het apparaat de nieuwste implementatie heeft toegepast of nog steeds een eerder implementatiemanifest uitvoert.

> [!NOTE]
> De gerapporteerde eigenschappen van de IoT Edge-agent zijn handig omdat ze kunnen worden opgevraagd met de [IoT Hub-querytaal](../iot-hub/iot-hub-devguide-query-language.md) om de status van implementaties op schaal te onderzoeken. Zie [IoT Edge-implementaties](module-deployment-monitoring.md)voor afzonderlijke apparaten of op schaal begrijpen voor meer informatie over het gebruik van de eigenschappen van de IoT Edge-agent voor status.

De volgende tabel bevat niet de informatie die wordt gekopieerd van de gewenste eigenschappen.

| Eigenschap | Beschrijving |
| -------- | ----------- |
| lastDesiredVersion | Dit geheel verwijst naar de laatste versie van de gewenste eigenschappen die door de IoT Edge-agent zijn verwerkt. |
| lastDesiredStatus.code | Deze statuscode verwijst naar de laatst gewenste eigenschappen die door de IoT Edge-agent worden gezien. Toegestane waarden: `200` `400` Succes, Ongeldige `412` configuratie, `417` Ongeldige schemaversie, de gewenste eigenschappen zijn leeg, `500` Mislukt |
| lastDesiredStatus.description | Tekstbeschrijving van de status |
| deviceHealth | `healthy`als de runtime-status van `running` `stopped`alle `unhealthy` modules een of , anderszins |
| configuratieHealth. {deploymentId}.health | `healthy`als de runtime-status van alle modules die door `running` `stopped`de `unhealthy` implementatie {deploymentId} is ingesteld, |
| runtime.platform.OS | Het besturingssysteem rapporteren dat op het apparaat wordt uitgevoerd |
| runtime.platform.architectuur | Rapportage van de architectuur van de CPU op het apparaat |
| systemModules.edgeAgent.runtimeStatus | De gerapporteerde status van IoT Edge-agent: {"running" \| "ongezond"} |
| systemModules.edgeAgent.statusBeschrijving | Tekstbeschrijving van de gerapporteerde status van de IoT Edge-agent. |
| systemModules.edgeHub.runtimeStatus | Status van IoT Edge hub: \| { \| "running" \| "gestopt" \| "mislukt" "backoff" "ongezond" } |
| systemModules.edgeHub.statusBeschrijving | Tekstbeschrijving van de status van de IoT Edge-hub als deze niet in orde is. |
| systemModules.edgeHub.exitCode | De exitcode die wordt gerapporteerd door de IoT Edge-hubcontainer als de container wordt afgesloten |
| systemModules.edgeHub.startTimeUtc | Tijd waarop de IoT Edge-hub voor het laatst is gestart |
| systemModules.edgeHub.lastExitTimeUtc | Tijd waarop de IoT Edge-hub voor het laatst is afgesloten |
| systemModules.edgeHub.lastRestartTimeUtc | Tijd waarop de IoT Edge-hub voor het laatst opnieuw is opgestart |
| systemModules.edgeHub.restartCount | Het aantal keren dat deze module opnieuw is gestart als onderdeel van het herstartbeleid. |
| Modules. {moduleId}.runtimeStatus | Status van de module: \| { "running" \| \| "gestopt" \| "mislukt" "backoff" "ongezond" } |
| Modules. {moduleId}.statusBeschrijving | Tekstbeschrijving van de status van de module als deze niet in orde is. |
| Modules. {moduleId}.exitCode | De exitcode die door de modulecontainer wordt gerapporteerd als de container wordt afgesloten |
| Modules. {moduleId}.startTimeUtc | Tijd waarop de module voor het laatst is gestart |
| Modules. {moduleId}.lastExitTimeUtc | Tijd waarop de module voor het laatst is afgesloten |
| Modules. {moduleId}.lastRestartTimeUtc {moduleId}.lastRestartTimeUtc {moduleId}.lastRestartTimeUtc {module | Tijd waarop de module voor het laatst opnieuw is opgestart |
| Modules. {moduleId}.opnieuw opstartenAantal | Het aantal keren dat deze module opnieuw is gestart als onderdeel van het herstartbeleid. |

## <a name="edgehub-desired-properties"></a>EdgeHub gewenste eigenschappen

De moduletwee voor de IoT `$edgeHub` Edge-hub wordt aangeroepen en coördineert de communicatie tussen de IoT Edge-hub die op een apparaat en IoT Hub wordt uitgevoerd. De gewenste eigenschappen worden ingesteld bij het toepassen van een implementatiemanifest op een specifiek apparaat als onderdeel van één apparaat of op schaal implementatie.

| Eigenschap | Beschrijving | Vereist in het implementatiemanifest |
| -------- | ----------- | -------- |
| schemaVersie | Moet "1.0" zijn | Ja |
| Routes. {routeName} | Een tekenreeks die een IoT Edge-hubroute vertegenwoordigt. Zie [Routes declareren voor](module-composition.md#declare-routes)meer informatie . | Het `routes` element kan aanwezig zijn, maar leeg. |
| storeAndForwardConfiguration.timeToLiveSecs | De tijd in seconden die IoT Edge-hub houdt berichten bij als ze geen verbinding hebben met routeringseindpunten, of het nu Gaat om IoT Hub of een lokale module. De waarde kan een positief geheel getal zijn. | Ja |

## <a name="edgehub-reported-properties"></a>EdgeHub gerapporteerde eigenschappen

| Eigenschap | Beschrijving |
| -------- | ----------- |
| lastDesiredVersion | Dit geheel verwijst naar de laatste versie van de gewenste eigenschappen die door de IoT Edge-hub worden verwerkt. |
| lastDesiredStatus.code | De statuscode die verwijst naar de laatst gewenste eigenschappen die worden gezien door de IoT Edge-hub. Toegestane waarden: `200` Succes, `400` Ongeldige configuratie, `500` Mislukt |
| lastDesiredStatus.description | Tekstbeschrijving van de status. |
| Clients. {apparaat of moduleId}.status | De verbindingsstatus van dit apparaat of module. Mogelijke waarden {"connected" \| "disconnected"}. Alleen moduleidentiteiten kunnen in de verbroken staat zijn. Downstream-apparaten die verbinding maken met de IoT Edge-hub, worden alleen weergegeven wanneer deze zijn aangesloten. |
| Clients. {apparaat of moduleId}.lastConnectTime | Laatste keer dat het apparaat of de module is aangesloten. |
| Clients. {apparaat of moduleId}.lastDisconnectTime | Laatste keer dat het apparaat of de module is losgekoppeld. |

## <a name="next-steps"></a>Volgende stappen

Zie [Begrijpen hoe IoT Edge-modules kunnen worden gebruikt, geconfigureerd en hergebruikt](module-composition.md)voor meer informatie over het gebruik van deze eigenschappen om implementatiemanifesten uit te bouwen.
