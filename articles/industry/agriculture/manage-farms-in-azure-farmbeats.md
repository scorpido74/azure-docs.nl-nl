---
title: Boerderijen beheren
description: Beschrijft hoe u boerderijen beheert
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271718"
---
# <a name="manage-farms"></a>Een boerderij beheren

U uw farms beheren in Azure FarmBeats. In dit artikel vindt u informatie over het maken van boerderijen, het installeren van apparaten, sensoren en drones die u helpen bij het beheren van uw boerderijen.

## <a name="create-farms"></a>Boerderijen maken

Voer de volgende stappen uit:

1. Log in op de Farm Accelerator, de **farms** pagina wordt weergegeven.
    Op de pagina **Boerderijen** wordt de lijst van boerderijen weergegeven voor het geval ze al in abonnement zijn gemaakt.

    Hier is de voorbeeldafbeelding:

    ![Project Farm Beats](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Selecteer **Farm maken** en geef **naam,** **gewassen** en **adres op.**
3. Selecteer in het veld **Farmgrens definiëren**(verplicht veld) **Markeren op kaart** of **GeoJSON-code plakken**.

Dit zijn de twee manieren om een bedrijfsgrens te definiëren:

1. **Markering op kaart:** gebruik het gereedschap kaartbesturingselement om de grens van de boerderij te tekenen en te markeren. Om de grenzen ![te](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) markeren, slaat Project Farm beats en markeert u de exacte grenzen.

    ![Project Farm Beats](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **GeoJson-code plakken:** De GeoJSON is een indeling voor het coderen van geografische gegevensstructuren met behulp van JavaScript Object Notatie (JSON). Met deze optie wordt een tekstvak weergegeven waarin een GeoJSON-tekenreeks kan worden ingevoerd om de bedrijfsgrenzen te markeren. U ook GeoJSON-code maken vanuit GeoJSON.io.
Gebruik de tooltips om de informatie in te vullen.

    ![Project Farm Beats](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  Selecteer **Verzenden** om een farm te maken. Een nieuwe boerderij wordt gemaakt en weergegeven op de **pagina Boerderijen.**

## <a name="view-farm"></a>Boerderij bekijken

Op de pagina Farmlist wordt een lijst met gemaakte bedrijven weergegeven. Selecteer een farm om de lijst van:

 - **Aantal apparaten** : geeft het aantal en de status weer van apparaten die in de farm zijn geïmplementeerd.
 - **Kaart** — kaart van de boerderij met de apparaten die in de boerderij zijn geïmplementeerd.
 - **Telemetrie** : geeft de telemetrie weer van de sensoren die in de farm zijn geïmplementeerd.
 - **Laatste Precision Maps** - toont de nieuwste satellietindexkaart (EVI, NDWI), Soil Moisture Heatmap en Sensor Placement map.

## <a name="edit-farm"></a>Boerderij bewerken

Op de pagina **Boerderijen** wordt een lijst van gecreëerde boerderijen weergegeven.

1.  Selecteer een farm die de boerderij wilt bekijken en bewerken.
2.  Selecteer **Farm bewerken** om de farminformatie te bewerken. In het venster **Farmdetails** u **naam**, **gewassen,** **adres**en velden **Farmboundary** bewerken.

    ![Project Farm Beats](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. Selecteer **Verzenden** om de bewerkte details op te slaan.

## <a name="delete-farm"></a>Farm verwijderen

Op de pagina **Boerderijen** wordt een lijst weergegeven van de bedrijven die zijn gemaakt. Gebruik de volgende stappen om een farm te verwijderen:

1.  Selecteer een farm in de lijst om bedrijfsgegevens te verwijderen.
2.  Selecteer **Farm verwijderen** om de farm te verwijderen.

    ![Project Farm Beats](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > Wanneer u een farm verwijdert, worden de apparaten en kaarten die aan de farm zijn gekoppeld, niet verwijderd. Alle bedrijfsinformatie die aan het apparaat en de kaarten is gekoppeld, is niet relevant. U apparaten, telemetrie en kaarten blijven bekijken vanuit de FarmBeats-service.


## <a name="next-steps"></a>Volgende stappen

Nu u uw farm hebt gemaakt, u leren hoe [u sensorgegevens](get-sensor-data-from-sensor-partner.md) in uw farm laten stromen.
