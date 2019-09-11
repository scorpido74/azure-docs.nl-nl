---
title: Handleiding voor het oplossen van problemen met Azure Security Center | Microsoft Docs
description: Dit document helpt u bij het oplossen van problemen in Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 26615819dc407e51281254c73076a1d721e6059f
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873389"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Handleiding voor het oplossen van problemen met Azure Security Center
Deze handleiding is bedoeld voor IT-specialisten, informatiebeveiligingsanalisten en cloudbeheerders van organisaties die Azure Security Center gebruiken en biedt procedures voor het oplossen van problemen met Azure Security Center.

>[!NOTE]
>Security Center gebruikt de micro soft Monitoring Agent voor het verzamelen en opslaan van gegevens. Zie [Migratie van Azure Security Center-platform](security-center-platform-migration.md) voor meer informatie.
>

## <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen
In deze handleiding wordt uitgelegd hoe u problemen oplost die betrekking hebben op Security Center. In de meeste gevallen vindt probleemoplossing in Security Center plaats door eerst de records in het [Controlelogboek](../azure-monitor/platform/activity-logs-overview.md) van het onderdeel met de fout te raadplegen. Met controlelogboeken kunt u het volgende bepalen:

* Welke bewerkingen er hebben plaatsgevonden
* Wie de bewerking heeft gestart
* Wanneer de bewerking is uitgevoerd
* De status van de bewerking
* De waarden van andere eigenschappen die u kunnen helpen bij het onderzoeken van het probleem

In het controlelogboek staan alle schrijfbewerkingen (PUT, POST, DELETE) die op uw resources zijn uitgevoerd, maar er staan geen leesbewerkingen (GET) in.

## <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent
Security Center maakt gebruik van micro soft monitoring agent. Dit is dezelfde agent die wordt gebruikt door de Azure Monitor-service: om beveiligings gegevens van uw Azure virtual machines te verzamelen. Nadat het verzamelen van gegevens is ingeschakeld en de agent juist is geïnstalleerd op de doelcomputer, wordt als het goed is dit proces uitgevoerd:

* HealthService.exe

Als u de console voor servicebeheer (services.msc) opent, ziet u ook dat de service Microsoft Monitoring Agent wordt uitgevoerd, zoals hieronder is weergegeven:

![Services](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Als u wilt zien welke versie van de agent u hebt, opent u **Taakbeheer**, zoekt u op het tabblad **Processen** **Microsoft Monitoring Agent-Service**, klikt u er met de rechtermuisknop op en klikt u ten slotte op **Eigenschappen**. Op het tabblad **Details** kunt u de bestandsversie vinden, zoals hieronder wordt weergegeven:

![File](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)


## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Installatiescenario's voor Microsoft Monitoring Agent
Er zijn twee installatiescenario's die verschillende resultaten kunnen opleveren bij het installeren van Microsoft Monitoring Agent op uw computer. Dit zijn de ondersteunde scenario's:

* **Agent automatisch geïnstalleerd door Security Center**: in dit scenario kunt u de waarschuwingen op beide locaties bekijken, in Security Center en door te zoeken in logboeken. U ontvangt e-mail meldingen voor het e-mail adres dat is geconfigureerd in het beveiligings beleid voor het abonnement waartoe de resource behoort.
.
* De **agent is hand matig geïnstalleerd op een virtuele machine die zich in azure bevindt**: in dit scenario kunt u de waarschuwingen in de Security Center Portal alleen bekijken als u deze hand 2017 matig hebt gedownload en geïnstalleerd op het abonnement van de werk ruimte. behoort. Als u filtert op het abonnement waartoe de resource behoort, worden er geen waarschuwingen weer geven. U ontvangt e-mail meldingen voor het e-mail adres dat is geconfigureerd in het beveiligings beleid voor het abonnement waarvan de werk ruimte deel uitmaakt.

>[!NOTE]
> U kunt het gedrag uit het tweede scenario voorkomen door de nieuwste versie van de agent te downloaden.
>

## Problemen met de agentstatus controleren <a name="mon-agent"></a>
**Bewakingsstatus** toont de reden waarom Security Center VM's en computers die zijn geïnitialiseerd voor automatische inrichting niet afdoende kan bewaken. In de volgende tabel ziet u de waarden, beschrijvingen en oplossingen voor **Bewakingsstatus**.

| Bewakingsstatus | Description | Oplossingen |
|---|---|---|
| Agent wacht op installatie | De installatie van de MMA is nog bezig.  De installatie kan enkele uren duren. | Wacht totdat de automatische installatie is voltooid. |
| Energiestatus uitgeschakeld | De virtuele machine is gestopt.  De MMA kan alleen worden geïnstalleerd op een virtuele machine die wordt uitgevoerd. | Start de VM opnieuw. |
| Azure VM-agent ontbreekt of is ongeldig | De MMA is nog niet geïnstalleerd.  Security Center installeert de extensie pas als er een geldige Azure VM-agent is. | Installeer de Azure VM-agent (opnieuw) op de virtuele machine of voer een upgrade uit. |
| VM-status niet gereed voor installatie  | De MMA is nog niet geïnstalleerd omdat de virtuele machine niet gereed is voor installatie. De virtuele machine is niet gereed voor installatie vanwege een probleem met de VM-agent of de VM-inrichting. | Controleer de status van uw virtuele machine. Ga terug naar **Virtuele machines** in de portal en selecteer de virtuele machine voor statusinformatie. |
|Installatie is mislukt: algemene fout | De MMA is geïnstalleerd, maar werkt niet vanwege een fout. | [Installeer de extensie handmatig](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) of verwijder de extensie zodat Security Center deze opnieuw probeert te installeren. |
| Installatie is mislukt: lokale agent is al geïnstalleerd | De installatie van MMA is mislukt. Security Center heeft vastgesteld dat er al een lokale agent (Log Analytics of System Center Operations Manager) is geïnstalleerd op de VM. De installatie van MMA is gestopt om een multihoming-configuratie - de virtuele machine rapporteert aan twee afzonderlijke werkruimten - te voorkomen. | U kunt dit op twee manieren oplossen: [de extensie handmatig installeren](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) en verbinden met de gewenste werkruimte. Of, de gewenste werkruimte instellen als uw standaardwerkruimte en automatische inrichting van de agent inschakelen.  Zie [Automatische inrichting inschakelen](security-center-enable-data-collection.md). |
| Kan geen verbinding maken tussen agent en werkruimte | De MMA is geïnstalleerd, maar werkt niet vanwege een netwerkverbinding.  Controleer of er internettoegang is. En anders moet er een geldige HTTP-proxy voor de agent zijn geconfigureerd. | Zie netwerk vereisten voor de bewakings agent. |
| Agent verbonden met ontbrekende of onbekende werkruimte | Security Center heeft gedetecteerd dat MMA die is geïnstalleerd op de VM, verbonden is met een werkruimte waartoe deze geen toegang heeft. | Dit kan gebeuren in twee gevallen. De werkruimte is verwijderd en bestaat niet meer. Installeer de agent opnieuw met de juiste werkruimte of verwijder de agent en laat Security Center de automatische inrichtingsinstallatie voltooien. In het tweede geval behoort de werkruimte tot een abonnement waarvoor Security Center geen machtigingen heeft. Security Center verleent de Microsoft Security Resource Provider toegang op basis van een abonnement. Als u toegang wilt inschakelen, registreert u het abonnement op Microsoft Security Resource Provider. U kunt dit doen via API, PowerShell, portal of gewoon door in het dashboard **Overzicht** van het Security Center te filteren op abonnement. Zie [Resourceproviders en -typen](../azure-resource-manager/resource-manager-supported-services.md#azure-portal) voor meer informatie. |
| Agent reageert niet of ID ontbreekt | Security Center kan de beveiligingsgegevens die zijn gescand van de virtuele machine niet ophalen, zelfs niet als de agent is geïnstalleerd. | De agent rapporteert geen gegevens, ook de heartbeat niet. De agent is mogelijk beschadigd of het verkeer wordt geblokkeerd. Of de agent rapporteert gegevens, maar beschikt niet over de Azure-resource-id, waardoor de juiste Azure VM voor de gegevens niet kan worden gevonden. Zie voor het oplossen van Linux, [Troubleshooting Guide voor Log Analytics-Agent voor Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal). Zie [Troubleshooting Windows Virtual Machines](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines) (Problemen met virtuele Windows-machines oplossen) voor het oplossen van problemen in Windows. |
| Agent niet geïnstalleerd | Gegevensverzameling is uitgeschakeld. | Schakel gegevensverzameling in het beveiligingsbeleid in of installeer de MMA handmatig. |


## Problemen oplossen met de netwerkvereisten voor de Monitoring Agent <a name="mon-network-req"></a>
Agents kunnen alleen verbinding maken met Security Center en zich daarbij registreren als ze toegang hebben tot netwerkbronnen, inclusief de poortnummers en domein-URL's.

- Voor proxyservers moet u ervoor zorgen dat de juiste resources voor de proxyserver zijn geconfigureerd in de instellingen voor de agent. Lees dit artikel voor meer informatie over [het wijzigen van de proxy-instellingen](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents).
- Als u een firewall gebruikt om de toegang tot internet te beperken, moet u uw firewall zodanig configureren dat toegang tot Log Analytics wordt toegestaan. De agent-instellingen hoeven niet te worden aangepast.

De volgende tabel bevat de resources die nodig zijn voor communicatie.

| Agentresource | Poorten | HTTPS-controle overslaan |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Ja |
| *.oms.opinsights.azure.com | 443 | Ja |
| *.blob.core.windows.net | 443 | Ja |
| *.azure-automation.net | 443 | Ja |

Als er problemen zijn tijdens het onboarden van de agent, leest u het artikel [Onboarding-problemen van Operations Management Suite oplossen](https://support.microsoft.com/en-us/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).


## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Oplossen van problemen met niet goed werkende eindpuntbeveiliging

De gastagent is het bovenliggende proces van alles wat de [Microsoft Antimalware](../security/fundamentals/antimalware.md)-extensie doet. Wanneer het proces van de gastagent mislukt, kan de Microsoft Antimalware die wordt uitgevoerd als onderliggend proces van de gastagent ook mislukken.  In dergelijke scenario's wordt aanbevolen de volgende opties te controleren:

- Als de doel-VM een aangepaste installatiekopie is en de maker van de VM nooit een gastagent heeft geïnstalleerd.
- Als het doel een Linux-VM in plaats van een Windows-VM is, mislukt het installeren van de Windows-versie van de anti-malware-extensie op een Linux-VM. De Linux-gastagent heeft specifieke vereisten voor het besturingssysteem en de vereiste pakketten. Als niet aan deze vereisten wordt voldaan, werkt de VM-agent ook niet.
- Als de VM is gemaakt met een oudere versie van gastagent. Als dat het geval is, houd er dan rekening mee dat een aantal oude agents niet automatisch kunnen bijwerken naar de nieuwe versie en dat dit tot dit probleem kan leiden. Gebruik altijd de nieuwste versie van gastagent als u uw eigen installatiekopieën maakt.
- Sommige beheersoftware van derden kan de gastagent uitschakelen of toegang tot bepaalde bestandslocaties blokkeren. Als u beheersoftware van derden op de VM hebt geïnstalleerd, zorgt u ervoor dat de agent op de uitsluitingslijst staat.
- Bepaalde firewallinstellingen of netwerkbeveiligingsgroepen (NSG) blokkeren mogelijk het netwerkverkeer naar en van de gastagent.
- Een toegangsbeheerlijst (ACL) blokkeert mogelijk de toegang tot de schijf.
- Onvoldoende schijfruimte kan er voor zorgen dat de gastagent niet goed functioneert.

De gebruikersinterface van Microsoft Antimalware is standaard uitgeschakeld. Zie [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) (De Microsoft Antimalware-gebruikersinterface inschakelen op Azure Resource Manager-VM's na implementatie) voor meer informatie over hoe u deze kunt inschakelen.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Problemen oplossen met het laden van het dashboard

Als u problemen ondervindt met het laden van het dashboard van Security Center, moet u ervoor zorgen dat de gebruiker die het Security Center-abonnement registreert (de eerste gebruiker die Security Center voor het abonnement opent) en de gebruiker die het verzamelen van gegevens inschakelt, in het abonnement de rol van *Eigenaar* of *Inzender* hebben. Vanaf dat moment kunnen ook gebruikers die in het abonnement de rol van *Lezer* hebben, het dashboard weergeven, en waarschuwingen, aanbevelingen en het beleid bekijken.

## <a name="contacting-microsoft-support"></a>Contact opnemen met Microsoft-ondersteuning
Bepaalde problemen kunnen worden geïdentificeerd aan de hand van de richtlijnen in dit artikel. Andere problemen vindt u ook beschreven in het openbare [Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter) van Security Center. Als u aanvullende hulp nodig hebt om bepaalde problemen op te lossen, kunt u via **Azure Portal** een nieuwe ondersteuningsaanvraag openen. Dit doet u als volgt:

![Microsoft-ondersteuning](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Zie ook
In dit document hebt u kunnen lezen hoe u het beveiligingsbeleid configureert in Azure Security Center. Zie de volgende onderwerpen voor meer informatie over het Azure Beveiligingscentrum:

* [Azure Security Center Planning and Operations Guide](security-center-planning-and-operations-guide.md) (Gids voor de planning en werking van Azure Security Center): leer de ontwerpoverwegingen kennen en leer hiervoor te plannen voor de overstap naar Azure Security Center.
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) (Beveiligingsstatus controleren in Azure Security Center): meer informatie over het controleren van de status van uw Azure-resources
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md): leer hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren
* [Partneroplossingen controleren met Azure Security Center](security-center-partner-solutions.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt controleren.
* [Veelgestelde vragen over Azure Security Center](security-center-faq.md): raadpleeg veelgestelde vragen over het gebruik van de service
* [Azure-beveiligingsblog](https://blogs.msdn.com/b/azuresecurity/): lees blogberichten over de beveiliging en naleving van Azure
