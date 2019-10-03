---
title: Uw factureringsaccounts weergeven in de Azure-portal | Microsoft Docs
description: Meer informatie over het weergeven van uw factureringsaccounts in de Azure-portal.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 6df787dbc06f7ee56ba9a26c8382396bf3b6e74a
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719605"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Factureringsaccounts in de Azure-portal weergeven  

Er wordt een factureringsaccount gemaakt wanneer u zich aanmeldt om Azure te gebruiken. U gebruikt uw factureringsaccount om uw facturen en betalingen te beheren en kosten bij te houden. U kunt toegang hebben tot meerdere factureringsaccounts. Stel dat u zich voor uw persoonlijke projecten hebt geregistreerd voor Azure. U kunt ook toegang hebben via de Enterprise-overeenkomst van uw organisatie of de Microsoft-klantovereenkomst. Voor elk van deze scenario's hebt u dan een afzonderlijk factureringsaccount.

De Azure-portal ondersteunt momenteel het volgende type factureringsaccount:

- **Microsoft Online Services-programma**: Er wordt een factureringsaccount voor een Microsoft Online Services-programma gemaakt wanneer u zich bij de Azure-website aanmeldt om Azure te gebruiken. Bijvoorbeeld als u zich aanmeldt voor een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/), een [account met tarieven op gebruiksbasis](https://azure.microsoft.com/offers/ms-azr-0003p/) of een [Visual Studio-abonnement](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise Overeenkomst**: Er wordt een factureringsaccount voor een Enterprise-overeenkomst gemaakt wanneer uw organisatie een [Enterprise-overeenkomst (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) ondertekent om Azure te gebruiken.

- **Microsoft-klantovereenkomst**: Er wordt een factureringsaccount voor een Microsoft-klantovereenkomst aangemaakt, wanneer uw organisatie samenwerkt met een Microsoft-vertegenwoordiger om een Microsoft-klantovereenkomst te ondertekenen. Sommige klanten in bepaalde regio's, die zich via de Azure-website aanmelden voor een [account met tarieven op gebruiksbasis](https://azure.microsoft.com/offers/ms-azr-0003p/) of die hun [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/) upgraden, hebben mogelijk ook een factureringsaccount voor een Microsoft-klantovereenkomst. Voor meer informatie, zie [Aan de slag met uw factureringsaccount voor een Microsoft-klantovereenkomst](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Bereiken voor factureringsaccounts
Een bereik is een knooppunt binnen een factureringsaccount waar gebruikers facturen kunnen weergeven en beheren. Dit de plek waar gebruikers factureringsgegevens, betalingen en facturen beheren en algemeen accountbeheer uitvoeren. 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services-programma

|Bereik  |Definitie  |
|---------|---------|
|Factureringsaccount     | Verwijst naar een enkele eigenaar (accountbeheerder) voor één of meer Azure abonnementen. Een accountbeheerder is gemachtigd om verschillende factureringstaken uit te voeren, zoals abonnementen maken, facturen inzien of de facturering voor abonnementen wijzigen.  |
|Abonnement     |  Verwijst naar een groepering van Azure-resources. De factuur wordt gegenereerd in dit bereik. Het heeft zijn eigen betalingswijzen die worden gebruikt om de factuur te betalen.|


### <a name="enterprise-agreement"></a>Enterprise Agreement

|Bereik  |Definitie  |
|---------|---------|
|Factureringsaccount    | Verwijst naar de registratie van een Enterprise-overeenkomst. De factuur wordt gegenereerd in dit bereik. Het is gestructureerd op basis van afdelingen en inschrijvingsaccounts.  |
|Afdeling     |  Optionele groepering van inschrijvingsaccounts.      |
|Inschrijvingsaccount     |  Verwijst naar een enkele accounteigenaar. Azure-abonnementen worden binnen dit bereik gemaakt.  |


### <a name="microsoft-customer-agreement"></a>Microsoft-klantovereenkomst

|Bereik  |Taken  |
|---------|---------|
|Factureringsaccount     |   Verwijst naar een klantenovereenkomst voor meerdere Microsoft-producten en -services. Het is gestructureerd op basis van factureringsprofielen en factuursecties.   |
|Factureringsprofiel     |  Verwijst naar een factuur en de bijbehorende betalingswijzen. De factuur wordt gegenereerd in dit bereik. Het kan meerdere factuursecties hebben.      |
|Factuursectie     |   Verwijst naar een kostengroep op een factuur. Abonnementen en andere aankopen zijn aan dit bereik gekoppeld.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Schakelen tussen factureringsbereiken in de Azure-portal


1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van zoekopdracht in de Azure-portal](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Selecteer **Alle factureringsbereiken** aan de linkerkant.

   ![Schermopname van alle factureringsbereiken](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** U ziet niet **Alle factureringsbereiken** als u toegang hebt tot slechts één bereik.

4. Selecteer een bereik om meer details weer te geven.



## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [analyseren van uw kosten](../cost-management/quick-acm-cost-analysis.md).