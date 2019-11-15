---
title: Hoe reserveringskorting wordt toegepast op Azure Database for PostgreSQL Single-servers
description: Hoe reserveringskorting wordt toegepast op Azure Database for PostgreSQL Single-servers
author: kummanish
ms.author: manishku
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: bae652e57cbcf141b248b20b0f8385003ba8f000
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608081"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-postgresql-single-server"></a>Hoe reserveringskorting wordt toegepast op Azure Database for PostgreSQL Single-servers

Als u gereserveerde capaciteit voor een Azure Database for PostgreSQL Single-server koopt, wordt de reserveringskorting automatisch toegepast op de databases van de PostgreSQL Single-servers die overeenkomen met de reserveringskenmerken en -hoeveelheid. Een reservering dekt alleen de rekenkosten van uw Azure Database for PostgreSQL Single-server. Er worden tegen de normale tarieven opslag- en netwerkkosten in rekening gebracht. 

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: ***gebruiken of verliezen***. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.</br>

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, verliest u de gereserveerde uren.

## <a name="discount-applied-to-azure-database-for-postgresql-single-server"></a>Korting toepassen op Azure Database for PostgreSQL Single-servers

Korting voor gereserveerde capaciteit van Azure Database for PostgreSQL Single-servers wordt op uurbasis op uw PostgreSQL Single-server toegepast. De reservering die u aanschaft, wordt vergeleken met het rekengebruik dat wordt gegenereerd door de actieve Azure Database for PostgreSQL Single-server. Voor PostgreSQL Single-servers die geen vol uur worden uitgevoerd, wordt de reservering automatisch toegepast op andere Azure Database for PostgreSQL Single-servers die aan de reserveringskenmerken voldoen. De korting kan worden toegepast op Azure Database for PostgreSQL Single-servers die gelijktijdig worden uitgevoerd. Als u geen PostgreSQL Single-servers hebt die aan de reserveringsvoorwaarden voldoen en het hele uur worden uitgevoerd, profiteert u mogelijk niet optimaal van de korting op de reserveringscapaciteit voor dat uur.

In de volgende voorbeelden ziet u hoe de korting voor de gereserveerde capaciteit voor de Azure Database for PostgreSQL Single-server wordt toegepast, afhankelijk van hoeveel kernen u hebt aangeschaft en wanneer deze worden uitgevoerd.

* **Voorbeeld 1**: U koopt gereserveerde capaciteit voor een Azure Database for PostgreSQL Single-server met 8 vCores. Als u actieve Azure Database for PostgreSQL Single-servers met 16 vCores uitvoert die met de rest van de reserveringskenmerken overeenkomen, betaalt u voor 8 vCores van uw PostgreSQL Single-serverrekengebruik de prijs per gebruik en krijgt u op de andere 8 vCores een uur lang reserveringskorting.</br>

Voor de rest van deze voorbeelden wordt ervan uitgegaan dat u gereserveerde capaciteit voor een Azure Database for PostgreSQL Single-server met 16 vCores koopt en dat de rest van de reserveringskenmerken overeenkomen met de actieve PostgreSQL Single-servers.

* **Voorbeeld 2**: U voert gedurende een uur twee actieve Azure Database for PostgreSQL Single-servers met elk 8 vCores uit. De reserveringskorting voor 16 vCores wordt toegepast op het rekengebruik van beide Azure Database for PostgreSQL Single-servers met 8 Vcores.

* **Voorbeeld 3**: U voert van 13.00 tot 13.30 uur een Azure Database for PostgreSQL Single-server met 16 vCores uit. Van 13.30 tot 14.00 uur voert u nog een Azure Database for PostgreSQL Single-server met 16 vCores uit. Beide worden gedekt door de reserveringskorting.

* **Voorbeeld 4**: U voert van 13.00 tot 13.45 uur een Azure Database for PostgreSQL Single-server met 16 vCores uit. Van 13.30 tot 14.00 uur voert u nog een Azure Database for PostgreSQL Single-server met 16 vCores uit. Voor de overlapping van 15 minuten wordt de prijs voor betalen per gebruik in rekening gebracht. De reserveringskorting geldt voor het rekengebruik voor de rest van de tijd.

Raadpleeg [Meer informatie over Azure-reserveringsgebruik](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) voor meer informatie over en inzicht in hoe uw Azure-reserveringen worden toegepast in uw gebruiksrapporten voor facturering.

## <a name="need-help-contact-us"></a>Hulp nodig? Contact opnemen
Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
