---
title: Logische apps beheren in de Azure-portal
description: Logische apps bewerken, inschakelen, uitschakelen of verwijderen met behulp van de Azure-portal.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/13/2020
ms.openlocfilehash: f726ca90c215c4aff3734bd8022bbc1ad4dc5f87
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415988"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Logische apps beheren in de Azure-portal

U logische apps beheren via de [Azure-portal](https://portal.azure.com) of [Visual Studio.](manage-logic-apps-with-visual-studio.md) In dit artikel ziet u hoe u logische apps in de Azure-portal bewerkt, uitschakelt, in- of verwijdert. Zie Wat is Azure Logic Apps als u nieuw bent in Azure Logic [Apps?](logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een bestaande logische app. Zie [Quickstart: Uw eerste werkstroom maken met Azure Logic Apps - Azure Portal](quickstart-create-first-logic-app-workflow.md)voor meer informatie over het maken van een logische app in de Azure-portal.

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Uw logische apps zoeken

Voer de volgende stappen uit om uw logische app te zoeken en te openen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw Azure-account.

1. Voer in de Azure-zoekbalk Logic Apps in `logic apps`en selecteer deze optie Logic **Apps**.

   ![Zoeken en selecteren "Logische apps"](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. Zoek en selecteer op de pagina **Logic Apps** de logische app die u wilt beheren.

   Nadat het deelvenster **Overzicht** van de logische app is geopend, u de lijst filteren die op de pagina **Logische apps** wordt weergegeven:

   * Zoeken naar logische apps op naam
   * Logische apps filteren op abonnement, brongroep, locatie en tags
   * Logische apps groeperen op resourcegroep, type, abonnement en locatie

## <a name="view-logic-app-properties"></a>Logische app-eigenschappen weergeven

1. Zoek en open uw logische app in de [Azure-portal.](#find-logic-app)

1. Selecteer **Eigenschappen**in het menu van uw logische app onder **Instellingen**.

1. In het deelvenster **Eigenschappen** u de volgende informatie over uw logische app weergeven en kopiëren:

   * **Naam**
   * **Resource-id**
   * **Resourcegroep**
   * **Locatie**
   * **Type** 
   * **Abonnementsnaam**
   * **Subscription ID**
   * **Access-eindpunt**
   * **Uitgaande IP-adressen runtime**
   * **Ip-adressen voor access-eindpunten**
   * **Uitgaande IP-adressen van connector**

## <a name="disable-or-enable-logic-apps"></a>Logische apps uitschakelen of inschakelen

U één [logische app](#disable-enable-single-logic-app) of [meerdere logische apps tegelijk](#disable-or-enable-multiple-logic-apps) in- of uitschakelen in de Azure-portal. U ook [logische apps in- of uitschakelen in Visual Studio](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app).

Het uitschakelen van uw logische app is van invloed op uw werkstroomexemplaren en wordt op de volgende manieren uitgevoerd:

* Alle lopende en lopende runs gaan door tot ze klaar zijn. Afhankelijk van het aantal van deze runs kan dit proces enige tijd in beslag nemen.

* De Logic Apps-engine maakt of voert geen nieuwe werkstroominstanties uit.

* De trekker zal niet vuren de volgende keer dat de voorwaarden zijn voldaan.

* De triggerstatus onthoudt het punt waarop de logische app is gestopt. Dus als u de logische app opnieuw inschakelt, wordt de trigger geactiveerd voor alle onverwerkte items sinds de laatste run.

  Als u wilt voorkomen dat uw logische app sinds de laatste run op onbewerkte items wordt afgevuurd, schakelt u de status van de trigger uit voordat u de logische app opnieuw inschakelt:

  1. Zoek en open uw logische app in de [Azure-portal.](#find-logic-app)

  1. Bewerk een deel van de trigger van de logische app.

  1. Sla uw wijzigingen op. Met deze stap wordt de huidige status van de trigger gereset.

  1. [Uw logische app opnieuw inschakelen](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Eén logische app uitschakelen of inschakelen

1. Zoek en open uw logische app in de [Azure-portal.](#find-logic-app)

1. Selecteer **Overzicht**in het menu van uw logische app . Kies uit de volgende opties:

   * Selecteer op de werkbalk **Disable**.

     ![Eén logische app uitschakelen in Azure-portal](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Als uw logica-app al is uitgeschakeld, ziet u alleen de optie **Inschakelen.**

   * Selecteer op de werkbalk **Inschakelen**.

     ![Eén logische app inschakelen in Azure-portal](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Als uw logische app al is ingeschakeld, ziet u alleen de optie **Uitschakelen.** 

   De Azure-portal toont een melding op de belangrijkste Azure-werkbalk die bevestigt of uw bewerking is geslaagd of mislukt.

   ![Kennisgeving om de bedrijfsstatus te bevestigen](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Meerdere logische apps uitschakelen of inschakelen

1. Zoek in de Azure-portal [de logische apps](#find-logic-app) die u wilt uitschakelen of inschakelen.

1. Als u wilt controleren of een logische app momenteel is ingeschakeld of uitgeschakeld, controleert u op de pagina **Logische apps** de kolom **Status** voor die logische app. 

   ![Statuskolom Logic Apps](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Als de kolom **Status** niet zichtbaar is, selecteert u op de werkbalk **Logische apps** de optie **Voorbeeld proberen**.

   ![Voorbeeld inschakelen](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. Schakel in de kolom selectievakje de logische apps in die u wilt uitschakelen of inschakelen. Selecteer op de werkbalk **Uitschakelen** of **Inschakelen**.

   ![Meerdere logische apps in de Azure-portal in- of uitschakelen](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Wanneer het bevestigingsvak wordt weergegeven, selecteert u **Ja** om door te gaan.

   De Azure-portal toont een melding op de belangrijkste Azure-werkbalk die bevestigt of uw bewerking is geslaagd of mislukt.

## <a name="delete-logic-apps"></a>Logische apps verwijderen

U [één logische app verwijderen](#delete-single-logic-app) of meerdere logische apps tegelijk verwijderen in [de](#delete-multiple-logic-apps) Azure-portal. U uw logische app ook [verwijderen in Visual Studio.](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)

Het verwijderen van uw logische app is van invloed op uw werkstroominstanties op de volgende manieren:

* Alle lopende en lopende runs gaan door tot ze klaar zijn. Afhankelijk van het aantal van deze runs kan dit proces enige tijd in beslag nemen.

* De Logic Apps-engine maakt of voert geen nieuwe werkstroominstanties uit.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Eén logische app verwijderen

1. Zoek en open uw logische app in de [Azure-portal.](#find-logic-app)

1. Selecteer **Overzicht**in het menu van uw logische app . Selecteer Verwijderen op de werkbalk **van**uw logische app .

   ![Selecteer op de werkbalk logische app de optie 'Verwijderen'](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Wanneer het bevestigingsvak wordt weergegeven, voert u de naam van uw logische app in en selecteert u **Verwijderen**.

   ![Bevestigen om uw logische app te verwijderen](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   De Azure-portal toont een melding op de belangrijkste Azure-werkbalk die bevestigt of uw bewerking is geslaagd of mislukt.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Meerdere logische apps verwijderen

1. Zoek in de Azure-portal [de logische apps die u wilt verwijderen.](#find-logic-app)

1. Selecteer in de kolom selectievakje de logische apps die u wilt verwijderen. Selecteer **verwijderen op**de werkbalk .

   ![Meerdere logische apps verwijderen](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Wanneer het bevestigingsvak `yes`wordt weergegeven, voert u Verwijderen in en selecteert u **Verwijderen**.

   ![Bevestigen om uw logische apps te verwijderen](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   De Azure-portal toont een melding op de belangrijkste Azure-werkbalk die bevestigt of uw bewerking is geslaagd of mislukt.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Logische app-versies beheren

U de Azure-portal gebruiken voor versiebeheer van uw logische apps. U de versiegeschiedenis van uw logica-app vinden en eerdere versies promoten.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Vorige versies zoeken en bekijken

1. Zoek in de Azure-portal [de logische app die u wilt beheren.](#find-logic-app)

1. Selecteer in het menu van uw logica-app onder **Hulpmiddelen voor ontwikkeling**de optie **Versies**.

   ![Selecteer in het menu van uw logische app de optie 'Versies' onder 'Ontwikkelhulpmiddelen'.](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. Selecteer de **versie** van uw logische app die u wilt beheren in de lijst. U de **versie-id** invoeren in de zoekbalk om de lijst te filteren.

1. Op de pagina **Geschiedenis ziet** u de details van de vorige versie in de alleen-lezen modus. U kiezen tussen de modi Logic Apps **Designer** en **Code view.**

   ![Pagina geschiedenisversie voor logische app met codeweergave en logische apps-ontwerpweergave](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Eerdere versies promoten

1. [Zoek en selecteer de versie die u wilt promoten](#find-version-history)in de versiegeschiedenis van uw logica-app.

1. Selecteer **op** de pagina Geschiedenis de optie **Promoten**.

   ![Knop Promoten in de versiegeschiedenis van de logische app](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. Bewerk op de pagina **Logic Apps Designer** die wordt geopend de versie die u wilt promoten als dat nodig is. U schakelen tussen **modi voor de weergave van ontwerpers** en **code.** U ook **parameters,** **sjablonen**en **connectoren bijwerken.**

   ![Logische Apps-pagina voor het promoten van een vorige versie](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. Als u eventuele updates wilt opslaan en de vorige versie wilt promoten, selecteert u **Opslaan**. (Of als u uw wijzigingen wilt annuleren, selecteert u **Verwijderen**.) 

   Wanneer u de [versiegeschiedenis van uw logische app](#find-version-history) opnieuw bekijkt, wordt de gepromote versie boven aan de lijst weergegeven en heeft deze een nieuwe id.

## <a name="next-steps"></a>Volgende stappen

* [Logische apps bewaken](monitor-logic-apps.md)
