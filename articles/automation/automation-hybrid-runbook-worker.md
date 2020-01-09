---
title: Azure Automation Hybrid Runbook Worker
description: Dit artikel bevat informatie over het installeren en gebruiken van Hybrid Runbook Worker, een functie van Azure Automation die u kunt gebruiken om runbooks uit te voeren op computers in uw lokale Data Center of in de Cloud provider.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: fd90b98f4df195297e31a5dda91f64efcc1f5212
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2020
ms.locfileid: "75658034"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Resources in uw Data Center of Cloud automatiseren met behulp van Hybrid Runbook Worker

Runbooks in Azure Automation hebben mogelijk geen toegang tot resources in andere Clouds of in uw on-premises omgeving omdat ze worden uitgevoerd op het Azure-Cloud platform. U kunt de functie Hybrid Runbook Worker van Azure Automation gebruiken om runbooks rechtstreeks uit te voeren op de computer waarop de rol wordt gehost en aan resources in de omgeving om deze lokale resources te beheren. Runbooks worden opgeslagen en beheerd in Azure Automation en vervolgens aan een of meer toegewezen computers geleverd.

In de volgende afbeelding ziet u deze functionaliteit:

![Overzicht van Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Elke Hybrid Runbook Worker is lid van een Hybrid Runbook Worker groep die u opgeeft wanneer u de Agent installeert. Een groep kan één agent bevatten, maar u kunt meerdere agents in een groep installeren voor een hoge Beschik baarheid. Elke machine kan één Hybrid Worker melden aan één Automation-account.

Wanneer u een runbook op een Hybrid Runbook Worker start, geeft u de groep op waarop deze wordt uitgevoerd. Elke werk nemer in de groep pollt Azure Automation om te zien of er taken beschikbaar zijn. Als een taak beschikbaar is, haalt de eerste werk nemer de taak op. De verwerkings tijd van de taken wachtrij is afhankelijk van het hardwareprofiel en de belasting van de Hybrid Worker. U kunt een bepaalde werk nemer niet opgeven. Hybrid Runbook Workers delen niet veel van de limieten die Azure-sandboxes hebben. Ze hebben niet dezelfde limieten op schijf ruimte, geheugen of netwerk sockets. Hybrid Runbook Workers worden alleen beperkt door de resources op het Hybrid Runbook Worker zelf. Daarnaast delen Hybrid Runbook Workers de 180 minuut [fair share](automation-runbook-execution.md#fair-share) tijd limiet die Azure-sandboxes wel hebben. Zie de pagina met taak [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) voor meer informatie over de service limieten voor Azure-sandboxes en Hybrid Runbook Workers.

## <a name="install-a-hybrid-runbook-worker"></a>Een Hybrid Runbook Worker installeren

Het proces voor het installeren van een Hybrid Runbook Worker is afhankelijk van het besturings systeem. De volgende tabel bevat koppelingen naar de methoden die u voor de installatie kunt gebruiken.

Als u een Windows-Hybrid Runbook Worker wilt installeren en configureren, kunt u twee methoden gebruiken. De aanbevolen methode maakt gebruik van een Automation-runbook om het proces van het configureren van een Windows-computer volledig te automatiseren. De tweede methode volgt een stapsgewijze procedure voor het hand matig installeren en configureren van de rol. Voor Linux-machines voert u een python-script uit om de agent op de computer te installeren.

|Besturingssysteem  |Implementatie typen  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Handmatig](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Als u de configuratie wilt beheren van uw servers die ondersteuning bieden voor de Hybrid Runbook Worker rol met behulp van desired state Configuration (DSC), moet u deze toevoegen als DSC-knoop punten. Voor meer informatie over het onboarden van deze voor beheer met DSC raadpleegt u [onboarding machines voor beheer door Azure Automation DSC](automation-dsc-onboarding.md).
>
>Als u de [updatebeheer oplossing](automation-update-management.md)inschakelt, wordt elke computer die is verbonden met uw Azure log Analytics-werk ruimte automatisch geconfigureerd als een Hybrid Runbook worker voor de ondersteuning van runbooks die in deze oplossing zijn opgenomen. De computer is echter niet geregistreerd bij Hybrid Worker groepen die al zijn gedefinieerd in uw Automation-account. De computer kan worden toegevoegd aan een Hybrid Runbook Worker groep in uw Automation-account ter ondersteuning van Automation-runbooks zolang u hetzelfde account gebruikt voor zowel de oplossing als het lidmaatschap van de Hybrid Runbook Worker-groep. Deze functionaliteit is toegevoegd aan versie 7.2.12024.0 van Hybrid Runbook Worker.

Bekijk de [informatie voor het plannen van uw netwerk](#network-planning) voordat u begint met de implementatie van een Hybrid Runbook Worker. Nadat u de werk nemer hebt geïmplementeerd, raadpleegt u [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md) voor meer informatie over het configureren van runbooks voor het automatiseren van processen in uw on-premises Data Center of een andere cloud omgeving.

De computer kan worden toegevoegd aan een Hybrid Runbook Worker groep in uw Automation-account ter ondersteuning van Automation-runbooks zolang u hetzelfde account gebruikt voor zowel de oplossing als het lidmaatschap van de Hybrid Runbook Worker-groep. Deze functionaliteit is toegevoegd aan versie 7.2.12024.0 van de Hybrid Runbook Worker.

## <a name="remove-a-hybrid-runbook-worker"></a>Een Hybrid Runbook Worker verwijderen

U kunt een of meer Hybrid Runbook Workers verwijderen uit een groep of u kunt de groep verwijderen, afhankelijk van uw vereisten. Als u een Hybrid Runbook Worker van een on-premises computer wilt verwijderen, gebruikt u de volgende stappen:

1. Ga in het Azure Portal naar uw Automation-account.
2. Onder **account instellingen**selecteert u **sleutels** en noteert u de waarden voor **URL** en **primaire toegangs sleutel**. U hebt deze informatie nodig voor de volgende stap.

### <a name="windows"></a>Windows

Open een Power shell-sessie in de beheerders modus en voer de volgende opdracht uit. Gebruik de schakel optie **-verbose** voor een gedetailleerd logboek van het verwijderings proces.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Als u verouderde machines uit uw Hybrid Worker groep wilt verwijderen, gebruikt u de optionele para meter `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

U kunt de opdracht `ls /var/opt/microsoft/omsagent` op de Hybrid Runbook Worker gebruiken om de workspaceid op te halen. Er bevindt zich een map in de map waarin de naam van de map de werk ruimte-id is.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Met deze code wordt de micro soft Monitoring Agent niet van de computer verwijderd, alleen de functionaliteit en configuratie van de Hybrid Runbook Worker rol.

## <a name="remove-a-hybrid-worker-group"></a>Een Hybrid Worker groep verwijderen

Als u een groep wilt verwijderen, moet u eerst de Hybrid Runbook Worker verwijderen van elke computer die lid is van de groep met behulp van de procedure die eerder is weer gegeven. Gebruik vervolgens de volgende stappen om de groep te verwijderen:

1. Open het Automation-account in de Azure Portal.
2. Onder **proces automatisering**selecteert u **Hybrid worker-groepen**. Selecteer de groep die u wilt verwijderen. De eigenschappen pagina voor die groep wordt weer gegeven.

   ![De pagina Eigenschappen](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Selecteer op de pagina eigenschappen voor de geselecteerde groep de optie **verwijderen**. Er wordt een bericht gevraagd om deze actie te bevestigen. Selecteer **Ja** als u zeker weet dat u wilt door gaan.

   ![Bevestigingsbericht](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Het kan enkele seconden duren voordat dit proces is voltooid. U kunt de voortgang bijhouden onder **Meldingen** in het menu.

## <a name="network-planning"></a>Uw netwerk configureren

### <a name="hybrid-worker-role"></a>Hybrid Worker rol

Het Hybrid Runbook Worker om verbinding te maken met Azure Automation, moet toegang hebben tot het poort nummer en de Url's die in deze sectie worden beschreven. Deze toegang bevindt zich boven de [poorten en url's die vereist zijn voor micro soft Monitoring Agent](../azure-monitor/platform/agent-windows.md) om verbinding te maken met Azure monitor Logboeken.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Als u een proxy server gebruikt voor communicatie tussen de agent en de Azure Automation-Service, moet u ervoor zorgen dat de juiste resources toegankelijk zijn. De time-out voor aanvragen van de Hybrid Runbook Worker en de Automation-Services is 30 seconden. Na 3 pogingen mislukt de aanvraag. Als u een firewall gebruikt om de toegang tot internet te beperken, moet u uw firewall zodanig configureren dat toegang wordt toegestaan. Als u de Log Analytics gateway als proxy gebruikt, moet u ervoor zorgen dat deze is geconfigureerd voor Hybrid worker-werk rollen. Zie [de log Analytics-gateway configureren voor Hybrid Automation-werk](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway)rollen voor meer informatie over hoe u dit moet doen.

De volgende poort en Url's zijn vereist voor de Hybrid Runbook Worker rol om te communiceren met Automation:

* Poort: alleen TCP 443 is vereist voor uitgaande internet toegang.
* Globale URL: *. azure-automation.net
* Globale URL van US Gov-Virginia: *. azure-automation.us
* Agent service: https://\<workspaceId\>. agentsvc.azure-automation.net

Het is raadzaam om de adressen te gebruiken die worden weer gegeven bij het definiëren van uitzonde ringen. Voor IP-adressen kunt u de [IP-adresbereiken van Microsoft Azure Data Center](https://www.microsoft.com/en-us/download/details.aspx?id=56519)downloaden. Dit bestand wordt wekelijks bijgewerkt en heeft de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken.

Als u een Automation-account hebt dat is gedefinieerd voor een specifieke regio, kunt u de communicatie beperken tot dat regionale Data Center. De volgende tabel bevat de DNS-record voor elke regio:

| **Regio** | **DNS-record** |
| --- | --- |
| VS - west-centraal | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| VS - zuid-centraal |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| VS - oost 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| VS - west 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Canada-Midden |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa - west |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa - noord |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Azië - zuidoost |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| India - centraal |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japan - Oost |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australië Oost |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Australië - zuidoost |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| VK - zuid | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Down load het [Azure Data Center IP-adres](https://www.microsoft.com/download/details.aspx?id=41653) XML-bestand in het micro soft Download centrum voor een lijst met IP-adressen van regio's in plaats van regio namen.

> [!NOTE]
> Het XML-bestand met IP-adressen van Azure Data Center geeft een lijst van de IP-adresbereiken die worden gebruikt in de Microsoft Azure data centers. Het bestand bevat compute-, SQL-en Storage-bereiken.
>
>Er wordt wekelijks een bijgewerkt bestand geplaatst. Het bestand weerspiegelt de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken. Nieuwe bereiken die in het bestand worden weer gegeven, worden gedurende ten minste één week niet gebruikt in de data centers.
>
> Het is een goed idee om elke week het nieuwe XML-bestand te downloaden. Werk vervolgens uw site bij om de services die in Azure worden uitgevoerd, correct te identificeren. Gebruikers van Azure ExpressRoute moeten weten dat dit bestand wordt gebruikt om de Border Gateway Protocol (BGP)-advertentie van Azure Space bij te werken in de eerste week van elke maand.

### <a name="update-management"></a>Updatebeheer

Op de standaard adressen en poorten die de Hybrid Runbook Worker vereist, zijn de volgende adressen specifiek vereist voor Updatebeheer. De communicatie met deze adressen geschiedt via poort 443.

|Openbare Azure-peering  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md)voor meer informatie over het configureren van uw runbooks om processen te automatiseren in uw on-premises Data Center of andere cloud omgeving.
* Zie [problemen met Hybrid Runbook Workers oplossen](troubleshoot/hybrid-runbook-worker.md#general) voor meer informatie over het oplossen van problemen met uw Hybrid runbook Workers

