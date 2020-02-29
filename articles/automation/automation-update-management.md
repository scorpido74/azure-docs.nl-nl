---
title: Updatebeheer oplossing in azure
description: In dit artikel wordt beschreven hoe u de Azure Updatebeheer-oplossing gebruikt voor het beheren van updates voor uw Windows-en Linux-computers.
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: 71cf1a2c4a0a2ff07d2aa0b48a8ee3bb3da8a540
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919704"
---
# <a name="update-management-solution-in-azure"></a>Updatebeheer oplossing in azure

U kunt de Updatebeheer-oplossing in Azure Automation gebruiken om updates van besturings systemen te beheren voor uw Windows-en Linux-machines in azure, in on-premises omgevingen en in andere Cloud omgevingen. U kunt snel de status van beschik bare updates op alle agent computers beoordelen en het proces voor het installeren van vereiste updates voor servers beheren.

U kunt Updatebeheer voor virtuele machines (Vm's) inschakelen met behulp van de volgende methoden:

- Vanuit uw [Azure Automation-account](automation-onboard-solutions-from-automation-account.md) voor een of meer Azure-machines en hand matig voor niet-Azure-machines.

- Voor één Azure-VM vanaf de pagina virtuele machine in het Azure Portal. Dit scenario is beschikbaar voor [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) -en [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) -vm's.

- Voor [meerdere virtuele Azure-machines](manage-update-multi.md) door ze te selecteren op de pagina **virtual machines** in de Azure Portal. 

> [!NOTE]
> Voor de Updatebeheer oplossing moet u een Log Analytics-werk ruimte koppelen aan uw Automation-account. Zie [Azure Workspace-toewijzingen](./how-to/region-mappings.md)voor een definitieve lijst met ondersteunde regio's. De regio toewijzingen hebben geen invloed op de mogelijkheid om Vm's te beheren in een andere regio dan uw Automation-account.

Er is een Azure [Resource Manager-sjabloon](automation-update-management-deploy-template.md) beschikbaar waarmee u de updatebeheer-oplossing kunt implementeren in een nieuw of bestaand Automation-account en log Analytics werk ruimte in uw abonnement.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Oplossingenoverzicht

Computers die worden beheerd door Updatebeheer gebruiken de volgende configuraties voor het uitvoeren van analyses en het bijwerken van implementaties:

* Log Analytics-agent voor Windows of Linux
* PowerShell Desired State Configuration (DSC) voor Linux
* Automation Hybrid Runbook Worker
* Microsoft Update of Windows Server Update Services (WSUS) voor Windows-computers

In het volgende diagram ziet u hoe de oplossing beveiligings updates evalueert en toepast op alle verbonden Windows Server-en Linux-computers in een werk ruimte:

![Proces stroom Updatebeheer](./media/automation-update-management/update-mgmt-updateworkflow.png)

Updatebeheer kan worden gebruikt om machines in meerdere abonnementen in dezelfde Tenant op te doen.

Nadat een pakket is vrijgegeven, duurt het 2 tot 3 uur voordat de patch wordt weer gegeven voor Linux-machines voor evaluatie. Voor Windows-computers duurt het 12 tot 15 uur voordat de patch wordt weer gegeven voor evaluatie nadat deze is uitgebracht.

Nadat een computer een scan heeft voltooid voor update compatibiliteit, stuurt de agent de gegevens bulksgewijs door naar Azure Monitor Logboeken. Op een Windows-computer wordt de compatibiliteits scan standaard elke 12 uur uitgevoerd.

Naast het scan schema wordt de controle op update vereisten binnen 15 minuten gestart nadat de Log Analytics agent opnieuw is opgestart, vóór de installatie van de update en na de installatie van de update.

Voor een Linux-computer wordt standaard elk uur de compatibiliteits scan uitgevoerd. Als de Log Analytics-agent opnieuw is opgestart, wordt een nalevings scan binnen 15 minuten gestart.

De oplossing rapporteert hoe up-to-date de machine is gebaseerd op de bron die u hebt geconfigureerd om te synchroniseren met. Als de Windows-computer is geconfigureerd om te rapporteren aan WSUS, afhankelijk van de laatste synchronisatie van WSUS met Microsoft Update, kunnen de resultaten afwijken van wat Microsoft Update laat zien. Dit gedrag is hetzelfde voor Linux-machines die zijn geconfigureerd om te rapporteren aan een lokale opslag plaats in plaats van naar een open bare opslag plaats.

> [!NOTE]
> Updatebeheer moet bepaalde Url's en poorten zijn ingeschakeld om de service goed te kunnen melden. Zie [netwerk planning voor Hybrid Workers voor](automation-hybrid-runbook-worker.md#network-planning)meer informatie over deze vereisten.

U kunt software-updates implementeren en installeren op computers die de updates nodig hebben door een geplande implementatie te maken. Updates die zijn geclassificeerd als *optioneel* , worden niet opgenomen in het implementatie bereik voor Windows-machines. Alleen vereiste updates zijn opgenomen in het implementatie bereik.

De geplande implementatie definieert welke doel computers de toepasselijke updates ontvangen. Dit doet u door bepaalde computers expliciet op te geven of door een [computer groep](../azure-monitor/platform/computer-groups.md) te selecteren die is gebaseerd op logboek zoekopdrachten van een specifieke set machines (of een [Azure-query](automation-update-management-query-logs.md) waarmee dynamisch virtuele Azure-machines worden geselecteerd op basis van opgegeven criteria). Deze groepen wijken af van de [Scope configuratie](../azure-monitor/insights/solution-targeting.md), die alleen wordt gebruikt om te bepalen op welke machines de Management Packs worden opgehaald die de oplossing inschakelen.

U kunt ook een schema opgeven dat u wilt goed keuren en een tijds periode instellen waarin updates kunnen worden geïnstalleerd. Deze periode wordt het onderhouds venster genoemd. Een periode van 20 minuten van het onderhouds venster is gereserveerd voor opnieuw opstarten, ervan uitgaande dat er een is vereist en u de juiste opstart optie hebt geselecteerd. Als de patch langer duurt dan verwacht en er minder dan 20 minuten in het onderhouds venster wordt weer gegeven, wordt de computer niet opnieuw opgestart.

Updates worden geïnstalleerd door runbooks in Azure Automation. U kunt deze runbooks niet weer geven en er is geen configuratie vereist. Wanneer een update-implementatie wordt gemaakt, wordt er een planning gemaakt waarmee een Master update-runbook wordt gestart op het opgegeven tijdstip voor de inbegrepen computers. Het hoofd-runbook start een onderliggend runbook op elke agent om de vereiste updates te installeren.

Op de datum en tijd die zijn opgegeven in de update-implementatie, wordt de implementatie parallel uitgevoerd op de doel computers. Voorafgaand aan de installatie wordt een scan uitgevoerd om te controleren of de updates nog steeds vereist zijn. Voor WSUS-client computers, als de updates niet zijn goedgekeurd in WSUS, mislukt de update-implementatie.

Het is niet mogelijk om een computer te registreren voor Updatebeheer in meer dan één Log Analytics-werk ruimte (ook wel multihoming genoemd).

## <a name="clients"></a>Clients

### <a name="supported-client-types"></a>Ondersteunde client-typen

De volgende tabel geeft een lijst van de ondersteunde besturings systemen voor update-evaluaties. Voor patching is een Hybrid Runbook Worker vereist. Zie de installatie handleidingen voor het installeren van een [Windows-Hybrid Runbook worker](automation-windows-hrw-install.md) en een Linux- [Hybrid Runbook worker](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)voor meer informatie over Hybrid Runbook worker vereisten.

|Besturingssysteem  |Opmerkingen  |
|---------|---------|
|Windows Server 2019 (Data Center/Data Center core/Standard)<br><br>Windows Server 2016 (Data Center/Data Center core/Standard)<br><br>Windows Server 2012 R2 (Data Center/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM en SP1 Standard)| Updatebeheer biedt alleen ondersteuning voor het uitvoeren van evaluaties voor dit besturings systeem. patching wordt niet ondersteund omdat de [Hybrid Runbook worker](automation-windows-hrw-install.md) niet wordt ondersteund voor Windows Server 2008 R2. |
|CentOS 6 (x86/x64) en 7 (x64)      | Linux-agents moeten toegang hebben tot een update opslagplaats. Voor op classificatie gebaseerde patches is `yum` vereist om beveiligings gegevens te retour neren die CentOS niet hebben in de RTM-releases. Zie [Update classificaties in Linux](automation-view-update-assessments.md#linux-2)voor meer informatie over op CentOS gebaseerde patches op basis van classificatie.          |
|Red Hat Enterprise 6 (x86/x64) en 7 (x64)     | Linux-agents moeten toegang hebben tot een update opslagplaats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) en 12 (x64)     | Linux-agents moeten toegang hebben tot een update opslagplaats.        |
|Ubuntu 14,04 LTS, 16,04 LTS en 18,04 (x86/x64)      |Linux-agents moeten toegang hebben tot een update opslagplaats.         |

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

### <a name="client-requirements"></a>Client vereisten

In de volgende informatie worden OS-specifieke client vereisten beschreven. Zie [netwerk planning](#ports)voor meer informatie.

#### <a name="windows"></a>Windows

Windows-agents moeten worden geconfigureerd om te communiceren met een WSUS-server of moeten toegang hebben tot Microsoft Update.

U kunt Updatebeheer gebruiken met Configuration Manager. Zie [Configuration Manager integreren met updatebeheer](oms-solution-updatemgmt-sccmintegration.md#configuration)voor meer informatie over integratie scenario's. De [log Analytics Windows-agent](../azure-monitor/platform/agent-windows.md) is vereist. De agent wordt automatisch geïnstalleerd als u een virtuele Azure-machine onboardeert.

Standaard worden Windows-Vm's die zijn geïmplementeerd vanuit Azure Marketplace ingesteld voor het ontvangen van automatische updates van Windows Update service. Dit gedrag verandert niet wanneer u deze oplossing toevoegt of Windows-Vm's toevoegt aan uw werk ruimte. Als u updates niet actief beheert met behulp van deze oplossing, is het standaard gedrag van toepassing (om updates automatisch toe te passen).

> [!NOTE]
> Een gebruiker kan groepsbeleid wijzigen zodat het opnieuw opstarten van de machine alleen door de gebruiker kan worden uitgevoerd, niet door het systeem. Beheerde computers kunnen vastraken als Updatebeheer geen rechten heeft om de computer opnieuw op te starten zonder hand matige interactie van de gebruiker.
>
> Zie [Groepsbeleid instellingen voor automatische updates configureren](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)voor meer informatie.

#### <a name="linux"></a>Linux

Voor Linux moet de computer toegang hebben tot een update opslagplaats. De update opslagplaats kan privé of openbaar zijn. TLS 1,1 of TLS 1,2 is vereist voor de interactie met Updatebeheer. Een Log Analytics-agent voor Linux die is geconfigureerd om te rapporteren aan meer dan één Log Analytics-werk ruimte, wordt niet ondersteund met deze oplossing. Op de computer moet ook python 2. x zijn geïnstalleerd.

Zie [log Analytics agent voor Linux](../azure-monitor/platform/agent-linux.md)voor meer informatie over het installeren van de log Analytics-agent voor Linux en het downloaden van de meest recente versie. Zie [Windows-computers verbinden met Azure monitor](../log-analytics/log-analytics-windows-agent.md)voor meer informatie over het installeren van de log Analytics-agent voor Windows.

Vm's die zijn gemaakt op basis van de installatie kopieën op Red Hat Enterprise Linux aanvraag (RHEL) die beschikbaar zijn in azure Marketplace, worden geregistreerd voor toegang tot de [Red Hat Update infrastructure (RHUI)](../virtual-machines/workloads/redhat/redhat-rhui.md) die is geïmplementeerd in Azure. Elke andere Linux-distributie moet worden bijgewerkt vanuit de online bestands opslagplaats van de distributie met behulp van de ondersteunde methoden van de distributie.

## <a name="permissions"></a>Machtigingen

U hebt specifieke machtigingen nodig om update-implementaties te maken en te beheren. Zie op [rollen gebaseerde toegang – updatebeheer](automation-role-based-access-control.md#update-management)voor meer informatie over deze machtigingen.

## <a name="solution-components"></a>Oplossingsonderdelen

De oplossing bestaat uit de volgende resources. Deze resources worden automatisch toegevoegd aan uw Automation-account wanneer u de oplossing inschakelt. 

### <a name="hybrid-worker-groups"></a>Hybrid Worker-groepen

Nadat u deze oplossing hebt ingeschakeld, wordt elke Windows-computer die rechtstreeks is verbonden met uw Log Analytics-werk ruimte automatisch geconfigureerd als een Hybrid Runbook Worker ter ondersteuning van de runbooks die in deze oplossing zijn opgenomen.

Elke Windows-computer die door de oplossing wordt beheerd, wordt weer gegeven in het deel venster **Hybrid worker groups** als een **Hybrid worker-groep** voor het Automation-account. De oplossingen gebruiken de *hostname-FQDN_GUID* naam Conventie. U kunt deze groepen niet richten op runbooks in uw account. Als u probeert, mislukt de poging. Deze groepen zijn alleen bedoeld ter ondersteuning van deze beheer oplossing.

U kunt de Windows-computer toevoegen aan een Hybrid Runbook Worker groep in uw Automation-account ter ondersteuning van Automation-runbooks als u hetzelfde account gebruikt voor zowel de oplossing als het lidmaatschap van de Hybrid Runbook Worker-groep. Deze functionaliteit is toegevoegd aan versie 7.2.12024.0 van de Hybrid Runbook Worker.

### <a name="management-packs"></a>Management packs

Als uw System Center Operations Manager-beheer groep is [verbonden met een log Analytics-werk ruimte](../azure-monitor/platform/om-agents.md), worden de volgende Management Packs in Operations Manager geïnstalleerd. Deze Management Packs worden ook op rechtstreeks verbonden Windows-computers geïnstalleerd nadat u de oplossing hebt toegevoegd. U hoeft deze Management Packs niet te configureren of te beheren.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Implementatie MP bijwerken

> [!NOTE]
> Als u een Operations Manager 1807-of 2019-beheer groep hebt verbonden met een Log Analytics-werk ruimte met agents die zijn geconfigureerd in de beheer groep om logboek gegevens te verzamelen, moet u de volgende regel negeren om ze te beheren met Updatebeheer: Vervang de para meter **IsAutoRegistrationEnabled** en stel deze in op **True** in de regel **Microsoft. intelligence packs. AzureAutomation. HybridAgent. init** .

Zie [Connect Operations Manager to Azure monitor logs](../azure-monitor/platform/om-agents.md)(Engelstalig) voor meer informatie over hoe oplossingen Management Packs worden bijgewerkt.

> [!NOTE]
> Voor machines met de Operations Manager-agent, om volledig te worden beheerd door Updatebeheer, moet de agent worden bijgewerkt naar de Log Analytics-agent voor Windows of Linux. Zie [een Operations Manager-agent bijwerken](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)voor meer informatie over het bijwerken van de agent. In omgevingen waarin Operations Manager wordt gebruikt, moet u System Center Operations Manager 2012 R2 UR 14 of hoger uitvoeren.

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

Er wordt elk uur een scan uitgevoerd voor elke beheerde Linux-machine.

Het kan tussen 30 minuten en 6 uur duren voordat het dash board bijgewerkte gegevens van beheerde computers weergeeft.

Het gemiddelde gegevens gebruik door Azure Monitor logboeken voor een machine met Updatebeheer is ongeveer 25 MB per maand. Deze waarde is alleen een benadering en is onderhevig aan wijzigingen, afhankelijk van uw omgeving. U wordt aangeraden uw omgeving te bewaken om uw exacte gebruik bij te houden. Zie [verbruik en kosten beheren](../azure-monitor/platform/manage-cost-storage.md)voor meer informatie over het analyseren van het gegevens gebruik.

## <a name="ports"></a>Netwerk planning

De volgende adressen zijn specifiek vereist voor Updatebeheer. Communicatie met deze adressen vindt plaats via poort 443.

|Open bare Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *. azure-automation.us|

Voor Windows-computers moet u ook verkeer toestaan voor eind punten die vereist zijn voor Windows Update. U kunt een bijgewerkte lijst met vereiste eind punten vinden in [kwesties met betrekking tot http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Als u een lokale [Windows Update server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)hebt, moet u ook verkeer toestaan naar de server die is opgegeven in uw [WSUS-sleutel](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Zie IP-adressen voor de vereiste eind punten voor [de RHUI content delivery servers](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) voor Red Hat Linux-machines. Raadpleeg de documentatie van uw provider voor andere Linux-distributies.

Zie [Hybrid worker Role ports](automation-hybrid-runbook-worker.md#hybrid-worker-role)(Engelstalig) voor meer informatie over poorten die vereist zijn voor de Hybrid Runbook Worker.

U wordt aangeraden de adressen te gebruiken die worden weer gegeven bij het definiëren van uitzonde ringen. Voor IP-adressen kunt u de [IP-adresbereiken van Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653)downloaden. Dit bestand wordt wekelijks bijgewerkt en weerspiegelt de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken.

Volg de instructies in [computers verbinden zonder Internet toegang](../azure-monitor/platform/gateway.md) voor het configureren van computers die geen toegang tot internet hebben.

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
|Hulpprogramma's     | Een hulp programma of functie waarmee u een of meer taken kunt volt ooien.        |
|Updates     | Een update voor een toepassing of bestand dat momenteel is geïnstalleerd.        |

### <a name="linux-2"></a>Spreek

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële en beveiligingsupdates     | Updates voor een specifiek probleem of een productspecifiek beveiligings probleem.         |
|Andere Updates     | Alle andere updates die niet kritiek zijn of die geen beveiligings updates zijn.        |

Voor Linux kan Updatebeheer een onderscheid maken tussen essentiële updates en beveiligings updates in de Cloud, terwijl evaluatie gegevens worden weer gegeven vanwege gegevens verrijking in de Cloud. Voor patching is Updatebeheer afhankelijk van de classificatie gegevens die op de computer beschikbaar zijn. In tegens telling tot andere distributies is CentOS deze informatie niet beschikbaar in de RTM-versie. Als er CentOS machines zijn geconfigureerd voor het retour neren van beveiligings gegevens voor de volgende opdracht, kunt Updatebeheer patch op basis van classificaties.

```bash
sudo yum -q --security check-update
```

Er is momenteel geen ondersteunde methode voor het inschakelen van systeem eigen classificatie-gegevens beschikbaarheid op CentOS. Op dit moment wordt alleen ondersteuning voor de beste werk belasting gegeven aan klanten die deze zelf kunnen hebben ingeschakeld. 

Als u updates wilt classificeren voor Red Hat Enter prise versie 6, moet u de yum-beveiligings-invoeg toepassing installeren. Op Red Hat Enterprise Linux 7 maakt de invoeg toepassing al deel uit van yum. u hoeft niets te installeren. Zie het volgende Red Hat [Knowledge-artikel](https://access.redhat.com/solutions/10021)voor meer informatie.

## <a name="integrate-with-configuration-manager"></a>Integreren met Configuration Manager

Klanten die hebben geïnvesteerd in micro soft endpoint Configuration Manager voor het beheren van Pc's, servers en mobiele apparaten, zijn ook afhankelijk van de kracht en de loop tijd van Configuration Manager om hen te helpen bij het beheren van software-updates. Configuration Manager maakt deel uit van de cyclus van software-update beheer (SUM).

Zie [Configuration Manager met updatebeheer integreren](oms-solution-updatemgmt-sccmintegration.md)voor meer informatie over het integreren van de beheer oplossing met Configuration Manager.

### <a name="third-party-updates-on-windows"></a>Updates van derden in Windows

Updatebeheer is afhankelijk van de lokaal geconfigureerde update opslagplaats om ondersteunde Windows-systemen bij te werken. Dit is WSUS of Windows Update. Met hulpprogram ma's als [System Center updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (updates Publisher) kunt u aangepaste updates importeren en publiceren met WSUS. Met dit scenario kunnen Updatebeheer machines bijwerken die gebruikmaken van Configuration Manager als de update opslagplaats met software van derden. Zie [install updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher)(Engelstalig) voor meer informatie over het configureren van updates Publisher.

## <a name="onboard"></a>Updatebeheer inschakelen

Als u systemen wilt bijwerken, moet u de Updatebeheer-oplossing inschakelen. Hieronder vindt u de aanbevolen en ondersteunde methoden voor het voorbereiden van de oplossing:

* [Van een virtuele machine](automation-onboard-solutions-from-vm.md)
* [Van surfen op meerdere computers](automation-onboard-solutions-from-browse.md)
* [Vanuit uw Automation-account](automation-onboard-solutions-from-automation-account.md)
* [Met een Azure Automation runbook](automation-onboard-solutions.md)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Veelgestelde](automation-faq.md) vragen over Azure Automation om veelgestelde vragen over deze oplossing te bekijken.
