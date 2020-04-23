---
title: Toegang tot Azure VMware-oplossing door CloudSimple vanuit on-premises
titleSuffix: Azure VMware Solution by CloudSimple
description: Toegang tot uw Azure VMware-oplossing door CloudSimple vanuit uw on-premises netwerk via een firewall
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 539665c4756a7dc87078922421b45a88404f58f1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868145"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Toegang tot uw CloudSimple Private Cloud-omgeving en -toepassingen van on-premises

Er kan een verbinding worden ingesteld van on-premises netwerk naar CloudSimple met Azure ExpressRoute of Site-to-Site VPN.  Krijg toegang tot uw CloudSimple Private Cloud vCenter en alle workloads die u met de verbinding op de Private Cloud uitvoert.  U bepalen welke poorten op de verbinding worden geopend met behulp van een firewall in uw on-premises netwerk.  In dit artikel worden enkele van de typische vereisten voor de toepassingspoort besproken.  Raadpleeg voor alle andere toepassingen de toepassingsdocumentatie voor poortvereisten.

## <a name="ports-required-for-accessing-vcenter"></a>Poorten die nodig zijn voor toegang tot vCenter

Om toegang te krijgen tot uw Private Cloud vCenter en NSX-T-manager, moeten poorten die in de onderstaande tabel zijn gedefinieerd, worden geopend op de on-premises firewall.  

| Poort       | Bron                           | Doel                      | Doel                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | On-premises DNS-servers          | Private Cloud DNS-servers        | Vereist voor het doorsturen van DNS-lookup van *az.cloudsimple.io* naar Private Cloud DNS-servers van on-premises netwerk.       |
| 53 (UDP)   | Private Cloud DNS-servers        | On-premises DNS-servers          | Vereist voor het doorsturen van DNS opzoeken van on-premises domeinnamen van Private Cloud vCenter naar on-premises DNS-servers. |
| 80 (TCP)   | On-premises netwerk              | Private Cloud management netwerk | Vereist voor het omleiden van vCenter-URL van *http* naar *https*.                                                           |
| 443 (TCP)  | On-premises netwerk              | Private Cloud management netwerk | Vereist voor toegang tot vCenter en NSX-T manager van on-premises netwerk.                                             |
| 8000 (TCP) | On-premises netwerk              | Private Cloud management netwerk | Vereist voor vMotion van virtuele machines van on-premises naar Private Cloud.                                            |
| 8000 (TCP) | Private Cloud management netwerk | On-premises netwerk              | Vereist voor vMotion van virtuele machines van Private Cloud naar on-premises.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Poorten die nodig zijn voor het gebruik van on-premises active directory als identiteitsbron

Als u on-premises active directory wilt configureren als een identiteitsbron in Private Cloud vCenter, moeten poorten die in de tabel zijn gedefinieerd, worden geopend.  Zie [Azure AD gebruiken als identiteitsprovider voor vCenter op CloudSimple Private Cloud](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-ad/) voor configuratiestappen.

| Poort         | Bron                           | Doel                                         | Doel                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Private Cloud DNS-servers        | On-premises DNS-servers                             | Vereist voor het doorsturen van DNS opzoeken van on-premises active directory domeinnamen van Private Cloud vCenter naar on-premises DNS-servers.          |
| 389 (TCP/UDP) | Private Cloud management netwerk | On-premises active directory-domeincontrollers     | Vereist voor LDAP-communicatie van Private Cloud vCenter-server naar active directory-domeincontrollers voor gebruikersverificatie.                |
| 636 (TCP)     | Private Cloud management netwerk | On-premises active directory-domeincontrollers     | Vereist voor veilige LDAP-communicatie (LDAPS) van Private Cloud vCenter-server naar active directory-domeincontrollers voor gebruikersverificatie. |
| 3268 (TCP)    | Private Cloud management netwerk | On-premises active directory global catalog servers | Vereist voor LDAP-communicatie in een implementatie van een multi-domeincontroller.                                                                        |
| 3269 (TCP)    | Private Cloud management netwerk | On-premises active directory global catalog servers | Vereist voor LDAPS-communicatie in een implementatie van een multi-domeincontroller.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Gemeenschappelijke poorten die nodig zijn voor toegang tot virtuele werkbelastingmachines

Voor virtuele workloads die op Private Cloud worden uitgevoerd, moeten poorten worden geopend op uw on-premises firewall.  Onderstaande tabel toont enkele van de vereiste gemeenschappelijke poorten en hun doel.  Raadpleeg voor toepassingspecifieke poortvereisten de toepassingsdocumentatie.

| Poort         | Bron                         | Doel                          | Doel                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | On-premises netwerk            | Private Cloud-workloadnetwerk       | Beveiligde shell toegang tot Linux virtuele machines die draaien op Private Cloud.              |
| 3389 (TCP)    | On-premises netwerk            | Private Cloud-workloadnetwerk       | Extern bureaublad naar virtuele windows-machines die worden uitgevoerd op Private Cloud.                 |
| 80 (TCP)      | On-premises netwerk            | Private Cloud-workloadnetwerk       | Krijg toegang tot webservers die zijn geïmplementeerd op virtuele machines die op Private Cloud worden uitgevoerd.        |
| 443 (TCP)     | On-premises netwerk            | Private Cloud-workloadnetwerk       | Krijg toegang tot beveiligde webservers die worden geïmplementeerd op virtuele machines die op Private Cloud worden uitgevoerd. |
| 389 (TCP/UDP) | Private Cloud-workloadnetwerk | On-premises active directory-netwerk | Sluit virtuele machines van Windows-workload aan met on-premises active directory-domein.       |
| 53 (UDP)      | Private Cloud-workloadnetwerk | On-premises netwerk                  | DNS-servicetoegang voor virtuele workloads voor on-premises DNS-servers.         |

## <a name="next-steps"></a>Volgende stappen

* [VLAN's en subnetten maken en beheren](https://docs.microsoft.com/azure/vmware-cloudsimple/create-vlan-subnet/)
* [Verbinding maken met on-premises netwerk via Azure ExpressRoute](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-connection/)
* [Site-to-Site VPN instellen van on-premises](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway/)
