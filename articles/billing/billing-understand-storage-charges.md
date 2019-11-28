---
title: Leer hoe de reserveringskorting wordt toegepast op Azure Storage | Microsoft Docs
description: Meer informatie over hoe korting voor gereserveerde Azure Storage-capaciteit wordt toegepast op blok-blob- en Azure Data Lake Storage Gen2-resources.
author: tamram
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: tamram
ms.openlocfilehash: 4dd224036989b4ce78ee382b539e99c12015128e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225752"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>Leer hoe de reserveringskorting wordt toegepast op Azure Storage

Nadat u gereserveerde Azure Storage-capaciteit hebt aangeschaft, wordt de reserveringskorting automatisch toegepast op blok-blob- en Azure Data Lake Storage Gen2-resources die aan de reserveringsvoorwaarden voldoen. De reserveringskorting wordt alleen toegepast op de opslagcapaciteit. Voor de bandbreedte en aanvraagsnelheid gelden tarieven per gebruik.

Zie [Optimize costs for Blob storage with reserved capacity](../storage/blobs/storage-blob-reserved-capacity.md) (Kosten optimaliseren voor blob-opslag met gereserveerde capaciteit) voor meer informatie over gereserveerde Azure Storage-capaciteit.

Zie [Prijzen voor blok-blobs](https://azure.microsoft.com/pricing/details/storage/blobs/) en [Prijzen voor Azure Data Lake Storage Gen 2](https://azure.microsoft.com/pricing/details/storage/data-lake/) voor meer informatie over Azure Storage-reserveringsprijzen.

## <a name="how-the-reservation-discount-is-applied"></a>Hoe de reserveringskorting wordt toegepast

De korting voor gereserveerde Azure Storage-capaciteit wordt op uurbasis toegepast op blok-blob- en Azure Data Lake Storage Gen2-resources.

De korting op de gereserveerde Azure Storage-capaciteit gaat verloren wanneer u deze niet gebruikt. Als u geen blok-blob- of Azure Data Lake Storage Gen2-resources hebt die voldoen aan de reserveringsvoorwaarden voor een bepaald uur, verliest u een reserveringshoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.

Wanneer u een resource verwijdert, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, verliest u de gereserveerde uren.

## <a name="discount-examples"></a>Kortingsvoorbeelden

De volgende voorbeelden laten zien hoe de korting voor gereserveerde Azure Storage-capaciteit wordt toegepast, afhankelijk van de implementaties.

Stel dat u voor een periode van 1 jaar 100 TB aan gereserveerde capaciteit hebt aangeschaft in de regio US - west 2. Uw reservering is voor lokaal redundante opslag (LRS) in de dynamische-toegangslaag.

De kosten van deze voorbeeldreservering bedragen $ 18.540. U kunt ervoor kiezen om het volledige bedrag vooraf te betalen of om de komende twaalf maanden een vaste maandelijkse termijn van $ 1545 te betalen.

In deze voorbeelden wordt ervan uitgegaan dat u zich hebt geregistreerd voor een maandelijks betalingsplan voor uw reserveringen. In de volgende scenario's wordt beschreven wat er gebeurt als u uw gereserveerde capaciteit te weinig of te veel gebruikt.

### <a name="underusing-your-capacity"></a>Uw capaciteit onderschrijden

Stel dat u in een bepaald uur binnen de reserveringsperiode slechts 80 TB van uw gereserveerde capaciteit van 100 TB hebt gebruikt. De resterende 20 TB wordt noch toegepast voor dat uur noch overgedragen.

### <a name="overusing-your-capacity"></a>Uw capaciteit overschrijden

Stel dat u in een bepaald uur binnen de reserveringsperiode 101 TB aan opslagcapaciteit hebt gebruikt. Op 100 TB aan gegevens is de reserveringskorting van toepassing en voor de resterende 1 TB gelden betalen-per-gebruiktarieven. Verandert uw gebruik het volgende uur in 100 TB, dan wordt al het gebruik door de reservering gedekt.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Kosten optimaliseren voor blob-opslag met gereserveerde capaciteit](../storage/blobs/storage-blob-reserved-capacity.md)
- [Wat zijn Azure-reserveringen?](billing-save-compute-costs-reservations.md)
