---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 266cd52117f36b282fdd4bc8615a15e451cc203f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132586"
---
Met Functions kun je eenvoudig Application Insights-integratie toevoegen aan een functie-app vanuit de [Azure-portal.]

1. Typ `Function Apps` in de [portal][Azure Portal]in de zoekbalk boven aan de pagina, kies uw functie-app en selecteer vervolgens de **toepassingsinzichten is niet geconfigureerdbanner** boven aan het venster. Als u deze banner niet ziet, heeft uw app application insights al ingeschakeld.

    ![Toepassingsinzichten inschakelen vanuit de portal](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Maak een Application Insights-bron met behulp van de instellingen die zijn opgegeven in de tabel onder de afbeelding.

   ![Een Application Insights-resource maken](media/functions-connect-new-app-insights/ai-general.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam** | Unieke app-naam | Het is het gemakkelijkst om dezelfde naam te gebruiken als uw functie-app, die uniek moet zijn in uw abonnement. | 
    | **Locatie** | Europa -west | Gebruik indien mogelijk dezelfde [regio](https://azure.microsoft.com/regions/) als de functie-app of een regio die zich in de buurt van die regio bevindt. |

1. Selecteer **OK**. De bron Application Insights wordt gemaakt in dezelfde resourcegroep en een abonnement als uw functie-app. Nadat de resource is gemaakt, sluit u het venster Toepassingsinzichten.

1. Terug in de functie-app selecteert u **Toepassingsinstellingen**en blader t/m naar beneden naar **toepassingsinstellingen**. Als u een `APPINSIGHTS_INSTRUMENTATIONKEY`instelling met de naam ziet, wordt de integratie van Application Insights ingeschakeld voor uw functie-app die wordt uitgevoerd in Azure.

[Azure-portal]: https://portal.azure.com
