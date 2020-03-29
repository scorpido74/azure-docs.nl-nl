---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67712530"
---
Met `ApplicationInsights` de instelling u telemetrieondersteuning azure [application insights](https://docs.microsoft.com/azure/application-insights) toevoegen aan uw container. Application Insights biedt een diepgaande monitoring van uw container. U uw container eenvoudig controleren op beschikbaarheid, prestaties en gebruik. U ook snel fouten in uw container identificeren en diagnosticeren.

In de volgende tabel worden de `ApplicationInsights` configuratie-instellingen beschreven die onder de sectie worden ondersteund.

|Vereist| Name | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Nee| `InstrumentationKey` | Tekenreeks | De instrumentatiesleutel van de instantie Application Insights waarnaar telemetriegegevens voor de container worden verzonden. Zie [Application Insights voor ASP.NET Core voor](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)meer informatie. <br><br>Voorbeeld:<br>`InstrumentationKey=123456789`|

