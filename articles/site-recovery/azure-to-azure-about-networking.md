---
title: Over netwerken in azure voor nood herstel met Azure met Azure Site Recovery | Microsoft Docs
description: Biedt een overzicht van netwerken voor replicatie van virtuele Azure-machines met behulp van Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: b549e8bf53ca7728b18e04542c7291f0ce720cb0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876460"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Over netwerken in azure naar Azure-replicatie



In dit artikel worden netwerk richtlijnen beschreven wanneer u virtuele machines van Azure repliceert en herstelt vanuit de ene regio naar een andere, met behulp van [Azure site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Voordat u begint

Meer informatie over hoe Site Recovery herstel na nood gevallen biedt voor [dit scenario](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typische netwerk infrastructuur

In het volgende diagram ziet u een typische Azure-omgeving, voor toepassingen die worden uitgevoerd op virtuele Azure-machines:

![klant-omgeving](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Als u Azure ExpressRoute of een VPN-verbinding van uw on-premises netwerk naar Azure gebruikt, is de omgeving als volgt:

![klant-omgeving](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normaal gesp roken worden netwerken beveiligd met firewalls en netwerk beveiligings groepen (Nsg's). Firewalls gebruiken een URL of IP-gebaseerde white list om de netwerk verbinding te beheren. Nsg's bieden regels die gebruikmaken van IP-adresbereiken voor het beheren van de netwerk verbinding.

>[!IMPORTANT]
> Het gebruik van een geverifieerde proxy voor het beheren van de netwerk verbinding wordt niet ondersteund door Site Recovery en de replicatie kan niet worden ingeschakeld.


## <a name="outbound-connectivity-for-urls"></a>Uitgaande connectiviteit voor URL's

Als u een firewall proxy op basis van een URL gebruikt voor het beheren van uitgaande connectiviteit, kunt u deze Site Recovery Url's toestaan:


**URL** | **Details**  
--- | ---
*.blob.core.windows.net | Vereist zodat gegevens kunnen worden geschreven naar het cache-opslag account in de bron regio van de virtuele machine. Als u alle cache opslag accounts voor uw virtuele machines weet, kunt u de specifieke Url's van het white list (bijvoorbeeld: cache1.blob.core.windows.net en cache2.blob.core.windows.net) in plaats van *. blob.core.windows.net
login.microsoftonline.com | Vereist voor autorisatie en verificatie voor de Url's van de Site Recovery-service.
*.hypervrecoverymanager.windowsazure.com | Vereist zodat de Site Recovery service communicatie kan worden uitgevoerd vanaf de virtuele machine. U kunt de bijbehorende ' Site Recovery IP ' gebruiken als uw firewall proxy Ip's ondersteunt.
*.servicebus.windows.net | Vereist zodat de Site Recovery bewakings-en diagnostische gegevens van de virtuele machine kunnen worden geschreven. U kunt de overeenkomende Site Recovery monitoring IP gebruiken als uw firewall proxy Ip's ondersteunt.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Uitgaande connectiviteit voor IP-adresbereiken

Als u een op IP gebaseerde firewall proxy gebruikt of NSG regels voor het beheren van uitgaande connectiviteit, moeten deze IP-bereiken zijn toegestaan.

- Alle IP-adresbereiken die overeenkomen met de opslag accounts in de bron regio
    - Maak een NSG-regel op basis van een [opslag service label](../virtual-network/security-overview.md#service-tags) voor de bron regio.
    - Sta deze adressen toe zodat gegevens kunnen worden geschreven naar het cache-opslag account van de VM.
- Maak een [Azure Active Directory (AAD)-servicetag](../virtual-network/security-overview.md#service-tags) op basis van NSG-regel voor het toestaan van toegang tot alle IP-adressen die overeenkomen met AAD
    - Als er nieuwe adressen worden toegevoegd aan de Azure Active Directory (AAD) in de toekomst, moet u nieuwe NSG-regels maken.
- Site Recovery IP-adressen van service-eind punten: beschikbaar in een [XML-bestand](https://aka.ms/site-recovery-public-ips) en zijn afhankelijk van de doel locatie.
- We raden u aan de vereiste NSG-regels te maken op een test-NSG en te controleren of er geen problemen zijn voordat u de regels op een productie NSG maakt.


Site Recovery IP-adresbereiken zijn als volgt:

   **Doel** | **Site Recovery IP-adres** |  **IP-Site Recovery bewaking**
   --- | --- | ---
   Azië - oost | 52.175.17.132 | 13.94.47.61
   Azië - zuidoost | 52.187.58.193 | 13.76.179.223
   India - centraal | 52.172.187.37 | 104.211.98.185
   India - zuid | 52.172.46.220 | 104.211.224.190
   US - noord-centraal | 23.96.195.247 | 168.62.249.226
   Europa - noord | 40.69.212.238 | 52.169.18.8
   Europa -west | 52.166.13.64 | 40.68.93.145
   East US | 13.82.88.226 | 104.45.147.24
   US - west | 40.83.179.48 | 104.40.26.199
   US - zuid-centraal | 13.84.148.14 | 104.210.146.250
   US - centraal | 40.69.144.231 | 52.165.34.144
   US - oost 2 | 52.184.158.163 | 40.79.44.59
   Japan - oost | 52.185.150.140 | 138.91.1.105
   Japan - west | 52.175.146.69 | 138.91.17.38
   Brazilië - zuid | 191.234.185.172 | 23.97.97.36
   Australië - oost | 104.210.113.114 | 191.239.64.144
   Australië - zuidoost | 13.70.159.158 | 191.239.160.45
   Canada - midden | 52.228.36.192 | 40.85.226.62
   Canada - oost | 52.229.125.98 | 40.86.225.142
   US - west-centraal | 52.161.20.168 | 13.78.149.209
   US - west 2 | 52.183.45.166 | 13.66.228.204
   Verenigd Koninkrijk West | 51.141.3.203 | 51.141.14.113
   Verenigd Koninkrijk Zuid | 51.140.43.158 | 51.140.189.52
   VK Zuid 2 | 13.87.37.4| 13.87.34.139
   VK Noord | 51.142.209.167 | 13.87.102.68
   Korea - centraal | 52.231.28.253 | 52.231.32.85
   Korea - zuid | 52.231.198.185 | 52.231.200.144
   Frankrijk - centraal | 52.143.138.106 | 52.143.136.55
   Frankrijk - zuid | 52.136.139.227 |52.136.136.62
   Australië-centraal| 20.36.34.70 | 20.36.46.142
   Australië - centraal 2| 20.36.69.62 | 20.36.74.130
   Zuid-Afrika - west | 102.133.72.51 | 102.133.26.128
   Zuid-Afrika - noord | 102.133.160.44 | 102.133.154.128
   VS (overheid) - Virginia | 52.227.178.114 | 23.97.0.197
   US Gov - Iowa | 13.72.184.23 | 23.97.16.186
   VS (overheid) - Arizona | 52.244.205.45 | 52.244.48.85
   VS (overheid) - Texas | 52.238.119.218 | 52.238.116.60
   US DoD - oost | 52.181.164.103 | 52.181.162.129
   US DoD - centraal | 52.182.95.237 | 52.182.90.133
   China - noord | 40.125.202.254 | 42.159.4.151
   China - noord 2 | 40.73.35.193 | 40.73.33.230
   China - oost | 42.159.205.45 | 42.159.132.40
   China - oost 2 | 40.73.118.52| 40.73.100.125
  
## <a name="example-nsg-configuration"></a>Voor beeld van NSG-configuratie

In dit voor beeld ziet u hoe u NSG-regels configureert voor replicatie van een virtuele machine.

- Als u NSG-regels gebruikt om de uitgaande connectiviteit te beheren, gebruikt u de regels HTTPS-uitgaand toestaan op poort: 443 voor alle vereiste IP-adresbereiken.
- In het voor beeld wordt ervan uitgegaan dat de bron locatie van de virtuele machine ' vs-Oost ' is en de doel locatie ' centraal VS ' is.

### <a name="nsg-rules---east-us"></a>NSG-regels-VS-Oost

1. Maak een uitgaande HTTPS (443)-beveiligings regel voor ' storage. oostelijke ' op de NSG, zoals wordt weer gegeven in de onderstaande scherm afbeelding.

      ![opslag code](./media/azure-to-azure-about-networking/storage-tag.png)

2. Maak een uitgaande HTTPS (443) beveiligings regel voor ' AzureActiveDirectory ' op de NSG, zoals weer gegeven in de onderstaande scherm afbeelding.

      ![Aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Maak regels voor uitgaande HTTPS (443) voor de Site Recovery IP-adressen die overeenkomen met de doel locatie:

   **Location** | **Site Recovery IP-adres** |  **IP-adres van Site Recovery bewaking**
    --- | --- | ---
   US - centraal | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG-regels-VS-Centraal

Deze regels zijn vereist zodat replicatie kan worden ingeschakeld vanuit de doel regio naar de bron regio na failover:

1. Maak een uitgaande HTTPS (443) beveiligings regel voor ' opslag. Centraalus ' op de NSG.

2. Maak een uitgaande HTTPS (443) beveiligings regel voor ' AzureActiveDirectory ' op de NSG.

3. Maak regels voor uitgaande HTTPS (443) voor de Site Recovery IP-adressen die overeenkomen met de bron locatie:

   **Location** | **Site Recovery IP-adres** |  **IP-adres van Site Recovery bewaking**
    --- | --- | ---
   East US | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Configuratie van virtueel netwerk apparaat

Als u virtuele netwerk apparaten (Nva's) gebruikt om het uitgaande netwerk verkeer van Vm's te beheren, kan het apparaat worden beperkt als alle replicatie verkeer via de NVA wordt door gegeven. U kunt het beste een netwerk service-eind punt in uw virtuele netwerk maken voor ' opslag ', zodat het replicatie verkeer niet naar de NVA gaat.

### <a name="create-network-service-endpoint-for-storage"></a>Netwerk service-eind punt voor opslag maken
U kunt een netwerk service-eind punt in uw virtuele netwerk maken voor ' opslag ' zodat het replicatie verkeer de Azure-grens niet verlaat.

- Selecteer uw virtuele Azure-netwerk en klik op service-eind punten

    ![opslag-eind punt](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Klik op het tabblad toevoegen en service-eind punten toevoegen wordt geopend
- Selecteer ' micro soft. Storage ' onder ' service ' en de vereiste subnetten onder het veld subnets en klik op toevoegen.

>[!NOTE]
>Beperk geen toegang tot het virtuele netwerk tot uw opslag accounts die worden gebruikt voor ASR. U moet toegang toestaan vanaf alle netwerken

### <a name="forced-tunneling"></a>Geforceerde tunneling

U kunt de standaard systeem route van Azure voor het adres voorvoegsel 0.0.0.0/0 vervangen door een [aangepaste route](../virtual-network/virtual-networks-udr-overview.md#custom-routes) en VM-verkeer omleiden naar een on-premises netwerk virtueel apparaat (NVA), maar deze configuratie wordt niet aanbevolen voor replicatie van site Recovery. Als u aangepaste routes gebruikt, moet u [een service-eind punt voor een virtueel netwerk maken](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) in het virtuele netwerk voor "opslag" zodat het replicatie verkeer de Azure-grens niet verlaat.

## <a name="next-steps"></a>Volgende stappen
- Begin met het beveiligen van uw workloads door [Azure virtual machines te repliceren](site-recovery-azure-to-azure.md).
- Meer informatie over het [bewaren van IP-adressen](site-recovery-retain-ip-azure-vm-failover.md) voor failover van de virtuele machine van Azure.
- Meer informatie over herstel na nood gevallen van [virtuele Azure-machines met ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
