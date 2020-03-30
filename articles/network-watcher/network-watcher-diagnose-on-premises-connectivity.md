---
title: Diagnose on-premises connectiviteit via VPN-gateway
titleSuffix: Azure Network Watcher
description: In dit artikel wordt beschreven hoe u on-premises connectiviteit diagnosticeren via vpn-gateway met problemen met Azure Network Watcher-bronnen.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 835b3a69e779b536961110b674ae67f4e8c13ce0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845065"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnose on-premises connectiviteit via VPN-gateways

Azure VPN Gateway stelt u in staat om hybride oplossingen te maken die de behoefte aan een veilige verbinding tussen uw on-premises netwerk en uw Virtuele Azure-netwerk aanpakken. Omdat uw vereisten uniek zijn, is de keuze van het on-premises VPN-apparaat ook uniek. Azure ondersteunt momenteel [verschillende VPN-apparaten](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) die voortdurend worden gevalideerd in samenwerking met de apparaatleveranciers. Controleer de apparaatspecifieke configuratie-instellingen voordat u uw on-premises VPN-apparaat configureert. Op dezelfde manier is Azure VPN Gateway geconfigureerd met een reeks [ondersteunde IPsec-parameters](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) die worden gebruikt voor het tot stand brengen van verbindingen. Momenteel is er geen manier voor u om een specifieke combinatie van IPsec-parameters op te geven of te selecteren in de Azure VPN Gateway. Om een succesvolle verbinding tot stand te brengen tussen on-premises en Azure, moeten de on-premises VPN-apparaatinstellingen in overeenstemming zijn met de IPsec-parameters die zijn voorgeschreven door Azure VPN Gateway. Als de instellingen onjuist zijn, is er een verlies van connectiviteit en tot nu toe het oplossen van deze problemen was niet triviaal en meestal uren duurde om te identificeren en het probleem op te lossen.

Met de probleemoplossingsfunctie azure network watcher u eventuele problemen met uw gateway en verbindingen diagnosticeren en beschikt u binnen enkele minuten over voldoende informatie om een weloverwogen beslissing te nemen om het probleem te verhelpen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Scenario

U wilt een site-to-site-verbinding configureren tussen Azure en on-premises met FortiGate als on-premises VPN-gateway. Om dit scenario te bereiken, hebt u de volgende instelling nodig:

1. Virtual Network Gateway - De VPN-gateway op Azure
1. Local Network Gateway - De [on-premises VPN-gatewayvertegenwoordiging](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) in Azure-cloud
1. Site-to-site-verbinding (routegebaseerd) - [Verbinding tussen de VPN-gateway en de on-premises router](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#CreateConnection)
1. [FortiGate configureren](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Gedetailleerde stapsgewijze richtlijnen voor het configureren van een site-to-site-configuratie zijn te vinden op bezoek bij: [Een VNet maken met een Site-to-Site-verbinding met behulp van de Azure-portal.](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Een van de kritieke configuratiestappen is het configureren van de IPsec-communicatieparameters, elke verkeerde configuratie leidt tot verlies van connectiviteit tussen het on-premises netwerk en Azure. Momenteel zijn Azure VPN Gateways geconfigureerd om de volgende IPsec-parameters voor fase 1 te ondersteunen. Zoals u zien in de onderstaande tabel, de encryptie-algoritmen ondersteund door Azure VPN Gateway zijn AES256, AES128, en 3DES.

### <a name="ike-phase-1-setup"></a>IKE fase 1 setup

| **Eigenschap** | **PolicyBased** | **RouteBased en Standard of High-Performance VPN-gateway** |
| --- | --- | --- |
| IKE-versie |IKEv1 |IKEv2 |
| Diffie-Hellman-groep |Groep 2 (1024 bits) |Groep 2 (1024 bits) |
| Verificatiemethode |Vooraf gedeelde sleutel |Vooraf gedeelde sleutel |
| Versleutelingsalgoritmen |AES256 AES128 3DES |AES256 3DES |
| Hash-algoritme |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Levensduur (tijd) van beveiligingskoppeling (SA) fase 1 |28.800 seconden |28.800 seconden |

Als gebruiker moet u uw FortiGate configureren, een voorbeeldconfiguratie is te vinden op [GitHub.](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt) Onbewust hebt u uw FortiGate geconfigureerd om SHA-512 te gebruiken als het hashing-algoritme. Omdat dit algoritme geen ondersteund algoritme is voor beleidsgebaseerde verbindingen, werkt uw VPN-verbinding wel.

Deze problemen zijn moeilijk op te lossen en de onderliggende oorzaken zijn vaak niet-intuïtief. In dit geval u een ondersteuningsticket openen om hulp te krijgen bij het oplossen van het probleem. Maar met Azure Network Watcher problemen API, u deze problemen te identificeren op uw eigen.

## <a name="troubleshooting-using-azure-network-watcher"></a>Problemen oplossen met Azure Network Watcher

Als u uw verbinding wilt diagnosticeren, maakt u verbinding met Azure PowerShell en start u de `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet. U de details over het gebruik van deze cmdlet vinden bij [Probleemie problemen virtuele netwerkgateway en verbindingen - PowerShell](network-watcher-troubleshoot-manage-powershell.md). Deze cmdlet kan enkele minuten in beslag nemen.

Zodra de cmdlet is voltooid, u naar de opslaglocatie navigeren die in de cmdlet is opgegeven om gedetailleerde informatie over het probleem en de logboeken te krijgen. Azure Network Watcher maakt een zip-map met de volgende logboekbestanden:

![1][1]

Open het bestand genaamd IKEErrors.txt en het geeft de volgende fout weer, die een probleem aangeeft met on-premises IKE-instelling verkeerde configuratie.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

U gedetailleerde informatie krijgen van de Scrubbed-wfpdiag.txt over de fout, zoals in dit geval vermeldt dat er was `ERROR_IPSEC_IKE_POLICY_MATCH` dat leiden tot verbinding niet goed werkt.

Een andere veel voorkomende verkeerde configuratie is het opgeven van onjuiste gedeelde sleutels. Als u in het voorgaande voorbeeld verschillende gedeelde sleutels hebt opgegeven, wordt `Error: Authentication failed. Check shared key`in IKEErrors.txt de volgende fout weergegeven: .

Azure Network Watcher probleemoplossing functie u diagnosticeren en problemen oplossen van uw VPN Gateway en Verbinding met het gemak van een eenvoudige PowerShell cmdlet. Momenteel ondersteunen we de diagnose van de volgende voorwaarden en werken we aan het toevoegen van meer conditie.

### <a name="gateway"></a>Gateway

| Fouttype | Reden | Logboek|
|---|---|---|
| NoFault | Als er geen fout is gedetecteerd. |Ja|
| GatewayNotFound | Kan gateway of Gateway niet vinden is niet ingericht. |Nee|
| PlannedMaintenance |  Er worden onderhoudswerkzaamheden uitgevoerd op het gatewayexemplaar.  |Nee|
| UserDrivenUpdate | Wanneer een gebruikersupdate aan de gang is. Dit kan een bewerking voor het formaat zijn. | Nee |
| VipUnResponsive | Kan de primaire instantie van de gateway niet bereiken. Dit gebeurt wanneer de gezondheidssonde uitvalt. | Nee |
| PlatformInActive | Er is een probleem met het platform. | Nee|
| ServiceNotRunning | De onderliggende service wordt niet uitgevoerd. | Nee|
| NoConnectionsFoundForGateway | Er bestaan geen verbindingen op de gateway. Dit is slechts een waarschuwing.| Nee|
| VerbindingenNiet verbonden | Geen van de verbindingen is verbonden. Dit is slechts een waarschuwing.| Ja|
| GatewayCPUUsageExceeded | Het huidige GATEWAY-gebruik CPU-gebruik is > 95%. | Ja |

### <a name="connection"></a>Verbinding

| Fouttype | Reden | Logboek|
|---|---|---|
| NoFault | Als er geen fout is gedetecteerd. |Ja|
| GatewayNotFound | Kan gateway of Gateway niet vinden is niet ingericht. |Nee|
| PlannedMaintenance | Er worden onderhoudswerkzaamheden uitgevoerd op het gatewayexemplaar.  |Nee|
| UserDrivenUpdate | Wanneer een gebruikersupdate aan de gang is. Dit kan een bewerking voor het formaat zijn.  | Nee |
| VipUnResponsive | Kan de primaire instantie van de gateway niet bereiken. Het gebeurt wanneer de gezondheidssonde faalt. | Nee |
| ConnectionEntityNotFound | Verbindingsconfiguratie ontbreekt. | Nee |
| Verbinding is verbroken losgekoppeld | De verbinding is gemarkeerd als 'losgekoppeld'. |Nee|
| ConnectionNotConfiguredOnGateway | De onderliggende service heeft de verbinding niet geconfigureerd. | Ja |
| VerbindingGemarkeerdDoor | De onderliggende service is gemarkeerd als stand-by.| Ja|
| Authentication | Preshared Key mismatch. | Ja|
| PeerReachability | De peer gateway is niet bereikbaar. | Ja|
| IkePolicyMismatch | De peer gateway heeft IKE-beleid dat niet wordt ondersteund door Azure. | Ja|
| WfpParse- fout | Er is een fout opgetreden bij het ontzeggen van het WFP-logboek. |Ja|

## <a name="next-steps"></a>Volgende stappen

Informatie over het controleren van VPN-gatewayconnectiviteit met PowerShell en Azure Automation door [naar Monitor VPN-gateways te](network-watcher-monitor-with-azure-automation.md) gaan met problemen met Azure Network Watcher

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
