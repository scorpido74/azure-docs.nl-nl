---
title: Azure-prijzen voor uw organisatie bekijken en downloaden
description: Krijg meer informatie over het bekijken en downloaden van prijzen, of het schatten van de kosten met de prijzen van uw organisatie.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 486a6b46b6c4590b7f49cd8aba449204cd8f4fac
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709705"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Azure-prijzen voor uw organisatie bekijken en downloaden

Azure-klanten met een Azure EA (Enterprise Agreement) of [Microsoft-klantovereenkomst](#check-your-access-to-a-microsoft-customer-agreement) kunnen hun prijzen bekijken en downloaden in de Azure-portal.

## <a name="ea-pricing"></a>EA-prijzen

Afhankelijk van beleid dat door de ondernemingsbeheerder is ingesteld voor uw organisatie, bieden alleen bepaalde beheerdersrollen toegang tot de EA-prijsgegevens van uw organisatie. Zie [Inzicht in Azure Enterprise Agreement-beheerdersrollen in Azure](billing-understand-ea-roles.md) voor meer informatie.

1. Meld u als ondernemingsbeheerder aan bij de [Azure-portal.](https://portal.azure.com/)
1. Zoek naar *Kostenbeheer en facturering*.

   ![Schermopname van de zoekopdracht in de Azure-portal](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. Selecteer in het factureringsaccount de optie **Gebruik en kosten**.

   ![Schermopname van het gebruik en de kosten onder Facturering](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Selecteer ![Schermopname van de zoekopdracht in de Azure-portal](./media/billing-ea-pricing/download-icon.png) **Downloaden** voor de maand.

1. Selecteer onder **Prijzenoverzicht** de optie **CSV-bestand downloaden**.

   ![Schermopname van de knop CSV-bestand downloaden voor een prijzenoverzicht](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="microsoft-customer-agreement-pricing"></a>Prijzen van Microsoft-klantovereenkomst

U moet de eigenaar van het factureringsprofiel, de bijdrager, de lezer of de factuurbeheerder zijn om prijzen te kunnen bekijken en downloaden. Zie [Factureringsprofielrollen en taken](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)voor meer informatie over factureringsrollen voor Microsoft-klantovereenkomsten.

### <a name="download-price-sheets-for-the-current-billing-period"></a>Prijzenoverzichten voor de huidige factureringsperiode downloaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar *Kostenbeheer en facturering*.
1. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk eerst een factureringsrekening selecteren.
1. Zoek in het gebied **Overzicht** de downloadkoppelingen onder de kosten (maand tot heden).
1. Selecteer **Prijzenoverzicht voor Azure**.
![Schermopname waarop downloaden uit Overzicht wordt weergegeven](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>Prijzenoverzichten voor gefactureerde kosten downloaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar *Kostenbeheer en facturering*.
1. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk eerst een factureringsrekening selecteren.
1. Selecteer **Facturen**.
1. Ga in het factuurraster naar de rij van de factuur die overeenkomt met het prijzenoverzicht dat u wilt downloaden.
1. Klik op het weglatingsteken (`...`) aan het einde van de rij.
![Schermopname met het geselecteerde weglatingsteken](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Als u de prijzen voor de services in de geselecteerde factuur wilt bekijken, selecteert u **Prijzenoverzicht voor facturering**.
1. Als u de prijzen voor alle Azure-services voor de betreffende factureringsperiode wilt zien, selecteert u **Azure-prijzenoverzicht**.

![Schermopname van het contextmenu met prijzenoverzichten](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Kosten schatten met de Azure-prijscalculator

U kunt ook de prijzen van uw organisatie gebruiken om de kosten te schatten met de Azure-prijscalculator.

1. Ga naar de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator).
1. Selecteer **Aanmelden** in de rechterbovenhoek.
1. Selecteer **EA (Enterprise Agreement)** onder **Programma's en aanbieding** > **Licentieprogramma**.
1. Selecteer **Geen geselecteerd** onder **Programma's en aanbieding** > **Geselecteerde overeenkomst**.

    ![Schermopname van de knop CSV-bestand downloaden voor een prijzenoverzicht](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Kies de organisatie.
1. Selecteer **Toepassen**.
1. Zoek producten en voeg deze vervolgens toe aan uw schatting.
1. Geschatte prijzen die worden weergegeven, zijn gebaseerd op prijzen voor de organisatie die u hebt geselecteerd.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Uw toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Volgende stappen

Als u een EA-klant bent, raadpleegt u:

- [Toegang tot uw EA-factureringsgegevens voor Azure beheren](billing-manage-access.md)
- [Uw Microsoft Azure-factuur bekijken en downloaden](billing-download-azure-invoice.md)
- [Uw Microsoft Azure-gebruik en -kosten bekijken en downloaden](billing-download-azure-daily-usage.md)
- [Inzicht in de factuur voor Enterprise Agreement-klanten](billing-understand-your-bill-ea.md)

Als u een Microsoft-klantovereenkomst hebt, raadpleegt u:

- [Inzicht in de voorwaarden in uw prijzenoverzicht voor een Microsoft-klantovereenkomst](billing-mca-understand-pricesheet.md)
- [Uw Microsoft Azure-factuur bekijken en downloaden](billing-download-azure-invoice.md)
- [Uw Microsoft Azure-gebruik en -kosten bekijken en downloaden](billing-download-azure-daily-usage.md)
- [Belastingdocumenten voor uw factureringsprofiel bekijken en downloaden](billing-mca-download-tax-document.md)
- [Inzicht in de kosten voor de factuur van uw factureringsprofiel](billing-mca-understand-your-bill.md)
