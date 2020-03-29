---
title: Verbinding maken met Twitter vanuit Azure Logic Apps
description: Automatiseer taken en werkstromen die tweets controleren en beheren, plus gegevens over volgers, uw gevolgde gebruikers, andere gebruikers, tijdlijnen en meer van uw Twitter-account met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 8ffd0fd558cf759fadd912de9dff4acf49d9659f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789083"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Twitter bewaken en beheren met Azure Logic Apps

Met Azure Logic Apps en de Twitter-connector u geautomatiseerde taken en werkstromen maken die gegevens die u belangrijk vindt in Twitter controleren en beheren, zoals tweets, volgers, gebruikers en gevolgde gebruikers, tijdlijnen en meer, samen met andere acties, bijvoorbeeld:

* Tweets controleren, posten en zoeken.
* Ontvang gegevens zoals volgers, gevolgde gebruikers, tijdlijnen en meer.

Je triggers gebruiken die reacties van je Twitter-account ontvangen en de uitvoer beschikbaar maken voor andere acties. Je acties gebruiken die taken uitvoeren met je Twitter-account. U ook andere acties gebruiken de output van Twitter acties. Wanneer er bijvoorbeeld een nieuwe tweet met een specifieke hashtag wordt weergegeven, u berichten verzenden met de Slack-connector. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw Twitter-account en gebruikersreferenties

   Uw referenties autoriseren uw logische app om een verbinding te maken en toegang te krijgen tot uw Twitter-account.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar je toegang wilt krijgen tot je Twitter-account. Als u wilt beginnen met een Twitter-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u een Twitter-actie wilt gebruiken, start u uw logica-app met een andere trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="connect-to-twitter"></a>Verbinding maken met Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Kies een pad: 

   * Voer 'twitter' in het zoekvak 'twitter' in als filter. 
   Selecteer onder de lijst triggers de gewenste trigger. 

     -of-

   * Voor bestaande logische apps: 
   
     * Kies Nieuwe **stap**onder de laatste stap waarin u een actie wilt toevoegen . 

       -of-

     * Tussen de stappen waar u een actie wilt toevoegen, beweegt u de aanwijzer over de pijl tussen de stappen. 
     Kies het plusteken (**+**) dat wordt weergegeven en selecteer Vervolgens Een actie **toevoegen**.
     
       Voer in het zoekvak 'twitter' in als filter. 
       Selecteer onder de lijst met acties de gewenste actie.

1. Als je wordt gevraagd je aan te melden bij Twitter, meld je je nu aan, zodat je de toegang voor je logica-app autoriseren.

1. Geef de benodigde details voor de geselecteerde trigger of actie en ga verder met het bouwen van de workflow van uw logische app.

## <a name="examples"></a>Voorbeelden

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter trigger: Wanneer een nieuwe tweet wordt geplaatst

Deze trigger start een logische app-workflow wanneer de trigger een nieuwe tweet detecteert, bijvoorbeeld met de hashtag, #Seattle. Wanneer deze tweets bijvoorbeeld worden gevonden, u bijvoorbeeld een bestand met de inhoud van de tweets toevoegen aan de opslag, zoals een Dropbox-account met behulp van de Dropbox-connector. 

Optioneel u een voorwaarde opnemen dat in aanmerking komende tweets afkomstig moeten zijn van gebruikers met ten minste een opgegeven aantal volgers.

**Ondernemingsvoorbeeld:** U deze trigger gebruiken om tweets over uw bedrijf te controleren en de inhoud van de tweets te uploaden naar een SQL-database.

### <a name="twitter-action-post-a-tweet"></a>Twitter actie: Post een tweet

Met deze actie wordt een tweet geplaatst, maar u de actie zo instellen dat de tweet de inhoud bevat van tweets die zijn gevonden door de eerder beschreven trigger. 

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/twitterconnector/)van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
