---
title: De weer gaven van log Analytics installeren en gebruiken | Microsoft Docs
description: Meer informatie over het installeren en gebruiken van de log Analytics-weer gaven voor Azure Active Directory
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
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014423"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>De log Analytics-weer gaven voor Azure Active Directory installeren en gebruiken

Met de weer gaven van de Azure Active Directory log Analytics kunt u de Azure AD-activiteiten logboeken analyseren en doorzoeken in uw Azure AD-Tenant. Azure AD-activiteiten logboeken zijn onder andere:

* Audit logboeken: in het [activiteiten rapport van controle logboeken](concept-audit-logs.md) krijgt u toegang tot de geschiedenis van elke taak die wordt uitgevoerd in uw Tenant.
* Aanmeld logboeken: met het [rapport aanmeldings activiteit](concept-sign-ins.md)kunt u bepalen wie de taken heeft uitgevoerd die worden gerapporteerd in de audit Logboeken.

## <a name="prerequisites"></a>Vereisten

Als u de log Analytics-weer gaven wilt gebruiken, hebt u het volgende nodig:

* Een Log Analytics-werk ruimte in uw Azure-abonnement. Meer informatie over het [maken van een log Analytics-werk ruimte](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Voer eerst de stappen uit om [de Azure AD-activiteiten logboeken te routeren naar uw log Analytics-werk ruimte](howto-integrate-activity-logs-with-log-analytics.md).
* Down load de weer gaven van de [github-opslag plaats](https://aka.ms/AADLogAnalyticsviews) naar uw lokale computer.

## <a name="install-the-log-analytics-views"></a>De log Analytics-weer gaven installeren

1. Navigeer naar uw Log Analytics-werk ruimte. Als u dit wilt doen, gaat u eerst naar de [Azure Portal](https://portal.azure.com) en selecteert u **alle services**. Typ **log Analytics** in het tekstvak en selecteer log Analytics- **werk ruimten**. Selecteer de werk ruimte waarnaar u de activiteiten Logboeken hebt gerouteerd als onderdeel van de vereisten.
2. Selecteer **Designer weer geven**, selecteer **importeren** en selecteer vervolgens **bestand kiezen** om de weer gaven van uw lokale computer te importeren.
3. Selecteer de weer gaven die u hebt gedownload uit de vereisten en selecteer **Opslaan** om de import op te slaan. Doe dit voor de weer gave gebeurtenissen voor het **inrichten van Azure AD-accounts** en de weer gave van de **aanmeld gebeurtenissen** .

## <a name="use-the-views"></a>De weer gaven gebruiken

1. Navigeer naar uw Log Analytics-werk ruimte. Als u dit wilt doen, gaat u eerst naar de [Azure Portal](https://portal.azure.com) en selecteert u **alle services**. Typ **log Analytics** in het tekstvak en selecteer log Analytics- **werk ruimten**. Selecteer de werk ruimte waarnaar u de activiteiten Logboeken hebt gerouteerd als onderdeel van de vereisten.

2. Zodra u zich in de werk ruimte bevindt, selecteert u **werkruimte samenvatting**. De volgende drie weer gaven moeten worden weer gegeven:

    * **Gebeurtenissen voor het inrichten van Azure AD-accounts**: deze weer gave bevat rapporten met betrekking tot het controleren van inrichtings activiteiten, zoals het aantal nieuwe gebruikers dat is ingericht en het inrichten van fouten, het aantal gebruikers dat is bijgewerkt en het bijwerken van fouten en het aantal gebruikers dat is ingericht en de bijbehorende fouten.    
    * **Gebeurtenissen voor aanmeldingen**: deze weer gave bevat de meest relevante rapporten met betrekking tot het controleren van de aanmeldings activiteit, zoals aanmeldingen per toepassing, gebruiker, apparaat en een samen vatting van het aantal aanmeldingen in de loop van de tijd.

3. Selecteer een van deze weer gaven om naar de afzonderlijke rapporten te gaan. U kunt ook waarschuwingen instellen voor een van de rapport parameters. Laten we bijvoorbeeld een waarschuwing instellen voor elke keer dat er een fout is opgetreden bij het aanmelden. Als u dit wilt doen, selecteert u eerst de weer gave voor de **aanmeldings gebeurtenissen** , selecteert u **aanmeldings fouten in een tijd** rapport en selecteert u vervolgens **Analytics** om de pagina Details te openen, met de daad werkelijke query achter het rapport. 

    ![Details](./media/howto-install-use-log-analytics-views/details.png)


4. Selecteer **waarschuwing instellen**en selecteer vervolgens **wanneer de aangepaste zoek opdracht voor logboeken is &lt;logica niet gedefinieerd&gt;** onder de sectie **waarschuwings criteria** . Omdat we willen waarschuwen wanneer er zich een aanmeldings fout **voordoet**, stelt u de **drempel waarde** van de standaard waarschuwings logica in op **1** en selecteert u vervolgens gereed. 

    ![Signaallogica configureren](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Voer een naam en beschrijving voor de waarschuwing in en stel de ernst in op **waarschuwing**.

    ![Regel maken](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Selecteer de actie groep waarvoor u een waarschuwing wilt ontvangen. In het algemeen kan dit een team zijn dat u per e-mail of SMS-bericht wilt laten weten, of het kan een geautomatiseerde taak zijn via webhooks, runbooks, functies, Logic apps of externe ITSM-oplossingen. Meer informatie over [het maken en beheren van actie groepen in de Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Selecteer **waarschuwings regel maken** om de waarschuwing te maken. U wordt nu gewaarschuwd wanneer er een fout is opgetreden bij het aanmelden.

## <a name="next-steps"></a>Volgende stappen

* [Activiteiten logboeken analyseren met Azure Monitor-logboeken](howto-analyze-activity-logs-log-analytics.md)
* [Aan de slag met Azure Monitor-Logboeken in de Azure Portal](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)