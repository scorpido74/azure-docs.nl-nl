---
title: Werkstroomautomatisering in Azure Security Center | Microsoft Documenten
description: Meer informatie over het maken en automatiseren van werkstromen in Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5d947cf41e13abdea9a2fd29f8a740d0c101dc6f
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397916"
---
# <a name="workflow-automation"></a>Werkstroomautomatisering

Elk beveiligingsprogramma bevat meerdere workflows voor incidentrespons. Deze processen kunnen bestaan uit het informeren van relevante belanghebbenden, het starten van een change management proces en het toepassen van specifieke herstelstappen. Beveiligingsexperts raden u aan zoveel mogelijk stappen van deze procedures te automatiseren. Automatisering vermindert de overhead. Het kan ook uw beveiliging verbeteren door ervoor te zorgen dat de processtappen snel, consistent en volgens uw vooraf gedefinieerde vereisten worden uitgevoerd.

In dit artikel wordt de functie voor workflowautomatisering van Azure Security Center beschreven. Deze functie kan Logic Apps activeren op beveiligingswaarschuwingen en aanbevelingen. U wilt bijvoorbeeld dat Beveiligingscentrum een specifieke gebruiker e-mailt wanneer er een waarschuwing optreedt. U leert ook hoe u Logische apps maakt met [Azure Logic Apps.](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)

> [!NOTE]
> Als u eerder de weergave Playbooks (Preview) op de zijbalk hebt gebruikt, vindt u dezelfde functies samen met de uitgebreide functionaliteit op de nieuwe pagina met werkstroomautomatisering.


## <a name="requirements"></a>Vereisten

* Als u wilt werken met Azure Logic Apps-werkstromen, moet u de volgende rollen/machtigingen voor Logische Apps hebben:

    * [Logic App Operator-machtigingen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) zijn vereist of Logic App-lees-/triggertoegang (deze rol kan geen logische apps maken of bewerken; alleen bestaande apps *uitvoeren)*

    * [Machtigingen voor logic-app-inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) zijn vereist voor het maken en wijzigen van logische app-bijdragen

* Als u Logic App-connectors wilt gebruiken, hebt u mogelijk aanvullende referenties nodig om u aan te melden bij hun respectievelijke services (bijvoorbeeld uw Outlook/Teams/Slack-exemplaren)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Een logische app maken en definiëren wanneer deze automatisch moet worden uitgevoerd 

1. Selecteer **workflowautomatisering**in de zijbalk van Security Center.

    [![Lijst met werkstroomautomatiseringen](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Op deze pagina u nieuwe automatiseringsregels maken en bestaande regels in- en uitschakelen of verwijderen.  
1. Als u een nieuwe werkstroom wilt definiëren, klikt u op **Werkstroomautomatisering toevoegen**. 

    Er verschijnt een deelvenster met de opties voor uw nieuwe automatisering. Hier u deelnemen aan:
    1. Een naam en beschrijving voor de automatisering.
    1. De triggers die deze automatische workflow starten. U wilt bijvoorbeeld dat uw Logic App wordt uitgevoerd wanneer een beveiligingswaarschuwing met 'SQL' wordt gegenereerd.
    1. De Logic-app die wordt uitgevoerd wanneer aan de triggervoorwaarden is voldaan. 

        [![Lijst met werkstroomautomatiseringen](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. Klik in de sectie Acties op **Een nieuw** maken om het proces voor het maken van logische apps te starten.

    U wordt naar Azure Logic Apps gebracht.

    [![Een nieuwe Logische app maken](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Voer een naam, resourcegroep en locatie in en klik op **Maken**.

1. In uw nieuwe Logic App u kiezen uit ingebouwde, vooraf gedefinieerde sjablonen uit de beveiligingscategorie. U ook een aangepaste stroom van gebeurtenissen definiëren die moeten optreden wanneer dit proces wordt geactiveerd.

    In de logic-app-ontwerper worden de volgende triggers van de Connectors voor beveiligingscentrum ondersteund:

    * **Wanneer een Aanbeveling voor Azure Security Center wordt gemaakt of geactiveerd**
    * **Wanneer een Azure Security Center Alert wordt gemaakt of geactiveerd** 
    
    > [!TIP]
    > U de trigger zo aanpassen dat deze alleen betrekking heeft op waarschuwingen met de ernstniveaus die u interesseren.
    
    > [!NOTE]
    > Als u de verouderde trigger 'Wanneer een reactie op een Azure Security Center-waarschuwing wordt geactiveerd, gebruikt, worden uw Logic Apps niet gestart met de functie Workflowautomatisering. In plaats daarvan, gebruik maken van een van de hierboven genoemde triggers. 

    [![Voorbeeldlogica-app](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Nadat u uw Logic App hebt gedefinieerd, gaat u terug naar het definitievenster voor de definitie van werkstroomautomatisering ('Werkstroomautomatisering toevoegen'). Klik **op Vernieuwen** om ervoor te zorgen dat uw nieuwe Logic App beschikbaar is voor selectie.

    ![Vernieuwen](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Selecteer uw Logic App en sla de automatisering op. Houd er rekening mee dat in de vervolgkeuzelijst Logic App alleen Logic Apps met eerder genoemde ondersteuningsconnectors voor beveiligingscentrum worden weergegeven.


## <a name="manually-trigger-a-logic-app"></a>Handmatig een Logische App activeren

U Logic Apps ook handmatig uitvoeren wanneer u een beveiligingswaarschuwing bekijkt of een aanbeveling die [quick fix-herstel biedt.](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation)

Als u een Logische App handmatig wilt uitvoeren, opent u een waarschuwing of een aanbeveling die quick fix-herstel ondersteunt en klikt u op **Trigger Logic App:**

[![Handmatig een Logische App activeren](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Gegevenstypenschema's

Als u de schema's voor ruwe gebeurtenissen van de beveiligingswaarschuwingen of aanbevelingen wilt weergeven die naar de instantie Logic App zijn doorgegeven, gaat u naar de [schema's voor gegevenstypen voor gegevenstypen voor werkstroomautomatisering](https://aka.ms/ASCAutomationSchemas). Dit kan handig zijn in gevallen waarin u de hierboven genoemde Logic App-connectors van Security Center niet gebruikt, maar in plaats daarvan de generieke HTTP-connector van Logic App gebruikt - u het JSON-schema van de gebeurtenis gebruiken om het handmatig te ontleden zoals u dat wilt.

## <a name="next-steps"></a>Volgende stappen

In dit artikel leerde u over het maken van Logische Apps, het automatiseren van de uitvoering ervan in Security Center en het handmatig uitvoeren ervan. 

Zie voor ander gerelateerd materiaal: 

- [De Microsoft Learn-module over het gebruik van workflowautomatisering om een beveiligingsantwoord te automatiseren](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Aanbevelingen voor beveiliging in Azure Security Center](security-center-recommendations.md)
- [Beveiligingswaarschuwingen in Azure Security Center](security-center-alerts-overview.md)
- [Over Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps-connectors](https://docs.microsoft.com/connectors/)
- [Gegevenstypen voor werkstroomautomatisering](https://aka.ms/ASCAutomationSchemas)
