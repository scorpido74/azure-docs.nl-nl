---
title: Verbinding maken met Project Online vanuit Azure Logic Apps
description: Automatiseer werkstromen die Project Online-projecten, -taken en -resources bewaken, maken en beheren met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 116922e018410f260b33f4dfd76e5983fbbcc0a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789321"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Project Online-projecten, -taken en -resources beheren met Azure Logic Apps

Met Azure Logic Apps en de Project Online-connector u via Office 365 geautomatiseerde taken en werkstromen voor uw projecten, taken en resources in Project Online maken. Uw werkstromen kunnen deze acties en andere uitvoeren, bijvoorbeeld:

* Controleer wanneer nieuwe projecten, taken of resources worden gemaakt. Of controleer wanneer nieuwe projecten worden gepubliceerd.
* Maak nieuwe projecten, taken of resources.
* Bestaande projecten of taken weergeven.
* Uitchecken, inchecken of publiceren van projecten.

Project Online helpt u bij het plannen, prioriteren en beheren van investeringen in projecten en projecten vanaf vrijwel elke locatie op bijna elk apparaat door krachtige mogelijkheden voor projectbeheer te bieden. U Project Online-triggers gebruiken die reacties van Project Online ontvangen en de uitvoer beschikbaar maken voor andere acties. U acties in uw logische apps gebruiken om verschillende taken uit te voeren in Project Online. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Project Online, beschikbaar via een [Office 365-account](https://www.office.com/), 

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw Project Online-gegevens. Als u wilt beginnen met een Project Online-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u Project Online-acties wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="connect-to-project-online"></a>Verbinding maken met Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Kies een pad: 

   * Voer in het zoekvak 'Project Online' in als filter. 
   Selecteer onder de lijst triggers de gewenste trigger. 

     -of-

   * Kies **Nieuwe stap**voor bestaande logische apps onder de stap waarin u een actie wilt toevoegen. Typ in het zoekvak 'Project Online' als filter. Selecteer onder de lijst met acties de gewenste actie.

1. Als u wordt gevraagd zich aan te melden bij Project Online, meldt u zich nu aan.

   Uw referenties geven toestemming voor uw logische app om een verbinding met Project Online te maken en toegang te krijgen tot uw gegevens.

1. Geef de benodigde details voor de geselecteerde trigger of actie en ga verder met het bouwen van de workflow van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/projectonline/)van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)