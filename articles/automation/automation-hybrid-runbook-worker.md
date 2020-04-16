---
title: Azure Automation Hybrid Runbook Worker
description: In dit artikel vindt u informatie over het installeren en gebruiken van Hybride Runbook Worker, een functie van Azure Automation die u gebruiken om boeken uit te voeren op machines in uw lokale datacenter of cloudprovider.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1e4470ce5ac69390cf8d361577b9ebf0013e4e51
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405785"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Resources in uw datacenter of cloud automatiseren met Hybrid Runbook Worker

Runbooks in Azure Automation hebben mogelijk geen toegang tot bronnen in andere clouds of in uw on-premises omgeving omdat ze worden uitgevoerd op het Azure-cloudplatform. U de functie Hybride runbookworker van Azure Automation gebruiken om boeken rechtstreeks uit te voeren op de computer die de rol host en tegen resources in de omgeving om deze lokale bronnen te beheren. Runbooks worden opgeslagen en beheerd in Azure Automation en vervolgens geleverd aan een of meer toegewezen computers.

De volgende afbeelding illustreert deze functionaliteit:

![Overzicht van Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Elke hybride runbookworker is lid van een hybride runbookworkergroep die u opgeeft wanneer u de agent installeert. Een groep kan één agent bevatten, maar u meerdere agents in een groep installeren voor hoge beschikbaarheid. Elke machine kan één Hybride Arbeider-rapportage hosten naar één Automatiseringsaccount.

Wanneer u een runbook op een hybride runbookworker start, geeft u de groep op waarop deze wordt uitgevoerd. Elke werknemer in de groep polls Azure Automation om te zien of er taken beschikbaar zijn. Als er een taak beschikbaar is, neemt de eerste werknemer die de taak krijgt deze aan. De verwerkingstijd van de taakwachtrij is afhankelijk van het hardwareprofiel en de belasting van de hybride werknemer. U een bepaalde werknemer niet opgeven. Hybride Runbook-werknemers delen niet veel van de limieten die Azure-zandbakken hebben. Ze hebben niet dezelfde limieten voor schijfruimte, geheugen of netwerksockets. Hybride Runbook-werknemers worden alleen beperkt door de resources op de hybride runbookworker zelf. Bovendien delen hybride runbook-werknemers niet de tijdslimiet van 180 minuten voor [eerlijke delen](automation-runbook-execution.md#fair-share) die Azure-zandbakken hebben. Zie de [taaklimieten](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)voor meer informatie over de servicelimieten voor Azure-zandboxen en hybride runbook-werknemers.

## <a name="install-a-hybrid-runbook-worker"></a>Een hybride runbookworker installeren

Het proces voor het installeren van een hybride runbook worker is afhankelijk van het besturingssysteem. In de onderstaande tabel worden de implementatietypen gedefinieerd.

|OS  |Implementatietypen  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Handmatig](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

De aanbevolen installatiemethode is om een Automation runbook te gebruiken om het proces van het configureren van een Windows-computer volledig te automatiseren. De tweede methode is het volgen van een stapsgewijze procedure om de rol handmatig te installeren en te configureren. Voor Linux-machines voert u een Python-script uit om de agent op de machine te installeren.

> [!NOTE]
> Als u de configuratie wilt beheren van uw servers die de rol Hybride Runbook Worker met gewenste statusconfiguratie (DSC) ondersteunen, moet u de servers als DSC-knooppunten toevoegen. Zie [Onboarding-machines voor beheer door Azure Automation DSC voor](automation-dsc-onboarding.md)meer informatie over onboarding voor beheer bij DSC.
>
>Als u de [oplossing Updatebeheer](automation-update-management.md)inschakelt, wordt elke computer die is verbonden met uw Azure Log Analytics-werkruimte automatisch geconfigureerd als hybride runbookwerker ter ondersteuning van runbooks die in deze oplossing zijn opgenomen. De computer is echter niet geregistreerd bij hybride werknemersgroepen die al zijn gedefinieerd in uw Automatiseringsaccount. De computer kan worden toegevoegd aan een hybride runbook worker-groep in uw Automatiseringsaccount ter ondersteuning van automatiseringsrunbooks, zolang u hetzelfde account gebruikt voor zowel de oplossing als het lidmaatschap van de hybride runbookworkergroep. Deze functionaliteit is toegevoegd aan versie 7.2.12024.0 van Hybrid Runbook Worker.

Bekijk de [informatie voor het plannen van uw netwerk](#network-planning) voordat u begint met het implementeren van een hybride runbookworker. Nadat u de werknemer hebt geïmplementeerd, bekijkt u [Run-boeken uitvoeren op een hybride runbookworker](automation-hrw-run-runbooks.md) om te leren hoe u uw runbooks configureert om processen in uw on-premises datacenter of andere cloudomgeving te automatiseren.

De computer kan worden toegevoegd aan een hybride runbook worker-groep in uw Automatiseringsaccount ter ondersteuning van automatiseringsrunbooks, zolang u hetzelfde account gebruikt voor zowel de oplossing als het lidmaatschap van de hybride runbookworkergroep. Deze functionaliteit is toegevoegd aan versie 7.2.12024.0 van de Hybrid Runbook Worker.

## <a name="a-nameremove-a-hybrid-runbook-workerremove-a-hybrid-runbook-worker-from-an-on-premises-computer"></a><a name="remove-a-hybrid-runbook-worker">Een hybride runbookworker verwijderen van een on-premises computer

U een hybride runbookworker verwijderen van een on-premises computer zoals beschreven in deze sectie voor Windows en Linux.

### <a name="remove-the-worker-on-windows"></a>De werknemer verwijderen in Windows

1. Ga in de Azure-portal naar uw Automatiseringsaccount.
2. Selecteer **onder Accountinstellingen** **toetsen** en noteer de waarden voor **URL** en **primaire toegangssleutel**.

3. Open een PowerShell-sessie in de administratormodus en voer de volgende opdracht uit met uw URL en primaire toegangswaarden. Gebruik `Verbose` de parameter voor een gedetailleerd logboek van het verwijderingsproces. Als u verouderde machines uit uw groep `machineName` Hybride werker wilt verwijderen, gebruikt u de optionele parameter.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="remove-the-worker-on-linux"></a>De werknemer op Linux verwijderen

U de `ls /var/opt/microsoft/omsagent` opdracht hybride runbookworker gebruiken om de werkruimte-id op te halen. Er wordt een map gemaakt met de naam van de werkruimte-id.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Met deze code wordt de Microsoft Monitoring Agent niet van de computer verwijderd. Hiermee worden alleen de functionaliteit en configuratie van de rol Hybride Runbook Worker verwijderd.

## <a name="remove-a-hybrid-worker-group"></a>Een Hybrid Worker-groep verwijderen

Als u een groep Hybride Runbook Worker wilt verwijderen, moet u eerst de hybride runbookworker verwijderen van elke computer die lid is van de groep. Gebruik vervolgens de volgende stappen om de groep te verwijderen:

1. Open het automatiseringsaccount in de Azure-portal.
2. Selecteer **Hybride werknemersgroepen** onder **Procesautomatisering**. Selecteer de groep die u wilt verwijderen. De eigenschappenpagina voor die groep wordt weergegeven.

   ![De pagina Eigenschappen](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Selecteer **Verwijderen**op de pagina eigenschappen voor de geselecteerde groep . In een bericht wordt u gevraagd deze actie te bevestigen. Selecteer **Ja** als u zeker weet dat u door wilt gaan.

   ![Bevestigingsbericht](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Dit proces kan enkele seconden duren om te voltooien. U kunt de voortgang bijhouden onder **Meldingen** in het menu.

## <a name="configure-your-network"></a><a name="network-planning"></a>Uw netwerk configureren

### <a name="hybrid-worker-role"></a>Rol Hybride arbeider

Als u de hybride runbookworker verbinding wilt maken met en zich wilt registreren met Azure Automation, moet deze toegang hebben tot het poortnummer en de URL's die in deze sectie worden beschreven. Deze toegang is bovenop de [poorten en URL's die nodig zijn voor Microsoft Monitoring Agent](../azure-monitor/platform/agent-windows.md) om verbinding te maken met Azure Monitor-logboeken.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Als u een proxyserver gebruikt voor communicatie tussen de agent en de Azure Automation-service, moet u ervoor zorgen dat de juiste resources toegankelijk zijn. De time-out voor aanvragen van de hybride runbookworker en de automatiseringsservices is 30 seconden. Na 3 pogingen zal het verzoek mislukken. Als u een firewall gebruikt om de toegang tot internet te beperken, moet u uw firewall configureren om toegang te verlenen. Als u de Log Analytics-gateway als proxy gebruikt, moet u ervoor zorgen dat deze is geconfigureerd voor hybride werknemers. Zie [De logboekanalysegateway configureren voor hybride werknemers voor automatisering](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway)voor instructies voor instructies.

De volgende poort- en URL's zijn vereist om de rol Hybride runbookworker te communiceren met Automatisering:

* Poort: Alleen TCP 443 is vereist voor uitgaande internettoegang.
* Globale URL: *.azure-automation.net
* Algemene URL van US Gov Virginia - *.azure-automation.us
* Agentservice:\<https://\>workspaceId .agentsvc.azure-automation.net

Het wordt aanbevolen om de adressen te gebruiken die worden vermeld bij het definiëren van uitzonderingen. Voor IP-adressen u de [IP-bereiken van Microsoft Azure Datacenter](https://www.microsoft.com/en-us/download/details.aspx?id=56519)downloaden. Dit bestand wordt wekelijks bijgewerkt en heeft de momenteel geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken.

Als u een Automatiseringsaccount hebt dat is gedefinieerd voor een bepaalde regio, u de communicatie beperken tot dat regionale datacenter. In de volgende tabel vindt u de DNS-record voor elke regio:

| **Regio** | **DNS-record** |
| --- | --- |
| VS - west-centraal | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| VS - zuid-centraal |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| VS - oost 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| VS - west 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Canada - midden |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa -west |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa - noord |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Azië - zuidoost |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| India - centraal |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japan - oost |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australië - centraal |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Australië - oost |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Australië - zuidoost |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Verenigd Koninkrijk Zuid | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| VS (overheid) - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Download het [XML-bestand azure datacenter-adres](https://www.microsoft.com/download/details.aspx?id=41653) van het Microsoft Downloadcentrum voor een lijst met regio-IP-adressen in plaats van regionamen.

> [!NOTE]
> Het XML-bestand met XML-adres azure datacenter bevat de IP-adresbereiken die worden gebruikt in de Microsoft Azure-datacenters. Het bestand bevat compute-, SQL- en opslagbereiken.
>
>Er wordt wekelijks een bijgewerkt bestand geplaatst. Het bestand weerspiegelt de momenteel geïmplementeerde bereiken en eventuele komende wijzigingen in de IP-bereiken. Nieuwe bereiken die in het bestand worden weergegeven, worden gedurende ten minste één week niet in de datacenters gebruikt.
>
> Het is een goed idee om het nieuwe XML-bestand elke week te downloaden. Werk vervolgens uw site bij om de services die in Azure worden uitgevoerd, correct te identificeren. Azure ExpressRoute-gebruikers moeten er rekening mee houden dat dit bestand wordt gebruikt om de BGP-advertentie (Border Gateway Protocol) van Azure-ruimte in de eerste week van elke maand bij te werken.

### <a name="update-management"></a>Updatebeheer

Naast de standaardadressen en poorten die de Hybride Runbook Worker nodig heeft, zijn de volgende adressen specifiek vereist voor UpdateManagement. Communicatie naar deze adressen gebeurt via poort 443.

|Openbare Azure-peering  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Volgende stappen

* Zie Logboeken uitvoeren [op een hybride runbookworker](automation-hrw-run-runbooks.md)voor meer informatie over het configureren van uw runbooks om processen in uw on-premises datacenter of andere cloudomgeving te automatiseren.
* Zie Problemen met hybride runbookworkers voor meer informatie over het oplossen van problemen met uw hybride [runbook-werknemers.](troubleshoot/hybrid-runbook-worker.md#general)