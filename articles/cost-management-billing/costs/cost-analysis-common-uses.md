---
title: Bij het analyseren van algemene kosten wordt gebruikgemaakt van Azure Cost Management
description: In dit artikel wordt uitgelegd hoe u resultaten kunt ophalen voor algemene kosten analyse taken in Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: ''
ms.openlocfilehash: 756e2f275a92a31a99604aaf7ef880bfdce8149b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988511"
---
# <a name="common-cost-analysis-uses"></a>Gemeen schappelijke kosten analyse gebruikt

Azure Cost Management gebruikers willen vaak antwoorden op vragen die veel door anderen worden gevraagd. Dit artikel begeleidt u bij het ophalen van resultaten voor algemene kosten analyse taken in Cost Management.

## <a name="view-cost-breakdown-by-azure-service"></a>Kosten analyse weer geven per Azure-service

Door de kosten van een Azure-service te bekijken, kunt u beter inzicht krijgen in de onderdelen van uw infra structuur die het meest rendabel zijn. De reken kosten voor virtuele machines kunnen bijvoorbeeld klein zijn. Maar u kunt aanzienlijke netwerk kosten in beslag nemen vanwege de hoeveelheid gegevens die afkomstig zijn van de Vm's. Meer informatie over de primaire kosten Stuur Programma's van uw Azure-Services is essentieel, zodat u het service gebruik, indien nodig, kunt aanpassen.

1. Ga in het Azure Portal naar Cost Analysis voor uw bereik. Bijvoorbeeld: **Cost Management + facturerings** > **Cost Management** > **kosten analyse**.
1. Selecteer **kosten per service** en vervolgens groeperen op **servicelaag**.
1. Wijzig de weer gave in **tabel**.

![Kosten analyse door Azure service](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Kosten analyse weer geven per Azure-resource

Uw services zijn gebouwd met Azure-resources. Door kosten op basis van resources te bekijken, kunt u uw primaire kosten voor inzenders snel identificeren. Als een service resources heeft die te duur zijn, kunt u overwegen om wijzigingen aan te brengen om uw kosten te verlagen.

1. Ga in het Azure Portal naar Cost Analysis voor uw bereik. Bijvoorbeeld: **Cost Management + facturerings** > **Cost Management** > **kosten analyse**.
1. Selecteer **kosten per resource**.
1. Wijzig de weer gave in **tabel**.

![Kosten analyse weer geven per Azure-resource](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Kosten analyse weer geven op geselecteerde dimensies

Met dimensies kunt u uw kosten indelen op basis van verschillende waarden voor meta gegevens die worden weer gegeven in uw kosten. U kunt bijvoorbeeld uw kosten groeperen op locatie.

1. Ga in het Azure Portal naar Cost Analysis voor uw bereik. Bijvoorbeeld: **Cost Management + facturerings** > **Cost Management** > **kosten analyse**.
1. Selecteer de **groeperen op** filter.  
    ![een groep selecteren op item](./media/cost-analysis-common-uses/group-by.png)
1. U kunt de weer gave eventueel opslaan voor later gebruik.
1. Klik op een cirkel diagram onder het diagram om gedetailleerde gegevens weer te geven.  
    ![Kosten analyse weer geven op geselecteerde dimensies](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Kosten per dag of per maand weer geven

De dagelijkse en maandelijkse kosten kunnen u helpen beter te begrijpen als er een tijd is voor de week of het jaar waarin uw kosten hoger zijn. Als u in een periode van een feestdag meer klant verkeer hebt, leidt dat tot een toename van de kosten van uw Azure-klanten. Is vrijdag een kost bare dag van meer dan maandag?

1. Ga in het Azure Portal naar Cost Analysis voor uw bereik. Bijvoorbeeld: **Cost Management + facturerings** > **Cost Management** > **kosten analyse**.
1. Stel de **granulatie** in op **maandelijks** of **dagelijks**.

![Kosten per dag weer geven](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Kosten voor een specifieke tag weer geven

Veel Azure-gebruikers passen tags toe op hun resources, zoals een kosten centrum of een ontwikkel omgeving (productie en test), om de kosten beter te categoriseren. Tags worden weer gegeven als een dimensie in de kosten analyse. U kunt de dimensie gebruiken om inzicht te krijgen in de categorisaties van uw aangepaste labels.

Ondersteuning voor Tags is van toepassing op het gebruik dat wordt gerapporteerd *nadat* het label is toegepast op de resource. Labels worden niet met terugwerkende kracht toegepast voor kosten tellingen.

1. Ga in het Azure Portal naar Cost Analysis voor uw bereik. Bijvoorbeeld: **Cost Management + facturerings** > **Cost Management** > **kosten analyse**.
1. Selecteer **groeperen op** voor uw tag.

![Kosten voor een specifieke tag weer geven](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>De gebruiks gegevens downloaden

Het rapport bestand met gebruiks gegevens in CSV-indeling bevat een overzicht van alle kosten die in rekening worden gebracht voor een factuur. U kunt het rapport gebruiken om het te vergelijken met en beter inzicht te krijgen in uw factuur. Elke gefactureerde kosten in uw factuur komen overeen met de onderverdeling van de kosten in het gebruiks rapport.

1. Navigeer in het Azure Portal naar het tabblad **gebruik en kosten** voor een facturerings account of-abonnement. Bijvoorbeeld: **Cost Management + facturering** > **factuur** > **gebruik + kosten**.
1. Selecteer het regel item dat u wilt downloaden en klik vervolgens op het Download symbool.  
    ![Gebruik en kosten downloaden](./media/cost-analysis-common-uses/download1.png)
1.  Selecteer het gebruiks bestand dat u wilt downloaden.  
    ![Kies een gebruiks bestand dat u wilt downloaden](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Maandelijkse EA-kosten analyse weer geven

Uw EA-inschrijving overschrijdt de kosten voor uw hele organisatie. Als u begrijpt hoe kosten toenemen en worden gefactureerd in de loop van de tijd, kunt u de juiste belanghebbenden helpen om ervoor te zorgen dat de kosten op een verantwoorde manier worden beheerd.

1. Ga in het Azure Portal naar **Cost Management + facturering** > **overzicht**.
1. Klik op **uitsplitsing** van de huidige maand en Bekijk uw monetaire toezeg ging.  
    overzicht van ![EA-kosten-overzicht van uitsplitsing](./media/cost-analysis-common-uses/breakdown1.png)
1.  Klik op het tabblad **gebruik en toeslagen** en Bekijk de uitsplitsing van de vorige maand in de gekozen tijds periode.  
    tabblad ![gebruik en toeslagen](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Maandelijkse kosten voor inschrijving per periode weer geven

Gebruik een grafische weer gave van de maandelijkse kosten van uw inschrijving om inzicht te krijgen in de kosten trends en gefactureerde bedragen voor een bepaalde periode.

1. Ga in het Azure Portal naar Cost Analysis voor uw bereik. Bijvoorbeeld: **Cost Management + facturerings** > **Cost Management** > **kosten analyse**.
1. Selecteer uw inschrijving en stel de inschrijvings periode in.
1. Stel de granulatie in op maandelijks en stel de weer gave in op **kolom (gestapeld)** .

U kunt groeperen op en filteren van uw gegevens voor een meer gedetailleerde analyse.

![Maandelijkse inschrijvings kosten per termijn](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Geaccumuleerde kosten voor EA-inschrijving weer geven

Bekijk de netto samengevoegde kosten gedurende een bepaalde periode om inzicht te krijgen in de algemene uitgaven voor uw organisatie.

1. Ga in het Azure Portal naar Cost Analysis voor uw bereik. Bijvoorbeeld: **Cost Management + facturerings** > **Cost Management** > **kosten analyse**.
1. Selecteer uw inschrijving en Bekijk de huidige geaccumuleerde kosten.

![Geaccumuleerde kosten voor inschrijving](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Volgende stappen
- Als u dit nog niet hebt al de eerste snelstartgids voltooid voor kostenbeheer, lezen via [kosten analyseren](quick-acm-cost-analysis.md).
- Lees de [Cost Management documentatie](../index.yml).
