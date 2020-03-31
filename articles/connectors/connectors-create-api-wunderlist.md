---
title: Verbinding maken met Wunderlist vanuit Azure Logic Apps
description: Werkstromen en werkstromen automatiseren die lijsten, taken, herinneringen en meer in uw Wunderlist-account bewaken en beheren met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5ac13595bd77238aaede5fa3bdc3a35ef69e8504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789117"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Wunderlist bewaken en beheren met Azure Logic Apps

Met Azure Logic Apps en de Wunderlist-connector u geautomatiseerde taken en werkstromen maken die takenlijsten, taken, herinneringen en meer in uw Wunderlist-account bewaken en beheren, samen met andere acties, bijvoorbeeld:

* Controleer wanneer nieuwe taken worden gemaakt, wanneer taken moeten worden uitgevoerd of er herinneringen worden uitgevoerd.
* Lijsten, notities, taken, subtaken en meer maken en beheren.
* Stel herinneringen in.
* Ontvang lijsten, taken, subtaken, herinneringen, bestanden, notities, opmerkingen en meer.

[Wunderlist](https://www.wunderlist.com/) is een service waarmee u uw projecten, takenlijsten en taken plannen, beheren en voltooien, op elk apparaat en overal. U triggers gebruiken die reacties van uw Wunderlist-account ontvangen en de uitvoer beschikbaar maken voor andere acties. U acties gebruiken die taken uitvoeren met uw Wunderlist-account. U ook andere acties de uitvoer van Wunderlist-acties laten gebruiken. Als er bijvoorbeeld nieuwe taken moeten worden uitgevoerd, u berichten plaatsen met de Slack-connector. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw Wunderlist-account en gebruikersreferenties

   Uw referenties autoriseren uw logische app om een verbinding te maken en toegang te krijgen tot uw Wunderlist-account.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw Yammer-account. Als u wilt beginnen met een Wunderlist-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u een Wunderlist-actie wilt gebruiken, start u uw logica-app met een andere trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="connect-to-wunderlist"></a>Verbinding maken met Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Kies een pad: 

   * Voer in het zoekvak 'wunderlist' in als filter. 
   Selecteer onder de lijst triggers de gewenste trigger. 

     -of-

   * Voor bestaande logische apps: 
   
     * Kies Nieuwe **stap**onder de laatste stap waarin u een actie wilt toevoegen . 

       -of-

     * Tussen de stappen waar u een actie wilt toevoegen, beweegt u de aanwijzer over de pijl tussen de stappen. 
     Kies het plusteken (**+**) dat wordt weergegeven en selecteer Vervolgens Een actie **toevoegen**.
     
       Voer in het zoekvak 'wunderlist' in als filter. 
       Selecteer onder de lijst met acties de gewenste actie.

1. Als u wordt gevraagd zich aan te melden bij Wunderlist, meldt u zich nu aan, zodat u toegang verlenen.

1. Geef de benodigde details voor de geselecteerde trigger of actie en ga verder met het bouwen van de workflow van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/wunderlist/)van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)