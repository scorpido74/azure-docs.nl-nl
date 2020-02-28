---
title: Meer informatie over hoe het automatische migratie proces voor uw Azure Monitor klassieke waarschuwingen werkt
description: Meer informatie over hoe het automatische migratie proces werkt.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668244"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Meer informatie over het automatische migratie proces voor uw klassieke waarschuwings regels

Zoals [eerder aangekondigd](monitoring-classic-retirement.md), worden klassieke waarschuwingen in azure monitor buiten gebruik gesteld in september 2019 (oorspronkelijk juli 2019). Als onderdeel van het pensioen proces is [er een hulp programma voor migratie](alerts-using-migration-tool.md) beschikbaar in de Azure portal voor klanten om de migratie zelf te activeren. Als u het hulp programma voor migratie niet op 31 augustus 2019 hebt gebruikt Azure Monitor, wordt het proces van automatische migratie van uw klassieke waarschuwingen vanaf 1 september 2019 gestart.
Dit artikel begeleidt u bij het automatische migratie proces en helpt u bij het oplossen van eventuele problemen.

  > [!NOTE]
  > Dit artikel is alleen van toepassing op de open bare Azure-Cloud. Het pensioen proces voor Azure Monitor klassieke waarschuwingen in Azure Government Cloud en Azure China 21Vianet wordt op de toekomst aangekondigd.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Wat gebeurt er tijdens het automatische migratie proces?

- Vanaf **1 September 2019**kunnen klanten geen nieuwe klassieke waarschuwings regels maken, met uitzonde ring van [bepaalde metrische gegevens](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).
  - Voor de uitzonde ringen kan de klant nieuwe regels voor klassieke waarschuwingen blijven maken en hun klassieke waarschuwingen gebruiken tot 2020 juni.
- Vanaf **1 September 2019**wordt de migratie van klassieke waarschuwingen geactiveerd in batches voor klanten die klassieke waarschuwingen hebben.
- Net als bij het hulp programma voor vrijwillige migratie blijven bepaalde klassieke waarschuwings regels die niet kunnen worden gemigreerd, ongewijzigd. Deze klassieke waarschuwings regels worden nog steeds ondersteund tot 2020 juni. Eventuele ongeldige klassieke waarschuwings regels worden echter verwijderd omdat ze niet functioneel zijn.
Klassieke waarschuwings regels die het controleren van verwijderde doel bronnen of [metrische gegevens die niet meer worden ondersteund](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) , worden als ongeldig beschouwd.
- Zodra de migratie voor uw abonnement wordt gestart, wordt de migratie binnen een uur voltooid, tenzij er problemen zijn. Klanten kunnen de status van de migratie controleren op [de Blade migratie in azure monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Abonnements eigenaren ontvangen een e-mail wanneer de migratie is voltooid.
- Als er problemen zijn tijdens de migratie, ontvangen abonnements eigenaren ook een e-mail die ze van elkaar informeert. Klanten kunnen de migratie Blade gebruiken om de volledige details van het probleem weer te geven.
- Als er een actie nodig is van de klant, zoals het tijdelijk uitschakelen van een resource vergrendeling of het wijzigen van een beleids toewijzing, moeten klanten alle problemen oplossen op 31 oktober 2019. Als de problemen niet worden opgelost, kan een geslaagde migratie van uw klassieke waarschuwingen niet worden gegarandeerd.

    > [!NOTE]
    > Als u niet wilt wachten totdat het automatische migratie proces wordt gestart, kunt u de migratie toch vrijwillig activeren met behulp van het hulp programma voor migratie.

## <a name="important-things-to-note"></a>Belang rijke aandachtspunten

Het migratie proces converteert klassieke waarschuwings regels naar nieuwe, gelijkwaardige waarschuwings regels en maakt actie groepen. Houd rekening met de volgende punten in voor bereiding:

- De indeling voor de meldings lading voor nieuwe waarschuwings regels wijkt af van die van de regels van de klassieke waarschuwing, omdat deze meer functies ondersteunen. Als u logische apps, runbooks of webhooks hebt die worden geactiveerd door een klassieke waarschuwings regel, kunnen ze niet meer werken zoals verwacht zodra de migratie is voltooid, omdat er verschillen zijn in nettoladingen van meldingen. [Meer informatie over het voorbereiden van de migratie](alerts-prepare-migration.md).

- Sommige klassieke waarschuwings regels kunnen niet worden gemigreerd met het hulp programma. [Meer informatie over welke regels niet kunnen worden gemigreerd en wat u ermee kunt doen](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > Het migratie proces heeft geen invloed op de evaluatie van uw klassieke waarschuwings regels. Ze blijven worden uitgevoerd en waarschuwingen verzenden totdat ze worden gemigreerd en de nieuwe waarschuwings regels van kracht worden.

## <a name="what-if-the-automatic-migration-fails"></a>Wat gebeurt er als de automatische migratie mislukt?

Wanneer het automatische migratie proces mislukt, ontvangen abonnements eigenaren een e-mail bericht met de melding dat het probleem is opgetreden. U kunt de Blade migratie in Azure Monitor gebruiken om de volledige details van het probleem weer te geven.

Raadpleeg de [hand leiding voor probleem oplossing](alerts-understand-migration.md#common-problems-and-remedies) voor hulp bij problemen die u tijdens de migratie kunt tegen komen.

  > [!NOTE]
  > Als er een actie nodig is van de klant, zoals het tijdelijk uitschakelen van een resource vergrendeling of het wijzigen van een beleids toewijzing, moeten klanten alle problemen oplossen op 31 oktober 2019. Als de problemen niet worden opgelost, kan een geslaagde migratie van uw klassieke waarschuwingen niet worden gegarandeerd.

## <a name="next-steps"></a>Volgende stappen

- [De migratie voorbereiden](alerts-prepare-migration.md)
- [Werking van het hulpprogramma voor migratie](alerts-understand-migration.md)
