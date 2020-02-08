---
title: Toegang tot Azure VMware-oplossingen (AVS) vanuit on-premises
description: Toegang tot uw Azure VMware-oplossingen (AVS) vanaf uw on-premises netwerk via een firewall
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a4a9760b5c7a70c58a1afe1b14b781a35f2b9b18
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082964"
---
# <a name="accessing-your-avs-private-cloud-environment-and-applications-from-on-premises"></a>Toegang tot uw persoonlijke cloud omgeving en-toepassingen van uw AVS vanuit on-premises

Een verbinding kan worden ingesteld vanuit een on-premises netwerk om te AVS met Azure ExpressRoute of site-naar-site-VPN. Toegang krijgen tot uw AVS-privécloud en alle werk belastingen die u uitvoert op de automatische AVS-Cloud met behulp van de verbinding. U kunt bepalen welke poorten op de verbinding worden geopend met behulp van een firewall in uw on-premises netwerk. In dit artikel worden enkele van de typische poort vereisten voor toepassingen besproken. Raadpleeg de documentatie van de toepassing voor de poort vereisten voor andere toepassingen.

## <a name="ports-required-for-accessing-vcenter"></a>Poorten die nodig zijn voor toegang tot vCenter

Om toegang te krijgen tot uw AVS-NSX en-T-beheer, moeten de poorten die in de onderstaande tabel zijn gedefinieerd, worden geopend op de on-premises firewall. 

| Poort       | Bron                           | Doel                      | Doel                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | On-premises DNS-servers          | DNS-servers voor de Privécloud in de Cloud        | Vereist voor het door sturen van de DNS-zoek opdracht van *AZ.cloudsimple.io* naar automatische AVS-DNS-servers in de Cloud vanuit een on-premises netwerk.     |
| 53 (UDP)   | DNS-servers voor de Privécloud in de Cloud        | On-premises DNS-servers          | Vereist voor het door sturen van DNS-zoek acties naar on-premises domein namen van AVS Private Cloud vCenter to on-premises DNS-servers. |
| 80 (TCP)   | On-premises netwerk              | Beheer netwerk voor een privé-cloud van AVS | Vereist voor het omleiden van de vCenter *-* URL van http naar *https*.                                                         |
| 443 (TCP)  | On-premises netwerk              | Beheer netwerk voor een privé-cloud van AVS | Vereist voor toegang tot vCenter en NSX-T-beheer vanuit een on-premises netwerk.                                           |
| 8000 (TCP) | On-premises netwerk              | Beheer netwerk voor een privé-cloud van AVS | Vereist voor vMotion van virtuele machines van on-premises tot de AVS-Privécloud.                                          |
| 8000 (TCP) | Beheer netwerk voor een privé-cloud van AVS | On-premises netwerk              | Vereist voor vMotion van virtuele machines van de Privécloud naar on-premises.                                          |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Poorten die vereist zijn voor het gebruik van on-premises Active Directory als een identiteits bron

Voor het configureren van een on-premises Active Directory als een identiteits bron op de AVS-Privécloud, moeten de poorten die in de tabel zijn gedefinieerd, worden geopend. Zie [Azure AD gebruiken als een id-provider voor vCenter in de privécloud](https://docs.azure.cloudsimple.com/azure-ad/) AVS voor configuratie stappen.

| Poort         | Bron                           | Doel                                         | Doel                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | DNS-servers voor de Privécloud in de Cloud        | On-premises DNS-servers                             | Vereist voor het door sturen van DNS-zoek acties van on-premises Active Directory-domein namen van AVS Private Cloud vCenter naar on-premises DNS-servers.        |
| 389 (TCP/UDP) | Beheer netwerk voor een privé-cloud van AVS | On-premises Active Directory-domein controllers     | Vereist voor LDAP-communicatie van AVS Private Cloud vCenter Server naar Active Directory-domein controllers voor gebruikers verificatie.              |
| 636 (TCP)     | Beheer netwerk voor een privé-cloud van AVS | On-premises Active Directory-domein controllers     | Vereist voor secure LDAP-communicatie (LDAPS) van AVS Private Cloud vCenter Server naar Active Directory-domein controllers voor gebruikers verificatie. |
| 3268 (TCP)    | Beheer netwerk voor een privé-cloud van AVS | On-premises Active Directory Global Catalog-servers | Vereist voor LDAP-communicatie in een implementatie van meerdere domein controllers.                                                                      |
| 3269 (TCP)    | Beheer netwerk voor een privé-cloud van AVS | On-premises Active Directory Global Catalog-servers | Vereist voor LDAPS-communicatie in een implementatie van meerdere domein controllers.                                                                     |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Algemene poorten die vereist zijn voor toegang tot virtuele workload-machines

Voor toegang tot virtuele machines van workload die worden uitgevoerd op de AVS-Privécloud, moeten poorten worden geopend op de on-premises firewall. In de onderstaande tabel ziet u een aantal algemene poorten die vereist zijn en hun doel. Raadpleeg de documentatie van de toepassing voor alle toepassingsspecifieke poort vereisten.

| Poort         | Bron                         | Doel                          | Doel                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | On-premises netwerk            | Het werkbelasting netwerk van de AVS-Cloud       | Beveiligde shell toegang tot virtuele Linux-machines die worden uitgevoerd op de cloud van de AVS.            |
| 3389 (TCP)    | On-premises netwerk            | Het werkbelasting netwerk van de AVS-Cloud       | Extern bureau blad naar virtuele Windows-machines die worden uitgevoerd op de cloud van de AVS.               |
| 80 (TCP)      | On-premises netwerk            | Het werkbelasting netwerk van de AVS-Cloud       | U hebt toegang tot alle webservers die zijn geïmplementeerd op virtuele machines waarop de Privécloud wordt uitgevoerd.      |
| 443 (TCP)     | On-premises netwerk            | Het werkbelasting netwerk van de AVS-Cloud       | Toegang krijgen tot beveiligde webservers die zijn geïmplementeerd op virtuele machines waarop de Privécloud wordt uitgevoerd. |
| 389 (TCP/UDP) | Het werkbelasting netwerk van de AVS-Cloud | On-premises Active Directory-netwerk | Virtuele Windows-werk belasting machines toevoegen aan een on-premises Active Directory-domein.     |
| 53 (UDP)      | Het werkbelasting netwerk van de AVS-Cloud | On-premises netwerk                  | Toegang tot de DNS-service voor virtuele werkbelasting machines naar on-premises DNS-servers.       |

## <a name="next-steps"></a>Volgende stappen

* [VLAN'S en subnetten maken en beheren](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Verbinding maken met een on-premises netwerk met behulp van Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Site-naar-site-VPN van on-premises instellen](https://docs.azure.cloudsimple.com/vpn-gateway/)
