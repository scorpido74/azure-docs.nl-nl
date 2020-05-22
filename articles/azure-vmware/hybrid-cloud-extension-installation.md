---
title: Hybrid Cloud extension (HCX) installeren
description: De VMWare Hybrid Cloud extension (HCX)-oplossing instellen voor uw persoonlijke cloud van Azure VMWare-oplossing (AVS)
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: 74bc0dece2fc565aebc6e5301ef7a36187d6a250
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83776473"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>HCX voor Azure VMWare-oplossing installeren

In dit artikel gaan we procedures volgen voor het instellen van de VMWare Hybrid Cloud extension (HCX)-oplossing voor uw Azure VMWare-oplossing (AVS) Private Cloud. HCX Advanced (standaard installatie) ondersteunt Maxi maal drie externe sites, waarbij elke externe site vereist dat een HCX Enter prise Manager of connector moet worden geïnstalleerd en geactiveerd.
HCX maakt de migratie van uw VMware-workloads naar de Cloud en of andere verbonden sites mogelijk via verschillende ingebouwde HCX ondersteunde migratie typen. Als er meer dan drie sites zijn vereist, hebben klanten de mogelijkheid om de HCX Enter prise-invoeg toepassing via ondersteuning in te scha kelen. HCX Enter prise heeft extra kosten voor klanten na algemene Beschik baarheid (GA), maar biedt [extra functies](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).

Controleer [voordat u](#before-you-begin)aan de slag gaat, vereisten voor de [Software versie](#software-version-requirements)en de [vereisten eerst.](#prerequisites) 

Vervolgens worden alle nood zakelijke procedures door lopen:

> [!div class="checklist"]
> * De on-premises HCXe eicellen implementeren
> * HCX activeren en configureren
> * Netwerk-uplink en service-net configureren
> * Voltooi de installatie door de status van het apparaat te controleren

Nadat de installatie is voltooid, worden de aanbevolen volgende stappen gegeven.

## <a name="before-you-begin"></a>Voordat u begint
    
* Bekijk de [zelf studie reeks](tutorial-network-checklist.md) Basic AVS Software Defined Data Center (SDDC)
* Bekijk en Raadpleeg de [documentatie van VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) , met inbegrip van de gebruikers handleiding voor HCX
* De migratie van VMware-documenten controleren [virtual machines met VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* Bekijk eventueel [overwegingen voor VMware HCX-implementaties](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* Bekijk eventueel gerelateerde VMware-materialen op HCX, zoals de VMware vSphere [blog serie](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) op HCX. 
* Bestel een AVS HCX Enter prise Activation via AVS-ondersteunings kanalen.

Het aanpassen van werk belastingen met reken-en opslag resources is een essentiële plannings stap bij het voorbereiden van het gebruik van de HCX-oplossing van de AVS-Privécloud. Deze stap van grootte moet worden geadresseerd als onderdeel van de eerste planning van de particuliere cloud omgeving. 

## <a name="software-version-requirements"></a>Software versie vereisten
Voor de infrastructuur onderdelen moet de vereiste minimum versie worden uitgevoerd. 
                                                         
| Onderdeel type                                                          | Vereisten voor de bron omgeving                                                                   | Vereisten voor de doel omgeving                                                                      |
| --- | --- | --- |
| vCenter Server                                                          | 5.1<br/><br/>Als u 5,5 U1 of eerder gebruikt, gebruikt u de zelfstandige HCX-gebruikers interface voor HCX-bewerkingen.         | 6,0 U2 en hoger                                                                                          |
| ESXi                                                                    | 5.0                                                                                               | ESXi 6,0 en hoger                                                                                        |
| NSX                                                                     | Voor HCX netwerk extensie van logische switches op de bron: NSXv 6.2 + of NSX-T 2.4 +              | NSXv 6.2 + of NSX-T 2.4 +<br/><BR/voor HCX nabij route ring: NSXv 6.4 + (proximity Routing wordt niet ondersteund met NSX-T) |
| vCloud Director                                                         | Niet vereist-geen interoperabiliteit met vCloud Director op de bron site | Wanneer de doel omgeving is geïntegreerd met vCloud Director, is de minimale waarde 9.1.0.2.              |

## <a name="prerequisites"></a>Vereisten

* Wereld wijd bereik moet worden geconfigureerd tussen on-premises en AVS SDDC-circuits.

* Alle vereiste poorten moeten open zijn tussen on-premises en AVS SDDC (Zie [VMware HCX-documentatie](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Eén IP-adres voor HCX Manager op on-premises en een minimum van twee IP-adressen voor Interconnect (IX) en netwerk extensie (NE) apparaat.

* On-premises HCX IX en NE apparaten moeten een vCenter-en ESXi-infra structuur kunnen bereiken.

* Voor het implementeren van het WAN Interconnect-apparaat moet u naast het/22 CIDR-netwerk adres blok dat wordt gebruikt voor de implementatie van SDDC in de Azure Portal, HCX een/29-blok vereist. U moet dit in uw netwerk plannen.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>De VMware HCX-eicellen on-premises implementeren

1. Meld u aan bij AVS SDDC vCenter en selecteer **HCX**.

    ![HCX in naar AVS vCenter selecteren](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. Als u het VMware HCX eicellen-bestand wilt downloaden, selecteert u **beheerders**  >  **systeem updates**.

    ![Systeem updates ophalen](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. Selecteer een OVF-sjabloon om te implementeren op on-premises vCenter.  

    ![OVF-sjabloon selecteren](./media/hybrid-cloud-extension-installation/select-template.png)

1. Selecteer een naam en locatie en selecteer vervolgens een resource/cluster waar HCX moet worden geïmplementeerd. Controleer vervolgens de details en de vereiste resources.  

    ![Details van sjabloon controleren](./media/hybrid-cloud-extension-installation/configure-template.png)

1. Raadpleeg de licentie voorwaarden en als u akkoord gaat, selecteert u vereiste opslag en netwerk. Selecteer vervolgens **Volgende**.

1. Voer in **sjabloon aanpassen**alle vereiste gegevens in. 

    ![Sjabloon aanpassen](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. Selecteer **volgende**, Controleer de configuratie en selecteer **volt ooien** om HCXe eicellen te implementeren.

## <a name="activate-hcx"></a>HCX activeren

Na de installatie voert u de volgende stappen uit.

1. Open HCX Manager op `https://HCXManagerIP:9443` en meld u aan met uw gebruikers naam en wacht woord. 

1. Voer in **licentie verlening**uw **Geavanceerde HCX-sleutel**in.  

    ![Voer de HCX-sleutel in](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX manager moet open Internet toegang of een proxy-configuratie hebben.

1. VCenter configureren.

    ![VCenter configureren](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. Bewerk op de locatie van het **Data Center**, indien nodig, de locatie van het Data Center.

    ![Database locatie](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>HCX configureren 

1. Meld u aan bij on-premises vCenter en selecteer vervolgens **Start**  >  **HCX**.

    ![HCX in VCenter](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. Selecteer **infra structuur**  >  **site koppelen**  >  **een site koppeling toevoegen**.

    ![Site koppeling toevoegen](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. Voer de URL, de **gebruikers naam**en het **wacht woord**voor de **externe HCX**in. Selecteer vervolgens **Verbinding maken**.

   Het systeem geeft de verbonden site weer.
   
    ![Site verbinding](./media/hybrid-cloud-extension-installation/site-connection.png)

1. Selecteer **interconnect**  >  **multi-site service net-**  >  **netwerk profielen**  >  **netwerk profiel maken**.

    ![Netwerk profiel maken](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. Voer HCX IX en NE IP-adresbereiken in (mini maal 2 IP-adressen zijn vereist voor IX en NE apparaten).
    
   ![IP-adresbereiken invoeren](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > Het netwerk extensie apparaat (HCX-NE) heeft een een-op-een-relatie met een gedistribueerde virtuele switch (DVS).  

1. **Selecteer nu reken profiel**Create COMPUTE  >  **profile**.

1. Voer een naam voor het reken profiel in en selecteer **door gaan**.  

    ![Reken profiel maken](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. Selecteer Services om in te scha kelen, zoals migratie, netwerk extensie, herstel na nood gevallen. Selecteer **Doorgaan**.

    ![Services selecteren](./media/hybrid-cloud-extension-installation/select-services.png)

1. Selecteer in **service resources selecteren**een of meer service resources waarvoor de geselecteerde HCX services moeten worden ingeschakeld. Selecteer **Doorgaan**.
    
   ![Service resources selecteren](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > Selecteer specifieke clusters waarin de bron-Vm's zijn gericht op migratie met behulp van HCX.

1. Selecteer **Data Store** en selecteer **door gaan**. 
      
    Selecteer elke Compute-en opslag resource voor het implementeren van de HCX Interconnect-apparaten. Wanneer er meerdere resources zijn geselecteerd, gebruikt HCX de eerste resource die is geselecteerd tot de capaciteit is uitgeput.  
    
    ![Implementatie resources selecteren](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. Selecteer het management-netwerk profiel dat is gemaakt in **netwerk profielen** en selecteer **door gaan**.  
      
    Selecteer het netwerk profiel waarmee de beheer interface van vCenter en de ESXi-hosts kunnen worden bereikt. Als u een dergelijk netwerk profiel nog niet hebt gedefinieerd, kunt u dit hier maken.  
    
    ![Beheer netwerk profiel selecteren](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. Selecteer **Network-uplink** en selecteer **door gaan**.
      
    Selecteer een of meer netwerk profielen, zodat een van de volgende voor waarden waar is:  
    * De Interconnect-apparaten op de externe site kunnen worden bereikt via dit netwerk  
    * De externe apparaten kunnen het lokale Interconnect-toestel bereiken via dit netwerk.  
    
    Als u Point-to-Point-netwerken zoals Direct Connect die niet worden gedeeld op meerdere sites, kunt u deze stap overs Laan, omdat reken profielen worden gedeeld met meerdere sites. In dergelijke gevallen kunnen Uplinkpoortprofiel worden overschreven en opgegeven tijdens het maken van het Connect-service-net.  
    
    ![Uplinkpoortprofiel selecteren](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. Selecteer **vMotion-netwerk profiel** en selecteer **door gaan**.
      
    Selecteer het netwerk profiel waarmee de vMotion-interface van de ESXi-hosts kan worden bereikt. Als u een dergelijk netwerk profiel nog niet hebt gedefinieerd, kunt u dit hier maken. Als u geen vMotion-netwerk hebt, selecteert u **beheer netwerk profiel**.  
    
    ![Netwerk profiel van vMotion selecteren](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. Selecteer **vSphere-replicatie netwerk profiel** en selecteer **door gaan**.
      
    Selecteer een netwerk profiel waarlangs de vSphere-replicatie interface van ESXi-hosts kan worden bereikt. In de meeste gevallen is dit profiel hetzelfde als het management-netwerk profiel.  
    
    ![VSphere-netwerk profiel voor replicatie selecteren](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. Selecteer **gedistribueerde switches voor netwerk uitbreidingen** en selecteer **door gaan**.  
      
    Selecteer de gedistribueerde virtuele switches waarvoor u netwerken hebt waarvan de Virtual Machines die worden gemigreerd, zijn verbonden.

    ![Gedistribueerde virtuele switches selecteren](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. Controleer de verbindings regels en selecteer **door gaan**. Selecteer **volt ooien** om het reken profiel te maken.  

    ![Reken profiel maken](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

## <a name="configure-network-uplink"></a>Netwerk-uplink configureren

Configureer nu de wijziging in het netwerk profiel in AVS SDDC voor Network Uplink.

1. Meld u aan bij SDDC NSX-T om een nieuwe logische switch te maken, of gebruik een bestaande logische switch die kan worden gebruikt voor netwerk-uplink tussen on-premises en AVS SDDC.

1. Maak een netwerk profiel voor HCX-uplink in AVS SDDC dat kan worden gebruikt voor on-premises om SDDC-communicatie te AVS.  
    
   ![Netwerk profiel voor uplink maken](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. Voer een naam in voor het netwerk profiel en ten minste 4-5 vrije IP-adressen op basis van de L2-netwerk extensie vereist.  
    
   ![Netwerk profiel voor uplink configureren](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. Selecteer **maken** om de AVS SDDC-configuratie te volt ooien

## <a name="configure-service-mesh"></a>Service-net configureren

Configureer nu het service-net tussen on-premises en AVS SDDC.

1. Meld u aan bij AVS SDDC vCenter en selecteer **HCX**.

1. Selecteer **Infrastructure**  >  service-**Interconnect**  >  **Service Mesh**  >  **mesh maken**in het infrastructuur Interconnect Service-net.  Configureer het netwerk en de reken profielen die u in de vorige stappen hebt gemaakt.    
      
    ![Service-net configureren](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

1. Selecteer **service-net maken** en selecteer **door gaan**.  
      
    Selecteer gekoppelde sites waartussen hybride mobiliteit moet worden ingeschakeld.  
    
    ![Gekoppelde sites selecteren](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

1. Selecteer **reken profiel** en selecteer **door gaan**.
      
    Selecteer één reken profiel in de bron-en externe sites om hybridity Services in te scha kelen. Met de selecties worden de resources gedefinieerd, waar Virtual Machines HCX-Services kunt gebruiken.  
      
    ![Hybridisatie Services inschakelen](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

1. Selecteer de services die moeten worden ingeschakeld voor HCX en selecteer **door gaan**.  
      
    ![HCX services selecteren](./media/hybrid-cloud-extension-installation/hcx-services.png)

1. Selecteer **door gaan**in **Geavanceerde configuratie-opheffen van Uplink-netwerk profielen** .  
      
    Uplink-netwerk profielen worden gebruikt om verbinding te maken met het netwerk via welke de verbindings apparaten van de externe site kunnen worden bereikt.  
      
    ![Uplinkpoortprofiel overschrijven](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

1. In **Geavanceerde configuratie: netwerk extensie toestel uitschalen**, selecteert **u Uitschalen van netwerk extensie apparaat configureren**. 
      
    ![Uitschalen netwerk extensie](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

1. Voer het aantal toestellen in dat overeenkomt met het aantal DVS-switches.  
      
    ![Aantal apparaten configureren](./media/hybrid-cloud-extension-installation/appliance-scale.png)

1. Selecteer **door gaan**in **Geavanceerde configuratie van verkeer**.  
      
    ![Verkeers techniek configureren](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

1. Bekijk de preview van de topologie en selecteer **door gaan**. Voer vervolgens een gebruiks vriendelijke naam in voor deze service mesh en selecteer **volt ooien** om te volt ooien.  
      
    ![Service-net volt ooien](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    Het service-net wordt geïmplementeerd en geconfigureerd.  
      
    ![Geïmplementeerd service-net](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>Status van apparaat controleren
Als u de status van het apparaat wilt controleren, selecteert u **interconnect**  >  **Appliances**. 
      
![Status van apparaat](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>Volgende stappen

Wanneer de status van het apparaat Connect **tunnel** is **ingesteld** op groen, bent u klaar om AVS-vm's te migreren en beveiligen met behulp van HCX. Zie [VMware HCX-documentatie](https://docs.vmware.com/en/VMware-HCX/index.html) en [Migreer virtual machines met VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) in de technische documentatie van VMware.
