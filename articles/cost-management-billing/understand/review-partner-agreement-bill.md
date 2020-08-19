---
title: Uw factuur voor uw Microsoft-partnerovereenkomst controleren - Azure
description: Lees hoe u uw factuur en het resourceverbruik controleert, evenals kosten voor uw Microsoft Partnerovereenkomst.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: banders
ms.openlocfilehash: 9e141b0cdeeb03882a0baa032f52bc6e451967c8
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185227"
---
# <a name="tutorial-review-your-microsoft-partner-agreement-invoice"></a>Zelfstudie: Uw factuur voor uw Microsoft-partnerovereenkomst controleren

 In de factureringsrekening voor een Microsoft Partner-overeenkomst wordt elke maand een factuur voor elk factureringsprofiel gegenereerd. De factuur bevat alle klantkosten van de vorige maand. U krijgt meer inzicht in de kosten op uw factuur door de afzonderlijke transacties te analyseren in de Azure-portal. U kunt ook uw facturen weergeven in de Azure-portal en de kosten vergelijken met het bestand met gebruiksgegevens.

Zie [hoe u facturen kunt downloaden vanuit de Azure-portal ](download-azure-invoice.md) voor meer informatie.

Deze zelfstudie is alleen van toepassing op Azure-partners met een Microsoft Partner-overeenkomst.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gefactureerde transacties controleren in de Azure-portal
> * Bekijk de openstaande kosten om een schatting te maken van uw volgende factuur
> * Uw Azure-gebruikskosten analyseren

## <a name="prerequisites"></a>Vereisten

Toegang tot een factureringsaccount voor een Microsoft Partner-overeenkomst.

Het moet meer dan 30 dagen geleden zijn dat u zich geabonneerd hebt op Azure. Azure factureert u aan het einde van uw factureringsperiode.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

- Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren

Controleer het type overeenkomst om te bepalen of u toegang hebt tot een factureringsrekening voor een Microsoft Partner-overeenkomst.

Typ in de Azure-portal *kostenbeheer en facturering* in het zoekvak en selecteer vervolgens **Kostenbeheer en facturering**.

![Schermopname van de zoekopdracht in de Azure-portal](./media/review-partner-agreement-bill/billing-search-cost-management-billing.png)

Als u toegang hebt tot slechts één factureringsbereik, selecteert u **Eigenschappen** aan de linkerkant. U hebt toegang tot een factureringsaccount voor een Microsoft Partner-overeenkomst als het type factureringsrekening **Microsoft Partner-overeenkomst** is.

![Schermopname van de Microsoft Partner-overeenkomst op de pagina Eigenschappen](./media/review-partner-agreement-bill/billing-account-properties-partner-agreement.png)

Als u toegang hebt tot meerdere factureringsbereiken, controleert u het type in de kolom voor de factureringsrekening. U hebt toegang tot een factureringsrekening voor een Microsoft Partner-overeenkomst als het type factureringsaccount voor een of meer van de bereiken **Microsoft Partner-overeenkomst**.

![Schermopname met Microsoft Partner-overeenkomst op de pagina met een lijst van factureringsaccounts](./media/review-partner-agreement-bill/mpa-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Gefactureerde transacties controleren in de Azure-portal

Selecteer in Kostenbeheer en facturering **Alle transacties** aan de linkerkant van de pagina. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount, een factureringsprofiel of een klant selecteren en vervolgens **Alle transacties**.

De pagina Alle transacties geeft de volgende informatie weer:

![Schermopname van lijst met gefactureerde transacties](./media/review-partner-agreement-bill/all-transactions.png)

|Kolom  |Definitie  |
|---------|---------|
|Date     | Transactiedatum  |
|Factuur-id     | De identificatie van de factuur waarop de transactie is gefactureerd. Als u een ondersteuningsaanvraag indient, deelt u deze id met de Azure-ondersteuning om uw ondersteuningsaanvraag te versnellen |
|Transactietype     |  Het type transactie, bijvoorbeeld aankoop-, annulerings- of gebruikskosten  |
|Productfamilie     | De categorie van producten, zoals berekening voor virtuele machines of database voor Azure SQL Database|
|Product-SKU     | Een unieke code die het exemplaar van uw product identificeert |
|Aantal     |  Het aantal transacties      |
|Factureringsprofiel     | De transactie wordt weergegeven in deze factuur van het factureringsprofiel |

Zoek op factuur-id om de transacties voor de betreffende factuur te filteren.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Bekijk de openstaande kosten om een schatting te maken van uw volgende factuur

De kosten worden geschat en als openstaande kosten beschouwd totdat ze worden gefactureerd. U kunt openstaande kosten voor het factureringsprofiel voor uw Microsoft Partner-overeenkomst bekijken in de Azure-portal om uw volgende factuur in te calculeren. De openstaande kosten zijn een schatting en zijn exclusief belasting. De werkelijke kosten op uw volgende factuur zullen daarom afwijken van de openstaande kosten.

### <a name="view-pending-transactions"></a>Openstaande transacties weergeven

Wanneer u de openstaande kosten identificeert, krijgt u inzicht in de kosten door de afzonderlijke transacties te analyseren die aan de kosten hebben bijgedragen. Op dit moment worden openstaande gebruikskosten niet weergegeven op de pagina Alle transacties. U kunt de openstaande gebruikskosten weergeven op de Azure-abonnementenpagina.

Selecteer een factureringsprofiel in Kostenbeheer en facturering. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount selecteren. Vanuit het factureringsaccount selecteert u **Factureringsprofielen** en vervolgens een factureringsprofiel.

Selecteer **Alle transacties** aan de linkerkant van de pagina.

Zoek op *Openstaand*. Gebruik de filter **Periode** om de openstaande kosten voor de huidige of vorige maand weer te geven.

<!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Openstaande kosten per klant weergeven

Selecteer een factureringsprofiel in Kostenbeheer en facturering. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount selecteren. Vanuit het factureringsaccount selecteert u **Factureringsprofielen** en vervolgens een factureringsprofiel.

Selecteer **Klanten** links op de pagina.

<!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

Op de pagina Klanten worden de kosten voor de huidige en vorige maand weergegeven voor elke klant die is gekoppeld aan het factureringsprofiel. De maandkosten tot heden zijn de openstaande kosten voor de huidige maand en worden gefactureerd wanneer de factuur voor deze maand wordt gegenereerd. Als de factuur van de vorige maand nog steeds niet is gegenereerd, dan zijn de kosten van vorige maand ook openstaand.

### <a name="view-pending-usage-charges"></a>Openstaande gebruikskosten weergeven

Selecteer een factureringsprofiel in Kostenbeheer en facturering. Afhankelijk van uw toegang moet u mogelijk een factureringsaccount selecteren. Vanuit het factureringsaccount selecteert u **Factureringsprofielen** en vervolgens een factureringsprofiel.

Selecteer **Azure-abonnementen** links op de pagina. De pagina Azure-abonnementen geeft de kosten voor elk abonnement voor de huidige en vorige maand weer in het factureringsprofiel. De maandkosten tot heden zijn de openstaande kosten voor de huidige maand en worden gefactureerd wanneer de factuur voor deze maand wordt gegenereerd. Als de factuur van de vorige maand nog steeds niet is gegenereerd, dan zijn de kosten van vorige maand ook openstaand.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Uw Azure-gebruikskosten analyseren

Gebruik het CSV-bestand voor Azure-gebruik en -kosten om uw kosten op gebruiksbasis te analyseren. U kunt het bestand met Azure-gebruiksgegevens en -kosten filteren om de gebruikskosten voor elk product in de PDF-factuur af te stemmen. Als u gedetailleerde gebruikskosten voor een bepaald product wilt weergeven, filtert u de kolom **product** in het CSV-bestand met Azure-gebruiksgegevens en -kosten zo dat alleen de naam van dat product wordt opgenomen.

U kunt ook de kolom **customerName** in het CSV-bestand met Azure-gebruiksgegevens en -kosten filteren om de dagelijkse gebruikskosten per klant te bekijken. Als u de dagelijkse gebruikskosten per Azure-abonnement wilt weergeven, filtert u de kolom **subscriptionName**.

## <a name="pay-your-bill"></a>Uw factuur betalen

Instructies voor het betalen van uw factuur staan onderaan de factuur. U kunt binnen 60 dagen na de factuurdatum betalen per directe overboeking of cheque.

Als u uw factuur al hebt betaald, kunt u de status van de betaling controleren op de pagina Facturen in de Azure-portal.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Gefactureerde transacties controleren in de Azure-portal
> * Bekijk de openstaande kosten om een schatting te maken van uw volgende factuur
> * Uw Azure-gebruikskosten analyseren

Lees meer over Azure Cost Management voor partners.

> [!div class="nextstepaction"]
> [Aan de slag met Azure Cost Management voor partners](../costs/get-started-partners.md)
