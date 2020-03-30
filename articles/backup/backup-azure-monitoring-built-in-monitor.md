---
title: Beveiligde azure-back-upworkloads bewaken
description: Lees in dit artikel meer over de bewakings- en meldingsmogelijkheden voor Azure Backup-workloads met behulp van de Azure-portal.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: de5a82f5ad1d8113b27c07484f2f08f4cf97c759
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294925"
---
# <a name="monitoring-azure-backup-workloads"></a>Azure Backup-workloads bewaken

Azure Backup biedt meerdere back-upoplossingen op basis van de back-upvereisten en infrastructuurtopologie (On-premises vs Azure). Elke back-upgebruiker of beheerder moet zien wat er gaande is in alle oplossingen en naar verwachting worden gemeld in belangrijke scenario's. In dit artikel worden de bewakings- en meldingsmogelijkheden van azure backup-service beschreven.

## <a name="backup-jobs-in-recovery-services-vault"></a>Back-uptaken in de kluis Van Herstelservices

Azure Backup biedt ingebouwde bewakings- en waarschuwingsmogelijkheden voor workloads die worden beschermd door Azure Backup. In de kluisinstellingen van Recovery Services biedt de sectie **Monitoring** ingebouwde taken en waarschuwingen.

![RS-kluis ingebouwde bewaking](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Taken worden gegenereerd wanneer bewerkingen zoals het configureren van back-up, back-up, herstel, back-up, enzovoort, worden uitgevoerd.

Taken van de volgende Azure Backup-oplossingen worden hier weergegeven:

- Azure VM Backup
- Azure-bestandsback-up
- Azure-workloadback-up, zoals SQL en SAP HANA
- Azure Backup-agent (MAB)

Taken van System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) worden NIET weergegeven.

> [!NOTE]
> Azure-workloads zoals SQL- en SAP HANA-back-ups binnen Azure VM's hebben een enorm aantal back-uptaken. Logback-ups kunnen bijvoorbeeld elke 15 minuten worden uitgevoerd. Daarom worden voor dergelijke DB-workloads alleen door de gebruiker geactiveerde bewerkingen weergegeven. Geplande back-upbewerkingen worden NIET weergegeven.

## <a name="backup-alerts-in-recovery-services-vault"></a>Back-upwaarschuwingen in de kluis Van Herstelservices

Waarschuwingen zijn voornamelijk scenario's waarin gebruikers worden gewaarschuwd, zodat ze relevante actie kunnen ondernemen. In de sectie **Back-upwaarschuwingen** worden waarschuwingen weergegeven die zijn gegenereerd door de Azure Backup-service. Deze waarschuwingen worden gedefinieerd door de service en de gebruiker kan geen waarschuwingen maken.

### <a name="alert-scenarios"></a>Waarschuwingsscenario's

De volgende scenario's worden door service gedefinieerd als waarschuwingsscenario's.

- Back-up-/herstelfouten
- Back-up is voltooid met waarschuwingen voor Azure Backup-Agent (MAB)
- Stop bescherming met gegevens behouden/Stop-beveiliging met gegevens verwijderen

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Waarschuwingen van de volgende Azure Backup-oplossingen worden hier weergegeven

- Back-ups van Azure-VM's
- Azure-bestandsback-ups
- Azure-workloadbacks zoals SQL, SAP HANA
- Azure Backup-agent (MAB)

> [!NOTE]
> Waarschuwingen van System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) worden hier NIET weergegeven.

### <a name="consolidated-alerts"></a>Geconsolideerde waarschuwingen

Voor Azure-workloadbackbackoplossingen zoals SQL en SAP HANA kunnen logboekback-ups zeer vaak worden gegenereerd (tot elke 15 minuten volgens het beleid). Dus het is ook mogelijk dat de log back-up fouten zijn ook zeer frequent (tot elke 15 minuten). In dit scenario wordt de eindgebruiker overweldigd als er een waarschuwing wordt gegenereerd voor elke foutgebeurtenis. Er wordt dus een waarschuwing verzonden voor de eerste gebeurtenis en als de volgende fouten zijn vanwege dezelfde hoofdoorzaak, worden er geen verdere waarschuwingen gegenereerd. De eerste waarschuwing wordt bijgewerkt met het aantal fouten. Maar als de waarschuwing wordt geïnactiveerd door de gebruiker, zal de volgende gebeurtenis een andere waarschuwing activeren en dit zal worden behandeld als de eerste waarschuwing voor die gebeurtenis. Zo voert Azure Backup waarschuwingsconsolidatie uit voor SQL- en SAP HANA-back-ups.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Uitzonderingen wanneer een waarschuwing niet wordt gegenereerd

Er zijn enkele uitzonderingen wanneer een waarschuwing niet wordt gegenereerd op een fout. Dit zijn:

- Gebruiker heeft de lopende taak expliciet geannuleerd
- De taak mislukt omdat een andere back-up baan is in uitvoering (niets om op te werken hier, omdat we gewoon moeten wachten tot de vorige taak te voltooien)
- De VM-back-uptaak mislukt omdat de back-up van Azure VM niet meer bestaat
- [Geconsolideerde waarschuwingen](#consolidated-alerts)

De bovenstaande uitzonderingen zijn ontworpen met dien verstande dat het resultaat van deze bewerkingen (voornamelijk user triggered) onmiddellijk wordt weergegeven op portal/PS/CLI-clients. De gebruiker is zich dus onmiddellijk bewust en heeft geen melding nodig.

### <a name="alert-types"></a>Waarschuwingstypen

Op basis van de ernst van de waarschuwing kunnen waarschuwingen in drie typen worden gedefinieerd:

- **Kritiek:** In principe zou een back-up- of herstelfout (gepland of geactiveerddoor gebruiker) leiden tot het genereren van een waarschuwing en zou worden weergegeven als een kritieke waarschuwing en ook destructieve bewerkingen zoals back-ups verwijderen.
- **Waarschuwing:** Als de back-upbewerking slaagt, maar met weinig waarschuwingen, worden ze weergegeven als Waarschuwingswaarschuwingen.
- **Informatief:** Vanaf vandaag wordt er geen informatieve waarschuwing gegenereerd door azure backup service.

## <a name="notification-for-backup-alerts"></a>Melding voor back-upmeldingen

> [!NOTE]
> Configuratie van de melding kan alleen worden gedaan via Azure Portal. Ondersteuning voor PS/CLI/REST API/Azure Resource Manager Template wordt niet ondersteund.

Zodra een waarschuwing is gegenereerd, worden gebruikers op de hoogte gebracht. Azure Backup biedt een ingebouwd meldingsmechanisme via e-mail. Men kan afzonderlijke e-mailadressen of distributielijsten opgeven die moeten worden aangemeld wanneer een waarschuwing wordt gegenereerd. U er ook voor kiezen om een melding te ontvangen voor elke afzonderlijke waarschuwing of om ze te groeperen in een samenvatting per uur en vervolgens een melding te ontvangen.

![RS Vault ingebouwde e-mailmelding](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Wanneer de melding is geconfigureerd, ontvangt u een welkomst- of inleidende e-mail. Dit bevestigt dat Azure Backup e-mails naar deze adressen kan verzenden wanneer een waarschuwing wordt gegenereerd.<br>

Als de frequentie is ingesteld op een uursamenvatting en een waarschuwing werd verhoogd en opgelost binnen een uur, zal het geen deel uitmaken van de komende uurverteren.

> [!NOTE]
>
> - Als een destructieve bewerking zoals **stopbeveiliging met verwijderingsgegevens** wordt uitgevoerd, wordt een waarschuwing gegenereerd en wordt een e-mail verzonden naar abonnementseigenaren, beheerders en medebeheerders, zelfs als meldingen NIET zijn geconfigureerd voor de kluis Service herstellen.
> - Als u de melding wilt configureren voor geslaagde taken, gebruikt u [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Waarschuwingen inactiveren

Als u een actieve waarschuwing wilt inactiveren/oplossen, u op het lijstitem klikken dat overeenkomt met de waarschuwing die u wilt inactiveren. Hiermee wordt een scherm geopend dat gedetailleerde informatie over de waarschuwing weergeeft, met een knop 'Inactiveren' bovenaan. Als u op deze knop klikt, wordt de status van de waarschuwing gewijzigd in 'Inactief'. U ook een waarschuwing inactiveren door met de rechtermuisknop op het lijstitem te klikken dat overeenkomt met die waarschuwing en 'Inactiveren' te selecteren.

![INactivering van RS Vault-waarschuwingen](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Volgende stappen

[Azure-back-upworkloads bewaken met Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
