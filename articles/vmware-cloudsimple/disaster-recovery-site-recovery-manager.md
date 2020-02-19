---
title: 'Azure VMware-oplossingen (AVS): AVS-Privécloud instellen als een nood herstel site met behulp van VMware Site Recovery Manager'
description: Hierin wordt beschreven hoe u uw Privécloud als een nood herstel site instelt voor on-premises VMware-workloads
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 209eb6ed93ed12f97b116b648a36d14e09b822f7
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461178"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Een persoonlijke cloud van AVS instellen als een nood herstel doel met VMware Site Recovery Manager

U kunt uw automatische AVS-Cloud gebruiken als een site voor herstel na nood gevallen (DR) voor on-premises VMware-workloads.

De oplossing DR is gebaseerd op vSphere-replicatie en VMware Site Recovery Manager Er kan een soort gelijke benadering worden gevolgd om uw Privécloud in te scha kelen als een primaire site die wordt beveiligd door uw on-premises herstel site.

De AVS-oplossing:

* Elimineert de nood zaak om een Data Center in te stellen dat specifiek is voor DR.
* Met kunt u gebruikmaken van de Azure-locaties waar AVS wordt geïmplementeerd voor wereld wijde geografische tolerantie.
* Biedt u een optie om implementatie kosten en total cost of ownership voor het instellen van DR te verlagen.

Voor de AVS-oplossing moet u het volgende doen:

* In de Privécloud van uw AVS kunt u vSphere-replicatie en-beveiligings installatie installeren, configureren en beheren.
* Geef uw eigen licenties voor een website op als de AVS-Privécloud de beveiligde site is. U hebt geen extra-licenties voor het nieuwe-server gebruik voor de AVS-site nodig als deze wordt gebruikt als de herstel site.

Met deze oplossing hebt u volledige controle over vSphere-replicatie en-de-veel-------- De vertrouwde interface-, API-en CLI-interfaces maken gebruik van uw bestaande scripts en hulpprogram ma's mogelijk.

![Site Recovery Manager-implementatie](media/srm-deployment.png)

U kunt alle versies van vRA en de beveiligings-en-runtime versie gebruiken die compatibel zijn met uw persoonlijke Cloud-en on-premises-omgevingen. In de voor beelden in deze hand leiding wordt gebruikgemaakt van vRA 6,5 en 6,5. Deze versies zijn compatibel met vSphere 6,5, die wordt ondersteund door AVS.

## <a name="deploy-the-solution"></a>De oplossing implementeren

In de volgende secties wordt beschreven hoe u een DR-oplossing implementeert met behulp van de beveiligings-cloud van uw AVS.

1. [Controleren of de VMware-product versies compatibel zijn](#verify-that-vmware-product-versions-are-compatible)
2. [De grootte van uw DR-omgeving schatten](#estimate-the-size-of-your-dr-environment)
3. [Een Privécloud maken voor uw omgeving](#create-an-avs-private-cloud-for-your-environment)
4. [Automatische AVS-Cloud netwerken instellen voor de oplossing voor de beveiligings-en hand-out](#set-up-avs-private-cloud-networking-for-the-srm-solution)
5. [Stel een site-naar-site-VPN-verbinding in tussen uw on-premises netwerk en de Privécloud en open de vereiste poorten.](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports)
6. [Infrastructuur services instellen in de Privécloud van uw AVS](#set-up-infrastructure-services-in-your-avs-private-cloud)
7. [VSphere-replicatie apparaat installeren in uw on-premises omgeving](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Het vSphere-replicatie apparaat installeren in uw cloud omgeving voor Privécloud](#install-vsphere-replication-appliance-in-your-avs-private-cloud-environment)
9. [Een server van het (en) installeren in uw on-premises omgeving](#install-srm-server-in-your-on-premises-environment)
10. [Een server van de beveiligings-Cloud installeren in de Privécloud](#install-srm-server-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Controleren of de VMware-product versies compatibel zijn

De configuraties in deze hand leiding zijn onderhevig aan de volgende compatibiliteits vereisten:

* U moet dezelfde versie van de beveiligings-en/of-versies implementeren in de Privécloud van uw AVS en uw on-premises omgeving.
* Dezelfde versie van vSphere-replicatie moet worden geïmplementeerd in de Privécloud van uw AVS en uw on-premises omgeving.
* De versies van de platform Services-controller (PSC) in uw Privécloud (Private Cloud) en uw on-premises omgeving moeten compatibel zijn.
* De versies van vCenter in uw Privécloud en uw on-premises omgeving moeten compatibel zijn.
* De versies van de vSphere-versie en-replicatie moeten compatibel zijn met elkaar en met de versies van PSC en vCenter.

Ga naar de documentatie van [vmware site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) voor koppelingen naar relevante informatie over VMware-documentatie en-compatibiliteit.

Open de AVS-Portal om de versies van vCenter en PSC te vinden in de Privécloud van uw AVS. Ga naar **resources**, selecteer de privécloud van uw AVS en klik op het tabblad **VSphere-beheer netwerk** .

![de Privécloud-versies van de vCenter-& in de cloud van de AVS](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>De grootte van uw DR-omgeving schatten

1. Controleer of de geïdentificeerde on-premises configuratie binnen de ondersteunde limieten valt. De limieten voor de 6,5-eenheid worden beschreven in het Knowledge Base-artikel van VMware over de [operationele limieten voor site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110).
2. Zorg ervoor dat u voldoende netwerk bandbreedte hebt om te voldoen aan de grootte van de workload en de RPO-vereisten. Het VMware Knowledge Base-artikel over het berekenen van de [bandbreedte vereisten voor vSphere-replicatie](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) biedt richt lijnen voor bandbreedte limieten.
3. Gebruik het hulp programma AVS-grootte om de resources die nodig zijn op uw DR-site te ramen om uw on-premises omgeving te beveiligen.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>Een Privécloud maken voor uw omgeving

Maak een automatische AVS-Cloud vanuit de AVS-portal door de instructies te volgen en aanbevelingen voor de grootte in te [stellen in een automatische AVS-Cloud maken](create-private-cloud.md).

### <a name="set-up-avs-private-cloud-networking-for-the-srm-solution"></a>Automatische AVS-Cloud netwerken instellen voor de oplossing voor de beveiligings-en hand-out

Open de AVS-Portal om een automatische AVS-Cloud netwerk in te stellen voor de oplossing van de beveiligings-en hand-out.

Maak een VLAN voor het oplossings netwerk van de netwerkhardware en wijs hieraan een-CIDR toe. Zie [vlan's en subnetten maken en beheren](create-vlan-subnet.md)voor instructies.

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports"></a>Stel een site-naar-site-VPN-verbinding in tussen uw on-premises netwerk en de Privécloud en open de vereiste poorten.

Voor het instellen van een site-naar-site-verbinding tussen uw on-premises netwerk en de Privécloud van uw AVS. Zie [een VPN-verbinding configureren voor de privécloud van uw AVS](set-up-vpn.md)voor instructies.

### <a name="set-up-infrastructure-services-in-your-avs-private-cloud"></a>Infrastructuur services instellen in de Privécloud van uw AVS

Configureer infrastructuur services in de cloud van de AVS, zodat u uw workloads en hulpprogram ma's eenvoudig kunt beheren.

U kunt een externe ID-provider toevoegen, zoals wordt beschreven in [Azure AD gebruiken als een id-provider voor vCenter in de privécloud op](azure-ad.md) de automatische AVS-Cloud als u een van de volgende handelingen wilt uitvoeren:

* Identificeer gebruikers van uw on-premises Active Directory (AD) in de Privécloud van uw AVS.
* Stel een AD in de Privécloud van uw AVS in voor alle gebruikers.
* Gebruik Azure AD.

Als u IP-adres zoeken, IP-adres beheer en naamomzettingsservices wilt bieden voor uw workloads in de Privécloud, stelt u een DHCP-en DNS-server in zoals beschreven in [DNS-en DHCP-toepassingen en werk belastingen instellen in de privécloud van uw AVS](dns-dhcp-setup.md).

Het domein *. cloudsimple.io wordt gebruikt door Management-Vm's en-hosts in de Privécloud van uw AVS. Als u aanvragen voor dit domein wilt omzetten, configureert u het door sturen van DNS op de DNS-server zoals beschreven in [een voorwaardelijke forwarder maken](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>VSphere-replicatie apparaat installeren in uw on-premises omgeving

Installeer vSphere Replication Appliance (vRA) in uw on-premises omgeving door de VMware-documentatie te volgen. De installatie bestaat uit de volgende stappen op hoog niveau:

1. Bereid uw on-premises omgeving voor op de installatie van vRA.

2. Implementeer vRA in uw on-premises omgeving met behulp van de OVF in de VR-ISO van vmware.com. Voor vRA 6,5 bevat [deze VMware-blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) de relevante informatie.

3. Registreer uw on-premises vRA met de vCenter-eenmalige aanmelding op de on-premises site. Zie voor gedetailleerde instructies voor vSphere-replicatie 6,5 het VMware-document [VMware vSphere replicatie-6,5-installatie en-configuratie](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-avs-private-cloud-environment"></a>Het vSphere-replicatie apparaat installeren in uw cloud omgeving voor Privécloud

Controleer voordat u begint of u over het volgende beschikt:

* IP-bereik baarheid van subnetten in uw on-premises omgeving naar het beheer subnet van de Privécloud van uw AVS
* IP-bereik baarheid van replicatie subnet in uw on-premises vSphere-omgeving naar het oplossings subnet van uw AVS-Privécloud

Zie [een VPN-verbinding configureren voor de privécloud van uw AVS](set-up-vpn.md)voor instructies. De stappen zijn vergelijkbaar met die voor de on-premises installatie.

AVS raadt aan om FQDN-namen te gebruiken in plaats van IP-adressen tijdens de installatie van de vRA en het-pakket. Open de AVS-Portal om de FQDN-namen van vCenter en PSC in de Privécloud van uw AVS te achterhalen. Ga naar **resources**, selecteer de privécloud van uw AVS en klik op het tabblad **VSphere-beheer netwerk** .

![FQDN-namen van vCenter/PSC in de Privécloud van de AVS zoeken](media/srm-resources.png)

Voor AVS moet u vRA en de standaard instelling ' cloudowner ' niet installeren, maar in plaats daarvan een nieuwe gebruiker te maken. Dit wordt gedaan om te zorgen voor een hoge uptime en beschik baarheid van uw AVS Private Cloud vCenter-omgeving. De standaard cloudowner-gebruiker in de AVS Private Cloud vCenter beschikt echter niet over voldoende bevoegdheden om een nieuwe gebruiker met beheerders bevoegdheden te maken.

Voordat u vRA en een hoeveelheid van de-cloudowner installeert, moet u de vCenter-bevoegdheden van de gebruiker van de gebruikers beheerder escaleren en vervolgens een gebruiker met beheerders bevoegdheden maken in het vCenter-SSO-domein. Zie voor meer informatie over het standaard gebruikers-en machtigings model van de automatische AVS-Cloud [het machtigings model voor de privécloud-persoonlijke Cloud](learn-private-cloud-permissions.md).

De installatie bestaat uit de volgende stappen op hoog niveau:

1. [Bevoegdheden escaleren](escalate-private-cloud-privileges.md).
2. Maak een gebruiker in de Privécloud van uw AVS voor vSphere-replicatie en-beveiligings-installatie. Hieronder wordt uitgelegd in [de vCenter-gebruikers interface: Maak een gebruiker in de automatische AVS-Cloud voor vRA &-beveiligings-installatie](#vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation).
3. Bereid uw automatische AVS-cloud omgeving voor op de vRA-installatie.
4. Implementeer vRA in uw cloud van de AVS met behulp van de OVF in de VR-ISO van vmware.com. Voor vRA 6,5 bevat [deze VMware-blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) relevante informatie.
5. Firewall regels configureren voor vRA. Hieronder beschreven in de [AVS-portal: Firewall regels configureren voor vRA](#avs-portal-configure-firewall-rules-for-vra).
6. Registreer de vRA van de privécloud met de eenmalige aanmelding van de AVS in de Privécloud-site.
7. Configureer vSphere-replicatie verbindingen tussen de twee apparaten. Zorg ervoor dat de vereiste poorten zijn geopend via de firewalls. Zie [dit artikel in de VMware Knowledge Base](https://kb.vmware.com/s/article/2087769) voor een lijst met poort nummers die open moeten zijn voor VSphere-replicatie 6,5.

Zie voor gedetailleerde installatie-instructies voor vSphere-replicatie 6,5 het VMware-document [VMware vSphere replicatie-6,5-installatie en-configuratie](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation"></a>vCenter-gebruikers interface: een gebruiker maken in de automatische AVS-Cloud voor de installatie van vRA en het beveiligings-exemplaar

Meld u aan bij vCenter met cloudowner-gebruikers referenties na het escaleren van bevoegdheden vanuit de AVS-Portal.

Maak een nieuwe gebruiker, `srm-soln-admin`, in vCenter en voeg deze toe aan de groep Administrators in vCenter.
Meld u af bij vCenter als de cloudowner-gebruiker en meld u aan als de *soln-beheerder-* gebruiker.

#### <a name="avs-portal-configure-firewall-rules-for-vra"></a>AVS-portal: Firewall regels configureren voor vRA

Configureer firewall regels zoals beschreven in [firewall tabellen en-regels instellen](firewall.md) voor het openen van poorten om communicatie mogelijk te maken tussen:

* vRA in de oplossing netwerk-en vCenter-en ESXi-hosts in het management-netwerk.
* vRA-apparaten op de twee sites.

Zie dit [artikel in de VMware Knowledge Base](https://kb.vmware.com/s/article/2087769) voor een lijst met poort nummers die open moeten zijn voor VSphere-replicatie 6,5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Een server van het (en) installeren in uw on-premises omgeving

Voordat u begint, controleert u het volgende:

* het vSphere-replicatie apparaat wordt geïnstalleerd in uw privé-Cloud omgevingen op locatie en AVS.
* De vSphere-replicatie apparaten op beide sites zijn met elkaar verbonden.
* U hebt de VMware-informatie beoordeeld op vereisten en aanbevolen procedures. Voor de 6,5 van uw document kunt u verwijzen naar de vereisten voor VMware-documenten [en best practices voor de oplossings-6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Volg de VMware-documentatie voor het uitvoeren van de installatie van een server met twee sites in het implementatie model met één vCenter-exemplaar per platform Services-controller, zoals beschreven in dit [VMware-document](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). De installatie-instructies voor de 6,5 is beschikbaar in het VMware-document [installatie van site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-avs-private-cloud"></a>Een server van de beveiligings-Cloud installeren in de Privécloud

Voordat u begint, controleert u het volgende:

* het vSphere-replicatie apparaat wordt geïnstalleerd in uw privé-Cloud omgevingen op locatie en AVS.
* De vSphere-replicatie apparaten op beide sites zijn met elkaar verbonden.
* U hebt de VMware-informatie beoordeeld op vereisten en aanbevolen procedures. U kunt voor de 6,5-versie van de [site Recovery Manager 6,5 server verwijzen naar vereisten en aanbevolen procedures](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

In de volgende stappen wordt de automatische AVS-installatie van de beveiligings-Cloud-versie beschreven.

1. [vCenter-gebruikers interface: de installatie van de.](#vcenter-ui-install-srm)
2. [AVS-portal: Firewall regels configureren voor een-de](#avs-portal-configure-firewall-rules-for-srm)
3. [vCenter UI: Configure](#vcenter-ui-configure-srm)
4. [AVS-portal: de bevoegdheden voor het deactiveren](#avs-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter-gebruikers interface: de installatie van de.

Nadat u zich hebt aangemeld bij vCenter met de soln-beheerders referenties, volgt u de VMware-documentatie voor het uitvoeren van de server installatie in het implementatie model ' twee-site topologie met één vCenter-exemplaar per platform Services-controller ', zoals beschreven in dit [VMware-document](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). De installatie-instructies voor de 6,5 is beschikbaar in het VMware-document [installatie van site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="avs-portal-configure-firewall-rules-for-srm"></a>AVS-portal: Firewall regels configureren voor een-de

Configureer firewall regels zoals beschreven in [firewall tabellen en-regels instellen](firewall.md) om communicatie toe te staan tussen:

De server met de beveiligings-en/of-software-en/of-servers in de AVS-privécloud.
De server-en-tijd servers op beide sites

Zie [dit artikel in de VMware Knowledge Base](https://kb.vmware.com/s/article/2087769) voor een lijst met poort nummers die open moeten zijn voor VSphere-replicatie 6,5.

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI: Configure

Nadat u de beveiligings-cloud van de AVS hebt geïnstalleerd, voert u de volgende taken uit, zoals beschreven in de secties van de installatie-en configuratie handleiding voor VMware Site Recovery Manager. Voor de 6,5. de instructies zijn beschikbaar in het VMware-document [installatie van site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Verbind de Site Recovery Manager server-exemplaren op de beveiligde en herstel sites.
2. Stel een client verbinding in met het externe Site Recovery Manager-Server exemplaar.
3. Installeer de licentie sleutel voor Site Recovery Manager.

#### <a name="avs-portal-de-escalate-privileges"></a>AVS-portal: de bevoegdheden voor het deactiveren

Als u de bevoegdheden wilt deescaleren, raadpleegt u [bevoegdheden deescaleren](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Doorlopend beheer van uw oplossing

U hebt volledige controle over de vSphere-replicatie en de beveiligings-software in uw cloud omgeving van uw AVS en verwacht het beheer van de benodigde software levenscyclus. Zorg ervoor dat er een nieuwe versie van software compatibel is met de AVS-Privécloud en PSC voor het bijwerken of upgraden van vSphere-replicatie of-beveiligings-updates.

> [!NOTE]
> AVS is momenteel bezig met het verkennen van opties voor het aanbieden van een beheerde DR-service. 

## <a name="multiple-replication-configuration"></a>Configuratie van meerdere replicatie

 [Replicatie-en vSphere-replicatie technologieën kunnen zowel op de matrix als](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) op dezelfde tijd worden gebruikt. Ze moeten echter worden toegepast op een afzonderlijke set virtuele machines (een bepaalde virtuele machine kan worden beveiligd door replicatie op basis van een matrix of vSphere, maar niet voor beide). Bovendien kan de AVS-site worden geconfigureerd als een herstel site voor meerdere beveiligde sites. Zie de opties voor de meerdere locaties van de [website](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) voor meer informatie over configuraties voor meerdere locaties.

## <a name="references"></a>Verwijzingen

* [Documentatie voor VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Operationele limieten voor Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110)
* [Bandbreedte vereisten voor vSphere-replicatie berekenen](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [OVF choices bij het implementeren van vSphere-replicatie 6,5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere replicatie 6,5-installatie en-configuratie](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Vereisten en aanbevolen procedures voor de 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site Recovery manager in een topologie met twee sites met een vCenter Server exemplaar per platform Services-controller](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Installatie-en configuratie handleiding voor VMware Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [VMware-blog op de vSphere met replicatie op basis van een matrix versus replicatie van de](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [VMware-blog op een laag met meerdere locaties-opties](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Poort nummers die open moeten zijn voor vSphere-replicatie 5,8. x, 6. x en 8](https://kb.vmware.com/s/article/2147112)
