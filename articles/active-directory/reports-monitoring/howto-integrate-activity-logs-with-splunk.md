---
title: Splunk integreren met behulp van Azure Monitor | Microsoft Docs
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
ms.date: 03/10/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61cae487c588c7649de638d9ea6d3111bfbe9e1a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229681"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Procedure: Azure Active Directory-logboeken integreren met Splunk met behulp van Azure Monitor

In dit artikel leert u hoe u Azure Active Directory (Azure AD)-Logboeken kunt integreren met Splunk met behulp van Azure Monitor. U stuurt de logboeken eerst naar een Azure Event Hub en integreert de Event Hub vervolgens met Splunk.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze functie te gebruiken:

- Een Azure-Event Hub die Azure AD-activiteiten Logboeken bevat. Meer informatie over het [streamen van uw activiteiten logboeken naar een event hub](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 

-  De [Microsoft Azure toevoegen aan voor Splunk](https://splunkbase.splunk.com/app/3757/). 

## <a name="integrate-azure-active-directory-logs"></a>Azure Active Directory-logboeken integreren 

1. Open uw Splunk-exemplaar en selecteer **gegevens overzicht**.

    ![De knop gegevens overzicht](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Selecteer het tabblad **Sourcetypes** en selecteer vervolgens **Amal: aadal: audit**

    ![Het tabblad gegevens overzicht Sourcetypes](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    De activiteiten logboeken van Azure AD worden weer gegeven in de volgende afbeelding:

    ![Activiteitenlogboeken](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Als u een invoeg toepassing niet kunt installeren in uw Splunk-exemplaar (bijvoorbeeld als u een proxy gebruikt of wordt uitgevoerd op de Splunk-Cloud), kunt u deze gebeurtenissen door sturen naar de HTTP-gebeurtenis verzamelaar van Splunk. Als u dit wilt doen, gebruikt u deze [Azure-functie](https://github.com/Microsoft/AzureFunctionforSplunkVS), die wordt geactiveerd door nieuwe berichten in de Event hub. 
>

## <a name="next-steps"></a>Volgende stappen

* [Schema van audit logboeken interpreteren in Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Aanmeldingslogboekenschema interpreteren in Azure Monitor)
* [Veelgestelde vragen en bekende problemen](concept-activity-logs-azure-monitor.md#frequently-asked-questions)