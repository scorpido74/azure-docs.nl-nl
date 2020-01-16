---
title: Bekijk de Azure-factuur van uw micro soft-klant overeenkomst
description: Meer informatie over het gebruik van het factuur-en Resource verbruik en voor het verifiëren van de kosten voor uw klant overeenkomst van micro soft.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: 3cbc4ab4f0e2ad18c7d1d430ed5501e23b5f5f4d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987066"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>Zelf studie: uw factuur voor micro soft-klant overeenkomst controleren

U kunt de kosten op uw factuur controleren door de afzonderlijke trans acties te analyseren. In het factureringsaccount voor een Microsoft-klantovereenkomst wordt elke maand een factuur voor elk factureringsprofiel gegenereerd. De factuur bevat alle kosten van de vorige maand. U kunt uw facturen weer geven in de Azure Portal en de kosten vergelijken met het detail bestand voor gebruik.

Deze zelf studie is alleen van toepassing op Azure-klanten met een klant overeenkomst van micro soft.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gefactureerde trans acties in de Azure Portal controleren
> * Bekijk de openstaande kosten om een schatting te maken van uw volgende factuur
> * Uw Azure-gebruikskosten analyseren

## <a name="prerequisites"></a>Vereisten

U moet een facturerings account hebben voor een klant overeenkomst van micro soft.

U moet toegang hebben tot een klant overeenkomst van micro soft. U moet een facturerings profiel eigenaar, bijdrager, lezer of factuur beheerder zijn om factuur-en gebruiks gegevens weer te geven. Zie [Rollen en taken voor factureringsprofielen](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks) voor meer informatie over factureringsrollen voor Microsoft-klantovereenkomsten.

Deze moet meer dan 30 dagen na de dag waarop u bent geabonneerd op Azure. Azure factureert u aan het einde van uw factureringsperiode.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

- Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren

Controleer het type overeenkomst om te bepalen of u toegang hebt tot een factureringsaccount voor een Microsoft-klantovereenkomst.

Typ in het Azure Portal *kosten beheer en facturering* in het zoekvak en selecteer vervolgens **Cost Management + facturering**.

![Schermopname van de zoekopdracht in Azure Portal naar kostenbeheer en facturering](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

Als u toegang hebt tot slechts één factureringsbereik, selecteert u **Eigenschappen** aan de linkerkant. U hebt toegang tot een factureringsaccount voor een Microsoft-klantovereenkomst als het type factureringsaccount **Microsoft-klantovereenkomst** is.

![Scherm opname van de micro soft-klant overeenkomst op de pagina eigenschappen](./media/review-customer-agreement-bill/billing-mca-property.png)

Als u toegang hebt tot meerdere factureringsbereiken, controleert u het type in de kolom voor de factureringsrekening. U hebt toegang tot een factureringsaccount voor een Microsoft-klantovereenkomst als het type factureringsaccount voor een of meer van de bereiken **Microsoft-klantovereenkomst** is.

![Scherm afbeelding waarin de micro soft-klant overeenkomst wordt weer gegeven in de lijst pagina met facturerings accounts](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Gefactureerde trans acties in de Azure Portal controleren

Selecteer in het Azure Portal **alle trans acties** aan de linkerkant van de pagina. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount, een factureringsprofiel of een factuursectie selecteren en vervolgens **Alle transacties**.

De pagina Alle transacties geeft de volgende informatie weer:

![Schermopname van lijst met gefactureerde transacties](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|Kolom  |Definitie  |
|---------|---------|
|Datum     | Transactiedatum  |
|Factuur-id     | De identificatie van de factuur waarop de transactie is gefactureerd. Als u een ondersteuningsaanvraag indient, deelt u deze id met de Azure-ondersteuning om uw ondersteuningsaanvraag te versnellen |
|Transactietype     |  Het type transactie, bijvoorbeeld aankoop-, annulerings- of gebruikskosten  |
|Productfamilie     | De categorie van producten, zoals berekening voor virtuele machines of database voor Azure SQL-database|
|Product-sku     | Een unieke code die het exemplaar van uw product identificeert |
|Bedrag     |  Het aantal transacties      |
|Factuursectie     | De transactie wordt weergegeven in deze factuursectie van het factureringsprofiel |
|Factureringsprofiel     | De transactie wordt weergegeven in deze factuur van het factureringsprofiel |

Zoek naar een factuur-ID om de trans acties voor de factuur te filteren.

### <a name="view-transactions-by-invoice-sections"></a>Transacties per factuursectie weergeven

Factuursecties helpen u bij het organiseren van de factuurkosten voor een factureringsprofiel. Voor meer informatie. Wanneer er een factuur wordt gegenereerd, worden de kosten voor alle secties in het factureringsprofiel weergegeven op de factuur.

De volgende afbeelding toont de factuurkosten voor de Administratieafdeling op een voorbeeldfactuur.

![Voorbeeldafbeelding met gegevens gesorteerd per factuursectie](./media/review-customer-agreement-bill/invoicesection-details.png)

Wanneer u de kosten voor een factuursectie hebt geïdentificeerd, kunt u de transacties in de Azure-portal weergeven voor meer inzicht in de kosten.

Ga naar de pagina Alle transacties in de Azure-portal om transacties voor een factuur weer te geven.

Filter op de naam van de factuursectie om de transacties weer te geven.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Bekijk de openstaande kosten om een schatting te maken van uw volgende factuur

In het facturerings account voor een micro soft-klant overeenkomst worden kosten geschat en beschouwd als in behandeling tot ze worden gefactureerd. U kunt de openstaande kosten in de Azure-portal weergeven om een schatting te maken van uw volgende factuur. De openstaande kosten zijn een schatting en zijn exclusief belasting. De werkelijke kosten op uw volgende factuur zullen daarom afwijken van de openstaande kosten.

### <a name="view-summary-of-pending-charges"></a>Overzicht van openstaande kosten weergeven

Meld u aan bij de [Azure Portal](https://portal.azure.com).

Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount selecteren. Vanuit het factureringsaccount selecteert u **Factureringsprofielen** en vervolgens een factureringsprofiel.

Selecteer het tabblad **samen vatting** van de bovenkant van het scherm.

De kostensectie geeft de maandkosten tot heden en de kosten van vorige maand weer.

![Schermopname van de zoekopdracht in Azure Portal naar kostenbeheer en facturering](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

De maandkosten tot heden zijn de openstaande kosten voor de huidige maand en worden gefactureerd wanneer de factuur voor deze maand wordt gegenereerd. Als de factuur van de vorige maand nog steeds niet is gegenereerd, dan zijn de kosten van vorige maand ook openstaand en verschijnen zij op uw volgende factuur.

### <a name="view-pending-transactions"></a>Openstaande transacties weergeven

Wanneer u de openstaande kosten identificeert, krijgt u inzicht in de kosten door de afzonderlijke transacties te analyseren die aan de kosten hebben bijgedragen. Op dit moment worden openstaande gebruikskosten niet weergegeven op de pagina Alle transacties. U kunt de openstaande gebruikskosten weergeven op de Azure-abonnementenpagina.

Meld u aan bij de [Azure Portal](https://portal.azure.com).

Typ in het Azure Portal *kosten beheer en facturering* in het zoekvak en selecteer vervolgens **Cost Management + facturering**.

Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount selecteren. Vanuit het factureringsaccount selecteert u **Factureringsprofielen** en vervolgens een factureringsprofiel.

Selecteer **Alle transacties** aan de linkerkant van de pagina.

Zoek op *Openstaand*. Gebruik de filter **Periode** om de openstaande kosten voor de huidige of vorige maand weer te geven.

![Schermopname van lijst met openstaande transacties](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Openstaande gebruikskosten weergeven

Meld u aan bij de [Azure Portal](https://portal.azure.com).

Typ in het Azure Portal *kosten beheer en facturering* in het zoekvak en selecteer vervolgens **Cost Management + facturering**.

Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount selecteren. Vanuit het factureringsaccount selecteert u **Factureringsprofielen** en vervolgens een factureringsprofiel.

Selecteer **Alle abonnementen** aan de linkerzijde van de pagina.

De pagina Azure-abonnementen geeft de kosten voor elk abonnement voor de huidige en vorige maand weer in het factureringsprofiel. De maandkosten tot heden zijn de openstaande kosten voor de huidige maand en worden gefactureerd wanneer de factuur voor deze maand wordt gegenereerd. Als de factuur van de vorige maand nog steeds niet is gegenereerd, dan zijn de kosten van vorige maand ook openstaand.

![Schermopname van lijst met Azure-abonnementen voor het factureringsprofiel](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Uw Azure-gebruikskosten analyseren

Gebruik het CSV-bestand voor Azure-gebruik en -kosten om uw kosten op gebruiksbasis te analyseren. U kunt het bestand downloaden voor een factuur of voor openstaande kosten.

### <a name="download-your-invoice-and-usage-details"></a>Uw factuur-en gebruiks gegevens downloaden

Afhankelijk van uw toegang moet u mogelijk een facturerings account of facturerings profiel in Cost Management en facturering selecteren. Selecteer **Facturen** onder **Facturering** in het menu aan de linkerkant. Zoek in het factuurraster de rij van de factuur die u wilt downloaden. Klik op het Download symbool of het weglatings teken (...) aan het einde van de rij. Down load het bestand met gebruiks gegevens en de factuur in het **Download** venster.

### <a name="view-detailed-usage-by-invoice-section"></a>Gedetailleerd gebruik per factuursectie weergeven

U kunt het bestand voor Azure-gebruik en -kosten filteren om de gebruikskosten voor uw factuursecties af te stemmen.

De volgende informatie helpt u bij het afstemmen van de reken kosten voor de afdeling factuur van de afdeling boek houding:

![Voorbeeldafbeelding met gegevens gesorteerd per factuursectie](./media/review-customer-agreement-bill/invoicesection-details.png)

| Factuur (PDF) | CSV met Azure-gebruik en -kosten |
| --- | --- |
|Administratieafdeling |invoiceSectionName |
|Gebruikskosten - Microsoft Azure Plan |productOrderName |
|Computing |serviceFamily |

Filter de kolom **invoiceSectionName** in het CSV-bestand op **Accounting afdeling**. Filter vervolgens de kolom **productOrderName** in het CSV-bestand om de **planning te Microsoft Azure**. Filter vervolgens de kolom **serviceFamily** in het CSV-bestand naar **micro soft. Compute**.

![Schermopname van gebruiks- en kostenbestand gefilterd op factuursectie](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>Gedetailleerd gebruik per abonnement weergeven

U kunt het CSV-bestand met Azure-gebruik en -kosten filteren om de gebruikskosten voor uw abonnementen af te stemmen. Wanneer u de kosten voor een abonnement identificeert, gebruikt u het CSV-bestand met Azure-gebruik en -kosten om de kosten te analyseren.

De volgende afbeelding toont de lijst met abonnementen in de Azure-portal.

![Schermopname van lijst met Azure-abonnementen voor het factureringsprofiel](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

Filter de kolom **subscriptionName** in het CSV-bestand met Azure-gebruik en -kosten op **WA_abonnement** om de gedetailleerde gebruikskosten voor WA_abonnement weer te geven.

![Schermopname van gebruiks- en kostenbestand gefilterd op abonnement](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Uw factuur betalen

Instructies voor het betalen van uw factuur staan onderaan de factuur. [Meer informatie over betalen](mca-understand-your-invoice.md#how-to-pay).

Als u uw factuur al hebt betaald, kunt u de status van de betaling controleren op de pagina Facturen in de Azure-portal.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Gefactureerde trans acties in de Azure Portal controleren
> * Bekijk de openstaande kosten om een schatting te maken van uw volgende factuur
> * Uw Azure-gebruikskosten analyseren

Voltooi de Snelstartgids om te beginnen met het gebruik van kosten analyse.

> [!div class="nextstepaction"]
> [Beginnen met kosten analyseren](../costs/quick-acm-cost-analysis.md)
