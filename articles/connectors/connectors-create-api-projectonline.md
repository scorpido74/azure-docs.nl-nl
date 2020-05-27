---
title: Verbinding maken met project online via Azure Logic Apps
description: Werk stromen automatiseren waarmee u project online-projecten,-taken en-resources kunt bewaken, maken en beheren met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a3e90fa3e3f57c1575a7ab09f9ce6941c13adcd1
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834863"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Project online-projecten,-taken en-resources beheren met Azure Logic Apps

Met Azure Logic Apps en de project Online-connector kunt u geautomatiseerde taken en werk stromen maken voor uw projecten, taken en resources in project online via Office 365. Uw werk stromen kunnen deze acties en andere uitvoeren, bijvoorbeeld:

* Controleren wanneer nieuwe projecten, taken of resources worden gemaakt. U kunt ook controleren wanneer er nieuwe projecten worden gepubliceerd.
* Nieuwe projecten, taken of resources maken.
* Bestaande projecten of taken weer geven.
* Bekijk of publiceer projecten en check deze uit.

Project online helpt u bij het plannen, bepalen en beheren van projecten en project portefeuille-investeringen van vrijwel elke locatie op vrijwel elk apparaat door krachtige mogelijkheden voor project beheer te bieden. U kunt project online-triggers gebruiken die reacties van project online ontvangen en de uitvoer beschikbaar maken voor andere acties. U kunt acties in uw Logic apps gebruiken om verschillende taken in project online uit te voeren. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Project online, verkrijgbaar via een [Office 365-account](https://www.office.com/), 

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw project online-gegevens. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een project online-trigger. Als u project online acties wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="connect-to-project-online"></a>Verbinding maken met project online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies een pad: 

   * Voor lege logische apps voert u in het zoekvak ' project online ' in als uw filter. 
   Selecteer de gewenste trigger onder de lijst met triggers. 

     -of-

   * Kies **nieuwe stap**onder de stap waar u een actie wilt toevoegen voor bestaande Logic apps. Voer in het zoekvak ' project online ' in als uw filter. Selecteer in de lijst acties de gewenste actie.

1. Als u wordt gevraagd om u aan te melden bij project online, meldt u zich nu aan.

   Met uw referenties wordt uw logische app geautoriseerd om een verbinding met project online te maken en toegang te krijgen tot uw gegevens.

1. Geef de benodigde gegevens op voor de geselecteerde trigger of actie en ga door met het bouwen van de werk stroom van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/projectonline/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga voor vragen naar de [pagina micro soft Q&een vraag voor Azure Logic apps](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)