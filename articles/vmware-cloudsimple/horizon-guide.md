---
title: 'Azure VMware-oplossingen (AVS): een Privécloud-site gebruiken om een virtuele-bureaublad infrastructuur te hosten met behulp van VMware horizon'
description: Hierin wordt beschreven hoe u de persoonlijke cloud site van AVS kunt gebruiken om een virtuele-bureaublad infrastructuur te hosten met behulp van VMware horizon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f909ce297344e80ed6004631d5218d6a7fcd5085
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025245"
---
# <a name="use-avs-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Een Privécloud-site gebruiken om een virtuele-bureaublad infrastructuur te hosten met behulp van VMware horizon

U kunt de Privécloud-site van uw AVS gebruiken om een virtuele desktop infrastructuur (VDI) te hosten met behulp van VMware horizon 7. x. In de volgende afbeelding ziet u de logische oplossings architectuur voor de VDI.

![Horizon-implementatie](media/horizon-deployment.png)

Met deze oplossing hebt u volledige controle over horizon View Manager en het app-volume. De vertrouwde interface-, API-en CLI-interfaces maken gebruik van uw bestaande scripts en hulpprogram ma's mogelijk.

Voor de AVS-oplossing moet u het volgende doen:

* VMware-horizon-7. x in de Privécloud van uw AVS installeren, configureren en beheren.
* Geef uw eigen horizon-licenties op.

## <a name="deploy-the-solution"></a>De oplossing implementeren

In de volgende secties wordt beschreven hoe u een VDI-oplossing implementeert met behulp van de Privécloud van uw AVS.

1. [Controleren of de VMware-product versies compatibel zijn](#verify-that-vmware-product-versions-are-compatible)
2. [De grootte van uw desktop omgeving schatten](#estimate-the-size-of-your-desktop-environment)
3. [Een Privécloud maken voor uw omgeving](#create-an-avs-private-cloud-for-your-environment)
4. [VMware-horizon installeren in de Privécloud van uw AVS](#install-vmware-horizon-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Controleren of de VMware-product versies compatibel zijn

* Controleer of uw huidige en geplande versies van de horizon, app-volumes, Unified Access-Gateway en User Environment Manager compatibel zijn met elkaar en met vCenter en PSC in de cloud van de AVS. Zie voor compatibiliteits informatie [VMware-compatibiliteits matrix voor Horizon 7,5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Als u de huidige versies van vCenter en PSC in de Privécloud van uw Cloud wilt zien, gaat u naar **resources** in de [AVS-Portal](access-cloudsimple-portal.md), selecteert u de privécloud-Cloud en klikt u op het tabblad **vSphere-beheer netwerk** .

![vCenter-en PSC-versies](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>De grootte van uw desktop omgeving schatten

* Controleer of de geïdentificeerde configuratie binnen de VMware-operationele limieten valt.
* Een schatting maken van de resources die nodig zijn voor al uw Desk tops en uw onderdelen van de horizon-management.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>Een Privécloud maken voor uw omgeving

1. Maak een automatische AVS-Cloud vanuit de AVS-portal door de instructies in [Configure an een privécloud in de cloud omgeving](quickstart-create-private-cloud.md)te volgen. AVS maakt een standaard-vCenter-gebruiker met de naam ' cloudowner ' in elke nieuw gemaakte AVS-privécloud. Zie voor meer informatie over het standaard gebruikers-en-machtigings model van de automatische AVS, [meer informatie over het machtigings model voor de persoonlijke cloud van de AVS](learn-private-cloud-permissions.md).
2. Maak een VLAN in de Privécloud van uw AVS voor het Horizon beheer vlak en wijs hieraan een-CIDR toe. Zie [vlan's en subnetten maken en beheren](create-vlan-subnet.md)voor instructies. Dit is het netwerk waarin alle oplossings onderdelen (Unified Access Gateway, Connection server, app volume server en User Environment Manager-servers) worden geïnstalleerd.
3. Bepaal of u een externe ID-provider wilt gebruiken met uw AVS Privécloud-vCenter. Zo ja, kies een van de volgende opties:
    * Gebruik uw on-premises Active Directory als externe ID-provider. Zie voor instructies [vCenter-identiteits bronnen](set-vcenter-identity.md).
    * Stel een Active Directory-server in de cloud van de AVS in op horizon Management vlak VLAN om te gebruiken als uw externe ID-provider. Zie voor instructies [vCenter-identiteits bronnen](set-vcenter-identity.md).
    * Een DHCP-en DNS-server in horizon Management vlak VLAN instellen in de Privécloud van de AVS. Zie [DNS-en DHCP-toepassingen en-workloads instellen in de privécloud van uw AVS](dns-dhcp-setup.md)voor instructies.
4. Configureer DNS-door sturen op de DNS-server die is geïnstalleerd in de AVS-privécloud. Zie [een voorwaardelijke doorstuur server maken](on-premises-dns-setup.md#create-a-conditional-forwarder)voor instructies.

### <a name="install-vmware-horizon-in-your-avs-private-cloud"></a>VMware-horizon installeren in de Privécloud van uw AVS

In het volgende implementatie diagram ziet u een horizon-oplossing die is geïmplementeerd in een Privécloud in de Cloud. Unified Access Gateway, AD/DC, View en app volume server zijn geïnstalleerd in door de gebruiker gemaakte VLAN 234. De gateway voor Unified Access heeft een openbaar IP-adres toegewezen dat bereikbaar is vanaf internet. Virtuele machines in de VM-groep worden in VLAN 235 geïmplementeerd om extra isolatie en beveiliging te bieden.

![Horizon-implementatie in de AVS-Privécloud](media/horizon-private-cloud.png)

In de volgende secties vindt u een overzicht van de instructies voor het instellen van een implementatie zoals die in de afbeelding wordt weer gegeven. Controleer voordat u begint of u over het volgende beschikt:

* Een automatische AVS-Cloud die is gemaakt met de AVS-Portal met voldoende capaciteit voor het uitvoeren van uw Desktop groepen.
* Voldoende band breedte tussen uw on-premises omgeving en de cloud-omgeving van de AVS om het netwerk verkeer voor uw Desk tops te ondersteunen.
* Een site-naar-site-VPN-tunnel die is ingesteld tussen uw on-premises Data Center en de cloud van de AVS.
* IP-bereik baarheid van eindgebruikers subnetten in uw on-premises omgeving naar de AVS-subnetten van de Privécloud.
* AD/DHCP/DNS dat is geïnstalleerd voor de Privécloud van uw AVS.

#### <a name="avs-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>AVS-portal: een toegewezen VLAN/subnet maken voor desktop groepen

Maak een VLAN voor de horizon Desk groepen en wijs hieraan een-CIDR toe. Zie [vlan's en subnetten maken en beheren](create-vlan-subnet.md)voor instructies. Dit is het netwerk waarin alle virtuele machines van het bureau blad worden uitgevoerd.

Volg de aanbevolen standaard procedures voor beveiliging om uw horizon-implementatie te beveiligen:

* Alleen RDP-verkeer/SSH-verkeer van het bureau blad naar uw Desktop-Vm's toestaan.
* Sta alleen beheer verkeer tussen het VLAN en het VLAN van de Desktop groep toe.
* Alleen beheer verkeer van het on-premises netwerk toestaan.

U kunt deze aanbevolen procedures afdwingen door [firewall regels](firewall.md) te configureren via de AVS-Portal.

#### <a name="avs-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>AVS-portal: Firewall regels configureren voor beveiliging horizon beheer vlak

Stel de volgende regels in de AVS-Portal in. Zie [firewall tabellen en-regels instellen](firewall.md)voor instructies.

1. Configureer firewall regels in de AVS N-S firewall om communicatie mogelijk te maken tussen on-premises subnetten en horizon-Management-VLAN zodat alleen de netwerk poorten die worden vermeld in de lijst VMware-document [horizon Port](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) zijn toegestaan.

2. E-W firewall regels maken tussen de VLAN van de horizon-VLAN en de Desktop groep in de automatische AVS-Cloud.

#### <a name="avs-portal-create-a-public-ip-address-for-unified-access-gateway"></a>AVS-portal: een openbaar IP-adres voor een Unified Access-gateway maken

Maak een openbaar IP-adres voor het gateway apparaat voor Unified Access om desktop-client verbindingen van het internet in te scha kelen. Zie [Public IP-adressen toewijzen](public-ips.md)voor instructies.

Wanneer het installatie programma is voltooid, wordt het open bare IP-adres toegewezen en weer gegeven op de open bare IPs-pagina.

#### <a name="avs-portal-escalate-privileges"></a>AVS-portal: bevoegdheden escaleren

De standaard gebruiker ' cloudowner ' beschikt niet over voldoende bevoegdheden in de AVS-beheerder van de Privécloud om horizon taal te installeren. Daarom moeten de vCenter-bevoegdheden van de gebruiker worden geëscaleerd. Zie [bevoegdheden voor escalatie](escalate-private-cloud-privileges.md)voor meer informatie.

#### <a name="vcenter-ui-create-a-user-in-avs-private-cloud-for-horizon-installation"></a>vCenter-gebruikers interface: een gebruiker maken in AVS Privécloud voor de horizon-installatie

1. Meld u aan bij vCenter met de gebruikers referenties ' cloudowner '.
2. Maak een nieuwe gebruiker ' Horizon-soln-admin ' in vCenter en voeg de gebruiker toe aan de groep Administrators in vCenter.
3. Meld u af bij vCenter als de gebruiker ' cloudowner ' en meld u aan als de gebruiker ' Horizon-soln-admin '.

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter-gebruikers interface: VMware-horizon installeren

Zoals vermeld in de sectie eerdere logische architectuur, heeft horizon-oplossing de volgende onderdelen:

* VMware-horizon-weer gave
* VMware Unified Access-Gateway
* VMware-app volume manager
* Beheer van VMware-gebruikers omgeving

Installeer de onderdelen als volgt:

1. Installeer en configureer Unified Access Gateway door de instructies in het VMware-document te volgen die [VMware Unified Access-Gateway implementeren en configureren](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Installeer de weer gave horizon in de cloud van de AVS door de instructies in de [installatie handleiding voor weer gave](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)te volgen.

3. Installeer app volume manager door de instructies in [VMware-app-volumes installeren en configureren](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)te volgen.

4. Installeer en configureer User Environment Manager aan de hand van de instructies in [about vmware user Environment Manager installeren en configureren](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Een ondersteunings aanvraag indienen voor het uploaden van vooraf verpakte VMware horizon-app-volumes

Als onderdeel van het installatie proces maakt app volume manager gebruik van vooraf verpakte volumes om app-stacks en beschrijf bare volumes in te richten. Deze volumes fungeren als sjablonen voor app-stacks en beschrijf bare volumes.

Voor het uploaden van de volumes naar de AVS-gegevens opslag voor de privécloud is het ESXi-hoofd wachtwoord vereist. Voor hulp dient u een [ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)in. Koppel de AppVolumes Installer-bundel zodat het ondersteunings personeel van AVS de sjablonen kan uploaden naar de Privécloud van uw Cloud.

#### <a name="avs-portal-de-escalate-privileges"></a>AVS-portal: de bevoegdheden voor het deactiveren

U kunt nu de bevoegdheden van de gebruiker ' cloudowner ' [deactiveren](escalate-private-cloud-privileges.md#de-escalate-privileges) .

## <a name="ongoing-management-of-your-horizon-solution"></a>Voortdurend beheer van uw horizon-oplossing

U hebt volledige controle over de software van de Cloud-en app-volume manager in uw Privécloud en verwacht het benodigde beheer van de software levenscyclus uit te voeren. Zorg ervoor dat alle nieuwe versies van software compatibel zijn met de AVS-Privécloud en PSC voor het bijwerken of upgraden van een horizon-of app-volume.
