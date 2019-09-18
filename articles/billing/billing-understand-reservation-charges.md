---
title: Meer informatie over reserveringen voor Azure SQL Database-instanties | Microsoft Docs
description: Meer informatie over hoe reserveringskorting wordt toegepast bij het uitvoeren van Azure SQL Database-instanties.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 4b4c6b390e9b3a0cf764f998523fe3c1cdc66026
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "60370284"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>Hoe reserveringskorting wordt toegepast bij het uitvoeren van Azure SQL Database-instanties

Wanneer u gereserveerde capaciteit voor Azure SQL Database hebt gekocht, wordt de reserveringskorting automatisch toegepast op SQL Database-instanties die overeenkomen met de kenmerken en hoeveelheid van de reservering. Een reservering dekt de rekenkosten van uw SQL Database-instantie. Er worden tegen de normale tarieven software-, opslag- en netwerkkosten in rekening gebracht. U kunt de licentiekosten voor SQL Database-instanties dekken via [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

Raadpleeg voor gereserveerde VM-instanties [Understand Azure Reserved VM Instances discount](billing-understand-vm-reservation-charges.md) (Meer informatie over korting voor gereserveerde VM-instanties voor Azure).

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: *gebruiken of verliezen*. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.

Wanneer u een resource afsluit, is de reserveringskorting automatisch van toepassing op een andere overeenkomende resource in het opgegeven bereik. Als er geen overeenkomende resources in het opgegeven bereik worden gevonden, *verliest* u de gereserveerde uren.

## <a name="discount-applied-to-sql-databases"></a>Korting toegepast op SQL Database-instanties

 De korting voor reservecapaciteit voor SQL Database wordt toegepast op actieve SQL Database-instanties en is op uurbasis. De reservering die u aanschaft, wordt vergeleken met het rekengebruik dat wordt gegenereerd door de actieve SQL Database-instanties. Voor SQL Database-instanties die geen vol uur worden uitgevoerd, wordt de reservering automatisch toegepast op andere SQL Database-instanties die aan de reserveringskenmerken voldoen. De korting kan ook worden toegepast op SQL Database-instanties die gelijktijdig worden uitgevoerd. Als u geen SQL Database-instanties hebt die voldoen aan de reserveringsvoorwaarden en het hele uur worden uitgevoerd, profiteert u mogelijk niet optimaal van de korting voor de reserveringscapaciteit voor dat uur.

In de volgende voorbeelden ziet u hoe de korting voor de gereserveerde capaciteit voor SQL Database wordt toegepast, afhankelijk van het aantal kernen dat u hebt aangeschaft en wanneer ze worden uitgevoerd.

- Scenario 1: U koopt gereserveerde capaciteit voor SQL Database voor een SQL Database-instantie met 8 cores. U voert een SQL Database-instantie met 16 cores uit die voldoet aan de rest van de kenmerken van de reservering. De prijs voor betalen per gebruik wordt in rekening gebracht voor het rekengebruik van 8 cores voor SQL Database. U krijgt de reserveringskorting op een uur rekengebruik van 8 cores voor SQL Database.

Voor de rest van deze voorbeelden wordt ervan uitgegaan dat u gereserveerde capaciteit voor SQL Database met 16 cores koopt en dat de rest van de reserveringskenmerken overeenkomen met de actieve SQL Database-instanties.

- Scenario 2: U voert een uur lang twee SQL Database-instanties met elk 8 cores uit. De korting van de reservering voor 16 cores wordt toegepast op het rekengebruik van beide SQL Database-instanties met 8 cores.
- Scenario 3: U voert één SQL Database-instantie met 16 cores uit van 13:00 tot 13:30 uur. U voert nog een SQL Database-instantie met 16 cores uit van 13:30 tot 14:00 uur. Beide worden gedekt door de reserveringskorting.
- Scenario 4: U voert een SQL Database-instantie met 16 cores uit van 13:00 tot 13:45 uur. U voert nog een SQL Database-instantie met 16 cores uit van 13:30 tot 14:00 uur. Voor de overlapping van 15 minuten wordt de prijs voor betalen per gebruik in rekening gebracht. De reserveringskorting geldt voor het rekengebruik voor de rest van de tijd.

Raadpleeg [Meer informatie over Azure-reserveringsgebruik](billing-understand-reserved-instance-usage-ea.md) voor meer informatie over en inzicht in hoe uw Azure-reserveringen worden toegepast in uw gebruiksrapporten voor facturering.

## <a name="need-help-contact-us"></a>Hulp nodig? Contact opnemen

Als u een vraag wilt stellen of hulp nodig hebt, maakt u een [ondersteuningsaanvraag](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Wat zijn Azure-reserveringen?](billing-save-compute-costs-reservations.md)
- [Vooruitbetalen voor Virtual Machines met Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Vooruitbetalen voor compute-resources van SQL Database met gereserveerde capaciteit voor Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Azure-reserveringen beheren](billing-manage-reserved-vm-instance.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](billing-understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](billing-understand-reserved-instance-usage-ea.md)
- [Inzicht in het gebruik van reserveringen voor CSP-abonnementen](/partner-center/azure-reservations)
