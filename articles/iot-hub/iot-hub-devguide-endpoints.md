---
title: Informatie over Azure IoT Hub-eindpunten | Microsoft Documenten
description: Ontwikkelaarshandleiding - referentie-informatie over iot Hub-apparaatgerichte en servicegerichte eindpunten.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 53660ad93ab2218d546ae6f363873c4d66872e2b
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730316"
---
# <a name="reference---iot-hub-endpoints"></a>Referentie - IoT Hub-eindpunten

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT-hubnamen

U de hostnaam van de IoT-hub die uw eindpunten host, vinden in de portal op de **overzichtspagina** van uw hub. Standaard ziet de DNS-naam van een `{your iot hub name}.azure-devices.net`IoT-hub eruit als: .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lijst met ingebouwde IoT Hub-eindpunten

Azure IoT Hub is een multi-tenant service die de functionaliteit ervan blootstelt aan verschillende actoren. In het volgende diagram ziet u de verschillende eindpunten die IoT Hub blootlegt.

![IoT Hub-eindpunten](./media/iot-hub-devguide-endpoints/endpoints.png)

In de volgende lijst worden de eindpunten beschreven:

* **Resourceprovider**. De IoT Hub-resourceprovider onthult een [Azure Resource Manager-interface.](../azure-resource-manager/management/overview.md) Met deze interface kunnen Azure-abonnementseigenaren IoT-hubs maken en verwijderen en iot-hub-eigenschappen bijwerken. IoT Hub-eigenschappen zijn van toepassing op [het beveiligingsbeleid op hubniveau,](iot-hub-devguide-security.md#access-control-and-permissions)in tegenstelling tot toegangscontrole op apparaatniveau, en functionele opties voor cloud-to-device- en device-to-cloud-berichten. Met de IoT Hub-resourceprovider u ook [apparaatidentiteiten exporteren.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

* **Apparaatidentiteitsbeheer**. Elke IoT-hub legt een set HTTPS REST-eindpunten bloot om apparaatidentiteiten te beheren (maken, ophalen, bijwerken en verwijderen). [Apparaatidentiteiten](iot-hub-devguide-identity-registry.md) worden gebruikt voor apparaatverificatie en toegangscontrole.

* **Device twin management**. Elke IoT-hub stelt een set https rest-eindpunt en servicegericht bureaublad bloot aan query-en [update-apparaattweelingen](iot-hub-devguide-device-twins.md) (updatetags en -eigenschappen).

* **Jobs management**. Elke IoT-hub onthult een set https rest-eindpunt en stelt een set servicegerichte HTTPS REST-eindpunten bloot om [taken](iot-hub-devguide-jobs.md)op te vragen en te beheren.

* **Apparaateindpunten**. Voor elk apparaat in het identiteitsregister wordt door IoT Hub een reeks eindpunten zichtbaar gemaakt:

  * *Stuur device-to-cloud berichten*. Een apparaat gebruikt dit eindpunt om [device-to-cloudberichten te verzenden.](iot-hub-devguide-messages-d2c.md)

  * *Ontvang berichten van cloud tot apparaat.* Een apparaat gebruikt dit eindpunt om gerichte [cloud-to-device-berichten te](iot-hub-devguide-messages-c2d.md)ontvangen.

  * *Bestandsuploads starten*. Een apparaat gebruikt dit eindpunt om een Azure Storage SAS URI van IoT Hub te ontvangen om een bestand te [uploaden.](iot-hub-devguide-file-upload.md)

  * *Dubbele eigenschappen van het apparaat ophalen en bijwerken*. Een apparaat gebruikt dit eindpunt om toegang te krijgen tot de eigenschappen van de [apparaattweeling.](iot-hub-devguide-device-twins.md)

  * *Ontvang directe methodeaanvragen*. Een apparaat gebruikt dit eindpunt om te luisteren naar de verzoeken van [de directe methode.](iot-hub-devguide-direct-methods.md)

    Deze eindpunten worden blootgesteld met [MQTT v3.1.1,](https://mqtt.org/)HTTPS 1.1 en [AMQP 1.0-protocollen.](https://www.amqp.org/) AMQP is ook beschikbaar via [WebSockets](https://tools.ietf.org/html/rfc6455) op poort 443.

* **Serviceeindpunten**. Elke IoT-hub legt een reeks eindpunten bloot voor uw back-end van uw oplossing om met uw apparaten te communiceren. Op één uitzondering na worden deze eindpunten alleen blootgesteld met behulp van het [AMQP-protocol.](https://www.amqp.org/) Het aanroeppunt van de methode wordt weergegeven via het HTTPS-protocol.
  
  * *Ontvang device-to-cloud berichten*. Dit eindpunt is compatibel met [Azure Event Hubs.](https://azure.microsoft.com/documentation/services/event-hubs/) Een back-endservice kan deze gebruiken om de [device-to-cloudberichten te](iot-hub-devguide-messages-d2c.md) lezen die door uw apparaten worden verzonden. U naast dit ingebouwde eindpunt aangepaste eindpunten maken op uw IoT-hub.
  
  * *Stuur berichten van cloud naar apparaat en ontvang leveringsbevestigingen.* Met deze eindpunten kan uw oplossing back-end worden verzonden naar betrouwbare [cloud-to-device-berichten](iot-hub-devguide-messages-c2d.md)en de bijbehorende leverings- of vervaldatumbevestigingen ontvangen.
  
  * *Ontvang meldingen van bestanden*. Met dit eindpunt voor berichten u meldingen ontvangen van wanneer uw apparaten een bestand hebben geüpload. 
  
  * *Directe methode aanroep*. Met dit eindpunt kan een back-endservice een [directe methode](iot-hub-devguide-direct-methods.md) op een apparaat aanroepen.
  
  * *Ontvang operationele controlegebeurtenissen*. Met dit eindpunt u gebeurtenissen voor het bewaken van bewerkingen ontvangen als uw IoT-hub is geconfigureerd om ze uit te zenden. Zie [IoT Hub Operations monitoring voor](iot-hub-operations-monitoring.md)meer informatie.

In het artikel [Azure IoT SDKs](iot-hub-devguide-sdks.md) worden de verschillende manieren beschreven om toegang te krijgen tot deze eindpunten.

Alle IoT Hub-eindpunten gebruiken het [TLS-protocol](https://tools.ietf.org/html/rfc5246) en er wordt nooit een eindpunt weergegeven op onversleutelde/onbeveiligde kanalen.

## <a name="custom-endpoints"></a>Aangepaste eindpunten

U bestaande Azure-services in uw abonnement koppelen aan uw IoT-hub om op te treden als eindpunten voor het routeren van berichten. Deze eindpunten fungeren als serviceeindpunten en worden gebruikt als sinks voor berichtroutes. Apparaten kunnen niet rechtstreeks naar de extra eindpunten schrijven. Meer informatie over [het routeren van berichten](../iot-hub/iot-hub-devguide-messages-d2c.md).

IoT Hub ondersteunt momenteel de volgende Azure-services als extra eindpunten:

* Azure Storage-containers
* Event Hubs
* Service Bus-wachtrijen
* Service Bus-onderwerpen

Zie [Quota en beperking](iot-hub-devguide-quotas-throttling.md)voor de limieten voor het aantal eindpunten dat u toevoegen.

U de REST API [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) gebruiken om de status van de eindpunten te krijgen. We raden u aan de [IoT Hub-statistieken](iot-hub-metrics.md) met betrekking tot de latentie van routeringsberichten te gebruiken om fouten te identificeren en te debuggen wanneer de status van eindpunt dood of ongezond is, omdat we verwachten dat de latentie hoger zal zijn wanneer het eindpunt zich in een van die statussen bevindt.

|Status|Beschrijving|
|---|---|
|Gezonde|Het eindpunt is het accepteren van berichten zoals verwacht.|
|Ongezonde|Het eindpunt accepteert geen berichten zoals verwacht en IoT Hub probeert opnieuw gegevens naar dit eindpunt te verzenden. De status van een ongezond eindpunt wordt bijgewerkt naar gezond wanneer IoT Hub een uiteindelijk consistente gezondheidstoestand heeft vastgesteld.|
|unknown|IoT Hub heeft geen verbinding met het eindpunt tot stand gebracht. Er zijn geen berichten bezorgd of afgewezen vanaf dit eindpunt.|
|Dood|Het eindpunt is het accepteren van berichten, nadat IoT Hub opnieuw geprobeerd berichten te verzenden voor de nieuwe proefperiode.|

## <a name="field-gateways"></a>Veldgateways

In een IoT-oplossing zit een *veldgateway* tussen uw apparaten en uw IoT Hub-eindpunten. Het bevindt zich meestal dicht bij uw apparaten. Uw apparaten communiceren rechtstreeks met de veldgateway met behulp van een protocol dat wordt ondersteund door de apparaten. De veldgateway maakt verbinding met een IoT Hub-eindpunt met behulp van een protocol dat wordt ondersteund door IoT Hub. Een veldgateway kan een speciaal hardwareapparaat zijn of een computer met een laag vermogen waarop aangepaste gatewaysoftware wordt uitgevoerd.

U [Azure IoT Edge](/azure/iot-edge/) gebruiken om een veldgateway te implementeren. IoT Edge biedt functionaliteit zoals multiplexing-communicatie van meerdere apparaten op dezelfde IoT Hub-verbinding.

## <a name="next-steps"></a>Volgende stappen

Andere referentieonderwerpen in deze IoT Hub-ontwikkelaarshandleiding zijn:

* [IoT Hub-querytaal voor apparaattweelingen, taken en berichtroutering](iot-hub-devguide-query-language.md)
* [Quota en beperkingen](iot-hub-devguide-quotas-throttling.md)
* [Ondersteuning voor IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Uw IP-adres van uw IoT-hub begrijpen](iot-hub-understand-ip-address.md)