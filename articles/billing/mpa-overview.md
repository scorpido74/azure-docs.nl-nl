---
title: Aan de slag met uw factureringsrekening voor Microsoft Partner-overeenkomst - Azure CSP
description: Meer informatie over uw factureringsrekening voor Microsoft Partner-overeenkomst (CSP)
author: bandersmsft
manager: amberbhargava
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2019
ms.author: banders
ms.openlocfilehash: c27f47c68dc5320d7e21434aebba829fe29be2c2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222664"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>Aan de slag met uw factureringsrekening voor Microsoft Partner-overeenkomst

Er wordt een factureringsaccount gemaakt wanneer u zich aanmeldt om Azure te gebruiken. U gebruikt uw factureringsaccount om facturen en betalingen te beheren en kosten bij te houden. U kunt toegang hebben tot meerdere factureringsaccounts. Stel dat u zich voor uw persoonlijke projecten hebt geregistreerd voor Azure. U kunt ook toegang hebben tot Azure via de Enterprise Agreement van uw organisatie of uw Microsoft-klantovereenkomst of Microsoft Partner-overeenkomst. Voor elk van deze scenario's hebt u een afzonderlijk factureringsaccount.

Dit artikel is van toepassing op factureringsrekeningen voor Microsoft Partner-overeenkomsten. Deze rekeningen worden gemaakt voor Cloud Solution Providers (CSP's) voor het beheren van de facturering voor hun klanten in de nieuwe commerce-ervaring. De nieuwe ervaring is alleen beschikbaar voor partners, die ten minste één klant hebben die een Microsoft-klantovereenkomst heeft geaccepteerd en een Azure-plan heeft. [Controleer of u toegang hebt tot een Microsoft Partner-overeenkomst](#check-access-to-a-microsoft-partner-agreement). Een [Azure-plan](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) biedt klanten toegang tot Azure-services tegen tarieven voor betalen per gebruik onder een Microsoft-klantovereenkomst.

## <a name="your-billing-account"></a>Uw factureringsaccount

Uw factureringsrekening voor de Microsoft Partner-overeenkomst bevat een factureringsprofiel voor elke valuta waarin u zaken doet. Met het factureringsprofiel kunt u uw facturen in de bijbehorende valuta beheren. Wanneer u relaties met klanten tot stand brengt, afhankelijk van hun valuta's, worden Azure-abonnementen en andere aankopen in rekening gebracht voor de respectieve factureringsprofielen.

Het volgende schema toont de relatie tussen een factureringsrekening, factureringsprofielen, klanten en resellers.

![Diagram van de factureringshiërarchie voor Microsoft Partner-overeenkomst](./media/mpa-overview/mpa-hierarchy.svg)

Gebruikers met de rol **Globale beheerder** en **Beheerderagent** in uw organisatie kunnen factureringsrekeningen, factureringsprofielen en klanten beheren. Zie [Partner Center - Assign users roles and permissions](https://docs.microsoft.com/partner-center/permissions-overview) (Engelstalig) voor meer informatie.

## <a name="billing-profiles"></a>Factureringsprofielen

Gebruik een factureringsprofiel om uw facturen in een valuta te beheren. Aan het begin van de maand wordt voor elk factureringsprofiel in uw account een maandelijkse factuur gegenereerd. De factuur bevat de kosten voor alle Azure-abonnementen en andere aankopen van de vorige maand in de valuta van het factureringsprofiel.

U kunt de factuur bekijken en de gerelateerde documenten, zoals het bestand met gebruiksgegevens en het prijzenoverzicht, downloaden in de Azure Portal. Zie [Facturen voor een Microsoft Partner-overeenkomst downloaden](billing-download-azure-invoice.md) voor meer informatie.

> [!IMPORTANT]
>
> De facturen voor factureringsprofielen bevatten kosten voor klanten met Azure-plannen, evenals SaaS, Azure Marketplace en reserveringsaankopen voor klanten die geen Microsoft-klantovereenkomst hebben geaccepteerd en geen Azure-plan hebben.

## <a name="customers"></a>Customers

U kunt klanten bekijken en beheren die een Microsoft-klantovereenkomst hebben geaccepteerd en een Azure-plan hebben in de Azure-portal. U kunt kosten en transacties bekijken en Azure-abonnementen voor deze klanten maken en beheren.

### <a name="enable-policy-to-give-visibility-into-cost"></a>Beleid inschakelen om kosten zichtbaar te maken

U kunt beleid toepassen om te bepalen of gebruikers in de organisatie van de klant kosten kunnen bekijken en analyseren op basis van betalen-per-gebruik-tarieven voor hun Azure-verbruik. Standaard is dit beleid uitgeschakeld en kunnen gebruikers de kosten niet bekijken. Als dit beleid is ingeschakeld, kunnen de gebruikers met de juiste [Azure RBAC ](https://docs.microsoft.com/azure/role-based-access-control/overview)-toegang tot een abonnement de kosten voor het abonnement bekijken en analyseren.

Het beleid inschakelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek naar **kostenbeheer en facturering**.

   ![Schermopname van de zoekopdracht in de Azure-portal](./media/mpa-overview/search-cmb.png)

1. Selecteer **Klanten** aan de linkerkant en selecteer vervolgens een klant in de lijst.

   ![Schermopname van het selecteren van een klant](./media/mpa-overview/mpa-customers.png)

1. Selecteer **Beleidsregels** aan de linkerkant.

   ![Schermopname van beleidsregels](./media/mpa-overview/mpa-change-policy.png)

1. Selecteer **Ja**.

## <a name="resellers"></a>Resellers

Indirecte providers in het CSP-[model met twee lagen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview#azure-csp-direct-and-azure-csp-indirect) kunnen een reseller selecteren tijdens het maken van abonnementen voor klanten in de Azure-portal. Na het maken kunnen ze de lijst met abonnementen gefilterd op een reseller weergeven en de kosten voor een klant per reseller analyseren in de Azure-kostenanalyse.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Toegang tot een Microsoft Partner-overeenkomst controleren
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u hulp nodig hebt, kunt u [contact opnemen met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over uw factureringsaccount:

- [Een extra Azure-abonnement voor Microsoft Partner-overeenkomst maken](billing-create-subscription.md)
- Integreer factureringsgegevens met uw eigen rapportagesysteem met behulp van de [Azure Billing-API's](https://docs.microsoft.com/rest/api/billing/)
- [Azure Cost Management-quickstart voor partners](https://go.microsoft.com/fwlink/?linkid=2106482)
