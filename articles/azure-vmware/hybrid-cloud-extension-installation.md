---
title: Hybrid Cloud extension (HCX) installeren
description: De VMware Hybrid Cloud extension (HCX)-oplossing instellen voor uw persoonlijke cloud van Azure VMware-oplossing (AVS)
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: b897a44fb6811c4e3564c59a8ab2c064506f0a4f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539156"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>HCX voor Azure VMware-oplossing installeren

In dit artikel worden de procedures beschreven voor het instellen van de VMWare Hybrid Cloud extension (HCX)-oplossing voor uw Azure VMWare-oplossing (AVS) privécloud. HCX maakt de migratie van uw VMware-workloads naar de Cloud en andere verbonden sites mogelijk via verschillende ingebouwde HCX ondersteunde migratie typen.

HCX Advanced, de standaard installatie, ondersteunt Maxi maal drie vCenter. Als er meer dan drie zijn vereist, hebben klanten de mogelijkheid om de HCX Enter prise-invoeg toepassing via ondersteuning in te scha kelen. HCX Enter prise-installatie bevat extra kosten voor klanten na algemene Beschik baarheid (GA), maar biedt [extra functies](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).


Controleer [voordat u](#before-you-begin)aan de slag gaat, vereisten voor de [Software versie](#software-version-requirements)en de [vereisten eerst.](#prerequisites) 

Vervolgens worden alle nood zakelijke procedures door lopen:

> [!div class="checklist"]
> * De on-premises HCXe eicellen implementeren
> * HCX activeren en configureren
> * Netwerk-uplink en service-net configureren
> * Voltooi de installatie door de status van het apparaat te controleren

Nadat de installatie is voltooid, kunt u de aanbevolen vervolg stappen volgen die aan het einde van dit artikel worden gegeven.  

## <a name="before-you-begin"></a>Voordat u begint
    
* Bekijk de [zelf studie reeks](tutorial-network-checklist.md) Basic AVS Software Defined Data Center (SDDC)
* Bekijk en Raadpleeg de [documentatie van VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) , met inbegrip van de gebruikers handleiding voor HCX
* De migratie van VMware-documenten controleren [virtual machines met VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* Bekijk eventueel [overwegingen voor VMware HCX-implementaties](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* Bekijk eventueel gerelateerde VMware-materialen op HCX, zoals de VMware vSphere [blog serie](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) op HCX. 
* Bestel een AVS HCX Enter prise Activation via AVS-ondersteunings kanalen.

Het aanpassen van werk belastingen met reken-en opslag resources is een essentiële plannings stap bij het voorbereiden van het gebruik van de HCX-oplossing van de AVS-Privécloud. Adresseer de grootte stap als onderdeel van de eerste planning van de particuliere cloud omgeving.   

## <a name="software-version-requirements"></a>Software versie vereisten
Voor de infrastructuur onderdelen moet de vereiste minimum versie worden uitgevoerd. 
                                                         
| Onderdeel type    | Vereisten voor de bron omgeving    | Vereisten voor de doel omgeving   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Als u 5,5 U1 of eerder gebruikt, gebruikt u de zelfstandige HCX-gebruikers interface voor HCX-bewerkingen.  | 6,0 U2 en hoger   |
| ESXi   | 5.0    | ESXi 6,0 en hoger   |
| NSX    | Voor HCX netwerk extensie van logische switches op de bron: NSXv 6.2 + of NSX-T 2.4 +   | NSXv 6.2 + of NSX-T 2.4 +<br/><br/>Voor HCX nabij route ring: NSXv 6.4 + (proximity Routing wordt niet ondersteund met NSX-T) |
| vCloud Director   | Niet vereist-geen interoperabiliteit met vCloud Director op de bron site | Wanneer u de doel omgeving met vCloud Director integreert, is de minimale 9.1.0.2.  |

## <a name="prerequisites"></a>Vereisten

* Wereld wijd bereik moet worden geconfigureerd tussen on-premises en AVS SDDC-circuits.

* Alle vereiste poorten moeten open zijn tussen on-premises en AVS SDDC (Zie [VMware HCX-documentatie](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Eén IP-adres voor HCX Manager op on-premises en een minimum van twee IP-adressen voor Interconnect (IX) en netwerk extensie (NE) apparaat.

* On-premises HCX IX en NE apparaten moeten een vCenter-en ESXi-infra structuur kunnen bereiken.

* Voor het implementeren van het WAN Interconnect-apparaat moet u naast het/22 CIDR-netwerk adres blok dat wordt gebruikt voor de implementatie van SDDC in de Azure Portal, HCX een/29-blok vereist. Zorg ervoor dat u deze vereiste in uw netwerk planning confactoreert.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>De VMware HCX-eicellen on-premises implementeren

1. Meld u aan bij AVS SDDC vCenter en selecteer **HCX**.

    ![HCX in naar AVS vCenter selecteren](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. Selecteer onder **beheer** **systeem updates** en selecteer vervolgens **Download koppeling aanvragen** om het VMware HCX eicellen-bestand te downloaden.

    ![Systeem updates ophalen](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. Ga vervolgens naar de on-premises vCenter en selecteer een OVF-sjabloon om te implementeren in uw on-premises vCenter.  

    ![OVF-sjabloon selecteren](./media/hybrid-cloud-extension-installation/select-template.png)

1. Selecteer een naam en locatie en selecteer vervolgens een resource/cluster waar HCX moet worden geïmplementeerd. Controleer vervolgens de details en de vereiste resources.  

    ![Details van sjabloon controleren](./media/hybrid-cloud-extension-installation/configure-template.png)

1. Raadpleeg de licentie voorwaarden en als u akkoord gaat, selecteert u vereiste opslag en netwerk. Selecteer vervolgens **Volgende**.

1. Voer in **sjabloon aanpassen**alle vereiste gegevens in. 

    ![Sjabloon aanpassen](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. Selecteer **volgende**, Controleer de configuratie en selecteer **volt ooien** om HCXe eicellen te implementeren.

## <a name="activate-hcx"></a>HCX activeren

Na de installatie voert u de volgende stappen uit.

1. Meld u aan bij on-premises HCX Manager op `https://HCXManagerIP:9443` en meld u aan met uw gebruikers naam en wacht woord. 

   > [!IMPORTANT]
   > Zorg ervoor dat u het `9443` poort nummer opneemt met het IP-adres van HCX Manager.

1. Voer in **licentie verlening**uw **Geavanceerde HCX-sleutel**in.  

    ![Voer de HCX-sleutel in](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX manager moet open Internet toegang of een proxy-configuratie hebben.

1. Bewerk, indien nodig, de vCenter-gegevens in **vCenter**.

    ![VCenter configureren](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. Bewerk op de locatie van het **Data Center**, indien nodig, de locatie van het Data Center.

    ![Database locatie](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>HCX configureren 

1. Meld u aan bij on-premises vCenter en selecteer onder **Home**de optie **HCX**.

    ![HCX in VCenter](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. Onder **infra structuur**selecteert u **site koppeling**om  >  **een site koppeling toe te voegen**.

    ![Site koppeling toevoegen](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. Voer de URL van de externe HCX of het IP-adres, AVS cloudadmin gebruikers naam en wacht woord in en selecteer vervolgens **verbinding maken**.

   Het systeem geeft de verbonden site weer.
   
    ![Site verbinding](./media/hybrid-cloud-extension-installation/site-connection.png)

1. Onder **infra structuur**selecteert u **interconnect**  >  **multi-site service net-**  >  **netwerk profielen**  >  **netwerk profiel maken**.

    ![Netwerk profiel maken](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. Voer voor het nieuwe netwerk profiel de HCX IX-en NE IP-adresbereiken in (mini maal twee IP-adressen zijn vereist voor IX en NE apparaten).
    
   ![IP-adresbereiken invoeren](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > Het netwerk extensie apparaat (HCX-NE) heeft een een-op-een-relatie met een gedistribueerde virtuele switch (DVS).  

1. **Selecteer nu reken profiel**Create COMPUTE  >  **profile**.

1. Voer een naam voor het reken profiel in en selecteer **door gaan**.  

    ![Reken profiel maken](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. Selecteer de services die u wilt inschakelen, zoals migratie, netwerk uitbreiding of herstel na nood gevallen, en selecteer vervolgens **door gaan**.

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

1. Selecteer in **vSphere-replicatie netwerk profiel**een netwerk profiel de VSphere-replicatie interface van ESXi-hosts en selecteer vervolgens **door gaan**.
      
   In de meeste gevallen is dit profiel hetzelfde als het management-netwerk profiel.  
    
   ![VSphere-netwerk profiel voor replicatie selecteren](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. Selecteer in **gedistribueerde switches voor netwerk uitbreidingen**de DVS waarop u netwerken wilt maken van de virtuele machines die worden geïntegreerd en verbonden.  Selecteer **Doorgaan**.  
      
    ![Gedistribueerde virtuele switches selecteren](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. Controleer de verbindings regels en selecteer **door gaan**.  

    ![Reken profiel maken](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

1.  Selecteer **volt ooien** om het reken profiel te maken.

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

2. Selecteer onder **infra structuur** **interconnect**  >  **service**-net  >  **Service-Mesh maken** om het netwerk en de reken profielen te configureren die in de vorige stappen zijn gemaakt.    
      
    ![Service-net configureren](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

3. Selecteer gekoppelde sites om hybride mogelijkheden in te scha kelen en selecteer **door gaan**.   
    
    ![Gekoppelde sites selecteren](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

4. Selecteer de bron-en externe Compute-profielen om hybridity Services in te scha kelen en selecteer **door gaan**.
      
    De selecties definiëren de resources, waarbij Vm's HCX-Services kunnen gebruiken.  
      
    ![Hybridisatie Services inschakelen](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

5. Selecteer de services die u wilt inschakelen en selecteer **door gaan**.  
      
    ![HCX services selecteren](./media/hybrid-cloud-extension-installation/hcx-services.png)

6. Selecteer **door gaan**in **Geavanceerde configuratie-opheffen van Uplink-netwerk profielen** .  
      
    Uplink-netwerk profielen worden gebruikt om verbinding te maken met het netwerk via welke de verbindings apparaten van de externe site kunnen worden bereikt.  
      
    ![Uplinkpoortprofiel overschrijven](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

7. Selecteer **de Uitschaal van het apparaat netwerk uitbreiding configureren**. 
      
    ![Uitschalen netwerk extensie](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

8. Voer het aantal toestellen in dat overeenkomt met het aantal DVS-switches.  
      
    ![Aantal apparaten configureren](./media/hybrid-cloud-extension-installation/appliance-scale.png)

9. Selecteer **door gaan** om over te slaan.  
      
    ![Verkeers techniek configureren](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

10. Bekijk de preview van de topologie en selecteer **door gaan**. 

11. Voer een gebruiks vriendelijke naam in voor deze service mesh en selecteer **volt ooien** om te volt ooien.  
      
    ![Service-net volt ooien](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    Het service-net wordt geïmplementeerd en geconfigureerd.  
      
    ![Geïmplementeerd service-net](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>Status van apparaat controleren
Als u de status van het apparaat wilt controleren, selecteert u **interconnect**  >  **Appliances**. 
      
![Status van apparaat](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>Volgende stappen

Wanneer de status van het apparaat Connect **tunnel** is **ingesteld** op groen, bent u klaar om AVS-vm's te migreren en beveiligen met behulp van HCX. Zie [VMware HCX-documentatie](https://docs.vmware.com/en/VMware-HCX/index.html) en [Migreer virtual machines met VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) in de technische documentatie van VMware.
