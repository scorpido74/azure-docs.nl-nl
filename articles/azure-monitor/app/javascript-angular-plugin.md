---
title: Hoek-invoeg toepassing voor Application Insights java script SDK
description: Een hoek-invoeg toepassing installeren en gebruiken voor Application Insights java script SDK.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: 152ba4b1c8a4e09db0bce759f5b67f577ec5d584
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843891"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Hoek-invoeg toepassing voor Application Insights java script SDK

Met de hoek-invoeg toepassing voor de Application Insights java script SDK kunt u:

- Tracering van router wijzigingen
- Gebruiks statistieken van hoek onderdelen

> [!WARNING]
> De hoek-invoeg toepassing is niet compatibel met ECMAScript 3 (ES3).

## <a name="getting-started"></a>Aan de slag

NPM-pakket installeren:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Basisgebruik

Stel een exemplaar van Application Insights in het onderdeel item in uw app in:

```js
import { Component, OnInit } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin, AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
    private appInsights;
    constructor(
        private router: Router,
        private angularPluginService: AngularPluginService 
    ){
        var angularPlugin = new AngularPlugin();
        this.angularPluginService.init(angularPlugin, this.router);
        this.appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
    }

    ngOnInit() {
        this.appInsights.loadAppInsights();
    }
}

```

Als u de `trackMetric` methode wilt gebruiken om het gebruik van hoek onderdelen bij te houden, voegt u toe `AngularPluginService` als provider in de lijst providers in het `app.module.ts` bestand.

```js
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
    ...
  providers: [ AngularPluginService ],
})
export class AppModule { }
```

Als u de levens duur van een onderdeel wilt bijhouden, roept u `trackMetric` de `ngOnDestroy` methode van dat onderdeel aan. Wanneer het onderdeel wordt vernietigd, wordt een gebeurtenis geactiveerd `trackMetric` die de tijd verzendt die de gebruiker op de pagina en de naam van het onderdeel verkrijgt.

```js
import { Component, OnDestroy, HostListener } from '@angular/core';
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnDestroy {

  constructor(private angularPluginService: AngularPluginService) {}

  @HostListener('window:beforeunload')
  ngOnDestroy() {
    this.angularPluginService.trackMetric();
  }
}
```

## <a name="next-steps"></a>Volgende stappen

- Zie de [Application Insights java script SDK-documentatie](javascript.md) voor meer informatie over de Java script-SDK.
- [Hoek-invoeg toepassing op GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)