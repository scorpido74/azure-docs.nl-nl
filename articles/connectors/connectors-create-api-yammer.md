---
title: Verbinding maken met Yammer vanuit Azure Logic Apps
description: Werk- en werkstromen automatiseren die berichten, feeds en meer in Yammer controleren, posten en beheren met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 194c08d034d44ba0a4472b3b516fc45d1d262d28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789066"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Uw Yammer-account bewaken en beheren met Azure Logic Apps

Met Azure Logic Apps en de Yammer-connector u geautomatiseerde taken en werkstromen maken die berichten, feeds en meer in uw Yammer-account controleren en beheren, samen met andere acties, bijvoorbeeld:

* Controleer wanneer er nieuwe berichten worden weergegeven in gevolgde feeds en groepen.
* Ontvang berichten, groepen, netwerken, gegevens van gebruikers en meer.
* Post en like berichten.

U triggers gebruiken die reacties uit uw Yammer-account ontvangen en de uitvoer beschikbaar maken voor andere acties. U acties gebruiken die taken uitvoeren met uw Yammer-account. U ook andere acties de uitvoer van Yammer-acties laten gebruiken. Wanneer er bijvoorbeeld nieuwe berichten worden weergegeven in feeds of groepen, u deze berichten delen met de Slack-connector. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw Yammer-account en gebruikersreferenties

   Uw referenties geven toestemming voor uw logische app om een verbinding te maken en toegang te krijgen tot uw Yammer-account.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw Yammer-account. Als u wilt beginnen met een Yammer-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u een Yammer-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="connect-to-yammer"></a>Verbinding maken met Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Kies een pad: 

   * Voer in het zoekvak 'Yammer' in het zoekvak in als filter. 
   Selecteer onder de lijst triggers de gewenste trigger. 

     -of-

   * Voor bestaande logische apps: 
   
     * Kies Nieuwe **stap**onder de laatste stap waarin u een actie wilt toevoegen . 

       -of-

     * Tussen de stappen waar u een actie wilt toevoegen, beweegt u de aanwijzer over de pijl tussen de stappen. 
     Kies het plusteken (**+**) dat wordt weergegeven en selecteer Vervolgens Een actie **toevoegen**.
     
       Voer in het zoekvak 'yammer' in als filter. 
       Selecteer onder de lijst met acties de gewenste actie.

1. Als u wordt gevraagd zich aan te melden bij Yammer, meldt u zich nu aan zodat u toegang verlenen.

1. Geef de benodigde details voor de geselecteerde trigger of actie en ga verder met het bouwen van de workflow van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/yammer/)van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)