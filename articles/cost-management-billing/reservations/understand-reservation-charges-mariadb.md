---
title: Hoe reserveringskorting wordt toegepast bij de uitvoering van Azure Database for MariaDB
description: Hoe reserveringskorting wordt toegepast bij de uitvoering van Azure Database for MariaDB
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: 340d395278eb924570e07d106308e63be3c2f9af
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995518"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mariadb"></a>Hoe reserveringskorting wordt toegepast bij de uitvoering van Azure Database for MariaDB

Wanneer u gereserveerde capaciteit voor Azure Database for MariaDB hebt gekocht, wordt de reserveringskorting automatisch toegepast op MariaDB-servers die overeenkomen met de kenmerken en hoeveelheid van de reservering. Een reservering dekt alleen de berekeningskosten van uw Azure Database for MariaDB. Er worden tegen de normale tarieven opslag- en netwerkkosten in rekening gebracht. 

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: ***gebruiken of verliezen***. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt niet-gebruikte gereserveerde uren niet meenemen.</br>

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, verliest u de gereserveerde uren.

## <a name="discount-applied-to-azure-database-for-mariadb"></a>Korting toegepast op Azure Database for MariaDB

De korting voor gereserveerde capaciteit van Azure Database for MariaDB wordt op uurbasis toegepast op de uitvoering van uw MariaDB-servers. De reservering die u aanschaft, wordt vergeleken met het rekengebruik dat wordt gegenereerd door de actieve Azure Database for MariaDB-servers. Voor MariaDB-servers die geen vol uur worden uitgevoerd, wordt de reservering automatisch toegepast op andere Azure Database for MariaDB-servers die aan de reserveringskenmerken voldoen. De korting kan worden toegepast op Azure Database for MariaDB-servers die gelijktijdig worden uitgevoerd. Als u geen MariaDB-servers hebt die voldoen aan de reserveringsvoorwaarden en het hele uur worden uitgevoerd, profiteert u mogelijk niet optimaal van de korting voor de reserveringscapaciteit voor dat uur.

In de volgende voorbeelden ziet u hoe de korting voor de gereserveerde capaciteit voor Azure Database for MariaDB wordt toegepast, afhankelijk van het aantal kernen dat u hebt aangeschaft en wanneer ze worden uitgevoerd.

* **Voor beeld 1**: u koopt een Azure database for MariaDB gereserveerde capaciteit voor een 8-vCore. Als u actieve Azure Database for MariaDB-servers hebt met 16 vCores die overeenkomen met de rest van de kenmerken van de reservering, worden er kosten in rekening gebracht voor het berekeningsgebruik van 8 vCores van uw MariaDB-server en krijgt u de reserveringskorting voor een uur van het berekeningsgebruik van 8 vCores van de MariaDB-server.</br>

Voor de rest van deze voorbeelden wordt ervan uitgegaan dat u gereserveerde capaciteit voor Azure Database for MariaDB met 16 vCores koopt en dat de rest van de reserveringskenmerken overeenkomen met de actieve MariaDB-servers.

* **Voor beeld 2**: u voert twee Azure database for MariaDB-servers elk uur uit met 8 vCore. De korting van de reservering voor 16 vCores wordt toegepast op het rekengebruik van beide Azure Database for MariaDB-servers met 8 Vcores.

* **Voor beeld 3**: u voert 1 16 vCore Azure database for MariaDB server van 1 tot 1:30 uur uit. U hebt nog een actieve Azure Database for MariaDB met 16 vCores van 13:30 uur tot 14:00 uur. Beide worden gedekt door de reserveringskorting.

* **Voor beeld 4**: u voert 1 16 vCore Azure database for MariaDB server van 1 tot 1:45 uur uit. U hebt nog een actieve Azure Database for MariaDB met 16 vCores van 13:30 uur tot 14:00 uur. Voor de overlapping van 15 minuten wordt de prijs voor betalen per gebruik in rekening gebracht. De reserveringskorting geldt voor het rekengebruik voor de rest van de tijd.

Raadpleeg [Meer informatie over Azure-reserveringsgebruik](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) voor meer informatie over en inzicht in hoe uw Azure-reserveringen worden toegepast in uw gebruiksrapporten voor facturering.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen
Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
