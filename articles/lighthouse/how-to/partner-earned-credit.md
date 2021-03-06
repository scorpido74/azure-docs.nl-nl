---
title: Koppel uw partner-ID om uw impact op gedelegeerde resources bij te houden
description: Meer informatie over hoe u uw partner-ID kunt koppelen aan de klant die u beheert via Azure Lighthouse.
ms.date: 10/30/2020
ms.topic: how-to
ms.openlocfilehash: fcbcc70e380116b8e9f9b1c1e365dee1adb87a99
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080274"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>Koppel uw partner-ID om uw impact op gedelegeerde resources bij te houden 

Als u lid bent van de [Microsoft Partner Network](https://partner.microsoft.com/), kunt u uw partner-id koppelen aan de referenties die worden gebruikt om overgedragen klanten resources te beheren. Met de partner beheerder koppeling (PAL) kan micro soft partners identificeren en herkennen die het succes van Azure-klanten testen. Met deze koppeling kunnen partners van de [CSP (Cloud Solution Provider)](/partner-center/csp-overview) voor klanten die [de micro soft Customer Agreement (MCA) hebben ondertekend, een door de klant overeengekomen](/partner-center/confirm-customer-agreement) [partner](/partner-center/partner-earned-credit) [ontvangen.](/partner-center/azure-plan-get-started)

Als u [klanten opdoet met beheerde service aanbiedingen in azure Marketplace](publish-managed-services-offers.md), wordt er automatisch een koppeling gemaakt met de MPN-id die is gekoppeld aan het partner centrum-account dat wordt gebruikt voor het publiceren van de aanbiedingen. Er is geen verdere actie nodig om de impact van deze klanten bij te houden.

Als u [klanten onboardt met behulp van Azure resource management-sjablonen](onboard-customer.md), moet u actie ondernemen om deze koppeling te maken. Dit doet u door [uw MPN-id te koppelen](../../cost-management-billing/manage/link-partner-id.md) aan ten minste één gebruikers account in uw Tenant voor beheer die toegang heeft tot elk van de onboarded abonnementen.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Uw partner-ID koppelen wanneer u nieuwe klanten onboardeert

Gebruik het volgende proces om uw partner-ID te koppelen (en het tegoed van de partner in te scha kelen, indien van toepassing) bij onboarding van klanten via Azure Resource Manager sjablonen (ARM-sjablonen). U moet weten wat uw [MPN-partner-id](/partner-center/partner-center-account-setup#locate-your-mpn-id) is om deze stappen uit te voeren. Zorg ervoor dat u de **bijbehorende MPN-id** gebruikt die op uw partnerprofiel wordt weergegeven.

Ter vereenvoudiging raden we u aan om een Service-Principal-account in uw Tenant te maken, deze te koppelen aan de **gekoppelde MPN-id** en vervolgens toegang te verlenen aan elke klant met een [ingebouwde Azure-rol die in aanmerking komt voor PEC](/partner-center/azure-roles-perms-pec).

1. [Maak een Service-Principal-account](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) in uw Tenant beheren. Voor dit voor beeld gebruiken we het *Automation-account* van de naam provider voor deze service-principal.
1. Gebruik het account van de Service-Principal [om een koppeling te maken naar de gekoppelde MPN-id](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) in uw Tenant voor beheer. U hoeft dit slechts één keer te doen.
1. Wanneer u [een klant onboardt met arm-sjablonen](onboard-customer.md), moet u een autorisatie opnemen die het provider Automation-account omvat als een gebruiker met een [ingebouwde Azure-rol die in aanmerking komt voor PEC](/partner-center/azure-roles-perms-pec).

Als u deze stappen volgt, wordt elke Tenant van de klant die u beheert, gekoppeld aan uw partner-ID. Het Automation-account van de provider hoeft geen acties in de Tenant van de klant te verifiëren of uit te voeren.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Diagram waarin het PAL-proces wordt weer gegeven met Azure Lighthouse.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Uw partner-ID toevoegen aan eerder onboarded klanten

Als u al een klant hebt voor bereid, wilt u mogelijk geen andere implementatie uitvoeren om de service-principal van uw provider Automation-account toe te voegen. In plaats daarvan kunt u de **gekoppelde MPN-id** koppelen aan een gebruikers account dat al toegang heeft tot het werk in de Tenant van die klant. Zorg ervoor dat aan het account een [ingebouwde Azure-rol is toegewezen die in aanmerking komt voor PEC](/partner-center/azure-roles-perms-pec).

Zodra het account is [gekoppeld aan de gekoppelde MPN-id](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) in uw Tenant voor beheer, kunt u de herkenning voor uw impact op die klant bijhouden.

## <a name="confirm-partner-earned-credit"></a>Het tegoed van de partner bevestigen

U kunt [de PEC-Details in het Azure Portal bekijken](/partner-center/partner-earned-credit-explanation#azure-cost-management) en bevestigen welke kosten het voor deel van PEC hebben ontvangen. Houd er rekening mee dat de PEC alleen van toepassing is op CSP-klanten die de MCA hebben ondertekend en onder het Azure-abonnement vallen.

Als u de bovenstaande stappen hebt gevolgd en de verwachte koppeling niet ziet, opent u een ondersteunings aanvraag in de Azure Portal.

U kunt ook de [SDK van partner Center](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) gebruiken en filteren `rateOfPartnerEarnedCredit` om de PEC-verificatie voor een abonnement te automatiseren.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Microsoft Partner Network](/partner-center/mpn-overview).
- Meer informatie [over hoe PEC wordt berekend en betaald](/partner-center/partner-earned-credit-explanation).
- [Onboarding van klanten](onboard-customer.md) naar Azure Lighthouse.