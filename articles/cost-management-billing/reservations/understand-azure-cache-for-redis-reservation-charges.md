---
title: Meer informatie over het toepassen van reserveringskorting op Azure Cache voor Redis | Microsoft Docs
description: Meer informatie over hoe reserveringskorting wordt toegepast op Azure Cache for Redis-exemplaren.
author: yegu-ms
manager: maiye
ms.service: cache
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: yegu
ms.openlocfilehash: 4ad28bd65fe9bc240e963f103ca5bd658e90fdfd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77529616"
---
# <a name="how-the-reservation-discount-is-applied-to-azure-cache-for-redis"></a>Het toepassen van de reserveringskorting op Azure Cache for Redis

Nadat u gereserveerde capaciteit voor Azure Cache for Redis hebt gekocht, wordt de reserveringskorting automatisch toegepast op cache-exemplaren die overeenkomen met de kenmerken en hoeveelheid van de reservering. Een reservering dekt alleen de rekenkosten van uw Azure Cache for Redis. Er worden tegen de normale tarieven opslag- en netwerkkosten in rekening gebracht. Gereserveerde capaciteit is alleen beschikbaar voor caches in de [Premium-laag](/azure/azure-cache-for-redis/cache-premium-tier-intro).

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: ***gebruiken of verliezen***. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, verliest u de gereserveerde uren.

## <a name="discount-applied-to-azure-cache-for-redis"></a>Korting toegepast op Azure Cache for Redis

De korting op gereserveerde capaciteit voor Azure Cache for Redis wordt op uurbasis toegepast op uw caches. De reservering die u koopt, wordt vergeleken met het rekengebruik dat wordt verzonden door de actieve caches. Wanneer deze caches geen vol uur worden uitgevoerd, wordt de reservering automatisch toegepast op andere caches die voldoen aan de reserveringskenmerken. De korting kan ook worden toegepast op caches die gelijktijdig worden uitgevoerd. Als u geen caches hebt die voldoen aan de reserveringsvoorwaarden en het volle uur worden uitgevoerd, profiteert u mogelijk niet optimaal van de reserveringskorting voor dit uur.

In de volgende voorbeelden ziet u hoe de korting op de gereserveerde capaciteit voor Azure Cache voor Redis wordt toegepast, afhankelijk van het aantal caches dat u hebt aangeschaft en wanneer deze worden uitgevoerd.

* **Voorbeeld 1**: U koopt een gereserveerde capaciteit voor Azure Cache for Redis voor een cache van 6 GB. Als u een cache van 13 GB uitvoert die overeenkomt met de rest van de reserveringskenmerken, betaalt u de prijs per gebruik voor 7 GB van uw Azure Cache voor Redis-rekengebruik, en krijgt u de reserveringskorting voor één uur cacherekengebruik van 6 GB.

In de rest van deze voorbeelden wordt ervan uitgegaan dat de gereserveerde capaciteit voor Azure Cache voor Redis die u koopt voor een cache van 26 GB is, en dat de rest van de reserveringskenmerken overeenkomen met de actieve cache.

* **Voorbeeld 2**: U voert gedurende één uur twee caches van 13 GB uit. De reserveringskorting van 26 GB wordt toegepast op het rekengebruik van beide caches.

* **Voorbeeld 3**: Eén cache van 26 GB voert u uit van 01:00 tot 01:30. Een andere cache van 26 GB voert u uit van 01:30 tot 02:00. Beide worden gedekt door de reserveringskorting.

* **Voorbeeld 4**: Eén cache van 26 GB voert u uit van 01:00 tot 01:45. Een andere cache van 26 GB voert u uit van 01:30 tot 02:00. Voor de overlapping van 15 minuten wordt de prijs voor betalen per gebruik in rekening gebracht. De reserveringskorting geldt voor het rekengebruik voor de rest van de tijd.

Raadpleeg [Meer informatie over Azure-reserveringsgebruik](/azure/billing/billing-understand-reserved-instance-usage-ea) voor meer informatie over en inzicht in hoe uw Azure-reserveringen worden toegepast in uw gebruiksrapporten voor facturering.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen
Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
