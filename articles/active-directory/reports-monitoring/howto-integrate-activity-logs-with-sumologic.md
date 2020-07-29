---
title: Stream-logboeken naar SumoLogic met behulp van Azure Monitor | Microsoft Docs
description: Meer informatie over het integreren van Azure Active Directory-logboeken met SumoLogic met behulp van Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91c3b1216d5ced766a77bb1fb63e41eaedb8220d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608973"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Azure Active Directory-logboeken integreren met SumoLogic met behulp van Azure Monitor

In dit artikel leert u hoe u Azure Active Directory (Azure AD)-logboeken integreert met SumoLogic met behulp van Azure Monitor. U stuurt de logboeken eerst naar een Azure Event Hub en integreert de Event Hub vervolgens met SumoLogic.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze functie te gebruiken:
* Een Azure-Event Hub die Azure AD-activiteiten Logboeken bevat. Meer informatie over het [streamen van uw activiteiten logboeken naar een event hub](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Een SumoLogic-abonnement met eenmalige aanmelding.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Stappen voor het integreren van Azure AD-logboeken met SumoLogic 

1. Eerst [moet u de Azure AD-logboeken streamen naar een Azure-Event hub](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Configureer uw SumoLogic-exemplaar voor het [verzamelen van Logboeken voor Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Installeer de Azure AD SumoLogic-app](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) voor het gebruik van de vooraf geconfigureerde Dash boards die een realtime-analyse van uw omgeving bieden.

   ![Dashboard](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Volgende stappen

* [Schema van audit logboeken interpreteren in Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Aanmeldingslogboekenschema interpreteren in Azure Monitor)
* [Veelgestelde vragen en bekende problemen](concept-activity-logs-azure-monitor.md#frequently-asked-questions)