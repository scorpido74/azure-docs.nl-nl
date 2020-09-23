---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73484087"
---
De container heeft de volgende configuratie-instellingen:

|Vereist|Instelling|Doel|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Registreert facturerings gegevens.|
|No|[ApplicationInsights](#applicationinsights-setting)|Hiermee kunt u ondersteuning voor [Azure-toepassing Insights](https://docs.microsoft.com/azure/application-insights) -telemetrie toevoegen aan uw container.|
|Yes|[Facturering](#billing-configuration-setting)|Hiermee geeft u de eindpunt-URI op van de service resource op Azure.|
|Yes|[Houdt](#eula-setting)| Geeft aan dat u de licentie voor de container hebt geaccepteerd.|
|No|[Fluentd](#fluentd-settings)|Schrijft logboek en, eventueel, metrische gegevens naar een vloeiende server.|
|No|HTTP-proxy|Hiermee configureert u een HTTP-proxy voor het maken van uitgaande aanvragen.|
|No|[Logboekregistratie](#logging-settings)|Biedt ASP.NET Core ondersteuning voor logboek registratie voor uw container. |
|No|[Koppelt](#mount-settings)|Leest en schrijft gegevens van de hostcomputer naar de container en van de container terug naar de hostcomputer.|
