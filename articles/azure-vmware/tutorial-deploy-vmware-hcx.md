---
title: 'Zelfstudie: VMware HCX implementeren en configureren'
description: Meer informatie over het implementeren en configureren van een VMware HCX-oplossing voor de privécloud van uw Azure VMware Solution.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 424abeef567d88f7de37f7a7a4ab7a7a8b6ef3bc
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791406"
---
# <a name="deploy-and-configure-vmware-hcx"></a>VMware HCX implementeren en configureren

In dit artikel gaan we de procedures doorlopen voor het implementeren en configureren van de on-premises VMware HCX-connector voor de privécloud van uw Azure VMware Solution. Met VMware HCX kunt u uw VMware-workloads migreren naar Azure VMware Solution en andere verbonden sites mogelijk via verschillende migratietypen. Omdat Azure VMware Solution de HCX Cloud Manager implementeert en configureert, moet u de HCX-connector in uw on-premises VMware-datacenter downloaden, activeren en configureren.

VMware HCX Advanced Connector is vooraf geïmplementeerd in Azure VMware Solution. De connector ondersteunt maximaal drie siteverbindingen (on-premises naar de cloud of van cloud naar cloud). Als u meer dan driesite verbindingen nodig hebt, dient u een [ondersteuningsaanvraag](https://rc.portal.azure.com/#create/Microsoft.Support) in om de invoegtoepassing [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) in te schakelen. Deze invoegtoepassing is momenteel in preview. 

>[!NOTE]
>VMware HCX Enterprise Edition (EE) is beschikbaar bij Azure VMware Solution als een preview-service. Het is gratis en de voorwaarden voor een preview-service zijn van toepassing. Nadat de VMware HCX EE-service algemeen beschikbaar is, krijgt u een melding dat de facturering over 30 dagen wordt omgeschakeld. U hebt ook de mogelijkheid om de service uit te schakelen of u ervan af te melden. Er is momenteel geen downgrade-pad van HCX Enterprise naar HCX Advanced. Klanten die een downgrade willen uitvoeren, dienen een nieuwe implementatie uit te voeren, waarbij op downtime moet worden gerekend.

Neem eerst de gedeelten [Voordat u begint](#before-you-begin), [Vereisten voor de softwareversie](#software-version-requirements) en [Vereisten](#prerequisites) van dit artikel door. 

Vervolgens worden alle benodigde procedures doorlopen voor het volgende:

> [!div class="checklist"]
> * De on-premises VMware HCX OVA (HCX-connector) implementeren.
> * Activeer de VMware HCX-connector.
> * Koppel uw on-premises HCX-connector aan uw Azure VMware Solution HCX-cloudbeheerder.
> * Configureer de interconnect (netwerkprofiel, rekenprofiel en service-mesh).
> * Voltooi de installatie door te controleren wat de apparaatstatus is en na te gaan of migratie mogelijk is.

Na afloop kunt u de aanbevolen volgende stappen aan het einde van dit artikel volgen.  

## <a name="before-you-begin"></a>Voordat u begint
   
* Bekijk de [reeks eenvoudige zelfstudies](tutorial-network-checklist.md) over Azure VMware Solution Software Defined Datacenter (SDDC).
* Bekijk en raadpleeg de [VMware HCX-documentatie](https://docs.vmware.com/en/VMware-HCX/index.html), met inbegrip van de gebruikershandleiding voor HCX.
* Bekijk het VMware-document [Virtuele machines migreren met VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Bekijk eventueel [Implementatieoverwegingen voor VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Bekijk eventueel gerelateerde VMware-materialen over HCX, zoals de [reeks blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) van VMware vSphere. 
* U kunt eventueel een activering voor Azure VMware Solution HCX Enterprise aanvragen via de ondersteuningskanalen van Azure VMware Solution.
* U kunt optioneel [vereiste netwerkpoorten voor HCX controleren](https://ports.vmware.com/home/VMware-HCX).
* Hoewel de cloudbeheerder van Azure VMware Solution HCX vooraf is geconfigureerd vanuit het /22-netwerk dat wordt geleverd voor de Azure VMware Solution-privécloud, moet u voor de on-premises HCX-connector netwerkbereiken toewijzen vanuit uw on-premises netwerk. Deze netwerken en bereiken worden verderop in dit artikel beschreven.

Het bepalen van de grootte van workloads met reken- en opslagresources is een essentiële planningsstap. Handel de stap voor het bepalen van de grootte af als onderdeel van de eerste planning van de privécloudomgeving. 

U kunt workloads vergroten of verkleinen door een [evaluatie van de Azure VMware Solution](../migrate/how-to-create-azure-vmware-solution-assessment.md) in de Azure Migrate-portal uit te voeren.

## <a name="software-version-requirements"></a>Vereisten voor de softwareversie

Voor de infrastructuuronderdelen moet de vereiste minimumversie worden uitgevoerd. 
                                                         
| Onderdeeltype    | Vereisten voor bronomgeving    | Vereisten voor de doelomgeving   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Als u 5.5 U1 of eerder gebruikt, gebruikt u de zelfstandige HCX-gebruikersinterface voor HCX-bewerkingen.  | 6.0 U2 en hoger   |
| ESXi   | 5,0    | ESXi 6.0 en hoger   |
| NSX    | Voor HCX-netwerkextensie van logische switches in de bron: NSXv 6.2 + of NSX-T 2.4 +.   | NSXv 6.2 + of NSX-T 2.4 +<br/><br/>Voor HCX-nabijheidsroutering: NSXv 6.4+. (Nabijheidsroutering wordt niet ondersteund bij NSX-T) |
| vCloud Director   | Niet vereist. Er is geen interoperabiliteit met vCloud Director op de bronsite. | Wanneer u de doelomgeving met vCloud Director integreert, is de minimumversie 9.1.0.2.  |

## <a name="prerequisites"></a>Vereisten

### <a name="network-and-ports"></a>Netwerk en poorten

* Configureer [ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) tussen on-premises en Azure VMware Solution SDDC ExpressRoute-circuits.

* [Alle vereiste poorten](https://ports.vmware.com/home/VMware-HCX) moeten zijn geopend voor communicatie tussen on-premises onderdelen en Azure VMware Solution SDDC.

Zie de [documentatie bij VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html) voor meer informatie.


### <a name="ip-addresses"></a>IP-adressen

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>De VMware HCX Connector OVA on-premises implementeren

> [!NOTE]
> Voordat u het virtuele apparaat in uw on-premises vCenter implementeert, moet u de VMware HCX Connector OVA downloaden. 

1. Open een browservenster, meld u aan bij de Azure VMware Solution HCX Manager op `https://x.x.x.9`poort 443 met de gebruikersreferenties van **cloudbeheerder** en ga vervolgens naar **Ondersteuning**.

   > [!TIP]
   > Noteer het IP-adres van de HCX-cloudbeheerder in de Azure VMware Solution. U kunt het IP-adres identificeren door op de blade Azure VMware Solution naar **Manage** > **Connectivity** te gaan en vervolgens het tabblad **HCX** te selecteren. 
   >
   >Het vCenter-wachtwoord is gedefinieerd tijdens het instellen van de privécloud.

1. Selecteer de **downloadkoppeling** om het VMware HCX Connector OVA -bestand te downloaden.

1. Ga naar uw on-premises vCenter. Selecteer een OVF-sjabloon. Dit is het OVA-bestand dat u hebt gedownload om de HCX-connector te implementeren in uw on-premises vCenter.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Schermopname van bladeren naar een OVF-sjabloon." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Selecteer een naam en locatie en vervolgens een resource of cluster waar u de HCX-connector implementeert. Bekijk vervolgens de details en de vereiste resources.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Schermopname van het controleren van details van de sjabloon." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Bekijk de licentievoorwaarden. Als u akkoord gaat, selecteert u de vereiste opslag en het gewenste netwerk en selecteert u vervolgens **Next**.

1. Voer in **Customize template** alle vereiste gegevens in. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Schermopname van de vakken voor het aanpassen van een sjabloon." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Selecteer **Next** , controleer de configuratie en selecteer **Finish** om de HCX-connector OVA te implementeren.
     
   > [!NOTE]
   > Over het algemeen wordt de VMware HCX-connector die u nu implementeert, geïmplementeerd in het beheernetwerk van het cluster.  
   
   > [!IMPORTANT]
   > Mogelijk moet u het virtuele apparaat handmatig inschakelen.  Wacht in dat geval 10 tot 15 minuten voordat u verdergaat met de volgende stap.

Bekijk voor een volledig overzicht van deze procedure de video [Azure VMware Solution: HCX Appliance Deployment](https://www.youtube.com/embed/BwSnQeefnso). 


## <a name="activate-vmware-hcx"></a>VMware HCX activeren

Nadat u de VMware HCX-connector OVA on-premises hebt geïmplementeerd en het apparaat hebt gestart, bent u klaar om te activeren. U moet eerst een licentiesleutel ophalen van de Azure VMware Solution-portal.

1. Ga in de Azure VMware Solution-portal naar **Beheren** > **Connectiviteit** , selecteer het tabblad **HCX** en selecteer **Toevoegen**.

1. Gebruik de **admin** -gebruikersreferenties om u aan te melden bij de on-premises VMware HCX Manager via `https://HCXManagerIP:9443`. 

   > [!IMPORTANT]
   > Zorg ervoor dat u het `9443`-poortnummer met het IP-adres van VMware HCX Manager toevoegt.

1. Voer bij **Licensing** uw sleutel voor **HCX Advanced Key** in.  
   
    > [!NOTE]
    > VMware HCX Manager moet open internettoegang hebben of er moet een proxy voor zijn geconfigureerd.

1. Geef in **Datacenter Location** de dichtstbijzijnde locatie op voor on-premises installatie van VMware HCX Manager.

1. Wijzig de naam bij **System Name** of accepteer de standaardwaarde.
   
1. Selecteer **Yes, Continue**.
    
1. Geef in **Connect your vCenter** de FQDN of het IP-adres van uw vCenter-server en de juiste referenties op en selecteer vervolgens **Continue**.
   
1. Geef in **Configure SSO/PSC** de FQDN of het IP-adres van uw Platform Services Controller op en selecteer **Continue**.
   
   >[!NOTE]
   >Normaal gesproken is de invoer hier hetzelfde als uw FQDN of uw IP-adres voor vCenter.

1. Controleer of alle invoerwaarden juist zijn en selecteer **Restart**.
    
   > [!NOTE]
   > Er vindt een vertraging plaats na het opnieuw opstarten voordat u wordt gevraagd om de volgende stap.

Nadat de services opnieuw zijn opgestart, is het belangrijk dat vCenter groen wordt weergegeven op het scherm dat verschijnt. Zowel vCenter als SSO moeten de juiste configuratieparameters hebben. Deze moeten hetzelfde zijn als in het vorige scherm.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Schermopname van het dashboard met de status groen voor vCenter." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Bekijk voor een volledig overzicht van deze procedure de video [Azure VMware Solution: Activeer de video over HCX ](https://www.youtube.com/embed/BkAV_TNYxdE).


## <a name="configure-the-vmware-hcx-connector"></a>VMware HCX-connector configureren

Nu bent u klaar om een sitekoppeling toe te voegen, een netwerk- en rekenprofiel te maken en services, zoals migratie, netwerkextensie of herstel na noodgevallen, in te schakelen. 

### <a name="add-a-site-pairing"></a>Een sitekoppeling toevoegen

U kunt de VMware HCX-cloudbeheerder in Azure VMware Solution verbinden (koppelen) met de VMware HCX-connector in uw datacentrum. 

1. Meld u aan bij uw on-premises vCenter en selecteer onder **Home** de optie **HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Schermopname van de vCenter-client met HCX geselecteerd tussen de snelkoppelingen." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. Selecteer onder **Infrastructure** de optie **Site Pairing** en selecteer vervolgens de optie **Connect To Remote Site** (in het midden van het scherm). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Schermopname van selecties voor het maken van een externe site." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Voer de eerder genoteerde URL of het eerder genoteerde IP-adres van de externe HCX, de gebruikersnaam en het wachtwoord van de Azure VMware Solution cloudadmin@vsphere.local in. Selecteer vervolgens **Connect**.

   > [!NOTE]
   > Voor een sitekoppeling moet uw HCX-connector het IP-adres van uw HCX-cloudbeheerder kunnen omleiden via poort 443.
   >
   > Het wachtwoord is hetzelfde dat u hebt gebruikt om u aan te melden bij vCenter. U hebt dit wachtwoord op het eerste implementatiescherm gedefinieerd.

   Er wordt een scherm weergegeven met een verbinding (koppeling) tussen uw HCX-cloudbeheerder in Azure VMware Solution en uw on-premises HCX-connector.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Schermopname van de koppeling van HCX manager in Azure VMware Solution en de HCX-connector.":::

Bekijk voor een volledig overzicht van deze procedure de video [Azure VMware Solution: Video over HCX-sitekoppeling](https://www.youtube.com/embed/sKizDCRHOko).



### <a name="create-network-profiles"></a>Netwerkprofielen maken

VMware HCX implementeert een subset virtuele apparaten (geautomatiseerd) waarvoor meerdere IP-segmenten zijn vereist. Wanneer u uw netwerkprofielen maakt, definieert u de IP-segmenten die u hebt geïdentificeerd tijdens de [Voorbereidings- en planningsfase van VMware HCX-netwerksegmenten vóór de implementatie](production-ready-deployment-steps.md#vmware-hcx-network-segments).

U gaat vier netwerkprofielen maken:

   - Beheer
   - vMotion
   - Replicatie
   - Uplink

1. Onder **Infrastructure** selecteert u **Interconnect** > **Multi-Site Service Mesh** > **Network Profiles** > **Create Network Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Schermopname van selecties om een netwerkprofiel te gaan maken." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Voor elk netwerkprofiel selecteert u het netwerk en de poortgroep, geeft u een naam op en maakt u de IP-adresgroep voor dat segment. Selecteer vervolgens **Maken**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Schermopname van details voor een nieuw netwerkprofiel.":::

Bekijk voor een volledig overzicht van deze procedure de video [Azure VMware Solution: Video voor HCX-netwerkprofiel](https://www.youtube.com/embed/NhyEcLco4JY).


### <a name="create-a-compute-profile"></a>Een rekenprofiel maken

1. Selecteer **Compute Profiles** > **Create Compute Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Schermopname van de selecties om een rekenprofiel te gaan maken." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Voer een naam in voor het profiel en selecteer **Continue**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Schermopname met invoer van de naam van een rekenprofiel en de knop Continue." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Selecteer de services die u wilt inschakelen, zoals migratie, netwerkextensie of herstel na noodgevallen, en selecteer **Continue**.
  
   > [!NOTE]
   > Over het algemeen verandert hier niets.

1. Selecteer in **Select Service Resources** een of meer serviceresources (clusters) om de geselecteerde VMware HCX-services in te schakelen.  

1. Wanneer u de clusters in uw on-premises datacentrum ziet, selecteert u **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Schermopname van de geselecteerde serviceresources en de knop Continue." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Selecteer in **Select Datastore** de gegevensopslagresource voor de implementatie van de VMware HCX Interconnect-apparaten. Selecteer vervolgens **Doorgaan**.

   Wanneer er meerdere resources zijn geselecteerd, gebruikt VMware HCX de eerste resource die is geselecteerd tot de capaciteit is uitgeput.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Schermopname van een geselecteerde resource voor gegevensopslag en de knop Continue." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Selecteer bij **Select Management Network Profile** het beheernetwerkprofiel dat u in de vorige stappen hebt gemaakt. Selecteer vervolgens **Doorgaan**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Schermopname van de selectie van een beheernetwerkprofiel en de knop Continue." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Met het beheernetwerkprofiel kan het VMware HCX-apparaat communiceren met vCenter. De ESXi-hosts kunnen via dit profiel worden bereikt.

1. Selecteer bij **Select Uplink Network Profile** het Uplink-netwerkprofiel dat u in de vorige stappen hebt gemaakt. Selecteer vervolgens **Doorgaan**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Schermopname van de selectie van een beheernetwerkprofiel en de knop Continue." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Selecteer bij **Select vMotion Network Profile** het vMotion-netwerkprofiel dat u in de vorige stappen hebt gemaakt. Selecteer vervolgens **Doorgaan**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Schermopname van de selectie van een vMotion-netwerkprofiel en de knop Continue." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Selecteer bij **Select vSphere Replication Network Profile** het replicatienetwerkprofiel dat u in de vorige stappen hebt gemaakt. Selecteer vervolgens **Doorgaan**.

   In de meeste gevallen is het replicatienetwerkprofiel hetzelfde als het beheernetwerkprofiel.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Schermopname van de selectie van een replicatienetwerkprofiel en de knop Continue." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Selecteer bij **Select Distributed Switches for Network Extensions** de gedistribueerde virtuele switches die de virtuele machines bevatten die moeten worden gemigreerd naar de Azure VMware Solution op een uitgebreid netwerk in laag 2. Selecteer vervolgens **Doorgaan**.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Schermopname van de selectie van gedistribueerde virtuele switches en de knop Continue." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Controleer de verbindingsregels en selecteer **Continue**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Schermopname van de verbindingsregels en de knop Continue." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Selecteer **Finish** om het rekenprofiel te voltooien.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Schermopname van gegevens van een rekenprofiel." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Bekijk voor een volledig overzicht van deze procedure de video [Azure VMware Solution: Video van rekenprofiel](https://www.youtube.com/embed/qASXi5xrFzM).

### <a name="create-a-service-mesh"></a>Een service-mesh maken

Het is nu tijd om een service-mesh tussen on-premises en Azure VMware Solution SDDC te configureren.

   > [!NOTE]
   > Een service-mesh maken met Azure VMware Solution:
   >
   > Poorten UDP 500/4500 zijn geopend tussen uw on-premises HCX-connector-gedefinieerde 'uplink'-netwerkprofieladressen en de 'uplink'-netwerkprofieladressen voor Azure VMware Solution HCX Cloud.
   >
   > Vergeet niet [De voor HCX vereiste poorten](https://ports.vmware.com/home/VMware-HCX) te raadplegen.

1. Selecteer onder **Infrastructure** achtereenvolgens **Interconnect** > **Service Mesh** > **Create Service Mesh**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Schermopname van selecties om een service-mesh te gaan maken." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Controleer de sites die vooraf zijn ingevuld en selecteer **Continue**. 

   >[!NOTE]
   >Als dit uw eerste service-meshconfiguratie is, hoeft u dit scherm niet te wijzigen.  

1. Selecteer de bron en externe rekenprofielen in de vervolgkeuzelijsten en selecteer **Continue**.  

   De selecties definiëren de resources waarin VM's VMware HCX-services kunnen gebruiken.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Schermopname van het selecteren van het bronrekenprofiel." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Schermopname van het selecteren van het externe rekenprofiel." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Controleer de services die worden ingeschakeld en selecteer **Continue**.  

1. Selecteer in **Advanced Configuration - Override Uplink Network profiles** de optie **Continue**.  

   Uplink-netwerkprofielen maken verbinding met het netwerk via welk de verbindingsapparaten van de externe site kunnen worden bereikt.  
  
1. Controleer de gegevens in **Advanced Configuration - Network Extension Appliance Scale Out** en selecteer **Continue**. 

1. Controleer de gegevens in **Advanced Configuration - Traffic Engineering** , breng de benodigde wijzigingen aan en selecteer **Continue**.

1. Bekijk de preview van de topologie en selecteer **Continue**.

1. Voer een gebruiksvriendelijke naam in voor deze service-mesh en selecteer **Finish** om de bewerking te voltooien.  

1. Selecteer **View Tasks** om de implementatie te controleren. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Schermopname met de knop voor het bekijken van taken.":::

   Wanneer de implementatie van de service-mesh is voltooid, worden de services groen weergegeven.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Schermopname met groene indicatoren bij services." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Verifieer de status van de service-mesh door de status van het apparaat te controleren. 
1. Selecteer **Interconnect** > **Appliances**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Schermopname met selecties voor het controleren van de status van het apparaat." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Bekijk voor een volledig overzicht van deze procedure de video [Azure VMware Solution: Video over service-mesh](https://www.youtube.com/embed/FyZ0d3P_T24).



### <a name="optional-create-a-network-extension"></a>(Optioneel) Een netwerkextensie maken

Als u netwerken van uw on-premises omgeving wilt uitbreiden naar Azure VMware-oplossing, voert u de volgende stappen uit:

1. Selecteer onder **Services** de optie **Network Extension** en selecteer vervolgens **Create a Network Extension**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Schermopname van de selecties om een rekenprofiel te gaan maken." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Selecteer elk netwerk dat u wilt uitbreiden naar Azure VMware Solution en selecteer **Next**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Schermopname van de selectie van een netwerk.":::

1. Voer het IP-adres van de on-premises gateway in voor elk van de netwerken die u uitbreidt en selecteer vervolgens **Submit**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Schermopname van de vermelding van een gateway-IP-adres.":::

   Het duurt enkele minuten voordat de netwerkextensie is voltooid. Wanneer dit het geval is, ziet u dat de status is gewijzigd in **extension complete**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Schermopname van de status van Extensie voltooid." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Bekijk voor een volledig overzicht van deze stap de video [Azure VMware Solution Video van netwerkextensie](https://www.youtube.com/embed/cNlp0f_tTr0).


## <a name="next-steps"></a>Volgende stappen

Als u dit punt bereikt en de tunnelstatus van het apparaat **UP** en groen is, kunt u virtuele machines van Azure VMware Solution migreren en beveiligen met behulp van VMware HCX. De Azure VMware Solution ondersteunt migraties van workloads (met of zonder een netwerkextensie). U kunt nog steeds workload migreren in uw vSphere-omgeving, naast het on-premises maken van netwerken en de implementatie van virtuele machines op die netwerken.  

Ga naar de technische documentatie voor VMware voor meer informatie over het gebruik van HCX:

* [Documentatie voor VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Virtuele machines migreren met VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* [Vereiste poorten voor HCX](https://ports.vmware.com/home/VMware-HCX)
