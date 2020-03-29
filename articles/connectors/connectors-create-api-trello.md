---
title: Verbinding maken met Trello vanuit Azure Logic Apps
description: Werkstromen en werkstromen automatiseren die lijsten, borden en kaarten in uw Trello-projecten bewaken en beheren met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5c4fcb9b4fea1a4d982b5cf665564599d371b7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789134"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Trello bewaken en beheren met Azure Logic Apps

Met Azure Logic Apps en de Trello-connector u geautomatiseerde taken en werkstromen maken die uw Trello-lijsten, kaarten, borden, teamleden enzovoort controleren en beheren, bijvoorbeeld:

* Controleer wanneer nieuwe kaarten worden toegevoegd aan borden en lijsten. 
* Maak, krijg en beheer borden, kaarten en lijsten.
* Voeg opmerkingen en leden toe aan kaarten.
* Lijstborden, bordjes, kaarten op borden, kaartopmerkingen, kaartleden, teamleden en teams waar je lid van bent. 
* Haal teams.

U triggers gebruiken die reacties van uw Trello-account ontvangen en de uitvoer beschikbaar maken voor andere acties. U acties gebruiken die taken uitvoeren met uw Trello-account. U ook andere acties de uitvoer van Trello-acties laten gebruiken. Wanneer er bijvoorbeeld een nieuwe kaart aan het bord of de lijst wordt toegevoegd, u berichten verzenden met de Slack-connector. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw Trello-account en gebruikersreferenties

  Uw referenties autoriseren uw logische app om een verbinding te maken en toegang te krijgen tot uw Trello-account.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt hebben tot uw Trello-account. Als u wilt beginnen met een Trello-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u een Trello-actie wilt gebruiken, start u uw logische app met een trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="connect-to-trello"></a>Verbinding maken met Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Voer in het zoekvak 'trello' in als filter. Selecteer onder de lijst triggers de gewenste trigger. 

   -of-

   Kies **Nieuwe stap**voor bestaande logische apps onder de laatste stap waarin u een actie wilt toevoegen. 
   Voer in het zoekvak 'trello' in als filter. 
   Selecteer onder de lijst met acties de gewenste actie.

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen de stappen. 
   Kies het plusteken (**+**) dat wordt weergegeven en selecteer Vervolgens Een actie **toevoegen**.

1. Als u wordt gevraagd zich aan te melden bij Trello, geeft u toestemming voor toegang voor uw logische app en meldt u zich aan.

1. Geef de benodigde details voor de geselecteerde trigger of actie en ga verder met het bouwen van de workflow van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/trello/)van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)