---
title: Knooppunteindpunten configureren in Azure Batch-groep | Microsoft Documenten
description: Toegang tot SSH- of RDP-poorten configureren of uitschakelen op compute-knooppunten in een Azure Batch-groep.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: labrenne
ms.openlocfilehash: 098ccf999391412520989c4ec2433fd73bc0a72d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77017221"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Externe toegang tot rekenknooppunten in een Azure Batch-groep configureren of uitschakelen

Batch staat standaard toe dat een [knooppuntgebruiker](/rest/api/batchservice/computenode/adduser) met netwerkconnectiviteit extern verbinding kan maken met een compute-knooppunt in een batchgroep. Een gebruiker kan bijvoorbeeld verbinding maken via Extern bureaublad (RDP) op poort 3389 met een compute-knooppunt in een Windows-groep. Op dezelfde manier kan een gebruiker standaard verbinding maken met Secure Shell (SSH) op poort 22 met een compute node in een Linux-pool. 

In uw omgeving moet u mogelijk deze standaardinstellingen voor externe toegang beperken of uitschakelen. U deze instellingen wijzigen met behulp van de Batch-API's om de eigenschap [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) in te stellen. 

## <a name="about-the-pool-endpoint-configuration"></a>Over de configuratie van het eindpunt van de groep
De endpoint-configuratie bestaat uit een of meer [NAT-groepen (Network Address Translation)](/rest/api/batchservice/pool/add#inboundnatpool) van frontend-poorten. (Verwar een NAT-groep niet met de batchgroep met rekenknooppunten.) U stelt elke NAT-groep in om de standaardverbindingsinstellingen op de rekenknooppunten van de groep te overschrijven. 

Elke NAT-poolconfiguratie bevat een of meer [NSG-regels (Network Security Group).](/rest/api/batchservice/pool/add#networksecuritygrouprule) Elke NSG-regel staat bepaald netwerkverkeer toe of weigert dit naar het eindpunt. U ervoor kiezen om al het verkeer, verkeer dat is geïdentificeerd door een [servicetag](../virtual-network/security-overview.md#service-tags) (zoals 'Internet') of verkeer van specifieke IP-adressen of subnetten toe te staan of te weigeren.

### <a name="considerations"></a>Overwegingen
* De configuratie van het eindpunt van de groep maakt deel uit van de [netwerkconfiguratie](/rest/api/batchservice/pool/add#networkconfiguration)van de groep. De netwerkconfiguratie kan optioneel instellingen bevatten om de groep te voegen bij een [virtueel Azure-netwerk.](batch-virtual-network.md) Als u de groep in een virtueel netwerk instelt, u NSG-regels maken die adresinstellingen in het virtuele netwerk gebruiken.
* U meerdere NSG-regels configureren wanneer u een NAT-groep configureert. De regels worden gecontroleerd in de volgorde van prioriteit. Zodra een regel van toepassing is, worden geen andere regels meer getest.


## <a name="example-deny-all-rdp-traffic"></a>Voorbeeld: Alle RDP-verkeer weigeren

In het volgende C#-fragment ziet u hoe u het RDP-eindpunt configureert op compute-knooppunten in een Windows-groep om al het netwerkverkeer te weigeren. Het eindpunt maakt gebruik van een frontend pool van poorten in het bereik *60000 - 60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Voorbeeld: Ontken al het SSH-verkeer van het internet

In het volgende Python-fragment ziet u hoe u het SSH-eindpunt configureert op compute-knooppunten in een Linux-groep om al het internetverkeer te weigeren. Het eindpunt maakt gebruik van een frontend pool van poorten in het bereik *4000 - 4100*. 

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                    source_address_prefix='Internet'
                )
            ]
        )
        ]
    )
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Voorbeeld: RDP-verkeer toestaan vanaf een specifiek IP-adres

In het volgende C#-fragment ziet u hoe u het RDP-eindpunt configureert op compute-knooppunten in een Windows-groep om ALLEEN RDP-toegang toe te staan vanaf *IP-adres 198.51.100.7*. De tweede NSG-regel weigert verkeer dat niet overeenkomt met het IP-adres.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Voorbeeld: SSH-verkeer toestaan vanaf een specifiek subnet

In het volgende Python-fragment ziet u hoe u het SSH-eindpunt op compute-knooppunten in een Linux-groep configureert om alleen toegang toe te staan vanaf subnet *192.168.1.0/24*. De tweede NSG-regel ontkent verkeer dat niet overeenkomt met het subnet.

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access='allow',
                    source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                    priority=175,
                    access='deny',
                    source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Volgende stappen

- Zie [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/security-overview.md)voor meer informatie over NSG-regels in Azure.

- Zie [Grootschalige parallelle rekenoplossingen ontwikkelen met Batch](batch-api-basics.md)voor een diepgaand overzicht van Batch.

