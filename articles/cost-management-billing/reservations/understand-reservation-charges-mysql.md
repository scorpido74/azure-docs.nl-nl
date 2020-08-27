---
title: 'Meer informatie over reserveringskorting: Azure Database for MySQL Single-servers'
description: Meer informatie over hoe reserveringskorting wordt toegepast op uw Azure Database for MySQL-servers.
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 4ef19b1fc8f0d503f1a35c60afd1faec2a548ca4
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681988"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mysql"></a>Hoe reserveringskorting wordt toegepast op Azure Database for MySQL

Wanneer u gereserveerde capaciteit voor Azure Database for MySQL hebt gekocht, wordt de reserveringskorting automatisch toegepast op MySQL-servers die overeenkomen met de reserveringskenmerken en -hoeveelheid. Een reservering dekt alleen de rekenkosten van uw Azure Database for MySQL. Er worden tegen de normale tarieven opslag- en netwerkkosten in rekening gebracht.

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: ***gebruiken of verliezen***. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.</br>

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, verliest u de gereserveerde uren.

## <a name="discount-applied-to-azure-database-for-mysql"></a>Korting toegepast op Azure Database for MySQL

De korting op gereserveerde capaciteit voor Azure Database for MySQL wordt op uurbasis toegepast op uw actieve MySQL-servers. De reservering die u aanschaft, wordt vergeleken met het rekengebruik dat wordt gegenereerd door de actieve Azure Database for MySQL-servers. Voor MySQL-servers die geen vol uur worden uitgevoerd, wordt de reservering automatisch toegepast op andere Azure Database for MySQL-instanties die aan de reserveringskenmerken voldoen. De korting kan worden toegepast op Azure Database for MySQL-servers die gelijktijdig worden uitgevoerd. Als u geen MySQL-server hebt die voldoet aan de reserveringsvoorwaarden en het hele uur wordt uitgevoerd, profiteert u mogelijk niet optimaal van de korting op de reserveringscapaciteit voor dat uur.

In de volgende voorbeelden ziet u hoe de korting voor de gereserveerde capaciteit voor Azure Database for MySQL wordt toegepast, afhankelijk van hoeveel kernen u hebt aangeschaft en wanneer deze worden uitgevoerd.

* **Voorbeeld 1**: U koopt een gereserveerde capaciteit voor Azure Database for MySQL voor acht vCores. Als u een Azure Database for MySQL-server met 16 vCores uitvoert die met de rest van de reserveringskenmerken overeenkomt, betaalt u voor 8 vCores van uw MySQL-serverrekengebruik de prijs per gebruik en krijgt u op de andere 8 vCores een uur lang reserveringskorting.</br>

Voor de rest van deze voorbeelden wordt ervan uitgegaan dat u gereserveerde capaciteit voor Azure Database for MySQL met 16 vCores koopt en dat de rest van de reserveringskenmerken overeenkomen met de actieve MySQL-servers.

* **Voorbeeld 2**: U voert gedurende een uur twee Azure Database for MySQL-servers met 8 vCores uit. De reserveringskorting voor 16 vCores wordt toegepast op het rekengebruik van beide Azure Database for MySQL-servers met 8 vCores.

* **Voorbeeld 3**: U voert van 13.00 tot 13.30 uur een Azure Database for MySQL-server met 16 vCores uit. Van 13.30 tot 14.00 uur voert u nog een Azure Database for MySQL-server met 16 vCores uit. Beide worden gedekt door de reserveringskorting.

* **Voorbeeld 4**: U voert van 13.00 tot 13.45 uur een Azure Database for MySQL-server met 16 vCores uit. Van 13.30 tot 14.00 uur voert u nog een Azure Database for MySQL-server met 16 vCores uit. Voor de overlapping van 15 minuten wordt de prijs voor betalen per gebruik in rekening gebracht. De reserveringskorting geldt voor het rekengebruik voor de rest van de tijd.

Raadpleeg [Meer informatie over Azure-reserveringsgebruik](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) voor meer informatie over en inzicht in hoe uw Azure-reserveringen worden toegepast in uw gebruiksrapporten voor facturering.

## <a name="next-steps"></a>Volgende stappen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
