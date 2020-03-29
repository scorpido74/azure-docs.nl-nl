---
title: Een systeemreboot voor een Azure VM begrijpen | Microsoft Documenten
description: Hiermee worden de gebeurtenissen weergegeven die ervoor kunnen zorgen dat een vm opnieuw wordt opgestart
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e94ffb3d34082745c3d7ca86cfda2b93c0ed08da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919410"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Een systeemopnieuw opstarten voor Azure VM begrijpen

Azure virtual machines (VM's) kunnen soms opnieuw opstarten zonder aanwijsbare reden, zonder bewijs dat u de rebootbewerking hebt gestart. In dit artikel worden de acties en gebeurtenissen weergegeven die ervoor kunnen zorgen dat VM's opnieuw worden opgestart en wordt inzicht gegeven in hoe onverwachte opnieuw opstartende problemen kunnen worden voorkomen of de impact van dergelijke problemen kunnen worden verminderd.

## <a name="configure-the-vms-for-high-availability"></a>De VM's configureren voor hoge beschikbaarheid

De beste manier om een toepassing die op Azure wordt uitgevoerd te beschermen tegen vm-reboots en downtime, is door de VM's te configureren voor hoge beschikbaarheid.

Om dit redundantieniveau aan uw toepassing te bieden, raden we u aan twee of meer VM's te groeperen in een beschikbaarheidsset. Deze configuratie zorgt ervoor dat tijdens een geplande of ongeplande onderhoudsgebeurtenis ten minste één VM beschikbaar is en voldoet aan de 99,95 procent [Azure SLA.](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/)

Zie [De beschikbaarheid van VM's beheren](../windows/manage-availability.md) voor meer informatie over beschikbaarheidssets

## <a name="resource-health-information"></a>Informatie over resourcestatus

Azure Resource Health is een service die de status van afzonderlijke Azure-resources blootlegt en bruikbare richtlijnen biedt voor het oplossen van problemen. In een cloudomgeving waar het niet mogelijk is om direct toegang te krijgen tot servers of infrastructuurelementen, is het doel van Resource Health om de tijd die u besteedt aan het oplossen van problemen te verminderen. Het doel is met name om de tijd die u besteedt te verminderen om te bepalen of de oorzaak van het probleem in de toepassing of in een gebeurtenis binnen het Azure-platform ligt. Zie [Resourcestatus begrijpen en gebruiken](../../resource-health/resource-health-overview.md)voor meer informatie .

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Acties en gebeurtenissen die ertoe kunnen leiden dat de VM opnieuw wordt opgestart

### <a name="planned-maintenance"></a>Gepland onderhoud

Microsoft Azure voert regelmatig updates uit over de hele wereld om de betrouwbaarheid, prestaties en beveiliging van de hostinfrastructuur die ten grondslag ligt aan VM's te verbeteren. Veel van deze updates, waaronder geheugenbehoudende updates, worden uitgevoerd zonder enige impact op uw VM's of cloudservices.

Voor sommige updates is echter een reboot nodig. In dergelijke gevallen worden de VM's afgesloten terwijl we de infrastructuur patchen en vervolgens worden de VM's opnieuw opgestart.

Zie de artikelen die hier worden vermeld om te begrijpen wat azure gepland onderhoud is en hoe dit van invloed kan zijn op de beschikbaarheid van uw Linux-VM's. Deze artikelen bieden achtergrondinformatie over het proces van gepland onderhoud in Azure, en over hoe u gepland onderhoud kunt inplannen om de impact ervan nog verder te beperken.

- [Gepland onderhoud voor VM's in Azure](../windows/planned-maintenance.md)
- [Gepland onderhoud inplannen voor Azure-VM's](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Updates met geheugenbehoud

Voor deze klasse updates in Microsoft Azure ondervinden gebruikers geen invloed op hun actieve VM's. Dit zijn veelal updates van onderdelen of services die kunnen worden bijgewerkt zonder de actieve sessie te verstoren. Sommige zijn platform infrastructuur updates op het host besturingssysteem die kunnen worden toegepast zonder een reboot van de VM's.

Deze updates met geheugenbehoud worden uitgevoerd met technologie die in-place livemigratie mogelijk maakt. Wanneer deze wordt bijgewerkt, wordt de vm in een *onderbroken* status geplaatst. In deze status blijft de geheugeninhoud in het RAM behouden. Ondertussen ontvangt het besturingssysteem van de onderliggende host de noodzakelijke updates en patches. De VM wordt 30 seconden na het onderbreken weer hervat. Op dat moment wordt de klok van de VM automatisch gesynchroniseerd.

Vanwege de korte pauzeperiode vermindert het implementeren van updates via dit mechanisme de impact op de VM's sterk. Niet alle updates kunnen echter op deze manier worden geïmplementeerd. 

Updates voor meerdere exemplaren (voor VM's in een beschikbaarheidsset) worden telkens voor één updatedomein tegelijk toegepast.

> [!NOTE]
> Linux-machines met oude kernelversies worden tijdens deze updatemethode beïnvloed door een kernelpanic. Update naar versie 3.10.0-327.10.1 of hoger om dit probleem te voorkomen. Zie [Een Azure Linux VM op een kernel panics van 3.10 na een upgrade van het hostknooppunt](https://support.microsoft.com/help/3212236)voor meer informatie.

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Door de gebruiker geïnitieerde acties voor opnieuw opstarten of afsluiten

Als u een reboot uitvoert vanuit de Azure-portal, Azure PowerShell, command-line interface of REST API, u de gebeurtenis vinden in het [Azure Activity Log.](../../azure-monitor/platform/platform-logs-overview.md)

Als u de actie uitvoert vanaf het besturingssysteem van de VM, u de gebeurtenis vinden in de systeemlogboeken.

Andere scenario's die er meestal voor zorgen dat de VM opnieuw wordt opgestart, omvatten meerdere configuratiewijzigingsacties. Normaal gesproken ziet u een waarschuwingsbericht dat aangeeft dat het uitvoeren van een bepaalde actie zal resulteren in een herstart van de VM. Voorbeelden hiervan zijn vm-wijzigingen, het wijzigen van het wachtwoord van het beheerdersaccount en het instellen van een statisch IP-adres.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center en Windows Update

Azure Security Center controleert dagelijks Windows- en Linux-VM's op ontbrekende updates van het besturingssysteem. Security Center haalt een lijst met beschikbare beveiligings- en kritieke updates op uit Windows Update of Windows Server Update Services (WSUS), afhankelijk van welke service is geconfigureerd op een Windows VM. Security Center controleert ook op de nieuwste updates voor Linux-systemen. Als uw vm een systeemupdate mist, raadt Het Beveiligingscentrum u aan systeemupdates toe te passen. De toepassing van deze systeemupdates wordt beheerd via het Beveiligingscentrum in de Azure-portal. Nadat u een aantal updates hebt toegepast, is vm-rebootmogelijk vereist. Zie [Systeemupdates toepassen in Azure Security Center](../../security-center/security-center-apply-system-updates.md)voor meer informatie.

Net als on-premises servers pusht Azure geen updates van Windows Update naar Windows VM's, omdat deze machines bedoeld zijn om door hun gebruikers te worden beheerd. U wordt echter aangemoedigd om de automatische Windows Update-instelling ingeschakeld te laten. Automatische installatie van updates van Windows Update kan er ook voor zorgen dat opnieuw opstarten optreedt nadat de updates zijn toegepast. Zie [Veelgestelde vragen over Windows Update](https://support.microsoft.com/help/12373/windows-update-faq)voor meer informatie.

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Andere situaties die van invloed zijn op de beschikbaarheid van uw VM

Er zijn andere gevallen waarin Azure het gebruik van een vm actief kan opschorten. Je ontvangt e-mailmeldingen voordat deze actie wordt ondernomen, zodat je de kans hebt om de onderliggende problemen op te lossen. Voorbeelden van problemen die van invloed zijn op de beschikbaarheid van VM's zijn beveiligingsschendingen en het verlopen van betalingsmethoden.

### <a name="host-server-faults"></a>Fouten met hostservers

De VM wordt gehost op een fysieke server die wordt uitgevoerd in een Azure-datacenter. De fysieke server voert een agent genaamd de Host Agent in aanvulling op een paar andere Azure-componenten. Wanneer deze Azure-softwarecomponenten op de fysieke server niet meer reageren, activeert het bewakingssysteem een herstart van de hostserver om te proberen het herstel te proberen. De VM is meestal binnen vijf minuten weer beschikbaar en blijft op dezelfde host als voorheen.

Serverfouten worden meestal veroorzaakt door hardwarefouten, zoals het uitvallen van een harde schijf of een solid-state schijf. Azure controleert deze gebeurtenissen voortdurend, identificeert de onderliggende bugs en rolt updates uit nadat de beperking is geïmplementeerd en getest.

Omdat sommige hostserverfouten specifiek kunnen zijn voor die server, kan een herhaalde situatie voor het opnieuw opstarten van vm's worden verbeterd door de VM handmatig opnieuw te implementeren naar een andere hostserver. Deze bewerking kan worden geactiveerd door de optie **opnieuw implementeren** te gebruiken op de detailpagina van de VM of door de VM in de Azure-portal te stoppen en opnieuw te starten.

### <a name="auto-recovery"></a>Automatisch herstel

Als de hostserver om welke reden dan ook niet opnieuw kan worden opgestart, start het Azure-platform een automatische herstelactie om de defecte hostserver uit de rotatie te halen voor verder onderzoek. 

Alle VM's op die host worden automatisch verplaatst naar een andere, gezonde hostserver. Dit proces is meestal voltooid binnen 15 minuten. Zie [Automatisch herstel van VM's](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines)voor meer informatie over het proces voor automatisch herstel.

### <a name="unplanned-maintenance"></a>Niet-gepland onderhoud

In zeldzame gevallen moet het Azure-operations-team mogelijk onderhoudsactiviteiten uitvoeren om de algehele status van het Azure-platform te garanderen. Dit gedrag kan van invloed zijn op de beschikbaarheid van VM's en dit resulteert meestal in dezelfde actie voor automatisch herstel als eerder beschreven.  

Ongepland onderhoud omvat het volgende:

- Urgentnodedefragmentatie
- Dringende netwerkswitchupdates

### <a name="vm-crashes"></a>VM crasht

VM's kunnen opnieuw worden opgestart vanwege problemen binnen de VM zelf. De werkbelasting of rol die op de VM wordt uitgevoerd, kan leiden tot een bugcontrole in het gastbesturingssysteem. Bekijk de systeem- en toepassingslogboeken voor Windows VM's en de seriële logboeken voor Linux-VM's om de reden voor de crash te bepalen.

### <a name="storage-related-forced-shutdowns"></a>Aan opslag gerelateerd geforceerd afsluiten

VM's in Azure vertrouwen op virtuele schijven voor besturingssysteem en gegevensopslag die worden gehost op de Azure Storage-infrastructuur. Wanneer de beschikbaarheid of connectiviteit tussen de VM en de bijbehorende virtuele schijven gedurende meer dan 120 seconden wordt beïnvloed, voert het Azure-platform een gedwongen uitschakeling van de VM's uit om gegevensbeschadiging te voorkomen. De VM's worden automatisch weer ingeschakeld nadat de opslagconnectiviteit is hersteld. 

De duur van de shutdown kan zo kort zijn als vijf minuten, maar kan aanzienlijk langer zijn. Het volgende is een van de specifieke gevallen die is gekoppeld aan opslag-gerelateerde gedwongen shutdowns: 

**Overschrijding van IO-limieten**

VM's kunnen tijdelijk worden uitgeschakeld wanneer I/O-aanvragen consistent worden beperkt omdat het volume van I/O-bewerkingen per seconde (IOPS) de I/O-limieten voor de schijf overschrijdt. (Standaardschijfopslag is beperkt tot 500 IOPS.) Gebruik schijfstriping of configureer de opslagruimte in de gast-vm, afhankelijk van de werkbelasting om dit probleem te verhelpen. Zie Azure [VM's configureren voor optimale opslagprestaties voor](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)meer informatie.

### <a name="other-incidents"></a>Andere incidenten

In zeldzame omstandigheden kan een wijdverbreid probleem meerdere servers in een Azure-datacenter beïnvloeden. Als dit probleem optreedt, stuurt het Azure-team e-mailmeldingen naar de getroffen abonnementen. U het [Azure Service Health-dashboard](https://azure.microsoft.com/status/) en de Azure-portal controleren op de status van aanhoudende uitval en eerdere incidenten.
