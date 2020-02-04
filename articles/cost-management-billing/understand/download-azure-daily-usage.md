---
title: Azure-gebruik en -kosten weergeven en downloaden
description: Hierin wordt beschreven hoe u uw dagelijkse Azure-gebruiksgegevens en -kosten kunt downloaden of weergeven.
keywords: facturering van gebruik, gebruikskosten, gebruik downloaden, gebruik weergeven, Azure-factuur, Azure-gebruik
author: bandersmsft
ms.author: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 02d446d1b70b64092501804e793b400e983a4d80
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "75995128"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Uw Azure-gebruik en -kosten weergeven en downloaden

U kunt een dagelijkse uitsplitsing van uw Azure-gebruiksgegevens en -kosten downloaden in de Azure-portal. Alleen bepaalde rollen hebben toestemming om Azure-gebruiksgegevens op te vragen, zoals de accountbeheerder of de Enterprise-beheerder. Zie [Toegang tot factureringsgegevens voor Azure beheren](../manage/manage-billing-access.md) voor meer informatie over het verkrijgen van toegang tot factureringsgegevens.

Als u een Microsoft-klantovereenkomst (MCA) hebt, moet u een eigenaar, inzender, lezer van het factureringsprofiel of een factuurbeheerder zijn om uw Azure-gebruik en -kosten te kunnen bekijken.  Als u een Microsoft Partner-overeenkomst (MPA) hebt, kan alleen de rol Globale beheerder en de rol Beheerderagent in de partnerorganisatie Azure-gebruiksgegevens en -kosten bekijken en downloaden. [Controleer uw type factureringsrekening in de Azure-portal ](#check-your-billing-account-type).

## <a name="download-usage-from-the-azure-portal-csv"></a>Gebruiksgegevens downloaden vanuit de Azure-portal (CSV)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar *Kostenbeheer en facturering*.

    ![Schermopname van de zoekopdracht in de Azure-portal](./media/download-azure-daily-usage/portal-cm-billing-search.png)

1. Afhankelijk van uw toegang moet u mogelijk een factureringsrekening of factureringsprofiel selecteren.
1. Selecteer **Facturen** onder **Facturering** in het menu aan de linkerkant.
1. Ga in het factuurraster naar de rij van de factureringsperiode die overeenkomt met de gebruiksgegevens die u wilt downloaden.
1. Selecteer het **downloadpictogram** of het beletselteken (`...`) aan de rechterkant.
1. Het deelvenster Downloaden wordt geopend aan de rechterkant. Selecteer **Downloaden** in de sectie **Gebruiksgegevens**.

## <a name="download-usage-for-ea-customers"></a>Gebruiksgegevens downloaden voor EA-klanten

Om als een EA-klant gebruiksgegevens weer te geven en te downloaden, moet u een Enterprise-beheerder, accounteigenaar of afdelingsbeheerder zijn en moet het beleid Kosten weergeven zijn ingeschakeld.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar *Kostenbeheer en facturering*.

    ![Schermopname van de zoekopdracht in de Azure-portal](./media/download-azure-daily-usage/portal-cm-billing-search.png)

1. Selecteer **Gebruik + kosten**.
1. Selecteer **Downloaden** voor de maand waarvan u de gegevens wilt downloaden.

## <a name="download-usage-for-pending-charges"></a>Gebruiksgegevens voor kosten in behandeling

Als u een Microsoft-klantovereenkomst hebt, kunt u het gebruik van de maand tot heden voor de huidige factureringsperiode downloaden. Deze gebruikskosten zijn nog niet gefactureerd.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zoek naar *Kostenbeheer en facturering*.
3. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk eerst een factureringsrekening selecteren.
4. Zoek in het gebied **Overzicht** de downloadkoppelingen onder de kosten (maand tot heden).
5. Selecteer **Azure-gebruik en -kosten**.

    ![Schermopname waarop downloaden uit Overzicht wordt weergegeven](./media/download-azure-daily-usage/open-usage01.png)

## <a name="check-your-billing-account-type"></a>Uw type factureringsrekening controleren
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over uw factuur en gebruikskosten:

- [Begrippen over uw gedetailleerde Microsoft Azure-gebruik begrijpen](understand-usage.md)
- [Meer informatie over uw factuur voor Microsoft Azure](review-individual-bill.md)
- [Uw Microsoft Azure-factuur bekijken en downloaden](download-azure-invoice.md)
- [Azure-prijzen voor uw organisatie bekijken en downloaden](../manage/ea-pricing.md)

Als u een Microsoft-klantovereenkomst hebt, raadpleegt u:

- [Meer informatie over begrippen over het gedetailleerde gebruik van uw Microsoft-klantovereenkomst](mca-understand-your-usage.md)
- [Meer informatie over de kosten van uw Microsoft-klantovereenkomst](review-customer-agreement-bill.md)
- [Uw Microsoft Azure-factuur weergeven en downloaden](download-azure-invoice.md)
- [Belastingdocumenten voor uw Microsoft-klantovereenkomst weergeven en downloaden](mca-download-tax-document.md)
- [Azure-prijzen voor uw organisatie bekijken en downloaden](../manage/ea-pricing.md)
