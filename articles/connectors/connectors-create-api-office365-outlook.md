---
title: Verbinding maken met Office 365 Outlook
description: Taken en werk stromen automatiseren waarmee e-mail, contact personen en agenda's in Office 365 Outlook worden beheerd met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75732671"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>E-mail, contactpersonen en agenda's beheren in Office 365 Outlook met behulp van Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de [Office 365 Outlook-Connector](/connectors/office365connector/)kunt u geautomatiseerde taken en werk stromen maken die uw Office 365-account beheren door Logic apps te bouwen. U kunt bijvoorbeeld de volgende taken automatiseren:

* E-mail ontvangen, verzenden en beantwoorden. 
* Plan vergaderingen in uw agenda.
* Contact personen toevoegen en bewerken. 

U kunt elke trigger gebruiken om uw werk stroom te starten, bijvoorbeeld wanneer er een nieuwe e-mail binnenkomt, wanneer een agenda-item wordt bijgewerkt of wanneer er een gebeurtenis optreedt in een diff-service, zoals Sales Force. U kunt acties gebruiken die reageren op de trigger gebeurtenis, bijvoorbeeld een e-mail bericht verzenden of een nieuwe agenda gebeurtenis maken. 

> [!NOTE]
> Voor het automatiseren van taken voor een @outlook.com @hotmail.com -of-account gebruikt u de [Outlook.com-connector](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Vereisten

* Een [Office 365-account](https://www.office.com/)

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* De logische app waar u toegang wilt krijgen tot uw Office 365 Outlook-account. Als u uw werk stroom wilt starten met een Office 365 Outlook-trigger, moet u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)hebben. Als u een Office 365 Outlook-actie wilt toevoegen aan uw werk stroom, moet uw logische app al een trigger hebben.

## <a name="add-a-trigger"></a>Een trigger toevoegen

Een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) is een gebeurtenis waarmee de werk stroom in uw logische app wordt gestart. In deze voor beeld van een logische app wordt een ' polling-trigger ' gebruikt waarmee wordt gecontroleerd op een bijgewerkte agenda gebeurtenis in uw e-mail account, op basis van de opgegeven interval en frequentie.

1. Open in de [Azure Portal](https://portal.azure.com)uw lege logische app in de ontwerp functie voor logische apps.

1. Voer in het zoekvak in `office 365 outlook` als uw filter. In dit voor beeld **wordt geselecteerd wanneer een geplande gebeurtenis binnenkort wordt gestart**.
   
   ![Selecteer trigger om uw logische app te starten](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Als u wordt gevraagd om u aan te melden, geeft u uw Office 365-referenties op zodat uw logische app verbinding kan maken met uw account. Als uw verbinding al bestaat, geeft u de informatie voor de eigenschappen van de trigger op.

   In dit voor beeld wordt de agenda geselecteerd die door de trigger wordt gecontroleerd, bijvoorbeeld:

   ![De eigenschappen van de trigger configureren](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. Stel in de trigger de **frequentie** -en **interval** waarden in. Als u andere beschik bare trigger eigenschappen, zoals **tijd zone**, wilt toevoegen, selecteert u de eigenschappen in de lijst **nieuwe para meter toevoegen** .

   Als u bijvoorbeeld wilt dat de trigger de kalender elke 15 minuten controleert, stelt u de **frequentie** in op **minuut**en stelt u **interval** in op `15` . 

   ![Stel de frequentie en het interval voor de trigger in](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Voeg nu een actie toe die wordt uitgevoerd nadat de trigger wordt geactiveerd. U kunt bijvoorbeeld de Twilio-bericht actie voor **verzenden** toevoegen, waarmee een tekst wordt verzonden wanneer een agenda gebeurtenis in 15 minuten wordt gestart.

## <a name="add-an-action"></a>Een actie toevoegen

Een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) is een bewerking die door de werk stroom in uw logische app wordt uitgevoerd. In dit voor beeld van een logische app wordt een nieuwe contact persoon gemaakt in Office 365 Outlook. U kunt de uitvoer van een andere trigger of actie gebruiken om de contact persoon te maken. Stel bijvoorbeeld dat uw logische app de trigger Dynamics 365 gebruikt **Wanneer een record wordt gemaakt**. U kunt de actie Office 365 Outlook- **contact persoon maken** toevoegen en de uitvoer van de Sales Force-trigger gebruiken om de nieuwe contact persoon te maken.

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **nieuwe stap**om een actie toe te voegen als de laatste stap in uw werk stroom. 

   Als u een actie tussen de stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen deze stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak in `office 365 outlook` als uw filter. In dit voor beeld wordt **contact maken**geselecteerd.

   ![Selecteer de actie die moet worden uitgevoerd in uw logische app](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Als u wordt gevraagd om u aan te melden, geeft u uw Office 365-referenties op zodat uw logische app verbinding kan maken met uw account. Als uw verbinding al bestaat, geeft u de informatie op voor de eigenschappen van de actie.

   In dit voor beeld wordt de map Contacts geselecteerd waarin de actie de nieuwe contact persoon maakt, bijvoorbeeld:

   ![De eigenschappen van de actie configureren](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Als u andere beschik bare actie-eigenschappen wilt toevoegen, selecteert u deze eigenschappen in de lijst **nieuwe para meter toevoegen** .

1. Selecteer **Opslaan** op de werkbalk van de ontwerper.

## <a name="connector-specific-details"></a>Connector-specifieke Details

Zie de [referentie pagina van de connector](/connectors/office365connector/)voor technische informatie over triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
