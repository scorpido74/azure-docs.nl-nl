---
title: EA-taken in Microsoft-klantovereenkomst - Azure
description: Lees hoe u Enterprise Agreement-taken kunt uitvoeren in uw nieuwe factureringsaccount.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: banders
ms.openlocfilehash: 6692bb8f323e42edfe546e15620442881f99032d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78272189"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Enterprise Agreement-taken uitvoeren in uw factureringsaccount voor een Microsoft-klantovereenkomst

Als uw organisatie een Microsoft-klantovereenkomst heeft ondertekend om uw Enterprise Agreement-inschrijving te vernieuwen, wordt er een nieuw factureringsaccount gemaakt voor de overeenkomst. De facturering in uw nieuwe account is anders ingedeeld dan in uw Enterprise Agreement. In dit artikel wordt beschreven hoe u het nieuwe factureringsaccount kunt gebruiken voor het uitvoeren van taken die eerder in uw Enterprise Agreement werden uitgevoerd.

## <a name="billing-organization-in-the-new-account"></a>Overzicht van facturering in het nieuwe account

In het volgende diagram wordt beschreven hoe facturering is georganiseerd in het nieuwe factureringsaccount.

![Afbeelding van ea-mca-post-transition-hierarchy](./media/mca-enterprise-operations/mca-post-transition-hierarchy.png)

| Enterprise Agreement   | Microsoft-klantovereenkomst    |
|------------------------|--------------------------------------------------------|
| Registratie            | U gebruikt een factureringsprofiel om de facturering voor uw organisatie te beheren, vergelijkbaar met uw Enterprise Agreement-inschrijving. Ondernemingsbeheerders worden eigenaar van het factureringsprofiel. Raadpleeg [Factureringsprofielen](../understand/mca-overview.md#billing-profiles) voor meer informatie over factureringsprofielen.
| Afdeling            | U gebruikt een factuursectie om uw kosten te ordenen, vergelijkbaar met afdelingen in uw Enterprise Agreement-inschrijving. Afdelingen worden factuursecties en afdelingsbeheerders worden eigenaar van de respectieve factuursecties. Raadpleeg [Factuursecties](../understand/mca-overview.md#invoice-sections) voor meer informatie over factuursecties. |
| Account               | De accounts die in uw Enterprise Agreement zijn gemaakt, worden niet ondersteund in de nieuwe factureringsrekening. De abonnementen van het account horen bij de respectieve factuursectie voor hun afdeling. Accounteigenaren kunnen abonnementen voor hun factuursecties maken en beheren. |

## <a name="changes-for-enterprise-administrators"></a>Wijzigingen voor ondernemingsbeheerders

De volgende wijzigingen zijn van toepassing op ondernemingsbeheerders voor een Enterprise Agreement die is vernieuwd en omgezet in een Microsoft-klantovereenkomst.

- Er wordt een factureringsprofiel gemaakt voor uw inschrijving. U gebruikt het factureringsprofiel om de facturering voor uw organisatie te beheren, vergelijkbaar met uw Enterprise Agreement-inschrijving. Raadpleeg [Informatie over factureringsprofielen](../understand/mca-overview.md#billing-profiles) voor meer informatie over factureringsprofielen.
- Er wordt een factuursectie gemaakt voor elke afdeling in uw Enterprise Agreement-inschrijving. U gebruikt de factuursecties om uw afdelingen te beheren. U kunt nieuwe factuursecties maken om extra afdelingen in te stellen. Raadpleeg [Informatie over factuursecties](../understand/mca-overview.md#invoice-sections) voor meer informatie over factuursecties.
- U gebruikt de rol Maker van Azure-abonnement voor factuursecties om anderen toestemming te geven om een Azure-abonnement te maken, zoals de accounts die worden gemaakt tijdens de inschrijving bij Enterprise Agreement.
- U gebruikt de [Azure-portal](https://portal.azure.com) voor het beheren van facturering voor uw organisatie, in plaats van de Azure EA-portal.

U krijgt de volgende rollen voor het nieuwe factureringsaccount:

**Eigenaar van factureringsprofiel**: deze rol wordt aan u toegewezen in het factureringsprofiel dat is gemaakt toen de overeenkomst werd ondertekend. Met de rol kunt u de facturering voor uw organisatie beheren. U kunt kosten en facturen inzien, kosten op de factuur organiseren, betalingswijzen beheren en de toegang tot de facturering van uw organisatie instellen.

**Factuursectie voor eigenaar**: deze rol wordt aan u toegewezen voor alle factuursecties die worden gemaakt voor de afdelingen in uw Enterprise Agreement-inschrijving. Met de rol kunt u bepalen wie Azure-abonnementen kan maken en andere producten kan kopen.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Kosten en tegoedsaldo voor uw organisatie weergeven

U gebruikt uw factureringsprofiel voor het bijhouden van kosten en het Azure-tegoedsaldo voor uw organisatie, vergelijkbaar met uw Enterprise Agreement-inschrijving.

Zie [Saldo van Azure-tegoed voor Microsoft-klantovereenkomst bijhouden](mca-check-azure-credits-balance.md) voor informatie over het weergeven van het tegoedsaldo voor een factureringsprofiel.

Zie [Informatie over de kosten op uw factuur voor Microsoft-klantovereenkomst](../understand/review-customer-agreement-bill.md) voor informatie over het weergeven van kosten voor een factureringsprofiel.

### <a name="view-charges-for-a-department"></a>Kosten voor een afdeling weergeven

Er wordt een factuursectie gemaakt voor elke afdeling uit uw Enterprise Agreement. U kunt de kosten voor een factuursectie bekijken in de Azure-portal. Voor meer informatie, zie [Transacties per factuursectie weergeven](../understand/review-customer-agreement-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Kosten voor een account weergeven

De accounts die zijn gemaakt in uw Enterprise Agreement-inschrijving worden niet ondersteund in het nieuwe factureringsaccount. De abonnementen van het account horen bij de respectieve factuursectie voor hun afdeling. Accounteigenaren kunnen abonnementen voor hun factuursecties maken en beheren.

Als u cumulatieve kosten wilt weergeven voor abonnementen die bij een account horen, moet u voor elk abonnement een kostenplaats instellen. Vervolgens kunt u het CSV-bestand met gebruiksgegevens en kosten van Azure gebruiken om de abonnementen te filteren op kostenplaats.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>CSV-bestand met gebruiksgegevens en kosten, prijzenoverzicht en belastingdocumenten downloaden

Voor elk factureringsprofiel in uw factureringsaccount wordt een maandelijkse factuur gegenereerd. Voor elke factuur kunt u een CSV-bestand met gebruiksgegevens en kosten van Azure, het prijzenoverzicht en belastingdocumenten (indien van toepassing) downloaden. U kunt ook een CSV-bestand met deze gegevens downloaden voor de kosten van de huidige maand.

Zie [Gebruiksgegevens voor uw Microsoft-klantovereenkomst downloaden](../understand/download-azure-daily-usage.md) als u wilt weten hoe u het CSV-bestand met gebruiksgegevens en kosten van Azure kunt downloaden.

Zie [Prijzenoverzichten voor de huidige factureringsperiode downloaden](ea-pricing.md) als u wilt weten hoe u een prijzenoverzicht kunt downloaden.

Zie [Belastingdocumenten voor uw Microsoft-klantovereenkomst weergeven](../understand/mca-download-tax-document.md#view-and-download-tax-documents) als u wilt weten hoe u belastingdocumenten kunt downloaden.

### <a name="add-an-additional-enterprise-administrator"></a>Een extra ondernemingsbeheerder toevoegen

Geef gebruikers toegang tot het factureringsprofiel zodat ze facturering voor uw organisatie kunnen bekijken en beheren. U kunt de pagina **Access Control (IAM)** in de Azure-portal gebruiken om toegang te verlenen.  Zie [Rollen en taken voor factureringsprofiel](understand-mca-roles.md#billing-profile-roles-and-tasks) voor meer informatie over rollen voor een factureringsprofiel.

Zie [Factureringsrollen beheren in de Azure-portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)voor meer informatie over het bieden van toegang tot uw factureringsprofiel.

### <a name="create-a-new-department"></a>Een nieuwe afdeling maken

Maak een factuursectie om uw kosten te ordenen op basis van uw behoeften, net als afdelingen in uw Enterprise Agreement-inschrijving. U kunt een nieuwe factuursectie maken in de Azure-portal. Zie [Secties maken in een factuur voor het ordenen van kosten](mca-section-invoice.md) voor meer informatie.

### <a name="create-a-new-account"></a>Een nieuw account maken

Geef gebruikers de rol Maker van Azure-abonnement voor factuursecties om ze toestemming te geven om een Azure-abonnement te maken, zoals de accounts die worden gemaakt tijdens de inschrijving bij Enterprise Agreement. Raadpleeg [Factureringsrollen beheren in de Azure-portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal) voor meer informatie over het toewijzen van rollen

## <a name="changes-for-department-administrators"></a>Wijzigingen voor afdelingsbeheerders

De volgende wijzigingen zijn van toepassing op afdelingsbeheerders voor een Enterprise Agreement die is vernieuwd en omgezet in een Microsoft-klantovereenkomst.

- Er wordt een factuursectie gemaakt voor elke afdeling in uw Enterprise Agreement-inschrijving. U gebruikt een of meer factuursecties om uw afdeling of afdelingen te beheren. Raadpleeg [Informatie over factuursecties](../understand/mca-overview.md#invoice-sections) voor meer informatie over factuursecties.
- U gebruikt de rol Maker van Azure-abonnement voor de factuursectie om anderen toestemming te geven om een Azure-abonnement te maken, zoals de accounts die worden gemaakt tijdens de inschrijving bij Enterprise Agreement.
- U gebruikt de Azure-portal voor het beheren van facturering voor uw organisatie, in plaats van de Azure EA-portal.

U krijgt de volgende rollen voor het nieuwe factureringsaccount:

**Factuursectie voor eigenaar**: deze rol wordt aan u toegewezen voor de factuursectie die wordt gemaakt voor de afdeling of afdelingen uit uw Enterprise Agreement-inschrijving. Met de rol kunt u kosten bekijken en bijhouden, en bepalen wie Azure-abonnementen kan maken en andere producten kan kopen voor de factuursectie.

### <a name="view-charges-for-your-department"></a>Kosten voor uw afdeling weergeven

U kunt kosten weergeven voor de factuursectie die voor uw afdeling is gemaakt. Ga hiervoor naar de pagina [Kostenbeheer en facturering](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) in de Azure-portal.

### <a name="add-an-additional-department-administrator"></a>Een extra afdelingsbeheerder toevoegen

Er wordt een factuursectie gemaakt voor elke afdeling uit uw Enterprise Agreement. U kunt de pagina **Access Control (IAM)** in de Azure-portal gebruiken om anderen toestemming te geven de factuursectie weer te geven en te beheren. Zie [Rollen en taken voor factuursectie](understand-mca-roles.md#invoice-section-roles-and-tasks)voor meer informatie over rollen voor de factuursectie.

Zie [Factureringsrollen beheren in de Azure-portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)voor meer informatie over het bieden van toegang tot de factuursectie.

### <a name="create-a-new-account-in-your-department"></a>Een nieuw account maken in uw afdeling

Wijs de rol Maker van Azure-abonnement toe aan gebruikers voor de factuursectie die voor uw afdeling is gemaakt. Raadpleeg [Factureringsrollen beheren in de Azure-portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal) voor meer informatie over het toewijzen van rollen

### <a name="view-charges-for-accounts-in-your-departments"></a>Kosten voor accounts in uw afdelingen weergeven

De accounts die zijn gemaakt in uw Enterprise Agreement-inschrijving worden niet ondersteund in het nieuwe factureringsaccount. De abonnementen van het account horen bij de respectieve factuursectie voor hun afdeling. Accounteigenaren kunnen abonnementen voor hun factuursecties maken en beheren.

Als u cumulatieve kosten wilt weergeven voor abonnementen die bij een account in uw afdeling horen, moet u voor elk abonnement een kostenplaats instellen. Vervolgens kunt u het bestand met gebruiksgegevens en kosten van Azure gebruiken om de abonnementen te filteren op kostenplaats.

## <a name="changes-for-account-owners"></a>Wijzigingen voor eigenaren van accounts

Accounteigenaren van de Enterprise Agreement krijgen toestemming om Azure-abonnementen te maken voor het nieuwe factureringsaccount. Uw bestaande Azure-abonnementen horen bij de factuursectie die voor uw afdeling wordt gemaakt. Als uw account niet bij een afdeling hoort, horen uw abonnementen bij een factuursectie met de naam Standaardfactuursectie.

Als u aanvullende Azure-abonnementen wilt maken, krijgt u de volgende rol voor het nieuwe factureringsaccount.

**Maker van Azure-abonnement**: u krijgt deze rol voor de factuursectie die voor uw afdeling wordt gemaakt in Enterprise Agreement. Als uw account geen deel uitmaakt van een afdeling, krijgt u de rol van Maker van Azure-abonnement voor een sectie met de naam Standaardfactuursectie. Met de rol kunt u Azure-abonnementen maken voor de factuursectie.

### <a name="create-an-azure-subscription"></a>Een Azure-abonnement maken

U kunt in de Azure-portal Azure-abonnementen maken voor uw factuursectie. Zie [Een extra Azure-abonnement voor Microsoft-klantovereenkomst maken](create-subscription.md) voor meer informatie.

### <a name="view-charges-for-your-account"></a>Kosten voor uw account weergeven

Als u de kosten wilt weergeven voor abonnementen die bij een account horen, gaat u naar de pagina [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure-portal. Op deze pagina worden kosten voor al uw abonnementen weergegeven.

### <a name="view-charges-for-a-subscription"></a>Kosten voor een abonnement weergeven

U kunt de kosten voor een abonnement bekijken op de pagina [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) of in Azure Kostenanalyse. Zie [Kosten verkennen en analyseren met Kostenanalyse](../costs/quick-acm-cost-analysis.md) voor meer informatie over Azure Kostenanalyse.

## <a name="need-help-contact-support"></a>Hulp nodig? Contact opnemen met ondersteuning

Als u hulp nodig hebt, kunt u [contact opnemen met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met uw factureringsaccount voor Microsoft-klantovereenkomst](../understand/mca-overview.md)
- [Meer informatie over uw factuur voor Microsoft Azure](../understand/review-individual-bill.md)
- [Meer informatie over uw factuur](../understand/understand-invoice.md)
- [Eigendom van facturering van Azure-abonnementen van andere accounts krijgen](mca-request-billing-ownership.md)
