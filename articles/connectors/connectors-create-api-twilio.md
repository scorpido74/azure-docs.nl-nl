---
title: Verbinding maken met Twilio vanuit Azure Logic Apps
description: Werkstromen en werkstromen automatiseren die algemene SMS-, MMS- en IP-berichten beheren via uw Twilio-account met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: e5b218efd9c8cfaad99d76d8118d181390a977c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789100"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Berichten beheren in Twilio met Azure Logic Apps

Met Azure Logic Apps en de Twilio-connector u geautomatiseerde taken en werkstromen maken die berichten in Twilio ontvangen, verzenden en aanbieden, waaronder algemene SMS-, MMS- en IP-berichten. U deze acties gebruiken om taken uit te voeren met uw Twilio-account. U ook andere acties de uitvoer van Twilio-acties laten gebruiken. Wanneer er bijvoorbeeld een nieuw bericht binnenkomt, u de inhoud van het bericht verzenden met de Slack-connector. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Van [Twilio](https://www.twilio.com/): 

  * Uw Twilio-account-ID en [verificatietoken](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), die u vinden op uw Twilio-dashboard

    Uw referenties geven uw logische app toestemming om een verbinding te maken en toegang te krijgen tot uw Twilio-account vanuit uw logische app. 
    Als u een Twilio-proefaccount gebruikt, u alleen sms'en naar *geverifieerde* telefoonnummers.

  * Een geverifieerd Twilio-telefoonnummer dat sms kan verzenden

  * Een geverifieerd Twilio-telefoonnummer dat sms kan ontvangen

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw Twilio-account. Als u een Twilio-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="connect-to-twilio"></a>Verbinding maken met Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Kies een pad: 

     * Kies Nieuwe **stap**onder de laatste stap waarin u een actie wilt toevoegen . 

       -of-

     * Tussen de stappen waar u een actie wilt toevoegen, beweegt u de aanwijzer over de pijl tussen de stappen. 
     Kies het plusteken (**+**) dat wordt weergegeven en selecteer Vervolgens Een actie **toevoegen**.
     
       Voer in het zoekvak 'twilio' in als filter. 
       Selecteer onder de lijst met acties de gewenste actie.

1. Geef de benodigde details voor uw verbinding op en kies **Maak:**

   * De naam die u moet gebruiken voor uw verbinding
   * Uw Twilio-account-id 
   * Uw Twilio-toegangstoken (authenticatie)

1. Geef de benodigde details voor uw geselecteerde actie en ga verder met het bouwen van de workflow van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/twilio/)van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)