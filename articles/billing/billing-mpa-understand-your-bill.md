---
title: Inzicht in de kosten op de factuur van uw Microsoft Partner-overeenkomst - Azure
description: Meer informatie over hoe u de kosten op uw factuur kunt lezen en begrijpen.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2019
ms.author: banders
ms.openlocfilehash: 9994ef6da379d0063930912a4d198b81623ddbe8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223701"
---
# <a name="understand-charges-on-your-microsoft-partner-agreement-invoice"></a>Inzicht in de kosten op de factuur van uw Microsoft Partner-overeenkomst

 In de factureringsrekening voor een Microsoft Partner-overeenkomst wordt elke maand een factuur voor elk factureringsprofiel gegenereerd. De factuur bevat alle klantkosten van de vorige maand. U krijgt meer inzicht in de kosten op uw factuur door de afzonderlijke transacties te analyseren in de Azure-portal. U kunt ook uw facturen weergeven in de Azure-portal. Zie [hoe u facturen kunt downloaden vanuit de Azure-portal ](billing-download-azure-invoice.md) voor meer informatie.

Dit artikel is van toepassing op een factureringsrekening voor een Microsoft Partner-overeenkomst. [Controleer of u toegang hebt tot een Microsoft Partner-overeenkomst](#check-access-to-a-microsoft-partner-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Transacties voor een factuur in de Azure-portal weergeven

1. Meld u aan bij [Azure Portal](https://www.azure.com).

2. Zoek naar *Kostenbeheer en facturering*.

   <!--  ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Selecteer **Alle transacties** aan de linkerkant van de pagina. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount, een factureringsprofiel of een klant selecteren en vervolgens **Alle transacties**.

4. De pagina Alle transacties geeft de volgende informatie weer:

    <!-- ![Screenshot that shows the billed transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-all-transactions.png) -->

    |Kolom  |Definitie  |
    |---------|---------|
    |Date     | Transactiedatum  |
    |Factuur-id     | De identificatie van de factuur waarop de transactie is gefactureerd. Als u een ondersteuningsaanvraag indient, deelt u deze id met de Azure-ondersteuning om uw ondersteuningsaanvraag te versnellen |
    |Transactietype     |  Het type transactie, bijvoorbeeld aankoop-, annulerings- of gebruikskosten  |
    |Productfamilie     | De categorie van producten, zoals berekening voor virtuele machines of database voor Azure SQL-database|
    |Product-sku     | Een unieke code die het exemplaar van uw product identificeert |
    |Aantal     |  Het aantal transacties      |
    |Factureringsprofiel     | De transactie wordt weergegeven in deze factuur van het factureringsprofiel |

5. Zoek op factuur-id om de transacties voor de betreffende factuur te filteren.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Bekijk de openstaande kosten om een schatting te maken van uw volgende factuur

De kosten worden geschat en als openstaande kosten beschouwd totdat ze worden gefactureerd. U kunt openstaande kosten voor het factureringsprofiel voor uw Microsoft Partner-overeenkomst bekijken in de Azure-portal om uw volgende factuur in te calculeren. De openstaande kosten zijn een schatting en zijn exclusief belasting. De werkelijke kosten op uw volgende factuur zullen daarom afwijken van de openstaande kosten.

### <a name="view-pending-transactions"></a>Openstaande transacties weergeven

Wanneer u de openstaande kosten identificeert, krijgt u inzicht in de kosten door de afzonderlijke transacties te analyseren die aan de kosten hebben bijgedragen. Op dit moment worden openstaande gebruikskosten niet weergegeven op de pagina Alle transacties. U kunt de openstaande gebruikskosten weergeven op de Azure-abonnementenpagina. Voor meer informatie, zie [Openstaande gebruikskosten weergeven](#view-pending-usage-charges)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek naar *Kostenbeheer en facturering*.

   <!-- ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount selecteren. Vanuit het factureringsaccount selecteert u **Factureringsprofielen** en vervolgens een factureringsprofiel.

4. Selecteer **Alle transacties** aan de linkerkant van de pagina.

5. Zoek op *Openstaand*. Gebruik de filter **Periode** om de openstaande kosten voor de huidige of vorige maand weer te geven.

   <!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Openstaande kosten per klant weergeven

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek naar *Kostenbeheer en facturering*.

3. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount selecteren. Vanuit het factureringsaccount selecteert u **Factureringsprofielen** en vervolgens een factureringsprofiel.

4. Selecteer **Klanten** links op de pagina.

    <!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

5. Op de pagina Klanten worden de kosten voor de huidige en vorige maand weergegeven voor elke klant die is gekoppeld aan het factureringsprofiel. De maandkosten tot heden zijn de openstaande kosten voor de huidige maand en worden gefactureerd wanneer de factuur voor deze maand wordt gegenereerd. Als de factuur van de vorige maand nog steeds niet is gegenereerd, dan zijn de kosten van vorige maand ook openstaand.

### <a name="view-pending-usage-charges"></a>Openstaande gebruikskosten weergeven

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek naar *Kostenbeheer en facturering*.

3. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount selecteren. Vanuit het factureringsaccount selecteert u **Factureringsprofielen** en vervolgens een factureringsprofiel.

4. Selecteer **Azure-abonnementen** links op de pagina.

5. De pagina Azure-abonnementen geeft de kosten voor elk abonnement voor de huidige en vorige maand weer in het factureringsprofiel. De maandkosten tot heden zijn de openstaande kosten voor de huidige maand en worden gefactureerd wanneer de factuur voor deze maand wordt gegenereerd. Als de factuur van de vorige maand nog steeds niet is gegenereerd, dan zijn de kosten van vorige maand ook openstaand.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Uw Azure-gebruikskosten analyseren

Gebruik het CSV-bestand voor Azure-gebruik en -kosten om uw kosten op gebruiksbasis te analyseren. U kunt het [CSV-bestand met uw Azure-gebruiksgegevens en -kosten downloaden vanuit de Azure-portal](billing-download-azure-daily-usage.md).

U kunt het bestand met Azure-gebruiksgegevens en -kosten filteren om de gebruikskosten voor elk product in de PDF-factuur af te stemmen. Als u gedetailleerde gebruikskosten voor een bepaald product wilt weergeven, filtert u de kolom **product** in het CSV-bestand met Azure-gebruiksgegevens en -kosten zo dat alleen de naam van dat product wordt opgenomen.

U kunt ook de kolom **customerName** in het CSV-bestand met Azure-gebruiksgegevens en -kosten filteren om de dagelijkse gebruikskosten per klant te bekijken. Als u de dagelijkse gebruikskosten per Azure-abonnement wilt weergeven, filtert u de kolom **subscriptionName**.


## <a name="pay-your-bill"></a>Uw factuur betalen

Instructies voor het betalen van uw factuur staan onderaan de factuur. U kunt binnen 60 dagen na de factuurdatum betalen per directe overboeking of cheque.

Als u uw factuur al hebt betaald, kunt u de status van de betaling controleren op de pagina Facturen in de Azure-portal.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Toegang tot een Microsoft Partner-overeenkomst controleren
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over uw factuur en gedetailleerde gebruik, zie:
