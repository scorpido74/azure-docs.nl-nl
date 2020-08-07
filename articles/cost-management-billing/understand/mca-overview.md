---
title: Aan de slag met de factureringsrekening voor Microsoft-klantovereenkomst - Azure
description: Lees meer over uw Microsoft Customer Agreement-factureringsaccount, waaronder factureringsprofielen en betalingsmethoden voor facturen.
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: banders
ms.openlocfilehash: adbb8f9aae7eb05ac3c4955beb71dba003ddacd0
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461898"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Aan de slag met uw factureringsaccount voor Microsoft-klantovereenkomst

Er wordt een factureringsaccount gemaakt wanneer u zich aanmeldt om Azure te gebruiken. U gebruikt uw factureringsaccount om facturen en betalingen te beheren en kosten bij te houden. U kunt toegang hebben tot meerdere factureringsaccounts. Stel dat u zich voor uw persoonlijke projecten hebt geregistreerd voor Azure. U kunt ook toegang hebben tot Azure via de Enterprise Agreement van uw organisatie of de Microsoft-klantovereenkomst. Voor elk van deze scenario's hebt u een afzonderlijk factureringsaccount.

Dit artikel is van toepassing op een factureringsaccount voor een Microsoft-klantovereenkomst. [Controleer of u toegang hebt tot een Microsoft-klantovereenkomst](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>Uw factureringsaccount

Uw factureringsaccount voor de Microsoft-klantovereenkomst bevat een of meer factureringsprofielen waarmee u uw facturen en betalingsmethoden kunt beheren. Elk factureringsprofiel bevat een of meer factuursecties waarmee u kosten op de factuur van het factureringsprofiel kunt organiseren.

Het volgende schema toont de relatie tussen een factureringsaccount, factureringsprofielen en factuursecties.

![Diagram van de factureringshiërarchie voor Microsoft-klantovereenkomst](./media/mca-overview/mca-billing-hierarchy.png)

Rollen op het factureringsaccount hebben het hoogste machtigingsniveau. Standaard krijgt alleen de gebruiker die zich heeft aangemeld voor Azure toegang tot het factureringsaccount. Deze rollen moeten worden toegewezen aan gebruikers die facturen moeten weergeven en de kosten voor uw hele organisatie moeten bijhouden, zoals medewerkers van de afdeling Financiën of IT-beheerders. Zie [Rollen en taken van een factureringsaccount](../manage/understand-mca-roles.md#billing-account-roles-and-tasks) voor meer informatie.

## <a name="billing-profiles"></a>Factureringsprofielen

Gebruik een factureringsprofiel om uw factuur en betalingsmethoden te beheren. Aan het begin van de maand wordt voor elk factureringsprofiel in uw account een maandelijkse factuur gegenereerd. De factuur bevat de respectievelijke kosten voor alle Azure-abonnementen en andere aankopen van de vorige maand.

Er wordt automatisch een factureringsprofiel gemaakt voor uw factureringsaccount. Het bevat standaard één factuursectie. U kunt extra secties maken om eenvoudig de kosten bij te houden en te organiseren op basis van uw behoeften, of het nu gaat om een project, afdeling of ontwikkelomgeving. U ziet deze secties op de factuur van uw factureringsprofiel die het gebruik van elk abonnement en elke aankoop die u eraan hebt toegewezen, weergeven.

Rollen in de factureringsprofielen hebben machtigingen om facturen en betalingswijzen weer te geven en te beheren. Wijs deze rollen toe aan gebruikers die facturen betalen, zoals leden van het boekhoudteam in uw organisatie. Zie [Rollen en taken van een factureringsprofiel](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks) voor meer informatie.

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Elk factureringsprofiel krijgt een maandelijkse factuur

Aan het begin van de maand wordt voor elk factureringsprofiel een maandelijkse factuur gegenereerd. De factuur bevat alle kosten van de vorige maand.

In Azure Portal kunt u de factuur bekijken, documenten downloaden en de instelling wijzigen om toekomstige facturen per e-mail te ontvangen. Zie [Facturen voor een Microsoft-klantovereenkomst downloaden](../manage/download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement) voor meer informatie.

### <a name="invoice-payment-methods"></a>Betalingsmethoden voor facturen

Elk factureringsprofiel heeft zijn eigen betalingsmethoden die worden gebruikt om de factuur te betalen. De volgende betalingsmethoden worden ondersteund:

| Type             | Definitie  |
|------------------|-------------|
|Azure-tegoed    |  Tegoeden worden automatisch toegepast op de in aanmerking komende kosten op uw factuur, waardoor het bedrag dat u moet betalen, wordt verlaagd. Zie [Het Azure-tegoedsaldo bijhouden voor uw factureringsprofiel](../manage/mca-check-azure-credits-balance.md)voor meer informatie. |
|Cheque/directe overboeking | Als uw account wordt goedgekeurd voor betaling via cheque/directe overboeking. U kunt het verschuldigde bedrag voor uw factuur betalen via cheque/directe overboeking. De instructies voor de betaling staan op de factuur vermeld |
|Creditcard | Klanten die zich aanmelden voor Azure via de Azure-website, kunnen betalen via een creditcard. |

### <a name="apply-policies-to-control-purchases"></a>Beleid toepassen om aankopen te beheren

Pas beleid toe om aankopen voor Azure Marketplace en Azure-reserveringen te beheren met een factureringsprofiel. U kunt beleid instellen om de aankoop van producten via Azure-reserveringen en Marketplace uit te schakelen. Wanneer het beleid wordt toegepast, kunnen abonnementen die worden gefactureerd aan het factureringsprofiel, niet worden gebruikt om deze aankopen te doen.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Azure-plannen bepalen de prijzen en de serviceovereenkomst voor abonnementen

Azure-plannen bepalen de prijzen en de serviceovereenkomsten voor Azure-abonnementen. Ze worden automatisch ingeschakeld wanneer u een factureringsprofiel maakt. Alle factuursecties die aan het factureringsprofiel zijn gekoppeld, kunnen gebruikmaken van deze plannen. Gebruikers met toegang tot de factuursectie gebruiken de plannen om Azure-abonnementen te maken. De volgende Azure-plannen worden ondersteund in factureringsaccounts voor een Microsoft-klantovereenkomst:

| Plannen             | Definitie  |
|------------------|-------------|
|Microsoft Azure Plan   | Gebruikers toestaan abonnementen te maken die alle werkbelastingen kunnen uitvoeren.  |
|Microsoft Azure Plan voor Dev/Test | Abonnees van Visual Studio toestaan abonnementen te maken die beperkt zijn tot het ontwikkelen of testen van werkbelastingen. Deze abonnementen krijgen voordelen zoals lagere tarieven en toegang tot exclusieve installatiekopieën van virtuele machines in Azure Portal. |

## <a name="invoice-sections"></a>Factuursecties

Maak factuursecties om de kosten op uw factuur te ordenen. Het is bijvoorbeeld mogelijk dat u één factuur voor uw organisatie nodig hebt, maar dat u de kosten wilt indelen per afdeling, team of project. Voor dit scenario hebt u één factureringsprofiel waarin u een factuursectie maakt voor elke afdeling, elk team of elk project.

Wanneer een factuursectie is gemaakt, kunt u anderen toestemming geven om Azure-abonnementen te maken die worden gefactureerd aan de sectie. Alle gebruikskosten en aankopen voor de abonnementen worden vervolgens gefactureerd aan de sectie.

Rollen in de factuursecties hebben machtigingen om te bepalen wie Azure-abonnementen maakt. Wijs deze rollen toe aan gebruikers die de Azure-omgeving instellen voor teams in onze organisatie, zoals engineering leads en technische architecten. Zie [Rollen en taken van een factuursectie](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks) voor meer informatie.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.

Als u hulp nodig hebt, neemt u [contact op met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over uw factureringsaccount:

- [Informatie over beheerdersrollen voor Microsoft-klantovereenkomsten in Azure](../manage/understand-mca-roles.md)
- [Een extra Azure-abonnement voor Microsoft-klantovereenkomst maken](../manage/create-subscription.md)
- [Secties maken op uw factuur voor het ordenen van uw kosten](../manage/mca-section-invoice.md)
