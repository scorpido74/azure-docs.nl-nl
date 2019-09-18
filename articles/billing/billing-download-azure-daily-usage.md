---
title: Azure-gebruik en -kosten weergeven en downloaden
description: Hierin wordt beschreven hoe u uw dagelijkse Azure-gebruiksgegevens en -kosten kunt downloaden of weergeven.
keywords: facturering van gebruik, gebruikskosten, gebruik downloaden, gebruik weergeven, Azure-factuur, Azure-gebruik
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 7d2d7be562eaaa7dd21e63735f5697ffe5a62f8a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491433"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Uw Azure-gebruik en -kosten weergeven en downloaden

Als u een EA-klant bent of een [Microsoft-klantovereenkomst](#check-your-access-to-a-microsoft-customer-agreement) hebt, kunt u uw Azure-gebruik en kosten downloaden in de [Azure-portal](https://portal.azure.com/). Voor andere abonnementen gaat u naar het [Azure-accountcentrum](https://account.azure.com/Subscriptions) om uw gebruiksgegevens te downloaden.

Alleen bepaalde rollen hebben toestemming om Azure-gebruiksgegevens op te vragen, zoals de accountbeheerder of de Enterprise-beheerder. Zie [Toegang tot factureringsgegevens voor Azure beheren](billing-manage-access.md) voor meer informatie over het verkrijgen van toegang tot factureringsgegevens.

Als u een [Microsoft-klantovereenkomst](#check-your-access-to-a-microsoft-customer-agreement) hebt, moet u een eigenaar, inzender, lezer van het factureringsprofiel of een factuurbeheerder zijn om uw Azure-gebruik en -kosten te kunnen bekijken. Zie [Factureringsprofielrollen en taken](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)voor meer informatie over factureringsrollen voor Microsoft-klantovereenkomsten.

## <a name="download-usage-from-the-account-center-csv"></a>Gebruiksgegevens downloaden uit het accountcentrum (.csv)

1. Meld u als accountbeheerder aan bij het [Azure-accountcentrum](https://account.windowsazure.com/subscriptions).

2. Selecteer het abonnement waarvoor u de factuur- en gebruiksgegevens wilt bekijken.

3. Selecteer **Factureringsgeschiedenis**.

    ![Schermopname van de optie Factureringsgeschiedenis](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. U kunt uw overzichten voor de afgelopen zes factureringsperioden en de huidige niet-gefactureerde periode bekijken.

    ![Schermopname van de factureringsperioden, opties voor het downloaden van facturen en dagelijkse gebruiksgegevens en de totale kosten voor elke factureringsperiode](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Selecteer **Huidig overzicht weergeven** om een schatting van uw kosten te zien voor het moment waarop de schatting wordt gegenereerd. Deze informatie wordt dagelijks bijgewerkt en omvat mogelijk niet alle gebruiksgegevens. Uw maandelijkse factuur kan afwijken van deze schatting.

    ![Schermopname van de optie Huidig overzicht weergeven](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Schermopname van de geschatte huidige kosten](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Selecteer **Gebruiksgegevens downloaden** om de dagelijkse gebruiksgegevens te downloaden als een CSV-bestand. Als er twee versies beschikbaar zijn, moet u versie 2 downloaden.

    ![Schermopname van de optie Gebruiksgegevens downloaden](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Alleen de accountbeheerder heeft toegang tot het Azure-accountcentrum. Andere factureringsbeheerders, zoals een eigenaar, kunnen gebruiksgegevens downloaden met behulp van de [Billing-API's](billing-usage-rate-card-overview.md).

Zie [Meer informatie over uw factuur voor Microsoft Azure](billing-understand-your-bill.md) voor meer informatie over uw dagelijkse gebruik. Zie [Onverwachte kosten voorkomen met Azure-facturering en -kostenbeheer](billing-getting-started.md) voor hulp bij het beheren van uw kosten.

## <a name="download-usage-for-ea-customers"></a>Gebruiksgegevens downloaden voor EA-klanten

Om als een EA-klant gebruiksgegevens weer te geven en te downloaden, moet u een Enterprise-beheerder, accounteigenaar of afdelingsbeheerder zijn en moet het beleid Kosten weergeven zijn ingeschakeld.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar *Kostenbeheer en facturering*.

    ![Schermopname van de zoekopdracht in de Azure-portal](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Selecteer **Gebruik + kosten**.
1. Selecteer **Downloaden** voor de maand waarvan u de gegevens wilt downloaden.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Gebruiksgegevens voor uw Microsoft-klantovereenkomst downloaden

Als u een Microsoft-klantovereenkomst hebt, kunt u uw Azure-gebruiksgegevens en -kosten voor uw factureringsprofiel downloaden. U moet een eigenaar, inzender, lezer van het factureringsprofiel of een factuurbeheerder zijn om de Azure-gebruiksgegevens en -kosten te kunnen downloaden (.csv).

### <a name="download-usage-for-billed-charges"></a>Gebruiksgegevens voor gefactureerde kosten downloaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zoek naar *Kostenbeheer en facturering*.
3. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk eerst een factureringsrekening selecteren.
4. Selecteer **Facturen**.
5. Ga in het factuurraster naar de rij van de factuur die overeenkomt met de gebruiksgegevens die u wilt downloaden.
6. Klik op het weglatingsteken (`...`) aan het einde van de rij.

    ![Schermopname van het weglatingsteken aan het einde van de rij](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. Selecteer **Azure-gebruik en -kosten** in het contextmenu voor het downloaden.

     ![Schermopname van het Azure-gebruik en de geselecteerde kosten](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Gebruiksgegevens voor kosten in behandeling

U kunt ook het gebruik van de maand tot heden voor de huidige factureringsperiode downloaden. Deze gebruikskosten zijn nog niet gefactureerd.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zoek naar *Kostenbeheer en facturering*.
3. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk eerst een factureringsrekening selecteren.
4. Zoek in het gebied **Overzicht** de downloadkoppelingen onder de kosten (maand tot heden).
5. Selecteer **Azure-gebruik en -kosten**.

    ![Schermopname waarop downloaden uit Overzicht wordt weergegeven](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Uw toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over uw factuur en gebruikskosten:

- [Begrippen over uw gedetailleerde Microsoft Azure-gebruik begrijpen](billing-understand-your-usage.md)
- [Meer informatie over uw factuur voor Microsoft Azure](billing-understand-your-bill.md)
- [Uw Microsoft Azure-factuur bekijken en downloaden](billing-download-azure-invoice.md)
- [Azure-prijzen voor uw organisatie bekijken en downloaden](billing-ea-pricing.md)

Als u een Microsoft-klantovereenkomst hebt, raadpleegt u:

- [Meer informatie over begrippen over het gedetailleerde gebruik van uw Microsoft-klantovereenkomst](billing-mca-understand-your-usage.md)
- [Meer informatie over de kosten van uw Microsoft-klantovereenkomst](billing-mca-understand-your-bill.md)
- [Uw Microsoft Azure-factuur bekijken en downloaden](billing-download-azure-invoice.md)
- [Belastingdocumenten voor uw Microsoft-klantovereenkomst weergeven en downloaden](billing-mca-download-tax-document.md)
- [Azure-prijzen voor uw organisatie bekijken en downloaden](billing-ea-pricing.md)
