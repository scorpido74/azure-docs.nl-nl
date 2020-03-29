---
title: Verbinding maken met SendGrid vanuit Azure Logic Apps
description: Taken en werkstromen automatiseren die e-mails verzenden en mailinglijsten beheren in SendGrid met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 998020c5e39c8d50e8a14c74c43b7b435752f43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789304"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>E-mails verzenden en mailinglijsten beheren in SendGrid met Azure Logic Apps

Met Azure Logic Apps en de SendGrid-connector u geautomatiseerde taken en werkstromen maken die e-mails verzenden en lijsten met geadresseerden beheren, bijvoorbeeld:

* Stuur e-mail.
* Ontvangers toevoegen aan lijsten.
* Wereldwijde onderdrukking oppakken, toevoegen en beheren.

U SendGrid-acties in uw logische apps gebruiken om deze taken uit te voeren. U ook andere acties de uitvoer van SendGrid-acties laten gebruiken. 

Deze connector biedt alleen acties, dus om uw logische app te starten, gebruikt u een aparte trigger, zoals een **recidieftrigger.** Als u bijvoorbeeld regelmatig geadresseerden aan uw lijsten toevoegt, u e-mail over geadresseerden en lijsten verzenden met de Office 365 Outlook-connector of Outlook.com connector.
Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Een [SendGrid-account](https://www.sendgrid.com/) en een [SendGrid API-sleutel](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Uw API-sleutel machtigt uw logische app om een verbinding te maken en toegang te krijgen tot uw SendGrid-account.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw SendGrid-account. Als u een SendGrid-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="connect-to-sendgrid"></a>Verbinding maken met SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Kies een pad: 

   * Kies Nieuwe **stap**onder de laatste stap waarin u een actie wilt toevoegen . 

     -of-

   * Tussen de stappen waar u een actie wilt toevoegen, beweegt u de aanwijzer over de pijl tussen de stappen. 
   Kies het plusteken (**+**) dat wordt weergegeven en selecteer Vervolgens Een actie **toevoegen**.

1. Voer in het zoekvak 'sendgrid' in als filter. Selecteer onder de lijst met acties de gewenste actie.

1. Geef een naam op voor uw verbinding. 

1. Voer de SendGrid API-sleutel in en kies **Maak .**

1. Geef de benodigde details voor uw geselecteerde actie en ga verder met het bouwen van de workflow van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/sendgrid/)van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)