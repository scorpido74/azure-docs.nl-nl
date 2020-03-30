---
title: Azure VMware Solutions by CloudSimple - Secure Private Cloud
description: Beschrijft hoe u Azure VMware-oplossingen beveiligen door CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565975"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Uw Private Cloud-omgeving beveiligen

Definieer rbac (role-based access control) voor CloudSimple Service, CloudSimple-portal en Private Cloud vanuit Azure.  Gebruikers, groepen en rollen voor toegang tot vCenter of Private Cloud worden opgegeven met VMware SSO.  

## <a name="rbac-for-cloudsimple-service"></a>RBAC voor CloudSimple-service

Voor het maken van cloudsimple-service is de rol **van eigenaar** of **inzender** vereist voor het Azure-abonnement.  Standaard kunnen alle eigenaren en bijdragers een CloudSimple-service maken en toegang krijgen tot CloudSimple-portal voor het maken en beheren van Private Clouds.  Per regio kan slechts één CloudSimple-service worden gemaakt.  Volg de onderstaande procedure om de toegang tot specifieke beheerders te beperken.

1. Een CloudSimple-service maken in een nieuwe **brongroep** op Azure-portal
2. Geef RBAC op voor de resourcegroep.
3. Knooppunten kopen en dezelfde brongroep gebruiken als cloudsimple-service

Alleen de gebruikers die **eigenaar-** of **inzenderrechten** hebben in de resourcegroep, zien de CloudSimple-service en starten cloudsimple-portal.

Zie [RBAC (Role-based access control) voor Azure-resources voor](../role-based-access-control/overview.md)meer informatie over RBAC.

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC voor Private Cloud vCenter

Er wordt `CloudOwner@cloudsimple.local` een standaardgebruiker gemaakt in het vCenter SSO-domein wanneer een Private Cloud wordt gemaakt.  CloudOwner-gebruiker heeft bevoegdheden voor het beheren van vCenter. Extra identiteitsbronnen worden toegevoegd aan de vCenter SSO voor het geven van toegang tot verschillende gebruikers.  Vooraf gedefinieerde rollen en groepen worden ingesteld op het vCenter dat kan worden gebruikt om extra gebruikers toe te voegen.

### <a name="add-new-users-to-vcenter"></a>Nieuwe gebruikers toevoegen aan vCenter

1. [De bevoegdheden](escalate-private-cloud-privileges.md) voor **CloudOwner\@cloudsimple.local** user op de Private Cloud escaleren.
2. Meld u aan bij vCenter met **CloudOwner\@cloudsimple.local**
3. [VCenter-gebruikers toevoegen aan één aanmelding](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Gebruikers toevoegen aan [vCenter-groepen voor eenmalige aanmelding](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Zie [CloudSimple Private Cloud-machtigingsmodel van vMware vCenter-artikel](learn-private-cloud-permissions.md) voor meer informatie over vooraf gedefinieerde rollen en groepen.

### <a name="add-new-identity-sources"></a>Nieuwe identiteitsbronnen toevoegen

U extra identiteitsproviders toevoegen voor vCenter SSO-domein van uw Private Cloud.  Identiteitsproviders bieden verificatie en vCenter SSO-groepen bieden autorisatie voor gebruikers.

* [Gebruik Active Directory als identiteitsprovider](set-vcenter-identity.md) in Private Cloud vCenter.
* [Azure AD gebruiken als identiteitsprovider](azure-ad.md) op Private Cloud vCenter

1. [De bevoegdheden](escalate-private-cloud-privileges.md) voor **CloudOwner\@cloudsimple.local** user op de Private Cloud escaleren.
2. Meld u aan bij vCenter met **CloudOwner\@cloudsimple.local**
3. Voeg gebruikers van de identiteitsprovider toe aan [vCenter-groepen voor één aanmelding](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Veilig netwerk op uw Private Cloud-omgeving

De netwerkbeveiliging van de Private Cloud-omgeving wordt gecontroleerd door netwerktoegang te beveiligen en het netwerkverkeer tussen resources te beheren.

### <a name="access-to-private-cloud-resources"></a>Toegang tot Private Cloud-bronnen

Private Cloud vCenter en resources toegang is via een beveiligde netwerkverbinding:

* **[ExpressRoute-verbinding](on-premises-connection.md)**. ExpressRoute biedt een veilige verbinding met hoge bandbreedte met een lage latentie vanuit uw on-premises omgeving.  Met de verbinding kunnen uw on-premises services, netwerken en gebruikers toegang krijgen tot uw Private Cloud vCenter.
* **[Site-to-Site VPN-gateway](vpn-gateway.md)**. Site-to-Site VPN geeft toegang tot uw Private Cloud-bronnen via een beveiligde tunnel.  U geeft aan welke on-premises netwerken netwerkverkeer naar uw Private Cloud kunnen verzenden en ontvangen.
* **[Point-to-Site VPN-gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. Gebruik Point-to-Site VPN-verbinding voor snelle externe toegang tot uw Private Cloud vCenter.

### <a name="control-network-traffic-in-private-cloud"></a>Netwerkverkeer beheren in Private Cloud

Firewalltabellen en regels regelen het netwerkverkeer in de Private Cloud.  Met de firewalltabel u het netwerkverkeer tussen een bronnetwerk of IP-adres en een doelnetwerk of IP-adres beheren op basis van de combinatie van regels die in de tabel zijn gedefinieerd.

1. Maak een [firewalltabel.](firewall.md#add-a-new-firewall-table)
2. [Regels toevoegen](firewall.md#create-a-firewall-rule) aan de firewalltabel.
3. [Bevestig een firewalltabel aan een VLAN/subnet](firewall.md#attach-vlans-subnet).
