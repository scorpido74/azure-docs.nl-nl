---
title: NetApp-bestanden voor de Azure VMware-oplossing
description: Gebruik Azure NetApp Files met Azure VMware-Vm's voor het migreren en synchroniseren van gegevens over on-premises servers, virtuele machines met Azure VMware-oplossingen en Cloud infrastructuren.
ms.topic: how-to
ms.date: 09/17/2020
ms.openlocfilehash: 0adf137db358d3561365a3a1ed2def338eeeb158
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708710"
---
# <a name="netapp-files-for-azure-vmware-solution"></a>NetApp-bestanden voor de Azure VMware-oplossing

In dit artikel wordt uitgelegd hoe u Azure NetApp Files integreert met Azure VMware-workloads op basis van oplossingen. [Azure NetApp files](../azure-netapp-files/azure-netapp-files-introduction.md) is een Azure-service voor het migreren en uitvoeren van werk belastingen van bedrijfs bestanden in de Cloud zonder code wijzigingen. Het maakt het eenvoudig om gegevens over on-premises en Cloud infrastructuren te migreren. Snelle beveiliging met verbeterde gegevens synchronisatie vereenvoudigt migratie-en DevOps scenario's met mogelijkheden als moment opname, herstel en Active Directory-integratie.

## <a name="topology"></a>Topologie

In dit scenario kunt u de Azure NetApp Files groeps share testen en controleren, Azure NetApp Files is geconfigureerd op Azure met de capaciteits groep, de volume groep en het subnet. We hebben het NFS-protocol gebruikt. Zowel Azure NetApp Files als Azure VMware-oplossing worden gemaakt in dezelfde Azure-regio, VS-Oost. De verbinding met de Azure VMware-oplossing is via Azure ExpressRoute.

![NetApp-bestanden voor de Azure VMware-oplossings topologie.](media/net-app-files/net-app-files-topology.png)

## <a name="prerequisites"></a>Vereisten 

> [!div class="checklist"]
> * Azure-abonnement met Azure NetApp Files
> * Subnet voor Azure NetApp-bestand
> * Linux-VM in azure VMware-oplossing
> * Windows VM in azure VMware-oplossing

## <a name="verify-configuration-of-azure-netapp-files"></a>De configuratie van Azure NetApp Files controleren 

Controleer eerst de configuratie van Azure NetApp Files, gemaakt in azure op een Premium-schijf.

1. Selecteer **Azure NetApp files**in de Azure portal onder **opslag**. Er wordt een lijst weer gegeven met uw geconfigureerde Azure NetApp Files.

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Lijst met Azure NetApp Files."::: 

2. Als u een NetApp-account selecteert, kunt u verschillende instellingen weer geven. Als u bijvoorbeeld **Contoso-anf2**selecteert, ziet u de instellingen ervan. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Instellingen van een NetApp-account weer geven."::: 

3. Selecteer de **capaciteits groepen** om de geconfigureerde groep te controleren. 

    :::image type="content" source="media/net-app-files/capacity-pools.png" alt-text="Geconfigureerde groep controleren.":::

    U kunt zien dat de opslag groep is geconfigureerd als 4 TiB met een Premium-schijf.

4. Selecteer **volumes** (zie scherm afbeelding van stap 2) om volumes weer te geven die zijn gemaakt onder de capaciteits groep.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Azure NetApp Files volume.":::

5. Selecteer een volume om de configuratie ervan weer te geven.  

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Configuratie details van een volume.":::

    U kunt zien dat de anfpool-volume groep, met 200 GiB, is gemaakt als een NetApp-bestands share. Er is een virtueel particulier IP-netwerk gemaakt voor Azure NetApp Files dat het NFS-pad naar de virtuele machines van de Azure VMware-oplossing kan koppelen. 

## <a name="protocols-supported-by-azure-netapp-files"></a>Protocollen die door Azure NetApp Files worden ondersteund  

Azure NetApp Files ondersteunt SMB-toewijzing (Server Message Block) en NFS-share (Network File System). 

- **SMB-share**: als u een SMB-volume wilt implementeren, moet u eerst een Active Directory verbinding maken. De opgegeven domein controllers moeten toegankelijk zijn voor het gedelegeerde subnet van Azure NetApp Files voor een geslaagde verbinding. Zodra Active Directory in het Azure NetApp Files account is geconfigureerd, wordt dit weer gegeven als een selecteerbaar item bij het maken van SMB-volumes. 

- **NFS-share**: Azure NetApp files draagt bij aan het maken van volumes met behulp van NFS (NFSv3 en nfsv 4.1), SMBv3 of een Dual Protocol (NFSV3 en SMB). Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool. NFS kan worden gekoppeld aan een Linux-server met behulp van een opdracht regel.

## <a name="create-azure-netapp-files"></a>Azure NetApp Files maken 

1. Meld u aan bij [Azure Portal](https://rc.portal.azure.com/#home). Onder Azure-Services selecteert u **Azure NetApp files**. 

2. Selecteer **+ toevoegen** om een nieuw Azure NetApp files volume te maken. 

3. Vul de vereiste velden (naam, abonnement, resource groep en locatie) in en selecteer **maken**. 

## <a name="add-capacity-pools-into-azure-netapp-files"></a>Voeg capaciteits Pools toe aan Azure NetApp Files 

1. Selecteer in de Azure Portal **Azure NetApp files**de optie **capaciteits Pools** en **+ groep toevoegen**. 

2. Voer de vereiste gegevens in voor de naam van de volume groep, het service niveau en de schijf grootte (FQDN) of het IP en gewicht. Selecteer **Toevoegen**.

    :::image type="content" source="media/net-app-files/add-capacity-pool.png" alt-text="Een capaciteits pool toevoegen aan NetApp-bestanden.":::

3. Als u volumes onder de capaciteits groep wilt maken, selecteert u **volumes** in het zoek venster en selecteert u **+ volume toevoegen**. 
 
4. Vul de vereiste velden in, zoals wordt weer gegeven in de volgende scherm afbeelding.

    :::image type="content" source="media/net-app-files/create-a-volume.png" alt-text="Voeg volumes toe onder de capaciteits groep.":::

5. Selecteer op de volgende pagina de protocol type share. Selecteer de versies als deze een NFS-share zijn en selecteer het Active Directory domein als het een SMB-share is.  

6. Als het een NFS-share is, voegt u het bron-IP-adres toe vanaf waar de protocol type share wordt geopend. Selecteer **Controleren + maken**. 

    :::image type="content" source="media/net-app-files/select-volume-details.png" alt-text="Selecteer de volume gegevens.":::
 
    Onder Azure NetApp Files in de Azure Portal is de NFS-share klaar voor gebruik.

    :::image type="content" source="media/net-app-files/share-ready.png" alt-text="De NFS-share is gereed.":::

## <a name="mount-an-nfs-file-share-on-your-azure-vmware-solution-vms"></a>Een NFS-bestands share koppelen op uw Azure VMware-oplossing Vm's

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-windows-vm"></a>Een NFS-bestands share koppelen aan een Azure VMware-oplossing Windows VM

- Open een opdracht prompt en voer de opdracht uit om de NFS-bestands share toe te wijzen. In de volgende scherm afbeeldingen wordt het share station weer gegeven dat is toegewezen op een Windows-VM in azure VMware-oplossing.  

    :::image type="content" source="media/net-app-files/share-mapped-to-windows-vm.png" alt-text="Deel het station dat is toegewezen op een Windows-VM in azure VMware-oplossing.":::
 
    :::image type="content" source="media/net-app-files/mapped-to-windows-drive.png" alt-text="Station delen dat is toegewezen op een Windows-VM.":::

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-linux-vm"></a>Een NFS-bestands share koppelen aan een Azure VMware-oplossing Linux VM

#### <a name="setting-up-your-azure-instance"></a>Uw Azure-exemplaar instellen

1. Koppel uw Azure-exemplaar in uw Azure Portal aan een subnet dat is gedefinieerd in hetzelfde virtuele netwerk als uw volume.

    > [!NOTE]
    > Het subnet heeft een regel voor de netwerk beveiligings groep nodig die verkeer op de NFS-poorten (2049, 111), UDP en TCP toestaat.

2. Open een SSH-client en maak verbinding met uw Azure-exemplaar. Zie [SSH-sleutels gebruiken met Windows op Azure](../virtual-machines/linux/ssh-from-windows.md)voor meer informatie.

3. Installeer de NFS-client op uw Azure-instantie:
   - Op Red Hat Enterprise Linux of SUSE Linux-instantie: `sudo yum install -y nfs-utils`
   - Op een Ubuntu-of Debian-instantie: `sudo apt-get install nfs-common` 

#### <a name="mounting-your-file-system"></a>Uw bestands systeem koppelen

1. Maak een nieuwe map op uw Azure-instantie: `sudo mkdir ANFPOOL`

2. Selecteer een IP-adres voor het koppel doel.

3. Uw bestands systeem koppelen: `sudo mount -t nfs -o rw,hard,rsize=1048576,wsize=1048576,vers=3,tcp 10.22.3.4:/ANFPOOL ANFPOOL`

    :::image type="content" source="media/net-app-files/root-test.png" alt-text="Hoofd test.":::

## <a name="create-an-active-directory-connection-for-an-smb-share"></a>Een Active Directory verbinding maken voor een SMB-share

1. Selecteer een NetApp-account in de Azure Portal.

2. Selecteer **Active Directory verbindingen**onder Azure NetApp files.

    :::image type="content" source="media/net-app-files/active-directory-connections.png" alt-text="Active Directory verbindingen."::: 

3. Selecteer **samen voegen** om de SMB-share toe te voegen aan Active Directory. In de volgende scherm afbeelding ziet u de domein Details van de SMB-share.

    :::image type="content" source="media/net-app-files/active-directory-connect-details.png" alt-text="Domein Details van SMB-share."::: 

    Uw Azure SMB-bestands share is klaar voor gebruik.  

    :::image type="content" source="media/net-app-files/smb-file-share-details.png" alt-text="De SMB-bestands share is gereed."::: 

4. Wijs de SMB-share toe aan uw VM met Windows Azure VMware-oplossing. Gebruik het pad SMB-koppeling zoals wordt weer gegeven in de vorige scherm afbeelding. Zie [een netwerk station toewijzen in Windows 10](https://support.microsoft.com/help/4026635/windows-10-map-a-network-drive) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [opslag hiërarchie van Azure NetApp files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).
- Bekijk [levenscyclus beheer van virtuele machines van Azure VMware-oplossingen](lifecycle-management-of-azure-vmware-solution-vms.md)
- Zie [Azure VMware-oplossing integreren in een hub-en spoke-architectuur](concepts-avs-hub-and-spoke-integration.md)
