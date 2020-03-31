---
title: Problemen met de Log Analytics-agent voor Windows oplossen
description: Beschrijf de symptomen, oorzaken en oplossingen voor de meest voorkomende problemen met de log-analyse-agent voor Windows in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 4112555347ce1d718375fbab3f166c6f2f5deeaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333510"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Problemen met de Log Analytics-agent voor Windows oplossen 

In dit artikel u helpen bij het oplossen van fouten die u mogelijk ondervindt van de Log Analytics-agent voor Windows in Azure Monitor en worden mogelijke oplossingen voorgesteld om deze op te lossen.

Als geen van deze stappen voor u werkt, zijn de volgende ondersteuningskanalen ook beschikbaar:

* Klanten met Premier-ondersteuningsvoordelen kunnen een ondersteuningsaanvraag openen bij [Premier.](https://premier.microsoft.com/)
* Klanten met Azure-ondersteuningsovereenkomsten kunnen een ondersteuningsaanvraag openen [in de Azure-portal.](https://manage.windowsazure.com/?getsupport=true)
* Ga naar de feedbackpagina van Log [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) Analytics om ingediende ideeën en bugs te bekijken of een nieuwe in te dienen. 

## <a name="important-troubleshooting-sources"></a>Belangrijke bronnen voor het oplossen van problemen

 Om problemen met het oplossen van problemen met betrekking tot de Log Analytics-agent voor Windows op te lossen, registreert de agent gebeurtenissen in het Windows-gebeurtenislogboek, specifiek onder *Toepassing en Services\Operations Manager*.  

## <a name="connectivity-issues"></a>Connectiviteitsproblemen

Als de agent communiceert via een proxyserver of firewall, zijn er mogelijk beperkingen die communicatie van de broncomputer en de Azure Monitor-service verhinderen. In het geval dat de communicatie wordt geblokkeerd, als gevolg van verkeerde configuratie, kan registratie bij een werkruimte mislukken tijdens het installeren van de agent of configureren van de agent na de installatie om te rapporteren aan een extra werkruimte. Agentcommunicatie kan mislukken na een succesvolle registratie. In deze sectie worden de methoden beschreven om dit type probleem met de Windows-agent op te lossen.

Controleer nogmaals of de firewall of proxy is geconfigureerd om de volgende poorten en URL's toe te staan die in de volgende tabel zijn beschreven. Bevestig ook dat HTTP-inspectie niet is ingeschakeld voor webverkeer, omdat het een veilig TLS-kanaal tussen de agent en Azure Monitor kan voorkomen.  

|Agentresource|Poorten |Richting |HTTPS-controle overslaan|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Poort 443 |Uitgaand|Ja |  
|*.oms.opinsights.azure.com |Poort 443 |Uitgaand|Ja |  
|*.blob.core.windows.net |Poort 443 |Uitgaand|Ja |  

Zie [Azure Government Management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)voor firewallgegevens die vereist zijn voor Azure Government. Als u van plan bent de Azure Automation Hybrid Runbook Worker te gebruiken om verbinding te maken met en te registreren bij de automatiseringsservice om runbooks of beheeroplossingen in uw omgeving te gebruiken, moet deze toegang hebben tot het poortnummer en de URL's die zijn beschreven in [Uw netwerk configureren voor de hybride runbookworker.](../../automation/automation-hybrid-runbook-worker.md#network-planning) 

Er zijn verschillende manieren waarop u controleren of de agent succesvol communiceert met Azure Monitor.

- Schakel de [azure log Analytics Agent Health-beoordeling](../insights/solution-agenthealth.md) in de werkruimte in. Bekijk in het dashboard Agentstatus de kolom **Aantal niet-reagerende agents** om snel te zien of de agent wordt weergegeven.  

- Voer de volgende query uit om te bevestigen dat de agent een heartbeat verzendt naar de werkruimte waaraan deze is geconfigureerd om te rapporteren. Vervang `<ComputerName>` door de werkelijke naam van de machine.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Als de computer met de service communiceert, moet de query een resultaat retourneren. Als de query geen resultaat heeft opgeleverd, controleert u eerst of de agent is geconfigureerd om te rapporteren aan de juiste werkruimte. Als het correct is geconfigureerd, gaat u door met stap 3 en zoekt u in het Windows-gebeurtenislogboek om te bepalen of de agent registreert welk probleem het mogelijk kan maken om te communiceren met Azure Monitor.

- Een andere methode om een verbindingsprobleem te identificeren, is door de **TestCloudConnectivity-tool** uit te voeren. Het hulpprogramma is standaard geïnstalleerd met de agent in de map *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*. Navigeer vanuit een opdrachtprompt met verhoogde bevoegdheid naar de map en voer het gereedschap uit. De tool retourneert de resultaten en markeert waar de test is mislukt (bijvoorbeeld als deze gerelateerd was aan een bepaalde poort/URL die is geblokkeerd). 

    ![Uitvoeringsresultaten van testCloudConnection-hulpprogramma's](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filter het gebeurtenislogboek *Operations Manager* op*Statusservicemodules*van **gebeurtenisbronnen,** -  *HealthService*en *Serviceconnector* en filter op *waarschuwing* en *fout* **op gebeurtenisniveau** om te bevestigen of er gebeurtenissen uit de volgende tabel zijn geschreven. Als dit het geval is, bekijkt u de oplossingsstappen die zijn opgenomen voor elke mogelijke gebeurtenis.

    |Gebeurtenis-id |Bron |Beschrijving |Oplossing |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Health Service |Verbinding met de service van de agent is mislukt |Deze fout kan optreden wanneer de agent niet rechtstreeks of via een firewall/proxyserver naar de Azure Monitor-service kan communiceren. Controleer de proxy-instellingen van de agent of dat de netwerkfirewall/proxy TCP-verkeer van de computer naar de service toestaat.|
    |2138 |Health Service-modules |Proxy vereist verificatie |Configureer de proxy-instellingen van de agent en geef de gebruikersnaam/wachtwoord op die vereist is om te verifiëren met de proxyserver. |
    |2129 |Health Service-modules |Mislukte TLS-onderhandeling |Controleer de TCP/IP-instellingen van uw netwerkadapter en proxy-instellingen voor agenten.|
    |2127 |Health Service-modules |Fout verzenden van ontvangen gegevens foutcode |Als het alleen periodiek gebeurt gedurende de dag, kan het gewoon een willekeurige anomalie die kan worden genegeerd. Controleer hoe vaak het gebeurt. Als dit vaak de hele dag gebeurt, controleert u eerst uw netwerkconfiguratie en proxy-instellingen. Als de beschrijving HTTP-foutcode 404 bevat en het de eerste keer is dat de agent gegevens naar de service probeert te verzenden, bevat deze een 500-fout met een interne foutcode van 404. 404 betekent niet gevonden, wat erop wijst dat de opslagruimte voor de nieuwe werkruimte nog steeds wordt ingericht. Bij de volgende nieuwe poging worden gegevens zoals verwacht naar de werkruimte geschreven. Een HTTP-fout 403 kan duiden op een probleem met machtigingen of referenties. Er is meer informatie opgenomen met de 403-fout om het probleem op te lossen.|
    |4000 |Serviceconnector |DNS-naamomzetting is mislukt |De machine kan het internetadres dat wordt gebruikt bij het verzenden van gegevens naar de service niet oplossen. Dit kunnen DNS resolver-instellingen op uw machine zijn, onjuiste proxy-instellingen of misschien een tijdelijk DNS-probleem met uw provider. Als dit periodiek gebeurt, kan dit worden veroorzaakt door een tijdelijk netwerkgerelateerd probleem.|
    |4001 |Serviceconnector |De verbinding met de service is mislukt. |Deze fout kan optreden wanneer de agent niet rechtstreeks of via een firewall/proxyserver naar de Azure Monitor-service kan communiceren. Controleer de proxy-instellingen van de agent of dat de netwerkfirewall/proxy TCP-verkeer van de computer naar de service toestaat.|
    |4002 |Serviceconnector |De service heeft http-statuscode 403 geretourneerd als reactie op een query. Neem contact op met de servicebeheerder voor de status van de service. De query wordt later opnieuw geprobeerd. |Deze fout wordt geschreven tijdens de eerste registratiefase van de agent en u ziet een URL die vergelijkbaar is met de volgende URL: *https://\<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Een foutcode 403 betekent verboden en kan worden veroorzaakt door een verkeerd getypte Workspace-id of sleutel, of de gegevens en tijd is onjuist op de computer. Als de tijd +/- 15 minuten van de huidige tijd is, mislukt de onboarding. Als u dit wilt corrigeren, werkt u de datum en/of tijdzone van uw Windows-computer bij.|

## <a name="data-collection-issues"></a>Problemen met het verzamelen van gegevens

Nadat de agent is geïnstalleerd en rapporteert aan de geconfigureerde werkruimte of werkruimten, kan deze stoppen met het ontvangen van configuratie, het verzamelen of doorsturen van prestaties, logboeken of andere gegevens naar de service, afhankelijk van wat is ingeschakeld en de doelgroep van de computer. Het is noodzakelijk om te bepalen of:

- Is het een bepaald gegevenstype of alle gegevens die niet beschikbaar zijn in de werkruimte?
- Is het gegevenstype opgegeven door een oplossing of opgegeven als onderdeel van de configuratie van de werkruimtegegevensverzameling?
- Hoeveel computers worden beïnvloed? Is het een enkele of meerdere computers die naar de werkruimte worden verzonden?
- Werkte het en stopte het op een bepaald moment van de dag, of is het nooit verzameld? 
- Is de log search query die u gebruikt syntactisch correct? 
- Heeft de agent ooit zijn configuratie ontvangen van Azure Monitor?

De eerste stap in het oplossen van problemen is om te bepalen of de computer een heartbeat-gebeurtenis verzendt.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Als de query resultaten retourneert, moet u bepalen of een bepaald gegevenstype niet wordt verzameld en doorgestuurd naar de service. Dit kan worden veroorzaakt doordat de agent geen bijgewerkte configuratie van de service ontvangt, of een ander symptoom waardoor het agent niet normaal kan werken. Voer de volgende stappen uit om problemen verder op te lossen.

1. Open een opdrachtprompt met verhoogde bevoegdheid op de `net stop healthservice && net start healthservice`computer en start de agentservice opnieuw door te typen .
2. Open het operations *manager-gebeurtenislogboek* en zoek naar **gebeurtenis-id's** *7023, 7024, 7025, 7028* en *1210* vanuit Event **source** *HealthService*.  Deze gebeurtenissen geven aan dat de agent een configuratie ontvangt van Azure Monitor en dat ze de computer actief controleren. In de beschrijving van de gebeurtenis-ID 1210 worden op de laatste regel ook alle oplossingen en inzichten vermeld die zijn opgenomen in het toezichtbereik van de agent.  

    ![Beschrijving gebeurtenis-id 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Als u na enkele minuten de verwachte gegevens niet ziet in de queryresultaten of visualisatie, afhankelijk van of u de gegevens uit een oplossing of Insight bekijkt, zoekt u in het gebeurtenislogboek *Operations Manager* naar **Gebeurtenisbronnen** *HealthService-* en Health Service *Modules* en filtert u op *Waarschuwing* en *fout* **op gebeurtenisniveau** om te bevestigen of deze gebeurtenissen uit de volgende tabel hebben geschreven.

    |Gebeurtenis-id |Bron |Beschrijving |Oplossing |
    |---------|-------|------------|
    |8000 |HealthService |Deze gebeurtenis geeft aan of een werkstroom met betrekking tot prestaties, gebeurtenis of ander gegevenstype dat is verzameld, niet kan doorsturen naar de service voor opname naar de werkruimte. | Gebeurtenis-ID 2136 van bron HealthService is samen met deze gebeurtenis geschreven en kan aangeven dat de agent niet in staat is om met de service te communiceren, mogelijk als gevolg van een verkeerde configuratie van de proxy- en verificatie-instellingen, netwerkuitval of de netwerkfirewall/proxy geen TCP-verkeer van de computer naar de service toestaat.| 
    |10102 en 10103 |Health Service-modules |De werkstroom kan de gegevensbron niet oplossen. |Dit kan gebeuren als het opgegeven prestatiemeterof instantie niet op de computer aanwezig is of onjuist is gedefinieerd in de instellingen voor werkruimtegegevens. Als dit een door de gebruiker opgegeven [prestatiemeter is,](data-sources-performance-counters.md#configuring-performance-counters)controleert u of de opgegeven informatie de juiste indeling volgt en op de doelcomputers bestaat. |
    |26002 |Health Service-modules |De werkstroom kan de gegevensbron niet oplossen. |Dit kan gebeuren als het opgegeven Windows-gebeurtenislogboek niet op de computer bestaat. Deze fout kan veilig worden genegeerd als de computer naar verwachting dit gebeurtenislogboek niet heeft geregistreerd, anders als dit een door de gebruiker opgegeven [gebeurtenislogboek](data-sources-windows-events.md#configuring-windows-event-logs)is, controleert u of de opgegeven informatie juist is. |

