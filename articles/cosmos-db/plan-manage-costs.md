---
title: Kosten voor Azure Cosmos DB plannen en beheren
description: Meer informatie over het plannen en beheren van kosten voor Azure Cosmos DB met behulp van kostenanalyse in Azure portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152581"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Kosten voor Azure Cosmos DB plannen en beheren

In dit artikel wordt beschreven hoe u de kosten voor Azure Cosmos DB plannen en beheren. Eerst gebruikt u de Azure Cosmos DB-capaciteitscalculator om de kosten te helpen plannen voordat u resources toevoegt. Als u vervolgens de Azure-resources toevoegt, u de geschatte kosten bekijken. Nadat u Azure Cosmos DB-resources hebt gebruikt, gebruikt u de functies voor kostenbeheer om budgetten in te stellen en de kosten te controleren. U ook de voorspelde kosten bekijken en uitgaventrends identificeren om gebieden te identificeren waar u mogelijk wilt handelen.

Begrijp dat de kosten voor Azure Cosmos DB slechts een deel van de maandelijkse kosten in uw Azure-factuur zijn. Als u andere Azure-services gebruikt, worden er kosten in rekening gebracht voor alle Azure-services en -resources die in uw Azure-abonnement worden gebruikt, inclusief de services van derden. In dit artikel wordt uitgelegd hoe u de kosten voor Azure Cosmos DB plannen en beheren. Nadat u bekend bent met het beheren van de kosten voor Azure Cosmos DB, u vergelijkbare methoden toepassen om de kosten te beheren voor alle Azure-services die in uw abonnement worden gebruikt.

## <a name="prerequisites"></a>Vereisten

Kostenanalyse biedt ondersteuning voor verschillende typen Azure-accounts. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](../cost-management-billing/costs/understand-cost-mgt-data.md). Als u kostengegevens wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig. Zie [Toegang tot gegevens toewijzen](../cost-management-billing/costs/assign-access-acm-data.md) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

## <a name="review-estimated-costs-with-capacity-calculator"></a>Geschatte kosten bekijken met capaciteitscalculator

Gebruik de [azure cosmos DB-capaciteitscalculator](https://cosmos.azure.com/capacitycalculator/) om de kosten te schatten voordat u de resources in een Azure Cosmos-account maakt. De capaciteitscalculator wordt gebruikt om een schatting te krijgen van de vereiste doorvoer en kosten van uw werkbelasting. Het configureren van uw Azure Cosmos-databases en -containers met de juiste hoeveelheid ingerichte doorvoer of [aanvraageenheden (RU/s)](request-units.md)voor uw werkbelasting is essentieel om de kosten en prestaties te optimaliseren. U moet gegevens invoeren, zoals API-type, aantal regio's, artikelgrootte, lees-/schrijfaanvragen per seconde, totale gegevens die zijn opgeslagen om een kostenraming te krijgen. Zie het [schattingsartikel voor](estimate-ru-with-capacity-planner.md) meer informatie over de capaciteitscalculator.

In de volgende schermafbeelding wordt de doorvoer- en kostenschatting weergegeven met behulp van de capaciteitscalculator:

![Kostenraming in Azure Cosmos DB-capaciteitscalculator](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>Geschatte kosten van de Azure-portal bekijken

Terwijl u Azure Cosmos DB-resources maakt vanuit azure-portal, u de geschatte kosten zien. Gebruik de volgende stappen om de kostenraming te bekijken:

1. Meld u aan bij de Azure-portal en navigeer naar uw Azure Cosmos-account.
1. Ga naar de **Data Explorer**.
1. Maak een nieuwe container, zoals een grafiekcontainer.
1. Voer de doorvoer in die nodig is voor uw werkbelasting, zoals 400 RU/s. Nadat u de doorvoerwaarde hebt ingevoerd, u de prijsschatting zien zoals weergegeven in de volgende schermafbeelding:

   ![Kostenraming in Azure-portal](./media/plan-manage-costs/cost-estimate-portal.png)

Als uw Azure-abonnement een bestedingslimiet heeft, voorkomt Azure dat u meer dan uw kredietbedrag uitgeeft. Terwijl u Azure-resources maakt en gebruikt, worden uw credits gebruikt. Wanneer u uw kredietlimiet bereikt, worden de resources die u hebt geïmplementeerd, uitgeschakeld voor de rest van die factureringsperiode. U uw kredietlimiet niet wijzigen, maar u deze wel verwijderen. Zie [Azure-bestedingslimiet](../billing/billing-spending-limit.md)voor meer informatie over bestedingslimieten.

## <a name="use-budgets-and-cost-alerts"></a>Budgetten en kostenwaarschuwingen gebruiken

U kunt [budgetten](../cost-management/tutorial-acm-create-budgets.md) maken om kosten te beheren en waarschuwingen te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en resourcegroepen, zodat ze nuttig zijn als onderdeel van een algemene strategie voor kostenbewaking. Het is echter mogelijk dat ze beperkte functionaliteit hebben om afzonderlijke Azure-servicekosten te beheren, zoals de kosten van Azure Cosmos DB, omdat ze zijn ontworpen om de kosten op een hoger niveau bij te houden.

## <a name="monitor-costs"></a>Kosten controleren

Terwijl u resources gebruikt met Azure Cosmos DB, brengt u kosten met zich mee. Kosten voor resourcegebruikseenheid variëren afhankelijk van tijdsintervallen (seconden, minuten, uren en dagen) of per aanvraag eenheidsgebruik. Zodra het gebruik van Azure Cosmos DB begint, worden kosten gemaakt en u ze zien in het [deelvenster kostenanalyse](../cost-management/quick-acm-cost-analysis.md) in de Azure-portal.

Wanneer u kostenanalyse gebruikt, u de kosten van Azure Cosmos DB in grafieken en tabellen bekijken voor verschillende tijdsintervallen. Enkele voorbeelden zijn per dag, huidige, voorafgaande maand en jaar. U ook de kosten bekijken ten opzichte van budgetten en voorspelde kosten. Als u na verloop van tijd overschakelt naar langere weergaven, u uitgaventrends identificeren en zien waar overbesteding heeft plaatsgevonden. Als u budgetten hebt gemaakt, u ook gemakkelijk zien waar ze zijn overschreden. Ga als bedoeld als u Azure Cosmos DB-kosten weerziet in kostenanalyse:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Open het venster **Kostenbeheer + Facturering,** selecteer **Kostenbeheer** in het menu en selecteer **Kostenanalyse**. Vervolgens u het bereik voor een specifiek abonnement wijzigen in de **vervolgkeuzelijst Scope.**

1. Standaard worden de kosten voor alle services weergegeven in de eerste donutgrafiek. Selecteer het gebied in de grafiek met het label 'Azure Cosmos DB'.

1. Als u de kosten voor één service wilt beperken, zoals Azure Cosmos DB, selecteert u **Filter toevoegen** en selecteert u **Servicenaam**. Kies vervolgens **Azure Cosmos DB** in de lijst. Hier is een voorbeeld met kosten voor alleen Azure Cosmos DB:
 
   ![Kosten bewaken met deelvenster Kostenanalyse](./media/plan-manage-costs/cost-analysis-pane.png)

In het voorgaande voorbeeld ziet u de huidige kosten voor Azure Cosmos DB voor de maand februari. De grafieken bevatten ook Azure Cosmos DB-kosten per locatie en per resourcegroep.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over hoe prijzen werken in Azure Cosmos DB:

* [Prijsmodel in Azure Cosmos DB](how-pricing-works.md)
* [Kosten voor ingerichte doorvoer optimaliseren in Azure Cosmos DB](optimize-cost-throughput.md)
* [Kosten van query's optimaliseren in Azure Cosmos DB](optimize-cost-queries.md)
* [Opslagkosten optimaliseren in Azure Cosmos DB](optimize-cost-storage.md)