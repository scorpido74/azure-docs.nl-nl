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
ms.openlocfilehash: 253e01b6bfa6609b4ec41d69a3c4b1bbe405ba5a
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240285"
---
# <a name="update-management-solution-in-azure"></a>Updatebeheer oplossing in azure

U kunt de Updatebeheer-oplossing in Azure Automation gebruiken om updates van besturings systemen te beheren voor uw Windows-en Linux-computers in azure, in on-premises omgevingen of in andere cloud providers. U kunt snel de status van de beschikbare updates op alle agentcomputers beoordelen en de procedure voor het installeren van vereiste updates voor servers beheren.

U kunt Updatebeheer voor virtuele machines rechtstreeks inschakelen vanuit uw Azure Automation-account. Zie [updates voor meerdere virtuele machines beheren](manage-update-multi.md)voor meer informatie over het inschakelen van updatebeheer voor virtuele machines vanuit uw Automation-account. U kunt Updatebeheer ook inschakelen voor een virtuele machine vanaf de pagina virtuele machine in de Azure Portal. Dit scenario is beschikbaar voor virtuele [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) -en [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) -machines.

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

De geplande implementatie definieert welke doel computers de toepasselijke updates ontvangen, hetzij door expliciet computers op te geven of door een [computer groep](../azure-monitor/platform/computer-groups.md) te selecteren die is gebaseerd op logboek zoekopdrachten van een specifieke set computers of een [Azure-query](#azure-machines) Hiermee worden virtuele Azure-machines dynamisch geselecteerd op basis van opgegeven criteria. Deze groepen wijken af van de [Scope configuratie](../azure-monitor/insights/solution-targeting.md), die alleen wordt gebruikt om te bepalen op welke machines de Management Packs worden opgehaald die de oplossing inschakelen.

U kunt ook een planning opgeven die u wilt goed keuren en een periode instellen waarin updates kunnen worden geïnstalleerd. Deze periode wordt het onderhouds venster genoemd. Tien minuten van het onderhouds venster is gereserveerd voor opnieuw opstarten als de computer opnieuw moet worden opgestart en u de juiste optie voor opnieuw opstarten hebt geselecteerd. Als de patch langer duurt dan verwacht en er minder dan tien minuten in het onderhouds venster wordt weer gegeven, wordt de computer niet opnieuw opgestart.

Updates worden geïnstalleerd door runbooks in Azure Automation. U kunt deze runbooks niet weer geven en de runbooks hebben geen configuratie nodig. Wanneer er een update-implementatie wordt gemaakt, maakt de update-implementatie een planning waarbij een Master update-runbook wordt gestart op het opgegeven tijdstip voor de inbegrepen computers. Het hoofd-runbook start een onderliggend runbook op elke agent om de vereiste updates te installeren.

Op de datum en tijd die zijn opgegeven in de update-implementatie, wordt de implementatie parallel uitgevoerd op de doel computers. Voorafgaand aan de installatie wordt een scan uitgevoerd om te controleren of de updates nog steeds vereist zijn. Als de updates niet zijn goedgekeurd in WSUS, mislukt de update-implementatie voor WSUS-client computers.

Het is niet mogelijk om een computer te registreren voor Updatebeheer in meer dan één Log Analytics-werk ruimten (multi-multihoming).

## <a name="clients"></a>Clients

### <a name="supported-client-types"></a>Ondersteunde client-typen

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

### <a name="unsupported-client-types"></a>Niet-ondersteunde client-typen

De volgende tabel geeft een overzicht van de besturingssystemen die niet worden ondersteund:

|Besturingssysteem  |Opmerkingen  |
|---------|---------|
|Windows-client     | Client-besturingssystemen (zoals Windows 7 en Windows 10) worden niet ondersteund.        |
|Windows Server 2016 Nano Server     | Wordt niet ondersteund.       |
|Azure Kubernetes-service knooppunten | Wordt niet ondersteund. Gebruik het patch proces dat wordt beschreven in [beveiligings-en kernel-updates Toep assen op Linux-knoop punten in azure Kubernetes service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Client vereisten

#### <a name="windows"></a>Windows

Windows-agents moeten worden geconfigureerd om te communiceren met een WSUS-server of moeten toegang hebben tot Microsoft Update. U kunt Updatebeheer gebruiken met System Center Configuration Manager. Zie [System Center Configuration Manager integreren met updatebeheer](oms-solution-updatemgmt-sccmintegration.md#configuration)voor meer informatie over integratie scenario's. De [Windows-agent](../azure-monitor/platform/agent-windows.md) is vereist. De agent wordt automatisch geïnstalleerd als u een virtuele machine van Azure wilt voorbereiden.

> [!NOTE]
> Het is mogelijk dat een gebruiker groepsbeleid kan wijzigen zodat het opnieuw opstarten van de computer kan worden uitgevoerd door de gebruiker, niet door het systeem. Beheerde computers kunnen vastlopen als Updatebeheer geen rechten heeft om de computer opnieuw op te starten zonder hand matige interactie van de gebruiker.
>
> Zie [Groepsbeleid instellingen voor automatische updates configureren](https://docs.microsoft.com/en-us/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)voor meer informatie.

#### <a name="linux"></a>Linux

Voor Linux moet de computer toegang hebben tot een update opslagplaats. De update opslagplaats kan privé of openbaar zijn. TLS 1,1 of TLS 1,2 is vereist voor de interactie met Updatebeheer. Een Log Analytics-agent voor Linux die is geconfigureerd om te rapporteren aan meer dan één Log Analytics werk ruimten, wordt niet ondersteund met deze oplossing.  Op de computer moet ook python 2. x zijn geïnstalleerd.

Zie [log Analytics agent voor Linux](https://github.com/microsoft/oms-agent-for-linux)voor meer informatie over het installeren van de log Analytics-agent voor Linux en het downloaden van de meest recente versie. Zie [micro soft Monitoring Agent voor Windows](../log-analytics/log-analytics-windows-agent.md)voor meer informatie over het installeren van de log Analytics-agent voor Windows.

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

## <a name="onboard"></a>Updatebeheer inschakelen

Als u met patches-systemen wilt beginnen, moet u de Updatebeheer-oplossing inschakelen. Er zijn veel manieren om machines te Updatebeheer. Hieronder vindt u de aanbevolen en ondersteunde manieren om de oplossing vrij te maken:

* [Van een virtuele machine](automation-onboard-solutions-from-vm.md)
* [Van surfen op meerdere computers](automation-onboard-solutions-from-browse.md)
* [Vanuit uw Automation-account](automation-onboard-solutions-from-automation-account.md)
* [Met een Azure Automation runbook](automation-onboard-solutions.md)

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Controleren of niet-Azure-machines zijn voor bereid

Om te bevestigen dat rechtstreeks verbonden computers communiceren met Azure Monitor-logboeken, kunt u na een paar minuten een van de volgende zoek opdrachten in Logboeken uitvoeren.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Op een Windows-computer kunt u de volgende informatie controleren om de agent connectiviteit met Azure Monitor-logboeken te controleren:

1. Open **micro soft Monitoring Agent**in het configuratie scherm. Op het tabblad **log Analytics van Azure** wordt het volgende bericht weer gegeven: **De micro soft monitoring agent heeft verbinding gemaakt met log Analytics**.
2. Open het Windows-gebeurtenis logboek. Ga naar **Application and Services Servicelogboeken\operations Manager** en zoek naar gebeurtenis-id 3000 en gebeurtenis-id 5002 van de bron **service connector**. Deze gebeurtenissen geven aan dat de computer is geregistreerd bij de Log Analytics-werk ruimte en dat er configuratie wordt ontvangen.

Als de agent niet kan communiceren met Azure Monitor-logboeken en de agent is geconfigureerd voor communicatie met Internet via een firewall of proxy server, controleert u of de firewall of proxy server correct is geconfigureerd. Zie [netwerk configuratie voor Windows-agent](../azure-monitor/platform/agent-windows.md) of [netwerk configuratie voor Linux-agent](../log-analytics/log-analytics-agent-linux.md)voor informatie over het controleren van de juiste configuratie van de firewall of proxy server.

> [!NOTE]
> Als uw Linux-systemen zijn geconfigureerd om te communiceren met een proxy-of Log Analytics gateway en u deze oplossing onboardt, werkt u de *proxy. conf* -machtigingen om de omiuser-groep lees machtigingen te verlenen voor het bestand met de volgende opdrachten:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nieuwe toegevoegde Linux-agents tonen de status **bijgewerkt** nadat een evaluatie is uitgevoerd. Dit proces kan maximaal zes uur duren.

Zie [Operations Manager integratie valideren met Azure monitor logboeken](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)om te controleren of een Operations Manager-beheer groep communiceert met Azure monitor-Logboeken.

## <a name="data-collection"></a>Gegevensverzameling

### <a name="supported-agents"></a>Ondersteunde agents

De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing:

| Verbonden bron | Ondersteund | Description |
| --- | --- | --- |
| Windows-agents |Ja |De oplossing verzamelt informatie over systeem updates van Windows-agents en start vervolgens de installatie van de vereiste updates. |
| Linux-agents |Ja |De oplossing verzamelt informatie over systeem updates van Linux-agents en start vervolgens de installatie van de vereiste updates op ondersteunde distributies. |
| Beheergroep Operations Manager |Ja |De oplossing verzamelt informatie over systeemupdates van agents in een verboden beheergroep.<br/>Een directe verbinding van de Operations Manager agent naar Azure Monitor-Logboeken is niet vereist. Gegevens uit de beheergroep doorgestuurd naar de Log Analytics-werkruimte. |

### <a name="collection-frequency"></a>Verzamelingsfrequentie

Voor elke beheerde Windows-computer wordt twee keer per dag een scan uitgevoerd. Elke 15 minuten wordt de Windows-API aangeroepen om een query uit te zoeken naar de laatste update tijd om te bepalen of de status is gewijzigd. Als de status is gewijzigd, wordt een nalevings scan gestart.

Er wordt elk uur een scan uitgevoerd voor elke beheerde Linux-computer.

Het kan tussen 30 minuten en 6 uur duren voordat het dash board bijgewerkte gegevens van beheerde computers weergeeft.

Het gemiddelde Azure Monitor gegevens gebruik voor een machine met behulp van Updatebeheer is ongeveer 25MB per maand. Deze waarde is alleen een benadering en kan worden gewijzigd op basis van uw omgeving. Het is raadzaam om uw omgeving te controleren om precies het gebruik te zien dat u hebt.

## <a name="viewing-update-assessments"></a>Update-evaluaties weer geven

Selecteer in uw Automation-account **updatebeheer** om de status van uw computers weer te geven.

Deze weer gave bevat informatie over uw computers, ontbrekende updates, update-implementaties en geplande update-implementaties. In de **kolom compatibiliteit**ziet u de laatste keer dat de computer is geëvalueerd. In de **gereedheids** kolom van de Update-Agent kunt u zien of de status van de Update Agent is. Als er een probleem is, selecteert u de koppeling om naar probleemoplossings documentatie te gaan en leert u welke stappen u moet ondernemen om het probleem te verhelpen.

Als u een zoek opdracht in het logboek wilt uitvoeren die informatie over de computer, update of implementatie retourneert, selecteert u het item in de lijst. Het deel venster **zoeken** in Logboeken wordt geopend met een query voor het geselecteerde item:

![Standaard weergave Updatebeheer](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Updates installeren

Nadat de updates zijn beoordeeld voor alle Linux-en Windows-computers in uw werk ruimte, kunt u de vereiste updates installeren door een *Update-implementatie*te maken. Als u een update-implementatie wilt maken, moet u schrijf toegang hebben tot het Automation-account en schrijf toegang hebben tot de Azure-Vm's die in de implementatie zijn gericht. Een update-implementatie is een geplande installatie van de vereiste updates voor een of meer computers. U geeft de datum en tijd op voor de implementatie en een computer of groep computers die moeten worden opgenomen in het bereik van een implementatie. Zie [computer groepen in azure monitor](../azure-monitor/platform/computer-groups.md)-logboeken voor meer informatie over computer groepen.

Wanneer u computer groepen opneemt in uw update-implementatie, wordt het groepslid maatschap slechts één keer geëvalueerd, op het moment dat het schema wordt gemaakt. Volgende wijzigingen aan een groep worden niet doorgevoerd. Om deze [dynamische groepen](#using-dynamic-groups)te gebruiken, worden deze groepen tijdens de implementatie omgezet en worden ze gedefinieerd door een query voor virtuele machines in azure of een opgeslagen zoek opdracht voor niet-Azure vm's.

> [!NOTE]
> Virtuele Windows-machines die zijn geïmplementeerd op basis van de Azure Marketplace, worden standaard ingesteld om automatische updates van Windows Update-service te ontvangen. Dit gedrag verandert niet wanneer u deze oplossing toevoegt of virtuele Windows-machines toevoegt aan uw werk ruimte. Als u updates niet actief beheert met behulp van deze oplossing, is het standaard gedrag van toepassing (om updates automatisch toe te passen).

Om te voor komen dat updates buiten een onderhouds venster op Ubuntu worden toegepast, moet u het pakket voor de upgrade zonder toezicht opnieuw configureren om automatische updates uit te scha kelen. Zie [Automatische updates onderwerp in de hand leiding voor de Ubuntu-Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)voor meer informatie over het configureren van het pakket.

Virtuele machines die zijn gemaakt op basis van de installatie kopieën op Red Hat Enterprise Linux aanvraag (RHEL) die beschikbaar zijn in azure Marketplace, worden geregistreerd voor toegang tot de [Red Hat Update infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) die is geïmplementeerd in Azure. Elke andere Linux-distributie moet worden bijgewerkt vanuit de online bestands opslagplaats van de distributie door de ondersteunde methoden van de distributie te volgen.

Selecteer **Update-implementatie plannen**om een nieuwe update-implementatie te maken. De pagina **nieuwe update-implementatie** wordt geopend. Voer waarden in voor de eigenschappen die in de volgende tabel worden beschreven en klik vervolgens op **maken**:

| Eigenschap | Description |
| --- | --- |
| Name |Unieke naam voor het identificeren van de update-implementatie. |
|Besturingssysteem| Linux of Windows|
| Bij te werken groepen |Voor Azure-machines definieert u een query op basis van een combi natie van abonnement, resource groepen, locaties en tags om een dynamische groep virtuele Azure-machines samen te stellen die in uw implementatie moeten worden meegenomen. </br></br>Voor niet-Azure-machines selecteert u een bestaande opgeslagen zoek opdracht om een groep van niet-Azure-machines te selecteren die u in de implementatie wilt gebruiken. </br></br>Zie [Dynamische groepen](automation-update-management.md#using-dynamic-groups) voor meer informatie|
| Machines die moeten worden bijgewerkt |selecteer een opgeslagen zoekopdracht of geïmporteerde groep, of kies Computer in de vervolgkeuzelijst en selecteer de afzonderlijke computers. Als u **Computers** selecteert, wordt de gereedheid van de computer weergegeven in de kolom **GEREEDHEID VOOR UPDATE-AGENT**.</br> Zie [Computergroepen in Azure Monitorlogboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken |
|Updateclassificaties|Selecteer alle update classificaties die u nodig hebt|
|Updates opnemen/uitsluiten|Hiermee opent u de pagina **opnemen/uitsluiten** . Updates die moeten worden opgenomen of uitgesloten, worden op afzonderlijke tabbladen weergegeven. Zie [Werking van opname](automation-update-management.md#inclusion-behavior) voor meer informatie over hoe de opname wordt verwerkt |
|Planningsinstellingen|Selecteer het tijdstip waarop u wilt beginnen en selecteer een of meer keren of terugkerend voor het terugkeer patroon|
| Pre-scripts en post scripts|De scripts selecteren die voor en na de implementatie moeten worden uitgevoerd|
| Onderhoudsvenster |Aantal minuten dat is ingesteld voor updates. De waarde mag niet minder dan 30 minuten en Maxi maal 6 uur zijn |
| Besturings element opnieuw opstarten| Hiermee wordt bepaald hoe opnieuw opstarten moet worden afgehandeld. De volgende opties zijn beschikbaar:</br>Opnieuw opstarten indien nodig (standaard)</br>Altijd opnieuw opstarten</br>Nooit opnieuw opstarten</br>Alleen opnieuw opstarten - updates worden niet geïnstalleerd|

Update-implementaties kunnen ook programmatisch worden gemaakt. Zie [Software-update configuraties-maken](/rest/api/automation/softwareupdateconfigurations/create)voor meer informatie over het maken van een update-implementatie met behulp van de rest API. Er is ook een voor beeld van een runbook dat kan worden gebruikt voor het maken van een wekelijkse update-implementatie. Zie [een wekelijkse update-implementatie maken voor een of meer virtuele machines in een resource groep](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)voor meer informatie over dit runbook.

> [!NOTE]
> De register sleutels onder [register sleutels die worden gebruikt voor het beheren van opnieuw opstarten](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kunnen een gebeurtenis voor opnieuw opstarten veroorzaken als **het besturings element voor opnieuw** opstarten is ingesteld op **nooit opnieuw opstarten**.

### <a name="maintenance-windows"></a>Onderhouds Vensters

Onderhouds Vensters bepalen de hoeveelheid tijd die is toegestaan voor het installeren van updates. Houd rekening met de volgende details wanneer u een onderhouds venster opgeeft.

* Onderhouds Vensters bepalen hoeveel updates worden geïnstalleerd.
* Updatebeheer stopt met het installeren van nieuwe updates als het einde van een onderhouds venster nadert.
* Met Updatebeheer worden lopende updates niet beëindigd als het onderhouds venster wordt overschreden.
* Als het onderhouds venster op Windows is overschreden, is het vaak een update van een service pack dat veel tijd kost om te installeren.

### <a name="multi-tenant"></a>Implementaties van cross-Tenant updates

Als u computers in een andere Azure-Tenant rapporteert om Updatebeheer die u moet patchen, moet u de volgende tijdelijke oplossing gebruiken om ze te laten plannen. U kunt de cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) gebruiken met de switch `-ForUpdate` om een planning te maken en de [cmdlet New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) te gebruiken en de computers in de andere op te geven Tenant met de `-NonAzureComputer` para meter. In het volgende voor beeld ziet u een voor beeld van hoe u dit doet:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="view-missing-updates"></a>Ontbrekende updates weer geven

Selecteer **ontbrekende updates** om de lijst met updates weer te geven die ontbreken op uw computers. Elke update wordt weer gegeven en kan worden geselecteerd. Informatie over het aantal machines dat moet worden bijgewerkt, het besturings systeem en een koppeling voor meer informatie wordt weer gegeven. In het deel venster **zoeken** in Logboeken ziet u meer informatie over de updates.

## <a name="view-update-deployments"></a>Update-implementaties weer geven

Selecteer het tabblad **Update** -implementaties om de lijst met bestaande update-implementaties weer te geven. Selecteer een van de update-implementaties in de tabel om het deel venster implementatie van de **Update** voor de update-implementatie te openen. Taak logboeken worden Maxi maal 30 dagen opgeslagen.

![Overzicht van de resultaten van update-implementatie](./media/automation-update-management/update-deployment-run.png)

Zie [configuratie van software-updates](/rest/api/automation/softwareupdateconfigurationruns)voor het weer geven van een update-implementatie vanuit het rest API.

## <a name="update-classifications"></a>Updateclassificaties

De volgende tabellen geven een lijst van de update classificaties in Updatebeheer, met een definitie voor elke classificatie.

### <a name="windows"></a>Windows

|Classificatie  |Description  |
|---------|---------|
|Essentiële updates     | Een update voor een specifiek probleem dat betrekking heeft op een kritieke bug die niet aan beveiliging voldoet.        |
|Beveiligingsupdates     | Een update voor een productspecifiek, beveiligings probleem.        |
|Updatepakketten     | Een cumulatieve set met hotfixes die samen zijn verpakt voor een eenvoudige implementatie.        |
|Feature Ppacks     | Nieuwe product functies die worden gedistribueerd buiten een product release.        |
|Servicepacks     | Een cumulatieve set met hotfixes die op een toepassing worden toegepast.        |
|Definitie-updates     | Een update van virus-of andere definitie bestanden.        |
|Hulpprogramma's     | Een hulp programma of functie waarmee u een of meer taken kunt volt ooien.        |
|Updates     | Een update voor een toepassing of bestand dat momenteel is geïnstalleerd.        |

### <a name="linux-2"></a>Linux

|Classificatie  |Description  |
|---------|---------|
|Essentiële en beveiligingsupdates     | Updates voor een specifiek probleem of een productspecifiek beveiligings probleem.         |
|Andere updates     | Alle andere updates die niet kritiek zijn of geen beveiligings updates zijn.        |

Voor Linux kan Updatebeheer een onderscheid maken tussen essentiële updates en beveiliging in de Cloud, terwijl evaluatie gegevens worden weer gegeven vanwege gegevens verrijking in de Cloud. Voor patching is Updatebeheer afhankelijk van de classificatie gegevens die op de computer beschikbaar zijn. In tegens telling tot andere distributies is deze informatie niet beschikbaar in CentOS. Als er CentOS-machines zijn geconfigureerd op een manier om beveiligings gegevens te retour neren voor de volgende opdracht, kan Updatebeheer patch op basis van classificaties.

```bash
sudo yum -q --security check-update
```

Er is momenteel geen methode die wordt ondersteund voor het inschakelen van systeem eigen classificatie-gegevens beschikbaarheid op CentOS. Op dit moment wordt alleen ondersteuning voor de beste werk belasting gegeven aan klanten die deze zelf kunnen hebben ingeschakeld.

## <a name="firstparty-predownload"></a>Geavanceerde instellingen

Updatebeheer is afhankelijk van Windows Update om Windows-updates te downloaden en te installeren. Als gevolg hiervan respecteren we veel van de instellingen die worden gebruikt door Windows Update. Als u instellingen gebruikt om niet-Windows-updates in te scha kelen, worden deze updates ook door Updatebeheer beheerd. Als u het downloaden van updates wilt inschakelen voordat een update-implementatie wordt uitgevoerd, kunnen update-implementaties sneller zijn en minder kans lopen om het onderhouds venster te overschrijden.

### <a name="pre-download-updates"></a>Updates vooraf downloaden

Voor het configureren van updates die automatisch worden gedownload in groepsbeleid, kunt u de instelling voor het [configureren van automatische updates](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) instellen op **3**. Hiermee downloadt u de updates die nodig zijn op de achtergrond, maar worden ze niet geïnstalleerd. Dit houdt Updatebeheer in de controle over schema's, maar laat updates downloaden buiten het onderhouds venster Updatebeheer. Dit kan ervoor zorgen dat het onderhouds **venster** fouten in updatebeheer overschrijdt.

U kunt dit ook instellen met Power shell, de volgende Power shell uitvoeren op een systeem waarop u automatisch updates wilt downloaden.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="disable-automatic-installation"></a>Automatische installatie uitschakelen

Voor virtuele Azure-machines is automatische installatie van updates standaard ingeschakeld. Dit kan ertoe leiden dat updates worden geïnstalleerd voordat u deze plant om te worden geïnstalleerd door Updatebeheer. U kunt dit gedrag uitschakelen door de `NoAutoUpdate` register sleutel in te stellen op. `1` In het volgende Power shell-fragment ziet u een manier om dit te doen.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

### <a name="enable-updates-for-other-microsoft-products"></a>Updates voor andere micro soft-producten inschakelen

Windows Update biedt standaard alleen updates voor Windows. Als u **updates voor andere micro soft-producten geven tijdens het bijwerken van Windows**inschakelt, worden er updates voor andere producten weer gegeven, waaronder beveiligings patches voor SQL Server of andere software van de eerste partij. Deze optie kan niet worden geconfigureerd door groepsbeleid. Voer de volgende Power shell uit op de systemen waarvoor u andere patches voor de eerste partij wilt inschakelen, en Updatebeheer deze instelling wordt nageleefd.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="third-party"></a>Patches van derden in Windows

Updatebeheer is afhankelijk van de lokaal geconfigureerde update opslagplaats voor patches die worden ondersteund door Windows-systemen. Dit is WSUS of Windows Update. Met hulpprogram [ma's](/sccm/sum/tools/updates-publisher
) als System Center updates Publisher (updates Publisher) kunt u aangepaste updates publiceren in WSUS. Met dit scenario kunnen Updatebeheer patches voor machines die gebruikmaken van System Center Configuration Manager als update opslagplaats met software van derden. Zie [install updates Publisher](/sccm/sum/tools/install-updates-publisher)(Engelstalig) voor meer informatie over het configureren van updates Publisher.

## <a name="ports"></a>Netwerk planning

De volgende adressen zijn specifiek vereist voor Updatebeheer. Communicatie met deze adressen vindt plaats via poort 443.

|Open bare Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

Voor Windows-computers moet u ook verkeer toestaan voor eind punten die vereist zijn voor Windows Update.  U kunt een bijgewerkte lijst met vereiste eind punten vinden in [kwesties met betrekking tot http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Als u een lokale [Windows Update server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)hebt, moet u ook verkeer toestaan naar de server die is opgegeven in uw [WSUS-sleutel](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Voor Red Hat Linux-machines raadpleegt u [de IP-adressen voor de RHUI content delivery servers](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) voor vereiste eind punten. Raadpleeg de documentatie van de provider voor andere Linux-distributies.

Zie [Hybrid worker Role ports](automation-hybrid-runbook-worker.md#hybrid-worker-role)(Engelstalig) voor meer informatie over poorten die de Hybrid Runbook worker nodig heeft.

Het is raadzaam om de adressen te gebruiken die worden weer gegeven bij het definiëren van uitzonde ringen. Voor IP-adressen kunt u de [IP-adresbereiken van Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653)downloaden. Dit bestand wordt wekelijks bijgewerkt en weerspiegelt de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken.

Volg de instructies in [computers verbinden zonder Internet toegang](../azure-monitor/platform/gateway.md) voor het configureren van computers die geen toegang tot internet hebben.

## <a name="search-logs"></a>Logboeken zoeken

Naast de details die zijn opgenomen in de Azure Portal, kunt u zoeken naar de logboeken. Selecteer **log Analytics**op de pagina's met oplossingen. Het deel venster **zoeken** in Logboeken wordt geopend.

Meer informatie over het aanpassen van query's of het gebruik ervan vanaf verschillende clients en meer vindt u op:  [Documentatie over](
https://dev.loganalytics.io/)log Analytics Search-API.

### <a name="sample-queries"></a>Voorbeeldquery's

De volgende secties bevatten voorbeeld logboek query's voor update records die door deze oplossing worden verzameld:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Enkelvoudige Azure VM-evaluatie query's (Windows)

Vervang de VMUUID-waarde door de VM-GUID van de virtuele machine waarop u een query uitvoert. U kunt de VMUUID vinden die moet worden gebruikt door de volgende query uit te voeren in Azure Monitor logs:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Samen vatting van ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Lijst met ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>Enkelvoudige Azure VM-evaluatie query's (Linux)

Voor sommige Linux-distributies is er sprake [van een conflict](https://en.wikipedia.org/wiki/Endianness) tussen de VMUUID-waarden die afkomstig zijn van Azure Resource Manager en wat wordt opgeslagen in azure monitor Logboeken. Met de volgende query wordt gecontroleerd op een overeenkomst op basis van de endian. Vervang de waarden voor VMUUID door de indeling big endian en little-endian van de GUID om de resultaten correct te retour neren. U kunt de VMUUID vinden die moet worden gebruikt door de volgende query uit te voeren in Azure Monitor logs:`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Samen vatting van ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Lijst met ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>Multi-VM-evaluatie query's

##### <a name="computers-summary"></a>Computers overzicht

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Samen vatting van ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="computers-list"></a>Lijst met computers

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>Lijst met ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="using-dynamic-groups"></a>Dynamische groepen gebruiken

Updatebeheer biedt de mogelijkheid om een dynamische groep Azure-of niet-Azure Vm's te richten op update-implementaties. Deze groepen worden geëvalueerd op het moment van implementatie, zodat u uw implementatie niet hoeft te bewerken om machines toe te voegen.

> [!NOTE]
> U moet over de juiste machtigingen beschikken bij het maken van een update-implementatie. Zie [updates installeren](#install-updates)voor meer informatie.

### <a name="azure-machines"></a>Azure-machines

Deze groepen worden gedefinieerd door een query, wanneer de implementatie van een update wordt gestart, worden de leden van die groep geëvalueerd. Dynamische groepen werken niet met klassieke Vm's. Bij het definiëren van uw query kunnen de volgende items samen worden gebruikt om de dynamische groep te vullen

* Subscription
* Resourcegroepen
* Locaties
* Tags

![Groepen selecteren](./media/automation-update-management/select-groups.png)

Als u de resultaten van een dynamische groep wilt bekijken, klikt u op de knop **Preview** . In deze preview wordt op dat moment het groepslid maatschap weer gegeven, in dit voor beeld wordt gezocht naar machines met de label **functie** is gelijk aan **BackendServer**. Als deze tag wordt toegevoegd aan meer computers, worden deze toegevoegd aan toekomstige implementaties op basis van die groep.

![Preview-groepen](./media/automation-update-management/preview-groups.png)

### <a name="non-azure-machines"></a>Niet-Azure-machines

Voor niet-Azure-computers worden opgeslagen Zoek opdrachten ook wel computer groepen genoemd, gebruikt om de dynamische groep te maken. Zie [een computer groep maken](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)voor meer informatie over het maken van een opgeslagen zoek opdracht. Nadat de groep is gemaakt, kunt u deze selecteren in de lijst met opgeslagen Zoek opdrachten. Klik op **voor beeld** om op dat moment een voor beeld van de computers in de opgeslagen zoek opdracht te bekijken.

![Groepen selecteren](./media/automation-update-management/select-groups-2.png)

## <a name="integrate-with-system-center-configuration-manager"></a>Integreren met System Center Configuration Manager

Klanten die hebben geïnvesteerd in System Center Configuration Manager voor het beheren van Pc's, servers en mobiele apparaten, zijn ook afhankelijk van de kracht en de loop tijd van Configuration Manager om hen te helpen bij het beheren van software-updates. Configuration Manager maakt deel uit van de cyclus van software-update beheer (SUM).

Zie [System Center Configuration Manager met updatebeheer integreren](oms-solution-updatemgmt-sccmintegration.md)voor meer informatie over het integreren van de beheer oplossing met System Center Configuration Manager.

## <a name="inclusion-behavior"></a>Opname gedrag

Met update include kunt u specifieke updates opgeven die moeten worden toegepast. De geïnstalleerde patches of pakketten worden geïnstalleerd. Als er patches of pakketten zijn opgenomen en er een classificatie is geselecteerd, worden zowel de opgenomen items als de items die voldoen aan de classificatie geïnstalleerd.

Het is belang rijk te weten dat uitsluitingen insluitingen opheffen. Als u bijvoorbeeld een uitsluitings regel van `*`opgeeft, worden er geen patches of pakketten geïnstalleerd, aangezien deze allemaal uitgesloten zijn. Uitgesloten patches worden nog steeds weer gegeven als ontbrekend van de machine. Voor Linux-machines als een pakket is opgenomen, maar een afhankelijk pakket heeft dat is uitgesloten, is het pakket niet geïnstalleerd.

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

## <a name="remove-a-vm-from-update-management"></a>Een virtuele machine verwijderen uit Updatebeheer

Een virtuele machine verwijderen uit Updatebeheer:

* In uw Log Analytics-werk ruimte verwijdert u de virtuele machine uit de opgeslagen zoek opdracht `MicrosoftDefaultScopeConfig-Updates`voor de scope configuratie. U kunt opgeslagen Zoek opdrachten vinden onder **Algemeen** in uw werk ruimte.
* Verwijder [micro soft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) of de [log Analytics-agent voor Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

Ga verder met de zelf studie voor meer informatie over het beheren van updates voor uw virtuele Windows-machines.

> [!div class="nextstepaction"]
> [Updates en patches voor uw Azure Windows-Vm's beheren](automation-tutorial-update-management.md)

* Zoek opdrachten in Logboeken in [Azure monitor logboeken](../log-analytics/log-analytics-log-searches.md) gebruiken om gedetailleerde update gegevens weer te geven.
* [Waarschuwingen maken](automation-tutorial-update-management.md#configure-alerts) voor de implementatie status van de update.

* Zie [Software-update configuraties](/rest/api/automation/softwareupdateconfigurations) voor meer informatie over het werken met updatebeheer via de rest API.
* Zie [problemen oplossen updatebeheer](troubleshoot/update-management.md) voor meer informatie over het oplossen van problemen met de updatebeheer.
