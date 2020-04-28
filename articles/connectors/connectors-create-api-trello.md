---
title: Verbinding maken met Trello vanuit Azure Logic Apps
description: Taken en werk stromen automatiseren waarmee u lijsten, kaarten en kaarten in uw Trello-projecten kunt bewaken en beheren met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5c4fcb9b4fea1a4d982b5cf665564599d371b7cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789134"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Trello met Azure Logic Apps bewaken en beheren

Met Azure Logic Apps en de Trello-connector kunt u geautomatiseerde taken en werk stromen maken voor het bewaken en beheren van uw Trello-lijsten,-kaarten,-borden,-team leden enzovoort, bijvoorbeeld:

* Controleren wanneer nieuwe kaarten worden toegevoegd aan kaarten en lijsten. 
* Kaarten, kaarten en lijsten maken, ophalen en beheren.
* Voeg opmerkingen en leden toe aan kaarten.
* Lijst met kaarten, bord etiketten, kaarten op kaarten, kaart opmerkingen, kaart leden, team leden en teams waarvan u lid bent. 
* Teams ophalen.

U kunt triggers gebruiken die reacties ophalen van uw Trello-account en de uitvoer beschikbaar maken voor andere acties. U kunt acties gebruiken om taken uit te voeren met uw Trello-account. U kunt ook andere acties uitvoeren met de uitvoer van Trello-acties. Als er bijvoorbeeld een nieuwe kaart aan het bord of de lijst wordt toegevoegd, kunt u berichten verzenden met de connector voor toegestane vertraging. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw Trello-account en gebruikers referenties

  Met uw referenties wordt uw logische app geautoriseerd om een verbinding te maken en toegang te krijgen tot uw Trello-account.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waartoe u toegang wilt krijgen tot uw Trello-account. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een Trello-trigger. Als u een Trello-actie wilt gebruiken, start u uw logische app met een trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="connect-to-trello"></a>Verbinding maken met Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Voor lege Logic apps voert u in het zoekvak ' Trello ' in als uw filter. Selecteer de gewenste trigger onder de lijst met triggers. 

   -of-

   Voor bestaande Logic apps, onder de laatste stap waar u een actie wilt toevoegen, kiest u **nieuwe stap**. 
   Voer in het zoekvak ' Trello ' in als uw filter. 
   Selecteer in de lijst acties de gewenste actie.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. 
   Kies het plus teken (**+**) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Als u wordt gevraagd om u aan te melden bij Trello, machtigt u toegang voor uw logische app en meldt u zich aan.

1. Geef de benodigde gegevens op voor de geselecteerde trigger of actie en ga door met het bouwen van de werk stroom van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/trello/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)