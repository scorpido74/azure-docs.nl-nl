---
title: Verbinding maken met Wunderlist vanuit Azure Logic Apps
description: Automatiseer taken en werk stromen die lijsten, taken, herinneringen en meer bewaken en beheren in uw Wunderlist-account met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5ac13595bd77238aaede5fa3bdc3a35ef69e8504
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789117"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Wunderlist controleren en beheren met behulp van Azure Logic Apps

Met Azure Logic Apps en de Wunderlist-connector kunt u geautomatiseerde taken en werk stromen maken waarmee TODO-lijsten, taken, herinneringen en meer worden bewaakt en beheerd in uw Wunderlist-account, samen met andere acties, bijvoorbeeld:

* Bewaak wanneer er nieuwe taken worden gemaakt, wanneer de taken moeten worden uitgevoerd of als er herinneringen optreden.
* Maak en beheer lijsten, notities, taken, subtaken en meer.
* Herinneringen instellen.
* Haal lijsten, taken, subtaken, herinneringen, bestanden, notities, opmerkingen en meer op.

[Wunderlist](https://www.wunderlist.com/) is een service die u helpt bij het plannen, beheren en volt ooien van uw projecten, TODO-lijsten en taken, op elk apparaat, overal. U kunt triggers gebruiken die reacties ophalen van uw Wunderlist-account en de uitvoer beschikbaar maken voor andere acties. U kunt acties gebruiken om taken uit te voeren met uw Wunderlist-account. U kunt ook andere acties uitvoeren met de uitvoer van Wunderlist-acties. Als er bijvoorbeeld nieuwe taken moeten worden voltooid, kunt u berichten plaatsen met de verbindings vertraging. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw Wunderlist-account en gebruikers referenties

   Met uw referenties wordt uw logische app geautoriseerd om een verbinding te maken en toegang te krijgen tot uw Wunderlist-account.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw Yammer-account. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een Wunderlist-trigger. Als u een actie Wunderlist wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="connect-to-wunderlist"></a>Verbinding maken met Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies een pad: 

   * Voor lege Logic apps voert u in het zoekvak ' wunderlist ' in als uw filter. 
   Selecteer de gewenste trigger onder de lijst met triggers. 

     -of-

   * Voor bestaande logische apps: 
   
     * Kies **nieuwe stap**onder de laatste stap waarin u een actie wilt toevoegen. 

       -of-

     * Als u een actie wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. 
     Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.
     
       In het zoekvak voert u ' wunderlist ' in als uw filter. 
       Selecteer in de lijst acties de gewenste actie.

1. Als u wordt gevraagd om u aan te melden bij Wunderlist, meldt u zich nu aan zodat u toegang kunt toestaan.

1. Geef de benodigde gegevens op voor de geselecteerde trigger of actie en ga door met het bouwen van de werk stroom van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/wunderlist/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="get-support"></a>Krijg ondersteuning

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)