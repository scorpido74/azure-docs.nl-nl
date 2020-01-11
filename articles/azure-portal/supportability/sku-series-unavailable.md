---
title: SKU-serie niet beschikbaar | Microsoft Docs
description: Sommige SKU-reeksen zijn niet beschikbaar voor het geselecteerde abonnement voor deze regio.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: e317ae1ad88cf162f1d55a06d19e7b3b0b88ce60
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896725"
---
# <a name="region-or-sku-unavailable"></a>Regio of SKU is niet beschikbaar
In dit artikel wordt beschreven hoe u het probleem oplost van een Azure-abonnement dat geen toegang heeft tot een regio of een VM-SKU.

## <a name="symptoms"></a>Symptomen

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Wanneer u een virtuele machine implementeert, wordt een van de volgende fout berichten weer gegeven:
```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Bij het kopen van gereserveerde exemplaren van virtuele machines, wordt een van de volgende fout berichten weer gegeven:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Bij het maken van een ondersteunings aanvraag om het quotum voor reken kernen te verhogen, is een regio of SKU-familie niet beschikbaar voor selectie.

## <a name="solution"></a>Oplossing
We raden u aan om een alternatieve regio of SKU te beschouwen die voldoet aan de behoeften van uw bedrijf. Als u geen geschikte regio of SKU kunt vinden, maakt u een [ondersteunings aanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) voor abonnements beheer volgens de onderstaande stappen:


- Selecteer op de pagina basis informatie probleem type als abonnements beheer, selecteer het abonnement en klik op volgende.

![Blade Grondbeginselen](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Selecteer op de pagina probleem het probleem type "andere algemene vragen".
- In de sectie Details:
  - Geef aan of u virtuele machines wilt implementeren of gereserveerde exemplaren van de virtuele machine wilt kopen
  - Geef de regio, de SKU en het aantal exemplaren van virtuele machines op die u wilt implementeren of aanschaffen


![Probleem](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Voer uw contact gegevens in en klik op maken.

![Contactgegevens](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Feedback
We zijn altijd te openen voor feedback en suggesties. Stuur ons uw [suggesties](https://feedback.azure.com/forums/266794-support-feedback). Daarnaast kunt u contact met ons opnemen via [Twitter](https://twitter.com/azuresupport) of de [MSDN-Forums](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Meer informatie
[Veelgestelde vragen over ondersteuning voor Azure](https://azure.microsoft.com/support/faq)

