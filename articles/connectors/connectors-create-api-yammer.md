---
title: Verbinding maken met Yammer vanuit Azure Logic Apps | Microsoft Docs
description: Taken en werk stromen automatiseren waarmee berichten, feeds en meer worden bewaakt, gepost en beheerd met behulp van Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 9228a94dcf27d8987b16e2caa2681cf973db0657
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050636"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Uw Yammer-account bewaken en beheren met behulp van Azure Logic Apps

Met Azure Logic Apps en de Yammer-connector kunt u geautomatiseerde taken en werk stromen maken waarmee berichten, feeds en meer worden gecontroleerd en beheerd in uw Yammer-account, samen met andere acties, bijvoorbeeld:

* Controleren wanneer er nieuwe berichten worden weer gegeven in gevolgde feeds en groepen.
* Ontvang berichten, groepen, netwerken, Details van gebruikers en nog veel meer.
* Berichten plaatsen en vergelijken.

U kunt triggers gebruiken die reacties ophalen van uw Yammer-account en de uitvoer beschikbaar maken voor andere acties. U kunt acties gebruiken om taken uit te voeren met uw Yammer-account. U kunt ook andere acties uitvoeren met de uitvoer van Yammer-acties. Als er bijvoorbeeld nieuwe berichten in feeds of groepen worden weer gegeven, kunt u deze berichten delen met de connector voor toegestane vertraging. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw Yammer-account en gebruikers referenties

   Met uw referenties wordt uw logische app geautoriseerd om een verbinding te maken en toegang te krijgen tot uw Yammer-account.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw Yammer-account. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een Yammer-trigger. Als u een Yammer-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="connect-to-yammer"></a>Verbinding maken met Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies een pad: 

   * Voor lege Logic apps voert u in het zoekvak ' Yammer ' in als uw filter. 
   Selecteer de gewenste trigger onder de lijst met triggers. 

     -of-

   * Voor bestaande logische apps: 
   
     * Kies **nieuwe stap**onder de laatste stap waarin u een actie wilt toevoegen. 

       -of-

     * Als u een actie wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. 
     Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.
     
       Voer in het zoekvak ' Yammer ' in als uw filter. 
       Selecteer in de lijst acties de gewenste actie.

1. Als u wordt gevraagd om u aan te melden bij Yammer, meldt u zich nu aan, zodat u toegang kunt toestaan.

1. Geef de benodigde gegevens op voor de geselecteerde trigger of actie en ga door met het bouwen van de werk stroom van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/yammer/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors