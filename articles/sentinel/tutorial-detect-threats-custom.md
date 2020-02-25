---
title: Aangepaste analytische regels maken voor het detecteren van verdachte dreigingen met Azure Sentinel | Microsoft Docs
description: Gebruik deze zelf studie voor meer informatie over het maken van aangepaste analyse regels voor het detecteren van verdachte dreigingen met Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: rkarlin
ms.openlocfilehash: bdd36e2f3c2b426f4bad3e787c12be2f7d09b303
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565516"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Zelf studie: aangepaste analyse regels maken om verdachte bedreigingen te detecteren

Nadat u [uw gegevens bronnen](quickstart-onboard.md) aan Azure Sentinel hebt gekoppeld, kunt u aangepaste regels maken waarmee kan worden gezocht naar specifieke criteria in uw omgeving en incidenten worden gegenereerd wanneer de criteria worden vergeleken, zodat u ze kunt onderzoeken. Deze zelf studie helpt u bij het maken van aangepaste regels voor het detecteren van bedreigingen met Azure Sentinel.

Deze zelf studie helpt u bij het detecteren van bedreigingen met Azure Sentinel.
> [!div class="checklist"]
> * Analytische regels maken
> * Bedreigings reacties automatiseren

## <a name="create-custom-analytic-rules"></a>Aangepaste analytische regels maken

U kunt aangepaste analyse regels maken om u te helpen bij het zoeken naar de typen bedreigingen en afwijkingen die in uw omgeving worden verdacht. De regel zorgt ervoor dat u meteen op de hoogte wordt gebracht, zodat u de bedreigingen kunt sorteren, onderzoeken en oplossen.

1. Selecteer in de Azure Portal onder Azure Sentinel **Analytics**.

1. Selecteer in de bovenste menu balk **+ maken** en selecteer **geplande query regel**. Hiermee opent u de **wizard Analytics-regel**.

    ![Geplande query maken](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Geef op het tabblad **Algemeen** een unieke **naam**en een **Beschrijving**op. In het veld **tactiek** kunt u kiezen uit verschillende categorieën aanvallen waarmee de regel wordt geclassificeerd. Stel de **Ernst** van de waarschuwing in als dat nodig is. Wanneer u de regel maakt, wordt de **status** standaard **ingeschakeld** , wat betekent dat deze onmiddellijk wordt uitgevoerd nadat u deze hebt gemaakt. Als u niet wilt dat deze onmiddellijk wordt uitgevoerd, selecteert u **uitgeschakeld**. de regel wordt toegevoegd aan het tabblad **actieve regels** en u kunt het gebruiken wanneer u het nodig hebt.

    ![Beginnen met het maken van een aangepaste analytische regel](media/tutorial-detect-threats-custom/general-tab.png)

1. Op het tabblad **regel logica instellen** kunt u een query rechtstreeks in het veld **regel query** schrijven, of de query in log Analytics maken, en deze vervolgens kopiëren en plakken.
 
   ![Query maken in de Azure-Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Bekijk de **Preview-versie** van de resultaten aan de rechter kant, waarbij Azure Sentinel het aantal resultaten (logboek gebeurtenissen) weergeeft dat door de query wordt gegenereerd, waardoor de vlucht wordt gewijzigd wanneer u uw query schrijft en configureert. De grafiek toont het aantal resultaten gedurende de gedefinieerde tijds periode, die wordt bepaald door de instellingen in de sectie **query planning** .
    - Als u ziet dat uw query te veel of te frequente waarschuwingen zou activeren, kunt u een basis lijn instellen in de sectie **waarschuwings drempel** .

      Hier volgt een voorbeeld query waarmee u wordt gewaarschuwd wanneer een afwijkend aantal resources wordt gemaakt in azure activity.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > De lengte van de query moet tussen 1 en 10.000 tekens lang zijn en mag geen ' Search \*' of ' Union \*' bevatten.

    1. Gebruik de sectie **entiteiten toewijzen** om de para meters van uw query resultaten te koppelen aan door Sentinel herkende entiteiten van Azure. Deze entiteiten vormen de basis voor verdere analyse, met inbegrip van het groeperen van waarschuwingen in incidenten op het tabblad **incident instellingen** .
    1. Stel in de sectie **query planning** de volgende para meters in:

       1. Stel **query uitvoeren** in om te bepalen hoe vaak de query wordt uitgevoerd, net zo vaak als elke 5 minuten of als een keer per dag.

       1. Stel **opzoek gegevens van de laatste** in om de tijds periode te bepalen van de gegevens die worden gedekt door de query. Dit kan bijvoorbeeld de afgelopen 10 minuten aan gegevens of de afgelopen 6 uur aan gegevens opvragen.

       > [!NOTE]
       > Deze twee instellingen zijn onafhankelijk van elkaar, tot een bepaald punt. U kunt een query uitvoeren met een kort interval voor een tijds periode die langer is dan het interval (als gevolg van overlappende query's), maar u kunt geen query uitvoeren met een interval dat de dekkings periode overschrijdt, anders worden er hiaten in de algehele query dekking.

    1. Gebruik de sectie **waarschuwings drempel** om een basis lijn te definiëren. Stel bijvoorbeeld een **waarschuwing genereren wanneer het aantal query resultaten** **groter is dan** en voer het getal 1000 in als u wilt dat de regel alleen een waarschuwing genereert als de query meer dan 1000 resultaten oplevert telkens wanneer deze wordt uitgevoerd. Als dit een verplicht veld is, kunt u, als u geen basis lijn wilt instellen, dat wil zeggen, als u wilt dat uw waarschuwing elke gebeurtenis registreert – Voer 0 in het veld getal in.

    1. In het gedeelte **onderdrukking** kunt u de instelling voor het **uitvoeren van de query stoppen nadat de waarschuwing is gegenereerd** inschakelen **op** als u een waarschuwing hebt ontvangen, wilt u de bewerking van deze regel voor een bepaalde periode voor het overschrijden van het query-interval onderbreken. Als u dit inschakelt, moet u de uitvoering van de **query stoppen** instellen op de tijd die nodig is voor het stoppen van de query, tot 24 uur.

1. Op het tabblad **instellingen voor incidenten** kunt u kiezen of en hoe Azure Sentinel waarschuwingen in incidenten kan omzetten. Als dit tabblad alleen wordt weer gegeven, maakt Azure Sentinel een afzonderlijke, afzonderlijk incident van elke waarschuwing. U kunt ervoor kiezen om geen incidenten te maken of om verschillende waarschuwingen in één incident te groeperen, door de instellingen op dit tabblad te wijzigen.

    1. In de sectie **incident instellingen** worden **incidenten gemaakt op basis van waarschuwingen die door deze analyse regel worden geactiveerd** , standaard ingesteld op **ingeschakeld**, wat betekent dat Azure Sentinel één afzonderlijk incident maakt van elke waarschuwing die wordt geactiveerd door de regel.<br></br>Als u niet wilt dat deze regel resulteert in het maken van incidenten (als deze regel bijvoorbeeld alleen gegevens voor de volgende analyse verzamelt), stelt u deze in op **uitgeschakeld**.

    1. Als u een enkel incident wilt genereren op basis van een groep vergelijk bare of terugkerende waarschuwingen, stelt u in de sectie **waarschuwings groepering** de **groep gerelateerde waarschuwingen in, die door deze analyse regel worden geactiveerd, in incidenten** op **ingeschakeld**en stelt u de volgende para meters in.

    1. **De groep beperken tot waarschuwingen die zijn gemaakt in het geselecteerde tijds bestek**:<br></br> Bepaal het tijds bestek waarbinnen de vergelijk bare of terugkerende waarschuwingen samen worden gegroepeerd. Alle bijbehorende waarschuwingen binnen deze tijds periode genereren gezamenlijk een incident of een reeks incidenten (afhankelijk van de groeperings instellingen hieronder). Waarschuwingen buiten deze tijds periode genereren een afzonderlijk incident of een reeks incidenten.

    2. **Groeps waarschuwingen die door deze analyse regel worden geactiveerd in één incident per**: Kies de basis waarop waarschuwingen worden gegroepeerd:

        - **Groepeer waarschuwingen in één incident als alle entiteiten overeenkomen**: <br></br>Waarschuwingen worden gegroepeerd als er identieke waarden worden gedeeld voor elk van de toegewezen entiteiten (gedefinieerd op het tabblad set-regel logica hierboven). Dit is de aanbevolen instelling.

        - **Alle waarschuwingen die door deze regel worden geactiveerd, groeperen in één incident**: <br></br>Alle waarschuwingen die door deze regel worden gegenereerd, worden gegroepeerd, zelfs als ze geen identieke waarden delen.

        - **Groepeer waarschuwingen in één incident als de geselecteerde entiteiten overeenkomen**: <br></br>Waarschuwingen worden gegroepeerd als er identieke waarden worden gedeeld voor een aantal toegewezen entiteiten (die u kunt selecteren in de vervolg keuzelijst). U kunt deze instelling gebruiken als u bijvoorbeeld afzonderlijke incidenten wilt maken op basis van de bron-of doel-IP-adressen.

    3. **Gesloten afsluitende incidenten opnieuw openen**: als er een incident is gesloten (wat betekent dat het onderliggende probleem is opgelost), en vervolgens een andere waarschuwing wordt gegenereerd die in dat incident is gegroepeerd, stelt u deze instelling in op **ingeschakeld** als u wilt dat het gesloten incident opnieuw wordt geopend en moet u deze optie **uitschakelen** als u wilt dat de waarschuwing een nieuw incident maakt.

1. Op het tabblad **geautomatiseerde antwoorden** selecteert u de playbooks die u automatisch wilt uitvoeren wanneer een waarschuwing wordt gegenereerd door de aangepaste regel. Zie [reageren op bedreigingen](tutorial-respond-threats-playbook.md)voor meer informatie over het maken en automatiseren van playbooks.

1. Selecteer **controleren en maken** om alle instellingen voor de nieuwe waarschuwings regel te controleren en selecteer vervolgens **maken om uw waarschuwings regel te initialiseren**.
  
1. Nadat de waarschuwing is gemaakt, wordt er een aangepaste regel toegevoegd aan de tabel onder **actieve regels**. In deze lijst kunt u elke regel inschakelen, uitschakelen of verwijderen.

1. Als u de resultaten van de waarschuwings regels die u maakt, wilt weer geven, gaat u naar de pagina **incidenten** , waar u de [incidenten](tutorial-investigate-cases.md)kunt sorteren, onderzoeken en de bedreigingen herstelt.


> [!NOTE]
> Waarschuwingen die zijn gegenereerd in azure Sentinel zijn beschikbaar via [Microsoft Graph beveiliging](https://aka.ms/securitygraphdocs). Zie de [documentatie over Microsoft Graph Security Alerts](https://aka.ms/graphsecurityreferencebetadocs)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u aan de slag gaat met het detecteren van bedreigingen met behulp van Azure Sentinel.

Als u wilt weten hoe u uw reacties op bedreigingen kunt automatiseren, [stelt u automatische reacties op dreigingen in azure Sentinel in](tutorial-respond-threats-playbook.md).

