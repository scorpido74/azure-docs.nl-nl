---
title: Kosten plannen en beheren voor Azure Storage
description: Meer informatie over het plannen en beheren van de kosten voor Azure Storage met behulp van kosten analyse in Azure Portal.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304522"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Kosten plannen en beheren voor Azure Storage

In dit artikel wordt beschreven hoe u de kosten voor Azure Storage plant en beheert. Eerst gebruikt u de prijs calculator van Azure om de opslag kosten te plannen voordat u resources toevoegt. Nadat u begint met het gebruik van Azure Storage resources, gebruikt u functies voor kosten beheer om budgetten in te stellen en kosten te bewaken. U kunt ook prognose kosten bekijken en uitgaven trends controleren om gebieden te identificeren waar u mogelijk wilt handelen.

Houd er rekening mee dat de kosten voor Azure Storage slechts een deel van de maandelijkse kosten in uw Azure-factuur zijn. Hoewel in dit artikel wordt uitgelegd hoe u de kosten voor Azure Storage plant en beheert, wordt u gefactureerd voor alle Azure-Services en-resources die worden gebruikt voor uw Azure-abonnement, inclusief de services van derden. Nadat u vertrouwd bent met het beheren van de kosten voor Azure Storage, kunt u soort gelijke methoden Toep assen om de kosten te beheren voor alle Azure-Services die in uw abonnement worden gebruikt.

## <a name="prerequisites"></a>Vereisten

Kostenanalyse biedt ondersteuning voor verschillende typen Azure-accounts. Zie [Gegevens van kostenbeheer begrijpen](../../cost-management-billing/costs/understand-cost-mgt-data.md) voor de volledige lijst met ondersteunde accounttypen. Als u kostengegevens wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig. Zie [Toegang tot gegevens toewijzen](../../cost-management-billing/costs/assign-access-acm-data.md) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Kosten schatten voordat u een Azure Storage account maakt

Gebruik de [prijs calculator van Azure](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voordat u gegevens gaat maken en overdragen naar een Azure Storage-account.

1. Kies op de pagina [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/) de tegel **opslag accounts** .

2. Schuif omlaag in de pagina en zoek het gedeelte **opslag accounts** in uw schatting.

3. Kies opties in de vervolg keuzelijst. 

   Wanneer u de waarde van deze vervolg keuzelijsten wijzigt, verandert de kosten raming. Deze schatting wordt weer gegeven in de bovenhoek en onder aan de schatting. 
    
   ![Het deel venster kosten bewaken met kosten analyse](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Wanneer u de waarde van de vervolg keuzelijst **type** wijzigt, veranderen ook andere opties die in dit werk blad worden weer gegeven. Gebruik de koppelingen in het gedeelte **meer info** voor meer informatie over wat elke optie betekent en hoe deze opties van invloed zijn op de prijs van bewerkingen die betrekking hebben op opslag. 

4. Wijzig de overige opties om de invloed op uw schatting te zien.

## <a name="use-budgets-and-cost-alerts"></a>Budgetten en kostenwaarschuwingen gebruiken

U kunt [budgetten](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) maken om kosten te beheren en waarschuwingen te maken waarmee de betrokkenen automatisch worden geïnformeerd over uitgave afwijkingen en het overeden van Risico's. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en-resource groepen, dus zijn ze nuttig als onderdeel van een strategie voor de kosten bewaking. Ze kunnen echter beperkte functionaliteit hebben voor het beheren van afzonderlijke Azure-service kosten, zoals de kosten van Azure Storage, omdat ze zijn ontworpen om de kosten op een hoger niveau bij te houden.

## <a name="monitor-costs"></a>Kosten bewaken

Wanneer u Azure-resources met Azure Storage gebruikt, worden er kosten in rekening gebracht. De kosten voor de resource gebruiks eenheid variëren per tijds interval (seconden, minuten, uren en dagen) of per eenheids gebruik (bytes, mega bytes, enzovoort). Er worden kosten in rekening gebracht zodra het gebruik van Azure Storage wordt gestart. U kunt de kosten bekijken in het deel venster [kosten analyse](../../cost-management-billing/costs/quick-acm-cost-analysis.md) in de Azure Portal.

Wanneer u kosten analyse gebruikt, kunt u de kosten van Azure Storage in grafieken en tabellen weer geven voor verschillende tijds intervallen. Enkele voor beelden zijn de dag, de huidige en de vorige maand en het jaar. U kunt ook kosten weer geven op basis van budgetten en geraamde kosten. Door over te scha kelen naar langere weer gaven kunt u uitgaven trends helpen identificeren en zien waar overuitgave mogelijk is gebeurd. Als u budgetten hebt gemaakt, kunt u ook eenvoudig zien waar ze zijn overschreden.

Azure Storage kosten voor de kosten analyse weer geven:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Open het venster **Cost Management en facturering** , selecteer **kosten beheer** in het menu en selecteer vervolgens **kosten analyse**. U kunt vervolgens het bereik voor een specifiek abonnement wijzigen in de vervolg keuzelijst **bereik** .

   ![Het deel venster kosten bewaken met kosten analyse](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Als u alleen de kosten voor Azure Storage wilt weer geven, selecteert u **filter toevoegen** en selecteert u vervolgens **service naam**. Kies vervolgens **opslag** in de lijst. 

   Hier volgt een voor beeld van de kosten voor alleen Azure Storage:

   ![De opslag kosten bewaken met het deel venster kosten analyse](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

In het vorige voor beeld ziet u de huidige kosten voor de service. De kosten per Azure-regio (locaties) en per resource groep worden ook weer gegeven.  

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van kosten met [kosten analyse](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

Raadpleeg de volgende artikelen voor meer informatie over de werking van prijzen met Azure Storage:

- [Prijzen Azure Storage overzicht](https://azure.microsoft.com/pricing/details/storage/)
- [Kosten optimaliseren voor blob-opslag met gereserveerde capaciteit](../blobs/storage-blob-reserved-capacity.md)
