---
title: De weergaven logboekanalyse installeren en gebruiken | Microsoft Documenten
description: Meer informatie over het installeren en gebruiken van de logboekanalyseweergaven voor Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b17026e4cfbe69e36c8e459aa259fe16b1c9d80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014423"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>De weergave van logboekanalyses voor Azure Active Directory installeren en gebruiken

Met de overzichtsweergaven van Azure Active Directory-logboekanalyses u de AD-activiteitslogboeken van Azure in uw Azure AD-tenant analyseren en doorzoeken. Azure AD-activiteitslogboeken omvatten:

* Controlelogboeken: Het [activiteitenrapport met controlelogboeken](concept-audit-logs.md) geeft u toegang tot de geschiedenis van elke taak die in uw tenant wordt uitgevoerd.
* Aanmeldingslogboeken: met het [aanmeldingsrapport](concept-sign-ins.md)u bepalen wie de taken heeft uitgevoerd die worden gerapporteerd in de controlelogboeken.

## <a name="prerequisites"></a>Vereisten

Als u de weergaven logboekanalyse wilt gebruiken, moet u het andere doen:

* Een Werkruimte logAnalytics in uw Azure-abonnement. Meer informatie over het [maken van een Log Analytics-werkruimte](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Voer eerst de stappen uit om [de Azure AD-activiteitslogboeken naar uw logboekanalysewerkruimte te routeren.](howto-integrate-activity-logs-with-log-analytics.md)
* Download de weergaven van de [GitHub-opslagplaats](https://aka.ms/AADLogAnalyticsviews) naar uw lokale computer.

## <a name="install-the-log-analytics-views"></a>De weergaven van logboekanalyse installeren

1. Navigeer naar uw werkruimte Log Analytics. Ga hiervoor eerst naar de [Azure-portal](https://portal.azure.com) en selecteer **Alle services**. Typ **Log Analytics** in het tekstvak en selecteer Log **Analytics-werkruimten**. Selecteer de werkruimte waarnaar u de activiteitslogboeken hebt doorgestuurd, als onderdeel van de vereisten.
2. Selecteer **Ontwerper weergeven,** selecteer **Importeren** en selecteer Vervolgens **Bestand kiezen** om de weergaven van uw lokale computer te importeren.
3. Selecteer de weergaven die u hebt gedownload uit de vereisten en selecteer **Opslaan** om de import op te slaan. Doe dit voor de weergave **Azure AD-accountinrichtingsgebeurtenissen** en de weergave **Aanmeldingsgebeurtenissen.**

## <a name="use-the-views"></a>De weergaven gebruiken

1. Navigeer naar uw werkruimte Log Analytics. Ga hiervoor eerst naar de [Azure-portal](https://portal.azure.com) en selecteer **Alle services**. Typ **Log Analytics** in het tekstvak en selecteer Log **Analytics-werkruimten**. Selecteer de werkruimte waarnaar u de activiteitslogboeken hebt doorgestuurd, als onderdeel van de vereisten.

2. Zodra u zich in de werkruimte bevindt, selecteert u **Werkruimteoverzicht**. U ziet de volgende drie weergaven:

    * **Azure AD-accountinrichtingsgebeurtenissen:** in deze weergave worden rapporten weergegeven met betrekking tot de controlebepalingsactiviteit, zoals het aantal nieuwe gebruikers dat in- en inrichtingsfouten heeft ingericht en ingericht, het aantal gebruikers dat is bijgewerkt en updatefouten en het aantal gebruikers dat is ingericht en bijbehorende fouten.    
    * **Aanmeldingsgebeurtenissen**: deze weergave toont de meest relevante rapporten met betrekking tot het bewaken van aanmeldingsactiviteiten, zoals aanmeldingen per toepassing, gebruiker, apparaat en een overzichtsweergave die het aantal aanmeldingen in de loop van de tijd bijhoudt.

3. Selecteer een van deze weergaven om in de afzonderlijke rapporten te springen. U ook waarschuwingen instellen voor een van de rapportparameters. Laten we bijvoorbeeld een waarschuwing instellen voor elke keer dat er een aanmeldingsfout optreedt. Selecteer hiervoor eerst de weergave **Aanmeldingsgebeurtenissen,** selecteer **Aanmeldingsfouten in de loop van de tijd** en selecteer vervolgens **Analytics** om de pagina details te openen, met de werkelijke query achter het rapport. 

    ![Details](./media/howto-install-use-log-analytics-views/details.png)


4. Selecteer **Waarschuwing instellen**en selecteer wanneer de aangepaste **logboekzoekopdracht logica is die niet is &lt;gedefinieerd&gt; ** onder de sectie **Waarschuwingscriteria.** Omdat we willen waarschuwen wanneer er een aanmeldingsfout optreedt, stelt u de **drempelwaarde** voor de standaardwaarschuwingslogica in op **1** en selecteert u **Gereed**. 

    ![Signaallogica configureren](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Voer een naam en beschrijving in voor de waarschuwing en stel de ernst in **op Waarschuwing.**

    ![Regel maken](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Selecteer de actiegroep die u wilt waarschuwen. In het algemeen kan dit een team zijn dat u via e-mail of sms wilt melden, of het kan een geautomatiseerde taak zijn met behulp van webhooks, runbooks, functies, logische apps of externe ITSM-oplossingen. Meer informatie over het [maken en beheren van actiegroepen in de Azure-portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Selecteer **Waarschuwingsregel maken** om de waarschuwing te maken. Nu wordt u elke keer gewaarschuwd als er een aanmeldingsfout optreedt.

## <a name="next-steps"></a>Volgende stappen

* [Activiteitslogboeken analyseren met Azure Monitor-logboeken](howto-analyze-activity-logs-log-analytics.md)
* [Aan de slag met Azure Monitor-logboeken in de Azure-portal](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)