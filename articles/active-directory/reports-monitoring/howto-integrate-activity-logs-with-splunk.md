---
title: Splunk integreren met Azure Monitor | Microsoft Documenten
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
ms.date: 03/10/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eda3643a7b1a341c7ed664dbfea933145f1f927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968714"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>How to: Azure Active Directory logs integreren met Splunk met Azure Monitor

In dit artikel leert u hoe u Azure Active Directory -logboeken (Azure AD) integreert met Splunk met Azure Monitor. U routeert de logboeken eerst naar een Azure-gebeurtenishub en vervolgens de gebeurtenishub met Splunk.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze functie te gebruiken:

- Een Azure-gebeurtenishub die Azure AD-activiteitslogboeken bevat. Meer informatie over het [streamen van uw activiteitslogboeken naar een gebeurtenishub](quickstart-azure-monitor-stream-logs-to-event-hub.md). 

-  De [Microsoft Azure Add on voor Splunk](https://splunkbase.splunk.com/app/3757/). 

## <a name="integrate-azure-active-directory-logs"></a>Azure Active Directory-logboeken integreren 

1. Open de instantie Splunk en selecteer **Gegevensoverzicht**.

    ![De knop 'Gegevensoverzicht'](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Selecteer het tabblad **Brontypen** en selecteer **vervolgens amal: aadal:audit**

    ![Het tabblad Brontypen voor gegevensoverzicht](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    De Azure AD-activiteitslogboeken worden weergegeven in de volgende afbeelding:

    ![Activiteitenlogboeken](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Als u een add-on niet installeren in uw Splunk-exemplaar (bijvoorbeeld als u een proxy gebruikt of op Splunk Cloud draait), u deze gebeurtenissen doorsturen naar de Splunk HTTP-gebeurtenisverzamelaar. Gebruik hiervoor deze [Azure-functie](https://github.com/Microsoft/AzureFunctionforSplunkVS), die wordt geactiveerd door nieuwe berichten in de gebeurtenishub. 
>

## <a name="next-steps"></a>Volgende stappen

* [Controlelogboekenschema interpreteren in Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Aanmeldingslogboekenschema interpreteren in Azure Monitor)
* [Veelgestelde vragen en bekende problemen](concept-activity-logs-azure-monitor.md#frequently-asked-questions)