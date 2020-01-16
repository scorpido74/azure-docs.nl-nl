---
title: Hoe reserveringskorting wordt toegepast op Azure Database for MySQL
description: Hoe reserveringskorting wordt toegepast op Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: 7be3a7913559947be1291db35ac3531f152dfae9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995544"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mysql"></a>Hoe reserveringskorting wordt toegepast op Azure Database for MySQL

Wanneer u gereserveerde capaciteit voor Azure Database for MySQL hebt gekocht, wordt de reserveringskorting automatisch toegepast op MySQL-servers die overeenkomen met de reserveringskenmerken en -hoeveelheid. Een reservering dekt alleen de rekenkosten van uw Azure Database for MySQL. Er worden tegen de normale tarieven opslag- en netwerkkosten in rekening gebracht. 

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: ***gebruiken of verliezen***. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt niet-gebruikte gereserveerde uren niet meenemen.</br>

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, verliest u de gereserveerde uren.

## <a name="discount-applied-to-azure-database-for-mysql"></a>Korting toegepast op Azure Database for MySQL

De korting op gereserveerde capaciteit voor Azure Database for MySQL wordt op uurbasis toegepast op uw actieve MySQL-servers. De reservering die u aanschaft, wordt vergeleken met het rekengebruik dat wordt gegenereerd door de actieve Azure Database for MySQL-servers. Voor MySQL-servers die geen vol uur worden uitgevoerd, wordt de reservering automatisch toegepast op andere Azure Database for MySQL-instanties die aan de reserveringskenmerken voldoen. De korting kan worden toegepast op Azure Database for MySQL-servers die gelijktijdig worden uitgevoerd. Als u geen MySQL-server hebt die voldoet aan de reserveringsvoorwaarden en het hele uur wordt uitgevoerd, profiteert u mogelijk niet optimaal van de korting op de reserveringscapaciteit voor dat uur.

In de volgende voorbeelden ziet u hoe de korting voor de gereserveerde capaciteit voor Azure Database for MySQL wordt toegepast, afhankelijk van hoeveel kernen u hebt aangeschaft en wanneer deze worden uitgevoerd.

* **Voor beeld 1**: u koopt een Azure database for MySQL gereserveerde capaciteit voor een 8-vCore. Als u een Azure Database for MySQL-server met 16 vCores uitvoert die met de rest van de reserveringskenmerken overeenkomt, betaalt u voor 8 vCores van uw MySQL-serverrekengebruik de prijs per gebruik en krijgt u op de andere 8 vCores een uur lang reserveringskorting.</br>

Voor de rest van deze voorbeelden wordt ervan uitgegaan dat u gereserveerde capaciteit voor Azure Database for MySQL met 16 vCores koopt en dat de rest van de reserveringskenmerken overeenkomen met de actieve MySQL-servers.

* **Voor beeld 2**: u voert twee Azure database for MySQL-servers elk uur uit met 8 vCore. De reserveringskorting voor 16 vCores wordt toegepast op het rekengebruik van beide Azure Database for MySQL-servers met 8 vCores.

* **Voor beeld 3**: u voert 1 16 vCore Azure database for mysql server van 1 tot 1:30 uur uit. Van 13.30 tot 14.00 uur voert u nog een Azure Database for MySQL-server met 16 vCores uit. Beide worden gedekt door de reserveringskorting.

* **Voor beeld 4**: u voert 1 16 vCore Azure database for mysql server van 1 tot 1:45 uur uit. Van 13.30 tot 14.00 uur voert u nog een Azure Database for MySQL-server met 16 vCores uit. Voor de overlapping van 15 minuten wordt de prijs voor betalen per gebruik in rekening gebracht. De reserveringskorting geldt voor het rekengebruik voor de rest van de tijd.

Raadpleeg [Meer informatie over Azure-reserveringsgebruik](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) voor meer informatie over en inzicht in hoe uw Azure-reserveringen worden toegepast in uw gebruiksrapporten voor facturering.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen
Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
