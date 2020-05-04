---
title: Meer informatie over hoe reserveringskorting wordt toegepast op Azure Database for PostgreSQL Single-servers
description: Meer informatie over hoe reserveringskorting wordt toegepast op Azure Database for PostgreSQL Single-servers.
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: e01b35bc540045bc272484159380f8a11e5a6c48
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509394"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-postgresql-single-server"></a>Hoe reserveringskorting wordt toegepast op Azure Database for PostgreSQL Single-servers

Als u gereserveerde capaciteit voor een Azure Database for PostgreSQL Single-server koopt, wordt de reserveringskorting automatisch toegepast op de databases van de PostgreSQL Single-servers die overeenkomen met de reserveringskenmerken en -hoeveelheid. Een reservering dekt alleen de rekenkosten van uw Azure Database for PostgreSQL Single-server. Er worden tegen de normale tarieven opslag- en netwerkkosten in rekening gebracht.

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: ***gebruiken of verliezen***. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.</br>

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, verliest u de gereserveerde uren.

## <a name="discount-applied-to-azure-database-for-postgresql-single-server"></a>Korting toepassen op Azure Database for PostgreSQL Single-servers

Korting voor gereserveerde capaciteit van Azure Database for PostgreSQL Single-servers wordt op uurbasis op uw PostgreSQL Single-server toegepast. De reservering die u aanschaft, wordt vergeleken met het rekengebruik dat wordt gegenereerd door de actieve Azure Database for PostgreSQL Single-server. Voor PostgreSQL Single-servers die geen vol uur worden uitgevoerd, wordt de reservering automatisch toegepast op andere Azure Database for PostgreSQL Single-servers die aan de reserveringskenmerken voldoen. De korting kan worden toegepast op Azure Database for PostgreSQL Single-servers die gelijktijdig worden uitgevoerd. Als u geen PostgreSQL Single-server hebt die aan de reserveringsvoorwaarden voldoet en het hele uur wordt uitgevoerd, profiteert u mogelijk niet optimaal van de korting op de reserveringscapaciteit voor dat uur.

In de volgende voorbeelden ziet u hoe de korting voor de gereserveerde capaciteit voor de Azure Database for PostgreSQL Single-server wordt toegepast, afhankelijk van hoeveel kernen u hebt aangeschaft en wanneer deze worden uitgevoerd.

* **Voorbeeld 1**: U koopt gereserveerde capaciteit voor een Azure Database for PostgreSQL Single-server met 8 vCores. Als u actieve Azure Database for PostgreSQL Single-servers met 16 vCores uitvoert die met de rest van de reserveringskenmerken overeenkomen, betaalt u voor 8 vCores van uw PostgreSQL Single-serverrekengebruik de prijs per gebruik en krijgt u op de andere 8 vCores een uur lang reserveringskorting.</br>

Voor de rest van deze voorbeelden wordt ervan uitgegaan dat u gereserveerde capaciteit voor een Azure Database for PostgreSQL Single-server met 16 vCores koopt en dat de rest van de reserveringskenmerken overeenkomen met de actieve PostgreSQL Single-servers.

* **Voorbeeld 2**: U voert gedurende een uur twee actieve Azure Database for PostgreSQL Single-servers met elk 8 vCores uit. De reserveringskorting voor 16 vCores wordt toegepast op het rekengebruik van beide Azure Database for PostgreSQL Single-servers met 8 Vcores.

* **Voorbeeld 3**: U voert van 13.00 tot 13.30 uur een Azure Database for PostgreSQL Single-server met 16 vCores uit. Van 13.30 tot 14.00 uur voert u nog een Azure Database for PostgreSQL Single-server met 16 vCores uit. Beide worden gedekt door de reserveringskorting.

* **Voorbeeld 4**: U voert van 13.00 tot 13.45 uur een Azure Database for PostgreSQL Single-server met 16 vCores uit. Van 13.30 tot 14.00 uur voert u nog een Azure Database for PostgreSQL Single-server met 16 vCores uit. Voor de overlapping van 15 minuten wordt de prijs voor betalen per gebruik in rekening gebracht. De reserveringskorting geldt voor het rekengebruik voor de rest van de tijd.

Raadpleeg [Meer informatie over Azure-reserveringsgebruik](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) voor meer informatie over en inzicht in hoe uw Azure-reserveringen worden toegepast in uw gebruiksrapporten voor facturering.

## <a name="next-steps"></a>Volgende stappen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
