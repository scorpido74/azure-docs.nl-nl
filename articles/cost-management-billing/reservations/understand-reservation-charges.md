---
title: Meer informatie over reserveringskorting voor Azure SQL Database | Microsoft Docs
description: Meer informatie over hoe reserveringskorting wordt toegepast op actieve Azure SQL-databases.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 736e046b5254de9aa18c898994d7df11efc98a9a
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037984"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-database"></a>Hoe reserveringskorting wordt toegepast op Azure SQL Database

Wanneer u gereserveerde capaciteit voor Azure SQL Database hebt gekocht, wordt de reserveringskorting automatisch toegepast op SQL-databases die overeenkomen met de kenmerken en hoeveelheid van de reservering. Een reservering dekt de rekenkosten van uw SQL Database-instantie. Er worden tegen de normale tarieven software-, opslag- en netwerkkosten in rekening gebracht. U kunt de licentiekosten voor SQL Database dekken via [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

Houd er rekening mee dat reserveringskortingen niet van toepassing zijn op serverloze modellen van Azure SQL Database.

Raadpleeg voor gereserveerde VM-instanties [Understand Azure Reserved VM Instances discount](../manage/understand-vm-reservation-charges.md) (Meer informatie over korting voor gereserveerde VM-instanties voor Azure).

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: '*gebruiken of verliezen*'. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, *verliest* u de gereserveerde uren.

## <a name="discount-applied-to-running-sql-databases"></a>Korting toegepast op actieve SQL-databases

 De korting voor reservecapaciteit voor SQL Database wordt op uurbasis toegepast op actieve SQL-databases. De reservering die u aanschaft, wordt vergeleken met het rekengebruik dat wordt gegenereerd door de actieve SQL-databases. Voor SQL-databases die geen vol uur worden uitgevoerd, wordt de reservering automatisch toegepast op andere SQL-databases die aan de reserveringskenmerken voldoen. De korting kan ook worden toegepast op SQL-databases die gelijktijdig worden uitgevoerd. Als u geen SQL-databases hebt die voldoen aan de reserveringsvoorwaarden en het hele uur worden uitgevoerd, profiteert u mogelijk niet optimaal van de korting voor de reserveringscapaciteit voor dat uur.

In de volgende voorbeelden ziet u hoe de korting voor de gereserveerde capaciteit voor SQL Database wordt toegepast, afhankelijk van het aantal kernen dat u hebt aangeschaft en wanneer ze worden uitgevoerd.

- Scenario 1: U koopt gereserveerde capaciteit voor SQL Database voor een SQL Database-instantie met 8 cores. U voert een SQL Database-instantie met 16 cores uit die voldoet aan de rest van de kenmerken van de reservering. De prijs voor betalen per gebruik wordt in rekening gebracht voor het rekengebruik van 8 cores voor SQL Database. U krijgt de reserveringskorting op een uur rekengebruik van 8 cores voor SQL Database.

Voor de rest van deze voorbeelden wordt ervan uitgegaan dat u gereserveerde capaciteit voor SQL Database met 16 kernen koopt en dat de rest van de reserveringskenmerken overeenkomen met de actieve SQL-databases.

- Scenario 2: U voert een uur lang twee SQL-databases met elk 8 kernen uit. De reserveringskorting voor 16 cores wordt toegepast op het rekengebruik van beide SQL-databases met 8 kernen.
- Scenario 3: U voert één SQL Database-instantie met 16 cores uit van 13:00 tot 13:30 uur. U voert nog een SQL Database-instantie met 16 cores uit van 13:30 tot 14:00 uur. Beide worden gedekt door de reserveringskorting.
- Scenario 4: U voert een SQL Database-instantie met 16 cores uit van 13:00 tot 13:45 uur. U voert nog een SQL Database-instantie met 16 cores uit van 13:30 tot 14:00 uur. Voor de overlapping van 15 minuten wordt de prijs voor betalen per gebruik in rekening gebracht. De reserveringskorting geldt voor het rekengebruik voor de rest van de tijd.

Raadpleeg [Meer informatie over Azure-reserveringsgebruik](understand-reserved-instance-usage-ea.md) voor meer informatie over en inzicht in hoe uw Azure-reserveringen worden toegepast in uw gebruiksrapporten voor facturering.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u een vraag wilt stellen of hulp nodig hebt, maakt u een [ondersteuningsaanvraag](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
- [Vooruitbetalen voor Virtual Machines met Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Vooruitbetalen voor compute-resources van SQL Database met gereserveerde capaciteit voor Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure-reserveringen beheren](manage-reserved-vm-instance.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
- [Inzicht in het gebruik van reserveringen voor CSP-abonnementen](/partner-center/azure-reservations)
