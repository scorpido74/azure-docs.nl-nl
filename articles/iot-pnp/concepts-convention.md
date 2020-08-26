---
title: IoT Plug en Play-conventies | Microsoft Docs
description: Beschrijving van de conventies IoT Plug en Play verwacht dat apparaten worden gebruikt wanneer ze telemetrie en eigenschappen verzenden en opdrachten en eigenschaps updates verwerken.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef29be53e776c4c185ac8430b3340c53ca85d855
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856046"
---
# <a name="iot-plug-and-play-conventions"></a>Conventies voor IoT Plug en Play

IoT Plug en Play preview-apparaten moeten een aantal conventies volgen wanneer ze berichten uitwisselen met een IoT-hub. IoT Plug en Play preview-apparaten gebruiken het MQTT-protocol om te communiceren met IoT Hub.

U beschrijft de telemetrie, eigenschappen en opdrachten die een IoT Plug en Play-apparaat implementeert met een [DTDL-model (Digital Apparaatdubbels Definition Language v2)](https://github.com/Azure/opendigitaltwins-dtdl) _model_. Er zijn twee typen model waarnaar wordt verwezen in dit artikel:

- **Geen onderdeel** : een model zonder onderdelen. Het model declareert telemetrie, eigenschappen en opdrachten als eigenschappen op het hoogste niveau in de sectie inhoud van de hoofd interface.
- **Meerdere onderdelen** : een model dat uit twee of meer interfaces bestaat. Een hoofd interface met telemetrie, eigenschappen en opdrachten. Een of meer interfaces die zijn gedeclareerd als onderdelen met aanvullende telemetrie, eigenschappen en opdrachten.

Zie [IoT Plug en Play-onderdelen in modellen](concepts-components.md)voor meer informatie.

## <a name="identify-the-model"></a>Het model identificeren

Voor het aankondigen van het model dat wordt geïmplementeerd, bevat een IoT-Plug en Play apparaat de model-ID in het MQTT-verbindings pakket door toe te voegen `model-id` aan het `USERNAME` veld.

Voor het identificeren van het model dat een apparaat implementeert, kan een service de model-ID ophalen uit:

- Het dubbele veld van het apparaat `modelId` .
- Het digitale dubbele `$metadata.$model` veld.
- Een digitale dubbele wijzigings melding.

## <a name="telemetry"></a>Telemetrie

Voor telemetrie die van een apparaat zonder onderdeel wordt verzonden, zijn geen extra meta gegevens vereist. Het systeem voegt de `dt-dataschema` eigenschap toe.

De telemetrie die vanaf een apparaat met meerdere onderdelen wordt verzonden, moet worden toegevoegd `$.sub` als een bericht eigenschap. Het systeem voegt de `dt-subject` Eigenschappen en toe `dt-dataschema` .

## <a name="read-only-properties"></a>Alleen-lezen eigenschappen

### <a name="sample-no-component-read-only-property"></a>Voor beeld geen eigenschap met het kenmerk alleen-lezen

Een apparaat kan een wille keurige geldige JSON verzenden die volgt op de DTDL v2-regels.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Voorbeeld lading**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>Voor beeld van meerdere eigenschappen met het kenmerk alleen-lezen

Het apparaat moet de markering toevoegen om aan te `{"__t": "c"}` geven dat het element verwijst naar een onderdeel.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Gerapporteerde eigenschap**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>Beschrijf bare eigenschappen

Het apparaat moet bevestigen dat het de eigenschap heeft ontvangen door een gerapporteerde eigenschap te verzenden. De gerapporteerde eigenschap moet het volgende bevatten:

- `value` -de werkelijke waarde van de eigenschap (doorgaans de ontvangen waarde, maar het apparaat kan besluiten een andere waarde te rapporteren).
- `ac` -een bevestigings code die gebruikmaakt van een HTTP-status code.
- `av` -een bevestigings versie die naar de `$version` gewenste eigenschap verwijst.
- `ad` -een optionele beschrijving van de bevestiging.

### <a name="sample-no-component-writable-property"></a>Voor beeld van geen onderdeel schrijf bare eigenschap

Een apparaat kan een wille keurige geldige JSON verzenden die volgt op de DTDL v2-regels:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Gewenste eigenschap**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Gerapporteerde eigenschap**

      ```json
      "reported": {
        "targetTemperature": {
          "value": 21.3,
          "ac": 200,
          "av": 3,
          "ad": "complete"
       }
     }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>Voor beeld van de eigenschap schrijf bare meerdere componenten

Het apparaat moet de markering toevoegen om aan te `{"__t": "c"}` geven dat het element verwijst naar een onderdeel.

De markering wordt alleen verzonden voor updates op onderdeel niveau, waardoor apparaten mag controleren op deze vlag.

Het apparaat moet bevestigen dat het de eigenschap heeft ontvangen door het verzenden van een gerapporteerde eigenschap:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Gewenste eigenschap**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Gerapporteerde eigenschap**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": {
            "value": 23,
            "ac": 200,
            "av": 3,
            "ad": "complete"
          }
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="commands"></a>Opdracht

Geen onderdeel interfaces gebruiken de opdracht naam zonder voor voegsel.

Op een apparaat gebruiken meerdere onderdeel interfaces opdracht namen met de volgende indeling: `componentName*commandName` .

## <a name="next-steps"></a>Volgende stappen

Nu u over Plug en Play conventies van IoT hebt geleerd, zijn hier enkele aanvullende bronnen:

- [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Apparaat-SDK voor C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Model onderdelen](./concepts-components.md)
