---
title: Aangepaste analytische regels maken om verdachte bedreigingen te detecteren met Azure Sentinel| Microsoft Documenten
description: Gebruik deze zelfstudie voor meer informatie over het maken van aangepaste analytische regels om verdachte bedreigingen met Azure Sentinel te detecteren.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: cea7429ecea105355b0afe306bfa334e55d5d9c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585104"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Zelfstudie: Aangepaste analytische regels maken om verdachte bedreigingen te detecteren

Nadat u [uw gegevensbronnen](quickstart-onboard.md) hebt gekoppeld aan Azure Sentinel, u aangepaste regels maken die kunnen zoeken naar specifieke criteria in uw omgeving en incidenten genereren wanneer de criteria worden geëvenaard, zodat u ze onderzoeken. Met deze zelfstudie u aangepaste regels maken om bedreigingen te detecteren met Azure Sentinel.

Met deze zelfstudie u bedreigingen detecteren met Azure Sentinel.
> [!div class="checklist"]
> * Analytische regels maken
> * Dreigingsreacties automatiseren

## <a name="create-custom-analytic-rules"></a>Aangepaste analytische regels maken

U aangepaste analytische regels maken om u te helpen zoeken naar de soorten bedreigingen en afwijkingen die verdacht zijn in uw omgeving. De regel zorgt ervoor dat u meteen op de hoogte wordt gesteld, zodat u de bedreigingen triage, onderzoeken en herstellen.

1. Selecteer **Analytics**in de Azure-portal onder Azure Sentinel .

1. Selecteer in de bovenste menubalk **+Maken** en selecteer **Geplande queryregel**. Hiermee wordt de **wizard Analytics-regel geopend**.

    ![Geplande query maken](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Geef op het tabblad **Algemeen** een unieke **naam**en een **beschrijving op**. In het veld **Tactiek** u kiezen uit categorieën aanvallen waarmee u de regel wilt classificeren. Stel de waarschuwing **ernst** in indien nodig. Wanneer u de regel maakt, is de **status** standaard **ingeschakeld,** wat betekent dat deze onmiddellijk wordt uitgevoerd nadat u de regel hebt gemaakt. Als u niet wilt dat deze onmiddellijk wordt uitgevoerd, selecteert u **Uitgeschakeld**en wordt de regel toegevoegd aan het tabblad **Actieve regels** en u deze vanaf daar inschakelen wanneer u het nodig hebt.

    ![Een aangepaste analytische regel maken](media/tutorial-detect-threats-custom/general-tab.png)

1. Op het tabblad **Regellogica instellen** u een query rechtstreeks schrijven in het **queryveld Regel** of de query maken in Logboekanalyse en deze vervolgens kopiëren en plakken.
 
   ![Query maken in Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Zie het **voorbeeldgebied Resultaten** aan de rechterkant, waar Azure Sentinel het aantal resultaten (logboekgebeurtenissen) toont dat de query genereert, waarbij u uw query on-the-fly wijzigt terwijl u uw query schrijft en configureert. De grafiek toont het aantal resultaten over de gedefinieerde periode, dat wordt bepaald door de instellingen in de sectie **Queryplanning.**
    - Als u ziet dat uw query te veel of te frequente waarschuwingen zou activeren, u een basislijn instellen in de sectie **Waarschuwingsdrempel.**

      Hier is een voorbeeldquery die u waarschuwt wanneer een afwijkend aantal resources wordt gemaakt in Azure Activity.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > De querylengte moet tussen de 1 en 10.000 tekens liggen en kan geen 'zoeken' \*of 'unie' \*bevatten.

    1. Gebruik de sectie **Entiteiten van kaart** om parameters uit uw queryresultaten te koppelen aan door Azure Sentinel erkende entiteiten. Deze entiteiten vormen de basis voor verdere analyse, waaronder het groeperen van waarschuwingen op incidenten op het tabblad **Incident-instellingen.**
    1. Stel in de sectie **Queryplanning** de volgende parameters in:

       1. Stel **Query's uitvoeren in** om te bepalen hoe vaak de query wordt uitgevoerd - zo vaak als elke 5 minuten of zo zelden als eenmaal per dag.

       1. **Stel opzoekgegevens van de laatste** in om de periode van de gegevens waarop de query betrekking heeft te bepalen- bijvoorbeeld de afgelopen 10 minuten aan gegevens of de afgelopen 6 uur aan gegevens.

       > [!NOTE]
       > Deze twee instellingen zijn onafhankelijk van elkaar, tot op zekere hoogte. U een query met een korte interval uitvoeren voor een langere periode dan het interval (in feite met overlappende query's), maar u een query niet uitvoeren met een interval dat de dekkingsperiode overschrijdt, anders hebt u hiaten in de totale querydekking.

    1. Gebruik de sectie **Waarschuwingsdrempel** om een basislijn te definiëren. Stel bijvoorbeeld **Waarschuwing genereren in wanneer het aantal queryresultaten** **groter is dan** en voer het getal 1000 in als u wilt dat de regel alleen een waarschuwing genereert als de query elke keer dat deze wordt uitgevoerd meer dan 1000 resultaten genereert. Aangezien dit een vereist veld is, voert u als u geen basislijn wilt instellen , dat wil zeggen, als u wilt dat uw waarschuwing elke gebeurtenis registreert, 0 in het getalveld in.

    1. In de sectie **Onderdrukking** u de **query Stoppen nadat de waarschuwing is gegenereerd,** **inschakelen** als u, zodra u een waarschuwing hebt ontvangen, de bewerking van deze regel wilt opschorten voor een periode die het queryinterval overschrijdt. Als u dit inschakelt, moet u **Query stoppen** instellen op de hoeveelheid tijd die de query moet stoppen met draaien, tot 24 uur.

1. Op het tabblad **Incidentinstellingen** u kiezen of en hoe Azure Sentinel waarschuwingen omzet in bruikbare incidenten. Als dit tabblad met rust wordt gelaten, maakt Azure Sentinel één afzonderlijk incident van elke waarschuwing. U ervoor kiezen om geen incidenten te laten maken of meerdere waarschuwingen te groeperen in één incident door de instellingen op dit tabblad te wijzigen.

    1. In de sectie **Incidentinstellingen** wordt **Incidenten maken van waarschuwingen die door deze analyseregel worden geactiveerd,** standaard ingesteld op **Ingeschakeld,** wat betekent dat Azure Sentinel één afzonderlijk incident maakt van elke waarschuwing die door de regel wordt geactiveerd.<br></br>Als u niet wilt dat deze regel leidt tot het maken van incidenten (bijvoorbeeld als deze regel alleen is om informatie te verzamelen voor volgende analyse), stelt u deze in op **Uitgeschakeld**.

    1. Als u in de sectie **Waarschuwinggroepering** wilt dat één incident wordt gegenereerd uit een groep vergelijkbare of terugkerende waarschuwingen, stelt u **Groepsgerelateerde waarschuwingen, geactiveerd door deze analyseregel, in op incidenten op** **Ingeschakeld**en stelt u de volgende parameters in.

    1. **Beperk de groep tot waarschuwingen die binnen het geselecteerde tijdsbestek zijn gemaakt:**<br></br> Bepaal het tijdsbestek waarbinnen de vergelijkbare of terugkerende waarschuwingen worden gegroepeerd. Alle bijbehorende waarschuwingen binnen deze periode genereren gezamenlijk een incident of een reeks incidenten (afhankelijk van de onderstaande groeperingsinstellingen). Waarschuwingen buiten dit tijdsbestek genereren een afzonderlijk incident of een reeks incidenten.

    2. **Groepswaarschuwingen die door deze analyseregel worden geactiveerd, worden omgezet in één incident door**: Kies de basis waarop waarschuwingen worden gegroepeerd:

        - **Groepswaarschuwingen tot één incident als alle entiteiten overeenkomen met:** <br></br>Waarschuwingen worden gegroepeerd als ze identieke waarden delen voor elk van de toegewezen entiteiten (gedefinieerd in het tabblad Regellogica instellen hierboven). Dit is de aanbevolen instelling.

        - **Groepeer alle waarschuwingen die door deze regel worden geactiveerd in één incident:** <br></br>Alle waarschuwingen die door deze regel worden gegenereerd, worden gegroepeerd, zelfs als ze geen identieke waarden delen.

        - **Groepeer waarschuwingen tot één incident als de geselecteerde entiteiten overeenkomen met:** <br></br>Waarschuwingen worden gegroepeerd als ze identieke waarden delen voor sommige toegewezen entiteiten (die u selecteren in de vervolgkeuzelijst). U deze instelling gebruiken als u bijvoorbeeld afzonderlijke incidenten wilt maken op basis van de bron- of doelIP-adressen.

    3. **Open gesloten matchingsincidenten**opnieuw: Als een incident is gesloten (wat betekent dat het onderliggende probleem is opgelost) en vervolgens een andere waarschuwing wordt gegenereerd die zou zijn gegroepeerd in dat incident, stelt u deze instelling in op **Ingeschakeld** als u wilt dat het gesloten incident opnieuw wordt geopend en vertrekt u als **uitgeschakeld** als u de waarschuwing wilt om een nieuw incident te maken.

1. Selecteer op het tabblad **Geautomatiseerde reacties** alle playbooks die u automatisch wilt uitvoeren wanneer een waarschuwing wordt gegenereerd door de aangepaste regel. Zie Reageren op [bedreigingen](tutorial-respond-threats-playbook.md)voor meer informatie over het maken en automatiseren van playbooks.

1. Selecteer **Controleren en maken** om alle instellingen voor uw nieuwe waarschuwingsregel te controleren en selecteer Vervolgens Maken om de **waarschuwingsregel te initialiseren.**
  
1. Nadat de waarschuwing is gemaakt, wordt een aangepaste regel toegevoegd aan de tabel onder **Actieve regels**. Vanuit deze lijst u elke regel in- en uitschakelen of verwijderen.

1. Als u de resultaten wilt bekijken van de waarschuwingsregels die u maakt, gaat u naar de pagina **Incidenten,** waar u incidenten triage, [incidenten onderzoeken](tutorial-investigate-cases.md)en de bedreigingen verwerkeren.


> [!NOTE]
> Waarschuwingen die in Azure Sentinel worden gegenereerd, zijn beschikbaar via [Microsoft Graph Security.](https://aka.ms/securitygraphdocs) Zie de documentatie [voor beveiligingswaarschuwingen van Microsoft Graph.](https://aka.ms/graphsecurityreferencebetadocs)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u aan de slag met het detecteren van bedreigingen met Azure Sentinel.

Als u wilt weten hoe u uw reacties op bedreigingen automatiseren, [stelt u geautomatiseerde bedreigingsreacties in Azure Sentinel](tutorial-respond-threats-playbook.md)in.

