---
title: Verbinding maken met RSS-feeds vanuit Azure Logic Apps
description: Werk- en werkstromen automatiseren die RSS-feeds bewaken en beheren met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 3f1e092c2ff325cdcbc32c617af316d6fbe6dd74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789338"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>RSS-feeds beheren met Azure Logic Apps

Met Azure Logic Apps en de RSS-connector u geautomatiseerde taken en werkstromen maken voor elke RSS-feed, bijvoorbeeld:

* Controleer wanneer RSS-feeditems worden gepubliceerd.
* Alle RSS-feeditems weergeven.

RSS (Rich Site Summary), ook wel Really Simple Syndication genoemd, is een populair formaat voor websyndicatie en wordt gebruikt voor het publiceren van veelrecente inhoud, zoals blogposts en nieuwskoppen. Veel uitgevers van inhoud bieden een RSS-feed, zodat gebruikers zich kunnen abonneren op die inhoud. 

U een RSS-trigger gebruiken die reacties van een RSS-feed krijgt en de uitvoer beschikbaar maakt voor andere acties. U een RSS-actie in uw logische apps gebruiken om een taak uit te voeren met de RSS-feed. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* De URL voor een RSS-feed

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot een RSS-feed. Als u wilt beginnen met een RSS-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u een RSS-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="connect-to-an-rss-feed"></a>Verbinding maken met een RSS-feed

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Kies een pad: 

   * Voer in het zoekvak 'rss' in als filter in voor lege logische apps. Selecteer onder de lijst triggers de gewenste trigger. 

     -of-

   * Kies **Nieuwe stap**voor bestaande logische apps onder de stap waarin u een actie wilt toevoegen. Typ 'rss' als filter in het zoekvak. Selecteer onder de lijst met acties de gewenste actie.

1. Geef de benodigde details voor de geselecteerde trigger of actie en ga verder met het bouwen van de workflow van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/rss/)van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)