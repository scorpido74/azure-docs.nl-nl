---
title: 'Zelfstudie: VMware HCX implementeren en configureren'
description: Meer informatie over het implementeren en configureren van de VMware HCX-oplossing voor de privécloud van uw Azure VMware Solution.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 69832d1537f0f1be95d3283f543ef6e54187b58d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578783"
---
# <a name="deploy-and-configure-vmware-hcx"></a>VMware HCX implementeren en configureren

In dit artikel gaan we de procedures doorlopen voor het implementeren en configureren van de VMware-HCX voor de privécloud van uw Azure VMware Solution. VMware HCX maakt de migratie van uw VMware-workloads naar Azure VMware Solution en andere verbonden sites mogelijk via verschillende migratietypen.

VMware HCX Advanced (vooraf geïmplementeerd in Azure VMware Solution) ondersteunt maximaal drie siteverbindingen (on-premises naar cloud of cloud naar coud). Als er meer dan drie siteverbindingen voor VMware HCX Enterprise vereist zijn, hebt u de mogelijkheid om de [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)-invoeg toepassing in te schakelen (deze is momenteel beschikbaar als *preview*) door een [ondersteuningsaanvraag in te dienen](https://rc.portal.azure.com/#create/Microsoft.Support). VMware HCX Enterprise Edition (EE) is beschikbaar met Azure VMware Solution als een *preview*-functie/-service. Hoewel VMware HCX EE voor Azure VMware Solution zich in *preview* bevindt, is het een gratis functie/service en onderhevig aan de servicevoorwaarden van de preview-versie. Zodra de VMware HCX EE-service algemeen beschikbaar wordt, krijgt u een melding dat de facturering over 30 dagen wordt omgeschakeld. U hebt ook de mogelijkheid om de service uit te schakelen of u ervan af te melden.

Neem voordat u begint [Voordat u begint](#before-you-begin), [Vereisten voor de softwareversie](#software-version-requirements) en [Vereisten](#prerequisites) grondig door. 

Vervolgens worden alle benodigde procedures doorlopen voor het volgende:

> [!div class="checklist"]
> * De on-premises VMware HCX OVA (connector) implementeren
> * VMware HCX activeren
> * Uw on-premises omgeving koppelen met uw Azure VMware Solution-omgeving.
> * De interconnect (rekenprofiel, netwerkprofiel(en) en service-mesh) configureren
> * De installatie voltooien door de status van het apparaat te controleren.

Als u klaar bent, kunt u de aanbevolen volgende stappen aan het einde van dit artikel volgen.  

## <a name="before-you-begin"></a>Voordat u begint
   
* Bekijk de [reeks eenvoudige zelfstudies](tutorial-network-checklist.md) over Azure VMware Solution Software Defined Datacenter (SDDC).
* Bekijk en raadpleeg de [VMware HCX-documentatie](https://docs.vmware.com/en/VMware-HCX/index.html), met inbegrip van de gebruikershandleiding voor HCX.
* Bekijk het VMware-document [Virtuele machines migreren met VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Bekijk eventueel [Implementatieoverwegingen voor VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Bekijk eventueel gerelateerde VMware-materialen over HCX, zoals de [reeks blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) van VMware vSphere over HCX. 
* U kunt eventueel een activering voor Azure VMware Solution HCX Enterprise aanvragen via de ondersteuningskanalen van Azure VMware Solution.
* Voor het implementeren van het WAN Interconnect-apparaat zijn specifieke CIDR-bereiken al toegewezen op basis van door de klant geleverde `\22` voor het maken van de privécloud.

Het bepalen van de grootte van workloads met reken- en opslagresources is een essentiële planningsstap. Handel de stap voor het bepalen van de grootte af als onderdeel van de eerste planning van de privécloudomgeving. 

U kunt workloads ook vergroten of verkleinen door een [Evaluatie van de Azure VMware Solution](../migrate/how-to-create-azure-vmware-solution-assessment.md) in de Azure Migrate-portal uit te voeren.

## <a name="software-version-requirements"></a>Vereisten voor de softwareversie

Voor de infrastructuuronderdelen moet de vereiste minimumversie worden uitgevoerd. 
                                                         
| Onderdeeltype    | Vereisten voor bronomgeving    | Vereisten voor de doelomgeving   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Als u 5.5 U1 of eerder gebruikt, gebruikt u de zelfstandige HCX-gebruikersinterface voor HCX-bewerkingen.  | 6.0 U2 en hoger   |
| ESXi   | 5,0    | ESXi 6.0 en hoger   |
| NSX    | Voor HCX-netwerkextensie van logische switches in de bron: NSXv 6.2 + of NSX-T 2.4 +   | NSXv 6.2 + of NSX-T 2.4 +<br/><br/>Voor HCX-nabijheidsroutering: NSXv 6.4 + (nabijheidsroutering wordt niet ondersteund met NSX-T) |
| vCloud Director   | Niet vereist: geen interoperabiliteit met vCloud Director op de bronsite | Wanneer u de doelomgeving met vCloud Director integreert, is de minimumversie 9.1.0.2.  |

## <a name="prerequisites"></a>Vereisten

### <a name="network-and-ports"></a>Netwerk en poorten

* [ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) geconfigureerd tussen on-premises en Azure VMware Solution SDDC ExpressRoute-circuits.

* Alle vereiste poorten moeten zijn geopend voor communicatie tussen on-premises onderdelen en on-premises naar Azure VMware Solution SDDC (Zie de [VMware HCX-documentatie](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* On-premises HCX IX- en NE-apparaten moeten een vCenter- en ESXi-infrastructuur kunnen bereiken.

### <a name="ip-addresses"></a>IP-adressen

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>De VMware HCX OVA on-premises implementeren

>[!NOTE]
>Voordat u het virtuele apparaat in uw on-premises vCenter implementeert, moet u de VMware HCX OVA downloaden. 

1. Meld u aan bij de Azure VMware Solution HCX Manager op `https://x.x.x.9`poort 443 met de gebruikersreferenties van **cloudadmin** en ga vervolgens naar **Support**.

   >[!TIP]
   >Als u het IP-adres van HCX Manager wilt identificeren, gaat u op de blade Azure VMware Solution naar **Manage** > **Connectivity** en selecteert u vervolgens het tabblad **HCX**. 
   >
   >Het vCenter-wachtwoord is gedefinieerd tijdens het instellen van de privécloud.

1. Selecteer de koppeling **download** om het VMware HCX OVA-bestand te downloaden.

1. Ga naar het on-premises vCenter en selecteer een OVF-sjabloon. Dit is het OVA-bestand dat u hebt gedownload om te implementeren in uw on-premises vCenter.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Selecteer een naam en locatie, een resource/cluster waarin u HCX implementeert en controleer de details en de vereiste resources.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Bekijk de licentievoorwaarden en als u akkoord gaat, selecteert u de vereiste opslag en het gewenste netwerk en selecteert u vervolgens **Next**.

1. Voer in **Customize template** alle vereiste gegevens in. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Selecteer **Next**, controleer de configuratie en selecteer vervolgens **Finish** om HCX OVA te implementeren.
     
   >[!NOTE]
   >Over het algemeen wordt de VMware HCX Manager die u nu implementeert, geïmplementeerd in het beheernetwerk van het cluster.  
   
   > [!IMPORTANT]
   > Nadat de implementatie is voltooid, moet u het virtuele apparaat mogelijk handmatig inschakelen.
   > Wacht nadat u het HCX-apparaat hebt ingeschakeld 10-15 minuten om naar de volgende stap te gaan.

Bekijk voor een volledig overzicht van deze stap de video [Azure VMware Solution: implementatie van VMware HCX-apparaat](https://www.youtube.com/embed/BwSnQeefnso). 


## <a name="activate-vmware-hcx"></a>VMware HCX activeren

Nadat u de VMware HCX OVA on-premises hebt geïmplementeerd, bent u klaar om VMware HCX te activeren. Voordat u VMware HCX kunt activeren, moet u een licentie ophalen.

1. Ga in Azure VMware Solution naar **Manage** > **Connectivity**, selecteer het tabblad **HCX** en selecteer vervolgens **Add**.

1. Meld u aan bij de on-premises VMware HCX Manager op `https://HCXManagerIP:9443` en meld u aan met de gebruikersreferenties voor de **admin**. 

   > [!IMPORTANT]
   > Zorg ervoor dat u het `9443`-poortnummer met het IP-adres van VMware HCX Manager toevoegt.

1. Voer in **Licensing** uw **HCX Advanced Key** in.  
   
    > [!NOTE]
    > VMware HCX Manager moet open internettoegang hebben of er moet een proxy voor zijn geconfigureerd.

1. Geef in **Datacenter Location** de dichtstbijzijnde locatie op waar u de VMware HCX Manager on-premises installeert.

1. Wijzig de **systeemnaam** of accepteer de standaardwaarde.
   
1. U kunt later voltooien of doorgaan. Selecteer de optie **Yes, Continue** om door te gaan.
    
1. Geef in **Connect your vCenter** de FQDN of het IP-adres van uw vCenter-server en de juiste referenties op en selecteer vervolgens **Continue**.
   
1. Geef in **Configure SSO/PSC** de FQDN of het IP-adres van uw PSC op en selecteer vervolgens **Continue**.
   
   >[!NOTE]
   >Normaal gesproken hetzelfde als het FQDN/de IP van uw vCenter.

1. Controleer of alle invoerwaarden juist zijn en selecteer **Restart**.
    
   > [!NOTE]
   > Er vindt een vertraging plaats na het opnieuw opstarten voordat u wordt gevraagd om de volgende stap.
   >
   >Nadat de services opnieuw zijn opgestart, is het belangrijk dat vCenter groen wordt weergegeven op het scherm dat verschijnt. Zowel vCenter als SSO hebben de juiste configuratieparameters. Deze moeten hetzelfde zijn als op het vorige scherm.

   :::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Bekijk voor een volledig overzicht van deze stap de video [Azure VMware Solution: activering van VMware HCX](https://www.youtube.com/embed/BkAV_TNYxdE).


## <a name="configure-vmware-hcx"></a>VMware HCX configureren

Nu bent u klaar om een sitekoppeling toe te voegen, een netwerk- en rekenprofiel te maken en services, zoals migratie, netwerkextensie of herstel na noodgevallen, in te schakelen. 

### <a name="add-a-site-pairing"></a>Een sitekoppeling toevoegen

U kunt de VMware HCX Manager in Azure VMware Solution verbinden (koppelen) met de VMware HCX Manager in uw datacentrum. 

1. Meld u aan bij uw on-premises vCenter en selecteer onder **Home** de optie **HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. Selecteer onder **Infrastructure** de optie **Site Pairing** en selecteer vervolgens de optie **Connect To Remote Site** (in het midden van het scherm). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Voer de **URL of het IP-adres van de externe HCX**, de cloudadmin@vsphere.local gebruikersnaam en het **wachtwoord** van Azure VMware Solution in en selecteer vervolgens **Connect**.

   > [!NOTE]
   > De **URL van de externe HCX** is de HX Manager van de privécloud van uw Azure VMware Solution, meestal het adres '. 9' van het beheernetwerk.  Als uw vCenter bijvoorbeeld 192.168.4.2 is, wordt uw HCX-URL 192.168.4.9.
   >
   > Het **wachtwoord** is hetzelfde wachtwoord als dat u hebt gebruikt om u aan te melden bij vCenter. U hebt dit wachtwoord op het eerste implementatiescherm gedefinieerd.

   Er wordt een scherm weergegeven met een verbinding (koppeling) tussen uw HCX Manager in Azure VMware Solution en uw on-premises HCX Manager.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter.":::

Bekijk voor een volledig overzicht van deze stap de video [Azure VMware Solution: sitekoppeling van VMware HCX](https://www.youtube.com/embed/sKizDCRHOko).



### <a name="create-network-profiles"></a>Netwerkprofielen maken

VMware HCX implementeert een aantal virtuele apparaten (geautomatiseerd) maar heeft meerdere IP-segmenten nodig.  Wanneer u uw netwerkprofielen maakt, definieert u de IP-segmenten die u hebt geïdentificeerd tijdens de [Voorbereiding en planningsfase van VMware HCX-netwerksegmenten vóór de implementatie](production-ready-deployment-steps.md#vmware-hcx-network-segments).

U maakt vier netwerkprofielen:

   - Beheer
   - vMotion
   - Replicatie
   - Uplink

1. Onder **Infrastructure** selecteert u **Interconnect** > **Multi-Site Service Mesh** > **Network Profiles** > **Create Network Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Voor elk netwerkprofiel selecteert u het netwerk, de poortgroep, geeft u een naam op, maakt u de IP-adresgroep voor dat specifieke segment en selecteert u vervolgens **Create**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter.":::

Bekijk voor een volledig overzicht van deze stap de video [Azure VMware Solution: VMware HCX-netwerkprofiel maken](https://www.youtube.com/embed/NhyEcLco4JY).


### <a name="create-compute-profile"></a>Rekenprofiel maken

1. Selecteer **Compute Profiles** > **Create Compute Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Voer een naam in voor het profiel en selecteer **Continue**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Selecteer de services die u wilt inschakelen, zoals migratie, netwerkextensie of herstel na noodgevallen, en selecteer vervolgens **Continue**.
  
   > [!NOTE]
   > In het algemeen wordt hier niets gewijzigd.

1. Selecteer in **Select Service Resources** een of meer serviceresources (clusters) om in te schakelen voor de geselecteerde VMware HCX-services.  

1. Wanneer u de clusters in uw on-premises datacentrum ziet, selecteert u **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Selecteer in **Select Datastore** de gegevensopslagresource voor de implementatie van de VMware HCX Interconnect-apparaten en selecteer vervolgens **Continue**.

   Wanneer er meerdere resources zijn geselecteerd, gebruikt VMware HCX de eerste resource die is geselecteerd tot de capaciteit is uitgeput.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Selecteer **Management Network Profile**, het beheernetwerkprofiel dat u in de vorige stappen hebt gemaakt en selecteer vervolgens **Continue**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Met het beheernetwerkprofiel kan het VMware HCX-apparaat communiceren met vCenter en kunnen de ESXi-hosts worden bereikt.

1. Selecteer **Uplink Network Profile**, het Uplink-netwerkprofiel dat u in de vorige stappen hebt gemaakt en selecteer vervolgens **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Selecteer **vMotion Network Profile**, het vMotion-netwerkprofiel dat u in de vorige stappen hebt gemaakt en selecteer vervolgens **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Kies **vSphere Replication Network Profile**, het netwerkprofiel voor replicatie dat u in de vorige stappen hebt gemaakt en kies vervolgens **Continue**.

   In de meeste gevallen is het netwerkprofiel voor replicatie hetzelfde als het beheernetwerkprofiel.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Selecteer **Distributed Switches for Network Extensions**, de gedistribueerde virtuele switches die de virtuele machines bevatten die moeten worden gemigreerd naar de Azure VMware Solution op een uitgebreid netwerk in laag 2, en selecteer vervolgens **Continue**.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Controleer de verbindingsregels en selecteer **Continue**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Selecteer **Finish** om het rekenprofiel te voltooien.

   Er wordt een venster zoals het onderstaande weergegeven.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Bekijk voor een volledig overzicht van deze stap de video [Azure VMware Solution: VMware HCX-rekenprofiel maken](https://www.youtube.com/embed/qASXi5xrFzM).




### <a name="create-service-mesh"></a>Service-mesh maken

Het is nu tijd om een service-mesh tussen on-premises en Azure VMware Solution SDDC te configureren.

1. Selecteer onder **Infrastructure** achtereenvolgens **Interconnect** > **Service Mesh** > **Create Service Mesh**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Controleer de sites die vooraf zijn ingevuld en selecteer **Continue**. 

   >[!NOTE]
   >Als dit uw eerste service-meshconfiguratie is, hoeft u dit scherm niet te wijzigen.  

1. Selecteer de vervolgkeuzelijst voor de bron en externe rekenprofielen en selecteer vervolgens **Continue**.  

   De selecties definiëren de resources waarin VM's VMware HCX-services kunnen gebruiken.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Controleer de services die worden ingeschakeld en selecteer **Continue**.  

1. Selecteer in **Advanced Configuration - Override Uplink Network profiles** de optie **Continue**.  Uplink-netwerkprofielen maken verbinding met het netwerk via welk de verbindingsapparaten van de externe site kunnen worden bereikt.  
  
1. Controleer de gegevens in **Advanced Configuration - Network Extension Appliance Scale Out** en selecteer **Continue**. 

1. Controleer de gegevens in **Advanced Configuration - Traffic Engineering**, breng de benodigde wijzigingen aan en selecteer vervolgens **Continue**.

1. Bekijk de preview van de topologie en selecteer **Continue**.

1. Voer een gebruiksvriendelijke naam in voor deze service-mesh en selecteer **Finish** om de bewerking te voltooien.  

1. Selecteer **View Tasks** om de implementatie te controleren. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter.":::

   Wanneer de implementatie van de service-mesh is voltooid, worden de services groen weergegeven.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Verifieer de status van de service-mesh door de status van het apparaat te controleren; selecteer **Interconnect** > **Appliances**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Bekijk voor een volledig overzicht van deze stap de video [Azure VMware Solution: VMware HCX-service-mesh maken](https://www.youtube.com/embed/FyZ0d3P_T24).



### <a name="optional-create-a-network-extension"></a>(Optioneel) Een netwerkextensie maken

Als u netwerken van uw on-premises omgeving wilt uitbreiden naar Azure VMware-oplossing, voert u de volgende stappen uit om deze netwerken uit te breiden.

1. Selecteer onder **Services** de optie **Network Extension** en selecteer vervolgens **Create a Network Extension**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Selecteer elk netwerk dat u wilt uitbreiden naar Azure VMware Solution en selecteer vervolgens **Next**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter.":::

1. Voer het IP-adres van de on-premises gateway in voor elk van de netwerken die u uitbreidt en selecteer vervolgens **Submit**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter.":::

   Het duurt enkele minuten voordat de netwerkextensie is voltooid. Wanneer dit het geval is, ziet u dat de status is gewijzigd in **extension complete**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Ga naar het on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Bekijk voor een volledig overzicht van deze stap de video [Azure VMware Solution: VMware HCX-netwerkuitbreiding](https://www.youtube.com/embed/cNlp0f_tTr0).


## <a name="next-steps"></a>Volgende stappen

Als u dit punt bereikt en de tunnelstatus van het apparaat **UP** en groen is, kunt u virtuele machines van Azure VMware Solution migreren en beveiligen met behulp van VMware HCX.  De Azure VMware Solution ondersteunt migraties van workloads (met of zonder een netwerkextensie).  U kunt nog steeds workloadmigraties uitvoeren in on-premises netwerken van uw vSphere-omgeving en virtuele machines op deze netwerken implementeren.  Zie de [VMware HCX-documentatie](https://docs.vmware.com/en/VMware-HCX/index.html) en [Virtuele machines migreren met VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) in de technische documentatie van VMware voor meer informatie over het gebruik van HCX.
