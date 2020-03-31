---
title: Activiteitslogboeken analyseren met Azure Monitor-logboeken | Microsoft Documenten
description: Meer informatie over het analyseren van Azure Active Directory-activiteitslogboeken met Azure Monitor-logboeken
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
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
ms.openlocfilehash: 2d6212692465270182db541889bed5f03a08a345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008276"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Azure AD-activiteitslogboeken analyseren met Azure Monitor-logboeken

Nadat u [Azure AD-activiteitslogboeken hebt geïntegreerd met Azure Monitor-logboeken,](howto-integrate-activity-logs-with-log-analytics.md)u de kracht van Azure Monitor-logboeken gebruiken om inzicht te krijgen in uw omgeving. U ook de [logboekanalyseweergaven voor Azure AD-activiteitslogboeken](howto-install-use-log-analytics-views.md) installeren om toegang te krijgen tot vooraf gebouwde rapporten rond audit- en aanmeldingsgebeurtenissen in uw omgeving.

In dit artikel leert u hoe u de Azure AD-activiteitslogboeken in uw werkruimte Log Analytics analyseert. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten 

Om mee te gaan, heb je het volgende nodig:

* Een Werkruimte logAnalytics in uw Azure-abonnement. Meer informatie over het [maken van een Log Analytics-werkruimte](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Voer eerst de stappen uit om [de Azure AD-activiteitslogboeken naar uw logboekanalysewerkruimte te routeren.](howto-integrate-activity-logs-with-log-analytics.md)
*  [Toegang tot](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) de werkruimte voor logboekanalyse
* De volgende rollen in Azure Active Directory (als u Log Analytics opent via Azure Active Directory-portal)
    - Beveiligingsbeheerder
    - Beveiligingslezer
    - Rapportlezer
    - Globale beheerder
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Navigeren naar de werkruimte Log Analytics

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 

2. Selecteer **Azure Active Directory**en selecteer **Logboeken** in de sectie **Controle** om de werkruimte Logboekanalyse te openen. De werkruimte wordt geopend met een standaardquery.

    ![Standaardquery](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Het schema voor Azure AD-activiteitslogboeken weergeven

De logboeken worden naar de tabellen **AuditLogs** en **SigninLogs** in de werkruimte geschoven. Ga als volgende over het schema voor deze tabellen:

1. Selecteer **Schema** en vouw de werkruimte uit in de standaardqueryweergave in de vorige sectie. 

2. Vouw de sectie **Logboekbeheer** uit en vouw **vervolgens AuditLogs** of **SignInLogs** uit om het logboekschema weer te geven.
    ![Logboeklogboeken controleren](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>De Azure AD-activiteitslogboeken opvragen

Nu u de logboeken in uw werkruimte hebt, u nu query's tegen deze query's uitvoeren. Als u bijvoorbeeld de beste toepassingen in de afgelopen week wilt laten gebruiken, vervangt u de standaardquery met de volgende query en selecteert u **Uitvoeren**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Als u de belangrijkste controlegebeurtenissen van de afgelopen week wilt ontvangen, gebruikt u de volgende query:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Waarschuwing op azure AD-activiteitslogboekgegevens

U ook waarschuwingen instellen voor uw zoekopdracht. Bijvoorbeeld om een waarschuwing te configureren wanneer er in de afgelopen week meer dan 10 toepassingen zijn gebruikt:

1. Selecteer in de werkruimte **De optie Waarschuwing instellen** om de pagina Regel **maken** te openen.

    ![Waarschuwing instellen](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Selecteer de **standaardwaarschuwingscriteria** die in de waarschuwing zijn gemaakt en werk de **drempelwaarde** in de standaardstatistiek bij op 10.

    ![Waarschuwingscriteria](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Voer een naam en beschrijving in voor de waarschuwing en kies het ernstniveau. Voor ons voorbeeld kunnen we het instellen op **Informational**.

4. Selecteer de **actiegroep** die wordt gewaarschuwd wanneer het signaal optreedt. U ervoor kiezen om uw team hiervan op de hoogte te stellen via e-mail of sms, of u de actie automatiseren met webhooks, Azure-functies of logische apps. Meer informatie over [het maken en beheren van waarschuwingsgroepen in de Azure-portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. Zodra u de waarschuwing hebt geconfigureerd, selecteert u **Waarschuwing maken** om deze in te schakelen. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Vooraf gebouwde weergaven voor Azure AD-activiteitslogboeken installeren en gebruiken

U ook de vooraf gebouwde logboekanalyseweergaven voor Azure AD-activiteitslogboeken downloaden. De weergaven bieden verschillende rapporten met betrekking tot veelvoorkomende scenario's met betrekking tot audit- en aanmeldingsgebeurtenissen. U ook een waarschuwing geven over een van de gegevens in de rapporten, met behulp van de stappen die in de vorige sectie zijn beschreven.

* **Azure AD-accountinrichtingsgebeurtenissen:** in deze weergave worden rapporten weergegeven met betrekking tot de controlebepalingsactiviteit, zoals het aantal nieuwe gebruikers dat in- en inrichtingsfouten heeft ingericht en ingericht, het aantal gebruikers dat is bijgewerkt en updatefouten en het aantal gebruikers dat is ingericht en bijbehorende fouten.    
* **Aanmeldingsgebeurtenissen**: deze weergave toont de meest relevante rapporten met betrekking tot het bewaken van aanmeldingsactiviteiten, zoals aanmeldingen per toepassing, gebruiker, apparaat en een overzichtsweergave die het aantal aanmeldingen in de loop van de tijd bijhoudt.
* **Gebruikers die toestemming geven**: In deze weergave worden rapporten weergegeven met betrekking tot toestemming van gebruikers, zoals de toestemmingssubsidies door de gebruiker, aanmeldingen door gebruikers die toestemming hebben verleend, evenals aanmeldingen per aanvraag voor alle op toestemming gebaseerde toepassingen. 

Ontdek hoe u [Log Analytics-weergaven voor activiteitenlogboeken van Azure AD installeert en gebruikt](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met query's in Azure Monitor-logboeken](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Waarschuwingsgroepen maken en beheren in de Azure-portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [De weergave van logboekanalyses voor Azure Active Directory installeren en gebruiken](howto-install-use-log-analytics-views.md)
