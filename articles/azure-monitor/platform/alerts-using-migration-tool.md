---
title: Uw klassieke waarschuwingen migreren in Azure Monitor met behulp van het hulpprogramma voor vrijwillige migratie
description: Meer informatie over het gebruik van de vrijwillige migratietool om uw klassieke waarschuwingsregels te migreren.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 537d84639523a74cbd9403d4ad25c34a798b7061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665099"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Gebruik de vrijwillige migratietool om uw klassieke waarschuwingsregels te migreren

Zoals [eerder aangekondigd](monitoring-classic-retirement.md), worden klassieke waarschuwingen in Azure Monitor in september 2019 (oorspronkelijk juli 2019) in gebruik genomen. Een migratietool is beschikbaar in de Azure-portal voor klanten die klassieke waarschuwingsregels gebruiken en die zelf migratie willen activeren. In dit artikel wordt uitgelegd hoe u de migratietool gebruiken om uw klassieke waarschuwingsregels vrijwillig te migreren voordat de automatische migratie in september 2019 van start gaat.

> [!NOTE]
> Vanwege vertraging bij de uitrol van migratietool is de pensioendatum voor klassieke waarschuwingen verlengd [tot 31 augustus 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) vanaf de oorspronkelijk aangekondigde datum van 30 juni 2019.

## <a name="benefits-of-new-alerts"></a>Voordelen van nieuwe waarschuwingen

Klassieke waarschuwingen worden vervangen door nieuwe, uniforme waarschuwingen in Azure Monitor. Het nieuwe waarschuwingsplatform heeft de volgende voordelen:

- U waarschuwen voor verschillende multidimensionale statistieken voor [veel meer Azure-services.](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- De nieuwe metrische waarschuwingen ondersteunen [waarschuwingsregels voor meerdere resources](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) die de overhead van het beheer van veel regels sterk verminderen.
- Het uniforme meldingsmechanisme, dat ondersteunt:
  - [Actiegroepen,](action-groups.md)een modulair meldingsmechanisme dat werkt met alle nieuwe waarschuwingstypen (metriek, logboek en activiteitenlogboek).
  - Nieuwe meldingsmechanismen zoals SMS, spraak en ITSM Connector.
- De [uniforme waarschuwingservaring](alerts-overview.md) brengt alle waarschuwingen op verschillende signalen (metriek, logboek en activiteitslogboek) op één plaats.

## <a name="before-you-migrate"></a>Voordat u migreert

Het migratieproces zet klassieke waarschuwingsregels om in nieuwe, gelijkwaardige waarschuwingsregels en maakt actiegroepen. Houd u bij de voorbereiding bewust van de volgende punten:

- Zowel de payload-indeling voor meldingen als de API's voor het maken en beheren van nieuwe waarschuwingsregels verschillen van die van de klassieke waarschuwingsregels omdat ze meer functies ondersteunen. [Meer informatie over hoe u zich voorbereiden op de migratie.](alerts-prepare-migration.md)

- Sommige klassieke waarschuwingsregels kunnen niet worden gemigreerd met behulp van het gereedschap. [Ontdek welke regels niet kunnen worden gemigreerd en wat ermee te doen.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)

    > [!NOTE]
    > Het migratieproces heeft geen invloed op de evaluatie van uw klassieke waarschuwingsregels. Ze blijven uitvoeren en verzenden waarschuwingen totdat ze zijn gemigreerd en de nieuwe waarschuwingsregels van kracht worden.

## <a name="how-to-use-the-migration-tool"></a>Het hulpprogramma voor migratie gebruiken

Voer de volgende stappen uit om de migratie van uw klassieke waarschuwingsregels in de Azure-portal te activeren:

1. Selecteer **Monitor**in [Azure-portal](https://portal.azure.com).

1. Selecteer **Waarschuwingen**en selecteer **Vervolgens Waarschuwingsregels beheren** of Klassieke **waarschuwingen weergeven**.

1. Selecteer **Migreren naar nieuwe regels** om naar de migratiebestemmingspagina te gaan. Op deze pagina ziet u een lijst met al uw abonnementen en hun migratiestatus:

    ![migratielanding](media/alerts-migration/migration-landing.png "Regels migreren")

    Alle abonnementen die kunnen worden gemigreerd met het hulpprogramma, zijn gemarkeerd als **Gereed om te migreren.**

    > [!NOTE]
    > De migratietool wordt gefaseerd uitgerold naar alle abonnementen die gebruikmaken van klassieke waarschuwingsregels. In de vroege fasen van de implementatie ziet u mogelijk een aantal abonnementen die zijn gemarkeerd als niet klaar voor migratie.

1. Selecteer een of meer abonnementen en selecteer **Voorbeeldmigratie**.

    De resulterende pagina toont de details van klassieke waarschuwingsregels die voor één abonnement tegelijk worden gemigreerd. U ook **de migratiegegevens voor dit abonnement downloaden** selecteren om de details in een CSV-indeling op te halen.

    ![migratie-voorbeeld](media/alerts-migration/migration-preview.png "Voorbeeld van migratie bekijken")

1. Geef een of meer e-mailadressen op die op de hoogte moeten worden gesteld van de migratiestatus. U ontvangt e-mail wanneer de migratie is voltooid of als er actie van u nodig is.

1. Selecteer **Migratie starten**. Lees de informatie in het bevestigingsdialoogvenster en bevestig dat u klaar bent om het migratieproces te starten.

    > [!IMPORTANT]
    > Nadat u migratie voor een abonnement hebt gestart, u geen klassieke waarschuwingsregels voor dat abonnement bewerken of maken. Deze beperking zorgt ervoor dat er geen wijzigingen in uw klassieke waarschuwingsregels verloren gaan tijdens de migratie naar de nieuwe regels. Hoewel u uw klassieke waarschuwingsregels niet wijzigen, blijven ze worden uitgevoerd en worden ze waarschuwingen verstrekt totdat ze zijn gemigreerd. Nadat de migratie is voltooid voor uw abonnement, u geen klassieke waarschuwingsregels meer gebruiken.

    ![migratie-bevestigen](media/alerts-migration/migration-confirm.png "Startmigratie bevestigen")

1. Wanneer de migratie is voltooid of als er actie van u vereist is, ontvangt u een e-mail op de adressen die u eerder hebt opgegeven. U ook periodiek de status controleren op de migratiebestemmingspagina in de portal.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Waarom wordt mijn abonnement vermeld als niet klaar voor migratie?

De migratietool wordt gefaseerd uitgerold naar klanten. In de vroege fasen kunnen de meeste of alle abonnementen worden gemarkeerd als **Niet klaar voor migratie.** 

Wanneer een abonnement klaar is voor migratie, ontvangt de eigenaar van het abonnement een e-mailbericht waarin staat dat de tool beschikbaar is. Houd dit bericht in de gaten.

### <a name="who-can-trigger-the-migration"></a>Wie kan de migratie activeren?

Gebruikers die de rol Monitorbijdrager hebben toegewezen op abonnementsniveau, kunnen de migratie activeren. [Meer informatie over op rollen gebaseerd toegangscontrole voor het migratieproces](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Hoe lang zal de migratie duren?

Migratie is voltooid voor de meeste abonnementen in minder dan een uur. U de migratievoortgang bijhouden op de migratiebestemmingspagina. Tijdens de migratie moet u er zeker van zijn dat uw waarschuwingen nog steeds worden uitgevoerd in het klassieke waarschuwingssysteem of in het nieuwe systeem.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Wat kan ik doen als ik tijdens de migratie een probleem tegenkom?

Raadpleeg de handleiding voor [probleemoplossing](alerts-understand-migration.md#common-problems-and-remedies) voor hulp bij problemen die u tijdens de migratie ondervinden. Als er actie van u nodig is om de migratie te voltooien, wordt u hiervan op de hoogte gesteld op de e-mailadressen die u hebt opgegeven bij het instellen van de tool.

## <a name="next-steps"></a>Volgende stappen

- [Voorbereiden op de migratie](alerts-prepare-migration.md)
- [Werking van het hulpprogramma voor migratie](alerts-understand-migration.md)
