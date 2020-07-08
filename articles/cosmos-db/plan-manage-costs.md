---
title: Kosten plannen en beheren voor Azure Cosmos DB
description: Meer informatie over het plannen en beheren van de kosten voor Azure Cosmos DB met behulp van kosten analyse in Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: 42421f745759d9aee75b285c3fbc6ea7217ba5c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85112698"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Kosten plannen en beheren voor Azure Cosmos DB

In dit artikel wordt beschreven hoe u de kosten voor Azure Cosmos DB kunt plannen en beheren. Eerst gebruikt u de Azure Cosmos DB capacity calculator om kosten te plannen voordat u resources toevoegt. Bij het toevoegen van de Azure-resources kunt u de geschatte kosten bekijken. Nadat u Azure Cosmos DB resources hebt gebruikt, gebruikt u de functies voor kosten beheer om budgetten in te stellen en kosten te bewaken. U kunt ook de geraamde kosten bekijken en de uitgaven trends identificeren om gebieden te identificeren waar u mogelijk wilt handelen.

Houd er rekening mee dat de kosten voor Azure Cosmos DB slechts een deel van de maandelijkse kosten in uw Azure-factuur zijn. Als u andere Azure-Services gebruikt, wordt u gefactureerd voor alle Azure-Services en-resources die worden gebruikt in uw Azure-abonnement, inclusief de services van derden. In dit artikel wordt uitgelegd hoe u de kosten voor Azure Cosmos DB plant en beheert. Nadat u vertrouwd bent met het beheren van de kosten voor Azure Cosmos DB, kunt u soort gelijke methoden Toep assen om de kosten te beheren voor alle Azure-Services die in uw abonnement worden gebruikt.

## <a name="prerequisites"></a>Vereisten

Kostenanalyse biedt ondersteuning voor verschillende typen Azure-accounts. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](../cost-management-billing/costs/understand-cost-mgt-data.md). Als u kostengegevens wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig. Zie [Toegang tot gegevens toewijzen](../cost-management-billing/costs/assign-access-acm-data.md) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

## <a name="review-estimated-costs-with-capacity-calculator"></a>Geschatte kosten controleren met de capaciteits Calculator

Gebruik de [Azure Cosmos DB capaciteits Calculator](https://cosmos.azure.com/capacitycalculator/) om de kosten te schatten voordat u de resources in een Azure Cosmos-account maakt. De capaciteits calculator wordt gebruikt om een schatting te krijgen van de vereiste door Voer en kosten van uw workload. Het configureren van uw Azure Cosmos-data bases en containers met de juiste hoeveelheid ingerichte door Voer of [aanvraag eenheden (ru/s)](request-units.md)voor uw werk belasting is essentieel om de kosten en prestaties te optimaliseren. U moet gegevens invoeren, zoals het API-type, het aantal regio's, de item grootte, lees/schrijf aanvragen per seconde, de totale hoeveelheid gegevens die is opgeslagen om een kosten raming op te halen. Zie het artikel [estimate](estimate-ru-with-capacity-planner.md) voor meer informatie over de capaciteits Calculator.

In de volgende scherm afbeelding ziet u de schatting van de door Voer en de kosten met behulp van de capaciteits Calculator:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Kosten raming in Azure Cosmos DB capaciteits Calculator":::

## <a name="review-estimated-costs-from-the-azure-portal"></a>Geschatte kosten van de Azure Portal controleren

Wanneer u Azure Cosmos DB resources van Azure Portal maakt, worden de geschatte kosten weer geven. Gebruik de volgende stappen om de kosten raming te controleren:

1. Meld u aan bij de Azure Portal en navigeer naar uw Azure Cosmos-account.
1. Ga naar de **Data Explorer**.
1. Maak een nieuwe container, zoals een grafiek container.
1. Voer de door Voer die vereist is voor uw werk belasting, zoals 400 RU/s. Nadat u de doorvoer waarde hebt ingevoerd, ziet u de prijs schatting zoals weer gegeven op de volgende scherm afbeelding:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Kosten raming in Azure Portal":::

Als uw Azure-abonnement een bestedings limiet heeft, voor komt u dat u kunt bestedingen over uw tegoed. Wanneer u Azure-resources maakt en gebruikt, worden uw tegoeden gebruikt. Wanneer u de krediet limiet bereikt, worden de resources die u hebt geïmplementeerd, uitgeschakeld voor de rest van die facturerings periode. U kunt uw krediet limiet niet wijzigen, maar wel verwijderen. Zie [Azure bestedings limiet](../billing/billing-spending-limit.md)voor meer informatie over bestedings limieten.

## <a name="use-budgets-and-cost-alerts"></a>Budgetten en kostenwaarschuwingen gebruiken

U kunt [budgetten](../cost-management/tutorial-acm-create-budgets.md) maken om kosten te beheren en waarschuwingen te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en-resource groepen, dus zijn ze nuttig als onderdeel van een strategie voor de kosten bewaking. Ze kunnen echter beperkte functionaliteit hebben voor het beheren van afzonderlijke Azure-service kosten, zoals de kosten van Azure Cosmos DB, omdat ze zijn ontworpen om de kosten op een hoger niveau bij te houden.

## <a name="monitor-costs"></a>Kosten bewaken

Als u resources met Azure Cosmos DB gebruikt, worden er kosten in rekening gebracht. De kosten voor de resource gebruiks eenheid variëren per tijds interval (seconden, minuten, uren en dagen) of per aanvraag eenheids gebruik. Zodra het gebruik van Azure Cosmos DB gestart, worden de kosten in rekening gebracht en kunt u ze zien in het deel venster [kosten analyse](../cost-management/quick-acm-cost-analysis.md) in de Azure Portal.

Wanneer u kosten analyse gebruikt, kunt u de kosten voor de Azure Cosmos DB in grafieken en tabellen weer geven voor verschillende tijds intervallen. Enkele voor beelden zijn dag, actueel, voor gaande maand en jaar. U kunt ook kosten weer geven op basis van budgetten en geraamde kosten. Door over te scha kelen naar langere weer gaven kunt u uitgaven trends helpen identificeren en zien waar overuitgave mogelijk is gebeurd. Als u budgetten hebt gemaakt, kunt u ook eenvoudig zien waar ze zijn overschreden. Azure Cosmos DB kosten voor de kosten analyse weer geven:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Open het venster **Cost Management en facturering** , selecteer **kosten beheer** in het menu en selecteer vervolgens **kosten analyse**. U kunt vervolgens het bereik voor een specifiek abonnement wijzigen in de vervolg keuzelijst **bereik** .

1. Kosten voor alle services worden standaard weer gegeven in de eerste cirkel diagram. Selecteer het gebied in de grafiek met het label Azure Cosmos DB.

1. Als u de kosten voor één service, zoals Azure Cosmos DB, wilt beperken, selecteert u **filter toevoegen** en selecteert u vervolgens **service naam**. Kies vervolgens **Azure Cosmos DB** in de lijst. Hier volgt een voor beeld van de kosten voor alleen Azure Cosmos DB:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Het deel venster kosten bewaken met kosten analyse":::

In het vorige voor beeld ziet u de huidige kosten voor Azure Cosmos DB voor de maand feb. De grafieken bevatten ook Azure Cosmos DB kosten per locatie en per resource groep.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over de werking van prijzen in Azure Cosmos DB:

* [Prijsmodel in Azure Cosmos DB](how-pricing-works.md)
* [Kosten voor ingerichte doorvoer optimaliseren in Azure Cosmos DB](optimize-cost-throughput.md)
* [Kosten van query's optimaliseren in Azure Cosmos DB](optimize-cost-queries.md)
* [De opslag kosten in Azure Cosmos DB optimaliseren](optimize-cost-storage.md)