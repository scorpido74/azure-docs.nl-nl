---
title: Factuurkosten van uw Microsoft-klantovereenkomst - Azure
description: Meer informatie over hoe u de kosten op uw factuur kunt lezen en begrijpen.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: b5dfffecbd2908e987b76f29b14937f0e50ce64f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75388989"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Inzicht in de kosten op de factuur van uw Microsoft-klantovereenkomst

U krijgt meer inzicht in de kosten op uw factuur door de afzonderlijke transacties te analyseren. In het factureringsaccount voor een Microsoft-klantovereenkomst wordt elke maand een factuur voor elk factureringsprofiel gegenereerd. De factuur bevat alle kosten van de vorige maand. U kunt uw facturen weergeven in de Azure-portal. Voor meer informatie, zie [Facturen downloaden voor een Microsoft-klantovereenkomst](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Dit artikel is van toepassing op een factureringsaccount voor een Microsoft-klantovereenkomst. [Controleer of u toegang hebt tot een Microsoft-klantovereenkomst](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Transacties voor een factuur in de Azure-portal weergeven

1. Meld u aan bij de [Azure-portal](https://www.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

    ![Schermopname van zoekopdracht in de Azure-portal naar kostenbeheer en facturering](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecteer **Alle transacties** aan de linkerkant van de pagina. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount, een factureringsprofiel of een factuursectie selecteren en vervolgens **Alle transacties**.

4. De pagina Alle transacties geeft de volgende informatie weer:

    ![Schermopname van lijst met gefactureerde transacties](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Kolom  |Definitie  |
    |---------|---------|
    |Date     | Transactiedatum  |
    |Factuur-id     | De identificatie van de factuur waarop de transactie is gefactureerd. Als u een ondersteuningsaanvraag indient, deelt u deze id met de Azure-ondersteuning om uw ondersteuningsaanvraag te versnellen |
    |Transactietype     |  Het type transactie, bijvoorbeeld aankoop-, annulerings- of gebruikskosten  |
    |Productfamilie     | De categorie van producten, zoals berekening voor virtuele machines of database voor Azure SQL-database|
    |Product-sku     | Een unieke code die het exemplaar van uw product identificeert |
    |Aantal     |  Het aantal transacties      |
    |Factuursectie     | De transactie wordt weergegeven in deze factuursectie van het factureringsprofiel |
    |Factureringsprofiel     | De transactie wordt weergegeven in deze factuur van het factureringsprofiel |

5. Zoek op factuur-id om de transacties voor de betreffende factuur te filteren.

### <a name="view-transactions-by-invoice-sections"></a>Transacties per factuursectie weergeven

Factuursecties helpen u bij het organiseren van de factuurkosten voor een factureringsprofiel. Voor meer informatie, zie [Inzicht in factuursecties](billing-mca-overview.md#invoice-sections). Wanneer er een factuur wordt gegenereerd, worden de kosten voor alle secties in het factureringsprofiel weergegeven op de factuur.

De volgende afbeelding toont de factuurkosten voor de Administratieafdeling op een voorbeeldfactuur.

![Voorbeeldafbeelding met gegevens gesorteerd per factuursectie](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Wanneer u de kosten voor een factuursectie hebt geïdentificeerd, kunt u de transacties in de Azure-portal weergeven voor meer inzicht in de kosten.

1. Ga naar de pagina Alle transacties in de Azure-portal om transacties voor een factuur weer te geven. Voor meer informatie, zie [Transacties voor een factuur in de Azure-portal weergeven](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Filter op de naam van de factuursectie om de transacties weer te geven.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Bekijk de openstaande kosten om een schatting te maken van uw volgende factuur

In de factureringsaccount voor een Microsoft-klantovereenkomst worden de kosten geraamd en op openstaand gezet totdat ze worden gefactureerd. U kunt de openstaande kosten in de Azure-portal weergeven om een schatting te maken van uw volgende factuur. De openstaande kosten zijn een schatting en zijn exclusief belasting. De werkelijke kosten op uw volgende factuur zullen daarom afwijken van de openstaande kosten.

### <a name="view-summary-of-pending-charges"></a>Overzicht van openstaande kosten weergeven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van zoekopdracht in de Azure-portal naar kostenbeheer en facturering](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount selecteren. Vanuit het factureringsaccount selecteert u **Factureringsprofielen** en vervolgens een factureringsprofiel.

4. Selecteer het tabblad **Overzicht** bovenin het scherm.

5. De kostensectie geeft de maandkosten tot heden en de kosten van vorige maand weer.

   ![Schermopname van zoekopdracht in de Azure-portal naar kostenbeheer en facturering](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

De maandkosten tot heden zijn de openstaande kosten voor de huidige maand en worden gefactureerd wanneer de factuur voor deze maand wordt gegenereerd. Als de factuur van de vorige maand nog steeds niet is gegenereerd, dan zijn de kosten van vorige maand ook openstaand en verschijnen zij op uw volgende factuur.

### <a name="view-pending-transactions"></a>Openstaande transacties weergeven

Wanneer u de openstaande kosten identificeert, krijgt u inzicht in de kosten door de afzonderlijke transacties te analyseren die aan de kosten hebben bijgedragen. Op dit moment worden openstaande gebruikskosten niet weergegeven op de pagina Alle transacties. U kunt de openstaande gebruikskosten weergeven op de Azure-abonnementenpagina. Voor meer informatie, zie [Openstaande gebruikskosten weergeven](#view-pending-usage-charges)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van zoekopdracht in de Azure-portal naar kostenbeheer en facturering](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount selecteren. Vanuit het factureringsaccount selecteert u **Factureringsprofielen** en vervolgens een factureringsprofiel.

4. Selecteer **Alle transacties** aan de linkerkant van de pagina.

5. Zoek op *Openstaand*. Gebruik de filter **Periode** om de openstaande kosten voor de huidige of vorige maand weer te geven.

   ![Schermopname van lijst met openstaande transacties](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Openstaande gebruikskosten weergeven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoek naar *Kostenbeheer en facturering*.

   ![Schermopname van zoekopdracht in de Azure-portal naar kostenbeheer en facturering](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount selecteren. Vanuit het factureringsaccount selecteert u **Factureringsprofielen** en vervolgens een factureringsprofiel.

4. Selecteer **Alle abonnementen** aan de linkerzijde van de pagina.

5. De pagina Azure-abonnementen geeft de kosten voor elk abonnement voor de huidige en vorige maand weer in het factureringsprofiel. De maandkosten tot heden zijn de openstaande kosten voor de huidige maand en worden gefactureerd wanneer de factuur voor deze maand wordt gegenereerd. Als de factuur van de vorige maand nog steeds niet is gegenereerd, dan zijn de kosten van vorige maand ook openstaand.

    ![Schermopname van lijst met Azure-abonnementen voor het factureringsprofiel](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Uw Azure-gebruikskosten analyseren

Gebruik het CSV-bestand voor Azure-gebruik en -kosten om uw kosten op gebruiksbasis te analyseren. U kunt het bestand downloaden voor een factuur of voor openstaande kosten. Voor meer informatie, zie [Uw Azure-factuur en dagelijkse gebruiksgegevens ophalen](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Gedetailleerd gebruik per factuursectie weergeven

U kunt het bestand voor Azure-gebruik en -kosten filteren om de gebruikskosten voor uw factuursecties af te stemmen.

De volgende stappen leiden u door het afstemmen van de berekeningskosten voor de factuursectie Administratieafdeling:

![Voorbeeldafbeelding met gegevens gesorteerd per factuursectie](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Factuur (PDF) | CSV met Azure-gebruik en -kosten |
 | --- | --- |
 |Administratieafdeling |invoiceSectionName |
 |Gebruikskosten - Microsoft Azure Plan |productOrderName |
 |Compute |serviceFamily |

1. Filter de kolom **invoiceSectionName** in het CSV-bestand op  **Administratieafdeling**.
2. Filter de kolom **productOrderName** in het CSV-bestand op **Microsoft Azure Plan**.
3. Filter de kolom **serviceFamily** in het CSV-bestand op **Microsoft.Compute**.

![Schermopname van gebruiks- en kostenbestand gefilterd op factuursectie](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Gedetailleerd gebruik per abonnement weergeven

U kunt het CSV-bestand met Azure-gebruik en -kosten filteren om de gebruikskosten voor uw abonnementen af te stemmen. Als u alle abonnementen in een factureringsprofiel wilt weergeven, raadpleegt u [Openstaande gebruikskosten weergeven ](#view-pending-usage-charges).

Wanneer u de kosten voor een abonnement identificeert, gebruikt u het CSV-bestand met Azure-gebruik en -kosten om de kosten te analyseren.

De volgende afbeelding toont de lijst met abonnementen in de Azure-portal.

![Schermopname van lijst met Azure-abonnementen voor het factureringsprofiel](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Filter de kolom **subscriptionName** in het CSV-bestand met Azure-gebruik en -kosten op **WA_abonnement** om de gedetailleerde gebruikskosten voor WA_abonnement weer te geven.

![Schermopname van gebruiks- en kostenbestand gefilterd op abonnement](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Uw factuur betalen

Instructies voor het betalen van uw factuur staan onderaan de factuur. [Meer informatie over betalen](billing-mca-understand-your-invoice.md#how-to-pay).

Als u uw factuur al hebt betaald, kunt u de status van de betaling controleren op de pagina Facturen in de Azure-portal.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over uw factuur en gedetailleerde gebruik, zie:

- [Uw Azure-factuur en de dagelijkse gebruiksgegevens ophalen](billing-download-azure-invoice-daily-usage-date.md)
- [Toelichting van de begrippen in de factuur voor uw Microsoft-klantovereenkomst](billing-mca-understand-your-invoice.md)
- [Toelichting van de begrippen in het CSV-bestand voor uw Microsoft-klantovereenkomst](billing-mca-understand-your-usage.md)
