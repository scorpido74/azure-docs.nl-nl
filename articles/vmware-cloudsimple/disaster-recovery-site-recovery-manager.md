---
title: Azure VMware-oplossing door CloudSimple - Private Cloud instellen als een rampherstelsite met VMware Site Recovery Manager
description: Beschrijft hoe u uw CloudSimple Private Cloud instellen als een rampherstelsite voor on-premises VMware-workloads
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa8b2da683d68a337df38e13726f22c5af43540a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565924"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Private Cloud instellen als een noodhersteldoel met VMware Site Recovery Manager

U uw CloudSimple Private Cloud gebruiken als dr-site (disaster recovery) voor on-premises VMware-workloads.

De DR-oplossing is gebaseerd op vSphere Replication en VMware Site Recovery Manager (SRM). Een vergelijkbare aanpak kan worden gevolgd om uw Private Cloud in te schakelen als een primaire site die wordt beschermd door uw on-premises herstelsite.

De CloudSimple-oplossing:

* Elimineert de noodzaak om een datacenter speciaal voor DR in te richten.
* Hiermee u gebruikmaken van de Azure-locaties waar CloudSimple wordt ingezet voor wereldwijde geografische veerkracht.
* Biedt u de mogelijkheid om de implementatiekosten en de totale eigendomskosten voor het opzetten van DR te verlagen.

De CloudSimple-oplossing vereist dat u het volgende doet:

* Installeer, configureer en beheer vSphere-replicatie en SRM in uw private cloud.
* Geef uw eigen licenties voor SRM op wanneer de Private Cloud de beveiligde site is. U hebt geen extra SRM-licenties nodig voor de CloudSimple-site wanneer deze wordt gebruikt als herstelsite.

Met deze oplossing hebt u volledige controle over vSphere-replicatie en SRM. De vertrouwde gebruikersinterface-, API- en CLI-interfaces maken het gebruik van uw bestaande scripts en hulpprogramma's mogelijk.

![Site Recovery Manager-implementatie](media/srm-deployment.png)

U alle versies van vRA en SRM gebruiken die compatibel zijn met uw Private Cloud en on-premises omgevingen. De voorbeelden in deze gids gebruiken vRA 6.5 en SRM 6.5. Deze versies zijn compatibel met vSphere 6.5, die wordt ondersteund door CloudSimple.

## <a name="deploy-the-solution"></a>De oplossing implementeren

In de volgende secties wordt beschreven hoe u een DR-oplossing implementeert met SRM in uw Private Cloud.

1. [Controleren of VMware-productversies compatibel zijn](#verify-that-vmware-product-versions-are-compatible)
2. [De grootte van uw DR-omgeving schatten](#estimate-the-size-of-your-dr-environment)
3. [Een private cloud maken voor uw omgeving](#create-a-private-cloud-for-your-environment)
4. [Private Cloud-netwerken instellen voor de SRM-oplossing](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Een Site-to-Site VPN-verbinding instellen tussen uw on-premises netwerk en de Private Cloud en de vereiste poorten openen](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Infrastructuurservices instellen in uw Private Cloud](#set-up-infrastructure-services-in-your-private-cloud)
7. [VSphere Replication-toestel installeren in uw on-premises omgeving](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [VSphere-replicatietoestel installeren in uw Private Cloud-omgeving](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [SRM-server installeren in uw on-premises omgeving](#install-srm-server-in-your-on-premises-environment)
10. [SRM-server installeren in uw Private Cloud](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Controleren of VMware-productversies compatibel zijn

De configuraties in deze handleiding zijn onderworpen aan de volgende compatibiliteitsvereisten:

* Dezelfde versie van SRM moet worden geïmplementeerd in uw Private Cloud en uw on-premises omgeving.
* Dezelfde versie van vSphere-replicatie moet worden geïmplementeerd in uw private cloud en uw on-premises omgeving.
* De versies van Platform Services Controller (PSC) in uw Private Cloud en uw on-premises omgeving moeten compatibel zijn.
* De versies van vCenter in uw private cloud en uw on-premises omgeving moeten compatibel zijn.
* De versies van SRM- en vSphere-replicatie moeten compatibel zijn met elkaar en met de versies van PSC en vCenter.

Ga voor koppelingen naar de relevante VMware-documentatie en compatibiliteitsinformatie naar de documentatie [van VMware Site Recovery Manager.](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)

Open de CloudSimple-portal om de versies van vCenter en PSC in uw Private Cloud te vinden. Ga naar **Resources,** selecteer uw Private Cloud en klik op het tabblad **vSphere Management Network.**

![vCenter & PSC-versies in Private Cloud](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>De grootte van uw DR-omgeving schatten

1. Controleer of uw geïdentificeerde on-premises configuratie binnen de ondersteunde limieten valt. Voor SRM 6.5 worden de limieten gedocumenteerd in het VMware knowledge base-artikel over [operationele limieten voor Site Recovery Manager 6.5](https://kb.vmware.com/s/article/2147110).
2. Zorg ervoor dat u voldoende netwerkbandbreedte hebt om te voldoen aan uw werkbelastinggrootte en RPO-vereisten. Het VMware knowledge base-artikel over [het berekenen van bandbreedtevereisten voor vSphere-replicatie](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) biedt richtlijnen voor bandbreedtelimieten.
3. Gebruik de cloudsimple-sizer-tool om een schatting te maken van de resources die nodig zijn in uw DR-site om uw on-premises omgeving te beschermen.

### <a name="create-a-private-cloud-for-your-environment"></a>Een private cloud maken voor uw omgeving

Maak een Private Cloud van de CloudSimple-portal door de instructies en maataanbevelingen te volgen in [Een private cloud maken.](create-private-cloud.md)

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Private Cloud-netwerken instellen voor de SRM-oplossing

Toegang tot de CloudSimple-portal voor het instellen van Private Cloud-netwerken voor de SRM-oplossing.

Maak een VLAN voor het SRM-oplossingsnetwerk en wijs het een subnet CIDR toe. Zie [VLAN's/subnetten maken en beheren voor](create-vlan-subnet.md)instructies.

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Een Site-to-Site VPN-verbinding instellen tussen uw on-premises netwerk en de Private Cloud en de vereiste poorten openen

Site-to-Site-connectiviteit instellen tussen uw on-premises netwerk en uw private cloud. Zie Een [VPN-verbinding configureren met uw CloudSimple Private Cloud](set-up-vpn.md)voor instructies.

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Infrastructuurservices instellen in uw Private Cloud

Configureer infrastructuurservices in de Private Cloud om het eenvoudig te maken om uw workloads en hulpprogramma's te beheren.

Als u een van de volgende gegevens wilt doen, u een externe identiteitsprovider toevoegen zoals beschreven in [Gebruik Azure AD als identiteitsprovider voor vCenter op CloudSimple Private Cloud:](azure-ad.md)

* Identificeer gebruikers vanuit uw on-premises Active Directory (AD) in uw private cloud.
* Stel een AD in uw private cloud in voor alle gebruikers.
* Gebruik Azure AD.

Als u IP-adreszoeking, IP-adresbeheer en naamomzettingsservices wilt bieden voor uw workloads in de Private Cloud, stelt u een DHCP- en DNS-server in zoals beschreven in [DNS- en DHCP-toepassingen en -workloads instellen in uw CloudSimple Private Cloud.](dns-dhcp-setup.md)

Het *.cloudsimple.io-domein wordt gebruikt door beheer-VM's en hosts in uw private cloud. Als u aanvragen voor dit domein wilt oplossen, configureert u DNS-forwarding op de DNS-server zoals beschreven in [Een voorwaardelijke doorstuurer maken.](on-premises-dns-setup.md#create-a-conditional-forwarder)

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>VSphere-replicatietoestel installeren in uw on-premises omgeving

Installeer vSphere Replication Appliance (vRA) in uw on-premises omgeving door de VMware-documentatie te volgen. De installatie bestaat uit deze stappen op hoog niveau:

1. Bereid uw on-premises omgeving voor op vRA-installatie.

2. Implementeer vRA in uw on-premises omgeving met behulp van de OVF in de VR ISO van vmware.com. Voor vRA 6.5 heeft [deze VMware blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) de relevante informatie.

3. Registreer uw on-premises vRA met vCenter Single Sign-On op de on-premises locatie. Zie het VMware-document [VMware vSphere Replication 6.5 Installatie en configuratie](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)voor gedetailleerde instructies voor vSphere-replicatie 6.5.

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>VSphere-replicatietoestel installeren in uw Private Cloud-omgeving

Controleer voordat u begint of u het volgende hebt:

* IP-bereikbaarheid van subnetten in uw on-premises omgeving tot het beheersubnet van uw Private Cloud
* IP-bereikbaarheid van replicatiesubnet in uw on-premises vSphere-omgeving tot het SRM-oplossingsubnet van uw Private Cloud

Zie Een [VPN-verbinding configureren met uw CloudSimple Private Cloud](set-up-vpn.md)voor instructies. De stappen zijn vergelijkbaar met die voor de on-premises installatie.

CloudSimple raadt aan om FQDN's te gebruiken in plaats van IP-adressen tijdens de installatie van vRA en SRM. Open de CloudSimple-portal om de FQDN van vCenter en PSC in uw Private Cloud te vinden. Ga naar **Resources,** selecteer uw Private Cloud en klik op het tabblad **vSphere Management Network.**

![FQDN van vCenter/PSC vinden in Private Cloud](media/srm-resources.png)

CloudSimple vereist dat u vRA en SRM niet installeert met de standaard 'cloudowner'-gebruiker, maar in plaats daarvan een nieuwe gebruiker maakt. Dit wordt gedaan om een hoge uptime en beschikbaarheid voor uw Private Cloud vCenter-omgeving te garanderen. De standaardgebruiker van de cloudeigenaar in het VCenter voor Private Cloud beschikt echter niet over voldoende bevoegdheden om een nieuwe gebruiker met beheerdersbevoegdheden te maken.

Voordat u vRA en SRM installeert, moet u de vCenter-bevoegdheden van de cloudeigenaargebruiker escaleren en vervolgens een gebruiker maken met beheerdersbevoegdheden in het VCenter SSO-domein. Zie [Het machtigingsmodel Privécloud leren voor](learn-private-cloud-permissions.md)meer informatie over het standaard-privécloud-gebruikers- en machtigingsmodel .

De installatie bestaat uit deze stappen op hoog niveau:

1. [Bevoegdheden escaleren](escalate-private-cloud-privileges.md).
2. Maak een gebruiker in uw Private Cloud voor vSphere-replicatie en SRM-installatie. Hieronder uitgelegd in [vCenter UI: Maak een gebruiker in Private Cloud voor vRA-& SRM-installatie.](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation)
3. Bereid uw Private Cloud-omgeving voor op de vRA-installatie.
4. Implementeer vRA in uw Private Cloud met behulp van de OVF in de VR ISO van vmware.com. Voor vRA 6.5 bevat [deze VMware blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) relevante informatie.
5. Firewallregels configureren voor vRA. Hieronder uitgelegd in [CloudSimple portal: Firewall regels configureren voor vRA](#cloudsimple-portal-configure-firewall-rules-for-vra).
6. Registreer Private Cloud vRA met vCenter Single Sign-On op de Private Cloud-site.
7. VSphere-replicatieverbindingen tussen de twee apparaten configureren. Zorg ervoor dat de vereiste poorten worden geopend in de firewalls. Zie [dit VMware knowledge base-artikel](https://kb.vmware.com/s/article/2087769) voor een lijst met poortnummers die open moeten staan voor vSphere Replication 6.5.

Zie het VMware-document [VMware vSphere Replication 6.5 Installatie en configuratie](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)voor gedetailleerde installatie-instructies voor vSphere-replicatie 6.5.

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter-gebruikersinterface: een gebruiker maken in Private Cloud voor vRA- en SRM-installatie

Meld u aan bij vCenter met behulp van gebruikersreferenties van cloudowner nadat de bevoegdheden van de CloudSimple-portal zijn geëscaleerd.

Maak een nieuwe `srm-soln-admin`gebruiker in vCenter en voeg deze toe aan de beheerdersgroep in vCenter.
Meld u af bij vCenter als gebruiker van de cloudeigenaar en meld u aan als gebruiker van *de srm-soln-admin.*

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple-portal: firewallregels configureren voor vRA

Firewallregels configureren zoals beschreven in [Firewalltabellen en -regels instellen](firewall.md) om poorten te openen om communicatie tussen:

* vRA in het SRM-oplossingsnetwerk en vCenter- en ESXi-hosts in het beheernetwerk.
* vRA-apparaten op de twee locaties.

Zie dit [VMware knowledge base-artikel](https://kb.vmware.com/s/article/2087769) voor een lijst met poortnummers die open moeten staan voor vSphere Replication 6.5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>SRM-server installeren in uw on-premises omgeving

Controleer voordat u begint het volgende:

* vSphere Replication Appliance is geïnstalleerd in uw on-premises en Private Cloud-omgevingen.
* De vSphere-replicatieapparaten op beide locaties zijn met elkaar verbonden.
* U hebt de VMware-informatie over vereisten en best practices beoordeeld. Voor SRM 6.5 u verwijzen naar het VMware-document [Vereisten en Aanbevolen procedures voor SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Volg VMware-documentatie om de installatie van srm-servers uit te voeren in het implementatiemodel 'Two-Site Topology with One vCenter Instance per Platform Services Controller' zoals beschreven in dit [VMWare-document.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html) De installatie-instructies voor SRM 6.5 zijn beschikbaar in het VMware-document [Site Recovery Manager installeren.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)

### <a name="install-srm-server-in-your-private-cloud"></a>SRM-server installeren in uw Private Cloud

Controleer voordat u begint het volgende:

* vSphere Replication Appliance is geïnstalleerd in uw on-premises en Private Cloud-omgevingen.
* De vSphere-replicatieapparaten op beide locaties zijn met elkaar verbonden.
* U hebt de VMware-informatie over vereisten en best practices beoordeeld. Voor SRM 6.5 u verwijzen naar [Vereisten en aanbevolen procedures voor siteherstelbeheer 6.5-serverinstallatie](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

In de volgende stappen wordt de Private Cloud SRM-installatie beschreven.

1. [vCenter-gebruikersinterface: SRM installeren](#vcenter-ui-install-srm)
2. [CloudSimple-portal: firewallregels configureren voor SRM](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter-gebruikersinterface: SRM configureren](#vcenter-ui-configure-srm)
4. [CloudSimple-portal: de-escaleren van bevoegdheden](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter-gebruikersinterface: SRM installeren

Volg vMware-documentatie na het inloggen op vCenter met srm-soln-admin-referenties om de installatie van srm-servers uit te voeren in het implementatiemodel 'Two-Site Topology with One vCenter Instance per Platform Services Controller' zoals beschreven in dit [VMWare-document.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html) De installatie-instructies voor SRM 6.5 zijn beschikbaar in het VMware-document [Site Recovery Manager installeren.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple-portal: firewallregels configureren voor SRM

Firewallregels configureren zoals beschreven in [Firewalltabellen en -regels instellen](firewall.md) om communicatie tussen:

De SRM server en vCenter / PSC in de Private Cloud.
De SRM-servers op beide sites

Zie [dit VMware knowledge base-artikel](https://kb.vmware.com/s/article/2087769) voor een lijst met poortnummers die open moeten staan voor vSphere Replication 6.5.

#### <a name="vcenter-ui-configure-srm"></a>vCenter-gebruikersinterface: SRM configureren

Nadat SRM is geïnstalleerd in de private cloud, voert u de volgende taken uit zoals beschreven in de secties van de installatie- en configuratiehandleiding voor VMware Site Recovery Manager. Voor SRM 6.5 zijn de instructies beschikbaar in het VMware-document [Site Recovery Manager installeren](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Verbind de serverinstanties van siteherstelbeheer op de beveiligde en herstelsites.
2. Een clientverbinding tot stand brengen met het extern beheerbeheer-serverexemplaar.
3. Installeer de licentiesleutel siteherstelbeheer.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple-portal: bevoegdheden de-escaleren

Zie [Bevoegdheden de-escaleren](escalate-private-cloud-privileges.md#de-escalate-privileges)als u bevoegdheden wilt de-escaleren.

## <a name="ongoing-management-of-your-srm-solution"></a>Doorlopend beheer van uw SRM-oplossing

U hebt volledige controle over vSphere Replication- en SRM-software in uw Private Cloud-omgeving en wordt verwacht dat u het noodzakelijke beheer van de softwarelevenscyclus uitvoert. Zorg ervoor dat elke nieuwe versie van software compatibel is met de Private Cloud vCenter en PSC voordat u vSphere-replicatie of SRM bijwerkt of upgradet.

> [!NOTE]
> CloudSimple onderzoekt momenteel opties voor het aanbieden van een managed DR-service. 

## <a name="multiple-replication-configuration"></a>Meervoudige replicatieconfiguratie

 [Zowel array-gebaseerde replicatie- als vSphere-replicatietechnologieën kunnen tegelijkertijd samen met SRM worden gebruikt.](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) Ze moeten echter worden toegepast op afzonderlijke set VM's (een bepaalde VM kan worden beschermd door array-gebaseerde replicatie of vSphere-replicatie, maar niet beide). Bovendien kan de CloudSimple-site worden geconfigureerd als een herstelsite voor meerdere beveiligde sites. Zie [SRM Multi-Site Opties](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) voor informatie over configuraties met meerdere locaties.

## <a name="references"></a>Verwijzingen

* [Documentatie vmware-siteherstelbeheer](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Operationele limieten voor siteherstelmanager 6.5](https://kb.vmware.com/s/article/2147110)
* [Bandbreedtevereisten berekenen voor vSphere-replicatie](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [OVF-keuzes bij het implementeren van vSphere-replicatie 6.5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere-replicatie 6.5 -installatie en -configuratie](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Voorwaarden en best practices voor SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site recovery Manager in een topologie met twee locaties met één vCenter-serverinstantie per Platform Services-controller](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Installatie- en configuratiehandleiding vMware-siteherstelbeheer 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [VMware-blog over SRM met replicatie op basis van arrays versus vSphere-replicatie](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [VMware Blog over SRM Multi-site opties](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Poortnummers die open moeten staan voor vSphere-replicatie 5.8.x, 6.x en 8](https://kb.vmware.com/s/article/2147112)
