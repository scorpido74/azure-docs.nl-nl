---
title: Azure DDoS Protection Standard beheren met de Azure Portal
titlesuffix: Azure Virtual Network
description: Meer informatie over het gebruik van Azure DDoS Protection standaard-telemetrie in Azure Monitor om een aanval te verhelpen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 706379649b47846b5c020dc76493a98e346c4a8f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317681"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Azure DDoS Protection Standard beheren met de Azure Portal

Meer informatie over het in-en uitschakelen van DDoS-beveiliging (Distributed Denial of service) en het gebruik van telemetrie om een DDoS-aanval met Azure DDoS Protection Standard te verhelpen. DDoS Protection Standard beveiligt Azure-resources, zoals virtuele machines, load balancers en toepassings gateways waaraan een [openbaar IP-adres](virtual-network-public-ip-address.md) van Azure is toegewezen. Zie [DDoS Protection Standard Overview](ddos-protection-overview.md)(Engelstalig) voor meer informatie over DDoS Protection Standard en de mogelijkheden ervan.

Voordat u de stappen in deze zelf studie voltooit, meldt u zich aan bij de Azure Portal https://portal.azure.com met een account dat is toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [machtigingen](#permissions-and-restrictions).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-ddos-protection-plan"></a>Een DDoS-beschermings plan maken

In een DDoS-beschermings plan wordt een set virtuele netwerken gedefinieerd waarvoor DDoS-beschermings standaard is ingeschakeld, via abonnementen. U kunt één DDoS-beveiligings plan configureren voor uw organisatie en virtuele netwerken vanuit meerdere abonnementen koppelen aan hetzelfde abonnement. Het DDoS Protection Plan zelf is ook gekoppeld aan een abonnement dat u selecteert tijdens het maken van het plan. Het DDoS Protection Plan werkt in verschillende regio's en abonnementen. Voor beeld: u kunt het plan in regio Oost-US maken en koppelen aan abonnement #1 in uw Tenant. Hetzelfde abonnement kan worden gekoppeld aan virtuele netwerken vanuit andere abonnementen in verschillende regio's, binnen uw Tenant. Het abonnement waaraan het plan is gekoppeld, maakt de maandelijks terugkerende factuur voor het plan en overschrijding kosten voor het geval het aantal beveiligde open bare IP-adressen groter is dan 100. Zie [prijs informatie](https://azure.microsoft.com/pricing/details/ddos-protection/)voor meer informatie over DDoS prijzen.

Het is niet nodig om meer dan één abonnement te maken voor de meeste organisaties. Een plan kan niet worden verplaatst tussen abonnementen. Als u het abonnement wilt wijzigen, moet u [het bestaande schema verwijderen](#work-with-ddos-protection-plans) en een nieuw plan maken.

1. Selecteer in de linkerbovenhoek van het Azure Portal **een resource maken** .
2. Zoek naar *DDoS*. Wanneer **DDoS-beschermings plan** wordt weer gegeven in de zoek resultaten, selecteert u dit.
3. Selecteer **Maken**.
4. Typ of Selecteer uw eigen waarden, of voer de volgende voorbeeld waarden in, en selecteer vervolgens **maken**:

    |Instelling        |Waarde                                              |
    |---------      |---------                                          |
    |Naam           | myDdosProtectionPlan                              |
    |Abonnement   | Selecteer uw abonnement.                         |
    |Resourcegroep | Selecteer **nieuwe maken** en voer *myResourceGroup* in |
    |Locatie       | VS - oost                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>DDoS inschakelen voor een nieuw virtueel netwerk

1. Selecteer in de linkerbovenhoek van het Azure Portal **een resource maken** .
2. Selecteer **Netwerken** en selecteer vervolgens **Virtueel netwerk**.
3. Typ of Selecteer uw eigen waarden, typ of selecteer de volgende voorbeeld waarden, accepteer de resterende standaard instellingen en selecteer vervolgens **maken**:

    | Instelling         | Waarde                                                        |
    | ---------       | ---------                                                    |
    | Naam            | myVirtualNetwork                                             |
    | Abonnement    | Selecteer uw abonnement.                                    |
    | Resourcegroep  | Selecteer **Bestaande gebruiken** en vervolgens **myResourceGroup** |
    | Locatie        | VS - oost                                                      |
    | DDoS Protection standaard | Selecteer **Inschakelen**. Het plan dat u selecteert, kan zich in hetzelfde of een ander abonnement bevindt dan het virtuele netwerk, maar beide abonnementen moeten aan dezelfde Azure Active Directory-Tenant zijn gekoppeld.|

U kunt een virtueel netwerk niet verplaatsen naar een andere resource groep of een ander abonnement wanneer DDoS standaard is ingeschakeld voor het virtuele netwerk. Als u een virtueel netwerk wilt verplaatsen waarbij DDoS Standard is ingeschakeld, moet u eerst DDoS-standaard uitschakelen, het virtuele netwerk verplaatsen en vervolgens DDoS Standard inschakelen. Na de verplaatsing worden de automatisch afgestemde beleids drempels voor alle beveiligde open bare IP-adressen in het virtuele netwerk opnieuw ingesteld.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>DDoS inschakelen voor een bestaand virtueel netwerk

1. Maak een DDoS-beschermings plan door de stappen in [een DDoS-beveiligings plan maken](#create-a-ddos-protection-plan)uit te voeren, als u geen bestaand DDoS-beveiligings abonnement hebt.
2. Selecteer in de linkerbovenhoek van het Azure Portal **een resource maken** .
3. Voer de naam in van het virtuele netwerk waarvoor u DDoS Protection standaard wilt inschakelen in het **vak resources, services en documenten zoeken** bovenaan de portal. Wanneer de naam van het virtuele netwerk wordt weer gegeven in de zoek resultaten, selecteert u dit.
4. Selecteer **DDoS Protection**onder **instellingen**.
5. selecteer **Standaard**. Selecteer onder **DDoS-beschermings plan**een bestaand DDoS-beveiligings plan of het plan dat u hebt gemaakt in stap 1 en selecteer vervolgens **Opslaan**. Het plan dat u selecteert, kan zich in hetzelfde of een ander abonnement bevindt dan het virtuele netwerk, maar beide abonnementen moeten aan dezelfde Azure Active Directory-Tenant zijn gekoppeld.

**Opdrachten** 
- Azure CLI: [AZ Network DDoS-Protection Create](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- Power shell: [New-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>DDoS uitschakelen voor een virtueel netwerk

1. Voer de naam in van het virtuele netwerk waarvoor u de DDoS-beveiligings standaard wilt uitschakelen in het **vak resources, services en documenten zoeken** bovenaan de portal. Wanneer de naam van het virtuele netwerk wordt weer gegeven in de zoek resultaten, selecteert u dit.
2. Selecteer **onder DDoS Protection standaard**de optie **uitschakelen**.

**Opdrachten** 
- Azure CLI: [AZ Network DDoS-Protection delete](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- Power shell: [Remove-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>Werken met DDoS-beschermings plannen

1. Selecteer **alle services** bovenaan, links van de portal.
2. Geef *DDoS* op in het vak **filter** . Wanneer **DDoS-beveiligings plannen** worden weer gegeven in de resultaten, selecteert u deze.
3. Selecteer het beveiligings plan dat u wilt weer geven in de lijst.
4. Alle virtuele netwerken die aan het plan zijn gekoppeld, worden weer gegeven.
5. Als u een plan wilt verwijderen, moet u eerst alle virtuele netwerken loskoppelen. Zie [DDoS uitschakelen voor een virtueel netwerk](#disable-ddos-for-a-virtual-network)als u een plan wilt loskoppelen van een virtueel netwerk.

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Waarschuwingen voor DDoS Protection-metrische gegevens configureren

U kunt een van de beschik bare metrische gegevens voor DDoS-beveiliging selecteren om u te waarschuwen wanneer er een actieve beperking is tijdens een aanval, met behulp van de configuratie van de Azure Monitor waarschuwingen. Wanneer aan de voor waarden wordt voldaan, ontvangt het opgegeven adres een e-mail waarschuwing:

1. Selecteer **alle services** bovenaan, links van de portal.
2. Voer *monitor* in het vak **filter** in. Wanneer de **monitor** wordt weer gegeven in de resultaten, selecteert u deze.
3. Selecteer **metrische gegevens** onder **gedeelde services**.
4. Voer uw eigen waarden in of Selecteer deze, of voer de volgende voorbeeld waarden in, accepteer de resterende standaard instellingen en selecteer **OK**:

    |Instelling                  |Waarde                                                                                               |
    |---------                |---------                                                                                           |
    |Naam                     | myDdosAlert                                                                                        |
    |Abonnement             | Selecteer het abonnement dat het open bare IP-adres bevat waarvoor u waarschuwingen wilt ontvangen.        |
    |Resourcegroep           | Selecteer de resource groep die het open bare IP-adres bevat waarvoor u waarschuwingen wilt ontvangen.      |
    |Resource                 | Selecteer het open bare IP-adres met het open bare IP-adres waarvoor u waarschuwingen wilt ontvangen. DDoS bewaakt open bare IP-adressen die zijn toegewezen aan bronnen in een virtueel netwerk. Als u geen resources met open bare IP-adressen in het virtuele netwerk hebt, moet u eerst een resource met een openbaar IP-adres maken. U kunt het open bare IP-adres van alle resources die zijn geïmplementeerd via Resource Manager (niet klassiek) bewaken in het [virtuele netwerk voor Azure-Services](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), met uitzonde ring van Azure app service omgevingen en Azure VPN gateway. Als u wilt door gaan met deze zelf studie, kunt u snel een virtuele [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machine maken.                   |
    |Gegevens                   | Onder DDoS-aanval of niet                                                                            |
    |Drempelwaarde                | 1- **1** betekent dat u een aanval ondervindt. **0** betekent dat u geen aanval ondervindt.                         |
    |Periode                   | Selecteer de waarde die u kiest.                                                                   |
    |Melden via E-mail         | Schakel het selectievakje in                                                                                  |
    |Extra beheerder | Voer uw e-mail adres in als u geen e-mail eigenaar, bijdrager of lezer bent voor het abonnement. |

    Binnen een paar minuten van aanvals detectie ontvangt u een e-mail bericht van Azure Monitor metrische gegevens die er ongeveer als volgt uitzien:

    ![Waarschuwing voor aanvallen](./media/manage-ddos-protection/ddos-alert.png)


Zie [DDoS Detection valideren](#validate-ddos-detection)als u een DDoS-aanval wilt simuleren om uw waarschuwing te valideren.

U kunt ook meer te weten komen over het [configureren van webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en [Logic apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor het maken van waarschuwingen.

## <a name="use-ddos-protection-telemetry"></a>Telemetrie van DDoS-beveiliging gebruiken

Telemetrie voor een aanval wordt via Azure Monitor in realtime verschaft. De telemetrie is alleen beschikbaar voor de duur dat een openbaar IP-adres wordt beperkt. U ziet geen telemetrie voor of nadat een aanval is verholpen.

1. Selecteer **alle services** bovenaan, links van de portal.
2. Voer *monitor* in het vak **filter** in. Wanneer de **monitor** wordt weer gegeven in de resultaten, selecteert u deze.
3. Selecteer **metrische gegevens**onder **gedeelde services**.
4. Selecteer het **abonnement** en de **resource groep** die het open bare IP-adres bevatten waarvoor u telemetrie wilt.
5. Selecteer **openbaar IP-adres** voor het **bron type**en selecteer vervolgens het specifieke open bare IP-adres waarvoor u telemetrie wilt.
6. Er wordt een reeks **beschik bare metrische gegevens** weer gegeven aan de linkerkant van het scherm. Deze metrische gegevens, indien geselecteerd, worden weer gegeven in de **grafiek Azure monitor metrische gegevens** op het scherm overzicht.
7. Selecteer het **aggregatie** type ten opzichte van **Max**

De metrische namen hebben verschillende pakket typen en bytes versus pakketten, met een basis constructie van label namen op elke metriek als volgt:

- **Verwijderde code naam** (bijvoorbeeld **inkomende pakketten DDoS**): het aantal pakketten dat is verwijderd door het DDoS-beveiligings systeem.
- **Naam van doorgestuurde code** (bijvoorbeeld door **sturen van binnenkomende pakketten DDoS**): het aantal pakketten dat door het DDoS-systeem is doorgestuurd naar het VIP-doel verkeer dat niet is gefilterd.
- **Er is geen label naam** (bijvoorbeeld **DDoS**): het totale aantal pakketten dat is geleverd aan het reinigings systeem, waarmee de som van de pakketten die worden verwijderd en doorgestuurd, wordt weer geven.

Zie [DDoS Detection valideren](#validate-ddos-detection)als u een DDoS-aanval wilt simuleren om telemetrie te valideren.

## <a name="view-ddos-mitigation-policies"></a>DDoS-beperkings beleid weer geven

DDoS Protection Standard heeft drie automatisch afgestemde beperkende beleids regels (TCP SYN, TCP & UDP) voor elk openbaar IP-adres van de beveiligde bron, in het virtuele netwerk waarop DDoS is ingeschakeld. U kunt de drempel waarden voor het beleid weer geven door de  **binnenkomende TCP-pakketten te selecteren voor het activeren van DDoS-beperking** en **binnenkomende UDP-pakketten om DDoS-beperkende metrische gegevens te activeren** met het **aggregatie** type ' Max ', zoals wordt weer gegeven in de volgende afbeelding:

![Risico beperkings beleid weer geven](./media/manage-ddos-protection/view-mitigation-policies.png)

Beleids drempels worden automatisch geconfigureerd via het samen stellen van het netwerk verkeer op basis van Azure machine learning. Alleen als de drempel waarde voor het beleid wordt geschonden, treedt er een DDoS-beperking op voor het IP-adres onder aanval.

## <a name="configure-ddos-attack-analytics"></a>Analyse voor DDoS-aanval configureren
Azure DDoS Protection Standard biedt uitgebreide aanval en visualisatie met DDoS-aanvals analyses. Klanten die hun virtuele netwerken beschermen tegen DDoS-aanvallen, hebben een gedetailleerde zicht baarheid van het aanvals verkeer en acties die zijn ondernomen om de aanval via rapporten voor aanvallen te beperken & beperkende stroom Logboeken. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Rapporten over het beperken van DDoS-aanvallen configureren
Bij het beperken van aanvallen wordt gebruikgemaakt van de netstroom protocol gegevens die worden geaggregeerd voor gedetailleerde informatie over de aanval van uw bron. Telkens wanneer een open bare IP-resource wordt aangevallen, wordt het rapport gegenereerd zodra de oplossing wordt gestart. Er wordt een incrementeel rapport gegenereerd om de 5 minuten en een rapport na de risico beperking voor de hele afkortings periode. Dit is om ervoor te zorgen dat de DDoS-aanval gedurende een langere periode langer duurt, dan kunt u de meest actuele moment opname van het risico rapport elke vijf minuten bekijken en een volledige samen vatting zodra de risico beperking is overschreden. 

1. Selecteer **alle services** bovenaan, links van de portal.
2. Voer *monitor* in het vak **filter** in. Wanneer de **monitor** wordt weer gegeven in de resultaten, selecteert u deze.
3. Selecteer bij **instellingen**de optie **Diagnostische instellingen**.
4. Selecteer het **abonnement** en de **resource groep** die het open bare IP-adres bevatten dat u wilt registreren.
5. Selecteer **openbaar IP-adres** voor het **bron type**en selecteer vervolgens het specifieke open bare IP-adres waarvoor u metrische gegevens wilt vastleggen.
6. Selecteer **Diagnostische gegevens inschakelen om het DDoSMitigationReports-logboek te verzamelen** en selecteer vervolgens zoveel van de volgende opties als u nodig hebt:

    - **Archiveren naar een opslag account**: gegevens worden naar een Azure Storage-account geschreven. Zie [Archief bron logboeken](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over deze optie.
    - **Streamen naar een event hub**: Hiermee kan een logboek ontvanger logboeken ophalen met behulp van een Azure Event hub. Event hubs maken integratie mogelijk met Splunk of andere SIEM-systemen. Zie [bron logboeken streamen naar een event hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over deze optie.
    - **Verzenden naar log Analytics**: schrijft logboeken naar de Azure Monitor-service. Zie [Logboeken verzamelen voor gebruik in azure monitor-logboeken voor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)meer informatie over deze optie.

De volgende velden zijn zowel het incrementele & na het oplossen van problemen met beperkende rapporten
- Aanvals vectoren
- Verkeers statistieken
- Reden voor verwijderde pakketten
- Betrokken protocollen
- Top 10 van de bron landen of regio's
- Top 10 van de bron Asn's

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>DDoS-aanvals stroom logboeken configureren
Met de stroom logboeken voor risico beperking kunt u het verloren verkeer, het doorgestuurde verkeer en andere interessante data Points tijdens een actieve DDoS-aanval in vrijwel real time bekijken. U kunt de constante stroom van deze gegevens opnemen in uw SIEM-systemen via Event Hub voor de nabije realtime-bewaking, mogelijke acties ondernemen en de nood zaak van uw verdedigings activiteiten aanpakken. 

1. Selecteer **alle services** bovenaan, links van de portal.
2. Voer *monitor* in het vak **filter** in. Wanneer de **monitor** wordt weer gegeven in de resultaten, selecteert u deze.
3. Selecteer bij **instellingen**de optie **Diagnostische instellingen**.
4. Selecteer het **abonnement** en de **resource groep** die het open bare IP-adres bevatten dat u wilt registreren.
5. Selecteer **openbaar IP-adres** voor het **bron type**en selecteer vervolgens het specifieke open bare IP-adres waarvoor u metrische gegevens wilt vastleggen.
6. Selecteer **Diagnostische gegevens inschakelen om het DDoSMitigationFlowLogs-logboek te verzamelen** en selecteer vervolgens zoveel van de volgende opties als u nodig hebt:

    - **Archiveren naar een opslag account**: gegevens worden naar een Azure Storage-account geschreven. Zie [Archief bron logboeken](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over deze optie.
    - **Streamen naar een event hub**: Hiermee kan een logboek ontvanger logboeken ophalen met behulp van een Azure Event hub. Event hubs maken integratie mogelijk met Splunk of andere SIEM-systemen. Zie [bron logboeken streamen naar een event hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over deze optie.
    - **Verzenden naar log Analytics**: schrijft logboeken naar de Azure Monitor-service. Zie [Logboeken verzamelen voor gebruik in azure monitor-logboeken voor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)meer informatie over deze optie.
1. Als u de gegevens van stroom logboeken wilt weer geven in de Azure Analytics-werkmap, kunt u het voorbeeld dashboard importeren uit https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook

Stroom logboeken bevatten de volgende velden: 
- Bron-IP
- Doel-IP
- Bronpoort 
- Doelpoort 
- Protocol type 
- Actie ondernomen tijdens risico beperking

Aanvals analyses werken alleen als DDoS Protection standaard is ingeschakeld op het virtuele netwerk van het open bare IP-adres. 

## <a name="validate-ddos-detection"></a>DDoS-detectie valideren

Micro soft heeft een samen werking met [BreakingPoint-Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) voor het bouwen van een interface waarmee u verkeer kunt genereren op basis van DDoS Protection open bare IP-adressen voor simulaties. Met de Cloud simulatie onderbrekings punt kunt u:

- Controleren hoe Microsoft Azure DDoS Protection uw Azure-resources beveiligt tegen DDoS-aanvallen
- Optimaliseer uw respons proces van uw incidenten door onder DDoS-aanval
- Conformiteit document DDoS
- Uw netwerk beveiligings teams trainen

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Waarschuwingen voor DDoS-beveiliging in Azure Security Center weer geven

Azure Security Center bevat een lijst met [beveiligings waarschuwingen](/azure/security-center/security-center-managing-and-responding-alerts)met informatie over het onderzoeken en oplossen van problemen. Met deze functie krijgt u een uniforme weer gave van waarschuwingen, waaronder waarschuwingen met betrekking tot DDoS-aanvallen en de acties die zijn ondernomen om de aanval in een nabije periode te verminderen.
Er zijn twee specifieke waarschuwingen die u zult zien voor een detectie en beperking van de DDoS-aanval:

- **DDoS-aanval gedetecteerd voor openbaar IP-adres**: deze waarschuwing wordt gegenereerd wanneer de DDoS Protection-service detecteert dat een van uw open bare IP-adressen het doel is van een DDoS-aanval.
- **DDoS-aanval beperkt voor openbaar IP-** adres: deze waarschuwing wordt gegenereerd wanneer een aanval op het open bare IP-adressen is verholpen.
Als u de waarschuwingen wilt weer geven, opent u **Security Center** in de Azure Portal. Selecteer onder **bedreigingen beveiliging**de optie **beveiligings waarschuwingen**. De volgende scherm afbeelding toont een voor beeld van de waarschuwingen voor DDoS-aanvallen.

![DDoS-waarschuwing in Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

De waarschuwingen bevatten algemene informatie over het open bare IP-adres dat wordt vermeld onder aanvallen, geo-en bedreigings informatie en herstel stappen.

## <a name="permissions-and-restrictions"></a>Machtigingen en beperkingen

Als u wilt werken met DDoS-beveiligings plannen, moet uw account worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rol waaraan de juiste acties in de volgende tabel zijn toegewezen:

| Actie                                            | Naam                                     |
| ---------                                         | -------------                            |
| Micro soft. Network/ddosProtectionPlans/lezen        | Een DDoS-beschermings plan lezen              |
| Micro soft. Network/ddosProtectionPlans/schrijven       | Een DDoS-beschermings plan maken of bijwerken  |
| Micro soft. Network/ddosProtectionPlans/verwijderen      | Een DDoS-beschermings plan verwijderen            |
| Micro soft. Network/ddosProtectionPlans/samen voegen/actie | Lid worden van een DDoS-beschermings plan              |

Als u DDoS-beveiliging wilt inschakelen voor een virtueel netwerk, moet aan uw account ook de juiste acties worden toegewezen [voor virtuele netwerken](manage-virtual-network.md#permissions).

### <a name="azure-policy"></a>Azure Policy
Voor klanten die verschillende abonnementen hebben en die ervoor willen zorgen dat één plan voor Azure DDoS Protection Standard wordt geïmplementeerd in hun Tenant voor kosten beheer, kunt u Azure Policy gebruiken om het [maken van Azure DDoS Protection standaard plannen te beperken](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Restrict%20creation%20of%20Azure%20DDoS%20Protection%20Standard%20Plans%20with%20Azure%20Policy). Met dit beleid wordt het maken van DDoS-abonnementen geblokkeerd, tenzij het abonnement eerder als een uitzonde ring is gemarkeerd. In dit beleid wordt ook een lijst weer gegeven met alle abonnementen waarvoor een DDoS-abonnement is geïmplementeerd, maar die niet moeten worden gemarkeerd als niet-compatibel. 

## <a name="next-steps"></a>Volgende stappen

- [Azure Policy definities ophalen](policy-samples.md) maken en toewijzen voor virtuele netwerken
