---
title: Invoeg toepassing reageren voor Application Insights java script-SDK
description: De reageer-invoeg toepassing voor Application Insights java script SDK installeren en gebruiken.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 677810c21f9ea6151e2ffe7a4e2b9cdc8473a09f
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227207"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Invoeg toepassing reageren voor Application Insights java script-SDK

De invoeg toepassing reageren voor de Application Insights java script-SDK, maakt het volgende mogelijk:

- Tracering van route wijzigingen
- Gebruiks statistieken van onderdelen reageren

## <a name="getting-started"></a>Aan de slag

NPM-pakket installeren:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Basisgebruik

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>Configuratie

| Name    | Standaard | Beschrijving                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| geschiedenis | null    | Router geschiedenis reageren. Zie de [documentatie over het reagerende router pakket](https://reactrouter.com/web/api/history)voor meer informatie. Voor meer informatie over het openen van het geschiedenis object buiten de onderdelen raadpleegt u de [Veelgestelde vragen over de reagerende router](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>Het bijhouden van onderdelen gebruiken

Als u verschillende reageer onderdelen wilt bijhouden, moet u de functie voor hogere onderdelen gebruiken `withAITracking` .

Hiermee wordt de tijd van de `ComponentDidMount` gebeurtenis gemeten via de `ComponentWillUnmount` gebeurtenis. Om dit nauw keuriger te maken, wordt de tijd waarop de gebruiker niet actief was, afgetrokken `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Als u deze waarde wilt weer geven in de Azure Portal u naar de Application Insights resource moet navigeren, selecteert u het tabblad "metrische gegevens" en configureert u de lege grafieken om de aangepaste metrische naam te tonen "reageren van het onderdeel tijd (seconden)", selecteert u aggregatie (Sum, AVG, enzovoort) van uw metrische waarde en past u Splits "onderdeel naam" toe.

![Scherm afbeelding van een grafiek met de aangepaste metrische tijd van het onderdeel reageren (seconden) "splitsen op" onderdeel naam "](./media/javascript-react-plugin/chart.png)

U kunt ook aangepaste query's uitvoeren om Application Insights gegevens te verdelen over het genereren van rapporten en visualisaties volgens uw vereisten. In de Azure Portal navigeert u naar de Application Insights resource, selecteert u ' analyse ' in het bovenste menu van het tabblad Overzicht en voert u de query uit.

![Scherm afbeelding van aangepaste metrische query resultaten.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Het kan Maxi maal tien minuten duren voordat nieuwe aangepaste metrische gegevens worden weer gegeven in de Azure-Portal.

## <a name="sample-app"></a>Voorbeeld-app

Bekijk de [Application Insights reageren](https://github.com/Azure-Samples/application-insights-react-demo)op de demo.

## <a name="next-steps"></a>Volgende stappen

- Zie de [Application Insights java script SDK-documentatie](javascript.md)voor meer informatie over de Java script-SDK.
- Zie het [overzicht van logboek query's](../../azure-monitor/log-query/log-query-overview.md)voor meer informatie over de Kusto-query taal en het opvragen van gegevens in log Analytics.
