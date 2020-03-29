---
title: Regio- of SKU-serie niet beschikbaar - Azure
description: Sommige SKU-series zijn niet beschikbaar voor het geselecteerde abonnement voor deze regio, waarvoor mogelijk ondersteuning voor abonnementsbeheer vereist is.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843623"
---
# <a name="region-or-sku-unavailable"></a>Regio of SKU is niet beschikbaar

In dit artikel wordt beschreven hoe u het probleem oplost dat een Azure-abonnement geen toegang heeft tot een regio of een VM-SKU.

## <a name="symptoms"></a>Symptomen

Wanneer u een virtuele machine implementeert, ontvangt u een van de volgende foutmeldingen:

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

Wanneer u gereserveerde virtuele machine-exemplaren koopt, ontvangt u een van de volgende foutmeldingen:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

Wanneer u een ondersteuningsaanvraag maakt om het rekenkernquotum te verhogen, is een regio of een SKU-gezin niet beschikbaar voor selectie.

## <a name="solution"></a>Oplossing

We raden u aan eerst een alternatieve regio of SKU te overwegen die aan uw bedrijfsbehoeften voldoet.

Als u geen geschikte regio of SKU vinden, maakt u de volgende stappen een [ondersteuningsaanvraag voor](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) **abonnementsbeheer:**

1. Selecteer **Help + ondersteuning**in het menu Azure [portal.](https://portal.azure.com) Selecteer vervolgens **Nieuw ondersteuningsverzoek**.

1. Selecteer **Abonnementsbeheer**selecteren in **Basisbeginselen**voor **Probleemtype**.

1. Selecteer een **abonnement** en voer een korte beschrijving in **samenvatting**in.

   ![Tabblad Basisbeginselen van Nieuw ondersteuningsverzoek](./media/SKU-series-unavailable/support-request-basics.png)

1. Kies Probleemtype selecteren voor probleemtype selecteren voor **Probleemtype** **selecteren**.

1. Kies voor **Probleemtype selecteren**een optie, bijvoorbeeld **Geen toegang tot mijn abonnement of bron** > **Mijn probleem staat hierboven niet vermeld.** Selecteer **Opslaan**.

   ![Een probleem voor de aanvraag opgeven](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Selecteer **Volgende: Oplossingen** om mogelijke oplossingen te verkennen. Selecteer indien nodig **Volgende: Details** om door te gaan.

1. Voer alle aanvullende informatie in die u verstrekken, samen met uw contactgegevens.

1. Selecteer **Controleren + maken**. Nadat u uw gegevens hebt geverifieerd, selecteert **u Maken** om de aanvraag te maken.

## <a name="send-us-your-suggestions"></a>Stuur ons uw suggesties

We staan altijd open voor feedback en suggesties! Stuur ons uw [suggesties.](https://feedback.azure.com/forums/266794-support-feedback) Daarnaast u contact met ons op [Twitter](https://twitter.com/azuresupport) of de [MSDN forums](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Meer informatie

[Veelgestelde vragen over Azure Support](https://azure.microsoft.com/support/faq)
