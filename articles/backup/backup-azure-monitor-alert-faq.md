---
title: Veelgestelde vragen over het bewaken van waarschuwingen en rapporten
description: Ontdek in dit artikel antwoorden op veelgestelde vragen over de Azure Backup Monitoring Alert en Azure Backup-rapporten.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: f5be97458ba658f315c31ae34e540842b64e3ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989566"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure Backup Monitoring Alert - Veelgestelde vragen

In dit artikel worden veelgestelde vragen beantwoord over Azure Backup-monitoring en rapportage.

## <a name="configure-azure-backup-reports"></a>Azure Backup-rapporten configureren

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Hoe controleer ik of rapportagegegevens zijn begonnen te stromen naar een Log Analytics (LA) Workspace?

Navigeer naar de LA Workspace die u hebt geconfigureerd, navigeer naar het **menu-item Logboeken** en voer de query CoreAzureBackup uit | neem 1. Als u ziet dat een record wordt geretourneerd, betekent dit dat gegevens naar de werkruimte zijn gestroomd. De initiële gegevenspush kan tot 24 uur duren.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Wat is de frequentie van gegevens push naar een LA Workspace?

De diagnostische gegevens uit de kluis worden met enige vertraging naar de werkruimte Log Analytics gepompt. Elk evenement arriveert in de Log Analytics-werkruimte 20 tot 30 minuten nadat het uit de kluis van Recovery Services is geschoven. Hier zijn meer details over de vertraging:

* Voor alle oplossingen worden de ingebouwde waarschuwingen van de back-upservice gepusht zodra ze zijn gemaakt. Ze worden dus meestal na 20 tot 30 minuten weergegeven in de werkruimte Log Analytics.
* Voor alle oplossingen worden on-demand back-uptaken en hersteltaken gepusht zodra ze klaar zijn.
* Voor alle oplossingen behalve SQL-back-up worden geplande back-uptaken gepusht zodra ze zijn voltooid.
* Voor SQL-back-ups, omdat logboekbackups elke 15 minuten kunnen optreden, wordt informatie voor alle voltooide geplande back-uptaken, inclusief logboeken, elke 6 uur gebatcht en gepusht.
* Over alle oplossingen wordt andere informatie, zoals het back-upitem, het beleid, herstelpunten, opslag, enzovoort, ten minste eenmaal per dag gepusht.
* Een wijziging in de back-upconfiguratie (zoals het wijzigen van beleid of bewerkingsbeleid) activeert een push van alle gerelateerde back-upgegevens.

### <a name="how-long-can-i-retain-reporting-data"></a>Hoe lang kan ik rapportagegegevens bewaren?

Nadat u een LA Workspace hebt gemaakt, u ervoor kiezen om gegevens maximaal 2 jaar te bewaren. Standaard bewaart een LA Workspace gegevens gedurende 31 dagen.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Zie ik al mijn gegevens in rapporten nadat ik de LA Workspace heb geconfigureerd?

 Alle gegevens die zijn gegenereerd nadat u diagnose-instellingen hebt geconfigureerd, worden naar de LA Workspace gepusht en zijn beschikbaar in rapporten. Lopende taken worden niet gepusht voor rapportage. Nadat de taak is voltooid of mislukt, wordt deze naar rapporten verzonden.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Kan ik rapporten bekijken over kluizen en abonnementen?

Ja, u rapporten bekijken in kluizen, abonnementen en regio's. Uw gegevens kunnen zich bevinden in één LA Workspace of een groep LA Workspaces.

### <a name="can-i-view-reports-across-tenants"></a>Kan ik rapporten over huurders bekijken?

Als u een [Azure Lighthouse-gebruiker](https://azure.microsoft.com/services/azure-lighthouse/) bent met gedelegeerde toegang tot de abonnementen van uw klanten of LA Workspaces, u Back-uprapporten gebruiken om gegevens over al uw tenants weer te geven.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Hoe lang duurt het voordat de azure-back-upagent-taakstatus in de portal wordt weergeven?

Het kan tot 15 minuten duren voordat de Azure-back-upagent-taakstatus wordt weergeven.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Hoelang duurt het om een waarschuwing te geven als een back-uptaak mislukt?

Binnen 20 minuten na de Azure-back-upfout wordt een waarschuwing gegenereerd.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Is er een geval waarin een e-mail niet wordt verzonden als meldingen zijn geconfigureerd?

Ja. In de volgende situaties worden geen meldingen verzonden.

* Als meldingen elk uur worden geconfigureerd en binnen het uur een waarschuwing wordt gegenereerd en opgelost
* Wanneer een taak wordt geannuleerd
* Als een tweede back-uptaak mislukt omdat de oorspronkelijke back-uptaak aan de gang is

## <a name="recovery-services-vault"></a>Recovery Services-kluis

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Hoe lang duurt het voordat de azure-back-upagent-taakstatus in de portal wordt weergeven?

Het kan tot 15 minuten duren voordat de Azure-back-upagent-taakstatus wordt weergeven.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Hoelang duurt het om een waarschuwing te geven als een back-uptaak mislukt?

Binnen 20 minuten na de Azure-back-upfout wordt een waarschuwing gegenereerd.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Is er een geval waarin een e-mail niet wordt verzonden als meldingen zijn geconfigureerd?

Ja. In de volgende situaties worden geen meldingen verzonden:

* Als meldingen elk uur worden geconfigureerd en binnen het uur een waarschuwing wordt gegenereerd en opgelost
* Wanneer een taak wordt geannuleerd
* Als een tweede back-uptaak mislukt omdat de oorspronkelijke back-uptaak aan de gang is

## <a name="next-steps"></a>Volgende stappen

Lees de andere veelgestelde vragen:

* [Veelgestelde vragen](backup-azure-vm-backup-faq.md) over Azure VM-back-ups.
* [Veelgestelde vragen](backup-azure-file-folder-backup-faq.md) over de Azure Backup-agent
