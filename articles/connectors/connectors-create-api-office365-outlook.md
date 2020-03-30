---
title: Verbinding maken met Office 365 Outlook
description: Taken en werkstromen automatiseren die e-mail, contactpersonen en agenda's beheren in Office 365 Outlook met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732671"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>E-mail, contactpersonen en agenda's beheren in Office 365 Outlook met behulp van Azure Logic Apps

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en de Office [365 Outlook-connector](/connectors/office365connector/)u geautomatiseerde taken en werkstromen maken die uw Office 365-account beheren door logische apps te maken. U automatiseert bijvoorbeeld deze taken:

* E-mail ontvangen, verzenden en beantwoorden. 
* Plan vergaderingen in uw agenda.
* Contactpersonen toevoegen en bewerken. 

U elke trigger gebruiken om uw werkstroom te starten, bijvoorbeeld wanneer een nieuwe e-mail binnenkomt, wanneer een agenda-item wordt bijgewerkt of wanneer een gebeurtenis plaatsvindt in een verschilservice, zoals Salesforce. U acties gebruiken die reageren op de triggergebeurtenis, bijvoorbeeld een e-mail verzenden of een nieuwe agenda-afspraak maken. 

> [!NOTE]
> Als u taken @outlook.com @hotmail.com voor een of account wilt automatiseren, gebruikt u de [Outlook.com connector](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Vereisten

* Een [Office 365-account](https://www.office.com/)

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* De logische app waarin u toegang wilt krijgen tot uw Office 365 Outlook-account. Als u uw werkstroom wilt starten met een Office 365 Outlook-trigger, moet u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)hebben. Als u een Actie van Office 365 Outlook aan uw werkstroom wilt toevoegen, moet uw logische app al een trigger hebben.

## <a name="add-a-trigger"></a>Een trigger toevoegen

Een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) is een gebeurtenis waarmee de werkstroom in uw logische app wordt gestart. In deze voorbeeldlogica-app wordt een 'polling'-trigger gebruikt die controleert op een bijgewerkte agendagebeurtenis in uw e-mailaccount, op basis van het opgegeven interval en de opgegeven frequentie.

1. Open uw lege logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com)

1. Voer in het `office 365 outlook` zoekvak in als filter. In dit voorbeeld wordt geselecteerd **wanneer een aanstaande gebeurtenis binnenkort van start gaat.**
   
   ![Trigger selecteren om uw logische app te starten](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Als u wordt gevraagd zich aan te melden, geeft u uw Office 365-referenties op zodat uw logische app verbinding kan maken met uw account. Als uw verbinding anders al bestaat, geeft u de informatie voor de eigenschappen van de trigger.

   In dit voorbeeld selecteert u de agenda die de trigger controleert, bijvoorbeeld:

   ![De eigenschappen van de trigger configureren](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. Stel in de trigger de waarden **Frequentie** en **Interval** in. Als u andere beschikbare triggereigenschappen wilt toevoegen, zoals **Tijdzone,** selecteert u deze eigenschappen in de lijst **Nieuwe parameter toevoegen.**

   Als u bijvoorbeeld wilt dat de trigger de agenda elke 15 minuten controleert, stelt u **Frequentie** in op **Minuut**en stelt u **Interval** in op `15`. 

   ![Frequentie en interval instellen voor de trigger](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

Voeg nu een actie toe die wordt uitgevoerd na de triggerbranden. U bijvoorbeeld de actie Twilio **Verzenden toevoegen,** die een tekst verzendt wanneer een agenda-afspraak binnen 15 minuten begint.

## <a name="add-an-action"></a>Een actie toevoegen

Een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) is een bewerking die wordt uitgevoerd door de werkstroom in uw logische app. Met deze voorbeeldlogica-app wordt een nieuwe contactpersoon gemaakt in Office 365 Outlook. U de uitvoer van een andere trigger of actie gebruiken om de contactpersoon te maken. Stel dat uw logica-app de trigger Dynamics 365 gebruikt **wanneer een record wordt gemaakt.** U de actie Office 365 Outlook **toevoegen Contact maken** en de uitvoer van de Trigger van SalesForce gebruiken om de nieuwe contactpersoon te maken.

1. Open uw logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com)

1. Als u een actie wilt toevoegen als laatste stap in uw werkstroom, selecteert u **Nieuwe stap**. 

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen deze stappen. Selecteer het plusteken (**+**) dat wordt weergegeven en selecteer vervolgens Een actie **toevoegen**.

1. Voer in het `office 365 outlook` zoekvak in als filter. In dit voorbeeld selecteert **u Contactpersoon maken**.

   ![Selecteer de actie die u wilt uitvoeren in uw logica-app](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Als u wordt gevraagd zich aan te melden, geeft u uw Office 365-referenties op zodat uw logische app verbinding kan maken met uw account. Als uw verbinding anders al bestaat, geeft u de informatie voor de eigenschappen van de actie.

   In dit voorbeeld selecteert u de map contactpersonen waarin de actie de nieuwe contactpersoon maakt, bijvoorbeeld:

   ![De eigenschappen van de actie configureren](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Als u andere beschikbare actie-eigenschappen wilt toevoegen, selecteert u deze eigenschappen in de lijst **Nieuwe parameter toevoegen.**

1. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

## <a name="connector-specific-details"></a>Connector-specifieke details

Zie de [referentiepagina van](/connectors/office365connector/)de connector voor technische details over triggers, acties en limieten zoals beschreven in het Swagger-bestand van de connector. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
