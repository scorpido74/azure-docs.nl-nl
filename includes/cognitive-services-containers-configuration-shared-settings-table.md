---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73484087"
---
De container heeft de volgende configuratie-instellingen:

|Vereist|Instelling|Doel|
|--|--|--|
|Ja|[ApiKey (ApiKey)](#apikey-configuration-setting)|Houdt factureringsgegevens bij.|
|Nee|[ApplicationInsights](#applicationinsights-setting)|Hiermee u telemetrieondersteuning van [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) toevoegen aan uw container.|
|Ja|[Facturering](#billing-configuration-setting)|Hiermee geeft u het eindpunt URI van de servicebron op Azure op.|
|Ja|[Overeenkomst](#eula-setting)| Geeft aan dat u de licentie voor de container hebt geaccepteerd.|
|Nee|[Vloeiend](#fluentd-settings)|Schrijft logboek- en, optioneel, metrische gegevens naar een Fluentd-server.|
|Nee|HTTP-proxy|Hiermee configureert u een HTTP-proxy voor het maken van uitgaande aanvragen.|
|Nee|[Logboekregistratie](#logging-settings)|Biedt ASP.NET Core-registratieondersteuning voor uw container. |
|Nee|[Mounts](#mount-settings)|Leest en schrijft gegevens van de hostcomputer naar de container en van de container terug naar de hostcomputer.|
