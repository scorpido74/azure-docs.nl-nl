---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84730989"
---
Met Azure Functions kunt u vanuit de [Azure Portal] eenvoudig de Application Insights-integratie aan een functie-app toevoegen.

1. Zoek in de [Azure] [Portal] naar de **functie-app** en selecteer deze en kies vervolgens uw functie-app. 

1. Selecteer bovenaan het venster de banner **Application Insights is not configured** (Application Insights is niet geconfigureerd). Als u deze banner niet ziet staan, is Application Insights al ingeschakeld voor uw app.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Enable Application Insights from the portal"::: (Application Insights vanuit de portal inschakelen)

1. Klap **Uw resource wijzigen** uit en maak een Application Insights-resource met de instellingen in de volgende tabel.  

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nieuwe resourcenaam** | Unieke app-naam | Het is het gemakkelijkst om dezelfde naam als voor uw functie-app te gebruiken. Deze naam moet uniek zijn in uw abonnement. | 
    | **Locatie** | Europa -west | Gebruik, indien mogelijk, dezelfde [regio](https://azure.microsoft.com/regions/) als uw functie-app, of een nabijgelegen regio. |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Enable Application Insights from the portal"::: (Een Application Insights-resource maken)

1. Selecteer **Toepassen**. 

   De Application Insights-resource wordt gemaakt in dezelfde resourcegroep en hetzelfde abonnement als uw functie-app. Nadat de resource is gemaakt, sluit u het venster Application Insights.

1. Selecteer **Configuratie** onder **Instellingen** in uw functie-app en selecteer vervolgens **Toepassingsinstellingen**. Als u een instelling met de naam `APPINSIGHTS_INSTRUMENTATIONKEY` ziet, wordt de Application Insights-integratie ingeschakeld voor de functie-app die in Azure wordt uitgevoerd.

[Azure Portal]: https://portal.azure.com
