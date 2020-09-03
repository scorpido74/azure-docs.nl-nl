---
title: Meer informatie over reserveringskorting op Azure Disk Storage
description: Lees hoe korting op gereserveerde Azure-schijven wordt toegepast op uw Azure Premium SSD Managed Disks.
author: roygara
ms.author: rogarana
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: cd89c3ddc3c58de02f1104109ce7f243c4d1e6fd
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682447"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Meer informatie over het toepassen van reserveringskorting op Azure Disk Storage

Nadat u gereserveerde Azure-schijfcapaciteit hebt gekocht, wordt een reserveringskorting automatisch toegepast op schijfresources die aan de voorwaarden van de reservering voldoen. De reserveringskorting wordt alleen toegepast op de schijf-SKU’s. Momentopnamen van schijven worden in rekening gebracht volgens betalen per gebruik-tarieven.

Zie [Kosten besparen met Azure-schijfreservering](../../virtual-machines/disks-reserved-capacity.md) voor meer informatie over Azure-schijfreserveringen. Zie [Prijzen van Azure Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/) voor informatie over prijzen voor Azure-schijfreserveringen.

## <a name="how-the-reservation-discount-is-applied"></a>Hoe de reserveringskorting wordt toegepast

De korting voor Azure-schijfreserveringen gaat verloren wanneer u deze niet gebruikt. De korting wordt elk uur toegepast op beheerde schijf-resources. Als u voor een bepaald uur geen beheerde schijf-resources hebt die voldoen aan de reserveringsvoorwaarden, verliest u een reserveringshoeveelheid voor dit uur. Niet-gebruikte uren kunt u niet doorschuiven naar een later tijdstip.

Wanneer u een resource verwijdert, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomende resource wordt gevonden, gaan de gereserveerde uren verloren.

## <a name="discount-examples"></a>Kortingsvoorbeelden

De volgende voorbeelden laten zien hoe de korting voor Azure-schijfreserveringen wordt toegepast, afhankelijk van uw implementatie.

Stel dat u 100 P30-schijven koopt en reserveert in de regio VS - west 2 voor een termijn van één jaar. Elke schijf heeft ongeveer 1 TiB opslag. Stel dat de kosten van deze voorbeeldreservering $ 140.100 bedragen. U kunt ervoor kiezen om het volledige bedrag vooraf te betalen, of om de komende twaalf maanden een vaste maandelijkse termijn van $ 11.675 te betalen.

In de volgende scenario's wordt beschreven wat er gebeurt als u uw gereserveerde capaciteit onderbenut, overbenut of gestaffeld gebruikt. In deze voorbeelden wordt ervan uitgegaan dat u zich hebt geregistreerd voor een maandelijks betalingsplan voor uw reserveringen.

### <a name="underusing-your-capacity"></a>Uw capaciteit onderschrijden

Stel dat u gedurende één uur binnen de reserveringsperiode slechts 99 van uw 100 gereserveerde Azure Premium SSD P30-schijven (solid-state drive) implementeert. De resterende P30-schijf wordt tijdens dit uur niet toegepast. U kunt deze schijf ook niet bewaren voor later gebruik.

### <a name="overusing-your-capacity"></a>Uw capaciteit overschrijden

Stel dat u in een uur binnen de reserveringsperiode 101 Premium SSD P30-schijven gebruikt. De reserveringskorting is van toepassing op slechts 100 P30-schijven. De extra P30-schijf wordt in rekening gebracht tegen een betalen per gebruik-tarief voor dit uur. Als het gebruik in het volgende uur afneemt naar 100 P30-schijven, valt al het gebruik onder de reservering.

### <a name="tiering-your-capacity"></a>Uw capaciteit gestaffeld gebruiken

Stel dat u in een bepaald uur binnen uw reserveringsperiode in totaal 200 P30 Premium SSD's wilt gebruiken. Stel ook dat u gedurende de eerste 30 minuten slechts 100 schijven gebruikt. Dit betekent dat uw gebruik tijdens deze periode volledig is afgedekt, aangezien u een reservering hebt gemaakt voor 100 P30-schijven. Als u vervolgens het gebruik van de eerste honderd schijven stopzet (en dus niets gebruikt) en vervolgens de andere honderd schijven gebruikt voor de resterende dertig minuten, valt dat gebruik ook onder uw reservering.

![Voorbeeld van onderbenutting, overbenutting of gestaffeld gebruik](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Kosten verlagen met Azure-schijfreservering](../../virtual-machines/disks-reserved-capacity.md)
- [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
