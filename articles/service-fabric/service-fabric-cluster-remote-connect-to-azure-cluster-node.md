---
title: Verbinding maken met een Azure Service Fabric-clusterknooppunt
description: Meer informatie over het op afstand maken van een verbinding met een schaalsetinstantie (een clusterknooppunt servicestructuur).
ms.topic: conceptual
ms.date: 03/23/2018
ms.openlocfilehash: c7ca4f0d5dce1b19837a44d5c9749f3e1293c6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458314"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Verbinding maken met een instantie voor het schalen van virtuele machines of een clusterknooppunt
In een cluster servicestructuur die in Azure wordt uitgevoerd, stelt elk clusterknooppunttype dat u [definieert een afzonderlijke schaal voor virtuele machines in.](service-fabric-cluster-nodetypes.md)  U op afstand verbinding maken met specifieke schaalset-exemplaren (clusterknooppunten).  In tegenstelling tot vm's met één instantie hebben schaalset-instanties geen eigen virtuele IP-adressen. Dit kan een uitdaging zijn wanneer u op zoek bent naar een IP-adres en poort die u gebruiken om op afstand verbinding te maken met een specifieke instantie.

Voer de volgende stappen uit om een IP-adres en poort te vinden die u op afstand gebruiken om verbinding te maken met een specifieke instantie.

1. Download de binnenkomende NAT-regels voor Extern bureaublad-protocol (RDP).

    Normaal gesproken heeft elk knooppunttype dat in uw cluster is gedefinieerd, zijn eigen virtuele IP-adres en een speciale load balancer. Standaard wordt de load balancer voor een knooppunttype benoemd met de volgende indeling: *LB-{cluster-name}-{node-type}*; *LB-mycluster-FrontEnd*. 
    
    Selecteer op de pagina voor uw load balancer in Azure-portal **de instellingen** > **inkomende NAT-regels:** 

    ![Regels voor inkomende INkomende laadbalansen](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    In de volgende schermafbeelding worden de binnenkomende NAT-regels weergegeven voor een knooppunttype met de naam FrontEnd: 

    ![Regels voor inkomende INkomende laadbalansen](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Voor elk knooppunt wordt het IP-adres weergegeven in de kolom **BESTEMMING,** geeft de kolom **DOEL** de schaalsetinstantie en de kolom **SERVICE** het poortnummer. Voor externe verbindingen worden poorten toegewezen aan elk knooppunt in oplopende volgorde, te beginnen met poort 3389.

    U ook de inkomende `Microsoft.Network/loadBalancers` NAT-regels vinden in het gedeelte van de sjabloon Resourcemanager voor uw cluster.
    
2. Als u wilt bevestigen dat de binnenkomende poort om poorttoewijzing voor een knooppunt te targeten, klikt u op de regel en kijkt u naar de waarde van de **doelpoort.** In de volgende schermafbeelding wordt de inkomende NAT-regel voor het knooppunt **FrontEnd (Instantie 1)** in de vorige stap weergegeven. Hoewel het (inbound) poortnummer 3390 is, wordt de doelpoort toegewezen aan poort 3389, de poort voor de RDP-service op het doel.  

    ![Toewijzing van doelpoorten](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Standaard is de doelpoort poort 3389 voor Windows-clusters, die wordt toegewezen aan de RDP-service op het doelknooppunt. Voor Linux-clusters is de doelpoort poort 22, die wordt toegewezen aan de Secure Shell (SSH) service.

3. Maak op afstand verbinding met het specifieke knooppunt (schaalsetinstantie). U de gebruikersnaam en het wachtwoord gebruiken die u hebt ingesteld toen u het cluster hebt gemaakt of andere referenties die u hebt geconfigureerd. 

    In de volgende schermafbeelding wordt weergegeven dat verbinding met extern bureaublad wordt gebruikt om verbinding te maken met het knooppunt **FrontEnd (Instantie 1)** in een Windows-cluster:
    
    ![Verbinding met extern bureaublad](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Op Linux-knooppunten u verbinding maken met SSH (in het volgende voorbeeld wordt hetzelfde IP-adres en dezelfde poort opnieuw gebruikt voor beknoptheid):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Lees voor de volgende stappen de volgende artikelen:
* Bekijk het [overzicht van de functie 'Overal implementeren' en een vergelijking met door Azure beheerde clusters.](service-fabric-deploy-anywhere.md)
* Meer informatie over [clusterbeveiliging](service-fabric-cluster-security.md).
* [De RDP-poortbereikwaarden](./scripts/service-fabric-powershell-change-rdp-port-range.md) na implementatie bijwerken
* [De gebruikersnaam en het wachtwoord van de beheerder voor](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) clusterVM's wijzigen

