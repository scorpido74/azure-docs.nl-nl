---
title: Metrics Advisor-clientbibliotheken-REST API
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om uw toepassingen te verbinden met de Metrics Advisor-API van Azure Cognitive Services.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 10/14/2020
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
ms.openlocfilehash: 5b3df5f4b41b2beeec68b667863f6ca7715df47b
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186913"
---
# <a name="quickstart-use-the-client-libraries-or-rest-apis-to-customize-your-solution"></a>Quickstart: De clientbibliotheken of REST API's gebruiken om uw oplossing aan te passen

Ga aan de slag met de Metrics Advisor-REST API of clientbibliotheken. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen.

Met Metrics Advisor kunt u het volgende doen:

* Een gegevensfeed van een gegevensbron toevoegen
* Opnamestatus controleren
* Detectie en waarschuwingen configureren 
* Query's uitvoeren op de anomaliedetectieresultaten
* Anomalieën diagnosticeren


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

- [De webportal gebruiken](web-portal.md)
- [Uw gegevensfeeds onboarden](../how-tos/onboard-your-data.md)
    - [Gegevensfeeds beheren](../how-tos/manage-data-feeds.md)
    - [Configuraties voor verschillende gegevens bronnen](../data-feeds-from-different-sources.md)
- [Metrische gegevens configureren en detectieconfiguratie afstemmen](../how-tos/configure-metrics.md)
- [Anomaliedetectie aanpassen met feedback](../how-tos/anomaly-feedback.md)