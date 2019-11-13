---
title: Gegevens opvragen uit een GA-omgeving C# met behulp van code-Azure time series Insights | Microsoft Docs
description: Meer informatie over het opvragen van gegevens uit een Azure Time Series Insights omgeving met behulp van C#een aangepaste app die is geschreven in.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 5946a1484a62b041772f388b0629d131afe37b92
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012552"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Gegevens opvragen uit de Azure Time Series Insights GA-omgeving met behulp vanC#

In C# dit voor beeld ziet u hoe u gegevens opvraagt uit de Azure time series Insights ga-omgeving.

Er worden enkele eenvoudige voorbeelden voor het gebruik van de API-query gegeven:

1. Als voorbereidende stap moet u het toegangs token verkrijgen via de API van Azure Active Directory. Geef dit token door in de `Authorization`-header van elke query-API-aanvraag. Zie [verificatie en autorisatie](time-series-insights-authentication-and-authorization.md)voor meer informatie over het instellen van niet-interactieve toepassingen. Zorg er ook voor dat alle constanten die aan het begin van het voor beeld zijn gedefinieerd, correct zijn ingesteld.
1. De lijst met omgevingen waartoe de gebruiker toegang heeft, wordt verkregen. Een van de omgevingen wordt opgehaald als de omgeving van belang en er worden verdere gegevens opgevraagd voor deze omgeving.
1. Er worden beschikbaarheidsgegevens opgevraagd voor de belangrijkste omgeving als voorbeeld van een HTTPS-aanvraag.
1. Er worden gebeurtenissamenvoegingsgegevens opgevraagd voor de belangrijkste omgeving als voorbeeld van een web socket-aanvraag. Er worden gegevens opgevraagd voor het volledige tijdsbereik van de beschikbaarheid.

> [!NOTE]
> De voorbeeld code is beschikbaar op [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Project afhankelijkheden

Voeg NuGet-pakketten toe `Microsoft.IdentityModel.Clients.ActiveDirectory` en `Newtonsoft.Json`.

## <a name="c-example"></a>C#Hierbij

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Volgende stappen

- Lees de [query-API-verwijzing](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)voor meer informatie over het uitvoeren van query's.

- Lees hoe u [met de client-SDK verbinding maakt met een Java script-app](https://github.com/microsoft/tsiclient) om te time series Insights.