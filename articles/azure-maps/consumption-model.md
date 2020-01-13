---
title: Voertuig modellen voor route ring | Microsoft Azure kaarten
description: In dit artikel vindt u informatie over verbruiks modellen voor Voer tuigen voor route ring in Microsoft Azure Maps.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5a8a0778ce279846b0d7a66b1729b6898e80a4b5
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911699"
---
# <a name="consumption-model"></a>Verbruiksmodel

Online route ring biedt een set para meters voor een gedetailleerde beschrijving van het model voor het gebruik van het Voer tuig.
Afhankelijk van de waarde van **vehicleEngineType**, worden twee belangrijke verbruiksmodellen ondersteund: _Combustion_ en _Electric_. Het opgeven van para meters die deel uitmaken van verschillende modellen in dezelfde aanvraag, is een fout.
Verbruik Model kan niet worden gebruikt met **travelMode** waarden _bicycle_ en _pedestrian_.

## <a name="parameter-constraints-for-consumption-model"></a>Parameter beperkingen voor verbruiks model

In beide verbruiks modellen moet u voor het expliciet opgeven van sommige para meters ook andere opgeven. Deze afhankelijkheden zijn:

* Voor alle para meters moet **constantSpeedConsumption** worden opgegeven door de gebruiker. Er is een fout opgetreden bij het opgeven van een andere para meter voor een verbruiks model, met uitzonde ring van **vehicleWeight**als **constantSpeedConsumption** niet is opgegeven.
* **accelerationEfficiency** en **decelerationEfficiency** moeten altijd worden opgegeven als een paar (ofwel of geen).
* Als **accelerationEfficiency** en **decelerationEfficiency** zijn opgegeven, mag het product van hun waarden niet groter zijn dan 1 (om permanente beweging te voor komen).
* **uphillEfficiency** en **downhillEfficiency** moeten altijd worden opgegeven als een paar (ofwel of geen).
* Als **uphillEfficiency** en **downhillEfficiency** zijn opgegeven, mag het product van hun waarden niet groter zijn dan 1 (om permanente beweging te voor komen).
* Als de \*__efficiëntie__ parameters worden opgegeven door de gebruiker, moet **vehicleWeight** ook worden opgegeven. Wanneer **vehicleEngineType** is _verbrand_, moet ook **fuelEnergyDensityInMJoulesPerLiter** worden opgegeven.
* **maxChargeInkWh** en **currentChargeInkWh** moeten altijd worden opgegeven als een paar (ofwel of geen).

> [!NOTE]
> Als er alleen **constantSpeedConsumption** is opgegeven, worden er geen andere verbruiks aspecten, zoals hellingen en voertuig versnelling, in rekening gebracht voor verbruiks berekeningen.

## <a name="combustion-consumption-model"></a>Model voor verbrandings consumptie

Het Model van de consumptie verbranding wordt gebruikt wanneer **vehicleEngineType** is ingesteld op _combustion_.
De lijst met para meters die deel uitmaken van dit model vindt u hieronder. Raadpleeg de sectie para meters voor een gedetailleerde beschrijving.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Model voor elektriciteits verbruik

Het elektrische verbruik Model wordt gebruikt wanneer **vehicleEngineType** is ingesteld op _electric_.
De lijst met para meters die deel uitmaken van dit model vindt u hieronder. Raadpleeg de sectie para meters voor een gedetailleerde beschrijving.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Praktische waarden van verbruiks parameters

Een bepaalde set verbruiks parameters kan worden afgewezen, zelfs als deze kan voldoen aan alle expliciete vereisten die hierboven zijn opgegeven. Het gebeurt wanneer de waarde van een specifieke para meter, of een combi natie van waarden van verschillende para meters, wordt beschouwd als een onredelijke omvang van verbruiks waarden. Als dat gebeurt, duidt dit meestal op een invoer fout, omdat er voldoende aandacht wordt besteed aan alle goed werkende waarden van verbruiks parameters. Als een bepaalde set verbruiks parameters wordt afgewezen, bevat het bijbehorende fout bericht een beschrijving van de reden (en).
De gedetailleerde beschrijvingen van de para meters hebben voor beelden van de voor waarden voor beide modellen.
