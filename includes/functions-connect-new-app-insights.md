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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132586"
---
Met functies kunt u eenvoudig Application Insights integratie toevoegen aan een functie-app vanuit de [Azure Portal].

1. In de [Portal][Azure Portal]typt `Function Apps` u in de zoek balk boven aan de pagina, kiest u de functie-app en selecteert u vervolgens de **Application Insights is geen banner geconfigureerd** aan de bovenkant van het venster. Als u deze banner niet ziet, is Application Insights al ingeschakeld voor uw app.

    ![Application Insights inschakelen vanuit de portal](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Maak een Application Insights resource met behulp van de instellingen die zijn opgegeven in de tabel onder de afbeelding.

   ![Een Application Insights-resource maken](media/functions-connect-new-app-insights/ai-general.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam** | Unieke app-naam | Het is het gemakkelijkst om dezelfde naam te gebruiken als uw functie-app, die uniek moet zijn in uw abonnement. | 
    | **Locatie** | Europa -west | Gebruik, indien mogelijk, dezelfde [regio](https://azure.microsoft.com/regions/) als de functie-app, of een van de regio's die zich dicht bij die regio bevinden. |

1. Selecteer **OK**. De Application Insights resource wordt gemaakt in dezelfde resource groep en hetzelfde abonnement als uw functie-app. Nadat de resource is gemaakt, sluit u het venster Application Insights.

1. Ga terug naar de functie-app, selecteer **Toepassings instellingen**en schuif vervolgens omlaag naar **Toepassings instellingen**. Als u een instelling ziet met `APPINSIGHTS_INSTRUMENTATIONKEY`de naam, wordt Application Insights integratie ingeschakeld voor uw functie-app die wordt uitgevoerd in Azure.

[Azure-Portal]: https://portal.azure.com
