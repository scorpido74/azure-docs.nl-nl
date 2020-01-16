---
title: Hoe reserveringskorting wordt toegepast op Azure Database for PostgreSQL Single-servers
description: Hoe reserveringskorting wordt toegepast op Azure Database for PostgreSQL Single-servers
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: 2f1843690861a463c634f523e82facffc72b6743
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995505"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-postgresql-single-server"></a>Hoe reserveringskorting wordt toegepast op Azure Database for PostgreSQL Single-servers

Als u gereserveerde capaciteit voor een Azure Database for PostgreSQL Single-server koopt, wordt de reserveringskorting automatisch toegepast op de databases van de PostgreSQL Single-servers die overeenkomen met de reserveringskenmerken en -hoeveelheid. Een reservering dekt alleen de rekenkosten van uw Azure Database for PostgreSQL Single-server. Er worden tegen de normale tarieven opslag- en netwerkkosten in rekening gebracht. 

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: ***gebruiken of verliezen***. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt niet-gebruikte gereserveerde uren niet meenemen.</br>

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, verliest u de gereserveerde uren.

## <a name="discount-applied-to-azure-database-for-postgresql-single-server"></a>Korting toepassen op Azure Database for PostgreSQL Single-servers

Korting voor gereserveerde capaciteit van Azure Database for PostgreSQL Single-servers wordt op uurbasis op uw PostgreSQL Single-server toegepast. De reservering die u aanschaft, wordt vergeleken met het rekengebruik dat wordt gegenereerd door de actieve Azure Database for PostgreSQL Single-server. Voor PostgreSQL Single-servers die geen vol uur worden uitgevoerd, wordt de reservering automatisch toegepast op andere Azure Database for PostgreSQL Single-servers die aan de reserveringskenmerken voldoen. De korting kan worden toegepast op Azure Database for PostgreSQL Single-servers die gelijktijdig worden uitgevoerd. Als u geen PostgreSQL Single-servers hebt die aan de reserveringsvoorwaarden voldoen en het hele uur worden uitgevoerd, profiteert u mogelijk niet optimaal van de korting op de reserveringscapaciteit voor dat uur.

In de volgende voorbeelden ziet u hoe de korting voor de gereserveerde capaciteit voor de Azure Database for PostgreSQL Single-server wordt toegepast, afhankelijk van hoeveel kernen u hebt aangeschaft en wanneer deze worden uitgevoerd.

* **Voor beeld 1**: u koopt een Azure database for PostgreSQL gereserveerde capaciteit van één server voor een 8-vCore. Als u actieve Azure Database for PostgreSQL Single-servers met 16 vCores uitvoert die met de rest van de reserveringskenmerken overeenkomen, betaalt u voor 8 vCores van uw PostgreSQL Single-serverrekengebruik de prijs per gebruik en krijgt u op de andere 8 vCores een uur lang reserveringskorting.</br>

Voor de rest van deze voorbeelden wordt ervan uitgegaan dat u gereserveerde capaciteit voor een Azure Database for PostgreSQL Single-server met 16 vCores koopt en dat de rest van de reserveringskenmerken overeenkomen met de actieve PostgreSQL Single-servers.

* **Voor beeld 2**: u kunt twee Azure database for PostgreSQL afzonderlijke servers met 8 vCore per uur uitvoeren. De reserveringskorting voor 16 vCores wordt toegepast op het rekengebruik van beide Azure Database for PostgreSQL Single-servers met 8 Vcores.

* **Voor beeld 3**: u voert 1 16 vCore Azure database for PostgreSQL een enkele server van 1 tot 1:30 uur uit. Van 13.30 tot 14.00 uur voert u nog een Azure Database for PostgreSQL Single-server met 16 vCores uit. Beide worden gedekt door de reserveringskorting.

* **Voor beeld 4**: u voert 1 16 vCore Azure database for PostgreSQL een enkele server van 1 tot 1:45 uur uit. Van 13.30 tot 14.00 uur voert u nog een Azure Database for PostgreSQL Single-server met 16 vCores uit. Voor de overlapping van 15 minuten wordt de prijs voor betalen per gebruik in rekening gebracht. De reserveringskorting geldt voor het rekengebruik voor de rest van de tijd.

Raadpleeg [Meer informatie over Azure-reserveringsgebruik](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) voor meer informatie over en inzicht in hoe uw Azure-reserveringen worden toegepast in uw gebruiksrapporten voor facturering.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen
Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
