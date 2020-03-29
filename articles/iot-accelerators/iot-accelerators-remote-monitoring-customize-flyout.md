---
title: Een flyout toevoegen aan de gebruikersinterface van de externe bewakingsoplossing - Azure | Microsoft Documenten
description: In dit artikel ziet u hoe u een nieuwe flyout toevoegt op een pagina in de webgebruikersinterface voor het versnellersweb op afstand.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447111"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Een aangepaste flyout toevoegen aan de webgebruikers interface voor de webserver van de oplossing voor externe bewaking

In dit artikel ziet u hoe u een nieuwe flyout toevoegt aan een pagina in de webgebruikersinterface voor het versnellersweb op afstand. Het artikel beschrijft:

- Hoe maak je een lokale ontwikkelingsomgeving voor te bereiden.
- Een nieuwe flyout toevoegen aan een pagina in de webgebruikersinterface.

In het voorbeeld flyout in dit artikel wordt op de pagina met het raster weergegeven dat het [aangepaste raster toevoegen aan het artikel over de web-aan-gebruikersinterface van de rasoplossing op afstand,](iot-accelerators-remote-monitoring-customize-grid.md) u laat zien hoe u deze toevoegen.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt uitvoeren, hebt u de volgende software nodig die op uw lokale ontwikkelingsmachine is geïnstalleerd:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Voordat u begint

Voer de stappen in de volgende artikelen uit voordat u verdergaat:

- [Voeg een aangepaste pagina toe aan de webgebruikersinterface voor de versneller van de oplossing voor externe bewaking.](iot-accelerators-remote-monitoring-customize-page.md)
- [Een aangepaste service toevoegen aan de webgebruikersinterface voor de versneller van de oplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-service.md)
- [Een aangepast raster toevoegen aan de webgebruikersinterface voor de versneller van de oplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Een flyout toevoegen

Als u een flyout wilt toevoegen aan de webgebruikersinterface, moet u de bronbestanden toevoegen die de flyout definiëren en een aantal bestaande bestanden wijzigen om de webgebruikersinterface bewust te maken van de nieuwe component.

### <a name="add-the-new-files-that-define-the-flyout"></a>De nieuwe bestanden toevoegen die de flyout definiëren

Om u op weg te helpen, bevat de **src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout** map de bestanden die een flyout definiëren:

**voorbeeldFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**voorbeeldFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Kopieer de **src/walkthrough/components/pages/pageWithFlyout/flyouts** map naar de **src/components/pages/example** folder.

### <a name="add-the-flyout-to-the-page"></a>De flyout toevoegen aan de pagina

Wijzig de **src/componenten/pagina's/example/basicPage.js** om de flyout toe te voegen.

**Btn** toevoegen aan de invoer uit **componenten/gedeelde componenten** en import toevoegen voor **svgs** en **ExampleFlyoutContainer:**

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

Voeg een **const** definitie voor **closedFlyoutState** en voeg deze toe aan de status in de constructor:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Voeg de volgende functies toe aan de klasse **BasicPage:**

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Voeg de volgende **const definities** toe aan de **renderfunctie:**

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Voeg een knop toe om de flyout te openen in het contextmenu:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Voeg wat tekst en de flyout-container toe aan de pagina-inhoud:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Test de flyout

Als de webgebruikersinterface nog niet lokaal wordt uitgevoerd, voert u de volgende opdracht uit in de hoofdtekst van uw lokale kopie van de opslagplaats:

```cmd/sh
npm start
```

Met de vorige opdracht wordt [http://localhost:3000/dashboard](http://localhost:3000/dashboard)de gebruikersinterface lokaal uitgevoerd op . Navigeer naar de **voorbeeldpagina** en klik op **Flyout openen**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over de beschikbare bronnen om u te helpen pagina's toe te voegen of aan te passen in de webgebruikersinterface in de versneller van de oplossing voor externe bewaking.

Nu u een flyout op een pagina hebt gedefinieerd, is de volgende stap het [toevoegen van een paneel aan het dashboard in de webgebruikersinterface voor het versnellerweb van de afstandsbedieningsoplossing.](iot-accelerators-remote-monitoring-customize-panel.md)

Zie [Remote Monitoring architecture](iot-accelerators-remote-monitoring-sample-walkthrough.md)voor meer conceptuele informatie over de remote monitoring oplossingsversneller.
