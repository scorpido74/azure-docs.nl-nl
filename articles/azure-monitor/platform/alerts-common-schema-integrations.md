---
title: Het algemene waarschuwingsschema integreren met Logic Apps
description: Meer informatie over het maken van een logische app die gebruikmaakt van het algemene waarschuwingsschema om al uw waarschuwingen te verwerken.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 9042ed8ddbb698192e638fa7538f74561574c262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668227"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Het algemene waarschuwingsschema integreren met Logic Apps

In dit artikel ziet u hoe u een logische app maakt die gebruikmaakt van het algemene waarschuwingsschema om al uw waarschuwingen te verwerken.

## <a name="overview"></a>Overzicht

Het [algemene waarschuwingsschema](https://aka.ms/commonAlertSchemaDocs) biedt een gestandaardiseerd en uitbreidbaar JSON-schema voor al uw verschillende waarschuwingstypen. Het algemene waarschuwingsschema is het handigst wanneer het programmatisch wordt gebruikt - via webhooks, runbooks en logische apps. In dit artikel laten we zien hoe één logische app kan worden geschreven om al uw waarschuwingen te verwerken. Dezelfde principes kunnen worden toegepast op andere programmatische methoden. De logica-app die in dit artikel wordt beschreven, maakt goed gedefinieerde variabelen voor de ['essentiële' velden](alerts-common-schema-definitions.md#essentials)en beschrijft ook hoe u met [waarschuwingstypespecifieke](alerts-common-schema-definitions.md#alert-context) logica omgaan.


## <a name="prerequisites"></a>Vereisten 

In dit artikel wordt ervan uitgegaan dat de lezer bekend is met 
* Waarschuwingsregels instellen ([metriek](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [logboek](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [activiteitenlogboek](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* [Actiegroepen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) instellen
* Het [algemene waarschuwingsschema](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) inschakelen vanuit actiegroepen

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Een logische app maken die gebruikmaakt van het algemene waarschuwingsschema

1. Volg de [stappen die zijn beschreven om uw logische app te maken.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app) 

1.  Selecteer de trigger: **Wanneer een HTTP-aanvraag is ontvangen.**

    ![Triggers voor logische app](media/action-groups-logic-app/logic-app-triggers.png "Triggers voor logische app")

1.  Selecteer **Bewerken** om de HTTP-aanvraagtrigger te wijzigen.

    ![HTTP-aanvraagtriggers](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-aanvraagtriggers")


1.  Kopieer en plak het volgende schema:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Selecteer **+** **Nieuwe stap** en kies Een actie **toevoegen**.

    ![Een actie toevoegen](media/action-groups-logic-app/add-action.png "Een actie toevoegen")

1. In dit stadium u verschillende connectors (Microsoft Teams, Slack, Salesforce, enz.) toevoegen op basis van uw specifieke bedrijfsvereisten. U de 'essentiële velden' out-of-the-box gebruiken. 

    ![Essentiële velden](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Essentiële velden")
    
    U ook voorwaardelijke logica maken op basis van het waarschuwingstype met de optie 'Expressie'.

    ![Logische app-expressie](media/alerts-common-schema-integrations/logic-app-expressions.png "Logische app-expressie")
    
     Met [het veld 'monitoringService'](alerts-common-schema-definitions.md#alert-context) u op unieke wijze het waarschuwingstype identificeren, op basis waarvan u de voorwaardelijke logica maken.

    
    In het onderstaande fragment wordt bijvoorbeeld gecontroleerd of de waarschuwing een logwaarschuwing voor Application Insights is en zo ja, hiermee worden de zoekresultaten afgedrukt. Anders drukt het 'NA' af.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Meer informatie over [het schrijven van logische app-expressies](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over actiegroepen](../../azure-monitor/platform/action-groups.md).
* [Meer informatie over het algemene waarschuwingsschema](https://aka.ms/commonAlertSchemaDocs).

