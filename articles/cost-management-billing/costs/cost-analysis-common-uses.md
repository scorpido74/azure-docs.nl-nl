---
title: Veelgebruikte toepassingen van kostenanalyse in Azure Cost Management
description: In dit artikel wordt uitgelegd hoe u veelvoorkomende analysetaken kunt uitvoeren in Azure Cost Management.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/21/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 1d9a498185e76f9771ec98656b1cad914b0f3214
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562578"
---
# <a name="common-cost-analysis-uses"></a>Veelgebruikte toepassingen van kostenanalyse

Gebruikers van Azure Cost Management willen vaak antwoorden op vragen die veel andere gebruikers ook hebben gesteld. Dit artikel begeleidt u bij het uitvoeren van veelvoorkomende analysetaken in Cost Management.

## <a name="view-cost-breakdown-by-azure-service"></a>Uitsplitsing van kosten weergeven per Azure-service

Door de kosten per Azure-service te bekijken, kunt u een beter inzicht krijgen in de onderdelen van uw infrastructuur die de meeste kosten met zich meebrengen. De compute-kosten voor VM's kunnen bijvoorbeeld laag zijn. Desondanks kunnen de netwerkkosten aanzienlijk zijn vanwege de hoeveelheid gegevens die afkomstig zijn van de VM's. Een duidelijk beeld van de primaire kostenfactoren van uw Azure-Services is essentieel om waar nodig het servicegebruik te kunnen aanpassen.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Selecteer **Kosten per service** en groepeer vervolgens op **Servicelaag**.
1. Wijzig de weergave in **Tabel**.

![Uitsplitsing van kosten per Azure-service](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Uitsplitsing van kosten weergeven per Azure-resource

Uw services zijn gebouwd met Azure-resources. Door kosten op basis van resources te bekijken, kunt u snel de primaire kostenfactoren identificeren. Als een service resources heeft die te duur zijn, kunt u overwegen om wijzigingen aan te brengen om uw kosten te verlagen.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Selecteer **Kosten per resource**
1. Wijzig de weergave in **Tabel**.

![Uitsplitsing van kosten weergeven per Azure-resource](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Uitsplitsing van kosten weergeven op geselecteerde dimensies

Met dimensies kunt u uw kosten indelen op basis van verschillende waarden voor metagegevens die worden weergegeven in uw kosten. U kunt uw kosten bijvoorbeeld groeperen op locatie.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Selecteer het filter **Groeperen op**.  
    ![Selecteer een optie voor Groeperen op](./media/cost-analysis-common-uses/group-by.png)
1. U kunt de weergave eventueel opslaan voor later gebruik.
1. Klik op een cirkeldiagram onder het diagram om meer gedetailleerde gegevens weer te geven.  
    ![Uitsplitsing van kosten weergeven op geselecteerde dimensies](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Kosten per dag of per maand weergeven

Een goed overzicht van de dagelijkse en maandelijkse kosten kan helpen om vast te stellen of er een tijd van de week of het jaar is waarin uw kosten hoger zijn. Als u in een vakantieperiode meer klantverkeer hebt, leidt dat dan tot een evenredige toename van de Azure-kosten? Is vrijdag een dag met meer kosten dan maandag?

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Stel **Granulariteit** in op **Maandelijks** of **Dagelijks**.

![Kosten per dag weergeven](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Kosten voor een specifieke tag weergeven

Veel Azure-gebruikers passen tags toe op hun resources, zoals een kostenplaats of een ontwikkelomgeving (productie en test), om de kosten beter te categoriseren. Tags worden als een dimensie weergegeven in Kostenanalyse. U kunt de dimensie gebruiken om inzicht te krijgen in uw aangepaste categorisaties met behulp van tags.

Ondersteuning voor labels is van toepassing op het gebruik dat is gerapporteerd *nadat* de tag is toegepast op de resource. Tags worden niet met terugwerkende kracht toegepast voor samengetelde kosten.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Selecteer **Groeperen op** voor de tag.

![Kosten voor een specifieke tag weergeven](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Uw gebruiksgegevens downloaden

Het rapportbestand met gebruiksgegevens, in CSV-indeling, bevat een overzicht van alle kosten die in rekening worden gebracht op een factuur. U kunt het rapport gebruiken om uw gegevens te vergelijken uw factuur om zo een beter inzicht te krijgen. Alle gefactureerde kosten op uw factuur komen overeen met de onderverdeling van kosten in het gebruiksrapport.

1. Ga in de Azure-portal naar het tabblad **Gebruik en kosten** voor een factureringsaccount of abonnement. Bijvoorbeeld: **Kostenbeheer en facturering** > **Facturering** > **Gebruik + kosten**.
1. Selecteer het regelitem dat u wilt downloaden en klik vervolgens op het downloadsymbool.  
    ![Gebruik en kosten downloaden](./media/cost-analysis-common-uses/download1.png)
1.  Selecteer het gebruiksbestand dat u wilt downloaden.  
    ![Kies een gebruiksbestand om te downloaden](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Uitsplitsing van maandelijkse EA-kosten weergeven

Uw EA-inschrijving gaat gepaard met kosten voor de hele organisatie. Als u begrijpt hoe kosten worden gemaakt en gefactureerd in de loop van de tijd, kunt u de betrokken belanghebbenden ervan overtuigen dat ze de kosten op een verantwoorde manier moet beheren.

Er worden alleen kosten weergegeven voor uw actieve inschrijving. Als u een inschrijving (inactief) hebt overgebracht naar een nieuwe inschrijving (actief), worden de kosten voor de eerdere inschrijving niet weergegeven in Cost Management.


1. Ga in de Azure-portal naar **Kostenbeheer en facturering** > **Overzicht**.
1. Klik op **Uitsplitsing** voor de huidige maand en kijk waaruit de kosten zijn opgebouwd.  
    ![Overzicht EA-kosten overzicht van uitsplitsing](./media/cost-analysis-common-uses/breakdown1.png)
1.  Klik op het tabblad **Gebruik en kosten** en bekijk de uitsplitsing van de vorige maand in de gekozen tijdsperiode.  
    ![Het tabblad Gebruik en kosten](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Maandelijkse inschrijvingskosten weergeven per periode

Gebruik een grafische weergave van de maandelijkse kosten van uw inschrijving om inzicht te krijgen in de kostentrends en gefactureerde bedragen voor een bepaalde periode.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Selecteer uw inschrijving en stel de inschrijvingsperiode in.
1. Stel de granulariteit in op maandelijks en stel de weergave in op **Kolom (gestapeld)** .

U kunt gegevens groeperen en filteren voor een meer gedetailleerde analyse.

![Maandelijkse inschrijvingskosten per periode](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Samengevoegde kosten voor EA-inschrijving weergeven

Bekijk de netto samengevoegde kosten om inzicht te krijgen in de algemene uitgaven voor uw organisatie gedurende een bepaalde periode.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Selecteer uw inschrijving en bekijk de huidige samengevoegde kosten.

![Samengevoegde kosten voor inschrijving](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Volgende stappen
- Als u de eerste quickstart voor Cost Management nog niet hebt voltooid, leest u die op [Kosten analyseren](quick-acm-cost-analysis.md).
- Lees de [documentatie van Cost Management](../index.yml).
