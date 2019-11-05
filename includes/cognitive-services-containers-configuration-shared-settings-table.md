---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73484087"
---
De container heeft de volgende configuratie-instellingen:

|Vereist|Instelling|Doel|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Registreert facturerings gegevens.|
|Nee|[ApplicationInsights](#applicationinsights-setting)|Hiermee kunt u ondersteuning voor [Azure-toepassing Insights](https://docs.microsoft.com/azure/application-insights) -telemetrie toevoegen aan uw container.|
|Ja|[Facturering](#billing-configuration-setting)|Hiermee geeft u de eindpunt-URI op van de service resource op Azure.|
|Ja|[Houdt](#eula-setting)| Geeft aan dat u de licentie voor de container hebt geaccepteerd.|
|Nee|[Fluentd](#fluentd-settings)|Schrijft logboek en, eventueel, metrische gegevens naar een vloeiende server.|
|Nee|HTTP-proxy|Hiermee configureert u een HTTP-proxy voor het maken van uitgaande aanvragen.|
|Nee|[Userenv](#logging-settings)|Biedt ASP.NET Core ondersteuning voor logboek registratie voor uw container. |
|Nee|[Koppelt](#mount-settings)|Leest en schrijft gegevens van de hostcomputer naar de container en van de container terug naar de hostcomputer.|
