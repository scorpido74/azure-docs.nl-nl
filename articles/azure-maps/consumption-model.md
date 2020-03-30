---
title: Voertuigverbruiksmodellen voor de routering | Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over voertuigverbruiksmodellen voor routering in Microsoft Azure Maps.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: c1572eddf78ca2d5f8f4e3ee9f1fe47b0d43f5aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190256"
---
# <a name="consumption-model"></a>Verbruiksmodel

De routeringsservice biedt een reeks parameters voor een gedetailleerde beschrijving van het voertuigspecifieke verbruiksmodel.
Afhankelijk van de waarde van **vehicleEngineType**worden twee belangrijkste verbruiksmodellen ondersteund: _Verbranding_ en _Elektrisch_. Het is onjuist om parameters op te geven die tot verschillende modellen in dezelfde aanvraag behoren. Ook kunnen de parameters van het verbruiksmodel niet worden gebruikt met de volgende **travelMode-waarden:** _fiets_ en _voetganger._

## <a name="parameter-constraints-for-consumption-model"></a>Parameterbeperkingen voor het verbruiksmodel

In beide verbruiksmodellen zijn er enkele afhankelijkheden bij het opgeven van parameters. Dit betekent dat het expliciet opgeven van bepaalde parameters het opgeven van een aantal andere parameters vereist. Hier zijn deze afhankelijkheden om bewust te zijn van:

* Alle parameters vereisen **constantSpeedConsumption** worden opgegeven door de gebruiker. Het is een fout om een andere parameter voor het verbruiksmodel op te geven, als **constantSpeedConsumption** niet is opgegeven. De parameter **vehicleWeight** is een uitzondering voor deze vereiste.
* **versnellingEfficiëntie** en **vertragingEfficiëntie** moeten altijd worden opgegeven als een paar (dat wil zeggen, beide of geen).
* Als **versnellingEfficiëntie** en vertragingefficiëntie zijn **gespecificeerd,** mag het product van hun waarden niet groter zijn dan 1 (om eeuwigdurende beweging te voorkomen).
* **uphillEfficiency** en **downhillEfficiëntie** moet altijd worden opgegeven als een paar (dat wil zeggen, beide of geen).
* Als **uphillEfficiency** en **downhillEfficiency** worden gespecificeerd, mag het product van hun waarden niet groter zijn dan 1 (om eeuwigdurende beweging te voorkomen).
* Als \*de __efficiëntieparameters__ door de gebruiker worden opgegeven, moet **ook het gewicht van het voertuig** worden opgegeven. Wanneer **het voertuigEngineType** wordt _verbrand,_ moet **ook brandstofEnergyDesityInMJoulesPerLiter** worden gespecificeerd.
* **maxChargeInkWh** en **currentChargeInkWh** moeten altijd worden opgegeven als een paar (dat wil zeggen, beide of geen).

> [!NOTE]
> Als alleen **constantSpeedConsumption** wordt gespecificeerd, worden geen andere verbruiksaspecten zoals hellingen en voertuigversnelling in aanmerking genomen voor verbruiksberekeningen.

## <a name="combustion-consumption-model"></a>Model voor verbrandingsverbruik

Het Combustion Consumption Model wordt gebruikt wanneer **vehicleEngineType** is ingesteld op _verbranding._
De lijst met parameters die bij dit model horen, vindt u hieronder. Raadpleeg de sectie Parameters voor een gedetailleerde beschrijving.

* constantSpeedConsumptionInLitersPerHundredkm
* voertuigGewicht
* huidige FuelInLiters
* hulpvermogenInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* versnellingEfficiëntie
* vertragingEfficiëntie
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Elektrisch verbruiksmodel

Het elektrische verbruik model wordt gebruikt wanneer **vehicleEngineType** is ingesteld op _elektrische_.
De lijst met parameters die bij dit model horen, vindt u hieronder. Raadpleeg de sectie Parameters voor een gedetailleerde beschrijving.

* constantSpeedConsumptionInkWhPerHundredkm
* voertuigGewicht
* huidigeChargeInkWh
* maxChargeInkWh
* hulpkrachtPowerInkW
* versnellingEfficiëntie
* vertragingEfficiëntie
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Verstandige waarden van verbruiksparameters

Een bepaalde set verbruiksparameters kan worden afgewezen, ook al voldoet de set mogelijk aan alle expliciete vereisten. Het gebeurt wanneer de waarde van een specifieke parameter, of een combinatie van waarden van verschillende parameters, wordt beschouwd als leiden tot onredelijke grootheden van de consumptiewaarden. Als dat gebeurt, duidt het waarschijnlijk op een invoerfout, omdat de juiste zorg wordt genomen om aan alle zinnige waarden van verbruiksparameters te voldoen. In het geval dat een bepaalde set verbruiksparameters wordt afgewezen, bevat het begeleidende foutbericht een tekstuele uitleg van de reden(en).
De gedetailleerde beschrijvingen van de parameters hebben voorbeelden van verstandige waarden voor beide modellen.
