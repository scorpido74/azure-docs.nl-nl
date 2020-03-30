---
title: Genereer bodemvocht Heatmap
description: Beschrijft hoe u soil moisture heatmap genereren in Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482567"
---
# <a name="generate-soil-moisture-heatmap"></a>Genereer bodemvocht Heatmap

Bodemvocht is het water dat wordt gehouden in de ruimtes tussen de bodemdeeltjes.Soil Moisture Heatmap helpt u de vochtgegevens op elke diepte en met hoge resolutie binnen uw boerderijen te begrijpen. Om een nauwkeurige en bruikbare bodemvocht heatmap te genereren, is een uniforme inzet van sensoren van dezelfde provider vereist. Verschillende aanbieders zullen verschillen hebben in de manier waarop bodemvocht wordt gemeten, samen met verschillen in kalibratie. De Heatmap wordt gegenereerd voor een bepaalde diepte met behulp van de sensoren ingezet op die diepte.

In dit artikel wordt het proces beschreven van het genereren van een Soil Moisture Heatmap voor uw bedrijf, met behulp van de Azure FarmBeats Accelerator. In dit artikel leert u hoe u:

- [Boerderijen maken](#create-a-farm)
- [Sensoren toewijzen aan farms](#get-soil-moisture-sensor-data-from-partner)
- [Genereer bodemvocht Heatmap](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat:  

- Een Azure-abonnement.
- Een actief exemplaar van Azure FarmBeats.
- Voor de boerderij zijn minimaal drie bodemvochtsensoren beschikbaar.

## <a name="create-a-farm"></a>Een boerderij maken

Een boerderij is een geografisch gebied van belang waarvoor u een bodemvocht heatmap wilt maken. U een farm maken met de [Farms API](https://aka.ms/FarmBeatsDatahubSwagger) of in de [FarmsBeats Accelerator UI](manage-farms-in-azure-farmbeats.md#create-farms)

## <a name="deploy-sensors"></a>Sensoren implementeren

U moet fysiek gebruik maken van bodemvocht sensoren op de boerderij. U bodemvochtsensoren kopen bij een van onze goedgekeurde partners - [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) en [Teralytic](https://teralytic.com/). U moet coördineren met uw sensor provider om de fysieke setup op uw boerderij te doen.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Haal gegevens van de bodemvochtsensor van partner

Als sensoren beginnen met streamen, de gegevens in het dashboard met partnergegevens, schakelen ze de gegevens in Azure FarmBeats in. Dit kan vanuit de partneraanvraag.

Als u bijvoorbeeld Davis-sensoren hebt gekocht, logt u in op uw weerkoppelingsaccount en verstrekt u de vereiste referenties om de gegevensstreaming naar Azure FarmBeats in te schakelen. Als u de vereiste referenties wilt ophalen, volgt u de instructies van [Sensorgegevens ophalen.](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)

Zodra u uw referenties invoert en **Verzenden** selecteert in de partnertoepassing, u de gegevens laten stromen naar Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Wijs bodemvochtsensoren toe aan de boerderij

Zodra u uw sensoraccount hebt gekoppeld aan Azure FarmBeats, moet u de bodemvochtsensoren toewijzen aan de boerderij van belang.

1.  Selecteer op de startpagina **Farms** in het menu, de **lijstpagina Boerderijen** wordt weergegeven.
2.  Selecteer **MyFarm** > **Add Devices**.
3.  Het venster **Apparaten toevoegen** wordt weergegeven. Selecteer een apparaat dat is gekoppeld aan de bodemvochtsensoren voor uw bedrijf.

    ![Project Farm Beats](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecteer **Apparaten toevoegen**.     

## <a name="generate-soil-moisture-heatmap"></a>Genereer bodemvocht Heatmap

Deze stap is het creëren van een taak of een langdurige operatie die soil moisture heatmap voor uw boerderij zal genereren.

1.  Ga op de startpagina naar **Farms** in het linkernavigatiemenu om de pagina Boerderijen te bekijken.
2.  Selecteer **MyFarm**.
3.  Selecteer op de pagina **Farmdetails** de optie **Precisiekaart genereren**.
4.  Selecteer **bodemvocht**in het vervolgkeuzemenu.
5.  Selecteer **Deze week**in het venster **Bodemvocht** .
6.  Voer in de **Select Soil Moisture** Sensor **Measure**de maat in die u voor de kaart wilt gebruiken.
    Om de sensor te meten, selecteer je in **Sensoren**een bodemvochtsensor. Gebruik **in Sensoreigenschappen**de waarde **Naam meten.**

    ![Project Farm Beats](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Selecteer **Kaarten genereren**.
    Er wordt een bevestigingsbericht met taakgegevens weergegeven. Zie Functiestatus in vacatures voor meer informatie.

    >[!NOTE]
    > De klus duurt ongeveer drie tot vier uur.

### <a name="download-the-soil-moisture-heatmap"></a>Download de Soil Moisture Heatmap

Voer de volgende stappen uit:

1. Controleer **op** de pagina Vacatures de **taakstatus** voor de taak die u in de laatste procedure hebt gemaakt.
2. Wanneer de taakstatus **Geslaagd wordt weergegeven,** selecteert u **Kaarten** in het menu.
3. Zoek naar de kaart op de dag dat deze is gemaakt in het formaat <bodem-moisture_MyFarm_YYYY-MM-DD>.
4. Selecteer een kaart in de kolom **Naam,** een pop-upvenster wordt weergegeven met het voorbeeld van de geselecteerde kaart.
5. Selecteer **Download**. De kaart wordt gedownload en opgeslagen in de lokale map van uw computer.

    ![Project Farm Beats](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Volgende stappen

Nu u met succes een Soil Moisture Heatmap hebt gegenereerd, leert u hoe u [sensorplaatsing](generate-maps-in-azure-farmbeats.md#sensor-placement-map) genereren en [historische telemetriegegevens innemen.](ingest-historical-telemetry-data-in-azure-farmbeats.md) 
