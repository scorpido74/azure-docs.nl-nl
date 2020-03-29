---
title: Verbinding maken met Salesforce vanuit Azure Logic Apps
description: Automatiseer taken en werkstromen die Salesforce-records en -taken bewaken, maken en beheren met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 000f4381ef2a7c0a2099a021b991087725ff2070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789287"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Salesforce-bronnen bewaken, maken en beheren met Azure Logic Apps

Met Azure Logic Apps en de Salesforce-connector u geautomatiseerde taken en werkstromen maken voor uw Salesforce-bronnen, zoals records, taken en objecten, bijvoorbeeld:

* Houd in de gaten wanneer records worden gemaakt of gewijzigd. 
* Taken en records maken, opvragen en beheren, inclusief acties invoegen, bijwerken en verwijderen.

Je Salesforce-triggers gebruiken die reacties van Salesforce ontvangen en de uitvoer beschikbaar maken voor andere acties. U acties in uw logische apps gebruiken om taken uit te voeren met Salesforce-bronnen. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Een [Salesforce-account](https://salesforce.com/)

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar je toegang wilt krijgen tot je Salesforce-account. Als u wilt beginnen met een Salesforce-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u een Salesforce-actie wilt gebruiken, start u uw logica-app met een andere trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="connect-to-salesforce"></a>Verbinding met Salesforce maken

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Kies een pad: 

   * Voer voor lege logische apps in het zoekvak 'salesforce' in als filter. 
   Selecteer onder de lijst triggers de gewenste trigger. 

     -of-

   * Kies **Nieuwe stap**voor bestaande logische apps onder de stap waarin u een actie wilt toevoegen. Voer in het zoekvak 'salesforce' in als filter. Selecteer onder de lijst met acties de gewenste actie.

1. Als je wordt gevraagd je aan te melden bij Salesforce, meld je je nu aan en geef je toegang.

   Uw referenties geven toestemming voor uw logische app om een verbinding met Salesforce te maken en toegang te krijgen tot uw gegevens.

1. Geef de benodigde details voor de geselecteerde trigger of actie en ga verder met het bouwen van de workflow van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/salesforce/)van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)