---
title: Azure VMware-oplossing door CloudSimple - Gebruik Private Cloud-site om een virtuele desktopinfrastructuur te hosten met VMware Horizon
description: Beschrijft hoe u uw CloudSimple Private Cloud-site gebruiken om een virtuele desktopinfrastructuur te hosten met VMware Horizon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025245"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>CloudSimple Private Cloud-site gebruiken om een virtuele desktopinfrastructuur te hosten met VMware Horizon

U uw CloudSimple Private Cloud-site gebruiken om een virtuele desktopinfrastructuur (VDI) te hosten met VMware Horizon 7.x. De volgende figuur toont de logische oplossingsarchitectuur voor de VDI.

![Horizon-implementatie](media/horizon-deployment.png)

Met deze oplossing hebt u volledige controle over Horizon View Manager en App Volume. De vertrouwde gebruikersinterface-, API- en CLI-interfaces maken het gebruik van uw bestaande scripts en hulpprogramma's mogelijk.

De CloudSimple-oplossing vereist dat u het volgende doet:

* Installeer, configureer en beheer VMware Horizon 7.x in uw Private Cloud.
* Geef uw eigen Horizon-licenties op.

## <a name="deploy-the-solution"></a>De oplossing implementeren

In de volgende secties wordt beschreven hoe u een VDI-oplossing implementeert met Horizon in uw Private Cloud.

1. [Controleren of VMware-productversies compatibel zijn](#verify-that-vmware-product-versions-are-compatible)
2. [De grootte van uw bureaubladomgeving schatten](#estimate-the-size-of-your-desktop-environment)
3. [Een private cloud maken voor uw omgeving](#create-a-private-cloud-for-your-environment)
4. [VMware Horizon installeren in uw Private Cloud](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Controleren of VMware-productversies compatibel zijn

* Controleer of uw huidige en geplande versies van Horizon, App-volumes, Unified Access Gateway en User Environment Manager compatibel zijn met elkaar en met vCenter en PSC in de Private Cloud. Zie [VMware Compatibility Matrix voor Horizon 7.5 voor](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=)compatibiliteitsinformatie.
* Als u de huidige versies van vCenter en PSC in uw private cloud wilt vinden, gaat u naar **Resources** in de [CloudSimple-portal,](access-cloudsimple-portal.md)selecteert u uw Private Cloud en klikt u op het tabblad **vSphere Management Network.**

![vCenter- en PSC-versies](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>De grootte van uw bureaubladomgeving schatten

* Controleer of uw geïdentificeerde configuratie binnen de operationele limieten van VMware valt.
* Schat de resources die nodig zijn voor al uw desktops en uw Horizon-beheercomponenten.

### <a name="create-a-private-cloud-for-your-environment"></a>Een private cloud maken voor uw omgeving

1. Maak een Private Cloud vanuit de CloudSimple-portal door de instructies in [Een Private Cloud-omgeving configureren](quickstart-create-private-cloud.md)te volgen.  CloudSimple maakt een standaard vCenter-gebruiker met de naam 'cloudowner' in elke nieuw gemaakte Private Cloud. Zie Het model Voor machtigingen voor privécloud leren voor meer informatie over het standaard-privécloud-gebruikers- en [machtigingsmodel](learn-private-cloud-permissions.md).
2. Maak een VLAN in uw Private Cloud voor het Horizon-beheervlak en wijs deze toe aan een subnet CIDR. Zie [VLAN's/subnetten maken en beheren voor](create-vlan-subnet.md)instructies. Dit is het netwerk waar alle oplossingscomponenten (Unified Access Gateway, Connection Server, App Volume Server en User Environment Manager-servers) worden geïnstalleerd.
3. Bepaal of u een externe identiteitsprovider wilt gebruiken met uw Private Cloud vCenter. Zo ja, kies dan een van de volgende opties:
    * Gebruik uw on-premises Active Directory als externe identiteitsprovider. Zie [vCenter-identiteitsbronnen](set-vcenter-identity.md)voor instructies .
    * Stel een Active Directory-server in de Private Cloud in Horizon-beheervlak VLAN in om te gebruiken als uw externe identiteitsprovider. Zie [vCenter-identiteitsbronnen](set-vcenter-identity.md)voor instructies .
    * Stel een DHCP- en DNS-server in horizonbeheervlak VLAN in de Private Cloud. Zie [DNS- en DHCP-toepassingen en -workloads instellen in uw CloudSimple Private Cloud](dns-dhcp-setup.md)voor instructies.
4. DNS-forwarding configureren op de DNS-server die is geïnstalleerd in de Private Cloud. Zie Een [voorwaardelijke doorstuurer maken](on-premises-dns-setup.md#create-a-conditional-forwarder)voor instructies.

### <a name="install-vmware-horizon-in-your-private-cloud"></a>VMware Horizon installeren in uw Private Cloud

In het volgende implementatiediagram wordt een Horizon-oplossing weergegeven die is geïmplementeerd in een private cloud. Unified Access Gateway, AD/DC, View en App Volume Server zijn geïnstalleerd in door de gebruiker gemaakte VLAN 234. Unified Access Gateway heeft een toegewezen openbaar IP-adres dat bereikbaar is vanaf internet. Horizon-desktoppoolVM's worden geïmplementeerd in VLAN 235 om extra isolatie en beveiliging te bieden.

![Horizon-implementatie in de Private Cloud](media/horizon-private-cloud.png)

In de volgende secties worden de instructies beschreven om een implementatie in te stellen die vergelijkbaar is met de implementatie die in de figuur wordt weergegeven. Controleer voordat u begint of u het volgende hebt:

* Een Private Cloud die is gemaakt met behulp van de CloudSimple-portal met voldoende capaciteit om uw bureaubladpools uit te voeren.
* Voldoende bandbreedte tussen uw on-premises omgeving en de Private Cloud-omgeving om het netwerkverkeer voor uw desktops te ondersteunen.
* Een Site-to-Site VPN-tunnel die is ingesteld tussen uw on-premises datacenter en de Private Cloud.
* IP-bereikbaarheid van subnetten voor eindgebruikers in uw on-premises omgeving tot de CloudSimple Private Cloud-subnetten.
* AD/DHCP/DNS geïnstalleerd voor uw Private Cloud.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple-portal: maak een speciaal VLAN/subnet voor bureaubladgroepen

Maak een VLAN voor de Horizon-bureaubladgroepen en wijs deze toe aan een subnet CIDR. Zie [VLAN's/subnetten maken en beheren voor](create-vlan-subnet.md)instructies. Dit is het netwerk waar alle virtuele desktopmachines draaien.

Volg standaard aanbevolen procedures voor beveiliging om uw Horizon-implementatie te beveiligen:

* Alleen extern RDP-verkeer / SSH-verkeer toestaan aan uw desktop-VM's.
* Alleen beheerverkeer toestaan tussen Horizon-beheervlak VLAN en desktoppool VLAN.
* Sta alleen beheerverkeer toe vanuit het on-premises netwerk.

U deze aanbevolen procedures afdwingen door [firewallregels](firewall.md) te configureren vanuit de CloudSimple-portal.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>CloudSimple-portal: firewallregels configureren om horizonbeheervlak te beveiligen

Stel de volgende regels in in de CloudSimple-portal. Zie [Firewalltabellen en -regels instellen](firewall.md)voor instructies.

1. Configureer firewallregels in de CloudSimple N-S-firewall om communicatie tussen on-premises subnetten en Horizon-beheer VLAN mogelijk te maken, zodat alleen de netwerkpoorten in de [VMware-documenthorizonpoortlijst](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) zijn toegestaan.

2. Maak E-W-firewallregels tussen de VLAN voor Horizonbeheer en de VLAN-bureaubladgroep in de Private Cloud.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>CloudSimple-portal: een openbaar IP-adres maken voor Unified Access Gateway

Maak een openbaar IP-adres voor het Unified Access Gateway-toestel om desktopclientverbindingen vanaf internet mogelijk te maken. Zie Openbare [IP-adressen toewijzen](public-ips.md)voor instructies .

Wanneer de installatie is voltooid, wordt het openbare IP-adres toegewezen en vermeld op de pagina Openbare IP-instellingen.

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple-portal: bevoegdheden escaleren

De standaardgebruiker 'cloudowner' heeft onvoldoende bevoegdheden in het VCenter voor Private Cloud om Horizon te installeren, dus de vCenter-bevoegdheden van de gebruiker moeten worden geëscaleerd. Zie [Bevoegdheden escaleren voor](escalate-private-cloud-privileges.md)meer informatie .

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter-gebruikersinterface: een gebruiker maken in private cloud voor horizon-installatie

1. Meld u aan bij vCenter met behulp van de gebruikersreferenties van de 'cloudowner'.
2. Maak een nieuwe gebruiker, 'horizon-soln-admin', in vCenter en voeg de gebruiker toe aan de beheerdersgroep in vCenter.
3. Meld u af bij vCenter als de 'cloudowner' gebruiker en meld u aan als de 'horizon-soln-admin' gebruiker.

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter-gebruikersinterface: VMware Horizon installeren

Zoals vermeld in de eerdere sectie logische architectuur, heeft Horizon-oplossing de volgende componenten:

* VMware Horizon-weergave
* VMware Unified Access Gateway
* VMware App Volume Manager
* VMware User Environment Manager

Installeer de onderdelen als volgt:

1. Installeer en configureer Unified Access Gateway door de instructies in het VMware-document [Te implementeren en te configureren vmware Unified Access Gateway](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Installeer Horizonweergave in de private cloud door de instructies in [de installatiehandleiding bekijken](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)te volgen.

3. Installeer App Volume Manager door de instructies in [VMware-appvolumes installeren en configureren](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)te volgen.

4. Installeer en configureer Gebruikersbeheer door de instructies in [Over het installeren en configureren van VMware User Environment Manager te](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)volgen.

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Een ondersteuningsverzoek indienen om VMware Horizon-voorverpakte app-volumes te uploaden

Als onderdeel van het installatieproces gebruikt App Volume Manager voorverpakte volumes om app-stacks en beschrijfbare volumes in te richten. Deze volumes dienen als sjablonen voor app-stacks en beschrijfbare volumes.

Voor het uploaden van de volumes naar de Private Cloud datastore is het ESXi-hoofdwachtwoord vereist. Voor bijstand dient u een [ondersteuningsverzoek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)in. Voeg de AppVolumes-installatiebundel toe, zodat cloudsimple-ondersteuningsmedewerkers de sjablonen kunnen uploaden naar uw Private Cloud-omgeving.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple-portal: bevoegdheden de-escaleren

U nu de bevoegdheden van de 'cloudowner'-gebruiker [de-escaleren.](escalate-private-cloud-privileges.md#de-escalate-privileges)

## <a name="ongoing-management-of-your-horizon-solution"></a>Doorlopend beheer van uw Horizon-oplossing

U hebt volledige controle over Horizon- en App Volume Manager-software in uw Private Cloud-omgeving en van u wordt verwacht dat u het noodzakelijke beheer van de softwarelevenscyclus uitvoert. Zorg ervoor dat nieuwe versies van software compatibel zijn met de Private Cloud vCenter en PSC voordat u Horizon of App Volume bijwerkt of upgradet.
