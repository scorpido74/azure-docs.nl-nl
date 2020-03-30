---
title: Logboeken streamen naar SumoLogic met Azure Monitor | Microsoft Documenten
description: Meer informatie over het integreren van Azure Active Directory-logboeken met SumoLogic met Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
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
ms.openlocfilehash: 6cdfc4e393ca7bf4bcbd523b4fad72690d5f2744
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014392"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Azure Active Directory-logboeken integreren met SumoLogic met Azure Monitor

In dit artikel vindt u informatie over het integreren van Azure Active Directory -logboeken (Azure AD) met SumoLogic met Azure Monitor. U routeert de logboeken eerst naar een Azure-gebeurtenishub en vervolgens de gebeurtenishub met SumoLogic.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze functie te gebruiken:
* Een Azure-gebeurtenishub die Azure AD-activiteitslogboeken bevat. Meer informatie over het [streamen van uw activiteitslogboeken naar een gebeurtenishub](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Een SumoLogic-abonnement met één aanmelding.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Stappen om Azure AD-logboeken te integreren met SumoLogic 

1. Stream [eerst de Azure AD-logboeken naar een Azure-gebeurtenishub.](quickstart-azure-monitor-stream-logs-to-event-hub.md)
2. Configureer uw SumoLogic-exemplaar om [logboeken te verzamelen voor Azure Active Directory.](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory)
3. [Installeer de Azure AD SumoLogic-app](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) om de vooraf geconfigureerde dashboards te gebruiken die realtime analyse van uw omgeving bieden.

   ![Dashboard](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Volgende stappen

* [Controlelogboekenschema interpreteren in Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Aanmeldingslogboekenschema interpreteren in Azure Monitor)
* [Veelgestelde vragen en bekende problemen](concept-activity-logs-azure-monitor.md#frequently-asked-questions)