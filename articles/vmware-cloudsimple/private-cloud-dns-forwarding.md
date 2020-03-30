---
title: Azure VMware-oplossing - DNS-forwarding van private cloud naar on-premises
description: Beschrijft hoe u uw CloudSimple Private Cloud DNS-server in staat stelt om on-premises bronnen vooruit te kijken
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961125"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>CloudSimple Private Cloud DNS-servers inschakelen om DNS-lookup van on-premises resources door te sturen naar uw DNS-servers

Private Cloud DNS-servers kunnen DNS-lookup voor on-premises bronnen doorsturen naar uw DNS-servers.  Door de lookup in te schakelen, kunnen Private Cloud vSphere-componenten alle services opzoeken die in uw on-premises omgeving worden uitgevoerd en met hen communiceren met volledig gekwalificeerde domeinnamen (FQDN).

## <a name="scenarios"></a>Scenario's 

Door DNS-lookup door sturen voor uw on-premises DNS-server u uw Private Cloud gebruiken voor de volgende scenario's:

* Gebruik Private Cloud als een noodherstel-installatie voor uw on-premises VMware-oplossing
* On-premises Active Directory gebruiken als identiteitsbron voor uw Private Cloud vSphere
* HCX gebruiken voor het migreren van virtuele machines van on-premises naar Private Cloud

## <a name="before-you-begin"></a>Voordat u begint

Een netwerkverbinding moet aanwezig zijn vanuit uw Private Cloud-netwerk met uw on-premises netwerk om DNS door te sturen naar het werk.  U een netwerkverbinding instellen met:

* [Maak verbinding van on-premises naar CloudSimple via ExpressRoute](on-premises-connection.md)
* [Een Site-to-Site VPN-gateway instellen](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

Firewallpoorten moeten op deze verbinding worden geopend om DNS door te sturen naar het werk.  Gebruikte poorten zijn TCP-poort 53 of UDP-poort 53.

> [!NOTE]
> Als u Site-to-Site VPN gebruikt, moet uw on-premises DNS-serversubnet worden toegevoegd als onderdeel van on-premises voorvoegsels.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>DNS-forwarding aanvragen van Private Cloud naar on-premises

Als u DNS-forwarding van Private Cloud naar on-premises wilt inschakelen, dient u een [ondersteuningsverzoek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)in met de volgende informatie.

* Type probleem: **Technisch**
* Abonnement: **Abonnement waarbij CloudSimple-service wordt geïmplementeerd**
* Service: **VMware Solution by CloudSimple**
* Probleemtype: **Adviserend of Hoe kan ik...**
* Probleemsubtype: **Hulp nodig met NW**
* Geef de domeinnaam van uw on-premises domein op in het detailvenster.
* Geef de lijst op van uw on-premises DNS-servers waarnaar de lookup vanuit uw privécloud wordt doorgestuurd in het detailvenster.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over on-premises firewallconfiguratie](on-premises-firewall-configuration.md)
* [On-premises DNS-serverconfiguratie](on-premises-dns-setup.md)
