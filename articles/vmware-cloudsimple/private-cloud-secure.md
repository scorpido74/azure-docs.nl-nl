---
title: Azure VMware-oplossingen (AVS)-Secure AVS Privécloud
description: Hierin wordt beschreven hoe u de Privécloud-persoonlijke Cloud (Azure VMware Solutions) kunt beveiligen
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b053f1493c2380153711176a4748ebf90b479a6c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020077"
---
# <a name="how-to-secure-your-avs-private-cloud-environment"></a>Uw persoonlijke cloud omgeving voor AVS beveiligen

Definieer op rollen gebaseerd toegangs beheer (RBAC) voor de AVS-service, de AVS-Portal en de automatische AVS-Cloud vanuit Azure. Gebruikers, groepen en rollen voor toegang tot de vCenter-privécloud van AVS worden opgegeven met VMware SSO. 

## <a name="rbac-for-avs-service"></a>RBAC voor AVS-service

Voor het maken van de AVS-service is de rol **Owner** of **Inzender** vereist voor het Azure-abonnement. Standaard kunnen alle eigen aren en mede werkers een AVS-service maken en toegang krijgen tot de AVS-portal voor het maken en beheren van automatische AVS-Clouds. Per regio kan slechts één AVS-service worden gemaakt. Volg de onderstaande procedure om de toegang tot specifieke beheerders te beperken.

1. Een AVS-service maken in een nieuwe **resource groep** op Azure Portal
2. Geef RBAC op voor de resource groep.
3. Knoop punten kopen en dezelfde resource groep gebruiken als AVS-service

Alleen de gebruikers die de rechten van de rol van **eigenaar** of **Inzender** hebben voor de resource groep, zien de AVS-service en starten de AVS-Portal.

Zie [Wat is op rollen gebaseerd toegangs beheer (RBAC) voor Azure-resources](../role-based-access-control/overview.md)voor meer informatie over RBAC.

## <a name="rbac-for-avs-private-cloud-vcenter"></a>RBAC voor AVS Private Cloud vCenter

Er wordt een standaard gebruikers `CloudOwner@AVS.local` gemaakt in het vCenter-SSO-domein wanneer er een AVS-privécloud wordt gemaakt. CloudOwner-gebruiker heeft bevoegdheden voor het beheren van vCenter. Aanvullende identiteits bronnen worden toegevoegd aan de vCenter-SSO om toegang te verlenen aan verschillende gebruikers. Vooraf gedefinieerde rollen en groepen worden ingesteld op de vCenter die kan worden gebruikt om extra gebruikers toe te voegen.

### <a name="add-new-users-to-vcenter"></a>Nieuwe gebruikers toevoegen aan vCenter

1. [Bevoegdheden](escalate-private-cloud-privileges.md) voor **CloudOwner@AVS.local** gebruiker in de Privécloud van de AVS escaleren.
2. Aanmelden bij vCenter met **CloudOwner@AVS.local**
3. [Gebruikers met eenmalige aanmelding via VCenter toevoegen](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Gebruikers toevoegen aan een [vCenter-groep voor eenmalige aanmelding](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Voor meer informatie over vooraf gedefinieerde rollen en groepen raadpleegt u het artikel over het [machtigings model voor de privécloud in de cloud van VMware vCenter](learn-private-cloud-permissions.md) .

### <a name="add-new-identity-sources"></a>Nieuwe identiteits bronnen toevoegen

U kunt aanvullende id-providers voor het vCenter-SSO-domein toevoegen aan de Privécloud van uw AVS. Id-providers bieden SSO-groepen voor verificatie en vCenter verificatie voor gebruikers.

* [Gebruik Active Directory als een id-provider](set-vcenter-identity.md) op AVS Private Cloud vCenter.
* [Azure AD als een id-provider gebruiken](azure-ad.md) op de AVS Private Cloud vCenter

1. [Bevoegdheden](escalate-private-cloud-privileges.md) voor **CloudOwner@AVS.local** gebruiker in de Privécloud van de AVS escaleren.
2. Aanmelden bij vCenter met **CloudOwner@AVS.local**
3. Gebruikers van de ID-provider toevoegen aan een [vCenter-groep voor eenmalige aanmelding](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-avs-private-cloud-environment"></a>Beveiligd netwerk op uw AVS-Privécloud

De netwerk beveiliging van de automatische AVS-cloud omgeving wordt beheerd door netwerk toegang te beveiligen en netwerk verkeer tussen bronnen te beheren.

### <a name="access-to-avs-private-cloud-resources"></a>Toegang tot de cloud-resources van de automatische AVS

De toegang tot de vCenter-en bronnen van de AVS-privécloud is via een beveiligde netwerk verbinding:

* **[ExpressRoute-verbinding](on-premises-connection.md)** . ExpressRoute biedt een veilige verbinding met lage latentie met hoge band breedte vanuit uw on-premises omgeving. Met de verbinding kunnen uw on-premises Services, netwerken en gebruikers toegang krijgen tot uw AVS-persoonlijke Cloud-vCenter.
* **[Site-naar-site VPN-gateway](vpn-gateway.md)** . Site-naar-site-VPN geeft vanuit on-premises toegang tot uw persoonlijke Cloud bronnen via een beveiligde tunnel. U geeft aan welke on-premises netwerken netwerk verkeer naar de Privécloud van uw AVS kunnen verzenden en ontvangen.
* **[Punt-naar-site-VPN-gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Gebruik een punt-naar-site-VPN-verbinding voor snelle externe toegang tot uw AVS-persoonlijke Cloud-vCenter.

### <a name="control-network-traffic-in-avs-private-cloud"></a>Netwerk verkeer in de automatische AVS-Cloud beheren

Firewall tabellen en-regels bepalen netwerk verkeer in de Privécloud. In de tabel firewall kunt u het netwerk verkeer tussen een bron netwerk of IP-adres en een doelnet of IP-adres beheren op basis van de combi natie van regels die in de tabel zijn gedefinieerd.

1. Een [firewall tabel](firewall.md#add-a-new-firewall-table)maken.
2. [Regels toevoegen](firewall.md#create-a-firewall-rule) aan de firewall tabel.
3. [Een firewall tabel koppelen aan een VLAN/subnet] (firewall. MD # attach-vlanssubnet.
