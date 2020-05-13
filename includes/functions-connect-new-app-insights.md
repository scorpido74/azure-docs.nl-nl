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
Met functies kunt u eenvoudig Application Insights integratie toevoegen aan een functie-app vanuit de [Azure Portal].

1. Zoek in de [Azure Portal][Azure-Portal]naar en selecteer **functie-app**en kies vervolgens uw functie-app. 

1. Selecteer de banner **Application Insights is niet geconfigureerd** aan de bovenkant van het venster. Als u deze banner niet ziet, is Application Insights al ingeschakeld voor uw app.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Application Insights inschakelen vanuit de portal":::

1. Maak een Application Insights resource met behulp van de instellingen die zijn opgegeven in de tabel onder de afbeelding.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Een Application Insights-resource maken":::

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam** | Unieke app-naam | Het is het gemakkelijkst om dezelfde naam te gebruiken als uw functie-app, die uniek moet zijn in uw abonnement. | 
    | **Locatie** | Europa -west | Gebruik, indien mogelijk, dezelfde [regio](https://azure.microsoft.com/regions/) als de functie-app, of een van de regio's die zich dicht bij die regio bevinden. |

1. Selecteer **Toepassen**. De Application Insights resource wordt gemaakt in dezelfde resource groep en hetzelfde abonnement als uw functie-app. Nadat de resource is gemaakt, sluit u het venster Application Insights.

1. Ga terug naar de functie-app, selecteer **instellingen**  >  **configuratie**en selecteer vervolgens **Toepassings instellingen**. Als u een instelling ziet `APPINSIGHTS_INSTRUMENTATIONKEY` met de naam, wordt Application Insights integratie ingeschakeld voor uw functie-app die wordt uitgevoerd in Azure.

[Azure-Portal]: https://portal.azure.com
