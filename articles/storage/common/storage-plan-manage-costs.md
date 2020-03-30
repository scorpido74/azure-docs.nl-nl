---
title: Kosten voor Azure Storage plannen en beheren
description: Meer informatie over het plannen en beheren van kosten voor Azure Storage met behulp van kostenanalyse in Azure-portal.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304522"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Kosten voor Azure Storage plannen en beheren

In dit artikel wordt beschreven hoe u de kosten voor Azure Storage plant en beheert. Eerst gebruikt u de Azure-prijscalculator om de opslagkosten te helpen plannen voordat u resources toevoegt. Nadat u Azure Storage-resources hebt gebruikt, gebruikt u kostenbeheerfuncties om budgetten in te stellen en de kosten te bewaken. U ook de voorspelde kosten bekijken en uitgaventrends controleren om gebieden te identificeren waar u mogelijk wilt handelen.

Houd er rekening mee dat de kosten voor Azure Storage slechts een deel van de maandelijkse kosten in uw Azure-factuur zijn. Hoewel in dit artikel wordt uitgelegd hoe u de kosten voor Azure Storage plannen en beheren, worden kosten in rekening gebracht voor alle Azure-services en -resources die worden gebruikt voor uw Azure-abonnement, inclusief de services van derden. Nadat u bekend bent met het beheren van de kosten voor Azure Storage, u vergelijkbare methoden toepassen om de kosten te beheren voor alle Azure-services die in uw abonnement worden gebruikt.

## <a name="prerequisites"></a>Vereisten

Kostenanalyse biedt ondersteuning voor verschillende typen Azure-accounts. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](../../cost-management-billing/costs/understand-cost-mgt-data.md). Als u kostengegevens wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig. Zie [Toegang tot gegevens toewijzen](../../cost-management-billing/costs/assign-access-acm-data.md) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Kosten schatten voordat u een Azure Storage-account maakt

Gebruik de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voordat u gegevens maakt en begint over te dragen naar een Azure Storage-account.

1. Kies op de pagina [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/) de tegel **Opslagaccounts.**

2. Schuif naar beneden op de pagina en zoek het gedeelte **Opslagaccounts** van uw schatting.

3. Kies opties in de vervolgkeuzelijsten. 

   Terwijl u de waarde van deze vervolgkeuzelijsten wijzigt, verandert de kostenraming. Die schatting verschijnt zowel in de bovenhoek als onderin de schatting. 
    
   ![Kosten bewaken met deelvenster Kostenanalyse](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Als u de waarde van de vervolgkeuzelijst **Type** wijzigt, worden ook andere opties die op dit werkblad worden weergegeven, gewijzigd. Gebruik de koppelingen in de sectie **Meer info** voor meer informatie over wat elke optie betekent en hoe deze opties van invloed zijn op de prijs van opslaggerelateerde bewerkingen. 

4. Wijzig de resterende opties om de invloed op uw schatting te zien.

## <a name="use-budgets-and-cost-alerts"></a>Budgetten en kostenwaarschuwingen gebruiken

U kunt [budgetten](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) maken om kosten te beheren en waarschuwingen te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en resourcegroepen, zodat ze nuttig zijn als onderdeel van een algemene strategie voor kostenbewaking. Ze kunnen echter beperkte functionaliteit hebben om afzonderlijke Azure-servicekosten te beheren, zoals de kosten van Azure Storage, omdat ze zijn ontworpen om de kosten op een hoger niveau bij te houden.

## <a name="monitor-costs"></a>Kosten controleren

Terwijl u Azure-resources gebruikt met Azure Storage, brengt u kosten met zich mee. Kosten voor resourcegebruikseenheid variëren afhankelijk van tijdsintervallen (seconden, minuten, uren en dagen) of per eenheidsgebruik (bytes, megabytes, enzovoort.) Er worden kosten gemaakt zodra het gebruik van Azure Storage begint. U de kosten zien in het deelvenster [kostenanalyse](../../cost-management-billing/costs/quick-acm-cost-analysis.md) in de Azure-portal.

Wanneer u kostenanalyse gebruikt, u azure-opslagkosten bekijken in grafieken en tabellen voor verschillende tijdsintervallen. Enkele voorbeelden zijn per dag, huidige en voorafgaande maand en jaar. U ook de kosten bekijken ten opzichte van budgetten en voorspelde kosten. Als u na verloop van tijd overschakelt naar langere weergaven, u uitgaventrends identificeren en zien waar overbesteding heeft plaatsgevonden. Als u budgetten hebt gemaakt, u ook gemakkelijk zien waar ze zijn overschreden.

Ga als u azure storagekosten weergeven in kostenanalyse:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Open het venster **Kostenbeheer + Facturering,** selecteer **Kostenbeheer** in het menu en selecteer **Kostenanalyse**. Vervolgens u het bereik voor een specifiek abonnement wijzigen in de **vervolgkeuzelijst Scope.**

   ![Kosten bewaken met deelvenster Kostenanalyse](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Als u alleen de kosten voor Azure Storage wilt weergeven, selecteert u **Filter toevoegen** en selecteert u **Servicenaam**. Kies vervolgens **opslag** in de lijst. 

   Hier vindt u een voorbeeld van kosten voor alleen Azure Storage:

   ![Opslagkosten bewaken met deelvenster Kostenanalyse](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

In het voorgaande voorbeeld ziet u de huidige kosten voor de service. Kosten per Azure-regio 's (locaties) en per resourcegroep worden ook weergegeven.  

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van kosten met [kostenanalyse](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

Zie de volgende artikelen voor meer informatie over hoe prijzen werken met Azure Storage:

- [Prijzen overzicht azure-opslag](https://azure.microsoft.com/pricing/details/storage/)
- [Kosten optimaliseren voor blob-opslag met gereserveerde capaciteit](../blobs/storage-blob-reserved-capacity.md)
