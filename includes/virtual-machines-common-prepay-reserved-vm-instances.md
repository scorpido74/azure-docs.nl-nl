---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/21/2019
ms.openlocfilehash: f583796fc353852ef3898e28fa96524e08cfb4ad
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414578"
---
When you commit to an Azure reserved VM instance you can save money. The reservation discount is applied automatically to the number of running virtual machines that match the reservation scope and attributes. You don't need to assign a reservation to a virtual machine to get the discounts. A reserved instance purchase covers only the compute part of your VM usage. For Windows VMs, the usage meter is split into two separate meters. There's a compute meter, which is same as the Linux meter, and a Windows IP meter. The charges that you see when you make the purchase are only for the compute costs. Charges don't include Windows software costs. For more information about software costs, see [Software costs not included with Azure Reserved VM Instances](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determine the right VM size before you buy

Before you buy a reservation, you should determine the size of the VM that you need. The following sections will help you determine the right VM size.

### <a name="use-reservation-recommendations"></a>Use reservation recommendations

You can use reservation recommendations to help determine the reservations you should purchase.

- Purchase recommendations and recommended quantity are show when you purchase a VM reserved instance in the Azure portal.
- Azure Advisor provides purchase recommendations for individual subscriptions.  
- You can use the APIs to get purchase recommendations for both shared scope and single subscription scope. For more information, see [Reserved instance purchase recommendation APIs for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- For Enterprise Agreement (EA) and Microsoft Customer Agreement (MCA) customers, purchase recommendations for shared and single subscription scopes are available with the [Azure Consumption Insights Power BI content pack](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Services waarop VM-reserveringskortingen van toepassing zijn

Uw VM-reserveringen zijn toepasbaar op het gebruik van VM’s dat vanuit meerdere services wordt verzonden – niet alleen op uw VM-implementaties. Resources die reserveringskortingen krijgen, veranderen afhankelijk van de instelling voor de flexibiliteit van de instantiegrootte.

#### <a name="instance-size-flexibility-setting"></a>Instelling voor de flexibiliteit van de instantiegrootte

De instelling voor de flexibiliteit van de instantiegrootte bepaalt welke services de kortingen voor de gereserveerde instanties ontvangen.

Of de instelling nu aan of uit staat, reserveringskortingen worden automatisch toegepast op elk overeenkomend VM-gebruik wanneer de *ConsumedService* `Microsoft.Compute` is. Controleer daarom uw gebruiksgegevens voor de *ConsumedService*-waarde. Voorbeelden zijn:

- Virtuele machines
- Virtuele-machineschaalsets
- Containerservice
- Azure Batch-implementaties (in de modus gebruikersabonnementen)
- Azure Kubernetes Service (AKS)
- Service Fabric

Als de instelling aan staat, worden de reserveringskortingen automatisch toegepast op elk overeenkomstig VM-gebruik wanneer het bij de *ConsumedService* om een van de volgende varianten gaat:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Controleer de *ConsumedService*-waarde in uw gebruiksgegevens om te bepalen of het gebruik in aanmerking komt voor reserveringskortingen.

Zie [Flexibiliteit van de VM-grootte met gereserveerde VM-instanties](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md) voor meer informatie over flexibiliteit met instantiegrootten.

### <a name="analyze-your-usage-information"></a>Analyze your usage information
Analyze your usage information to help determine which reservations you should purchase.

Usage data is available in the usage file and APIs. Use them together to determine which reservation to purchase. Check for VM instances that have high usage on daily basis to determine the quantity of reservations to purchase.

Avoid the `Meter` subcategory and `Product` fields in usage data. They don't distinguish between VM sizes that use premium storage. If you use these fields to determine the VM size for reservation purchase, you may buy the wrong size. Then you won't get the reservation discount you expect. Instead, refer to the `AdditionalInfo` field in your usage file or usage API to determine the correct VM size.

### <a name="purchase-restriction-considerations"></a>Purchase restriction considerations

Reserved VM Instances are available for most VM sizes with some exceptions. Reservation discounts don't apply for the following VMs:

- **VM series** - A-series, Av2-series, or G-series.

- **Preview or Promo VMs** - Any VM-series or size that is in preview or uses promotional meter.

- **Clouds** - Reservations aren't available for purchase in Germany or China regions.

- **Insufficient quota** - A reservation that is scoped to a single subscription must have vCPU quota available in the subscription for the new RI. For example, if the target subscription has a quota limit of 10 vCPUs for D-Series, then you can't buy a reservation for 11 Standard_D1 instances. The quota check for reservations includes the VMs already deployed in the subscription. For example, if the subscription has a quota of 10 vCPUs for D-Series and has two standard_D1 instances deployed, then you can buy a reservation for 10 standard_D1 instances in this subscription. You can [create quote increase request](../articles/azure-supportability/resource-manager-core-quotas-request.md) to resolve this issue.

- **Capacity restrictions** - In rare circumstances, Azure limits the purchase of new reservations for subset of VM sizes, because of low capacity in a region.

## <a name="buy-a-reserved-vm-instance"></a>Buy a Reserved VM Instance

You can buy a reserved VM instance in the [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Betaal [vooraf of per maand](../articles/billing/billing-monthly-payments-reservations.md) voor de reservering.
These requirements apply to buying a reserved VM instance:

- You must be in an Owner role for at least one EA subscription or a subscription with a pay-as-you-go rate.
- For EA subscriptions, the **Add Reserved Instances** option must be enabled in the [EA portal](https://ea.azure.com/). Or, if that setting is disabled, you must be an EA Admin for the subscription.
- For the Cloud Solution Provider (CSP) program, only the admin agents or sales agents can buy reservations.

To buy an instance:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Alle services** > **Reserveringen**.
1. Select **Add** to purchase a new reservation and then click **Virtual machine**.
1. Vul de verplichte velden in. Actieve VM-instanties die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de reserveringskorting. Het werkelijke aantal VM-exemplaren dat de korting krijgt, is afhankelijk van het bereik en de geselecteerde hoeveelheid.

If you have an EA agreement, you can use the **Add more option** to quickly add additional instances. The option isn't available for other subscription types.


| Veld      | Beschrijving|
|------------|--------------|
|Abonnement|The subscription used to pay for the reservation. The payment method on the subscription is charged the costs for the reservation. The subscription type must be an enterprise agreement (offer numbers: MS-AZR-0017P or MS-AZR-0148P) or Microsoft Customer Agreement or an individual subscription with pay-as-you-go rates (offer numbers: MS-AZR-0003P or MS-AZR-0023P). The charges are deducted from the monetary commitment balance, if available, or charged as overage. For a subscription with pay-as-you-go rates, the charges are billed to the credit card or invoice payment method on the subscription.|    
|Scope       |The reservation’s scope can cover one subscription or multiple subscriptions (shared scope). If you select: <ul><li>**Bereik van één resourcegroep**: de reserveringskorting wordt alleen toegepast op de overeenkomende resources in de geselecteerde resourcegroep.</li><li>**Bereik van één abonnement**: de reserveringskorting wordt toegepast op de overeenkomende resources in het geselecteerde abonnement.</li><li>**Gedeeld bereik**: de reserveringskorting wordt toegepast op overeenkomende resources binnen in aanmerking komende abonnementen die zich in de factureringscontext bevinden. For EA customers, the billing context is the enrollment. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.</li></ul>|
|Regio    |The Azure region that’s covered by the reservation.|    
|VM-grootte     |The size of the VM instances.|
|Optimize for     |VM instance size flexibility is selected by default. Click **Advanced settings** to change the instance size flexibility value to apply the reservation discount to other VMs in the same [VM size group](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). Met de capaciteitsprioriteit wordt prioriteit toegekend aan de datacentercapaciteit voor uw implementaties. It offers additional confidence in your ability to launch the VM instances when you need them. Capacity priority is only available when the reservation scope is single subscription. |
|Termijn        |One year or three years.|
|Hoeveelheid    |The number of instances being purchased within the reservation. The quantity is the number of running VM instances that can get the billing discount. For example, if you are running 10 Standard_D2 VMs in the East US, then you would specify quantity as 10 to maximize the benefit for all running VMs. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Usage data and reservation utilization

Uw gebruiksgegevens kosten niets voor het deel waarvoor u een reserveringskorting verkrijgt. You can see which VM instance received the reservation discount for each reservation.

For more information about how reservation discounts appear in usage data, see [Understand Azure reservation usage for your Enterprise enrollment](../articles/billing/billing-understand-reserved-instance-usage-ea.md) if you are an EA customer. If you have an individual subscription, see [Understand Azure reservation usage for your Pay-As-You-Go subscription](../articles/billing/billing-understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Change a reservation after purchase

Na aankoop kunt u de volgende typen wijzigingen aanbrengen in een reservering:

- Het bereik van de reservering bijwerken
- Instance size flexibility (if applicable)
- Ownership

You can also split a reservation into smaller chunks and merge already split reservations. None of the changes cause a new commercial transaction or change the end date of the reservation.

You can't make the following types of changes after purchase, directly:

- An existing reservation’s region
- SKU
- Hoeveelheid
- Duur

However, you can *exchange* a reservation if you want to make changes.

## <a name="cancel-exchange-or-refund-reservations"></a>Annulering, omwisseling of terugbetaling van reserveringen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfservice voor ruiling en terugbetaling van Azure-reserveringen](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md) voor meer informatie.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-reserveringen beheren](../articles/billing/billing-manage-reserved-vm-instance.md) voor meer informatie over het beheren van een reservering.
- Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:
    - [Wat zijn Azure-reserveringen?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Reserveringen beheren in Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Begrijpen hoe de reserveringskorting wordt toegepast](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Kosten van Windows-software zijn niet inbegrepen bij reserveringen](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](https://docs.microsoft.com/partner-center/azure-reservations)
