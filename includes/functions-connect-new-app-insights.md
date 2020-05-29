---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/09/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3289ba03d0f613d004bc8bff4dbcf2bd434f3da3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121529"
---
Met Functions kunt u vanuit de [Azure Portal] eenvoudig de Application Insights-integratie aan een functie-app toevoegen.

1. Zoek in de [Azure] [Portal] naar de **functie-app** en selecteer deze en kies vervolgens uw functie-app. 

1. Selecteer bovenaan het venster de banner **Application Insights is not configured** (Application Insights is niet geconfigureerd). Als u deze banner niet ziet staan, is Application Insights al ingeschakeld voor uw app.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Enable Application Insights from the portal"::: (Application Insights vanuit de portal inschakelen)

1. Maak een Application Insights-resource met behulp van de instellingen die zijn opgegeven in de tabel onder de afbeelding.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Create an Application Insights resource"::: (Een Application Insights-resource maken)

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam** | Unieke app-naam | Het is het gemakkelijkst om dezelfde naam als voor uw functie-app te gebruiken. Deze naam moet uniek zijn in uw abonnement. | 
    | **Locatie** | Europa -west | Gebruik, indien mogelijk, dezelfde [regio](https://azure.microsoft.com/regions/) als uw functie-app, of een nabijgelegen regio. |

1. Selecteer **Toepassen**. De Application Insights-resource wordt gemaakt in dezelfde resourcegroep en hetzelfde abonnement als uw functie-app. Nadat de resource is gemaakt, sluit u het venster Application Insights.

1. Selecteer in uw functie-app **Instellingen** > **Configuratie** en vervolgens **Toepassingsinstellingen**. Als u een instelling met de naam `APPINSIGHTS_INSTRUMENTATIONKEY` ziet, wordt de Application Insights-integratie ingeschakeld voor de functie-app die in Azure wordt uitgevoerd.

[Azure Portal]: https://portal.azure.com
