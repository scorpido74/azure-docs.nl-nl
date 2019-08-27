---
title: Verbinding maken met SendGrid vanuit Azure Logic Apps | Microsoft Docs
description: Taken en werk stromen automatiseren waarmee e-mails worden verzonden en mailing lijsten worden beheerd in SendGrid met behulp van Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 4f5efd73ef2a08069e3b9f2c7d60be99c209ddca
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050804"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>E-mails verzenden en mailing lijsten beheren in SendGrid met behulp van Azure Logic Apps

Met Azure Logic Apps en de SendGrid-connector kunt u geautomatiseerde taken en werk stromen maken waarmee e-mails worden verzonden en uw ontvangers lijsten worden beheerd, bijvoorbeeld:

* E-mail verzenden.
* Ontvangers toevoegen aan lijsten.
* Wereld wijde onderdrukking ophalen, toevoegen en beheren.

U kunt SendGrid-acties in uw Logic apps gebruiken om deze taken uit te voeren. U kunt ook andere acties uitvoeren met de uitvoer van SendGrid-acties. 

Deze connector biedt alleen acties, dus als u uw logische app wilt starten, gebruikt u een afzonderlijke trigger, zoals een **terugkeer patroon** trigger. Als u bijvoorbeeld regel matig ontvangers toevoegt aan uw lijsten, kunt u via de Office 365 Outlook Connector of de Outlook.com-connector e-mail verzenden over ontvangers en lijsten.
Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Een [SendGrid-account](https://www.sendgrid.com/) en een [SendGrid-API-sleutel](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Uw API-sleutel geeft uw logische app toestemming om een verbinding te maken en toegang te krijgen tot uw SendGrid-account.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waartoe u toegang wilt krijgen tot uw SendGrid-account. Als u een SendGrid-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="connect-to-sendgrid"></a>Verbinding maken met SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies een pad: 

   * Kies **nieuwe stap**onder de laatste stap waarin u een actie wilt toevoegen. 

     -of-

   * Als u een actie wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. 
   Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak ' sendgrid ' in als uw filter. Selecteer in de lijst acties de gewenste actie.

1. Geef een naam op voor uw verbinding. 

1. Voer uw SendGrid-API-sleutel in en kies vervolgens **maken**.

1. Geef de benodigde gegevens voor de geselecteerde actie op en blijf door gaan met het bouwen van de werk stroom van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/sendgrid/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors