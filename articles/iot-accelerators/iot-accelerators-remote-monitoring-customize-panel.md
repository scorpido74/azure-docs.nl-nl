---
title: Een paneel toevoegen aan de gebruikers interface van de oplossing voor externe bewaking-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een nieuw deel venster aan het dash board toevoegt in de webgebruikersinterface van de oplossing voor externe bewaking.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 694cc83ffce20a8744d7452a8f6d67c9ce23641c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187225"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Een aangepast paneel toevoegen aan het dash board in de Web-UI van de oplossing voor externe bewaking

In dit artikel wordt beschreven hoe u een nieuw deel venster kunt toevoegen aan een dashboard pagina in de webgebruikersinterface van de oplossing voor externe bewaking. In het artikel worden de volgende informatie beschreven:

- Het voorbereiden van een lokale ontwikkel omgeving.
- Een nieuw deel venster toevoegen aan een dashboard pagina in de gebruikers interface van het web.

Het deel venster voor beeld in dit artikel wordt weer gegeven op de bestaande dash board-pagina.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt uitvoeren, moet u de volgende software op uw lokale ontwikkel computer installeren:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Voordat u begint

U moet de stappen in het artikel [een aangepaste pagina toevoegen aan de Web-UI van de oplossing voor externe controle](iot-accelerators-remote-monitoring-customize-page.md) uitvoeren voordat u doorgaat.

## <a name="add-a-panel"></a>Een deelvenster toevoegen

Als u een paneel wilt toevoegen aan de gebruikers interface, moet u de bron bestanden toevoegen die het paneel definiëren en vervolgens het dash board wijzigen om het deel venster weer te geven.

### <a name="add-the-new-files-that-define-the-panel"></a>De nieuwe bestanden toevoegen die het paneel definiëren

Om aan de slag te gaan, bevat de map **src/walkthrough/onderdelen/pages/dash board/panels/examplePanel** de bestanden waarmee een paneel wordt gedefinieerd, waaronder:

**examplePanel. js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Kopieer de map **src/walkthrough/onderdelen/pages/dash board/panels/examplePanel** naar de map **src/Components/pages/dash board/panels** .

Voeg de volgende export toe aan het bestand **src/walkthrough/Components/pages/dash board/panels/index. js** :

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Het deel venster toevoegen aan het dash board

Wijzig de **src/Components/pages/dash board/dash board. js** om het deel venster toe te voegen.

Voeg het deel venster voor beeld toe aan de lijst met invoer uit de deel Vensters:

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

Voeg de volgende definitie van de cel toe aan het raster in de pagina-inhoud:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>De flyout testen

Als de Web-UI niet al lokaal wordt uitgevoerd, voert u de volgende opdracht uit in de hoofdmap van uw lokale kopie van de opslag plaats:

```cmd/sh
npm start
```

Met de vorige opdracht wordt de gebruikers interface `http://localhost:3000/dashboard`lokaal uitgevoerd op. Navigeer naar de pagina **dash board** om het nieuwe deel venster weer te geven.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de resources die beschikbaar zijn om u te helpen bij het toevoegen of aanpassen van Dash boards in de Web-UI in de oplossings versneller voor externe controle.

Zie [architectuur voor externe bewaking](iot-accelerators-remote-monitoring-sample-walkthrough.md)voor meer conceptuele informatie over de oplossings versneller voor externe controle.
