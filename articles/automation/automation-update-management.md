---
title: Updatebeheer oplossing in azure
description: Dit artikel is bedoeld om u te helpen begrijpen hoe u de Azure Updatebeheer-oplossing kunt gebruiken om updates voor uw Windows-en Linux-computers te beheren.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1751e8d67f59285d011df33a2d4d1d6d8abcec6a
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376070"
---
# <a name="update-management-solution-in-azure"></a>Updatebeheer oplossing in azure

U kunt de Updatebeheer-oplossing in Azure Automation gebruiken om updates van besturings systemen te beheren voor uw Windows-en Linux-computers in azure, in on-premises omgevingen of in andere cloud providers. U kunt snel de status van de beschikbare updates op alle agentcomputers beoordelen en de procedure voor het installeren van vereiste updates voor servers beheren.

U kunt Updatebeheer voor virtuele machines rechtstreeks inschakelen vanuit uw Azure Automation-account. Zie [updates voor meerdere virtuele machines beheren](manage-update-multi.md)voor meer informatie over het inschakelen van updatebeheer voor virtuele machines vanuit uw Automation-account. U kunt Updatebeheer ook inschakelen voor een virtuele machine vanaf de pagina virtuele machine in de Azure Portal. Dit scenario is beschikbaar voor virtuele [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) -en [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) -machines.

> [!NOTE]
> Voor de Updatebeheer oplossing moet u een Log Analytics-werk ruimte koppelen aan uw Automation-account. Zie [Azure Workspace-toewijzingen](./how-to/region-mappings.md)voor een definitieve lijst met ondersteunde regio's. De regio toewijzingen hebben geen invloed op de mogelijkheid om virtuele machines in een andere regio dan uw Automation-account te beheren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Oplossingenoverzicht

Computers die worden beheerd door Updatebeheer gebruiken de volgende configuraties voor het uitvoeren van evaluatie-en update-implementaties:

* Micro soft Monitoring Agent (MMA) voor Windows of Linux
* PowerShell Desired State Configuration (DSC) voor Linux
* Automation Hybrid Runbook Worker
* Microsoft Update of Windows Server Update Services (WSUS) voor Windows-computers

Het volgende diagram toont een conceptueel overzicht van het gedrag en de gegevens stroom met de manier waarop de oplossing beveiligings updates evalueert en toepast op alle verbonden Windows Server-en Linux-computers in een werk ruimte:

![Proces stroom Updatebeheer](./media/automation-update-management/update-mgmt-updateworkflow.png)

Updatebeheer kan worden gebruikt om machines in meerdere abonnementen in dezelfde Tenant op te doen.

Zodra een pakket is vrijgegeven, duurt het 2-3 uur voordat de patch wordt weer gegeven voor Linux-machines voor evaluatie. Voor Windows-computers duurt 12-15 uur voordat de patch wordt weer gegeven voor evaluatie nadat deze is uitgebracht.

Nadat een computer een scan voor de compatibiliteit van updates heeft voltooid, stuurt de agent de gegevens bulksgewijs door naar Azure Monitor Logboeken. Op een Windows-computer wordt de compatibiliteits scan standaard elke 12 uur uitgevoerd.

Naast het scan schema wordt de controle op update vereisten binnen 15 minuten gestart nadat de MMA opnieuw is opgestart, vóór de installatie van de update en na de installatie van de update.

Voor een Linux-computer wordt standaard elk uur de compatibiliteits scan uitgevoerd. Als de MMA-agent opnieuw wordt gestart, wordt een nalevings scan binnen 15 minuten gestart.

De oplossing rapporteert hoe up-to-date de computer is gebaseerd op de bron die u hebt geconfigureerd om te synchroniseren met. Als de Windows-computer is geconfigureerd om te rapporteren aan WSUS, afhankelijk van de laatste synchronisatie van WSUS met Microsoft Update, kunnen de resultaten verschillen van wat er in micro soft-updates wordt weer gegeven. Dit gedrag is hetzelfde voor Linux-computers die zijn geconfigureerd om te rapporteren aan een lokale opslag plaats in plaats van naar een open bare opslag plaats.

> [!NOTE]
> Updatebeheer moet bepaalde Url's en poorten zijn ingeschakeld om de service goed te kunnen melden. Zie [netwerk planning voor Hybrid Workers voor](automation-hybrid-runbook-worker.md#network-planning)meer informatie over deze vereisten.

U kunt software-updates implementeren en installeren op computers die updates vereisen door daarvoor een planning in te stellen. Updates die zijn geclassificeerd als *optioneel* , worden niet opgenomen in het implementatie bereik voor Windows-computers. Alleen vereiste updates zijn opgenomen in het implementatie bereik.

De geplande implementatie definieert welke doel computers de toepasselijke updates ontvangen, hetzij door expliciet computers op te geven of door een [computer groep](../azure-monitor/platform/computer-groups.md) te selecteren die is gebaseerd op logboek zoekopdrachten van een specifieke set computers of een [Azure-query](automation-update-management-query-logs.md) Hiermee worden virtuele Azure-machines dynamisch geselecteerd op basis van opgegeven criteria. Deze groepen wijken af van de [Scope configuratie](../azure-monitor/insights/solution-targeting.md), die alleen wordt gebruikt om te bepalen op welke machines de Management Packs worden opgehaald die de oplossing inschakelen.

U kunt ook een planning opgeven die u wilt goed keuren en een periode instellen waarin updates kunnen worden geïnstalleerd. Deze periode wordt het onderhouds venster genoemd. Twintig minuten van het onderhouds venster is gereserveerd voor opnieuw opstarten als de computer opnieuw moet worden opgestart en u de juiste optie voor opnieuw opstarten hebt geselecteerd. Als patches langer duren dan verwacht en er minder dan twintig minuten in het onderhouds venster worden weer gegeven, wordt de computer niet opnieuw opgestart.

Updates worden geïnstalleerd door runbooks in Azure Automation. U kunt deze runbooks niet weer geven en de runbooks hebben geen configuratie nodig. Wanneer er een update-implementatie wordt gemaakt, maakt de update-implementatie een planning waarbij een Master update-runbook wordt gestart op het opgegeven tijdstip voor de inbegrepen computers. Het hoofd-runbook start een onderliggend runbook op elke agent om de vereiste updates te installeren.

Op de datum en tijd die zijn opgegeven in de update-implementatie, wordt de implementatie parallel uitgevoerd op de doel computers. Voorafgaand aan de installatie wordt een scan uitgevoerd om te controleren of de updates nog steeds vereist zijn. Als de updates niet zijn goedgekeurd in WSUS, mislukt de update-implementatie voor WSUS-client computers.

Het is niet mogelijk om een computer te registreren voor Updatebeheer in meer dan één Log Analytics-werk ruimten (multi-multihoming).

## <a name="clients"></a>Clients

### <a name="supported-client-types"></a>Ondersteunde client typen

In de volgende tabel ziet u een lijst met ondersteunde besturings systemen voor update-evaluaties. Voor patching is een Hybrid Runbook Worker vereist. Zie de installatie handleidingen voor [Windows HRW](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) en [Linux HRW](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)voor informatie over Hybrid Runbook worker vereisten.

|Besturingssysteem  |Opmerkingen  |
|---------|---------|
|Windows Server 2019 (Data Center/Data Center core/Standard)<br><br>Windows Server 2016 (Data Center/Data Center core/Standard)<br><br>Windows Server 2012 R2 (Data Center/Standard)<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 (RTM en SP1 Standard)||
|CentOS 6 (x86/x64) en 7 (x64)      | Linux-agents moeten toegang hebben tot een opslagplaats voor updates. Voor op classificatie gebaseerde patches moet ' yum ' worden geretourneerd om beveiligings gegevens te retour neren die geen deel uitmaakt van het CentOS. Zie [Update classificaties in Linux](#linux-2) voor meer informatie over op CentOS gebaseerde patches op basis van classificaties.          |
|Red Hat Enterprise 6 (x86/x64) en 7 (x64)     | Linux-agents moeten toegang hebben tot een opslagplaats voor updates.        |
|SUSE Linux Enterprise Server 11 (x86/x64) en 12 (x64)     | Linux-agents moeten toegang hebben tot een opslagplaats voor updates.        |
|Ubuntu 14,04 LTS, 16,04 LTS en 18,04 (x86/x64)      |Linux-agents moeten toegang hebben tot een opslagplaats voor updates.         |

> [!NOTE]
> Virtuele-machine schaal sets van Azure kunnen worden beheerd met Updatebeheer. Updatebeheer werkt op de instanties zelf en niet op de basis installatie kopie. U moet de updates op een incrementele manier plannen, alsof u niet alle VM-instanties tegelijk bijwerkt.
> U kunt VMSS-knoop punten toevoegen door de stappen onder [onboarding van een niet-Azure-machine](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)uit te voeren.

### <a name="unsupported-client-types"></a>Niet-ondersteunde client typen

De volgende tabel geeft een lijst van besturings systemen die niet worden ondersteund:

|Besturingssysteem  |Opmerkingen  |
|---------|---------|
|Windows-client     | Client besturingssystemen (zoals Windows 7 en Windows 10) worden niet ondersteund.        |
|Windows Server 2016 nano server     | Wordt niet ondersteund.       |
|Azure Kubernetes-service knooppunten | Wordt niet ondersteund. Gebruik het patch proces dat wordt beschreven in [beveiligings-en kernel-updates Toep assen op Linux-knoop punten in azure Kubernetes service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Client vereisten

Hieronder vindt u de informatie over specifieke vereisten voor het besturings systeem.  U moet ook de [netwerk planning](#ports) controleren voor meer informatie.

#### <a name="windows"></a>Windows

Windows-agents moeten worden geconfigureerd om te communiceren met een WSUS-server of moeten toegang hebben tot Microsoft Update.

U kunt Updatebeheer gebruiken met System Center Configuration Manager. Zie [System Center Configuration Manager integreren met updatebeheer](oms-solution-updatemgmt-sccmintegration.md#configuration)voor meer informatie over integratie scenario's. De [Windows-agent](../azure-monitor/platform/agent-windows.md) is vereist. De agent wordt automatisch geïnstalleerd als u een virtuele machine van Azure wilt voorbereiden.

Virtuele Windows-machines die zijn geïmplementeerd op basis van de Azure Marketplace, worden standaard ingesteld om automatische updates van Windows Update-service te ontvangen. Dit gedrag verandert niet wanneer u deze oplossing toevoegt of virtuele Windows-machines toevoegt aan uw werk ruimte. Als u updates niet actief beheert met behulp van deze oplossing, is het standaard gedrag van toepassing (om updates automatisch toe te passen).

> [!NOTE]
> Het is mogelijk dat een gebruiker groepsbeleid kan wijzigen zodat het opnieuw opstarten van de computer kan worden uitgevoerd door de gebruiker, niet door het systeem. Beheerde computers kunnen vastlopen als Updatebeheer geen rechten heeft om de computer opnieuw op te starten zonder hand matige interactie van de gebruiker.
>
> Zie [Groepsbeleid instellingen voor automatische updates configureren](https://docs.microsoft.com/en-us/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)voor meer informatie.

#### <a name="linux"></a>Linux

Voor Linux moet de computer toegang hebben tot een update opslagplaats. De update opslagplaats kan privé of openbaar zijn. TLS 1,1 of TLS 1,2 is vereist voor de interactie met Updatebeheer. Een Log Analytics-agent voor Linux die is geconfigureerd om te rapporteren aan meer dan één Log Analytics werk ruimten, wordt niet ondersteund met deze oplossing.  Op de computer moet ook python 2. x zijn geïnstalleerd.

Zie [log Analytics agent voor Linux](https://github.com/microsoft/oms-agent-for-linux)voor meer informatie over het installeren van de log Analytics-agent voor Linux en het downloaden van de meest recente versie. Zie [micro soft Monitoring Agent voor Windows](../log-analytics/log-analytics-windows-agent.md)voor meer informatie over het installeren van de log Analytics-agent voor Windows.

Virtuele machines die zijn gemaakt op basis van de installatie kopieën op Red Hat Enterprise Linux aanvraag (RHEL) die beschikbaar zijn in azure Marketplace, worden geregistreerd voor toegang tot de [Red Hat Update infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) die is geïmplementeerd in Azure. Elke andere Linux-distributie moet worden bijgewerkt vanuit de online bestands opslagplaats van de distributie door de ondersteunde methoden van de distributie te volgen.

## <a name="permissions"></a>Machtigingen

U hebt specifieke machtigingen nodig om update-implementaties te maken en te beheren. Zie op [rollen gebaseerde toegang updatebeheer](automation-role-based-access-control.md#update-management)voor meer informatie over deze machtigingen.

## <a name="solution-components"></a>Oplossingsonderdelen

De oplossing bestaat uit de volgende resources. De resources worden toegevoegd aan uw Automation-account. Ze zijn rechtstreeks verbonden agents of in een door Operations Manager verbonden beheer groep.

### <a name="hybrid-worker-groups"></a>Hybrid Worker-groepen

Nadat u deze oplossing hebt ingeschakeld, wordt elke Windows-computer die rechtstreeks is verbonden met uw Log Analytics-werk ruimte automatisch geconfigureerd als een Hybrid Runbook Worker ter ondersteuning van de runbooks die in deze oplossing zijn opgenomen.

Elke Windows-computer die wordt beheerd door de oplossing, wordt weer gegeven in het deel venster **Hybrid worker groups** als een **Hybrid worker-groep** voor het Automation-account. De oplossingen gebruiken de naam Conventie *hostname FQDN_GUID*. U kunt deze groepen niet richten op runbooks in uw account. Het mislukken als u probeert. Deze groepen zijn bedoeld om alleen de beheer oplossing te ondersteunen.

U kunt de Windows-computers toevoegen aan een Hybrid Runbook Worker groep in uw Automation-account ter ondersteuning van Automation-runbooks als u hetzelfde account gebruikt voor zowel de oplossing als het lidmaatschap van de Hybrid Runbook Worker-groep. Deze functionaliteit is toegevoegd aan versie 7.2.12024.0 van de Hybrid Runbook Worker.

### <a name="management-packs"></a>Management packs

Als uw System Center Operations Manager-beheer groep is verbonden met een Log Analytics-werk ruimte, worden de volgende Management Packs in Operations Manager geïnstalleerd. Deze Management Packs worden ook op rechtstreeks verbonden Windows-computers geïnstalleerd nadat u de oplossing hebt toegevoegd. U hoeft deze Management Packs niet te configureren of te beheren.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Implementatie MP bijwerken

> [!NOTE]
> Als u een Operations Manager 1807-of 2019-beheer groep hebt met agents die zijn geconfigureerd op het niveau van de beheer groep om te koppelen aan een werk ruimte, moet u de huidige tijdelijke oplossing voor het weer geven van **IsAutoRegistrationEnabled** op **True** overschrijven in de regel **Microsoft. intelligence packs. AzureAutomation. HybridAgent. init** .

Zie [Connect Operations Manager to Azure monitor logs](../azure-monitor/platform/om-agents.md)(Engelstalig) voor meer informatie over hoe oplossingen Management Packs worden bijgewerkt.

> [!NOTE]
> Voor systemen met de Operations Manager-agent om volledig te kunnen worden beheerd door Updatebeheer, moet de agent worden bijgewerkt naar micro soft monitoring agent. Zie [een Operations Manager-agent bijwerken](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)voor meer informatie over het bijwerken van de agent. Voor omgevingen met Operations Manager is het vereist dat u System Center Operations Manager 2012 R2 UR 14 of hoger uitvoert.

## <a name="data-collection"></a>Gegevensverzameling

### <a name="supported-agents"></a>Ondersteunde agents

De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing:

| Verbonden bron | Ondersteund | Beschrijving |
| --- | --- | --- |
| Windows-agents |Ja |De oplossing verzamelt informatie over systeem updates van Windows-agents en start vervolgens de installatie van de vereiste updates. |
| Linux-agents |Ja |De oplossing verzamelt informatie over systeem updates van Linux-agents en start vervolgens de installatie van de vereiste updates op ondersteunde distributies. |
| Beheergroep Operations Manager |Ja |De oplossing verzamelt informatie over systeemupdates van agents in een verboden beheergroep.<br/>Een directe verbinding van de Operations Manager agent naar Azure Monitor-Logboeken is niet vereist. Gegevens worden doorgestuurd van de beheer groep naar de Log Analytics-werk ruimte. |

### <a name="collection-frequency"></a>Verzamelingsfrequentie

Voor elke beheerde Windows-computer wordt twee keer per dag een scan uitgevoerd. Elke 15 minuten wordt de Windows-API aangeroepen om een query uit te zoeken naar de laatste update tijd om te bepalen of de status is gewijzigd. Als de status is gewijzigd, wordt een nalevings scan gestart.

Er wordt elk uur een scan uitgevoerd voor elke beheerde Linux-computer.

Het kan tussen 30 minuten en 6 uur duren voordat het dash board bijgewerkte gegevens van beheerde computers weergeeft.

Het gemiddelde Azure Monitor gegevens gebruik voor een machine met behulp van Updatebeheer is ongeveer 25MB per maand. Deze waarde is alleen een benadering en kan worden gewijzigd op basis van uw omgeving. Het is raadzaam om uw omgeving te controleren om precies het gebruik te zien dat u hebt.

## <a name="ports"></a>Netwerk planning

De volgende adressen zijn specifiek vereist voor Updatebeheer. Communicatie met deze adressen vindt plaats via poort 443.

|Open bare Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

Voor Windows-computers moet u ook verkeer toestaan voor eind punten die vereist zijn voor Windows Update.  U kunt een bijgewerkte lijst met vereiste eind punten vinden in [kwesties met betrekking tot http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Als u een lokale [Windows Update server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)hebt, moet u ook verkeer toestaan naar de server die is opgegeven in uw [WSUS-sleutel](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Voor Red Hat Linux-machines raadpleegt u [de IP-adressen voor de RHUI content delivery servers](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) voor vereiste eind punten. Raadpleeg de documentatie van de provider voor andere Linux-distributies.

Zie [Hybrid worker Role ports](automation-hybrid-runbook-worker.md#hybrid-worker-role)(Engelstalig) voor meer informatie over poorten die de Hybrid Runbook worker nodig heeft.

Het is raadzaam om de adressen te gebruiken die worden weer gegeven bij het definiëren van uitzonde ringen. Voor IP-adressen kunt u de [IP-adresbereiken van Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653)downloaden. Dit bestand wordt wekelijks bijgewerkt en weerspiegelt de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken.

Volg de instructies in [computers verbinden zonder Internet toegang](../azure-monitor/platform/gateway.md) voor het configureren van computers die geen toegang tot internet hebben.

## <a name="view-update-assessments"></a>Update-evaluaties weer geven

Selecteer in uw Automation-account **updatebeheer** om de status van uw computers weer te geven.

Deze weer gave bevat informatie over uw computers, ontbrekende updates, update-implementaties en geplande update-implementaties. In de **kolom compatibiliteit**ziet u de laatste keer dat de computer is geëvalueerd. In de **gereedheids** kolom van de Update-Agent kunt u zien of de status van de Update Agent is. Als er een probleem is, selecteert u de koppeling om naar probleemoplossings documentatie te gaan en leert u welke stappen u moet ondernemen om het probleem te verhelpen.

Als u een zoek opdracht in het logboek wilt uitvoeren die informatie over de computer, update of implementatie retourneert, selecteert u het item in de lijst. Het deel venster **Zoeken in Logboeken** wordt geopend met een query voor het geselecteerde item:

![Standaard weergave Updatebeheer](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Ontbrekende updates weer geven

Selecteer **ontbrekende updates** om de lijst met updates weer te geven die ontbreken op uw computers. Elke update wordt weer gegeven en kan worden geselecteerd. Informatie over het aantal machines dat moet worden bijgewerkt, het besturings systeem en een koppeling voor meer informatie wordt weer gegeven. In het deel venster **Zoeken in Logboeken** ziet u meer informatie over de updates.

![Ontbrekende updates](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Update classificaties

De volgende tabellen geven een lijst van de update classificaties in Updatebeheer, met een definitie voor elke classificatie.

### <a name="windows"></a>Windows

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële updates     | Een update voor een specifiek probleem dat betrekking heeft op een kritieke bug die niet aan beveiliging voldoet.        |
|Beveiligingsupdates     | Een update voor een productspecifiek, beveiligings probleem.        |
|Updatepakketten     | Een cumulatieve set met hotfixes die samen zijn verpakt voor een eenvoudige implementatie.        |
|Functiepakketten     | Nieuwe product functies die worden gedistribueerd buiten een product release.        |
|Servicepacks     | Een cumulatieve set met hotfixes die op een toepassing worden toegepast.        |
|Definitie-updates     | Een update van virus-of andere definitie bestanden.        |
|Tools     | Een hulp programma of functie waarmee u een of meer taken kunt volt ooien.        |
|Updates     | Een update voor een toepassing of bestand dat momenteel is geïnstalleerd.        |

### <a name="linux-2"></a>Spreek

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële en beveiligingsupdates     | Updates voor een specifiek probleem of een productspecifiek beveiligings probleem.         |
|Andere Updates     | Alle andere updates die niet kritiek zijn of geen beveiligings updates zijn.        |

Voor Linux kan Updatebeheer een onderscheid maken tussen essentiële updates en beveiliging in de Cloud, terwijl evaluatie gegevens worden weer gegeven vanwege gegevens verrijking in de Cloud. Voor patching is Updatebeheer afhankelijk van de classificatie gegevens die op de computer beschikbaar zijn. In tegens telling tot andere distributies is deze informatie niet beschikbaar in CentOS. Als er CentOS-machines zijn geconfigureerd op een manier om beveiligings gegevens te retour neren voor de volgende opdracht, kan Updatebeheer patch op basis van classificaties.

```bash
sudo yum -q --security check-update
```

Er is momenteel geen methode die wordt ondersteund voor het inschakelen van systeem eigen classificatie-gegevens beschikbaarheid op CentOS. Op dit moment wordt alleen ondersteuning voor de beste werk belasting gegeven aan klanten die deze zelf kunnen hebben ingeschakeld.

## <a name="integrate-with-system-center-configuration-manager"></a>Integreren met System Center Configuration Manager

Klanten die hebben geïnvesteerd in System Center Configuration Manager voor het beheren van Pc's, servers en mobiele apparaten, zijn ook afhankelijk van de kracht en de loop tijd van Configuration Manager om hen te helpen bij het beheren van software-updates. Configuration Manager maakt deel uit van de cyclus van software-update beheer (SUM).

Zie [System Center Configuration Manager met updatebeheer integreren](oms-solution-updatemgmt-sccmintegration.md)voor meer informatie over het integreren van de beheer oplossing met System Center Configuration Manager.

### <a name="third-party-patches-on-windows"></a>Patches van derden in Windows

Updatebeheer is afhankelijk van de lokaal geconfigureerde update opslagplaats voor patches die worden ondersteund door Windows-systemen. Dit is WSUS of Windows Update. Met hulpprogram ma's als [System Center updates Publisher](/sccm/sum/tools/updates-publisher) (updates Publisher) kunt u aangepaste updates publiceren in WSUS. Met dit scenario kunnen Updatebeheer patches voor machines die gebruikmaken van System Center Configuration Manager als update opslagplaats met software van derden. Zie [install updates Publisher](/sccm/sum/tools/install-updates-publisher)(Engelstalig) voor meer informatie over het configureren van updates Publisher.

## <a name="patch-linux-machines"></a>Linux-machines bijwerken

In de volgende secties worden mogelijke problemen met Linux-patches uitgelegd.

### <a name="unexpected-os-level-upgrades"></a>Onverwachte upgrades op besturingssysteem niveau

Bij sommige Linux-varianten, zoals Red Hat Enterprise Linux, kunnen upgrades op besturingssysteem niveau worden uitgevoerd via pakketten. Dit kan leiden tot Updatebeheer worden uitgevoerd, waarbij het versie nummer van het besturings systeem verandert. Omdat Updatebeheer dezelfde methoden gebruikt voor het bijwerken van pakketten die een beheerder lokaal zou gebruiken op de Linux-computer, is dit gedrag opzettelijk.

Gebruik de functie **uitsluiting** om te voor komen dat de versie van het besturings systeem wordt bijgewerkt via updatebeheer uitvoeringen.

In Red Hat Enterprise Linux is de pakket naam die moet worden uitgesloten, RedHat-release-server. x86_64.

![Pakketten die moeten worden uitgesloten voor Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Essentiële/beveiligings patches worden niet toegepast

Wanneer u updates op een Linux-machine implementeert, kunt u update classificaties selecteren. Hiermee worden de updates die op de computer worden toegepast, gefilterd die voldoen aan de opgegeven criteria. Dit filter wordt lokaal op de computer toegepast wanneer de update wordt geïmplementeerd.

Omdat Updatebeheer verrijking update uitvoert in de Cloud, kunnen sommige updates worden gemarkeerd in Updatebeheer als gevolg van de beveiliging, zelfs als de lokale computer deze informatie niet bevat. Als u essentiële updates toepast op een Linux-computer, zijn er mogelijk updates die niet zijn gemarkeerd als beveiligings impact op die computer en worden de updates niet toegepast.

Updatebeheer kunt de computer echter toch als niet-compatibel melden, omdat deze aanvullende informatie over de relevante update heeft.

Het implementeren van updates via update classificatie werkt niet CentOS uit het vak. Als u updates voor CentOS correct wilt implementeren, selecteert u alle classificaties om te controleren of er updates zijn toegepast. Voor SUSE selecteert u *alleen* ' andere updates ' als de classificatie kan ertoe leiden dat sommige beveiligings updates ook worden geïnstalleerd als er eerst beveiligings updates met betrekking tot Zypper (pakket beheer) of de afhankelijkheden ervan zijn vereist. Dit gedrag is een beperking van Zypper. In sommige gevallen kan het nodig zijn om de update-implementatie opnieuw uit te voeren. Controleer het Update logboek om dit te controleren.

### <a name="multi-tenant"></a>Implementaties van cross-Tenant updates

Als u computers in een andere Azure-Tenant rapporteert om Updatebeheer die u moet patchen, moet u de volgende tijdelijke oplossing gebruiken om ze te laten plannen. U kunt de cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) gebruiken met de switch `-ForUpdate` om een schema te maken en de cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) te gebruiken en de computers in de andere Tenant door te geven aan de para meter `-NonAzureComputer`. In het volgende voor beeld ziet u een voor beeld van hoe u dit doet:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>Updatebeheer inschakelen

Als u met patches-systemen wilt beginnen, moet u de Updatebeheer-oplossing inschakelen. Er zijn veel manieren om machines te Updatebeheer. Hieronder vindt u de aanbevolen en ondersteunde manieren om de oplossing vrij te maken:

* [Van een virtuele machine](automation-onboard-solutions-from-vm.md)
* [Van surfen op meerdere computers](automation-onboard-solutions-from-browse.md)
* [Vanuit uw Automation-account](automation-onboard-solutions-from-automation-account.md)
* [Met een Azure Automation runbook](automation-onboard-solutions.md)

## <a name="next-steps"></a>Volgende stappen

Ga verder met de zelf studie voor meer informatie over het beheren van updates voor uw virtuele Windows-machines.

> [!div class="nextstepaction"]
> [Updates en patches voor uw Azure Windows-Vm's beheren](automation-tutorial-update-management.md)

* Zoek opdrachten in Logboeken in [Azure monitor logboeken](../log-analytics/log-analytics-log-searches.md) gebruiken om gedetailleerde update gegevens weer te geven.
* [Waarschuwingen maken](automation-tutorial-update-management.md#configure-alerts) voor de implementatie status van de update.

* Zie [Software-update configuraties](/rest/api/automation/softwareupdateconfigurations) voor meer informatie over het werken met updatebeheer via de rest API.
* Zie [problemen oplossen updatebeheer](troubleshoot/update-management.md) voor meer informatie over het oplossen van problemen met de updatebeheer.
