---
title: Reserveringskorting toepassen op Azure Disk Storage
description: Lees hoe kortingen voor gereserveerde schijven van Azure worden toegepast op uw Premium SSD Managed Disks.
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 7f23aaebc20b562768fb04ae988e4aff1b62fb21
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902142"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Reserveringskorting toepassen op Azure Disk Storage

Nadat u gereserveerde Azure-schijfcapaciteit hebt gekocht, wordt de reserveringskorting automatisch toegepast op schijfresources die aan de voorwaarden van de reservering voldoen. De reserveringskorting is alleen van toepassing op schijfcapaciteit; momentopnamen van schijven worden in rekening gebracht tegen betalen naar gebruik-tarieven.

Zie [Kosten besparen met Azure-schijfreservering](../../virtual-machines/linux/disks-reserved-capacity.md) voor meer informatie over Azure-schijfreserveringen.
Zie [Prijzen van Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/) voor informatie over prijzen voor Azure-schijfreserveringen.

## <a name="how-the-reservation-discount-is-applied"></a>Hoe de reserveringskorting wordt toegepast

De korting voor een Azure-schijfreservering is een korting van het type 'use-it-or-lose-it' en wordt op uurbasis toegepast op resources van beheerde schijven. Als u in een bepaald uur geen resources van beheerde schijven hebt die voldoen aan de reserveringsvoorwaarden, verliest u een reserveringshoeveelheid voor dat uur. Niet-gebruikte uren worden niet meegenomen naar de volgende periode.

Wanneer u een resource verwijdert, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, verliest u de gereserveerde uren.

## <a name="discount-examples"></a>Kortingsvoorbeelden

De volgende voorbeelden laten zien hoe de korting voor Azure-schijfreserveringen worden toegepast, afhankelijk van uw implementaties:

Stel dat u voor een periode van één jaar honderd P30-schijven (~1 TiB per schijf) aan gereserveerde capaciteit hebt gekocht in de regio US - west 2. De kosten van deze voorbeeldreservering bedragen $ 140.100. U kunt ervoor kiezen om het volledige bedrag vooraf te betalen of om de komende twaalf maanden een vaste maandelijkse termijn van $ 11.675 te betalen.
In deze voorbeelden wordt ervan uitgegaan dat u zich hebt geregistreerd voor een maandelijks betalingsplan voor uw reserveringen. In de volgende scenario's wordt beschreven wat er gebeurt als u uw gereserveerde capaciteit onderbenut, overbenut of gestaffeld gebruikt.

### <a name="underusing-your-capacity"></a>Uw capaciteit onderschrijden

Stel dat u in een bepaald uur binnen de reserveringsperiode slechts 99 P30 Premium SSD's van uw reservering van honderd P30-schijven hebt geïmplementeerd. De resterende P3-schijf wordt dan niet toegepast voor dat uur en komt ook niet als extra schijf beschikbaar voor de volgende periode.

### <a name="overusing-your-capacity"></a>Uw capaciteit overschrijden

Stel dat u in een bepaald uur binnen de reserveringsperiode 101 P30 Premium SSD's gebruikt. De reserveringskorting geldt alleen voor honderd P30-schijven en voor de resterende P30-schijf worden voor dat uur betalen-per-gebruik-tarieven gehanteerd. Als het gebruik in het volgende uur afneemt tot honderd P30-schijven, valt al het gebruik onder de reservering.

### <a name="tiering-your-capacity"></a>Uw capaciteit gestaffeld gebruiken

Stel dat u in een bepaald uur binnen uw reserveringsperiode in totaal 200 P30 Premium SSD's wilt gebruiken. Gedurende de eerste dertig minuten gebruikt u slechts 100 schijven. Dit betekent dat uw gebruik tijdens deze periode volledig is afgedekt omdat u een reservering hebt gemaakt voor honderd P30-schijven. Als u vervolgens het gebruik van de eerste honderd schijven stopzet (en dus niets gebruikt) en vervolgens de andere honderd schijven gebruikt voor de resterende dertig minuten, valt dat gebruik ook onder uw reservering.

![Voorbeelden van onderbenutting, overbenutting of gestaffeld gebruik van gereserveerde capaciteit](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Kosten verlagen met Azure-schijfreservering (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Kosten verlagen met Azure-schijfreservering (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)