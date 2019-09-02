---
title: Kennismaking met de gebruikersinterface van Azure IoT Central | Microsoft Docs
description: Als maker is het nuttig om vertrouwd te raken met de belangrijkste gebieden van de gebruikersinterface van Azure IoT Central, dat u gebruikt om een IoT-oplossing te maken.
author: dominicbetts
ms.author: dobett
ms.date: 07/05/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 4a0c9d16474ddf032ff88382bc240713bc734ff8
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211901"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-preview-features"></a>Volg een rond leiding door de Azure IoT Central-gebruikers interface (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

In dit artikel maakt u kennis met de gebruikersinterface van Microsoft Azure IoT Central. U kunt de gebruikersinterface gebruiken om een Azure IoT Central-oplossing te maken en de daarmee verbonden apparaten te beheren en gebruiken.

Als _maker_ gebruikt u de gebruikersinterface van Azure IoT Central om uw Azure IoT Central-oplossing te definiëren. U kunt de gebruikersinterface gebruiken om:

* De typen apparaten te definiëren die met uw oplossing verbinding maken.
* De regels en acties voor uw apparaten te configureren.
* De gebruikersinterface aan te passen voor een _operator_ die uw oplossing gebruikt.

Als _operator_ gebruikt u de gebruikersinterface van Azure IoT Central om uw Azure IoT Central-oplossing te beheren. U kunt de gebruikersinterface gebruiken om:

* Uw apparaten te controleren.
* Uw apparaten te configureren.
* Problemen met uw apparaten op te lossen.
* Nieuwe apparaten inrichten.

## <a name="use-the-left-navigation-menu"></a>Gebruik het linkernavigatiemenu

Gebruik het navigatie menu aan de linkerkant om toegang te krijgen tot de verschillende gebieden van de toepassing. U kunt de navigatie balk uitvouwen of samen **<** vouwen **>** door te selecteren of:

:::row:::
  :::column span="":::
      ![Left navigation menu](media/overview-iot-central-tour-pnp/navigationbar.png)
  :::column-end:::
  :::column span="2":::

      **Dashboard** displays your application dashboard. As a builder, you can customize the dashboard for your operators. Users can also create their own  dashboards.
    
      **Devices** lists the simulated and real devices associated with each device template in the application. As an operator, you use the **Device Explorer** to manage your connected devices.
    
      **Device groups** lets you view and create device groups. As an operator, you can create device groups as a logical collections of devices specified by a query.

      **Rules** lets you edit rules that fire based on device telemetry and trigger customizable actions.
    
      **Analytics** shows analytics derived from device telemetry for devices and device groups. As an operator, you can create custom views on top of device data to derive insights from your application.
    
      **Jobs** enables bulk device management by having you create and run jobs to update your devices at scale.
    
      **Device templates** shows the tools a builder uses to create and manage device templates.
    
      **Data export** enables an administrator to configure a continuous export to other Azure services such as storage and queues.
    
      **Administration** shows the application administration pages where an administrator can manage application settings, users, and roles.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Zoeken, hulp en ondersteuning

Het bovenste menu wordt op elke pagina weergegeven:

![Werkbalk](media/overview-iot-central-tour-pnp/toolbar.png)

* Als u wilt zoeken naar Apparaatinstellingen en apparaten, voert u een **Zoek** waarde in.
* Als u de taal of het thema van de gebruikers interface wilt wijzigen, kiest u het pictogram **instellingen** .
* Kies het **account** pictogram om u af te melden bij de toepassing.
* Als u hulp en ondersteuning nodig hebt, kiest u het keuzemenu **Help** voor een lijst met bronnen. In een proef toepassing omvatten de ondersteunings bronnen toegang tot [Live Chat](howto-show-hide-chat.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Voor de gebruikersinterface kunt u kiezen tussen een licht thema of een donker thema:

![Een thema voor de gebruikersinterface kiezen](media/overview-iot-central-tour-pnp/themes.png)

> [!NOTE]
> De optie om te kiezen tussen lichte en donkere Thema's is niet beschikbaar als uw beheerder een aangepast thema voor de toepassing heeft geconfigureerd.

## <a name="dashboard"></a>Dashboard

![Dashboard](media/overview-iot-central-tour-pnp/homepage.png)

* Het dash board is de eerste pagina die u ziet wanneer u zich aanmeldt bij uw Azure IoT Central-toepassing. Als ontwerper kunt u het toepassings dashboard aanpassen voor andere gebruikers door tegels toe te voegen. Zie de zelf studie [een sjabloon voor een apparaat instellen](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) voor meer informatie.

* Als operator kunt u persoonlijke Dash boards maken en scha kelen tussen deze en het standaard dashboard. Zie het artikel [persoonlijke Dash boards maken en beheren](howto-personalize-dashboard.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) voor meer informatie.

## <a name="devices"></a>Apparaten

![Pagina apparaten](media/overview-iot-central-tour-pnp/explorer.png)

Op de Device Explorer-pagina worden de _apparaten_ in uw Azure IoT Central-toepassing gegroepeerd weergegeven op _apparaatsjabloon_.

* Met een apparaatsjabloon wordt een type apparaat gedefinieerd dat met uw toepassing verbinding kan maken. Raadpleeg voor meer informatie de zelfstudie [Define a new device type in your Azure IoT Central application](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Een nieuw apparaattype definiëren in uw Azure IoT Central-toepassing).
* Een apparaat vertegenwoordigt een echt of een gesimuleerd apparaat in uw toepassing. Raadpleeg voor meer informatie de zelfstudie [Add a new device to your Azure IoT Central application](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Een nieuw apparaat toevoegen aan uw Azure IoT Central-toepassing).

## <a name="device-groups"></a>Apparaatgroepen

![Pagina apparaatgroepen](media/overview-iot-central-tour-pnp/devicesets.png)

Op de pagina _Apparaatgroepen_ worden de apparaatgroepen weer gegeven die zijn gemaakt door de opbouw functie. Een apparaatgroep is een verzameling van gerelateerde apparaten. Een Builder definieert een query voor het identificeren van de apparaten die zijn opgenomen in een apparaatgroep. U gebruikt apparaatgroepen wanneer u de analyse in uw toepassing aanpast. Zie voor meer informatie het artikel apparaatgroepen [gebruiken in uw Azure IOT Central-toepassing](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="rules"></a>Regels

![Pagina regels](media/overview-iot-central-tour-pnp/rules.png)

Op de pagina regels kunt u regels definiëren op basis van telemetrie, Apparaatstatus of faxgebeurtenissen. Wanneer een regel wordt geactiveerd, kan deze een actie activeren, zoals het verzenden van een e-mail bericht naar een operator. De opbouw functie gebruikt deze pagina om regels te maken en te beheren. Zie voor meer informatie de zelf studie [regels en acties voor uw apparaten configureren in Azure IOT Central](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="analytics"></a>Analyse

![Analysepagina](media/overview-iot-central-tour-pnp/analytics.png)

De pagina Analyse bevat diagrammen met informatie over hoe de apparaten die met uw toepassing zijn verbonden, zich gedragen. Een operator gebruikt deze pagina om problemen met de verbonden apparaten te controleren en te onderzoeken. De maker kan de diagrammen die op deze pagina worden weergegeven, definiëren. Raadpleeg voor meer informatie het artikel [Create custom analytics for your Azure IoT Central application](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Aangepaste analyses maken voor uw Azure IoT Central-toepassing).

## <a name="jobs"></a>Taken

![Pagina Taken](media/overview-iot-central-tour-pnp/jobs.png)

Op de pagina taken kunt u bulk bewerkingen voor Apparaatbeheer uitvoeren op uw apparaten. De opbouwfunctie maakt gebruik van deze pagina om apparaateigenschappen, instellingen en opdrachten bij te werken. Zie voor meer informatie, het artikel [Een taak uitvoeren](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="device-templates"></a>Apparaatinstellingen

![Pagina met Apparaatinstellingen](media/overview-iot-central-tour-pnp/templates.png)

De opbouwfunctie gebruikt de pagina Apparaatsjablonen om de apparaatsjablonen in de toepassing te maken en beheren. Een sjabloon voor apparaten geeft de kenmerken van het apparaat aan, zoals:

* Telemetrie-, status-en gebeurtenis metingen.
* Eigenschappen.
* Opdrachten.

De opbouw functie kan ook formulieren en dash boards maken die door Opera tors kunnen worden gebruikt om apparaten te beheren.

Raadpleeg voor meer informatie de zelfstudie [Define a new device type in your Azure IoT Central application](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Een nieuw apparaattype definiëren in uw Azure IoT Central-toepassing).

## <a name="data-export"></a>Gegevensexport

![Pagina voor het exporteren van gegevens](media/overview-iot-central-tour-pnp/export.png)

De pagina voor het exporteren van gegevens is de locatie waar een beheerder bepaalt hoe gegevens worden gestreamd, zoals telemetrie, van de toepassing. Andere services kunnen de geëxporteerde gegevens opslaan of die gebruiken voor analysedoeleinden. Zie het artikel [Exporteren van gegevens in Azure IoT Central](howto-export-data.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) voor meer informatie.

## <a name="administration"></a>Beheer

![Beheerpagina](media/overview-iot-central-tour-pnp/administration.png)

De beheer pagina bevat koppelingen naar de hulpprogram ma's die een beheerder gebruikt, zoals het definiëren van gebruikers en rollen in de toepassing, en het aanpassen van de gebruikers interface. Raadpleeg voor meer informatie het artikel [Administer your Azure IoT Central application](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Uw Azure IoT Central-toepassing beheren).

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van Azure IoT Central en bekend bent met de indeling van de gebruikersinterface, is de voorgestelde vervolgstap om de snelstart [Create an Azure IoT Central application](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Een Azure IoT Central-toepassing maken) te voltooien.
