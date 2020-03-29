---
title: Verbinding maken met Slack vanuit Azure Logic Apps
description: Werk- en werkstromen automatiseren die bestanden bewaken en kanalen, groepen en berichten beheren in uw Slack-account met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5f61009ee7b43be618e37acb4a783a54dbf11e55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789168"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Slack bewaken en beheren met Azure Logic Apps

Met Azure Logic Apps en de Slack-connector u geautomatiseerde taken en werkstromen maken die uw Slack-bestanden bewaken en uw Slack-kanalen, berichten, groepen enzovoort beheren, bijvoorbeeld:

* Controleer wanneer er nieuwe bestanden worden gemaakt.
* Kanalen maken, aanbieden en deelnemen 
* Berichten plaatsen.
* Groepen maken en instellen niet storen.

U triggers gebruiken die reacties van uw Slack-account ontvangen en de uitvoer beschikbaar maken voor andere acties. U acties gebruiken die taken uitvoeren met uw Slack-account. U ook andere acties de uitvoer van Slack-acties laten gebruiken. Wanneer er bijvoorbeeld een nieuw bestand wordt gemaakt, u e-mail verzenden met de Office 365 Outlook-connector. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw [Slack-account](https://slack.com/) en gebruikersreferenties

  Uw referenties autoriseren uw logische app om een verbinding te maken en toegang te krijgen tot uw Slack-account.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt hebben tot uw Slack-account. Als u wilt beginnen met een Slack-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u een Slack-actie wilt gebruiken, start u uw logische app met een trigger, zoals een Slack-trigger of een andere trigger, zoals de **recidieftrigger.**

## <a name="connect-to-slack"></a>Verbinding maken met Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Voer in het zoekvak 'speling' in als filter. Selecteer onder de lijst triggers de gewenste trigger. 

   -of-

   Kies **Nieuwe stap**voor bestaande logische apps onder de laatste stap waarin u een actie wilt toevoegen. 
   Voer in het zoekvak 'speling' in als filter. 
   Selecteer onder de lijst met acties de gewenste actie.

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen de stappen. 
   Kies het plusteken (**+**) dat wordt weergegeven en selecteer Vervolgens Een actie **toevoegen**.

1. Als u wordt gevraagd zich aan te melden bij Slack, meldt u zich aan bij uw Werkruimte Slack. 

   ![Aanmelden bij de werkruimte Slack](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Toegang voor uw logische app autoriseren.

   ![Toegang tot Slack autoriseren](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Geef de benodigde details op voor de geselecteerde trigger of actie. Als u de werkstroom van uw logische app wilt blijven bouwen, voegt u meer acties toe.

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/slack/)van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
