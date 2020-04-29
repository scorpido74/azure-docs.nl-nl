---
title: Logische apps beheren in de Azure Portal
description: Logische apps bewerken, inschakelen, uitschakelen of verwijderen met behulp van de Azure Portal.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/13/2020
ms.openlocfilehash: f726ca90c215c4aff3734bd8022bbc1ad4dc5f87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415988"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Logische apps beheren in de Azure Portal

U kunt logische apps beheren met behulp van de [Azure Portal](https://portal.azure.com) of [Visual Studio](manage-logic-apps-with-visual-studio.md). In dit artikel wordt beschreven hoe u logische apps in de Azure Portal kunt bewerken, uitschakelen, inschakelen of verwijderen. Als u nog geen ervaring hebt met Azure Logic Apps, raadpleegt u [Wat is Azure Logic apps](logic-apps-overview.md)?

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een bestaande logische app. Voor meer informatie over het maken van een logische app in de Azure Portal, raadpleegt u [Quick Start: uw eerste werk stroom maken met behulp van Azure Logic apps-Azure Portal](quickstart-create-first-logic-app-workflow.md).

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Uw Logic apps zoeken

Voer de volgende stappen uit om uw logische app te zoeken en te openen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw Azure-account.

1. Voer `logic apps`in de zoek balk van Azure het selectie vakje **Logic apps**in.

   ![Zoek en selecteer ' Logic Apps '](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. Zoek en selecteer op de pagina **Logic apps** de logische app die u wilt beheren.

   Nadat het **overzichts** venster van de logische app wordt geopend, kunt u de lijst die op de **Logic apps** pagina wordt weer gegeven op de volgende manieren filteren:

   * Op naam zoeken naar Logic apps
   * Logische apps filteren op abonnement, resource groep, locatie en Tags
   * Logische apps groeperen op resource groep, type, abonnement en locatie

## <a name="view-logic-app-properties"></a>Eigenschappen van logische app weer geven

1. [Zoek en open uw logische app](#find-logic-app)In de Azure Portal.

1. Selecteer in het menu van de logische app onder **instellingen**de optie **Eigenschappen**.

1. In het deel venster **Eigenschappen** kunt u de volgende informatie over uw logische app bekijken en kopiëren:

   * **Naam**
   * **Resource-ID**
   * **Resourcegroep**
   * **Locatie**
   * **Type** 
   * **Abonnements naam**
   * **Subscription ID**
   * **Toegangs eindpunt**
   * **Uitgaande IP-adressen voor runtime**
   * **Toegangs punt-IP-adressen**
   * **Uitgaande IP-adressen van connector**

## <a name="disable-or-enable-logic-apps"></a>Logische apps in-of uitschakelen

U kunt [één logische app](#disable-enable-single-logic-app) of [meerdere Logic apps tegelijk](#disable-or-enable-multiple-logic-apps) in-of uitschakelen in de Azure Portal. U kunt [logische apps ook in-of uitschakelen in Visual Studio](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app).

Het uitschakelen van uw logische app heeft gevolgen voor uw workflowexemplaren en wordt op de volgende manieren uitgevoerd:

* Alle lopende uitvoeringen en in behandeling zijnde uitvoeringen gaan door totdat ze zijn voltooid. Afhankelijk van het aantal uitvoeringen kan dit proces enige tijd in beslag nemen.

* De Logic Apps-Engine maakt of voert geen nieuwe exemplaren van werk stromen uit.

* De trigger wordt niet geactiveerd wanneer aan de volgende voor waarden wordt voldaan.

* De trigger status onthoudt het punt waarop de logische app is gestopt. Als u de logische app opnieuw inschakelt, wordt de trigger geactiveerd voor alle niet-verwerkte items sinds de laatste uitvoering.

  Als u wilt voor komen dat uw logische app wordt geactiveerd voor niet-verwerkte items sinds de laatste uitvoering, wist u de status van de trigger voordat u de logische app opnieuw inschakelt:

  1. [Zoek en open uw logische app](#find-logic-app)In de Azure Portal.

  1. Bewerk elk deel van de trigger van de logische app.

  1. Sla uw wijzigingen op. Met deze stap wordt de huidige status van de trigger opnieuw ingesteld.

  1. [Schakel de logische app opnieuw in](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Eén logische app in-of uitschakelen

1. [Zoek en open uw logische app](#find-logic-app)In de Azure Portal.

1. Selecteer **overzicht**in het menu van de logische app. Kies uit de volgende opties:

   * Selecteer **uitschakelen**op de werk balk.

     ![Een app met één logische invoeg toepassing in Azure Portal uitschakelen](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Als uw logische app al is uitgeschakeld, ziet u alleen de optie **inschakelen** .

   * Selecteer **inschakelen**op de werk balk.

     ![Eén logische app inschakelen in Azure Portal](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Als uw logische app al is ingeschakeld, ziet u alleen de optie **uitschakelen** . 

   In de Azure Portal wordt een melding weer gegeven op de hoofdwerk balk van Azure, waarin wordt bevestigd of uw bewerking is geslaagd of mislukt.

   ![Melding voor het bevestigen van de bewerkings status](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Meerdere Logic apps in-of uitschakelen

1. Zoek in het Azure Portal [naar de Logic apps](#find-logic-app) die u wilt in-of uitschakelen.

1. Als u wilt controleren of een logische app op dat moment is ingeschakeld of uitgeschakeld, controleert u op de pagina **Logic apps** de kolom **status** voor die logische app. 

   ![Kolom Logic Apps status](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Als de kolom **status** niet wordt weer gegeven, selecteert u op de **Logic apps** -werk balk de optie **Preview uitproberen**.

   ![Preview inschakelen](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. Selecteer in de kolom selectie vakje de Logic apps die u wilt in-of uitschakelen. Selecteer in de werk balk de optie **uitschakelen** of **inschakelen**.

   ![Meerdere Logic apps in-of uitschakelen in de Azure Portal](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Wanneer het bevestigings venster wordt weer gegeven, selecteert u **Ja** om door te gaan.

   In de Azure Portal wordt een melding weer gegeven op de hoofdwerk balk van Azure, waarin wordt bevestigd of uw bewerking is geslaagd of mislukt.

## <a name="delete-logic-apps"></a>Logic apps verwijderen

U kunt [één logische app verwijderen](#delete-single-logic-app) of [op hetzelfde moment meerdere Logic apps verwijderen](#delete-multiple-logic-apps) in de Azure Portal. U kunt ook [uw logische app verwijderen in Visual Studio](manage-logic-apps-with-visual-studio.md#delete-your-logic-app).

Het verwijderen van uw logische app heeft invloed op uw werk stroom exemplaren op de volgende manieren:

* Alle lopende uitvoeringen en in behandeling zijnde uitvoeringen gaan door totdat ze zijn voltooid. Afhankelijk van het aantal uitvoeringen kan dit proces enige tijd in beslag nemen.

* De Logic Apps-Engine maakt of voert geen nieuwe exemplaren van werk stromen uit.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Eén logische app verwijderen

1. [Zoek en open uw logische app](#find-logic-app)In de Azure Portal.

1. Selecteer **overzicht**in het menu van de logische app. Selecteer **verwijderen**op de werk balk van de logische app.

   ![Selecteer verwijderen op de werk balk van de logische app.](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Wanneer het bevestigings venster wordt weer gegeven, voert u de naam van de logische app in en selecteert u **verwijderen**.

   ![Bevestigen om de logische app te verwijderen](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   In de Azure Portal wordt een melding weer gegeven op de hoofdwerk balk van Azure, waarin wordt bevestigd of uw bewerking is geslaagd of mislukt.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Meerdere Logic apps verwijderen

1. Zoek in de Azure Portal [de Logic apps die u wilt verwijderen](#find-logic-app).

1. Selecteer in de kolom selectie vakje de Logic apps die u wilt verwijderen. Selecteer **verwijderen**op de werk balk.

   ![Meerdere Logic apps verwijderen](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Wanneer het bevestigings venster wordt weer gegeven `yes`, voert u in en selecteert u **verwijderen**.

   ![Bevestigen om uw Logic apps te verwijderen](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   In de Azure Portal wordt een melding weer gegeven op de hoofdwerk balk van Azure, waarin wordt bevestigd of uw bewerking is geslaagd of mislukt.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Logische app-versies beheren

U kunt de Azure Portal gebruiken voor versie beheer van uw logische apps. U kunt de versie geschiedenis van uw logische app vinden en de vorige versies promoten.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Eerdere versies zoeken en weer geven

1. Zoek in de Azure Portal [de logische app die u wilt beheren](#find-logic-app).

1. Selecteer in het menu van de logische app onder **ontwikkelingsprogram ma's**de optie **versies**.

   ![Selecteer in het menu van de logische app de optie ' versies ' onder ' ontwikkelingsprogram Ma's '](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. Selecteer de **versie** van uw logische app die u wilt beheren in de lijst. U kunt de **versie** -id opgeven in de zoek balk om de lijst te filteren.

1. Op de pagina **geschiedenis versie** ziet u de details van de vorige versie in de modus alleen-lezen. U kunt kiezen tussen de Logic Apps **Designer** -en **code weergave** modus.

   ![Pagina geschiedenis versie van de logische app met de ontwerp weergave voor code weergave en Logic apps](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Eerdere versies promoten

1. Zoek in de versie geschiedenis van de logische app [naar de versie die u wilt promoten en selecteer](#find-version-history)deze.

1. Op de pagina **geschiedenis versie** selecteert u **niveau verhogen**.

   ![De knop niveau verhogen in de versie geschiedenis van de logische app](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. Bewerk op de pagina **Logic apps Designer** die wordt geopend, de versie die u wilt promo veren als dat nodig is. U kunt scha kelen tussen de **Designer** -en **code weergave** modus. U kunt ook **para meters**, **sjablonen**en **connectors**bijwerken.

   ![Logic Apps Designer-pagina voor het promo veren van een vorige versie](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. Selecteer **Opslaan**om de updates op te slaan en de promotie van de vorige versie te volt ooien. (Als u de wijzigingen wilt annuleren, selecteert u **verwijderen**.) 

   Wanneer u de [versie geschiedenis van de logische app opnieuw bekijkt](#find-version-history) , wordt de gepromoveerde versie boven aan de lijst weer gegeven en heeft deze een nieuwe id.

## <a name="next-steps"></a>Volgende stappen

* [Logische apps bewaken](monitor-logic-apps.md)
