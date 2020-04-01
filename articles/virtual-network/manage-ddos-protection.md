---
title: Azure DDoS-beveiligingsstandaard beheren met de Azure-portal
titlesuffix: Azure Virtual Network
description: Meer informatie over het gebruik van Azure DDoS Protection Standard telemetrie in Azure Monitor om een aanval te beperken.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 95a70a1d0c4367adb1dd276bff1b1eb20caafc59
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473374"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Azure DDoS-beveiligingsstandaard beheren met de Azure-portal

Meer informatie over het in- en uitschakelen van DDoS-beveiliging (Distributed Denial of Service) en het gebruik van telemetrie om een DDoS-aanval te beperken met Azure DDoS Protection Standard. DDoS Protection Standard beschermt Azure-bronnen zoals virtuele machines, load balancers en toepassingsgateways waarop een Openbaar [IP-adres van](virtual-network-public-ip-address.md) Azure is toegewezen. Zie [DDoS Protection Standard overzicht](ddos-protection-overview.md)voor meer informatie over DDoS Protection Standard en de mogelijkheden.

Voordat u stappen in deze zelfstudie voltooit, https://portal.azure.com meldt u zich aan bij de Azure-portal met een account dat is toegewezen aan de [rol van netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of bij een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die in [Machtigingen](#permissions)worden vermeld.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-a-ddos-protection-plan"></a>Een DDoS-beveiligingsplan maken

Een DDoS-beveiligingsplan definieert een set virtuele netwerken die ddos-beveiligingsstandaard hebben ingeschakeld, voor verschillende abonnementen. U één DDoS-beveiligingsplan voor uw organisatie configureren en virtuele netwerken van meerdere abonnementen koppelen aan hetzelfde abonnement. Het DDoS Protection Plan zelf is ook gekoppeld aan een abonnement dat u selecteert tijdens het maken van het abonnement. Het DDoS Protection Plan werkt in verschillende regio's en abonnementen. Voorbeeld -u het abonnement maken in Regio Oost-VS en een koppeling maken naar abonnement #1 in uw tenant. Hetzelfde abonnement kan worden gekoppeld aan virtuele netwerken van andere abonnementen in verschillende regio's, in uw tenant. Het abonnement dat het abonnement is gekoppeld aan de maandelijksterugkerende factuur voor het abonnement, evenals overschrijdingskosten, in het geval het aantal beveiligde openbare IP-adressen hoger is dan 100. Zie [prijsdetails voor](https://azure.microsoft.com/pricing/details/ddos-protection/)meer informatie over DDoS-prijzen.

Het maken van meer dan één plan is niet vereist voor de meeste organisaties. Een abonnement kan niet worden verplaatst tussen abonnementen. Als u het abonnement waarin een abonnement is gewijzigd, moet u [het bestaande abonnement verwijderen](#work-with-ddos-protection-plans) en een nieuw abonnement maken.

1. Selecteer **Een resource maken** in de linkerbovenhoek van de Azure-portal.
2. Zoeken naar *DDoS*. Wanneer **ddos-beveiligingsplan** wordt weergegeven in de zoekresultaten, selecteert u het.
3. Selecteer **Maken**.
4. Voer uw eigen waarden in of selecteer deze in of selecteer de volgende voorbeeldwaarden en selecteer **Vervolgens Maken:**

    |Instelling        |Waarde                                              |
    |---------      |---------                                          |
    |Name           | myDdosProtectionPlan                              |
    |Abonnement   | Selecteer uw abonnement.                         |
    |Resourcegroep | Selecteer **Nieuw maken** en *mijnResourcegroep* invoeren |
    |Locatie       | VS - oost                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>DDoS inschakelen voor een nieuw virtueel netwerk

1. Selecteer **Een resource maken** in de linkerbovenhoek van de Azure-portal.
2. Selecteer **Netwerken** en selecteer vervolgens **Virtueel netwerk**.
3. Voer uw eigen waarden in of selecteer de volgende voorbeeldwaarden in of selecteer deze, accepteer de resterende standaardwaarden en selecteer **Vervolgens Maken:**

    | Instelling         | Waarde                                                        |
    | ---------       | ---------                                                    |
    | Name            | myVirtualNetwork                                             |
    | Abonnement    | Selecteer uw abonnement.                                    |
    | Resourcegroep  | Selecteer **Bestaand gebruiken**en selecteer vervolgens **myResourceGroup** |
    | Locatie        | VS - oost                                                      |
    | DDoS-bescherming | Selecteer **Standaard** en selecteer vervolgens onder **DDoS-beveiliging** **myDdosProtectionPlan**. Het abonnement dat u selecteert, kan zich in hetzelfde of een ander abonnement bevinden dan het virtuele netwerk, maar beide abonnementen moeten worden gekoppeld aan dezelfde Azure Active Directory-tenant.|

U een virtueel netwerk niet verplaatsen naar een andere brongroep of -abonnement wanneer DDoS-standaard is ingeschakeld voor het virtuele netwerk. Als u een virtueel netwerk moet verplaatsen met DDoS Standard ingeschakeld, schakelt u DDoS-standaard eerst uit, verplaatst u het virtuele netwerk en schakelt u vervolgens ddos-standaard in. Na de verhuizing worden de automatisch afgestemde beleidsdrempels voor alle beveiligde openbare IP-adressen in het virtuele netwerk opnieuw ingesteld.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>DDoS inschakelen voor een bestaand virtueel netwerk

1. Maak een DDoS-beveiligingsplan door de stappen in [Een DDoS-beveiligingsplan maken](#create-a-ddos-protection-plan)uit te voeren als u geen bestaand DDoS-beveiligingsplan hebt.
2. Selecteer **Een resource maken** in de linkerbovenhoek van de Azure-portal.
3. Voer de naam in van het virtuele netwerk waarvoor u DDoS Protection Standard wilt inschakelen in het **vak Zoekbronnen, services en documenten** boven aan de portal. Wanneer de naam van het virtuele netwerk wordt weergegeven in de zoekresultaten, selecteert u deze.
4. Selecteer **DDoS-beveiliging**onder **INSTELLINGEN**.
5. Selecteer **Standaard**. Selecteer onder **DDoS-beveiligingsplan**een bestaand DDoS-beveiligingsplan of het plan dat u in stap 1 hebt gemaakt en selecteer **Opslaan**. Het abonnement dat u selecteert, kan zich in hetzelfde of een ander abonnement bevinden dan het virtuele netwerk, maar beide abonnementen moeten worden gekoppeld aan dezelfde Azure Active Directory-tenant.

**Opdrachten** 
- Azure CLI: [ddos-beveiliging az-netwerk maken](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- Powershell: [Nieuw-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>DDoS uitschakelen voor een virtueel netwerk

1. Voer de naam in van het virtuele netwerk waarvoor u de DDoS-beveiligingsstandaard wilt uitschakelen in het **vak Bronnen, services en documenten zoeken** boven aan de portal. Wanneer de naam van het virtuele netwerk wordt weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer **DDoS-beveiliging**onder **INSTELLINGEN**.
3. Selecteer **Basic** onder **DDoS-beveiligingsplan** en selecteer **Opslaan**.

**Opdrachten** 
- Azure CLI: [ddos-beveiliging az-netwerk verwijderen](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- Powershell: [Remove-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>Werken met DDoS-beveiligingsplannen

1. Selecteer **Alle services** boven, links van de portal.
2. Voer *DDoS* in het vak **Filter** in. Wanneer **DDoS-beveiligingsplannen** in de resultaten worden weergegeven, selecteert u deze.
3. Selecteer het beveiligingsplan dat u wilt weergeven in de lijst.
4. Alle virtuele netwerken die aan het plan zijn gekoppeld, worden weergegeven.
5. Als u een abonnement wilt verwijderen, moet u eerst alle virtuele netwerken ervan scheiden. Zie [DDoS uitschakelen voor een virtueel netwerk](#disable-ddos-for-a-virtual-network)als u een plan wilt scheiden van een virtueel netwerk.

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Waarschuwingen configureren voor DDoS-beveiligingsstatistieken

U een van de beschikbare DDoS-beveiligingsstatistieken selecteren om u te waarschuwen wanneer er een actieve beperking is tijdens een aanval, met behulp van de azure monitor-waarschuwingsconfiguratie. Wanneer aan de voorwaarden is voldaan, ontvangt het opgegeven adres een waarschuwingse-mail:

1. Selecteer **Alle services** boven, links van de portal.
2. Voer *Monitor* in het vak **Filter** in. Wanneer **Monitor** in de resultaten wordt weergegeven, selecteert u deze.
3. Selecteer **Statistieken** onder **GEDEELDE SERVICES**.
4. Voer uw eigen waarden in of selecteer deze, of voer de volgende voorbeeldwaarden in, accepteer de resterende standaardwaarden en selecteer **OK:**

    |Instelling                  |Waarde                                                                                               |
    |---------                |---------                                                                                           |
    |Name                     | myDdosAlert                                                                                        |
    |Abonnement             | Selecteer het abonnement met het openbare IP-adres waarvoor u waarschuwingen wilt ontvangen.        |
    |Resourcegroep           | Selecteer de brongroep die het openbare IP-adres bevat waarvoor u waarschuwingen wilt ontvangen.      |
    |Resource                 | Selecteer het openbare IP-adres met het openbare IP-adres waarvoor u waarschuwingen wilt ontvangen. DDoS controleert openbare IP-adressen die zijn toegewezen aan bronnen binnen een virtueel netwerk. Als u geen resources hebt met openbare IP-adressen in het virtuele netwerk, moet u eerst een bron maken met een openbaar IP-adres. U het openbare IP-adres controleren van alle resources die zijn geïmplementeerd via Resource Manager (niet klassiek) dat wordt vermeld in [virtueel netwerk voor Azure-services,](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)met uitzondering van Azure App Service-omgevingen en Azure VPN Gateway. Om verder te gaan met deze tutorial, u snel een [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuele machine maken.                   |
    |Gegevens                   | Onder DDoS-aanval of niet                                                                            |
    |Drempelwaarde                | 1 - **1** betekent dat je wordt aangevallen. **0** betekent dat u niet wordt aangevallen.                         |
    |Periode                   | Selecteer welke waarde u kiest.                                                                   |
    |Melden via e-mail         | Schakel het selectievakje in                                                                                  |
    |Extra beheerder | Voer uw e-mailadres in als u geen e-maileigenaar, bijdrager of lezer voor het abonnement bent. |

    Binnen een paar minuten na de detectie van aanvallen ontvangt u een e-mail van Azure Monitor-statistieken die lijkt op de volgende afbeelding:

    ![Aanvalswaarschuwing](./media/manage-ddos-protection/ddos-alert.png)


Zie [DDoS-detectie valideren](#validate-ddos-detection)als u een DDoS-aanval wilt simuleren om uw waarschuwing te valideren.

U ook meer informatie krijgen over [het configureren van webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en [logische apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor het maken van waarschuwingen.

## <a name="use-ddos-protection-telemetry"></a>DDoS-beveiliging telemetrie gebruiken

Telemetrie voor een aanval wordt in realtime geleverd via Azure Monitor. De telemetrie is alleen beschikbaar voor de duur dat een openbaar IP-adres wordt beperkt. U ziet geen telemetrie voor of na een aanval wordt beperkt.

1. Selecteer **Alle services** boven, links van de portal.
2. Voer *Monitor* in het vak **Filter** in. Wanneer **Monitor** in de resultaten wordt weergegeven, selecteert u deze.
3. Selecteer **Statistieken**onder **GEDEELDE SERVICES**.
4. Selecteer de **groep** **Abonnement** en Resource die het openbare IP-adres bevatten waarvoor u telemetrie wilt.
5. Selecteer **Openbaar IP-adres** voor **resourcetype**en selecteer vervolgens het specifieke openbare IP-adres waarvoor u telemetrie wilt.
6. Aan de **linkerkant** van het scherm wordt een reeks beschikbare statistieken weergegeven. Deze statistieken worden, wanneer ze zijn geselecteerd, weergegeven in de **Grafiek Azure Monitor Metrics** op het overzichtsscherm.
7. Selecteer het **aggregatietype** als **Max**

De metrische namen presenteren verschillende pakkettypen en bytes versus pakketten, met een basisconstructie van tagnamen op elke statistiek als volgt:

- **De tagnaam** is verwijderd (bijvoorbeeld **Inbound Packets Dropped DDoS):** het aantal pakketten dat is gedropt/geschrobd door het DDoS-beveiligingssysteem.
- **Doorgestuurde tagnaam** (bijvoorbeeld **Inbound Packets Forwarded DDoS):** Het aantal pakketten dat door het DDoS-systeem wordt doorgestuurd naar de vip-bestemming – verkeer dat niet is gefilterd.
- **Geen tagnaam** (bijvoorbeeld **Inbound Packets DDoS):** Het totale aantal pakketten dat in het schrobsysteem is gekomen , wat de som van de pakketten vertegenwoordigt die zijn gedropt en doorgestuurd.

Zie [DDoS-detectie valideren](#validate-ddos-detection)als u een DDoS-aanval wilt simuleren om telemetrie te valideren.

## <a name="view-ddos-mitigation-policies"></a>DDoS-mitigatiebeleid weergeven

DDoS Protection Standard past drie automatisch afgestemdmitigatiebeleid (TCP SYN, TCP & UDP) toe voor elk openbaar IP-adres van de beveiligde bron, in het virtuele netwerk waarvoor DDoS is ingeschakeld. U de beleidsdrempels bekijken door de **binnenkomende TCP-pakketten te selecteren om DDoS-mitigatie-** en **Inbound UDP-pakketten te activeren om DDoS-mitigatiestatistieken** met **aggregatietype** als 'Max' te activeren, zoals in de volgende afbeelding wordt weergegeven:

![Mitigatiebeleid weergeven](./media/manage-ddos-protection/view-mitigation-policies.png)

Beleidsdrempels worden automatisch geconfigureerd via Azure machine learning-gebaseerde netwerkverkeerprofilering. Pas wanneer de beleidsdrempel wordt overschreden, treedt DDoS-mitigatie op voor het IP-adres dat wordt aangevallen.

## <a name="configure-ddos-attack-analytics"></a>Analyse voor DDoS-aanval configureren
Azure DDoS Protection-standaard biedt gedetailleerde aanvalsinzichten en visualisatie met DDoS Attack Analytics. Klanten die hun virtuele netwerken beschermen tegen DDoS-aanvallen hebben gedetailleerd inzicht in aanvalsverkeer en acties die zijn ondernomen om de aanval te beperken via aanvalsmitigatierapporten & mitigatiestroomlogboeken. 

## <a name="configure-ddos-attack-mitigation-reports"></a>DDoS-aanvalsbeperkende rapporten configureren
Aanvalsbeperkende rapporten maakt gebruik van de Netflow-protocolgegevens die worden samengevoegd om gedetailleerde informatie te geven over de aanval op uw resource. Op elk moment dat een openbare IP-bron wordt aangevallen, zal de rapportgeneratie beginnen zodra de beperking begint. Er zal een incrementeel rapport worden gegenereerd om de 5 minuten en een post-mitigatie rapport voor de hele mitigatie periode. Dit is om ervoor te zorgen dat in een geval dat de DDoS-aanval voor een langere tijd doorgaat, u in staat zult zijn om de meest actuele momentopname van mitigatierapport elke 5 minuten en een volledige samenvatting te bekijken zodra de beperking van de aanval voorbij is. 

1. Selecteer **Alle services** boven, links van de portal.
2. Voer *Monitor* in het vak **Filter** in. Wanneer **Monitor** in de resultaten wordt weergegeven, selecteert u deze.
3. Selecteer **onder INSTELLINGEN**de optie Diagnostische **instellingen**.
4. Selecteer de **groep** **Abonnement** en Resource die het openbare IP-adres bevatten dat u wilt registreren.
5. Selecteer **Openbaar IP-adres** voor **resourcetype**en selecteer vervolgens het specifieke openbare IP-adres waarvoor u metrische gegevens wilt registreren.
6. Selecteer **Diagnostische gegevens inschakelen om het logboek DDoSMitigationReports te verzamelen** en selecteer vervolgens zoveel van de volgende opties als u nodig hebt:

    - **Archiveren naar een opslagaccount:** gegevens worden naar een Azure Storage-account geschreven. Zie [Diagnostische logboeken archiveren](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over deze optie.
    - **Streamen naar een gebeurtenishub:** hiermee kan een logboekontvanger logboeken ophalen met behulp van een Azure Event Hub. Event hubs maken integratie met Splunk of andere SIEM-systemen mogelijk. Zie [Diagnostische logboeken streamen naar een gebeurtenishub voor](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)meer informatie over deze optie.
    - **Verzenden naar Logboekanalyse:** schrijft logboeken naar de Azure Monitor-service. Zie [Logboeken verzamelen voor gebruik in Azure Monitor-logboeken voor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)meer informatie over deze optie.

Zowel de incrementele & rapporten voor het beperken na de aanval bevatten de volgende velden
- Aanvalsvectoren
- Verkeersstatistieken
- Reden voor gedropte pakketten
- Betrokken protocollen
- Top 10 bronlanden of regio's
- Top 10 bron-ASN's

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>DDoS-aanvalsstroomlogboeken configureren
Met Attack Mitigation Flow Logs u het gedropte verkeer, doorgestuurd verkeer en andere interessante datapunten bekijken tijdens een actieve DDoS-aanval in bijna realtime. U de constante stroom van deze gegevens in uw SIEM-systemen opnemen via event hub voor near-real-time monitoring, mogelijke acties ondernemen en de behoefte van uw defensie-operaties aanpakken. 

1. Selecteer **Alle services** boven, links van de portal.
2. Voer *Monitor* in het vak **Filter** in. Wanneer **Monitor** in de resultaten wordt weergegeven, selecteert u deze.
3. Selecteer **onder INSTELLINGEN**de optie Diagnostische **instellingen**.
4. Selecteer de **groep** **Abonnement** en Resource die het openbare IP-adres bevatten dat u wilt registreren.
5. Selecteer **Openbaar IP-adres** voor **resourcetype**en selecteer vervolgens het specifieke openbare IP-adres waarvoor u metrische gegevens wilt registreren.
6. Selecteer **Diagnostische gegevens inschakelen om het logboek DDoSMitigationFlowLogs te verzamelen** en selecteer vervolgens zoveel van de volgende opties als u nodig hebt:

    - **Archiveren naar een opslagaccount:** gegevens worden naar een Azure Storage-account geschreven. Zie [Diagnostische logboeken archiveren](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over deze optie.
    - **Streamen naar een gebeurtenishub:** hiermee kan een logboekontvanger logboeken ophalen met behulp van een Azure Event Hub. Event hubs maken integratie met Splunk of andere SIEM-systemen mogelijk. Zie [Diagnostische logboeken streamen naar een gebeurtenishub voor](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)meer informatie over deze optie.
    - **Verzenden naar Logboekanalyse:** schrijft logboeken naar de Azure Monitor-service. Zie [Logboeken verzamelen voor gebruik in Azure Monitor-logboeken voor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)meer informatie over deze optie.
1. Als u de gegevens van stroomlogboeken wilt weergeven in het Azure Analytics-dashboard, u het voorbeelddashboard importeren uithttps://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Stroomlogboeken hebben de volgende velden: 
- Bron-IP
- Doel-IP
- Bronpoort 
- Doelpoort 
- Protocoltype 
- Maatregelen genomen tijdens mitigatie

Aanvalsanalyses werken alleen als DDoS Protection Standard is ingeschakeld op het virtuele netwerk van het openbare IP-adres. 

## <a name="validate-ddos-detection"></a>DDoS-detectie valideren

Microsoft werkt samen met [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) om een interface te bouwen waar u verkeer genereren tegen openbare IP-adressen met DDoS Protection voor simulaties. Met de BreakPoint Cloud-simulatie u:

- Valideren hoe Microsoft Azure DDoS Protection uw Azure-bronnen beschermt tegen DDoS-aanvallen
- Optimaliseer uw incidentresponsproces terwijl u onder DDoS-aanval staat
- DDoS-naleving van documenten
- Train uw netwerkbeveiligingsteams

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>DDoS-beveiligingswaarschuwingen weergeven in Azure Security Center

Azure Security Center biedt een lijst met [beveiligingswaarschuwingen,](/azure/security-center/security-center-managing-and-responding-alerts)met informatie om problemen te onderzoeken en op te lossen. Met deze functie krijgt u een uniform overzicht van waarschuwingen, waaronder DDoS-aanvalsgerelateerde waarschuwingen en de acties die zijn ondernomen om de aanval in de buurt te beperken.
Er zijn twee specifieke waarschuwingen die u ziet voor elke DDoS-aanval detectie en mitigatie:

- **DDoS-aanval gedetecteerd voor openbaar IP:** deze waarschuwing wordt gegenereerd wanneer de DDoS-beveiligingsservice detecteert dat een van uw openbare IP-adressen het doelwit is van een DDoS-aanval.
- **DDoS-aanval beperkt voor openbaar IP:** Deze waarschuwing wordt gegenereerd wanneer een aanval op het openbare IP-adres is beperkt.
Als u de waarschuwingen wilt weergeven, opent u **Beveiligingscentrum** in de Azure-portal. Selecteer **Beveiligingswaarschuwingen**onder **bedreigingsbeveiliging**. De volgende schermafbeelding toont een voorbeeld van de DDoS-aanvalswaarschuwingen.

![DDoS-waarschuwing in Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

De waarschuwingen bevatten algemene informatie over het openbare IP-adres dat wordt aangevallen, geo- en bedreigingsinformatie en herstelstappen.

## <a name="permissions"></a>Machtigingen

Als u wilt werken met DDoS-beveiligingsplannen, moet uw account worden toegewezen aan de [rol van netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rol waaraan de juiste acties in de volgende tabel zijn toegewezen:

| Actie                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Lees een DDoS-beveiligingsplan              |
| Microsoft.Network/ddosProtectionPlans/write       | Een DDoS-beveiligingsplan maken of bijwerken  |
| Microsoft.Network/ddosProtectionPlans/delete      | Een DDoS-beveiligingsplan verwijderen            |
| Microsoft.Network/ddosProtectionPlans/join/action | Deelnemen aan een DDoS-beveiligingsplan              |

Om DDoS-beveiliging voor een virtueel netwerk in te schakelen, moet uw account ook de juiste [acties voor virtuele netwerken](manage-virtual-network.md#permissions)toegewezen krijgen.

## <a name="next-steps"></a>Volgende stappen

- [Azure-beleid](policy-samples.md) maken en toepassen voor virtuele netwerken