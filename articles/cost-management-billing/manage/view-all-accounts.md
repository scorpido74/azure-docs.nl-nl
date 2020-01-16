---
title: Uw factureringsaccounts weergeven in de Azure-portal | Microsoft Docs
description: Meer informatie over het weergeven van uw factureringsaccounts in de Azure-portal.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 2768d6e146a37e86bb36353f661179ebd7b5033d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994127"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Facturerings accounts en-bereiken in de Azure Portal

Er wordt een factureringsaccount gemaakt wanneer u zich aanmeldt om Azure te gebruiken. U gebruikt uw factureringsaccount om uw facturen en betalingen te beheren en kosten bij te houden. U kunt toegang hebben tot meerdere factureringsaccounts. Stel dat u zich voor uw persoonlijke projecten hebt geregistreerd voor Azure. U kunt ook toegang hebben via de Enterprise-overeenkomst van uw organisatie of de Microsoft-klantovereenkomst. Voor elk van deze scenario's hebt u dan een afzonderlijk factureringsaccount.

Azure Portal ondersteunt het volgende type facturerings accounts:

- **Micro soft Online Services-programma**: er wordt een facturerings account voor een micro soft Online Services-programma gemaakt wanneer u zich aanmeldt voor Azure via de Azure-website. Bijvoorbeeld toen u zich aanmeldde voor een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/), een [account met tarieven voor betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/) of een [Visual Studio-abonnement](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise Agreement**: er wordt een facturerings account voor een Enterprise Agreement gemaakt wanneer uw organisatie een [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) ondertekent om Azure te gebruiken.

- **Micro soft-klant overeenkomst**: een facturerings account voor een micro soft-klant overeenkomst wordt gemaakt wanneer uw organisatie samenwerkt met een micro soft-vertegenwoordiger om een micro soft-klant overeenkomst te ondertekenen. Sommige klanten in regio's selecteren die zich aanmelden via de Azure-website voor een [account met betalen per gebruik-tarieven](https://azure.microsoft.com/offers/ms-azr-0003p/) of een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/) hebben mogelijk een facturerings account voor een micro soft-klant overeenkomst. Voor meer informatie, zie [Aan de slag met uw factureringsaccount voor een Microsoft-klantovereenkomst](../understand/mca-overview.md).

- **Micro soft-partner overeenkomst**: een facturerings account voor een micro soft-partner overeenkomst is gemaakt voor de Cloud Solution Provider (CSP)-partners om hun klanten te beheren in de nieuwe Commerce ervaring. Partners moeten ten minste één klant met een Azure- [abonnement](https://docs.microsoft.com/partner-center/purchase-azure-plan) hebben voor het beheren van hun facturerings account in de Azure Portal. Zie [aan de slag met uw facturerings account voor micro soft Partner Agreement](../understand/mpa-overview.md)voor meer informatie.

Zie [Het type van uw factureringsaccount controleren](#check-the-type-of-your-account) om het type van uw factureringsaccount te bepalen.

## <a name="scopes-for-billing-accounts"></a>Bereiken voor factureringsaccounts
Een bereik is een knoop punt binnen een facturerings account dat u gebruikt om facturering te bekijken en te beheren. U beheert facturerings gegevens, betalingen, facturen en een algemeen account beheer.

### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

 ![Scherm opname van de MOSP-hiërarchie](./media/view-all-accounts/mosp-hierarchy.png)

|Scope  |Definitie  |
|---------|---------|
|Factureringsaccount     | Vertegenwoordigt een overeenkomst die een klant accepteert om Azure te gebruiken. Het bevat een of meer abonnementen.  |
|Abonnement     |  Verwijst naar een groepering van Azure-resources. De factuur wordt gegenereerd in dit bereik. Andere facturerings gegevens, zoals betalings wijzen en adres gebruik, worden aan dit bereik gekoppeld.|

### <a name="enterprise-agreement"></a>Enterprise Agreement

![Scherm opname van EA-hiërarchie](./media/view-all-accounts/ea-hierarchy.png)

|Scope  |Definitie  |
|---------|---------|
|Factureringsaccount    | Verwijst naar de registratie van een Enterprise-overeenkomst. Het bevat een of meer afdelingen en accounts. De factuur wordt gegenereerd in dit bereik. |
|Afdeling     |  Optionele groepering van accounts voor het segmenteren van kosten in logische groeperingen en het instellen van budget.     |
|Account     |  Verwijst naar een enkele accounteigenaar. Account eigenaren hebben machtigingen voor het maken en beheren van Azure-abonnementen die worden gefactureerd voor de inschrijving. |

### <a name="microsoft-customer-agreement"></a>Microsoft-klantovereenkomst

![Scherm afbeelding waarin MCA-hiërarchie wordt weer gegeven](./media/view-all-accounts/mca-hierarchy.png)

|Scope  |Taken  |
|---------|---------|
|Factureringsaccount     |   Vertegenwoordigt een overeenkomst die een klant aanvaardt om micro soft-producten en-services te gebruiken. Het bevat een of meer facturerings profielen. |
|Factureringsprofiel     |   Vertegenwoordigt een factuur en de gerelateerde facturerings gegevens, zoals betalings wijzen en facturerings adres. Het bevat een of meer factuur secties. |
|Factuursectie     |   Vertegenwoordigt een groepering van kosten in een factuur. Azure-abonnementen en andere aankopen, zoals Azure Marketplace en app-bron producten, zijn gekoppeld aan dit bereik.    |

### <a name="microsoft-partner-agreement"></a>Micro soft-partner overeenkomst

![Scherm afbeelding waarin de MPA-hiërarchie wordt weer gegeven](./media/view-all-accounts/mpa-hierarchy.png)

|Scope  |Taken  |
|---------|---------|
|Factureringsaccount     |   Vertegenwoordigt een partner overeenkomst voor het beheren van micro soft-producten en-services van klanten in de nieuwe Commerce ervaring. Het bevat een of meer facturerings profielen en klanten.   |
|Factureringsprofiel     |   Hiermee wordt een factuur voor een valuta aangeduid.     |
|Klant    |   Vertegenwoordigt een klant voor een Cloud Solution Provider (CSP)-partner.  Azure-abonnementen en andere aankopen, zoals Azure Marketplace en app-bron producten, zijn gekoppeld aan dit bereik.  |
|Reseller    |   Reseller die services levert aan een klant. Het is een optioneel veld voor een abonnement dat alleen van toepassing is op indirecte providers in het CSP-model met twee lagen.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Schakelen tussen factureringsbereiken in de Azure-portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van zoekopdracht in de Azure-portal](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Selecteer op de pagina overzicht de **optie switch-bereik**.

   ![Scherm opname van de facturerings bereiken](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > U ziet geen switch-bereik als u alleen toegang tot één bereik hebt.

4. Selecteer een bereik om meer details weer te geven.

   ![Scherm opname van de facturerings bereiken](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Controleer het type van uw account
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u een vraag wilt stellen of hulp nodig hebt, maakt u een [ondersteuningsaanvraag](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [analyseren van uw kosten](../costs/quick-acm-cost-analysis.md).
