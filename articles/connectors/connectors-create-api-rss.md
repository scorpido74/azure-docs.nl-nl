---
title: Verbinding maken met RSS-feeds vanuit Azure Logic Apps
description: Taken en werk stromen automatiseren waarmee RSS-feeds worden gecontroleerd en beheerd met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 1e95b7bfce3d57dfe579a5622c3c13fac2b6af68
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834812"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>RSS-feeds beheren met behulp van Azure Logic Apps

Met Azure Logic Apps en de RSS-connector kunt u geautomatiseerde taken en werk stromen voor elke RSS-feed maken, bijvoorbeeld:

* Controleren wanneer RSS-feed-items worden gepubliceerd.
* Alle items van de RSS-feed weer geven.

RSS (uitgebreid site overzicht), ook wel echt eenvoudige syndicatie genoemd, is een populaire indeling voor Websyndicatie en wordt gebruikt voor het publiceren van regel matig bijgewerkte inhoud, zoals blog berichten en nieuwskoppen. Veel uitgevers van inhoud bieden een RSS-feed zodat gebruikers zich kunnen abonneren op die inhoud. 

U kunt een RSS-trigger gebruiken die antwoorden ontvangt van een RSS-feed en de uitvoer beschikbaar maakt voor andere acties. U kunt een RSS-actie in uw Logic apps gebruiken om een taak uit te voeren met de RSS-feed. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* De URL voor een RSS-feed

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waarvoor u toegang wilt krijgen tot een RSS-feed. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een RSS-trigger. Als u een RSS-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="connect-to-an-rss-feed"></a>Verbinding maken met een RSS-feed

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies een pad: 

   * Voor lege logische apps voert u in het zoekvak ' RSS ' in als uw filter. Selecteer de gewenste trigger onder de lijst met triggers. 

     -of-

   * Kies **nieuwe stap**onder de stap waar u een actie wilt toevoegen voor bestaande Logic apps. Typ 'rss' als filter in het zoekvak. Selecteer in de lijst acties de gewenste actie.

1. Geef de benodigde gegevens op voor de geselecteerde trigger of actie en ga door met het bouwen van de werk stroom van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/rss/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga voor vragen naar de [pagina micro soft Q&een vraag voor Azure Logic apps](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)