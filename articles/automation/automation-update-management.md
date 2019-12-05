---
title: Updatebeheer oplossing in azure
description: In dit artikel wordt beschreven hoe u de Azure Updatebeheer-oplossing gebruikt voor het beheren van updates voor uw Windows-en Linux-computers.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 06d7ede1e9b91832f908c87a22cca37ec2866365
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806538"
---
# <a name="update-management-solution-in-azure"></a>Updatebeheer oplossing in azure

U kunt de Updatebeheer-oplossing in Azure Automation gebruiken om updates van besturings systemen te beheren voor uw Windows-en Linux-computers in azure, in on-premises omgevingen en in andere cloud providers. U kunt snel de status van de beschikbare updates op alle agentcomputers beoordelen en de procedure voor het installeren van vereiste updates voor servers beheren.

U kunt Updatebeheer voor virtuele machines (Vm's) rechtstreeks vanuit uw Azure Automation-account inschakelen. Zie [updates voor meerdere virtuele machines beheren](manage-update-multi.md)voor meer informatie. U kunt Updatebeheer ook inschakelen voor een VM op de pagina virtuele machine in de Azure Portal. Dit scenario is beschikbaar voor [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) -en [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) -vm's.

> [!NOTE]
> Voor de Updatebeheer oplossing moet u een Log Analytics-werk ruimte koppelen aan uw Automation-account. Zie [Azure Workspace-toewijzingen](./how-to/region-mappings.md)voor een definitieve lijst met ondersteunde regio's. De regio toewijzingen hebben geen invloed op de mogelijkheid om Vm's te beheren in een andere regio dan uw Automation-account.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Oplossingenoverzicht

Computers die worden beheerd door Updatebeheer gebruiken de volgende configuraties voor het uitvoeren van analyses en het bijwerken van implementaties:

* Microsoft Monitoring Agent (MMA) voor Windows of Linux
* PowerShell Desired State Configuration (DSC) voor Linux
* Automation Hybrid Runbook Worker
* Microsoft Update of Windows Server Update Services (WSUS) voor Windows-computers

In het volgende diagram ziet u hoe de oplossing beveiligings updates evalueert en toepast op alle verbonden Windows Server-en Linux-computers in een werk ruimte:

![Proces stroom Updatebeheer](./media/automation-update-management/update-mgmt-updateworkflow.png)

Updatebeheer kan worden gebruikt voor de systeemeigen onboarding van computers in meerdere abonnementen in dezelfde tenant.

Nadat een pakket is vrijgegeven, duurt het 2 tot 3 uur voordat de patch wordt weer gegeven voor Linux-machines voor evaluatie. Voor Windows-computers duurt het 12 tot 15 uur voordat de patch wordt weer gegeven voor evaluatie nadat deze is uitgebracht.

Nadat een computer een scan voor de compatibiliteit van updates heeft voltooid, stuurt de agent de gegevens bulksgewijs door naar Azure Monitor Logboeken. Op een Windows-computer wordt de compatibiliteits scan standaard elke 12 uur uitgevoerd.

Naast het scan schema wordt de controle op update vereisten binnen 15 minuten gestart nadat de MMA opnieuw is opgestart, vóór de installatie van de update en na de installatie van de update.

Voor een Linux-computer wordt standaard elk uur de compatibiliteits scan uitgevoerd. Als de MMA-agent opnieuw wordt gestart, wordt een nalevings scan binnen 15 minuten gestart.

De oplossing rapporteert hoe up-to-date de computer is gebaseerd op de bron die u hebt geconfigureerd om te synchroniseren met. Als de Windows-computer is geconfigureerd om te rapporteren aan WSUS, afhankelijk van de laatste synchronisatie van WSUS met Microsoft Update, kunnen de resultaten afwijken van wat Microsoft Update laat zien. Dit gedrag is hetzelfde voor Linux-computers die zijn geconfigureerd om te rapporteren aan een lokale opslag plaats in plaats van naar een open bare opslag plaats.

> [!NOTE]
> Updatebeheer moet bepaalde Url's en poorten zijn ingeschakeld om de service goed te kunnen melden. Zie [netwerk planning voor Hybrid Workers voor](automation-hybrid-runbook-worker.md#network-planning)meer informatie over deze vereisten.

U kunt software-updates implementeren en installeren op computers die updates vereisen door daarvoor een planning in te stellen. Updates die zijn geclassificeerd als *optioneel* , worden niet opgenomen in het implementatie bereik voor Windows-computers. Alleen vereiste updates zijn opgenomen in het implementatie bereik.

De geplande implementatie definieert welke doel computers de toepasselijke updates ontvangen. Dit doet u door bepaalde computers expliciet op te geven of door een [computer groep](../azure-monitor/platform/computer-groups.md) te selecteren die is gebaseerd op logboek zoekopdrachten van een specifieke set computers (of op een [Azure-query](automation-update-management-query-logs.md) waarmee dynamisch virtuele Azure-machines worden geselecteerd op basis van opgegeven criteria). Deze groepen wijken af van de [Scope configuratie](../azure-monitor/insights/solution-targeting.md), die alleen wordt gebruikt om te bepalen op welke machines de Management Packs worden opgehaald die de oplossing inschakelen.

U kunt ook een schema opgeven dat u wilt goed keuren en een tijds periode instellen waarin updates kunnen worden geïnstalleerd. Deze periode wordt het onderhouds venster genoemd. Een periode van 20 minuten van het onderhouds venster is gereserveerd voor opnieuw opstarten, ervan uitgaande dat er een is vereist en u de juiste opstart optie hebt geselecteerd. Als de patch langer duurt dan verwacht en er minder dan 20 minuten in het onderhouds venster wordt weer gegeven, wordt de computer niet opnieuw opgestart.

Updates worden geïnstalleerd door runbooks in Azure Automation. U kunt deze runbooks niet weer geven en er is geen configuratie vereist. Wanneer een update-implementatie wordt gemaakt, wordt er een planning gemaakt waarmee een Master update-runbook op de opgegeven tijd wordt gestart voor de inbegrepen computers. Het hoofd-runbook start een onderliggend runbook op elke agent om de vereiste updates te installeren.

Op de datum en tijd die zijn opgegeven in de update-implementatie, wordt de implementatie parallel uitgevoerd op de doel computers. Voorafgaand aan de installatie wordt een scan uitgevoerd om te controleren of de updates nog steeds vereist zijn. Voor WSUS-client computers, als de updates niet zijn goedgekeurd in WSUS, mislukt de update-implementatie.

Het is niet mogelijk om een computer te registreren voor Updatebeheer in meer dan één Log Analytics-werk ruimte (multihoming).

## <a name="clients"></a>Clients

### <a name="supported-client-types"></a>Ondersteunde client-typen

De volgende tabel geeft een lijst van de ondersteunde besturings systemen voor update-evaluaties. Voor patching is een Hybrid Runbook Worker vereist. Zie de installatie handleidingen voor het installeren van een [Windows-Hybrid Runbook worker](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) en een Linux- [Hybrid Runbook worker](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)voor meer informatie over Hybrid Runbook worker vereisten.

|Besturingssysteem  |Opmerkingen  |
|---------|---------|
|Windows Server 2019 (Data Center/Data Center core/Standard)<br><br>Windows Server 2016 (Data Center/Data Center core/Standard)<br><br>Windows Server 2012 R2 (Data Center/Standard)<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 (RTM en SP1 Standard)||
|CentOS 6 (x86/x64) en 7 (x64)      | Linux-agents moeten toegang hebben tot een opslagplaats voor updates. Voor op classificatie gebaseerde patches is `yum` vereist om beveiligings gegevens te retour neren die CentOS niet hebben in de RTM-releases. Zie [Update classificaties in Linux](#linux-2)voor meer informatie over op CentOS gebaseerde patches op basis van classificatie.          |
|Red Hat Enterprise 6 (x86/x64) en 7 (x64)     | Linux-agents moeten toegang hebben tot een opslagplaats voor updates.        |
|SUSE Linux Enterprise Server 11 (x86/x64) en 12 (x64)     | Linux-agents moeten toegang hebben tot een opslagplaats voor updates.        |
|Ubuntu 14,04 LTS, 16,04 LTS en 18,04 (x86/x64)      |Linux-agents moeten toegang hebben tot een opslagplaats voor updates.         |

> [!NOTE]
> Virtuele-machine schaal sets van Azure kunnen worden beheerd via Updatebeheer. Updatebeheer werkt op de instanties zelf en niet op basis van de installatie kopie. U moet de updates op een incrementele manier plannen, zodat niet alle VM-exemplaren tegelijk worden bijgewerkt.
> U kunt knoop punten voor schaal sets voor virtuele machines toevoegen door de stappen onder [onboarding van een niet-Azure-computer](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)uit te voeren.

### <a name="unsupported-client-types"></a>Niet-ondersteunde client-typen

De volgende tabel bevat een lijst met niet-ondersteunde besturings systemen:

|Besturingssysteem  |Opmerkingen  |
|---------|---------|
|Windows-client     | Client-besturingssystemen (zoals Windows 7 en Windows 10) worden niet ondersteund.        |
|Windows Server 2016 Nano Server     | Wordt niet ondersteund.       |
|Azure Kubernetes-service knooppunten | Wordt niet ondersteund. Gebruik het patch proces dat wordt beschreven in [beveiligings-en kernel-updates Toep assen op Linux-knoop punten in azure Kubernetes service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Clientvereisten

In de volgende informatie worden OS-specifieke client vereisten beschreven. Zie [netwerk planning](#ports)voor meer informatie.

#### <a name="windows"></a>Windows

Windows-agents moeten worden geconfigureerd om te communiceren met een WSUS-server of moeten toegang hebben tot Microsoft Update.

U kunt Updatebeheer gebruiken met System Center Configuration Manager. Zie [System Center Configuration Manager integreren met updatebeheer](oms-solution-updatemgmt-sccmintegration.md#configuration)voor meer informatie over integratie scenario's. De [Windows-agent](../azure-monitor/platform/agent-windows.md) is vereist. De agent wordt automatisch geïnstalleerd als u een virtuele Azure-machine onboardeert.

Standaard worden Windows-Vm's die zijn geïmplementeerd vanuit Azure Marketplace ingesteld voor het ontvangen van automatische updates van Windows Update service. Dit gedrag verandert niet wanneer u deze oplossing toevoegt of Windows-Vm's toevoegt aan uw werk ruimte. Als u updates niet actief beheert met behulp van deze oplossing, is het standaard gedrag van toepassing (om updates automatisch toe te passen).

> [!NOTE]
> Een gebruiker kan groepsbeleid wijzigen zodat het opnieuw opstarten van de machine alleen door de gebruiker kan worden uitgevoerd, niet door het systeem. Beheerde computers kunnen vastraken als Updatebeheer geen rechten heeft om de computer opnieuw op te starten zonder hand matige interactie van de gebruiker.
>
> Zie [Groepsbeleid instellingen voor automatische updates configureren](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)voor meer informatie.

#### <a name="linux"></a>Linux

Voor Linux moet de computer toegang hebben tot een update opslagplaats. De update opslagplaats kan privé of openbaar zijn. TLS 1,1 of TLS 1,2 is vereist voor de interactie met Updatebeheer. Een Log Analytics-agent voor Linux die is geconfigureerd om te rapporteren aan meer dan één Log Analytics-werk ruimte, wordt niet ondersteund met deze oplossing. Op de computer moet ook python 2. x zijn geïnstalleerd.

Zie [log Analytics agent voor Linux](https://github.com/microsoft/oms-agent-for-linux)voor meer informatie over het installeren van de log Analytics-agent voor Linux en het downloaden van de meest recente versie. Zie [Windows-computers verbinden met Azure monitor](../log-analytics/log-analytics-windows-agent.md)voor meer informatie over het installeren van de log Analytics-agent voor Windows.

Vm's die zijn gemaakt op basis van de installatie kopieën op Red Hat Enterprise Linux aanvraag (RHEL) die beschikbaar zijn in azure Marketplace, worden geregistreerd voor toegang tot de [Red Hat Update infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) die is geïmplementeerd in Azure. Elke andere Linux-distributie moet worden bijgewerkt vanuit de online bestands opslagplaats van de distributie met behulp van de ondersteunde methoden van de distributie.

## <a name="permissions"></a>Machtigingen

U hebt specifieke machtigingen nodig om update-implementaties te maken en te beheren. Zie op [rollen gebaseerde toegang – updatebeheer](automation-role-based-access-control.md#update-management)voor meer informatie over deze machtigingen.

## <a name="solution-components"></a>Oplossingsonderdelen

De oplossing bestaat uit de volgende resources. De resources worden toegevoegd aan uw Automation-account. Ze zijn rechtstreeks verbonden agents of in een door Operations Manager verbonden beheer groep.

### <a name="hybrid-worker-groups"></a>Hybrid Worker-groepen

Nadat u deze oplossing hebt ingeschakeld, wordt elke Windows-computer die rechtstreeks is verbonden met uw Log Analytics-werk ruimte automatisch geconfigureerd als een Hybrid Runbook Worker ter ondersteuning van de runbooks die in deze oplossing zijn opgenomen.

Elke Windows-computer die wordt beheerd door de oplossing, wordt weer gegeven in het deel venster **Hybrid worker groups** als een **Hybrid worker-groep** voor het Automation-account. De oplossingen gebruiken de *hostname-FQDN_GUID* naam Conventie. U kunt deze groepen niet richten op runbooks in uw account. Als u probeert, mislukt de poging. Deze groepen zijn bedoeld om alleen de beheer oplossing te ondersteunen.

U kunt de Windows-computers toevoegen aan een Hybrid Runbook Worker groep in uw Automation-account ter ondersteuning van Automation-runbooks als u hetzelfde account gebruikt voor zowel de oplossing als het lidmaatschap van de Hybrid Runbook Worker-groep. Deze functionaliteit is toegevoegd aan versie 7.2.12024.0 van de Hybrid Runbook Worker.

### <a name="management-packs"></a>Management packs

Als de beheergroep van uw System Center Operations Manager is verbonden met een Log Analytics-werkruimte, worden de volgende management packs geïnstalleerd in Operations Manager. Zodra u de oplossing hebt toegevoegd, worden deze management packs ook op rechtstreeks verbonden Windows-computers geïnstalleerd. U hoeft deze management packs niet te configureren of te beheren.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Implementatie MP bijwerken

> [!NOTE]
> Stel dat u een Operations Manager 1807-of 2019-beheer groep hebt met agents die zijn geconfigureerd op het niveau van de beheer groep om ze te koppelen aan een werk ruimte. De huidige tijdelijke oplossing om ze weer te geven, is om **IsAutoRegistrationEnabled** te overschrijven in **True** in de regel **micro soft. intelligence packs. AzureAutomation. HybridAgent. init** .

Zie [Connect Operations Manager to Azure monitor logs](../azure-monitor/platform/om-agents.md)(Engelstalig) voor meer informatie over hoe oplossingen Management Packs worden bijgewerkt.

> [!NOTE]
> Voor systemen met de Operations Manager-agent: voor een agent die volledig wordt beheerd door Updatebeheer, moet de agent worden bijgewerkt naar de MMA. Zie [een Operations Manager-agent bijwerken](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)voor meer informatie over het bijwerken van de agent. In omgevingen waarin Operations Manager wordt gebruikt, moet u System Center Operations Manager 2012 R2 UR 14 of hoger uitvoeren.

## <a name="data-collection"></a>Gegevensverzameling

### <a name="supported-agents"></a>Ondersteunde agents

De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing:

| Verbonden bron | Ondersteund | Beschrijving |
| --- | --- | --- |
| Windows-agents |Ja |De oplossing verzamelt informatie over systeem updates van Windows-agents en start vervolgens de installatie van de vereiste updates. |
| Linux-agents |Ja |De oplossing verzamelt informatie over systeem updates van Linux-agents en start vervolgens de installatie van de vereiste updates op ondersteunde distributies. |
| Beheergroep Operations Manager |Ja |De oplossing verzamelt informatie over systeemupdates van agents in een verboden beheergroep.<br/><br/>Een directe verbinding van de Operations Manager agent naar Azure Monitor-Logboeken is niet vereist. Gegevens uit de beheergroep doorgestuurd naar de Log Analytics-werkruimte. |

### <a name="collection-frequency"></a>Verzamelingsfrequentie

Voor elke beheerde Windows-computer wordt twee keer per dag een scan uitgevoerd. Elke 15 minuten wordt de Windows-API aangeroepen om een query uit te zoeken naar de laatste update tijd om te bepalen of de status is gewijzigd. Als de status is gewijzigd, wordt een nalevings scan gestart.

Er wordt elk uur een scan uitgevoerd voor elke beheerde Linux-computer.

Het kan dertig minuten tot zes uur duren voordat er in het dashboard bijgewerkte gegevens van beheerde computers worden weergegeven.

Het gemiddelde gegevens gebruik door Azure Monitor logboeken voor een machine met Updatebeheer is ongeveer 25 MB per maand. Deze waarde is alleen een benadering en is onderhevig aan wijzigingen, afhankelijk van uw omgeving. U wordt aangeraden uw omgeving te bewaken om uw exacte gebruik bij te houden.

## <a name="ports"></a>Netwerk planning

De volgende adressen zijn specifiek vereist voor Updatebeheer. Communicatie met deze adressen vindt plaats via poort 443.

|Openbare Azure-peering  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

Voor Windows-computers moet u ook verkeer toestaan voor eind punten die vereist zijn voor Windows Update. U kunt een bijgewerkte lijst met vereiste eind punten vinden in [kwesties met betrekking tot http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Als u een lokale [Windows Update server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)hebt, moet u ook verkeer toestaan naar de server die is opgegeven in uw [WSUS-sleutel](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Zie IP-adressen voor de vereiste eind punten voor [de RHUI content delivery servers](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) voor Red Hat Linux-machines. Raadpleeg de documentatie van uw provider voor andere Linux-distributies.

Zie [Hybrid worker Role ports](automation-hybrid-runbook-worker.md#hybrid-worker-role)(Engelstalig) voor meer informatie over poorten die de Hybrid Runbook worker nodig heeft.

U wordt aangeraden de adressen te gebruiken die worden weer gegeven bij het definiëren van uitzonde ringen. Voor IP-adressen kunt u de [IP-adresbereiken van Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653)downloaden. Dit bestand wordt wekelijks bijgewerkt en weerspiegelt de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken.

Volg de instructies in [computers verbinden zonder Internet toegang](../azure-monitor/platform/gateway.md) voor het configureren van computers die geen toegang tot internet hebben.

## <a name="view-update-assessments"></a>Update-evaluaties bekijken

Selecteer in uw Automation-account **updatebeheer** om de status van uw computers weer te geven.

Deze weer gave bevat informatie over uw computers, ontbrekende updates, update-implementaties en geplande update-implementaties. In de kolom **compatibiliteit** ziet u de laatste keer dat de computer is geëvalueerd. In de **gereedheids** kolom van de Update-Agent kunt u de status van de Update Agent controleren. Als er een probleem is, selecteert u de koppeling om naar probleemoplossings documentatie te gaan waarmee u het probleem kunt verhelpen.

Als u een zoek opdracht in het logboek wilt uitvoeren die informatie over de computer, update of implementatie retourneert, selecteert u het bijbehorende item in de lijst. Het deel venster **Zoeken in Logboeken** wordt geopend met een query voor het geselecteerde item:

![Standaard weergave Updatebeheer](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Ontbrekende updates weer geven

Selecteer **ontbrekende updates** om de lijst met updates weer te geven die ontbreken op uw computers. Elke update wordt weer gegeven en kan worden geselecteerd. Informatie over het aantal machines dat moet worden bijgewerkt, het besturings systeem en een koppeling voor meer informatie wordt weer gegeven. In het deel venster **Zoeken in Logboeken** ziet u meer informatie over de updates.

![Ontbrekende updates](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Updateclassificaties

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

### <a name="linux-2"></a>Linux

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële en beveiligingsupdates     | Updates voor een specifiek probleem of een productspecifiek beveiligings probleem.         |
|Andere Updates     | Alle andere updates die niet kritiek zijn of die geen beveiligings updates zijn.        |

Voor Linux kan Updatebeheer een onderscheid maken tussen essentiële updates en beveiligings updates in de Cloud, terwijl evaluatie gegevens worden weer gegeven vanwege gegevens verrijking in de Cloud. Voor patching is Updatebeheer afhankelijk van de classificatie gegevens die op de computer beschikbaar zijn. In tegens telling tot andere distributies is CentOS deze informatie niet beschikbaar in de RTM-versie. Als er CentOS machines zijn geconfigureerd voor het retour neren van beveiligings gegevens voor de volgende opdracht, kunt Updatebeheer patch op basis van classificaties.

```bash
sudo yum -q --security check-update
```

Er is momenteel geen ondersteunde methode voor het inschakelen van systeem eigen classificatie-gegevens beschikbaarheid op CentOS. Op dit moment wordt alleen ondersteuning voor de beste werk belasting gegeven aan klanten die deze zelf kunnen hebben ingeschakeld.

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

### <a name="criticalsecurity-patches-arent-applied"></a>Essentiële/beveiligings patches worden niet toegepast

Wanneer u updates op een Linux-machine implementeert, kunt u update classificaties selecteren. Met deze optie worden de updates die op de computer worden toegepast, gefilterd die voldoen aan de opgegeven criteria. Dit filter wordt lokaal op de computer toegepast wanneer de update wordt geïmplementeerd.

Omdat Updatebeheer verrijking update uitvoert in de Cloud, kunnen sommige updates worden gemarkeerd in Updatebeheer als gevolg van een beveiligings effect, zelfs als de lokale computer deze informatie niet bevat. Als u essentiële updates toepast op een Linux-computer, zijn er mogelijk updates die niet zijn gemarkeerd als een beveiligings effect op die computer en daarom worden de updates niet toegepast. Updatebeheer kan de computer echter toch als niet-compatibel melden, omdat deze aanvullende informatie over de relevante update heeft.

Het implementeren van updates op update classificatie werkt niet in de RTM-versies van CentOS. Als u updates voor CentOS correct wilt implementeren, selecteert u alle classificaties om er zeker van te zijn dat er updates worden toegepast. Voor SUSE selecteert u *alleen* **andere updates** als de classificatie kan ertoe leiden dat bepaalde beveiligings updates ook worden geïnstalleerd als de beveiligings updates met betrekking tot Zypper (pakket beheer) of de afhankelijkheden hiervan eerst zijn vereist. Dit gedrag is een beperking van Zypper. In sommige gevallen kan het nodig zijn om de update-implementatie opnieuw uit te voeren. Controleer het Update logboek om dit te controleren.

### <a name="multi-tenant"></a>Implementaties van cross-Tenant updates

Als u computers in een andere Azure-Tenant rapporteert om Updatebeheer die u moet patchen, moet u de volgende tijdelijke oplossing gebruiken om ze te laten plannen. U kunt de cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) met de `-ForUpdate` switch gebruiken om een schema te maken en de cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) te gebruiken en de computers in de andere Tenant door te geven aan de `-NonAzureComputer`-para meter. In het volgende voor beeld ziet u hoe u dit doet:

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

Gebruik de volgende zelf studie voor meer informatie over het beheren van updates voor uw Windows-Vm's:

> [!div class="nextstepaction"]
> [Updates en patches voor uw Azure Windows-Vm's beheren](automation-tutorial-update-management.md)

* Zoek opdrachten in Logboeken in [Azure monitor logboeken](../log-analytics/log-analytics-log-searches.md) gebruiken om gedetailleerde update gegevens weer te geven.
* [Waarschuwingen maken](automation-tutorial-update-management.md#configure-alerts) voor de implementatie status van de update.

* Zie [Software-update configuraties](/rest/api/automation/softwareupdateconfigurations)voor meer informatie over het werken met updatebeheer via de rest API.
* Zie [problemen oplossen updatebeheer](troubleshoot/update-management.md)voor meer informatie over het oplossen van problemen met updatebeheer.
