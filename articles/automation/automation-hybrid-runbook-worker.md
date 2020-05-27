---
title: Overzicht van Azure Automation Hybrid Runbook Worker
description: Dit artikel bevat een overzicht van de Hybrid Runbook Worker, die u kunt gebruiken om runbooks uit te voeren op computers in uw lokale Data Center of Cloud provider.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 9305d0d6443c923c680af0d5fafc58887dadb902
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835288"
---
# <a name="hybrid-runbook-worker-overview"></a>Overzicht van Hybrid Runbook Worker

Runbooks in Azure Automation hebben mogelijk geen toegang tot resources in andere Clouds of in uw on-premises omgeving omdat ze worden uitgevoerd op het Azure-Cloud platform. U kunt de functie Hybrid Runbook Worker van Azure Automation gebruiken om runbooks rechtstreeks uit te voeren op de computer waarop de rol wordt gehost en aan resources in de omgeving om deze lokale resources te beheren. Runbooks worden opgeslagen en beheerd in Azure Automation en vervolgens aan een of meer toegewezen computers geleverd.

In de volgende afbeelding ziet u deze functionaliteit:

![Overzicht van Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Met een Hybrid Runbook Worker kunt u het Windows-of Linux-besturings systeem uitvoeren. Voor bewaking vereist het gebruik van Azure Monitor en een Log Analytics agent voor het ondersteunde besturings systeem. Zie [Azure monitor](automation-runbook-execution.md#azure-monitor)voor meer informatie.

Elke Hybrid Runbook Worker is lid van een Hybrid Runbook Worker groep die u opgeeft wanneer u de Agent installeert. Een groep kan één agent bevatten, maar u kunt meerdere agents in een groep installeren voor een hoge Beschik baarheid. Elke computer kan één Hybrid worker-rapportage hosten aan één Automation-account. 

Wanneer u een runbook op een Hybrid Runbook Worker start, geeft u de groep op waarop deze wordt uitgevoerd. Elke werk nemer in de groep pollt Azure Automation om te zien of er taken beschikbaar zijn. Als een taak beschikbaar is, haalt de eerste werk nemer de taak op. De verwerkings tijd van de taken wachtrij is afhankelijk van het hardwareprofiel en de belasting van de Hybrid Worker. U kunt een bepaalde werk nemer niet opgeven. 

Gebruik een Hybrid Runbook Worker in plaats van een [Azure-sandbox](automation-runbook-execution.md#runbook-execution-environment) omdat deze niet veel van de sandbox- [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) voor schijf ruimte, geheugen of netwerk sockets bevat. De limieten voor een Hybrid worker zijn alleen gerelateerd aan de eigen resources van de werk nemer. 

> [!NOTE]
> Hybrid Runbook Workers worden niet beperkt door de omvang van de [reële share](automation-runbook-execution.md#fair-share) tijd die Azure-sandboxes hebben. 

## <a name="hybrid-runbook-worker-installation"></a>Installatie van Hybrid Runbook Worker

Het proces voor het installeren van een Hybrid Runbook Worker is afhankelijk van het besturings systeem. In de volgende tabel worden de implementatie typen gedefinieerd.

|Besturingssysteem  |Implementatie typen  |
|---------|---------|
|Windows     | [Geautomatiseerd](automation-windows-hrw-install.md#automated-deployment)<br>[Handmatig](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

De aanbevolen installatie methode is om een Azure Automation runbook te gebruiken om het proces van het configureren van een Windows-computer volledig te automatiseren. Bij de tweede methode moet u een stapsgewijze procedure volgen om de rol hand matig te installeren en te configureren. Voor Linux-machines voert u een python-script uit om de agent op de computer te installeren.

## <a name="network-planning"></a><a name="network-planning"></a>Netwerkplanning

Het Hybrid Runbook Worker om verbinding te maken met Azure Automation, moet toegang hebben tot het poort nummer en de Url's die in deze sectie worden beschreven. De werk nemer moet ook toegang hebben tot de [poorten en url's die vereist zijn voor de log Analytics-agent](../azure-monitor/platform/agent-windows.md) om verbinding te maken met de Azure monitor log Analytics-werk ruimte.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

De volgende poort en Url's zijn vereist voor de Hybrid Runbook Worker:

* Poort: alleen TCP 443 vereist voor uitgaande internet toegang
* Globale URL: *. azure-automation.net
* Algemene URL van US Gov Virginia - *.azure-automation.us
* Agent service: https:// \< workspaceId \> . agentsvc.Azure-Automation.net

U wordt aangeraden de adressen te gebruiken die worden weer gegeven bij het definiëren van [uitzonde ringen](automation-runbook-execution.md#exceptions). Voor IP-adressen kunt u de [IP-adresbereiken van Microsoft Azure Data Center](https://www.microsoft.com/en-us/download/details.aspx?id=56519)downloaden. Dit bestand wordt wekelijks bijgewerkt en heeft de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken.

### <a name="dns-records-per-region"></a>DNS-records per regio

Als u een Automation-account hebt dat is gedefinieerd voor een specifieke regio, kunt u Hybrid Runbook Worker communicatie beperken tot dat regionale Data Center. De volgende tabel bevat de DNS-record voor elke regio.

| **Regio** | **DNS-record** |
| --- | --- |
| Australië - centraal |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Australië - oost |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Australië - zuidoost |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Canada - midden |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| India - centraal |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| VS - oost 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Japan - oost |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Europa - noord |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| VS - zuid-centraal |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Azië - zuidoost |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Verenigd Koninkrijk Zuid | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| VS (overheid) - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| VS - west-centraal | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Europa -west |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| VS - west 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

Down load het [Azure Data Center IP-adres](https://www.microsoft.com/download/details.aspx?id=41653) XML-bestand in het micro soft Download centrum voor een lijst met IP-adressen van regio's in plaats van regio namen. Er wordt wekelijks een bijgewerkt IP-adres bestand geplaatst. 

Het IP-adres bestand bevat de IP-adresbereiken die worden gebruikt in de Microsoft Azure data centers. Het omvat compute-, SQL-en opslag bereiken en weerspiegelt de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken. Nieuwe bereiken die in het bestand worden weer gegeven, worden gedurende ten minste één week niet gebruikt in de data centers.

Het is een goed idee om elke week het nieuwe IP-adres bestand te downloaden. Werk vervolgens uw site bij om de services die in Azure worden uitgevoerd, correct te identificeren. 

> [!NOTE]
> Als u Azure ExpressRoute gebruikt, moet u er rekening mee houden dat het IP-adres bestand wordt gebruikt voor het bijwerken van de Border Gateway Protocol (BGP) advertisement van Azure Space in de eerste week van elke maand.

### <a name="proxy-server-use"></a>Gebruik van proxy server

Als u een proxy server gebruikt voor communicatie tussen Azure Automation en de Log Analytics agent, moet u ervoor zorgen dat de juiste resources toegankelijk zijn. De time-out voor aanvragen van de Hybrid Runbook Worker-en Automation-Services is 30 seconden. Na drie pogingen mislukt een aanvraag. 

### <a name="firewall-use"></a>Gebruik van Firewall

Als u een firewall gebruikt om de toegang tot internet te beperken, moet u de firewall configureren om toegang toe te staan. Als u de Log Analytics-gateway als een proxy gebruikt, moet u ervoor zorgen dat deze is geconfigureerd voor Hybrid Runbook Workers. Zie [de log Analytics-gateway configureren voor Hybrid Automation-werk](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway)rollen.

## <a name="update-management-on-hybrid-runbook-worker"></a>Updatebeheer op Hybrid Runbook Worker

Als Azure Automation [updatebeheer](automation-update-management.md) is ingeschakeld, wordt een computer die is verbonden met uw log Analytics-werk ruimte automatisch geconfigureerd als een Hybrid Runbook Worker. Elke werk nemer kan runbooks ondersteunen die gericht zijn op update beheer. 

Een computer die op deze manier is geconfigureerd, is niet geregistreerd met Hybrid Runbook Worker groepen die al zijn gedefinieerd in uw Automation-account. U kunt de computer toevoegen aan een Hybrid Runbook Worker groep, maar u moet hetzelfde account gebruiken voor zowel Updatebeheer als het lidmaatschap van de Hybrid Runbook Worker-groep. Deze functionaliteit is toegevoegd aan versie 7.2.12024.0 van Hybrid Runbook Worker.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Updatebeheer adressen voor Hybrid Runbook Worker

Voor de standaard adressen en poorten die voor de Hybrid Runbook Worker vereist zijn, Updatebeheer de adressen in de volgende tabel nodig. Communicatie met deze adressen maakt gebruik van poort 443.

|Openbare Azure-peering  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net | *. blob.core.usgovcloudapi.net|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation status configuratie op een Hybrid Runbook Worker

U kunt [Azure Automation status configuratie](automation-dsc-overview.md) uitvoeren op een Hybrid Runbook Worker. Als u de configuratie wilt beheren van servers die ondersteuning bieden voor de Hybrid Runbook Worker, moet u de servers als DSC-knoop punten toevoegen. Zie [machines inschakelen voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks op een Hybrid Runbook Worker

Mogelijk hebt u runbooks die resources op de lokale computer beheren of worden uitgevoerd op resources in de lokale omgeving waar een Hybrid Runbook Worker wordt geïmplementeerd. In dit geval kunt u ervoor kiezen om uw runbooks uit te voeren op de Hybrid worker in plaats van een Automation-account. Runbooks die worden uitgevoerd op een Hybrid Runbook Worker, zijn identiek in de structuur van de mappen die u uitvoert in het Automation-account. Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid Runbook Worker taken

Hybrid Runbook Worker taken worden uitgevoerd onder het lokale **systeem** account op Windows of het [nxautomation-account](automation-runbook-execution.md#log-analytics-agent-for-linux) in Linux. Azure Automation verwerkt taken van Hybrid Runbook Workers enigszins anders dan taken die worden uitgevoerd in azure-sandboxes. Zie [Runbook Execution Environment](automation-runbook-execution.md#runbook-execution-environment).

Als de Hybrid Runbook Worker-hostcomputer opnieuw wordt opgestart, wordt elke actieve Runbook-taak opnieuw gestart vanaf het begin of van het laatste controle punt voor Power shell-werk stroom runbooks. Nadat een runbook-taak meer dan drie keer opnieuw is opgestart, wordt deze onderbroken.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook-machtigingen voor een Hybrid Runbook Worker

Omdat ze toegang hebben tot niet-Azure-resources, kunnen runbooks die worden uitgevoerd op een Hybrid Runbook Worker niet gebruikmaken van het verificatie mechanisme dat doorgaans wordt gebruikt door runbooks die verifiëren naar Azure-resources. Een runbook biedt een eigen verificatie voor lokale bronnen of configureert verificatie met behulp [van beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). U kunt ook een uitvoeren als-account opgeven om een gebruikers context te bieden voor alle runbooks.

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md)voor meer informatie over het configureren van uw runbooks om processen te automatiseren in uw on-premises Data Center of andere cloud omgeving.
* Zie [problemen met Hybrid Runbook worker oplossen](troubleshoot/hybrid-runbook-worker.md#general)voor meer informatie over het oplossen van problemen met uw Hybrid Runbook Workers.