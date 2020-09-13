---
title: Koppel uw partner-ID om het tegoed van de partner in te scha kelen op gedelegeerde resources
description: Meer informatie over hoe u uw partner-ID kunt koppelen aan de klant die u beheert via Azure Lighthouse.
ms.date: 09/04/2020
ms.topic: how-to
ms.openlocfilehash: 0a9e7f51e90b38bad24eada5a665ca60bf43452f
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89493339"
---
# <a name="link-your-partner-id-to-enable-partner-earned-credit-on-delegated-resources"></a>Koppel uw partner-ID om het tegoed van de partner in te scha kelen op gedelegeerde resources

Als u lid bent van de [Microsoft Partner Network](https://partner.microsoft.com/), kunt u uw partner-id koppelen aan de referenties die worden gebruikt om overgedragen klanten resources te beheren. Op deze manier kunt u de gevolgen van klant afspraken bijhouden en het [tegoed van de partner ontvangen voor beheerde services (PEC)](/partner-center/partner-earned-credit).

Als u [klanten opvolgt met beheerde service aanbiedingen in azure Marketplace](publish-managed-services-offers.md), wordt deze koppeling automatisch uitgevoerd, met behulp van de MPN-id die is gekoppeld aan het partner centrum-account dat is gebruikt voor het publiceren van de aanbiedingen. Er is geen verdere actie vereist om de PEC voor deze klanten te ontvangen.

Als u [klanten onboardt met behulp van Azure resource management-sjablonen](onboard-customer.md), moet u actie ondernemen om deze koppeling te maken. Dit doet u door [uw MPN-id te koppelen](../../cost-management-billing/manage/link-partner-id.md) aan ten minste één gebruikers account in uw Tenant voor beheer die toegang heeft tot elk van de onboarded abonnementen.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Uw partner-ID koppelen wanneer u nieuwe klanten onboardeert

Wanneer klanten onboarding uitvoeren via Azure Resource Manager-sjablonen, kunt u het volgende proces gebruiken om uw partner-ID te koppelen en het tegoed van de partner in te scha kelen. U moet weten wat uw [MPN-partner-id](/partner-center/partner-center-account-setup#locate-your-mpn-id) is om deze stappen uit te voeren.

Ter vereenvoudiging raden wij u aan een Service-Principal-account in uw Tenant te maken, dit te koppelen aan uw MPN-ID en vervolgens toegang te verlenen aan elke klant met een [ingebouwde Azure-rol die in aanmerking komt voor PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

1. [Maak een Service-Principal-account](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) in uw Tenant beheren. Voor dit voor beeld noemen we dit Service-Principal PEC Automation-account.
1. Gebruik het account van de Service-Principal [om een koppeling naar uw partner-id](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) in uw Tenant beheren te maken. U hoeft dit slechts één keer te doen.
1. Wanneer u [een klant onboardt met Azure Resource Manager-sjablonen](onboard-customer.md), moet u een autorisatie opnemen die het PEC Automation-account omvat als een gebruiker met een [ingebouwde Azure-rol die in aanmerking komt voor PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

Als u deze stappen volgt, wordt elke Tenant van de klant die u beheert, gekoppeld aan uw partner-ID, zodat u PEC voor deze klanten kunt ontvangen. Het PEC Automation-account hoeft geen acties in de Tenant van de klant te verifiëren of uit te voeren.

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Uw partner-ID toevoegen aan eerder onboarded klanten

Als u een klant al hebt voor bereid, wilt u mogelijk geen andere implementatie uitvoeren om uw PEC Automation-account Service-Principal toe te voegen. In plaats daarvan kunt u de MPN-ID koppelen aan een gebruikers account dat al toegang heeft tot werk in de Tenant van die klant. Zorg ervoor dat aan het account een [ingebouwde Azure-rol is toegewezen die in aanmerking komt voor PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

Zodra het account is [gekoppeld aan uw partner-id](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) in uw Tenant beheren, kunt u een PEC voor die klant ontvangen.

## <a name="confirm-partner-earned-credit"></a>Het tegoed van de partner bevestigen

U kunt [de PEC-Details in het Azure Portal bekijken](/partner-center/partner-earned-credit-explanation#azure-cost-management) en bevestigen welke kosten het voor deel van PEC hebben ontvangen.

Als u de bovenstaande stappen hebt gevolgd en de koppeling niet ziet, opent u een ondersteunings aanvraag in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het toevoegen van de [Microsoft Partner Network](/partner-center/mpn-overview).
- Meer informatie [over hoe PEC wordt berekend en betaald](/partner-center/partner-earned-credit-explanation).
- [Onboarding van klanten](onboard-customer.md) naar Azure Lighthouse.
