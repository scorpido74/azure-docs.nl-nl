---
title: 'Informatie over hoe de runtime beheert voor apparaten: Azure IoT Edge | Microsoft Docs'
description: Meer informatie over hoe de IoT Edge runtime modules, beveiliging, communicatie en rapportage beheert op uw apparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c184972789c412406f264f725f8b94e1f7f162ce
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284900"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>De Azure IoT Edge-runtime en de bijbehorende architectuur begrijpen

De IoT Edge runtime is een verzameling Program ma's die een apparaat in een IoT Edge apparaat omzetten. De IoT Edge-runtime-onderdelen maken het samen IoT Edge apparaten de mogelijkheid code te ontvangen die aan de rand worden uitgevoerd en de resultaten te communiceren.

De IoT Edge runtime is verantwoordelijk voor de volgende functies op IoT Edge apparaten:

* Workloads op het apparaat installeren en bijwerken.
* De Azure IoT Edge-beveiligingsstandaarden op het apparaat onderhouden.
* Zorg ervoor dat [IOT Edge modules](iot-edge-modules.md) altijd worden uitgevoerd.
* De status van de module aan de cloud rapporteren voor externe bewaking.
* Communicatie tussen downstream-apparaten en IoT Edge apparaten beheren.
* Communicatie tussen modules op het IoT Edge apparaat beheren.
* Communicatie tussen het IoT Edge apparaat en de Cloud beheren.

![Runtime communiceert inzichten en status van de module voor IoT-Hub](./media/iot-edge-runtime/Pipeline.png)

De verantwoordelijkheden van de IoT Edge-runtime kunnen worden onderverdeeld in twee categorieën: communicatie-en-module. Deze twee rollen worden uitgevoerd door twee onderdelen die deel uitmaken van de IoT Edge runtime. De *IOT Edge hub* is verantwoordelijk voor communicatie, terwijl de *IOT Edge agent* de modules implementeert en bewaakt.

Zowel de IoT Edge hub als de IoT Edge-agent zijn modules, net als elke andere module die wordt uitgevoerd op een IoT Edge apparaat. Ze worden soms ook wel de *runtime modules*genoemd.

## <a name="iot-edge-hub"></a>IoT Edge hub

De IoT Edge hub is een van de twee modules waaruit de Azure IoT Edge-runtime bestaat. Het fungeert als een lokale proxyserver voor IoT-Hub bij het blootstellen van de dezelfde protocoleindpunten als IoT-Hub. Deze consistentie betekent dat clients (of apparaten of modules) kan verbinding maken met de IoT Edge-runtime, net als met IoT Hub.

>[!NOTE]
> IoT Edge hub ondersteunt clients die verbinding maken met behulp van MQTT of AMQP. Clients die HTTP gebruiken, worden niet ondersteund.

De IoT Edge hub is geen volledige versie van IoT Hub die lokaal wordt uitgevoerd. IoT Edge hub delegeert sommige taken op de achtergrond aan IoT Hub. IoT Edge hub stuurt bijvoorbeeld verificatie aanvragen door naar IoT Hub wanneer een apparaat voor het eerst verbinding probeert te maken. Nadat de eerste verbinding tot stand is gebracht, worden de beveiligings gegevens lokaal opgeslagen in de cache van IoT Edge hub. Toekomstige verbindingen van dat apparaat zijn toegestaan zonder dat u zich opnieuw hoeft te verifiëren bij de Cloud.

Om de band breedte te verminderen die uw IoT Edge oplossing gebruikt, optimaliseert de IoT Edge hub het aantal werkelijke verbindingen dat in de Cloud wordt gemaakt. IoT Edge hub maakt logische verbindingen van modules of downstream-apparaten en combineert deze voor één fysieke verbinding met de Cloud. De details van dit proces zijn transparant voor de rest van de oplossing. U kunt clients zien dat ze hun eigen verbinding naar de cloud hebben, zelfs als ze al worden verzonden via dezelfde verbinding.

![IoT Edge hub is een gateway tussen fysieke apparaten en IoT Hub](./media/iot-edge-runtime/Gateway.png)

IoT Edge hub kunt bepalen of deze is verbonden met IoT Hub. Als de verbinding is verbroken, slaat IoT Edge hub berichten of dubbele updates lokaal op. Zodra een verbinding opnieuw tot stand is gebracht, worden deze gesynchroniseerd met alle gegevens. De locatie die wordt gebruikt voor deze tijdelijke cache wordt bepaald door een eigenschap van de module van de IoT Edge hub. De grootte van de cache wordt niet beperkt tot, en zullen groeien zolang het apparaat heeft opslagcapaciteit. Zie [offline mogelijkheden](offline-capabilities.md)voor meer informatie.

### <a name="module-communication"></a>Module-communicatie

IoT Edge hub vereenvoudigt module naar module communicatie. Als u IoT Edge hub als Message Broker gebruikt, blijven de modules onafhankelijk van elkaar. Modules hoeft alleen te geven van de invoer waarop ze berichten en de uitvoer waaraan ze berichten schrijven accepteren. Een oplossings ontwikkelaar kan deze invoer en uitvoer samen voegen, zodat de modules gegevens verwerken in de volg orde die specifiek is voor die oplossing.

![IoT Edge hub vereenvoudigt de communicatie van module-naar-module](./media/iot-edge-runtime/module-endpoints.png)

Als u gegevens naar de IoT Edge hub wilt verzenden, roept een module de SendEventAsync-methode aan. Het eerste argument geeft op welke uitvoer het bericht te verzenden. De volgende pseudocode verzendt een bericht op **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Registreren voor het ontvangen van een bericht, een callback die de berichten die binnenkomen op een specifieke invoer worden verwerkt. De volgende pseudocode registreert de functie messageProcessor die moet worden gebruikt voor het verwerken van alle berichten die op **input1**worden ontvangen:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Voor meer informatie over de ModuleClient-klasse en de communicatie methoden raadpleegt u de API-verwijzing voor uw voorkeurs [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)taal voor SDK:, [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)of [node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

De oplossings ontwikkelaar is verantwoordelijk voor het opgeven van de regels die bepalen hoe IoT Edge hub berichten tussen modules doorgeeft. Routerings regels worden gedefinieerd in de Cloud en naar IoT Edge hub gepusht in de module dubbele. Dezelfde syntaxis voor routes voor IoT Hub wordt gebruikt voor het definiëren van routes tussen modules in Azure IoT Edge. Zie [informatie over het implementeren van modules en het tot stand brengen van routes in IOT Edge](module-composition.md)voor meer informatie.

![Routes tussen modules worden IoT Edge hub door lopen](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge-agent

De IoT Edge-agent is de module die de Azure IoT Edge-runtime vormt. Het is verantwoordelijk voor het instantiëren van modules, ervoor te zorgen dat ze worden uitgevoerd en de status van de modules teruggestuurd rapportages naar IoT Hub. Deze configuratie gegevens worden geschreven als een eigenschap van de IoT Edge Agent module dubbele.

De [IOT Edge Security daemon](iot-edge-security-manager.md) start de IOT Edge-agent op het opstarten van het apparaat. De agent haalt de moduledubbel van IoT-Hub en inspecteert het manifest van de implementatie. Het manifest voor de implementatie is een JSON-bestand dat verklaart de modules die worden gestart.

Elk item in het implementatie manifest bevat specifieke informatie over een module en wordt gebruikt door de IoT Edge-agent voor het beheren van de levens cyclus van de module. Sommige van de interessanter eigenschappen zijn:

* **Settings. image** : de container installatie kopie die door de IOT Edge-agent wordt gebruikt om de module te starten. De IoT Edge-agent moet worden geconfigureerd met referenties voor het container register als de installatie kopie wordt beveiligd met een wacht woord. Referenties voor het container register kunnen extern worden geconfigureerd met behulp van het implementatie manifest of op het IoT Edge apparaat zelf door het `config.yaml`-bestand in de IoT Edge programmamap bij te werken.
* **Settings. createOptions** : een teken reeks die rechtstreeks wordt door gegeven aan de Moby-container-daemon bij het starten van de container van een module. Door de opties in deze eigenschap toe te voegen, kunt u geavanceerde configuraties configureren, zoals poort door sturing of het koppelen van volumes in de container van een module.  
* **status** : de status waarin de IOT Edge agent de module plaatst. Normaal gesp roken is deze waarde ingesteld op *actief* , zodat de IOT Edge agent direct alle modules op het apparaat moet starten. U kunt echter de begin status van een module opgeven die moet worden gestopt en u moet wachten op een toekomstige tijd om de IoT Edge agent te laten weten dat een module moet worden gestart. De IoT Edge agent rapporteert de status van elke module terug naar de cloud in de gerapporteerde eigenschappen. Een verschil tussen de gewenste eigenschap en de gerapporteerde eigenschap is een indicator van een metagegevenscaching apparaat. De ondersteunde statussen zijn:

  * Downloaden
  * In uitvoering
  * Niet in orde
  * Mislukt
  * Gestopt

* **restartPolicy** : de manier waarop de IOT Edge-agent een module opnieuw opstart. Mogelijke waarden:
  
  * `never`: de IoT Edge agent voert de module nooit opnieuw uit.
  * `on-failure`-als de module vastloopt, wordt deze door de IoT Edge-agent opnieuw opgestart. Als de module op een schone manier wordt afgesloten, wordt deze niet opnieuw opgestart door de IoT Edge-agent.
  * `on-unhealthy`-als de module vastloopt of als beschadigd wordt beschouwd, wordt deze door de IoT Edge-agent opnieuw opgestart.
  * `always`-als de module vastloopt, wordt als niet in orde beschouwd of op een wille keurige manier wordt afgesloten, wordt deze door de IoT Edge-agent opnieuw opgestart.

* **imagePullPolicy** : bepaalt of de IOT Edge-agent automatisch of niet de nieuwste installatie kopie probeert te halen voor een module. Als u geen waarde opgeeft, wordt de standaard *onCreate*. Mogelijke waarden:

  * `on-create`-wanneer u een module start of een module bijwerkt op basis van een nieuw implementatie manifest, probeert de IoT Edge agent de module installatie kopie uit het container register te halen.
  * `never`: de IoT Edge agent probeert nooit de module installatie kopie uit het container register te halen. Met deze configuratie bent u verantwoordelijk voor het ophalen van de module installatie kopie op het apparaat en het beheren van installatie kopie-updates.

De IoT Edge-agent verzendt runtimereactie naar IoT Hub. Hier volgt een lijst van mogelijke reacties:
  
* 200 - OK
* 400 - de implementatieconfiguratie is onjuist gevormd of ongeldig.
* 417-er is geen implementatie configuratie ingesteld op het apparaat.
* 412 - de schemaversie in de implementatieconfiguratie is ongeldig.
* 406-het IoT Edge apparaat is offline of stuurt geen status rapporten.
* 500-er is een fout opgetreden in de IoT Edge-runtime.

Zie [informatie over het implementeren van modules en het tot stand brengen van routes in IOT Edge](module-composition.md)voor meer informatie.

### <a name="security"></a>Beveiliging

De IoT Edge agent speelt een cruciale rol in de beveiliging van een IoT Edge-apparaat. Bijvoorbeeld, worden er acties, zoals de installatiekopie van een module controleren voordat u begint met het uitgevoerd.

Meer informatie over het Azure IoT Edge Security Framework vindt u in de [IOT Edge Security Manager](iot-edge-security-manager.md).

## <a name="next-steps"></a>Volgende stappen

[Azure IoT Edge-modules begrijpen](iot-edge-modules.md)
