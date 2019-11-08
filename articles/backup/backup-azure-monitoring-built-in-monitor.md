---
title: 'Azure Backup: Azure Backup beveiligde werk belastingen bewaken'
description: In dit artikel vindt u informatie over de bewakings-en meldings mogelijkheden voor Azure Backup werk belastingen met behulp van de Azure Portal.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: Azure Backup; Berichten
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: dacurwin
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: f1dc1c7fe6aa5e8d20504427679052f68612f65e
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747356"
---
# <a name="monitoring-azure-backup-workloads"></a>Bewaking Azure Backup werk belastingen

Azure Backup biedt meerdere back-upoplossingen op basis van de back-upvereisten en infrastructuur topologie (on-premises versus Azure). Elke back-upgebruiker of beheerder moet zien wat er in alle oplossingen gebeurt en naar verwachting in belang rijke scenario's wordt gewaarschuwd. In dit artikel vindt u meer informatie over de bewakings-en meldings mogelijkheden van Azure Backup service.

## <a name="backup-jobs-in-recovery-services-vault"></a>Back-uptaken in Recovery Services kluis

Azure Backup biedt ingebouwde bewakings-en waarschuwings functies voor werk belastingen die worden beveiligd door Azure Backup. In de Recovery Services kluis instellingen bevat de sectie **bewaking** ingebouwde taken en waarschuwingen.

![Ingebouwde RS-kluis bewaking](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Taken worden gegenereerd wanneer bewerkingen, zoals het configureren van back-ups, back-ups maken, herstellen, verwijderen van back-ups, enzovoort, worden uitgevoerd.

Taken van de volgende Azure Backup oplossingen worden hier weer gegeven:

- Azure VM Backup
- Back-up van Azure-bestand
- Back-up van Azure-workloads, zoals SQL
- Azure Backup-agent (MAB)

Taken van System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup-Server (MABS) worden niet weer gegeven.

> [!NOTE]
> Azure-workloads, zoals SQL-back-ups in azure Vm's, hebben een groot aantal back-uptaken. Logboek back-ups kunnen bijvoorbeeld elke 15 minuten worden uitgevoerd. Voor dergelijke DB-workloads worden dus alleen door de gebruiker geactiveerde bewerkingen weer gegeven. Geplande back-upbewerkingen worden niet weer gegeven.

## <a name="backup-alerts-in-recovery-services-vault"></a>Back-upwaarschuwingen in Recovery Services kluis

Waarschuwingen zijn voornamelijk scenario's waarin gebruikers een melding ontvangen dat ze relevante actie kunnen ondernemen. In het gedeelte **back-upwaarschuwingen** worden waarschuwingen weer gegeven die zijn gegenereerd door Azure backup service. Deze waarschuwingen worden gedefinieerd door de service en de gebruiker kan geen waarschuwingen maken.

### <a name="alert-scenarios"></a>Waarschuwings scenario's

De volgende scenario's worden door de service gedefinieerd als scenario's met waarschuwingen.

- Back-up-/herstelfouten
- Back-up is voltooid met waarschuwingen voor Azure Backup-Agent (MAB)
- Beveiliging stoppen met het bewaren van gegevens of beveiliging tegen stoppen met gegevens verwijderen

### <a name="exceptions-when-an-alert-is-not-raised"></a>Uitzonde ringen wanneer een waarschuwing niet wordt gegenereerd

Er zijn enkele uitzonde ringen wanneer een waarschuwing niet wordt gegenereerd bij een fout:

- De gebruiker heeft de actieve taak expliciet geannuleerd
- De taak is mislukt omdat er een andere back-uptaak wordt uitgevoerd (er is niets om te handelen omdat we er gewoon op moeten wachten tot de vorige taak is voltooid)
- De back-uptaak van de VM is mislukt omdat de back-up van de virtuele machine van Azure niet meer bestaat

De bovenstaande uitzonde ringen zijn ontworpen op basis van de uitleg dat het resultaat van deze bewerkingen (voornamelijk door de gebruiker geactiveerd) direct wordt weer gegeven op de portal-of PS/CLI-clients. Daarom is de gebruiker onmiddellijk op de hoogte en heeft deze geen melding nodig.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Waarschuwingen van de volgende Azure Backup oplossingen worden hier weer gegeven:

- Back-ups van Azure-VM's
- Azure-bestandsback-ups
- Back-ups van Azure-workloads, zoals SQL
- Azure Backup-agent (MAB)

> [!NOTE]
> Waarschuwingen van System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup-Server (MABS) worden hier niet weer gegeven.

### <a name="alert-types"></a>Waarschuwingstypen

Waarschuwingen zijn gebaseerd op ernst van waarschuwingen en kunnen worden gedefinieerd in drie typen:

- **Kritiek**: in principe zou elke back-up-of herstel fout (gepland of door de gebruiker geactiveerd) leiden tot het genereren van een waarschuwing en worden weer gegeven als een kritieke waarschuwing en ook destructieve bewerkingen, zoals het verwijderen van back-ups.
- **Waarschuwing**: als de back-upbewerking is geslaagd, maar met weinig waarschuwingen, worden deze weer gegeven als waarschuwings meldingen.
- **Ter informatie**: vanaf vandaag wordt er geen informatieve waarschuwing gegenereerd door Azure backup service.

## <a name="notification-for-backup-alerts"></a>Melding voor back-upwaarschuwingen

> [!NOTE]
> De configuratie van de melding kan alleen worden gedaan via Azure Portal. Ondersteuning voor PS/CLI/REST API/Azure Resource Manager-sjablonen wordt niet ondersteund.

Zodra een waarschuwing is gegenereerd, worden gebruikers hiervan op de hoogte gebracht. Azure Backup biedt een ingebouwd meldings mechanisme via e-mail. Een kan afzonderlijke e-mail adressen of distributie lijsten opgeven die moeten worden gewaarschuwd wanneer er een waarschuwing wordt gegenereerd. U kunt ook kiezen of u wilt worden gewaarschuwd voor elke afzonderlijke waarschuwing of ze wilt groeperen in een samen vatting per uur en vervolgens een melding ontvangen.

![E-mail melding door RS-kluis inbouwen](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

> [!NOTE]
> De waarschuwingen voor SQL-back-ups worden geconsolideerd en het e-mail bericht wordt alleen voor de eerste keer verzonden. Maar als de waarschuwing wordt gedeactiveerd door de gebruiker, wordt door de volgende instantie een andere e-mail geactiveerd.

Wanneer de melding is geconfigureerd, ontvangt u een welkomst-of inkomend e-mail bericht. Hiermee wordt bevestigd dat Azure Backup e-mail berichten naar deze adressen kunt verzenden wanneer er een waarschuwing wordt gegenereerd.<br>

Als de frequentie is ingesteld op een samen vatting per uur en er binnen een uur een waarschuwing is gegenereerd en opgelost, zal deze geen deel uitmaken van het komende uur overzicht.

> [!NOTE]
>
> - Als er een destructieve bewerking wordt uitgevoerd, zoals het **stoppen van de beveiliging bij het verwijderen van gegevens** , wordt er een waarschuwing gegenereerd en wordt er een e-mail bericht verzonden naar de eigen aren van het abonnement, beheerders en mede beheerders, zelfs als er geen meldingen zijn geconfigureerd voor de herstel service-kluis.
> - Gebruik [log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)voor het configureren van een melding voor geslaagde taken.

## <a name="inactivating-alerts"></a>Waarschuwingen inactiveren

Als u een actieve waarschuwing wilt inactief maken/oplossen, klikt u op het lijst item dat overeenkomt met de waarschuwing die u wilt deactiveren. Hiermee opent u een scherm waarin gedetailleerde informatie over de waarschuwing wordt weer gegeven, met de knop ' inactief ' bovenaan. Als u op deze knop klikt, wordt de status van de waarschuwing gewijzigd in inactief. U kunt ook een waarschuwing inactief maken door met de rechter muisknop te klikken op het item in de lijst dat overeenkomt met die waarschuwing en ' inactief ' te selecteren.

![Waarschuwing voor RS-kluis inactivering](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Volgende stappen

[Azure backup-workloads bewaken met behulp van Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
