---
title: Verbinding maken met Outlook.com
description: Werkstromen en werkstromen automatiseren die e-mail, agenda's en contactpersonen beheren in Outlook.com met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707183"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>E-mail, agenda's en contactpersonen beheren in Outlook.com met Azure Logic Apps

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en de [Outlook.com-connector](/connectors/outlook/)u @outlook.com @hotmail.com geautomatiseerde taken en werkstromen maken die uw of account beheren door logische apps te bouwen. U automatiseert bijvoorbeeld deze taken:

* E-mail ontvangen, verzenden en beantwoorden.
* Plan vergaderingen in uw agenda.
* Contactpersonen toevoegen en bewerken.

U elke trigger gebruiken om uw werkstroom te starten, bijvoorbeeld wanneer een nieuwe e-mail binnenkomt, wanneer een agenda-item wordt bijgewerkt of wanneer een gebeurtenis plaatsvindt in een verschilservice. U acties gebruiken die reageren op de triggergebeurtenis, bijvoorbeeld een e-mail verzenden of een nieuwe agenda-afspraak maken.

> [!NOTE]
> Als u taken voor een @fabrikam.onmicrosoft.comMicrosoft-werkaccount wilt automatiseren, zoals, gebruikt u de [Office 365 Outlook-connector](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Vereisten

* Een [Outlook.com account](https://outlook.live.com/owa/)

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* De logische app waar u toegang wilt krijgen tot uw Outlook.com-account. Als u uw werkstroom wilt starten met een Outlook.com trigger, moet u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)hebben. Als u een Outlook.com actie aan uw werkstroom wilt toevoegen, moet uw logische app al een trigger hebben.

## <a name="add-a-trigger"></a>Een trigger toevoegen

Een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) is een gebeurtenis waarmee de werkstroom in uw logische app wordt gestart. In dit voorbeeld wordt een logische app gebruikt een 'polling'-trigger die controleert op nieuwe e-mail in uw e-mailaccount, op basis van het opgegeven interval en de opgegeven frequentie.

1. Open uw lege logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com)

1. Voer in het zoekvak 'outlook.com' in als filter. Selecteer in dit voorbeeld **wanneer een nieuwe e-mail wordt binnenkomt**.

1. Als u wordt gevraagd zich aan te melden, geeft u uw Outlook.com referenties op zodat uw logische app verbinding kan maken met uw account. Als uw verbinding anders al bestaat, geeft u de informatie voor de triggereigenschappen op:

1. Stel in de trigger de waarden **Frequentie** en **Interval** in.

   Als u bijvoorbeeld wilt dat de trigger elke 15 minuten wordt gepeild, stelt u de **frequentie** in op **Minute**en stelt u het **interval** in op **15**.

1. Selecteer op de werkbalk Van de ontwerper **opslaan**, waarmee uw logische app wordt opgeslagen.

Als u wilt reageren op de trigger, voegt u een andere actie toe. U bijvoorbeeld de actie Twilio **Verzenden toevoegen,** die een tekst verzendt wanneer een e-mail binnenkomt.

## <a name="add-an-action"></a>Een actie toevoegen

Een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) is een bewerking die wordt uitgevoerd door de werkstroom in uw logische app. In deze voorbeeldlogica stuurt de logica-app een e-mail van uw Outlook.com-account. U de uitvoer van een andere trigger gebruiken om de actie te vullen. Stel dat uw logica-app de SalesForce gebruikt **wanneer een object wordt gemaakt.** U de Outlook.com **Een e-mailactie verzenden** en de uitvoer van de SalesForce-trigger in de e-mail gebruiken.

1. Open uw logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com)

1. Als u een actie wilt toevoegen als laatste stap in uw werkstroom, selecteert u **Nieuwe stap**. 

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen deze stappen. Selecteer het plusteken (**+**) dat wordt weergegeven en selecteer vervolgens Een actie **toevoegen**.

1. Voer in het zoekvak 'outlook.com' in als filter. Selecteer in dit voorbeeld **Een e-mail verzenden**. 

1. Als u wordt gevraagd zich aan te melden, geeft u uw Outlook.com referenties op zodat uw logische app verbinding kan maken met uw account. Als uw verbinding al bestaat, geeft u de informatie voor de actie-eigenschappen.

1. Selecteer op de werkbalk Van de ontwerper **opslaan**, waarmee uw logische app wordt opgeslagen.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](/connectors/outlook/)de connector voor technische details, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
