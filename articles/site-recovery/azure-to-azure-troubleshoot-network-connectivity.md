---
title: Problemen met de connectiviteit voor Azure met Azure-herstel met Azure-herstel oplossen
description: Verbindingsproblemen oplossen in Azure VM-noodherstel
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: d2cc4133e52e7cab812413d23948da6ac2660e77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884865"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Problemen met azure-to-Azure VM-netwerkconnectiviteit oplossen

In dit artikel worden de veelvoorkomende problemen beschreven die verband houden met netwerkconnectiviteit wanneer u virtuele azure-machines (VM) van de ene regio naar een andere regio repliceert en herstelt. Zie de [verbindingsvereisten voor het repliceren van Azure VM's voor](azure-to-azure-about-networking.md)meer informatie over netwerkvereisten.

Als replicatie van siteherstel werkt, is uitgaande connectiviteit met specifieke URL's of IP-bereiken vereist van de VM. Als uw vm zich achter een firewall bevindt of NSG-regels (Network Security Group) gebruikt om uitgaande connectiviteit te beheren, u met een van deze problemen worden geconfronteerd.

| URL | Details |
|---|---|
| `*.blob.core.windows.net` | Vereist zodat gegevens vanaf de VM naar het cacheopslagaccount in het brongebied kunnen worden geschreven. Als u alle cacheopslagaccounts voor uw VM's kent, u een lijst met toegestane gegevens gebruiken voor de URL's van het specifieke opslagaccount. Bijvoorbeeld, `cache1.blob.core.windows.net` en `cache2.blob.core.windows.net` in `*.blob.core.windows.net`plaats van . |
| `login.microsoftonline.com` | Vereist voor autorisatie en verificatie voor de URL's van de siteherstelservice. |
| `*.hypervrecoverymanager.windowsazure.com` | Vereist zodat de servicecommunicatie siteherstel vanuit de VM kan plaatsvinden. U het bijbehorende _SITE Recovery IP_ gebruiken als uw firewallproxy IP's ondersteunt. |
| `*.servicebus.windows.net` | Vereist zodat de siteherstelcontrole- en diagnostische gegevens kunnen worden geschreven vanuit de VM. U het bijbehorende _IP Site Recovery Monitoring-ip_ gebruiken als uw firewallproxy IP's ondersteunt. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Uitgaande connectiviteit voor URL's of IP-bereiken voor siteherstel (foutcode 151037 of 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Probleem 1: Kan geen virtuele Azure-machine registreren met siteherstel (151195)

#### <a name="possible-cause"></a>Mogelijke oorzaak

Er kan geen verbinding worden gemaakt met eindpunten voor siteherstel vanwege een DNS-oplossingsfout (Domain Name System). Dit probleem komt vaker voor tijdens herbescherming wanneer u bent mislukt via de VM, maar de DNS-server is niet bereikbaar vanuit het DR-gebied (Disaster Recovery).

#### <a name="resolution"></a>Oplossing

Als u aangepaste DNS gebruikt, controleert u of de DNS-server toegankelijk is vanuit het gebied voor noodherstel.

Ga als u controleren of de VM een aangepaste DNS-instelling gebruikt:

1. Open **virtuele machines** en selecteer de VM.
1. Navigeer naar de **VM-instellingen** en selecteer **Netwerken**.
1. Selecteer **in Virtueel netwerk/subnet**de koppeling om de bronpagina van het virtuele netwerk te openen.
1. Ga naar **Instellingen** en selecteer **DNS-servers**.

Probeer toegang te krijgen tot de DNS-server vanaf de virtuele machine. Als de DNS-server niet toegankelijk is, maakt u deze toegankelijk door te falen via de DNS-server of door de sitelijn tussen het DR-netwerk en DNS te maken.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-fout":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Probleem 2: Configuratie van siteherstel is mislukt (151196)

> [!NOTE]
> Als de VM's zich achter een **standaard** interne load balancer bevinden, heeft deze standaard `login.microsoftonline.com`geen toegang tot de IP's van Office 365, zoals . Wijzig deze in het type **Basisinterne** load balancer of maak uitgaande toegang zoals vermeld in het artikel [Load balancing en outbound rules configureren in Standard Load Balancer met Azure CLI](/azure/load-balancer/configure-load-balancer-outbound-cli).

#### <a name="possible-cause"></a>Mogelijke oorzaak

Er kan geen verbinding worden gemaakt met IP4-eindpunten voor verificatie en identiteit van Office 365.

#### <a name="resolution"></a>Oplossing

- Azure Site Recovery vereist toegang tot de IP-bereiken van Office 365 voor verificatie.
- Als u NSG-regels/firewallproxy (Azure Network Security Group) gebruikt om uitgaande netwerkconnectiviteit op de VM te beheren, moet u de communicatie met de IP-bereiken van Office 365 toestaan. Maak een OP [Azure Active Directory (Azure AD) servicetag](/azure/virtual-network/security-overview#service-tags) gebaseerde NSG-regel die toegang biedt tot alle IP-adressen die overeenkomen met Azure AD.
- Als er in de toekomst nieuwe adressen aan Azure AD worden toegevoegd, moet u nieuwe NSG-regels maken.

### <a name="example-nsg-configuration"></a>Voorbeeld NSG-configuratie

In dit voorbeeld ziet u hoe u NSG-regels configureert voor een vm die moet worden gerepliceerd.

- Als u NSG-regels gebruikt om uitgaande connectiviteit te beheren, gebruikt u **HTTPS-uitgaande** regels toestaan voor poort 443 voor alle vereiste IP-adresbereiken.
- Het voorbeeld gaat ervan uit dat de VM-bronlocatie **Oost-VS** is en de doellocatie **Centraal VS**.

#### <a name="nsg-rules---east-us"></a>NSG regels - Oost-VS

1. Maak een HTTPS-regel voor uitgaande beveiliging voor de NSG, zoals in de volgende schermafbeelding wordt weergegeven. In dit voorbeeld wordt de **servicetag Destination:** _Storage.EastUS_ en **Destination port ranges**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="opslaglabel":::

1. Maak een HTTPS-regel voor uitgaande beveiliging voor de NSG, zoals in de volgende schermafbeelding wordt weergegeven. In dit voorbeeld wordt de **servicetag Doel gebruikt:** _AzureActiveDirectory-_ en **Doelpoortbereiken:** _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tag":::

1. Https-poort 443-outboundregels maken voor de IP's van siteherstel die overeenkomen met de doellocatie:

   | Locatie | IP-adres siteherstel | IP-adres siteherstelcontrole |
   | --- | --- | --- |
   | VS - centraal | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>NSG regels - Centrale VS

Voor dit voorbeeld zijn deze NSG-regels vereist, zodat replicatie kan worden ingeschakeld van het doelgebied naar het brongebied na failover:

1. Maak een HTTPS-regel voor uitgaande beveiliging voor _Storage.CentralUS:_

   - **Doelservicetag**: _Storage.CentralUS_
   - **Bestemmingspoortbereiken**: _443_

1. Maak een HTTPS-outbound beveiligingsregel voor _AzureActiveDirectory_.

   - **Doelservicetag:** _AzureActiveDirectory_
   - **Bestemmingspoortbereiken**: _443_

1. Https-poort 443-outboundregels maken voor de IP-functie siteherstel die overeenkomen met de bronlocatie:

   | Locatie | IP-adres siteherstel | IP-adres siteherstelcontrole |
   | --- | --- | --- |
   | VS - oost | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Probleem 3: Configuratie van siteherstel is mislukt (151197)

#### <a name="possible-cause"></a>Mogelijke oorzaak

Er kan geen verbinding worden gemaakt met eindpunten van de Azure Site Recovery-service.

#### <a name="resolution"></a>Oplossing

Azure Site Recovery vereist toegang tot de [IP-bereiken van Site Recovery](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) afhankelijk van de regio. Zorg ervoor dat de vereiste IP-bereiken toegankelijk zijn vanaf de VM.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Probleem 4: Azure-to-Azure-replicatie is mislukt wanneer het netwerkverkeer via on-premises proxyserver gaat (151072)

#### <a name="possible-cause"></a>Mogelijke oorzaak

De aangepaste proxy-instellingen zijn ongeldig en de Azure Site Recovery Mobility-serviceagent heeft de proxy-instellingen van Internet Explorer (IE) niet automatisch gedetecteerd.

#### <a name="resolution"></a>Oplossing

1. De Mobiliteitsserviceagent detecteert de proxy-instellingen `/etc/environment` van IE op Windows en op Linux.
1. Als u de proxy alleen instelt voor azure site recovery mobility-service, u de proxygegevens opgeven in _ProxyInfo.conf_ op:

   - **Linux**:`/usr/local/InMage/config/`
   - **Windows**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. De _ProxyInfo.conf_ moet de proxy-instellingen in de volgende _INI-indeling_ hebben:

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure Site Recovery Mobility-serviceagent ondersteunt alleen **niet-geverifieerde proxy's.**

### <a name="fix-the-problem"></a>Het probleem oplossen

Volg de stappen in het [netwerkrichtlijnendocument](site-recovery-azure-to-azure-networking-guidance.md)om [de vereiste URL's](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) of de [vereiste IP-bereiken](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)toe te staan .

## <a name="next-steps"></a>Volgende stappen

[Azure VM's repliceren naar een andere Azure-regio](azure-to-azure-how-to-enable-replication.md)
