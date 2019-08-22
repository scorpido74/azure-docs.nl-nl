---
title: Architecturale concepten in azure IoT Central | Microsoft Docs
description: In dit artikel worden de belangrijkste concepten besproken die betrekking hebben op de architectuur van Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: abc09ac1a13537c31fe96fae14edefd0d06b6aef
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880252"
---
# <a name="azure-iot-central-architecture-preview-features"></a>Azure IoT Central-architectuur (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Dit artikel bevat een overzicht van de Microsoft Azure IoT Central architectuur.

![Architectuur op het hoogste niveau](media/concepts-architecture-pnp/architecture.png)

## <a name="devices"></a>Apparaten

Apparaten wisselen gegevens uit met uw Azure IoT Central-toepassing. Een apparaat kan:

- Metingen zoals telemetrie verzenden.
- Synchroniseer de instellingen met uw toepassing.

In azure IoT Central worden de gegevens die een apparaat kan uitwisselen met uw toepassing opgegeven in een sjabloon voor een apparaat. Zie [meta gegevens beheer](#metadata-management)voor meer informatie over Apparaatinstellingen.

Raadpleeg [connectiviteit](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)van apparaten voor meer informatie over de manier waarop apparaten verbinding maken met uw Azure IOT Central-toepassing.

## <a name="cloud-gateway"></a>Cloud gateway

Azure IoT Central maakt gebruik van Azure IoT Hub als een Cloud gateway die connectiviteit van apparaten mogelijk maakt. IoT Hub maakt het volgende mogelijk:

- Gegevens opname op schaal in de Cloud.
- Apparaatbeheer.
- Connectiviteit van beveiligde apparaten.

Zie voor meer informatie over IoT Hub [Azure IOT hub](https://docs.microsoft.com/azure/iot-hub/).

Zie [connectiviteit van apparaten](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)voor meer informatie over de connectiviteit van apparaten in azure IOT Central.

## <a name="data-stores"></a>Gegevensopslag

In azure IoT Central worden toepassings gegevens opgeslagen in de Cloud. De opgeslagen toepassings gegevens zijn onder andere:

- Device-sjablonen.
- Apparaat-id's.
- Meta gegevens van het apparaat.
- Gebruikers gegevens en-rollen.

Azure IoT Central gebruikt een time series Store voor de meet gegevens die vanaf uw apparaten worden verzonden. Time Series-gegevens van apparaten die worden gebruikt door de analyse service.

## <a name="analytics"></a>Analyse

De analyse service is verantwoordelijk voor het genereren van de aangepaste rapportage gegevens die door de toepassing worden weer gegeven. Een operator kan [de analyse aanpassen](howto-create-analytics.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) die in de toepassing wordt weer gegeven. De Analytics-service is boven op [Azure time series Insights](https://azure.microsoft.com/services/time-series-insights/) gebouwd en verwerkt de meet gegevens die vanaf uw apparaten worden verzonden.

## <a name="rules-and-actions"></a>Regels en acties

[Regels en acties](howto-create-telemetry-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) kunnen nauw samen worden gebruikt om taken in de toepassing te automatiseren. Een Builder kan regels definiëren op basis van de telemetrie van apparaten, zoals de Tempe ratuur die een gedefinieerde drempel waarde overschrijdt. Azure IoT Central gebruikt een Stream-processor om te bepalen wanneer aan de voor waarden van de regel wordt voldaan. Wanneer aan een regel voorwaarde wordt voldaan, wordt een door de Builder gedefinieerde actie geactiveerd. Een actie kan bijvoorbeeld een e-mail verzenden om een technicus op de hoogte te stellen dat de Tempe ratuur op een apparaat te hoog is.

## <a name="metadata-management"></a>Beheer van meta gegevens

In een Azure IoT Central-toepassing worden met Apparaatinstellingen het gedrag en de mogelijkheden van typen apparaten gedefinieerd. Een sjabloon voor een koel kast bevat bijvoorbeeld de telemetrie die een koel kast naar uw toepassing verzendt.

![Sjabloon architectuur](media/concepts-architecture-pnp/template-architecture.png)

In een IoT Central voorbeeld sjabloon voor een toepassings apparaat:

- Met **hulp modellen voor apparaten** kunt u de mogelijkheden van een apparaat opgeven, zoals de telemetrie die het verzendt, de eigenschappen die de status van het apparaat definiëren en de opdrachten waarop het apparaat reageert. De mogelijkheden van het apparaat zijn ingedeeld in een of meer interfaces. Zie de [IoT Plug en Play](https://aka.ms/iot-pnp-docs) -documentatie voor meer informatie over de mogelijkheden van apparaten.
- Met **Cloud eigenschappen** geeft u de eigenschappen op IOT Central winkels voor een apparaat. Deze eigenschappen worden alleen opgeslagen in IoT Central en worden nooit naar een apparaat verzonden.
- Met **weer gaven** worden de Dash boards en formulieren opgegeven die de opbouw functie maakt, zodat de operator de apparaten kan controleren en beheren.
- Met **aanpassingen** kunnen de opbouw functie enkele van de definities in het hulp model van het apparaat overschrijven zodat deze relevanter worden voor de IOT Central-toepassing.

Een toepassing kan een of meer gesimuleerde en echte apparaten op basis van elk apparaatprofiel hebben.

## <a name="data-export"></a>Gegevensexport

In een Azure IoT Central-toepassing kunt u [uw gegevens voortdurend exporteren](howto-export-data-event-hubs-service-bus-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) naar uw eigen azure-Event Hubs en Azure service bus exemplaren. U kunt uw gegevens ook periodiek exporteren naar uw Azure Blob-opslag account. IoT Central kunt metingen, apparaten en apparaatinstellingen exporteren.

## <a name="batch-device-updates"></a>Updates voor batch-apparaten

In een Azure IoT Central-toepassing kunt u [taken maken en uitvoeren](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) om verbonden apparaten te beheren. Met deze taken kunt u bulksgewijs updates uitvoeren op eigenschappen of instellingen van het apparaat, of opdracht geven. U kunt bijvoorbeeld een taak maken om de snelheid van de ventilator voor meerdere gekoelde verkoop machines te verg Roten.

## <a name="role-based-access-control-rbac"></a>Toegangsbeheer op basis van rollen (RBAC)

Een [beheerder kan toegangs regels definiëren](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) voor een Azure IOT Central-toepassing met behulp van de vooraf gedefinieerde rollen. Een beheerder kan gebruikers toewijzen aan rollen die bepalen op welke gebieden van de toepassing de gebruiker toegang heeft.

## <a name="security"></a>Beveiliging

Beveiligings functies in azure IoT Central zijn onder andere:

- Gegevens worden in transit en op rest versleuteld.
- Verificatie wordt door Azure Active Directory of een micro soft-account verschaft. Verificatie met twee factoren wordt ondersteund.
- Volledige isolatie van de Tenant.
- Beveiliging op apparaatniveau.

## <a name="ui-shell"></a>UI-shell

De UI-shell is een moderne, op de browser gebaseerde toepassing die in de Cloud is gebaseerd.
Een beheerder kan de gebruikers interface van de toepassing aanpassen door aangepaste Thema's toe te passen en de Help-koppelingen te wijzigen zodat deze naar uw eigen aangepaste Help-bronnen verwijzen. Zie het artikel over [de Azure IOT Central-gebruikers interface aanpassen](howto-customize-ui.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) voor meer informatie over UI-aanpassing.

Een operator kan gepersonaliseerde toepassings dashboards maken. U kunt verschillende Dash boards hebben die verschillende gegevens weer geven en hiertussen scha kelen.

## <a name="next-steps"></a>Volgende stappen

Nu u over de architectuur van Azure IoT Central hebt geleerd, is de voorgestelde volgende stap in azure IoT Central meer informatie over de [connectiviteit van apparaten](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .