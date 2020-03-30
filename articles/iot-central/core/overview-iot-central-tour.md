---
title: Kennismaking met de gebruikersinterface van Azure IoT Central | Microsoft Docs
description: Maak kennis met de belangrijkste gebieden van de Azure IoT Central UI die u gebruikt om uw IoT-oplossing te maken, te beheren en te gebruiken.
author: lmasieri
ms.author: lmasieri
ms.date: 12/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: b905b1e86810b25c4c94072d6cd414b993e2a883
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77426007"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Kennismaking met de gebruikersinterface van Azure IoT Central



In dit artikel maakt u kennis met de gebruikersinterface van Microsoft Azure IoT Central. U kunt de gebruikersinterface gebruiken om een Azure IoT Central-oplossing te maken en de daarmee verbonden apparaten te beheren en gebruiken.

Als _oplossingsbouwer_gebruikt u de Azure IoT Central UI om uw Azure IoT Central-oplossing te definiëren. U kunt de gebruikersinterface gebruiken om:

* De typen apparaten te definiëren die met uw oplossing verbinding maken.
* De regels en acties voor uw apparaten te configureren. 
* De gebruikersinterface aan te passen voor een _operator_ die uw oplossing gebruikt.

Als _operator_ gebruikt u de gebruikersinterface van Azure IoT Central om uw Azure IoT Central-oplossing te beheren. U kunt de gebruikersinterface gebruiken om:

* Uw apparaten te controleren.
* Uw apparaten te configureren.
* Problemen met uw apparaten op te lossen.
* Nieuwe apparaten inrichten.

## <a name="iot-central-homepage"></a>IoT Central-startpagina

De [IoT Central-startpagina](https://aka.ms/iotcentral-get-started) pagina is de plek waar u meer te weten komen over het laatste nieuws en functies beschikbaar op IoT Central, nieuwe toepassingen maken en uw bestaande toepassing zien en starten.

> [!div class="mx-imgBorder"]
> ![IoT Central-startpagina](media/overview-iot-central-tour/iot-central-homepage-pnp.png)

### <a name="create-an-application"></a>Een app maken

In de sectie Bouwen u bladeren door de lijst met brancherelevante IoT Central-sjablonen om u te helpen snel aan de slag te gaan of helemaal opnieuw te beginnen met een aangepaste app-sjabloon.  
> [!div class="mx-imgBorder"]
> ![IoT Central-buildpagina](media/overview-iot-central-tour/iot-central-build-pnp.png)

Zie snel aan de slag met [de toepassing Een Azure IoT Central](quick-deploy-iot-central.md) maken voor meer informatie.

### <a name="launch-your-application"></a>Uw toepassing starten

U uw IoT Central-toepassing starten door naar de URL te gaan die u of uw oplossingsbouwer kiest tijdens het maken van apps. U ook een lijst zien van alle toepassingen waartoe u toegang hebt in de [IoT Central-appmanager.](https://aka.ms/iotcentral-apps)

> [!div class="mx-imgBorder"]
> ![IoT Central-appmanager](media/overview-iot-central-tour/app-manager-pnp.png)

## <a name="navigate-your-application"></a>Navigeren door uw toepassing

Als u eenmaal in uw IoT-toepassing bent, gebruikt u het linkerdeelvenster om toegang te krijgen tot de verschillende gebieden. U het linkerdeelvenster uitvouwen of samenvouwen door het pictogram met drie gevoerde teksten boven aan het deelvenster te selecteren:

> [!NOTE]
> De items die u in het linkerdeelvenster ziet, zijn afhankelijk van uw gebruikersrol. Meer informatie over [het beheren van gebruikers en rollen](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      > [!div class="mx-imgBorder"]
      > ![Linkerdeelvenster](media/overview-iot-central-tour/navigationbar-pnp.png)
  :::column-end:::
  :::column span="2":::
     **In het dashboard** wordt het dashboard van uw toepassing weergegeven. Als *oplossingsbouwer*u het globale dashboard voor uw operators aanpassen. Afhankelijk van hun gebruikersrol kunnen operators ook hun eigen persoonlijke dashboards maken.
     
     **Met apparaten** u uw verbonden apparaten beheren - echt en gesimuleerd.

     **Met apparaatgroepen** u logische verzamelingen van apparaten weergeven en maken die door een query zijn opgegeven. U deze query opslaan en apparaatgroepen via de toepassing gebruiken om bulkbewerkingen uit te voeren.

     **Met regels** u regels maken en bewerken om uw apparaten te controleren. Regels worden geëvalueerd op basis van apparaattelemetrie en triggeren aanpasbare acties.

     **Met Analytics** u aangepaste weergaven maken bovenop apparaatgegevens om inzichten uit uw toepassing te halen.

     **Met Taken** u uw apparaten op schaal beheren door bulkbewerkingen uit te voeren.

     **Apparaatsjablonen** is de plek waar u de kenmerken van de apparaten die verbinding maken met uw toepassing maakt en beheert.

     **Met gegevensexport** u een continue export naar externe services configureren, zoals opslag en wachtrijen.

     **Beheer** is de plek waar u de instellingen, aanpassingen, facturering, gebruikers en rollen van uw toepassing beheren.

     **Met IoT Central** kunnen *beheerders* terugspringen naar de app-manager van IoT Central.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Zoeken, helpen, thema's en ondersteuning

Het bovenste menu wordt op elke pagina weergegeven:

> [!div class="mx-imgBorder"]
> ![Werkbalk](media/overview-iot-central-tour/toolbar-pnp.png)

* Als u wilt zoeken naar apparaatsjablonen en -apparaten, voert u een **zoekwaarde** in.
* Als u de gebruikersinterfacetaal of -thema wilt wijzigen, kiest u het pictogram **Instellingen.** Meer informatie over [het beheren van uw toepassingsvoorkeuren](howto-manage-preferences.md)
* Als u zich wilt afmelden bij de toepassing, kiest u het pictogram **Account.**
* Als u hulp en ondersteuning nodig hebt, kiest u het keuzemenu **Help** voor een lijst met bronnen. In een toepassing op het gratis prijsplan bevatten de ondersteuningsbronnen toegang tot [live chat.](howto-show-hide-chat.md)

Voor de gebruikersinterface kunt u kiezen tussen een licht thema of een donker thema:

> [!NOTE]
> De optie om te kiezen tussen lichte en donkere thema's is niet beschikbaar als uw beheerder een aangepast thema voor de toepassing heeft geconfigureerd.

> [!div class="mx-imgBorder"]
> ![Een thema voor de gebruikersinterface kiezen](media/overview-iot-central-tour/themes-pnp.png)

### <a name="dashboard"></a>Dashboard
> [!div class="mx-imgBorder"]
> ![Dashboard](media/overview-iot-central-tour/dashboard-pnp.png)

* Het dashboard is de eerste pagina die u ziet wanneer u zich aanmeldt bij uw Azure IoT Central-toepassing. Als *oplossingsbouwer*u meerdere globale toepassingsdashboards voor andere gebruikers maken en aanpassen. Meer informatie over [het toevoegen van tegels aan uw dashboard](howto-add-tiles-to-your-dashboard.md)

* Als *operator*u, als uw gebruikersrol dit toelaat, persoonlijke dashboards maken om te controleren waar u om geeft. Zie het artikel [Voor het maken van azure IoT Central persoonlijke dashboards.](howto-create-personal-dashboards.md)

### <a name="devices"></a>Apparaten

> [!div class="mx-imgBorder"]
> ![Pagina Apparaten](media/overview-iot-central-tour/devices-pnp.png)

Op de Device Explorer-pagina worden de _apparaten_ in uw Azure IoT Central-toepassing gegroepeerd weergegeven op _apparaatsjabloon_. 

* Met een apparaatsjabloon wordt een type apparaat gedefinieerd dat met uw toepassing verbinding kan maken.
* Een apparaat vertegenwoordigt een echt of een gesimuleerd apparaat in uw toepassing.

Zie de [snelstart van uw apparaten controleren](./quick-monitor-devices.md) voor meer informatie. 

### <a name="device-groups"></a>Device groups

> [!div class="mx-imgBorder"]
> ![Pagina Apparaatgroepen](media/overview-iot-central-tour/device-groups-pnp.png)

Apparaatgroep is een verzameling verwante apparaten. Een *oplossingsbouwer* definieert een query om de apparaten te identificeren die zijn opgenomen in een apparaatgroep. U gebruikt apparaatgroepen om bulkbewerkingen uit te voeren in uw toepassing. Zie [apparaatgroepen gebruiken in uw Azure IoT Central-toepassingsartikel](tutorial-use-device-groups.md) voor meer informatie.

### <a name="rules"></a>Regels
> [!div class="mx-imgBorder"]
> ![Pagina Regels](media/overview-iot-central-tour/rules-pnp.png)

Op de pagina Regels u regels definiëren op basis van de telemetrie, status of gebeurtenissen van apparaten. Wanneer een regel wordt geactiveerd, kan deze een of meer acties activeren, zoals het verzenden van een e-mail, het melden van een extern systeem via webhook-waarschuwingen, enz. Zie de zelfstudie [Regels configureren](tutorial-create-telemetry-rules.md) voor meer informatie. 

### <a name="analytics"></a>Analyse

> [!div class="mx-imgBorder"]
> ![Analysepagina](media/overview-iot-central-tour/analytics-pnp.png)

Met de analyses u aangepaste weergaven maken bovenop apparaatgegevens om inzichten uit uw toepassing te halen. Zie analyse maken [voor uw Azure IoT Central-toepassingsartikel voor](howto-create-analytics.md) meer informatie.

### <a name="jobs"></a>Taken

> [!div class="mx-imgBorder"]
> ![Pagina Taken](media/overview-iot-central-tour/jobs-pnp.png)

Op de pagina Taken u bulkapparaatbeheeruitvoeren op uw apparaten. U apparaateigenschappen, -instellingen en -opdrachten tegen apparaatgroepen bijwerken. Zie voor meer informatie, het artikel [Een taak uitvoeren](howto-run-a-job.md).

### <a name="device-templates"></a>Apparaatsjablonen

> [!div class="mx-imgBorder"]
> ![Pagina Apparaatsjablonen](media/overview-iot-central-tour/templates-pnp.png)

De opbouwfunctie gebruikt de pagina Apparaatsjablonen om de apparaatsjablonen in de toepassing te maken en beheren. Een apparaatsjabloon geeft kenmerken van apparaten op, zoals:

* Telemetrie, status- en gebeurtenismetingen
* Eigenschappen
* Opdrachten
* Weergaven

De *oplossingsbouwer* kan ook formulieren en dashboards maken die operators kunnen gebruiken om apparaten te beheren.

Raadpleeg voor meer informatie de zelfstudie [Define a new device type in your Azure IoT Central application](howto-set-up-template.md) (Een nieuw apparaattype definiëren in uw Azure IoT Central-toepassing). 

### <a name="data-export"></a>Gegevensexport
> [!div class="mx-imgBorder"]
> ![Pagina Gegevensexporteren](media/overview-iot-central-tour/export-pnp.png)

Met gegevensexport u gegevensstromen, zoals telemetrie, instellen van de toepassing naar externe systemen. Zie het artikel [Exporteren van gegevens in Azure IoT Central](./howto-export-data.md) voor meer informatie.

### <a name="administration"></a>Beheer
> [!div class="mx-imgBorder"]
> ![Beheerpagina](media/overview-iot-central-tour/administration-pnp.png)

Op de beheerpagina u uw IoT Central-toepassing configureren en aanpassen. Hier u uw toepassingsnaam, URL, themathema wijzigen, gebruikers en rollen beheren, API-tokens maken en uw toepassing exporteren. Raadpleeg voor meer informatie het artikel [Administer your Azure IoT Central application](howto-administer.md) (Uw Azure IoT Central-toepassing beheren).

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van Azure IoT Central en bekend bent met de indeling van de gebruikersinterface, is de voorgestelde vervolgstap om de snelstart [Create an Azure IoT Central application](quick-deploy-iot-central.md) (Een Azure IoT Central-toepassing maken) te voltooien.
