---
title: Een service toevoegen aan de gebruikers interface van de oplossing voor externe bewaking-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een nieuwe service kunt toevoegen aan de Web-UI van de oplossing voor externe bewaking.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "61447043"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Een aangepaste service toevoegen aan de webgebruikersinterface voor externe bewaking oplossings versneller

In dit artikel wordt beschreven hoe u een nieuwe service kunt toevoegen aan de Web-UI van de oplossing voor externe bewaking. In het artikel worden de volgende informatie beschreven:

- Het voorbereiden van een lokale ontwikkel omgeving.
- Een nieuwe service toevoegen aan de gebruikers interface van het web.

In de voorbeeld service in dit artikel worden de gegevens weer gegeven voor een raster dat door het [toevoegen van een aangepast raster aan het web-UI-artikel van de oplossing voor externe controle](iot-accelerators-remote-monitoring-customize-grid.md)

In een reagerende toepassing communiceert een service doorgaans met een back-end-service. Voor beelden van de oplossings versneller voor externe controle zijn services die communiceren met IoT hub Manager en configuratie micro Services.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt uitvoeren, moet u de volgende software op uw lokale ontwikkel computer installeren:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Voordat u begint

U moet de stappen in het artikel [een aangepaste pagina toevoegen aan het onderdeel voor het oplossen van oplossingen voor externe controle op afstand](iot-accelerators-remote-monitoring-customize-page.md) uitvoeren voordat u doorgaat.

## <a name="add-a-service"></a>Een service toevoegen

Als u een service wilt toevoegen aan de webgebruikersinterface, moet u de bron bestanden toevoegen waarmee de service wordt gedefinieerd en een aantal bestaande bestanden wijzigen om de Web-UI op de hoogte te stellen van de nieuwe service.

### <a name="add-the-new-files-that-define-the-service"></a>De nieuwe bestanden toevoegen waarmee de service wordt gedefinieerd

Om aan de slag te gaan, bevat de map **src/walkthrough/Services** de bestanden die een eenvoudige service definiëren:

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Voor meer informatie over hoe services worden geïmplementeerd, raadpleegt [u de inleiding op reactieve programmering die u mist](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754).

**model/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Kopieer **exampleService.js** naar de map **src/services** en kopieer **exampleModels.js** naar de map **src/Services/modellen** .

Werk het **index.js** -bestand in de map **src/Services** bij om de nieuwe service te exporteren:

```js
export * from './exampleService';
```

Werk het **index.js** -bestand in de map **src/Services/modellen** bij om het nieuwe model te exporteren:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>De aanroepen naar de service in de Store instellen

Om aan de slag te gaan, bevat de map **src/walkthrough/Store/Reducers** een voor beeld van een versmaller:

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Kopieer **exampleReducer.js** naar de map **src/Store/Reducers** .

Zie [Redux-waarneembaar](https://redux-observable.js.org/)voor meer informatie over de Reducer en de **EPICS**.

### <a name="configure-the-middleware"></a>De middleware configureren

Als u de middleware wilt configureren, voegt u de verminderr toe aan het **rootReducer.js** -bestand in de map **src/Store** :

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

Voeg de EPICS toe aan het **rootEpics.js** -bestand in de map **src/Store** :

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

In dit artikel hebt u geleerd over de resources die beschikbaar zijn om u te helpen bij het toevoegen of aanpassen van services in de webgebruikersinterface in de oplossings versneller voor externe controle.

Nu u een service hebt gedefinieerd, is de volgende stap het [toevoegen van een aangepast raster aan de Web-UI van de externe bewakings oplossing](iot-accelerators-remote-monitoring-customize-grid.md) waarmee gegevens worden weer gegeven die door de service worden geretourneerd.

Zie [architectuur voor externe bewaking](iot-accelerators-remote-monitoring-sample-walkthrough.md)voor meer conceptuele informatie over de oplossings versneller voor externe controle.
