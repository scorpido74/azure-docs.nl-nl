---
title: Veelgestelde vragen over het Azure Backup controleren van waarschuwingen
description: In dit artikel vindt u antwoorden op veelgestelde vragen over de Azure Backup bewakings waarschuwing en Azure Backup rapporten.
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: dacurwin
ms.openlocfilehash: dc0225092d23371ca97dfedd48a2d3ffcf85a9be
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747395"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Bewakings waarschuwing Azure Backup-Veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over de bewakings waarschuwing van Azure.

## <a name="configure-azure-backup-reports"></a>Azure Backup-rapporten configureren

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Hoe kan ik controleren of rapport gegevens zijn begonnen met stroom naar een opslag account?

Ga naar het opslag account dat u hebt geconfigureerd en selecteer containers. Als de container een vermelding voor inzichten-logs-azurebackupreport bevat, geeft deze aan dat rapport gegevens in de stroom zijn begonnen.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Wat is de frequentie van het pushen van gegevens naar een opslag account en het Azure Backup-inhouds pakket in Power BI?

  Voor dag 0-gebruikers duurt het circa 24 uur om gegevens naar een opslag account te pushen. Nadat deze eerste push is voltooid, worden de gegevens vernieuwd met de frequentie die in de volgende afbeelding wordt weer gegeven.

* Gegevens met betrekking tot **taken**, **waarschuwingen**, **Back-upitems**, **kluizen**, **beveiligde servers**en **beleids regels** worden gepusht naar een opslag account van de klant als en wanneer het wordt geregistreerd.

* Gegevens met betrekking tot **opslag** worden elke 24 uur gepusht naar een opslag account van de klant.

    ![Gegevens push frequentie van Azure Backup-rapporten](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

* Power BI heeft [eenmaal per dag een geplande vernieuwing](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). U kunt de gegevens in Power BI voor het inhouds pakket hand matig vernieuwen.

### <a name="how-long-can-i-retain-reports"></a>Hoe lang kan ik rapporten behouden?

Wanneer u een opslag account configureert, kunt u een Bewaar periode voor rapport gegevens in het opslag account selecteren. Voer stap 6 in het gedeelte [opslag account voor rapporten configureren](backup-azure-configure-reports.md#configure-storage-account-for-reports) uit. U kunt ook [rapporten in Excel analyseren](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) en deze opslaan voor een langere Bewaar periode, op basis van uw behoeften.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Worden alle gegevens in rapporten weer gegeven nadat ik het opslag account heb geconfigureerd?

 Alle gegevens die worden gegenereerd nadat u een opslag account hebt geconfigureerd, worden gepusht naar het opslag account en zijn beschikbaar in rapporten. Taken in uitvoering worden niet gepusht voor rapportage. Nadat de taak is voltooid of mislukt, wordt deze naar rapporten verzonden.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Als ik het opslag account al heb geconfigureerd om rapporten weer te geven, kan ik dan de configuratie wijzigen zodat er een ander opslag account wordt gebruikt?

Ja, u kunt de configuratie wijzigen zodat deze naar een ander opslag account wijst. Gebruik het zojuist geconfigureerde opslag account terwijl u verbinding maakt met het Azure Backup-inhouds pakket. Nadat een ander opslag account is geconfigureerd, worden er ook nieuwe gegevens stromen in dit opslag account. Oudere gegevens (voordat u de configuratie wijzigt) blijven behouden in het oudere opslag account.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Kan ik rapporten weer geven over kluizen en abonnementen?

Ja, u kunt hetzelfde opslag account configureren voor verschillende kluizen om rapporten over meerdere kluizen weer te geven. U kunt ook hetzelfde opslag account configureren voor kluizen in abonnementen. U kunt dit opslag account vervolgens gebruiken terwijl u verbinding maakt met het Azure Backup-inhouds pakket in Power BI om de rapporten weer te geven. Het geselecteerde opslag account moet zich in dezelfde regio bevinden als de Recovery Services kluis.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Hoe lang duurt het voordat de taak status van de Azure backup-agent in de portal wordt weer gegeven?

Het Azure Portal kan Maxi maal 15 minuten duren om de taak status van de Azure backup-agent weer te geven.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Hoe lang duurt het om een waarschuwing te genereren wanneer een back-uptaak mislukt?

Er wordt een waarschuwing gegenereerd binnen 20 minuten van de Azure backup-fout.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Is er sprake van een e-mail bericht dat niet wordt verzonden als er meldingen zijn geconfigureerd?

Ja. In de volgende situaties worden er geen meldingen verzonden.

* Als meldingen per uur worden geconfigureerd en er binnen het uur een waarschuwing wordt gegenereerd en opgelost
* Wanneer een taak wordt geannuleerd
* Als een tweede back-uptaak mislukt, omdat de oorspronkelijke back-uptaak wordt uitgevoerd

## <a name="recovery-services-vault"></a>Recovery Services kluis

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Hoe lang duurt het voordat de taak status van de Azure backup-agent in de portal wordt weer gegeven?

Het Azure Portal kan Maxi maal 15 minuten duren om de taak status van de Azure backup-agent weer te geven.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Hoe lang duurt het om een waarschuwing te genereren wanneer een back-uptaak mislukt?

Er wordt een waarschuwing gegenereerd binnen 20 minuten van de Azure backup-fout.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Is er sprake van een e-mail bericht dat niet wordt verzonden als er meldingen zijn geconfigureerd?

Ja. In de volgende situaties worden er geen meldingen verzonden:

* Als meldingen per uur worden geconfigureerd en er binnen het uur een waarschuwing wordt gegenereerd en opgelost
* Wanneer een taak wordt geannuleerd
* Als een tweede back-uptaak mislukt, omdat de oorspronkelijke back-uptaak wordt uitgevoerd

## <a name="next-steps"></a>Volgende stappen

Lees de andere veelgestelde vragen:

* [Veelgestelde vragen](backup-azure-vm-backup-faq.md) over back-ups van Azure-vm's.
* [Veelgestelde vragen](backup-azure-file-folder-backup-faq.md) over de Azure backup-agent
