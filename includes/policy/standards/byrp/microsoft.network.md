---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 93dcd58550d16a25c9d886107692c03c782596ff
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380242"
---
## <a name="azure-security-benchmark"></a>Azure Security-benchmark

De [Azure Security-benchmark](../../../../articles/security/benchmarks/overview.md) biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. Bekijk de [toewijzingsbestanden van de Azure Security-benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) om te zien hoe deze service volledig is toegewezen aan de Azure Security-benchmark.

Raadpleeg [Naleving van Azure Policy-regelgeving - Azure Security-benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md) om te zien hoe de beschikbare ingebouwde modules voor Azure Policy voor alle Azure-services zijn toegewezen aan deze nalevingsstandaard.

|Domain |Id van besturingselement |Titel van besturingselement |Beleid<br /><sub>(Azure-portal)</sub> |Beleidsversie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Netwerkbeveiliging |1.1 |Resources beschermen met behulp van netwerkbeveiligingsgroepen of Azure Firewall op uw virtueel netwerk |[Alle internetverkeer moet worden gerouteerd via uw geïmplementeerde Azure Firewall](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|Netwerkbeveiliging |1.1 |Resources beschermen met behulp van netwerkbeveiligingsgroepen of Azure Firewall op uw virtueel netwerk |[Subnets moeten worden gekoppeld aan een netwerkbeveiligingsgroep](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|Netwerkbeveiliging |1.1 |Resources beschermen met behulp van netwerkbeveiligingsgroepen of Azure Firewall op uw virtueel netwerk |[Virtuele machines moeten zijn verbonden met een goedgekeurd virtueel netwerk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|Netwerkbeveiliging |1.1 |Resources beschermen met behulp van netwerkbeveiligingsgroepen of Azure Firewall op uw virtueel netwerk |[Virtuele netwerken moeten gebruikmaken van de opgegeven virtuele-netwerkgateway](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff1776c76-f58c-4245-a8d0-2b207198dc8b) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |
|Netwerkbeveiliging |1.2 |De configuratie en het verkeer van VNet's, Subnets en NIC's controleren en vastleggen |[Network Watcher moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|Netwerkbeveiliging |1.4 |Communicatie met gekende schadelijke IP-adressen weigeren |[Alle internetverkeer moet worden gerouteerd via uw geïmplementeerde Azure Firewall](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|Netwerkbeveiliging |1.4 |Communicatie met gekende schadelijke IP-adressen weigeren |[Azure DDoS-beveiligingsstandaard moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
|Netwerkbeveiliging |1.5 |Netwerkpakketten en stroomlogboeken vastleggen |[Network Watcher moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations Benchmark

Raadpleeg [Naleving van Azure Policy-regelgeving - CIS Microsoft Azure Foundations Benchmark 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md) om te zien hoe de beschikbare ingebouwde modules voor Azure Policy voor alle Azure-services zijn toegewezen aan deze nalevingsstandaard.
Zie [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/)voor meer informatie over deze nalevingsstandaard.

|Domain |Id van besturingselement |Titel van besturingselement |Beleid<br /><sub>(Azure-portal)</sub> |Beleidsversie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Security Center |2.9 |Controleren of de standaardbeleidsinstelling voor "NGFW-bewaking inschakelen" van de ASC is niet is "uitgeschakeld" |[Subnets moeten worden gekoppeld aan een netwerkbeveiligingsgroep](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|Netwerken |6.1 |Controleren of de RDP-toegang is afgescheiden van het internet |[RDP-toegang via internet moet worden geblokkeerd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |
|Netwerken |6,2 |Controleren of SSH-toegang is afgescheiden van het internet |[SSH-toegang via internet moet worden geblokkeerd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |
|Netwerken |6.5 |Controleren of Network Watcher is 'ingeschakeld' |[Network Watcher moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Raadpleeg [Naleving van Azure Policy-regelgeving - NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md) om te zien hoe de beschikbare ingebouwde modules voor Azure Policy voor alle Azure-services zijn toegewezen aan deze nalevingsstandaard.
Zie [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)voor meer informatie over deze nalevingsstandaard.

|Domain |Id van besturingselement |Titel van besturingselement |Beleid<br /><sub>(Azure-portal)</sub> |Beleidsversie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Systeem- en gegevensintegriteit |3.14.6 |Organisatiesystemen, met inbegrip van inkomend en uitgaand communicatieverkeer, bewaken om aanvallen en indicatoren van mogelijke aanvallen te detecteren. |[Network Watcher moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Raadpleeg [Naleving van Azure Policy-regelgeving - NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md) om te zien hoe de beschikbare ingebouwde modules voor Azure Policy voor alle Azure-services zijn toegewezen aan deze nalevingsstandaard.
Zie [NIST SP 800-53 R4](https://nvd.nist.gov/800-53)voor meer informatie over deze nalevingsstandaard.

|Domain |Id van besturingselement |Titel van besturingselement |Beleid<br /><sub>(Azure-portal)</sub> |Beleidsversie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Systeem- en communicatiebeveiliging |SC-5 |Denial of Service Protection |[Azure DDoS-beveiligingsstandaard moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |

