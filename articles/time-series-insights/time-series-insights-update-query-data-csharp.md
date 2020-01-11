---
title: Gegevens opvragen uit een preview-omgeving C# met-Azure time series Insights | Microsoft Docs
description: Meer informatie over het opvragen van gegevens uit een Azure Time Series Insights omgeving met behulp van C#een app die is geschreven in.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: a1d32bf7ea296ed2c4ed9351fcefe400c03effa5
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861435"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Gegevens opvragen uit de Azure Time Series Insights voorbeeld omgeving met behulp vanC#

In C# dit voor beeld ziet u hoe u gegevens opvraagt uit de Azure time series Insights-voorbeeld omgeving.

Er worden enkele eenvoudige voorbeelden voor het gebruik van de API-query gegeven:

1. Als voorbereidende stap moet u het toegangs token verkrijgen via de API van Azure Active Directory. Geef dit token door in de `Authorization`-header van elke query-API-aanvraag. Lees [verificatie en autorisatie](time-series-insights-authentication-and-authorization.md)voor het instellen van niet-interactieve toepassingen. Zorg er ook voor dat alle constanten die aan het begin van het voor beeld zijn gedefinieerd, correct zijn ingesteld.
1. De lijst met omgevingen waartoe de gebruiker toegang heeft, wordt verkregen. Een van de omgevingen wordt opgehaald als de omgeving van belang en er worden verdere gegevens opgevraagd voor deze omgeving.
1. Er worden beschikbaarheidsgegevens opgevraagd voor de belangrijkste omgeving als voorbeeld van een HTTPS-aanvraag.
1. Hier vindt u een voor beeld van het automatisch genereren van SDK-ondersteuning van [Azure auto rest](https://github.com/Azure/AutoRest).

> [!NOTE]
> De voorbeeld code en de stappen voor het compileren en uitvoeren hiervan zijn beschikbaar op [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>C#Hierbij

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> Het bovenstaande code voorbeeld kan worden uitgevoerd zonder de standaard omgevings waarden te wijzigen.

## <a name="next-steps"></a>Volgende stappen

- Lees de [query-API-verwijzing](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)voor meer informatie over het uitvoeren van query's.

- Lees hoe u [met de client-SDK verbinding maakt met een Java script-app](https://github.com/microsoft/tsiclient) om te time series Insights.
