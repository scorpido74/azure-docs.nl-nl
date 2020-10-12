---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67712530"
---
Met deze `ApplicationInsights` instelling kunt u ondersteuning voor [Azure-toepassing Insights](https://docs.microsoft.com/azure/application-insights) -telemetrie toevoegen aan de container. Application Insights biedt uitgebreide controle over uw container. U kunt uw container eenvoudig controleren op Beschik baarheid, prestaties en gebruik. U kunt ook snel fouten in uw container identificeren en onderzoeken.

In de volgende tabel worden de configuratie-instellingen beschreven die in de sectie worden ondersteund `ApplicationInsights` .

|Vereist| Name | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Nee| `InstrumentationKey` | Tekenreeks | De instrumentatie sleutel van het Application Insights exemplaar waarnaar de telemetriegegevens voor de container worden verzonden. Zie [Application Insights voor ASP.net core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)voor meer informatie. <br><br>Voorbeeld:<br>`InstrumentationKey=123456789`|

