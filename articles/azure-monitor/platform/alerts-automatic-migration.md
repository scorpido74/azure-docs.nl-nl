---
title: Begrijpen hoe het automatische migratieproces voor uw klassieke Azure Monitor-waarschuwingen werkt
description: Meer informatie over hoe het automatische migratieproces werkt.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668244"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Inzicht in het automatische migratieproces voor uw klassieke waarschuwingsregels

Zoals [eerder aangekondigd](monitoring-classic-retirement.md), worden klassieke waarschuwingen in Azure Monitor in september 2019 (oorspronkelijk juli 2019) in gebruik genomen. Als onderdeel van het pensioenproces is [er een migratietool](alerts-using-migration-tool.md) beschikbaar in de Azure-portal voor klanten om zelf migratie te activeren. Als u de migratietool niet voor 31 augustus 2019 hebt gebruikt, start Azure Monitor vanaf 1 september 2019 het proces van automatische migratie van uw klassieke waarschuwingen.
In dit artikel u het automatische migratieproces doorlopen en u eventuele problemen oplossen die u tegenkomen.

  > [!NOTE]
  > Dit artikel is alleen van toepassing op de openbare Azure-cloud. Pensioenproces voor klassieke azure-monitorwaarschuwingen in Azure Government-cloud en Azure China 21Vianet wordt op de toekomstige datum aangekondigd.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Wat gebeurt er tijdens het automatische migratieproces?

- Vanaf **1 september 2019**kunnen klanten geen nieuwe klassieke waarschuwingsregels maken, behalve op [bepaalde statistieken](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).
  - Voor de uitzonderingen kan de klant tot juni 2020 nieuwe klassieke waarschuwingsregels blijven maken en hun klassieke waarschuwingen gebruiken.
- Vanaf **1 september 2019**wordt de migratie van klassieke waarschuwingen in batches geactiveerd voor klanten met klassieke waarschuwingen.
- Net als bij de vrijwillige migratietool worden bepaalde klassieke waarschuwingsregels die niet migrerend worden achtergelaten zoals ze zijn. Deze klassieke waarschuwingsregels blijven worden ondersteund tot juni 2020. Ongeldige klassieke waarschuwingsregels worden echter verwijderd omdat ze niet-functioneel zijn.
Klassieke waarschuwingsregels die verwijderde doelbronnen controleren of op [statistieken die niet langer worden ondersteund,](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) worden als ongeldig beschouwd.
- Zodra de migratie voor uw abonnement wordt gestart, moet de migratie binnen een uur voltooid zijn, tenzij er problemen zijn. Klanten kunnen de status van migratie controleren op [het migratieblad in Azure Monitor.](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)
- Eigenaren van abonnementen ontvangen een e-mail na succesvolle afronding van de migratie.
- Als er problemen zijn tijdens de migratie, ontvangen eigenaren van een abonnement ook een e-mail waarin ze hetzelfde worden geïnformeerd. Klanten kunnen het migratieblad gebruiken om alle details van het probleem te zien.
- Als er een actie nodig is van de klant, zoals het tijdelijk uitschakelen van een resourcelock of het wijzigen van een beleidstoewijzing, moeten klanten eventuele problemen voor 31 oktober 2019 oplossen. Als de problemen tegen die tijd niet zijn opgelost, kan een succesvolle migratie van uw klassieke waarschuwingen niet worden gegarandeerd.

    > [!NOTE]
    > Als u niet wilt wachten tot het automatische migratieproces is gestart, u de migratie nog steeds vrijwillig activeren met behulp van het migratiehulpprogramma.

## <a name="important-things-to-note"></a>Belangrijke dingen om op te merken

Het migratieproces zet klassieke waarschuwingsregels om in nieuwe, gelijkwaardige waarschuwingsregels en maakt actiegroepen. Houd u bij de voorbereiding bewust van de volgende punten:

- De payload-indelingen voor meldingen voor nieuwe waarschuwingsregels verschillen van die van de klassieke waarschuwingsregels omdat ze meer functies ondersteunen. Als u logische apps, runbooks of webhooks hebt die worden geactiveerd door een klassieke waarschuwingsregel, kunnen ze stoppen met functioneren zoals verwacht zodra de migratie is voltooid vanwege verschillen in meldingspayloads. [Meer informatie over hoe u zich voorbereiden op de migratie.](alerts-prepare-migration.md)

- Sommige klassieke waarschuwingsregels kunnen niet worden gemigreerd met behulp van het gereedschap. [Ontdek welke regels niet kunnen worden gemigreerd en wat ermee te doen.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > Het migratieproces heeft geen invloed op de evaluatie van uw klassieke waarschuwingsregels. Ze blijven uitvoeren en verzenden waarschuwingen totdat ze zijn gemigreerd en de nieuwe waarschuwingsregels van kracht worden.

## <a name="what-if-the-automatic-migration-fails"></a>Wat gebeurt er als de automatische migratie mislukt?

Wanneer het automatische migratieproces mislukt, ontvangen abonnementseigenaren een e-mail waarin ze op de hoogte worden gesteld van het probleem. U het migratieblad in Azure Monitor gebruiken om de volledige details van het probleem te bekijken.

Raadpleeg de handleiding voor [probleemoplossing](alerts-understand-migration.md#common-problems-and-remedies) voor hulp bij problemen die u tijdens de migratie ondervinden.

  > [!NOTE]
  > Als er een actie nodig is van de klant, zoals het tijdelijk uitschakelen van een resourcelock of het wijzigen van een beleidstoewijzing, moeten klanten eventuele problemen voor 31 oktober 2019 oplossen. Als de problemen tegen die tijd niet zijn opgelost, kan een succesvolle migratie van uw klassieke waarschuwingen niet worden gegarandeerd.

## <a name="next-steps"></a>Volgende stappen

- [Voorbereiden op de migratie](alerts-prepare-migration.md)
- [Werking van het hulpprogramma voor migratie](alerts-understand-migration.md)
