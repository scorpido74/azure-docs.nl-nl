---
title: Een service toevoegen aan de gebruikersinterface van de oplossing voor externe bewaking - Azure | Microsoft Documenten
description: In dit artikel ziet u hoe u een nieuwe service toevoegt aan de webgebruikersinterface voor het versnellersweb van de Remote Monitoring-oplossing.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447043"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Een aangepaste service toevoegen aan de webgebruikersinterface voor de versneller van de oplossing voor externe bewaking

In dit artikel ziet u hoe u een nieuwe service toevoegt aan de webgebruikersinterface voor het versnellersweb van de Remote Monitoring-oplossing. Het artikel beschrijft:

- Hoe maak je een lokale ontwikkelingsomgeving voor te bereiden.
- Een nieuwe service toevoegen aan de gebruikersinterface van het web.

De voorbeeldservice in dit artikel bevat de gegevens voor een raster dat met het aangepaste raster toevoegen aan het artikel over de web-to-pagina van [de rasoplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-grid.md) van de oplossing sto toont hoe u toevoegen.

In een React-toepassing werkt een service doorgaans samen met een back-endservice. Voorbeelden in de remote monitoring oplossingversneller zijn services die communiceren met de IoT-hubmanager en configuratiemicroservices.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt uitvoeren, hebt u de volgende software nodig die op uw lokale ontwikkelingsmachine is geïnstalleerd:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Voordat u begint

Voer de stappen in de [pagina Een aangepaste pagina toevoegen uit aan de web-gebruikersinterface](iot-accelerators-remote-monitoring-customize-page.md) van de rasoplossing voor externe bewaking voordat u verdergaat.

## <a name="add-a-service"></a>Een service toevoegen

Als u een service wilt toevoegen aan de webgebruikersinterface, moet u de bronbestanden toevoegen die de service definiëren en enkele bestaande bestanden wijzigen om de webgebruikersinterface op de hoogte te brengen van de nieuwe service.

### <a name="add-the-new-files-that-define-the-service"></a>De nieuwe bestanden toevoegen die de service definiëren

Om u op weg te helpen, bevat de map **src/walkthrough/services** de bestanden die een eenvoudige service definiëren:

**voorbeeldService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Zie [De inleiding tot Reactieve programmering die u hebt gemist](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)voor meer informatie over hoe services worden geïmplementeerd.

**model/voorbeeldModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Kopieer **exampleService.js** naar de **map src/services** en kopieer **voorbeeldModels.js** naar de map **src/services/models.**

Werk het **bestand index.js** bij in de map **src/services** om de nieuwe service te exporteren:

```js
export * from './exampleService';
```

Werk het **bestand index.js** bij in de map **src/services/models** om het nieuwe model te exporteren:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>De gesprekken naar de service vanuit de winkel instellen

Om u op weg te helpen, bevat de map **src/walkthrough/store/reducers** een voorbeeldreducer:

**voorbeeldReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Kopieer **voorbeeldReducer.js** naar de map **src/store/reducers.**

Voor meer informatie over de reducer en **Epics,** zie [redux-waarneembare](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>De middleware configureren

Als u de middleware wilt configureren, voegt u de reducer toe aan het bestand **rootReducer.js** in de map **src/store:**

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

Voeg de epics toe aan het **bestand rootEpics.js** in de **src/store** map:

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over de beschikbare bronnen om u te helpen services toe te voegen of aan te passen in de webgebruikersinterface in de versneller van de oplossing voor externe bewaking.

Nu u een service hebt gedefinieerd, is de volgende stap het [toevoegen van een aangepast raster aan de webgebruikersinterface voor het versnellerssysteem voor externe bewaking](iot-accelerators-remote-monitoring-customize-grid.md) met gegevens die door de service zijn geretourneerd.

Zie [Remote Monitoring architecture](iot-accelerators-remote-monitoring-sample-walkthrough.md)voor meer conceptuele informatie over de remote monitoring oplossingsversneller.
