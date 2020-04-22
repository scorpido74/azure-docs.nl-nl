---
title: Meer informatie over hoe de runtime apparaten beheert - Azure IoT Edge | Microsoft Documenten
description: Ontdek hoe de IoT Edge-runtime modules, beveiliging, communicatie en rapportage op uw apparaten beheert
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ef31bd74c73aa081c32031b71392f69a1ca14f75
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730912"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Inzicht in de runtime en de architectuur van Azure IoT Edge

De IoT Edge-runtime is een verzameling programma's die ervoor zorgt dat u een apparaat als een IoT Edge-apparaat kunt gebruiken. Gezamenlijk stellen de IoT Edge runtime-componenten IoT Edge-apparaten in staat om code te ontvangen om aan de rand te worden uitgevoerd en de resultaten te communiceren.

De IoT Edge-runtime is verantwoordelijk voor de volgende functies op IoT Edge-apparaten:

* Workloads op het apparaat installeren en bijwerken.
* De Azure IoT Edge-beveiligingsstandaarden op het apparaat onderhouden.
* Zorg ervoor dat [IoT Edge-modules](iot-edge-modules.md) altijd worden uitgevoerd.
* De status van de module aan de cloud rapporteren voor externe bewaking.
* Beheer de communicatie tussen downstream-apparaten en IoT Edge-apparaten.
* Beheer de communicatie tussen modules op het IoT Edge-apparaat.
* Beheer de communicatie tussen het IoT Edge-apparaat en de cloud.

![Runtime communiceert inzichten en modulestatus naar IoT Hub](./media/iot-edge-runtime/Pipeline.png)

De verantwoordelijkheden van de IoT Edge runtime vallen in twee categorieën: communicatie en modulemanagement. Deze twee rollen worden uitgevoerd door twee componenten die deel uitmaken van de IoT Edge-runtime.De *IoT Edge-hub* is verantwoordelijk voor de communicatie, terwijl de *IoT Edge-agent* de modules implementeert en bewaakt.

Zowel de IoT Edge-hub als de IoT Edge-agent zijn modules, net als elke andere module die op een IoT Edge-apparaat wordt uitgevoerd. Ze worden soms aangeduid als de *runtime modules*.

## <a name="iot-edge-hub"></a>IoT Edge-hub

De IoT Edge-hub is een van de twee modules die deel uitmaken van de Azure IoT Edge-runtime. Het fungeert als een lokale proxy voor IoT Hub door dezelfde protocoleindpunten bloot te leggen als IoT Hub. Deze consistentie betekent dat clients (of het nu apparaten of modules zijn) verbinding kunnen maken met de IoT Edge-runtime, net zoals ze dat zouden doen met IoT Hub.

>[!NOTE]
> IoT Edge-hub ondersteunt clients die verbinding maken via MQTT of AMQP. Het ondersteunt geen clients die HTTP gebruiken.

De IoT Edge-hub is geen volledige versie van IoT Hub die lokaal wordt uitgevoerd. IoT Edge-hub delegeert in stilte sommige taken naar IoT Hub. De IoT Edge-hub stuurt bijvoorbeeld verificatieaanvragen door naar IoT Hub wanneer een apparaat voor het eerst verbinding probeert te maken. Nadat de eerste verbinding tot stand is gebracht, wordt beveiligingsinformatie lokaal opgeslagen door de IoT Edge-hub. Toekomstige verbindingen vanaf dat apparaat zijn toegestaan zonder dat u zich opnieuw hoeft te verifiëren naar de cloud.

Om de bandbreedte te verminderen die uw IoT Edge-oplossing gebruikt, optimaliseert de IoT Edge-hub het aantal werkelijke verbindingen met de cloud. IoT Edge-hub maakt logische verbindingen van modules of downstream-apparaten en combineert deze voor één fysieke verbinding met de cloud. De details van dit proces zijn transparant voor de rest van de oplossing. Klanten denken dat ze hun eigen verbinding met de cloud hebben, ook al worden ze allemaal via dezelfde verbinding verzonden.

![IoT Edge-hub is een gateway tussen fysieke apparaten en IoT Hub](./media/iot-edge-runtime/Gateway.png)

De IoT Edge-hub kan bepalen of deze is verbonden met IoT Hub. Als de verbinding verloren gaat, slaat de IoT Edge-hub berichten of twee updates lokaal op. Zodra een verbinding is hersteld, synchroniseert het alle gegevens. De locatie die wordt gebruikt voor deze tijdelijke cache wordt bepaald door een eigenschap van de moduletwin van de IoT Edge-hub. De grootte van de cache is niet afgetopt en zal groeien zolang het apparaat opslagcapaciteit heeft.Zie [Offlinemogelijkheden](offline-capabilities.md)voor meer informatie.

### <a name="module-communication"></a>Modulecommunicatie

IoT Edge hub faciliteert module-communicatie met module. Het gebruik van IoT Edge hub als een berichtenmakelaar houdt modules onafhankelijk van elkaar. Modules hoeven alleen de ingangen op te geven waarop ze berichten accepteren en de uitgangen waarop ze berichten schrijven. Een ontwikkelaar van oplossingen kan deze ingangen en uitgangen aan elkaar naaien, zodat de modules gegevens verwerken in de volgorde die specifiek is voor die oplossing.

![IoT Edge Hub faciliteert module-naar-module communicatie](./media/iot-edge-runtime/module-endpoints.png)

Als u gegevens naar de IoT Edge-hub wilt verzenden, roept een module de sendeventasync-methode aan. Het eerste argument geeft aan op welke uitvoer het bericht moet worden verzonden. De volgende pseudocode stuurt een bericht op **output1:**

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Als u een bericht wilt ontvangen, registreert u een terugroep die berichten verwerkt die binnenkomen op een specifieke invoer. De volgende pseudocode registreert de functie messageProcessor te gebruiken voor de verwerking van alle berichten ontvangen op **input1:**

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Zie de API-verwijzing naar de gewenste SDK-taal [c#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C,](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h) [Python,](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python) [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)of [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)voor meer informatie over de klasse ModuleClient en de bijbehorende communicatiemethoden.

De ontwikkelaar van de oplossing is verantwoordelijk voor het opgeven van de regels die bepalen hoe IoT Edge-hub berichten tussen modules doorgeeft. Routeringsregels worden gedefinieerd in de cloud en naar de IoT Edge-hub in de moduletwee geduwd. Dezelfde syntaxis voor IoT Hub-routes wordt gebruikt om routes tussen modules in Azure IoT Edge te definiëren. Zie [Meer informatie over het implementeren van modules en het instellen van routes in IoT Edge](module-composition.md)voor meer informatie.

![Routes tussen modules gaan via IoT Edge-hub](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge-agent

De IoT Edge-agent is de andere module die deel uitmaakt van de Azure IoT Edge-runtime. Het is verantwoordelijk voor het instantiëren van modules, ervoor te zorgen dat ze blijven draaien en het rapporteren van de status van de modules terug naar IoT Hub. Deze configuratiegegevens worden geschreven als een eigenschap van de IoT Edge-agentmoduletwee.

De [IoT Edge security daemon](iot-edge-security-manager.md) start de IoT Edge-agent bij het opstarten van het apparaat. De agent haalt de moduletwin uit IoT Hub en inspecteert het implementatiemanifest. Het implementatiemanifest is een JSON-bestand dat de modules declareert die moeten worden gestart.

Elk item in het implementatiemanifest bevat specifieke informatie over een module en wordt gebruikt door de IoT Edge-agent voor het beheren van de levenscyclus van de module. Enkele van de meer interessante eigenschappen zijn:

* **settings.image** – De containerafbeelding die de IoT Edge-agent gebruikt om de module te starten. De IoT Edge-agent moet zijn geconfigureerd met referenties voor het containerregister als de afbeelding is beveiligd met een wachtwoord. Referenties voor het containerregister kunnen op afstand worden geconfigureerd met behulp van het implementatiemanifest of op het IoT Edge-apparaat zelf door het `config.yaml` bestand bij te werken in de iot edge-programmamap.
* **settings.createOptions** – Een tekenreeks die rechtstreeks wordt doorgegeven aan de Moby container daemon bij het starten van de container van een module. Als u opties in deze eigenschap toevoegt, kunnen geavanceerde configuraties zoals het doorsturen van poorten of het monteren van volumes in de container van een module worden toegevoegd.  
* **status** – De status waarin de IoT Edge-agent de module plaatst. Meestal is deze waarde ingesteld op *hardlopen* omdat de meeste mensen willen dat de IoT Edge-agent onmiddellijk alle modules op het apparaat start. U echter de beginstatus opgeven van een module die moet worden gestopt en wachten op een toekomstige tijd om de IoT Edge-agent te vertellen een module te starten.De IoT Edge-agent rapporteert de status van elke module terug naar de cloud in de gerapporteerde eigenschappen. Een verschil tussen de gewenste eigenschap en de gerapporteerde eigenschap is een indicator van een apparaat dat zich misdraagt. De ondersteunde statussen zijn:

  * Downloaden
  * In uitvoering
  * Slechte status
  * Mislukt
  * Gestopt

* **opnieuw opstartenBeleid** – Hoe de IoT Edge-agent een module opnieuw start. Mogelijke waarden zijn:
  
  * `never`– De IoT Edge-agent start de module nooit opnieuw op.
  * `on-failure`- Als de module vastloopt, start de IoT Edge-agent deze opnieuw. Als de module netjes wordt afgesloten, wordt de IoT Edge-agent niet opnieuw opgestart.
  * `on-unhealthy`- Als de module vastloopt of als ongezond wordt beschouwd, start de IoT Edge-agent deze opnieuw op.
  * `always`- Als de module vastloopt, als ongezond wordt beschouwd of op enigerlei wijze wordt afgesloten, wordt de IoT Edge-agent opnieuw opgestart.

* **imagePullPolicy** - Of de IoT Edge-agent nu probeert de nieuwste afbeelding voor een module automatisch te trekken of niet. Als u geen waarde opgeeft, is de standaardinstelling *opMaken*. Mogelijke waarden zijn:

  * `on-create`- Wanneer u een module start of een module bijwerkt op basis van een nieuw implementatiemanifest, probeert de IoT Edge-agent de moduleafbeelding uit het containerregister te halen.
  * `never`- De IoT Edge-agent zal nooit proberen de moduleafbeelding uit het containerregister te halen. Met deze configuratie bent u verantwoordelijk voor het verkrijgen van de moduleafbeelding op het apparaat en het beheren van eventuele afbeeldingsupdates.

De IoT Edge-agent verzendt runtime-respons naar IoT Hub. Hier is een lijst van mogelijke reacties:
  
* 200 - OK
* 400 - De implementatieconfiguratie is onjuist of ongeldig.
* 417 - Het apparaat heeft geen implementatieconfiguratieingesteld.
* 412 - De schemaversie in de implementatieconfiguratie is ongeldig.
* 406 - Het IoT Edge-apparaat is offline of verzendt geen statusrapporten.
* 500 - Er is een fout opgetreden in de runtime van IoT Edge.

Zie [Meer informatie over het implementeren van modules en het instellen van routes in IoT Edge](module-composition.md)voor meer informatie.

### <a name="security"></a>Beveiliging

De IoT Edge-agent speelt een cruciale rol in de beveiliging van een IoT Edge-apparaat. Er worden bijvoorbeeld acties uitgevoerd zoals het verifiëren van de afbeelding van een module voordat deze wordt gestart.

Lees meer informatie over het [Beveiligingsframework van IoT Edge](iot-edge-security-manager.md)van Azure.

## <a name="next-steps"></a>Volgende stappen

[Informatie over Azure IoT Edge-modules](iot-edge-modules.md)
