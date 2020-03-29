---
title: Een raster toevoegen aan de gebruikersinterface van de oplossing voor externe bewaking - Azure | Microsoft Documenten
description: In dit artikel ziet u hoe u een nieuwe gid toevoegt op een pagina in de webgebruikersinterface voor het versnellersweb op afstand.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.openlocfilehash: a24cb7f39ccb8ea07d4dde2869dc7c924b91983a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447094"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Een aangepast raster toevoegen aan de webgebruikersinterface voor de versneller van de oplossing voor externe bewaking

In dit artikel ziet u hoe u een nieuw raster toevoegt aan een pagina in de webgebruikersinterface voor het versnellersweb op afstand. Het artikel beschrijft:

- Hoe maak je een lokale ontwikkelingsomgeving voor te bereiden.
- Een nieuw raster toevoegen aan een pagina in de webgebruikersinterface.

In het voorbeeldraster in dit artikel worden de gegevens van de service weergegeven die de aangepaste service toevoegen aan het artikel over de web-to-functie van [de rasoplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-service.md) toont u hoe u deze toevoegt.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt uitvoeren, hebt u de volgende software nodig die op uw lokale ontwikkelingsmachine is geïnstalleerd:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Voordat u begint

Voer de stappen in de volgende artikelen uit voordat u verdergaat:

- [Voeg een aangepaste pagina toe aan de webgebruikersinterface voor de versneller van de oplossing voor externe bewaking.](iot-accelerators-remote-monitoring-customize-page.md)
- [Een aangepaste service toevoegen aan de webgebruikersinterface voor de versneller van de oplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Een raster toevoegen

Als u een raster wilt toevoegen aan de webgebruikersinterface, moet u de bronbestanden toevoegen die het raster definiëren en enkele bestaande bestanden wijzigen om de webgebruikersinterface op de hoogte te houden van de nieuwe component.

### <a name="add-the-new-files-that-define-the-grid"></a>De nieuwe bestanden toevoegen die het raster definiëren

Om u op weg te helpen, bevat de map **src/walkthrough/components/pages/pageWithGrid/exampleGrid** de bestanden die een raster definiëren:

**voorbeeldGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**voorbeeldGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Kopieer de **src/walkthrough/components/pages/pageWithGrid/exampleGrid** map naar de **src/components/pages/example** folder.

### <a name="add-the-grid-to-the-page"></a>Het raster aan de pagina toevoegen

Wijzig de **src/componenten/pagina's/example/basicPage.container.js** als volgt om de servicedefinities te importeren:

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

Wijzig de **src/componenten/pagina's/example/basicPage.js** als volgt om het raster toe te voegen:

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

Wijzig de **src/componenten/pagina's/example/basicPage.test.js** als volgt om de tests bij te werken:

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>Het raster testen

Als de webgebruikersinterface nog niet lokaal wordt uitgevoerd, voert u de volgende opdracht uit in de hoofdtekst van uw lokale kopie van de opslagplaats:

```cmd/sh
npm start
```

Met de vorige opdracht wordt [http://localhost:3000/dashboard](http://localhost:3000/dashboard)de gebruikersinterface lokaal uitgevoerd op . Navigeer naar de **voorbeeldpagina** om de rasterweergavegegevens van de service te bekijken.

## <a name="select-rows"></a>Rijen selecteren

Er zijn twee opties om een gebruiker in staat te stellen rijen in het raster te selecteren:

### <a name="hard-select-rows"></a>Moeilijk geselecteerde rijen

Als een gebruiker op meerdere rijen tegelijk moet reageren, gebruikt u selectievakjes in rijen:

1. Schakel een harde selectie van rijen in door een **selectievakjekolom** toe te voegen aan de **kolomDefs** die aan het raster worden geleverd. **checkboxColumn** is gedefinieerd in **/src/components/shared/pcsGrid/pcsGrid.js:**

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Als u toegang wilt krijgen tot de geselecteerde items, krijgt u een verwijzing naar de interne grid-API:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Geef contextknoppen op de pagina wanneer een rij in het raster hard is geselecteerd:

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. Wanneer op een contextknop wordt geklikt, krijgt u de moeilijk geselecteerde items om uw werk aan te doen:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Rijen met zachte selectie

Als de gebruiker slechts op één rij hoeft te werken, configureert u een koppeling voor soft-select voor een of meer kolommen in de **kolomDefs**.

1. Voeg **in voorbeeldGridConfig.js** **SoftSelectLinkRenderer** toe als **cellRendererFramework** voor een **kolomDef**.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. Wanneer op een koppeling met soft-select wordt geklikt, wordt de gebeurtenis **onSoftSelectChange** geactiveerd. Voer de gewenste actie uit voor die rij, zoals het openen van een flyout met details. In dit voorbeeld wordt eenvoudig naar de console geschreven:

    ```js
    onSoftSelectChange = (rowId, rowData) => {
      //Note: only the Id is reliable, rowData may be out of date
      const { onSoftSelectChange } = this.props;
      if (rowId) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', rowId);
        this.setState({ softSelectedId: rowId });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(rowId, rowData);
      }
    }
    ```

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over de beschikbare bronnen om u te helpen pagina's toe te voegen of aan te passen in de webgebruikersinterface in de versneller van de oplossing voor externe bewaking.

Nu u een raster hebt gedefinieerd, is de volgende stap het [toevoegen van een aangepaste flyout aan de web-gebruikersinterface van de externe bewakingsoplossing](iot-accelerators-remote-monitoring-customize-flyout.md) die wordt weergegeven op de voorbeeldpagina.

Zie [Remote Monitoring architecture](iot-accelerators-remote-monitoring-sample-walkthrough.md)voor meer conceptuele informatie over de remote monitoring oplossingsversneller.
