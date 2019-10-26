---
title: Kennismaking met de gebruikersinterface van Azure IoT Central | Microsoft Docs
description: Als maker is het nuttig om vertrouwd te raken met de belangrijkste gebieden van de gebruikersinterface van Azure IoT Central, dat u gebruikt om een IoT-oplossing te maken.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 6e375e809308da8be0723fb2fcbdf718e9fb5479
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957767"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Kennismaking met de gebruikersinterface van Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

In dit artikel maakt u kennis met de gebruikersinterface van Microsoft Azure IoT Central. U kunt de gebruikersinterface gebruiken om een Azure IoT Central-oplossing te maken en de daarmee verbonden apparaten te beheren en gebruiken.

Als _maker_ gebruikt u de gebruikersinterface van Azure IoT Central om uw Azure IoT Central-oplossing te definiëren. U kunt de gebruikersinterface gebruiken om:

- De typen apparaten te definiëren die met uw oplossing verbinding maken.
- De regels en acties voor uw apparaten te configureren.
- De gebruikersinterface aan te passen voor een _operator_ die uw oplossing gebruikt.

Als _operator_ gebruikt u de gebruikersinterface van Azure IoT Central om uw Azure IoT Central-oplossing te beheren. U kunt de gebruikersinterface gebruiken om:

- Uw apparaten te controleren.
- Uw apparaten te configureren.
- Problemen met uw apparaten op te lossen.
- Nieuwe apparaten inrichten.

## <a name="use-the-left-pane"></a>Het linkerdeel venster gebruiken

Gebruik het linkerdeel venster om toegang te krijgen tot de verschillende gebieden van de toepassing. U kunt de navigatie balk uitvouwen of samen vouwen door **<** of **>** te selecteren:

:::row:::
  :::column span="":::
      ![linkerdeel venster](media/overview-iot-central-tour/navigationbar.png)
  :::column-end:::
  :::column span="2":::
     Het **dash board** van uw toepassing wordt weer gegeven. Als opbouw functie kunt u het dash board aanpassen voor uw Opera tors. Gebruikers kunnen ook hun eigen Dash boards maken.
    
     **Device Explorer** geeft een lijst van de gesimuleerde en werkelijke apparaten die zijn gekoppeld aan elke apparaatprofiel in de toepassing. Als operator gebruikt u de **Device Explorer** om uw verbonden apparaten te beheren.
    
     Met **Apparaatsets** kunt u Apparaatsets weer geven en maken. Als operator kunt u de apparaatsets maken als een logische verzameling apparaten die door een query wordt gespecificeerd.
    
     **Analytics** toont analyses die zijn afgeleid van de telemetrie van apparaten voor apparaat-en Apparaatsets. Als operator kunt u uw apparaatgegevens aangepast weergeven voor meer inzicht in uw toepassing.
    
     Met **taken** maakt u Bulk beheer mogelijk door taken te maken en uit te voeren om updates op schaal uit te voeren.
    
     Met **device sjablonen** worden de hulpprogram ma's weer gegeven die een opbouw functie gebruikt voor het maken en beheren van sjablonen.
    
     Met **continue gegevens export** kan een beheerder een continue export naar andere Azure-Services configureren, zoals opslag en wacht rijen.
    
     **Beheer** toont de beheer pagina's voor toepassingen waar een beheerder toepassings instellingen, gebruikers en rollen kan beheren.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Zoeken, hulp en ondersteuning

Het bovenste menu wordt op elke pagina weergegeven:

![Werkbalk](media/overview-iot-central-tour/toolbar.png)

- Als u wilt zoeken naar Apparaatinstellingen en apparaten, voert u een **Zoek** waarde in.
- Als u de taal of het thema van de gebruikers interface wilt wijzigen, kiest u het pictogram **instellingen** .
- Kies het **account** pictogram om u af te melden bij de toepassing.
- Als u hulp en ondersteuning nodig hebt, kiest u het keuzemenu **Help** voor een lijst met bronnen. In een proef toepassing omvatten de ondersteunings bronnen toegang tot [Live Chat](howto-show-hide-chat.md).

Voor de gebruikersinterface kunt u kiezen tussen een licht thema of een donker thema:

![Een thema voor de gebruikersinterface kiezen](media/overview-iot-central-tour/themes.png)

> [!NOTE]
> De optie om te kiezen tussen lichte en donkere Thema's is niet beschikbaar als uw beheerder een aangepast thema voor de toepassing heeft geconfigureerd.

## <a name="dashboard"></a>Dashboard

![Dashboard](media/overview-iot-central-tour/homepage.png)

* Het dash board is de eerste pagina die u ziet wanneer u zich aanmeldt bij uw Azure IoT Central-toepassing. Als ontwerper kunt u het toepassings dashboard aanpassen voor andere gebruikers door tegels toe te voegen. Raadpleeg voor meer informatie de zelfstudie [Customize the Azure IoT Central operator's view](tutorial-customize-operator.md) (De operatorweergave van Azure IoT Central aanpassen).

* Als operator kunt u persoonlijke Dash boards maken en scha kelen tussen deze en het standaard dashboard. Zie het artikel [Azure IOT Central persoonlijke Dash boards maken](howto-create-personal-dashboards.md) voor meer informatie.

## <a name="device-explorer"></a>Apparatenverkenner

![Device Explorer-pagina](media/overview-iot-central-tour/explorer.png)

Op de Device Explorer-pagina worden de _apparaten_ in uw Azure IoT Central-toepassing gegroepeerd weergegeven op _apparaatsjabloon_.

* Met een apparaatsjabloon wordt een type apparaat gedefinieerd dat met uw toepassing verbinding kan maken. Raadpleeg voor meer informatie de zelfstudie [Define a new device type in your Azure IoT Central application](tutorial-define-device-type.md) (Een nieuw apparaattype definiëren in uw Azure IoT Central-toepassing).
* Een apparaat vertegenwoordigt een echt of een gesimuleerd apparaat in uw toepassing. Raadpleeg voor meer informatie de zelfstudie [Add a new device to your Azure IoT Central application](tutorial-add-device.md) (Een nieuw apparaat toevoegen aan uw Azure IoT Central-toepassing).

## <a name="device-sets"></a>Apparaatsets

![Apparaatsets-pagina](media/overview-iot-central-tour/devicesets.png)

Op de pagina _Apparaatsets_ worden de apparaatsets getoond die door de maker zijn gemaakt. Een apparaatset is een verzameling verwante apparaten. Een maker definieert een query om de apparaten te identificeren die in een apparaatset zijn opgenomen. U gebruikt apparaatsets wanneer u de analytische gegevens in uw toepassing aanpast. Raadpleeg voor meer informatie het artikel [Use device sets in your Azure IoT Central application](howto-use-device-sets.md) (Apparaatsets gebruiken in uw Azure IoT Central-toepassing).

## <a name="analytics"></a>Analyse

![Analysepagina](media/overview-iot-central-tour/analytics.png)

De pagina Analyse bevat diagrammen met informatie over hoe de apparaten die met uw toepassing zijn verbonden, zich gedragen. Een operator gebruikt deze pagina om problemen met de verbonden apparaten te controleren en te onderzoeken. De maker kan de diagrammen die op deze pagina worden weergegeven, definiëren. Raadpleeg voor meer informatie het artikel [Create custom analytics for your Azure IoT Central application](howto-use-device-sets.md) (Aangepaste analyses maken voor uw Azure IoT Central-toepassing).

## <a name="jobs"></a>Taken

![Pagina Taken](media/overview-iot-central-tour/jobs.png)

Op de pagina taken kunt u bulk bewerkingen voor Apparaatbeheer uitvoeren op uw apparaten. De opbouwfunctie maakt gebruik van deze pagina om apparaateigenschappen, instellingen en opdrachten bij te werken. Zie voor meer informatie, het artikel [Een taak uitvoeren](howto-run-a-job.md).

## <a name="device-templates"></a>Apparaatinstellingen

![De pagina Apparaatsjablonen](media/overview-iot-central-tour/templates.png)

De opbouwfunctie gebruikt de pagina Apparaatsjablonen om de apparaatsjablonen in de toepassing te maken en beheren. Een sjabloon voor apparaten geeft de kenmerken van het apparaat aan, zoals:

- Telemetrie-, status-en gebeurtenis metingen.
- Instellingen en eigenschappen.
- Opdrachten.
- Regels op basis van gebeurtenissen of telemetrie-waarden.

Raadpleeg voor meer informatie de zelfstudie [Define a new device type in your Azure IoT Central application](tutorial-define-device-type.md) (Een nieuw apparaattype definiëren in uw Azure IoT Central-toepassing).

## <a name="continuous-data-export"></a>Voortdurende gegevensexport

![Pagina Continue gegevensexport](media/overview-iot-central-tour/export.png)

De pagina doorlopend gegevens exporteren is de locatie waar een beheerder bepaalt hoe gegevens worden gestreamd, zoals telemetrie, van de toepassing. Andere services kunnen de geëxporteerde gegevens opslaan of die gebruiken voor analysedoeleinden. Zie het artikel [Exporteren van gegevens in Azure IoT Central](howto-export-data-blob-storage.md) voor meer informatie.

## <a name="administration"></a>Beheer

![Beheerpagina](media/overview-iot-central-tour/administration.png)

De beheer pagina bevat koppelingen naar de hulpprogram ma's die een beheerder gebruikt, zoals het definiëren van gebruikers en rollen in de toepassing, en het aanpassen van de gebruikers interface. Raadpleeg voor meer informatie het artikel [Administer your Azure IoT Central application](howto-administer.md) (Uw Azure IoT Central-toepassing beheren).

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van Azure IoT Central en bekend bent met de indeling van de gebruikersinterface, is de voorgestelde vervolgstap om de snelstart [Create an Azure IoT Central application](quick-deploy-iot-central.md) (Een Azure IoT Central-toepassing maken) te voltooien.
