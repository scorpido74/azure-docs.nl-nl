---
title: Verbinding maken met Outlook.com-Azure Logic Apps | Microsoft Docs
description: E-mail, agenda's en contact personen beheren met Outlook.com REST-Api's en Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.topic: article
ms.date: 08/18/2016
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 4586255e96647267bc913f2bc054610163e16bd3
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050877"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>E-mail, agenda's en contact personen in Outlook.com beheren met Azure Logic Apps

In dit artikel wordt beschreven hoe u uw Outlook.com-account binnen een logische app kunt maken en beheren met de box-connector. Op die manier kunt u logische apps maken voor het automatiseren van taken en werk stromen voor uw Outlook.com-account, bijvoorbeeld:

* E-mail verzenden. 
* Vergaderingen plannen.
* Contact personen toevoegen. 

Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Vereisten

* Een [Outlook.com-account](https://outlook.live.com/owa/)

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* De logische app waartoe u toegang wilt krijgen tot uw Outlook.com-account. Als u uw logische app wilt starten met een Outlook-trigger, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig. 

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Verbinding maken met Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentie pagina van de connector](/connectors/outlook/)voor technische details, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector. 

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors