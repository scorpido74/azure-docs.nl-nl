---
title: Verbinding maken met Twitter vanuit Azure Logic Apps
description: Automatiseer taken en werk stromen voor het bewaken en beheren van tweets, plus gegevens over de volgers, uw gevolgde gebruikers, andere gebruikers, tijd lijnen en meer van uw Twitter-account met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 8ffd0fd558cf759fadd912de9dff4acf49d9659f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789083"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Twitter controleren en beheren met behulp van Azure Logic Apps

Met Azure Logic Apps en de Twitter-connector kunt u geautomatiseerde taken en werk stromen maken die u bedenken en beheren van gegevens die u bevalt in Twitter, zoals tweets, volgers, gebruikers, gevolgde gebruikers, tijd lijnen en meer, samen met andere acties, bijvoorbeeld:

* Tweets controleren, plaatsen en zoeken.
* Gegevens ophalen zoals volgers, gevolgde gebruikers, tijd lijnen en meer.

U kunt triggers gebruiken die reacties ophalen van uw Twitter-account en de uitvoer beschikbaar maken voor andere acties. U kunt acties gebruiken om taken uit te voeren met uw Twitter-account. U kunt ook andere acties uitvoeren met de uitvoer van Twitter-acties. Wanneer bijvoorbeeld een nieuwe tweet met een specifieke hashtag wordt weer gegeven, kunt u berichten verzenden met de connector voor toegestane vertraging. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw Twitter-account en gebruikers referenties

   Met uw referenties wordt uw logische app geautoriseerd om een verbinding te maken en toegang te krijgen tot uw Twitter-account.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waarvoor u toegang wilt krijgen tot uw Twitter-account. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een Twitter-trigger. Als u een Twitter-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="connect-to-twitter"></a>Verbinding maken met Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies een pad: 

   * Voor lege logische apps voert u in het zoekvak ' Twitter ' in als uw filter. 
   Selecteer de gewenste trigger onder de lijst met triggers. 

     -of-

   * Voor bestaande logische apps: 
   
     * Kies **nieuwe stap**onder de laatste stap waarin u een actie wilt toevoegen. 

       -of-

     * Als u een actie wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. 
     Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.
     
       Voer in het zoekvak ' Twitter ' in als uw filter. 
       Selecteer in de lijst acties de gewenste actie.

1. Als u wordt gevraagd om u aan te melden bij Twitter, meldt u zich nu aan zodat u toegang kunt verlenen voor uw logische app.

1. Geef de benodigde gegevens op voor de geselecteerde trigger of actie en ga door met het bouwen van de werk stroom van uw logische app.

## <a name="examples"></a>Voorbeelden

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter-trigger: wanneer een nieuwe tweet wordt geplaatst

Deze trigger start een werk stroom voor logische apps wanneer de trigger een nieuwe Tweet detecteert, bijvoorbeeld met de hashtag, #Seattle. Als deze tweets bijvoorbeeld worden gevonden, kunt u een bestand met de inhoud van de Tweets toevoegen aan de opslag, zoals een Dropbox-account met behulp van de Dropbox-connector. 

U kunt desgewenst een voor waarde toevoegen die in aanmerking komt voor tweets van gebruikers met ten minste een opgegeven aantal volgers.

**Enter prise-voor beeld**: u kunt deze trigger gebruiken om tweets over uw bedrijf te bewaken en de tweets-inhoud te uploaden naar een SQL database.

### <a name="twitter-action-post-a-tweet"></a>Twitter-actie: een Tweet plaatsen

Met deze actie wordt een Tweet geboekt, maar u kunt de actie zodanig instellen dat de Tweet de inhoud bevat van tweets die door de eerder beschreven trigger is gevonden. 

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/twitterconnector/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="get-support"></a>Krijg ondersteuning

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
