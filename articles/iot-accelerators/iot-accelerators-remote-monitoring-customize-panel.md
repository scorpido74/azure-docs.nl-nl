---
title: Een paneel toevoegen aan de gebruikersinterface van de oplossing voor externe bewaking - Azure | Microsoft Documenten
description: In dit artikel ziet u hoe u een nieuw paneel toevoegt aan het dashboard in de webgebruikersinterface voor het versnellersweb op afstand.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 3b855c3bed75945f44b55463bdacd049b7930aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447060"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Een aangepast paneel toevoegen aan het dashboard in de web-gebruikersinterface van de oplossing voor externe bewaking

In dit artikel ziet u hoe u een nieuw paneel toevoegt aan een dashboardpagina in de webgebruikersinterface voor het versnellersweb op afstand. Het artikel beschrijft:

- Hoe maak je een lokale ontwikkelingsomgeving voor te bereiden.
- Een nieuw paneel toevoegen aan een dashboardpagina in de webgebruikersinterface.

Het voorbeeldpaneel in dit artikel wordt weergegeven op de bestaande dashboardpagina.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt uitvoeren, hebt u de volgende software nodig die op uw lokale ontwikkelingsmachine is geïnstalleerd:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Voordat u begint

Voer de stappen in de [aangepaste pagina Toevoegen uit aan het webgebruikersinterfacevan de rasgebruikersinterface](iot-accelerators-remote-monitoring-customize-page.md) van de oplossing voor externe bewaking voordat u verdergaat.

## <a name="add-a-panel"></a>Een deelvenster toevoegen

Als u een deelvenster wilt toevoegen aan de webgebruikersinterface, moet u de bronbestanden toevoegen die het deelvenster definiëren en vervolgens het dashboard wijzigen om het deelvenster weer te geven.

### <a name="add-the-new-files-that-define-the-panel"></a>De nieuwe bestanden toevoegen die het deelvenster definiëren

Om u op weg te helpen, bevat de map **src/walkthrough/components/pages/dashboard/panels/examplePanel** de bestanden die een paneel definiëren, waaronder:

**voorbeeldPanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Kopieer de **src/walkthrough/components/pages/dashboard/panels/examplePanel** map naar de **src/components/pages/dashboard/panels** map.

Voeg de volgende export toe aan het bestand **src/walkthrough/components/pages/dashboard/panels/index.js:**

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Het deelvenster toevoegen aan het dashboard

Wijzig de **src/components/pages/dashboard/dashboard.js** om het paneel toe te voegen.

Voeg het voorbeeldpaneel toe aan de lijst met invoer uit deelvensters:

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Voeg de volgende celdefinitie toe aan het raster in de pagina-inhoud:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Test de flyout

Als de webgebruikersinterface nog niet lokaal wordt uitgevoerd, voert u de volgende opdracht uit in de hoofdtekst van uw lokale kopie van de opslagplaats:

```cmd/sh
npm start
```

Met de vorige opdracht wordt [http://localhost:3000/dashboard](http://localhost:3000/dashboard)de gebruikersinterface lokaal uitgevoerd op . Navigeer naar de **dashboardpagina** om het nieuwe deelvenster weer te geven.

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over de beschikbare bronnen om u te helpen dashboards toe te voegen of aan te passen in de web-gebruikersinterface in de versneller van de oplossing voor externe bewaking.

Zie [Remote Monitoring architecture](iot-accelerators-remote-monitoring-sample-walkthrough.md)voor meer conceptuele informatie over de remote monitoring oplossingsversneller.
