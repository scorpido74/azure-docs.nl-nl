---
title: Azure Migrate-apparaat
description: Biedt een overzicht van het Azure Migrate apparaat dat in Server evaluatie en-migratie wordt gebruikt.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: d02227747be4bc7d994e2ea84cd74e7f2fd2531f
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425456"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-apparaat

In dit artikel wordt het Azure Migrate apparaat beschreven. U implementeert het apparaat wanneer u [Azure migrate gebruikt: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) hulp programma voor het detecteren en beoordelen van apps, infra structuur en workloads voor migratie naar Microsoft Azure. Het apparaat wordt ook gebruikt wanneer u virtuele VMware-machines naar Azure migreert met behulp van [Azure migrate: Server evaluatie](migrate-services-overview.md#azure-migrate-server-migration-tool) met [migratie zonder agent](server-migrate-overview.md).

## <a name="appliance-overview"></a>Overzicht van apparaten

Het Azure Migrate apparaat wordt gebruikt in de volgende scenario's.

**Scenario** | **Hulpprogramma** | **Gebruikt voor** 
--- | --- | ---
VMware-VM | Azure Migrate: Server evaluatie<br/><br/> Azure Migrate: Server migratie | VMware-Vm's detecteren<br/><br/> Machine-apps en-afhankelijkheden detecteren<br/><br/> Meta gegevens en prestaties van meta gegevens van machines voor evaluaties verzamelen.<br/><br/> Virtuele VMware-machines repliceren met migratie zonder agent.
Virtuele Hyper-V-machine | Azure Migrate: Server evaluatie | Virtuele Hyper-V-machines detecteren<br/><br/> Meta gegevens en prestaties van meta gegevens van machines voor evaluaties verzamelen.
Fysieke machine |  Azure Migrate: Server evaluatie |  Fysieke servers detecteren<br/><br/> Meta gegevens en prestaties van meta gegevens van machines voor evaluaties verzamelen.

## <a name="appliance---vmware"></a>Apparaat-VMware 

**Vereiste** | **VMware** 
--- | ---
**Indeling voor downloaden** | . RESPECTIEVELIJK 
**Download koppeling** | https://aka.ms/migrate/appliance/vmware 
**Download grootte** | 11,2 GB
**Houders** | De sjabloon voor het gedownloade apparaat wordt geleverd met een Windows Server 2016-evaluatie licentie, die voor 180 dagen geldig is. Als de evaluatie periode bijna is verlopen, raden wij aan dat u een nieuw apparaat downloadt en implementeert, of dat u de licentie voor het besturings systeem van de apparaat-VM activeert.
**Implementatie** | U implementeert het apparaat als een virtuele VMware-machine. U hebt voldoende resources op de vCenter Server nodig om een virtuele machine toe te wijzen met 32 GB RAM, 8 Vcpu's, ongeveer 80 GB aan schijf opslag en een externe virtuele switch.<br/> Voor het apparaat is toegang tot internet vereist, hetzij rechtstreeks hetzij via een proxy.<br/> Het apparaat kan verbinding maken met één vCenter Server.
**Hardware** | Resources in vCenter om een virtuele machine toe te wijzen met 32 GB RAM 8 Vcpu's, ongeveer 80 GB aan schijf opslag en een externe virtuele switch. 
**Hash-waarde** | MD5: c06ac2a2c0f870d3b274a0b7a73b78b1<br/><br/> SHA256:4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c
**vCenter-Server/host** | De toestel-VM moet worden geïmplementeerd op een ESXi-host waarop versie 5,5 of hoger wordt uitgevoerd.<br/><br/> vCenter Server met 5,5, 6,0, 6,5 of 6,7.
**Azure Migrate project** | Een apparaat kan worden gekoppeld aan een enkel project. <br/> Een wille keurig aantal apparaten kan aan één project worden gekoppeld.<br/> 
**Detectie** | Een apparaat kan tot 10.000 VMware-Vm's detecteren op een vCenter Server.<br/> Een apparaat kan verbinding maken met één vCenter Server.
**Toestel onderdelen** | Beheer-app: Web-app in het apparaat voor gebruikers invoer tijdens de implementatie.<br/> Detectie agent: verzamelt computer configuratie gegevens.<br/> Beoordelings agent: prestatie gegevens verzamelen.<br/> DRA: organiseert de replicatie van de virtuele machine en coördineert de communicatie tussen computers/Azure.<br/> Gateway: verstuurt gerepliceerde gegevens naar Azure.<br/> Service voor automatische updates: onderdelen bijwerken (elke 24 uur wordt uitgevoerd).
**VDDK (migratie zonder agent)** | Als u een migratie zonder agent uitvoert met Azure Migrate server migratie, moet de VMware vSphere VDDK zijn geïnstalleerd op de apparaat-VM.


## <a name="appliance---hyper-v"></a>Apparaat-Hyper-V

**Vereiste** | **Hyper-V** 
--- | ---
**Indeling voor downloaden** | Gezipte map (met VHD)
**Download koppeling** | https://aka.ms/migrate/appliance/hyperv 
**Download grootte** | 10 GB
**Houders** | De sjabloon voor het gedownloade apparaat wordt geleverd met een Windows Server 2016-evaluatie licentie, die voor 180 dagen geldig is. Als de evaluatie periode bijna is verlopen, raden wij aan dat u een nieuw apparaat downloadt en implementeert, of dat u de licentie voor het besturings systeem van de apparaat-VM activeert.
**Implementatie van het apparaat**   |  U implementeert het apparaat als een Hyper-V-VM.<br/> De apparaat-VM van Azure Migrate is Hyper-V VM versie 5,0.<br/> Op de Hyper-V-host moet Windows Server 2012 R2 of later worden uitgevoerd.<br/> De host moet voldoende ruimte hebben om 16 GB RAM-geheugen, 8 Vcpu's, ongeveer 80 GB aan opslag ruimte toe te wijzen en een externe switch voor de apparaat-VM.<br/> Het apparaat heeft een statisch of dynamisch IP-adres nodig en Internet toegang.
**Hardware** | Resources op de Hyper-V-host om 16 GB RAM-geheugen, 8 Vcpu's, ongeveer 80 GB aan opslag ruimte toe te wijzen en een externe switch voor de apparaat-VM.
**Hash-waarde** | MD5:29a7531f32bcf69f32d964fa5ae950bc<br/><br/> SHA256:37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31
**Hyper-V-host** | Met Windows Server 2012 R2 of hoger.
**Azure Migrate project** | Een apparaat kan worden gekoppeld aan een enkel project. <br/> Een wille keurig aantal apparaten kan aan één project worden gekoppeld.<br/> 
**Detectie** | Een apparaat kan tot 5000 Hyper-V-Vm's detecteren.<br/> Een apparaat kan verbinding maken met Maxi maal 300 Hyper-V-hosts.
**Toestel onderdelen** | Beheer-app: Web-app in het apparaat voor gebruikers invoer tijdens de implementatie.<br/> Detectie agent: verzamelt computer configuratie gegevens.<br/> Beoordelings agent: prestatie gegevens verzamelen.<br/>  Service voor automatische updates: onderdelen bijwerken (elke 24 uur wordt uitgevoerd).


## <a name="appliance---physical"></a>Apparaat-fysiek

**Vereiste** | **Werkelijke** 
--- | ---
**Indeling voor downloaden** | Gezipte map (met installatie script op basis van Power shell)
**Download koppeling** | [Download koppeling](https://go.microsoft.com/fwlink/?linkid=2105112)
**Download grootte** | 59,7 MB
**Hardware** | Toegewezen fysieke machine of een virtuele machine gebruiken. Voor de computer met het apparaat is 16 GB RAM, 8 Vcpu's, ongeveer 80 GB aan opslag ruimte en een externe switch vereist.<br/> Het apparaat heeft een statisch of dynamisch IP-adres nodig en Internet toegang.
**Hash-waarde** | MD5:1e92ede3e87c03bd148e56a708cdd33f<br/><br/> SHA256: a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c
**Besturingssysteem** | Op de apparaatapparaat moet Windows Server 2016 worden uitgevoerd. 
**Implementatie van het apparaat**   |  Het toestel installatie script wordt gedownload van de portal (in een gezipte map). <br/> U comprimeert de map en voert het Power shell-script (AzureMigrateInstaller. ps1) uit.
**Detectie** | Een apparaat kan Maxi maal 250 fysieke servers detecteren.
**Toestel onderdelen** | Beheer-app: Web-app in het apparaat voor gebruikers invoer tijdens de implementatie.<br/> Detectie agent: verzamelt computer configuratie gegevens.<br/> Beoordelings agent: prestatie gegevens verzamelen.<br/>  Service voor automatische updates: onderdelen bijwerken (elke 24 uur wordt uitgevoerd).


## <a name="url-access"></a>URL-toegang

Het Azure Migrate-apparaat heeft verbinding met internet nodig.

- Wanneer u het apparaat implementeert, controleert Azure Migrate een connectiviteits controle op de Url's die in de onderstaande tabel worden samenvatten.
- Als u een op URL gebaseerde proxy gebruikt om verbinding te maken met internet, toegang tot deze Url's toestaan, zodat de proxy alle CNAME-records verhelpt die zijn ontvangen tijdens het opzoeken van de Url's.

**URL** | **Details**  
--- | --- |
*.portal.azure.com  | Navigeer naar het Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com | Meld u aan bij uw Azure-abonnement.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Maak Active Directory apps voor het apparaat om te communiceren met Azure Migrate.
management.azure.com | Maak Active Directory apps voor het apparaat om te communiceren met de Azure Migrate-service.
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne bewaking.
*.vault.azure.net | Geheimen beheren in de Azure Key Vault.
aka.ms/* | Toegang tot ook wel-koppelingen toestaan. Wordt gebruikt voor Azure Migrate apparaat-updates.
download.microsoft.com/download | Down loads van micro soft downloaden toestaan.
*.servicebus.windows.net | Communicatie tussen het apparaat en de Azure Migrate service.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Verbinding maken met Azure Migrate service-Url's.
*.hypervrecoverymanager.windowsazure.com | **Gebruikt voor VMware-migratie zonder agent**<br/><br/> Verbinding maken met Azure Migrate service-Url's.
*.blob.core.windows.net |  **Gebruikt voor VMware-migratie zonder agent**<br/><br/>Gegevens uploaden naar de opslag voor migratie.




## <a name="collected-data---vmware"></a>Verzamelde gegevens-VMware

### <a name="collected-performance-data-vmware"></a>Verzamelde prestatie gegevens-VMware

Dit zijn de prestatie gegevens van de VMware-VM die door het apparaat worden verzameld en naar Azure worden verzonden.

**Gegevens** | **Item** | **Beoordelings impact**
--- | --- | ---
CPU-gebruik | CPU. usage. Average | Aanbevolen VM-grootte/kosten
Geheugen gebruik | mem. usage. Average | Aanbevolen VM-grootte/kosten
Lees doorvoer schijf (MB per seconde) | virtualDisk.read.average | Berekening voor schijf grootte, opslag kosten, VM-grootte
Schrijf doorvoer schijf (MB per seconde) | virtualDisk. write. Average | Berekening voor schijf grootte, opslag kosten, VM-grootte
Lees bewerkingen op de schijf per seconde | virtualDisk.numberReadAveraged.average | Berekening voor schijf grootte, opslag kosten, VM-grootte
Schrijf bewerkingen per seconde van schijf | virtualDisk.numberWriteAveraged.average  | Berekening voor schijf grootte, opslag kosten, VM-grootte
Lees doorvoer van NIC (MB per seconde) | net. received. Average | Berekening voor VM-grootte
Doorvoer capaciteit van NIC (MB per seconde) | net. verzonden. gemiddeld  |Berekening voor VM-grootte


### <a name="collected-metadata-vmware"></a>Verzamelde meta gegevens-VMware

> [!NOTE]
> Meta gegevens die door het Azure Migrate apparaat worden gedetecteerd, worden gebruikt om u te helpen uw toepassingen van rechts formaat te migreren naar Azure, Azure-geschiktheids analyse, analyse van toepassings afhankelijkheden en kosten planning uit te voeren. Micro soft gebruikt deze gegevens niet ten opzichte van een controle op naleving van licenties.

Hier ziet u de volledige lijst met virtuele VMware-VM-meta gegevens die door het apparaat worden verzameld en naar Azure worden verzonden.

**Gegevens** | **Item**
--- | --- 
**Computer Details** | 
VM-ID | vm.Config.InstanceUuid 
VM-naam | vm.Config.Name
vCenter Server-ID | VMwareClient.Instance.Uuid
VM-beschrijving | vm.Summary.Config.Annotation
Licentie product naam | vm.Client.ServiceContent.About.LicenseProductName
Type besturings systeem | vm.SummaryConfig.GuestFullName
Opstart type | vm.Config.Firmware
Aantal kernen | vm.Config.Hardware.NumCPU
Geheugen (MB) | vm.Config.Hardware.MemoryMB
Aantal schijven | VM. Config. hardware. device. ToList (). FindAll (x = > is VirtualDisk). Count
Lijst met schijf grootte | VM. Config. hardware. device. ToList (). FindAll (x = > is VirtualDisk)
Lijst met netwerk adapters | VM. Config. hardware. device. ToList (). FindAll (x = > is VirtualEthernet). Count
CPU-gebruik | CPU. usage. Average
Geheugen gebruik |mem. usage. Average
**Details per schijf** | 
Waarde van schijf sleutel | schijf. Prestatie
Dikunit-nummer | schijf. UnitNumber
Sleutel waarde van schijf controller | disk.ControllerKey.Value
Gigabytes ingericht | virtualDisk. DeviceInfo. summary
Schijf naam | De waarde die is gegenereerd met de schijf. UnitNumber, schijf. Sleutel, schijf. ControllerKey. waarde
Lees bewerkingen per seconde | virtualDisk.numberReadAveraged.average
Schrijf bewerkingen per seconde | virtualDisk.numberWriteAveraged.average
Lees doorvoer (MB per seconde) | virtualDisk.read.average
Schrijf doorvoer (MB per seconde) | virtualDisk. write. Average
**Details van de NIC** | 
Naam van netwerk adapter | adapter. Prestatie
MAC-adres | ((VirtualEthernetCard)nic).MacAddress
IPv4-adressen | vm.Guest.Net
IPv6-adressen | vm.Guest.Net
Lees doorvoer (MB per seconde) | net. received. Average
Schrijf doorvoer (MB per seconde) | net. verzonden. gemiddeld
**Details van configuratiepad** | 
Name | container.GetType().Name
Type onderliggend object | verpakking. ChildType
Referentie Details | verpakking. MoRef
Details van bovenliggend item | Container.Parent
Details van map per VM | ((Folder)container).ChildEntity.Type
Details van Data Center per VM | ((Datacenter)container).VmFolder
Details van Data Center per host-map | (Container (Data Center)). HostFolder
Cluster Details per host | ((ClusterComputeResource) container). Hostsite
Details van host per VM | ((HostSystem) container). VM

## <a name="collected-data---hyper-v"></a>Verzamelde gegevens-Hyper-V

### <a name="collected-performance-data-hyper-v"></a>Verzamelde prestatie gegevens-Hyper-V

> [!NOTE]
> Meta gegevens die door het Azure Migrate apparaat worden gedetecteerd, worden gebruikt om u te helpen uw toepassingen van rechts formaat te migreren naar Azure, Azure-geschiktheids analyse, analyse van toepassings afhankelijkheden en kosten planning uit te voeren. Micro soft gebruikt deze gegevens niet ten opzichte van een controle op naleving van licenties.

Dit zijn de prestatie gegevens van de Hyper-VM die het apparaat verzamelt en verzendt naar Azure.

**Klasse prestatie meter** | **Item** | **Beoordelings impact**
--- | --- | ---
Virtuele processor van Hyper-V Hyper Visor | % Gast-uitvoerings tijd | Aanbevolen VM-grootte/kosten
Hyper-V-dynamisch geheugen VM | Huidige belasting (%)<br/> Zichtbaar fysiek geheugen voor gast (MB) | Aanbevolen VM-grootte/kosten
Virtuele Hyper-V-opslag apparaat | Gelezen bytes per seconde | Berekening voor schijf grootte, opslag kosten, VM-grootte
Virtuele Hyper-V-opslag apparaat | Geschreven bytes per seconde | Berekening voor schijf grootte, opslag kosten, VM-grootte
Virtual Network Adapter voor Hyper-V | Ontvangen bytes per seconde | Berekening voor VM-grootte
Virtual Network Adapter voor Hyper-V | Verzonden bytes per seconde | Berekening voor VM-grootte

- CPU-gebruik is de som van alle gebruik voor alle virtuele processors die zijn gekoppeld aan een virtuele machine.
- Geheugen gebruik is (huidige druk * gast zichtbaar fysiek geheugen)/100.
- De waarden voor de schijf-en netwerk gebruik worden verzameld uit de vermelde Hyper-V-prestatie meter items.

### <a name="collected-metadata-hyper-v"></a>Verzamelde meta gegevens-Hyper-V

Hier vindt u de volledige lijst met meta gegevens van Hyper-V-VM'S die het apparaat verzamelt en verzendt naar Azure.

**Gegevens** | **WMI-klasse** | **WMI-klasse-eigenschap**
--- | --- | ---
**Computer Details** | 
Serie nummer van BIOS _ Msvm_BIOSElement | BIOSSerialNumber
VM-type (gen 1 of 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Weergave naam van VM | Msvm_VirtualSystemSettingData | ElementName
VM-versie | Msvm_ProcessorSettingData | VirtualQuantity
Geheugen (bytes) | Msvm_MemorySettingData | VirtualQuantity
Maximale hoeveelheid geheugen die kan worden gebruikt door VM | Msvm_MemorySettingData | Limiet
Dynamisch geheugen ingeschakeld | Msvm_MemorySettingData | DynamicMemoryEnabled
Naam/versie/FQDN van het besturings systeem | Msvm_KvpExchangeComponent | Naam gegevens GuestIntrinsicExchangeItems
Energie status van de VM | Msvm_ComputerSystem | EnabledState
**Details per schijf** | 
Schijf-id | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Type virtuele harde schijf | Msvm_VirtualHardDiskSettingData | Type
Grootte van virtuele harde schijf | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Bovenliggende virtuele harde schijf | Msvm_VirtualHardDiskSettingData | ParentPath
**Details van de NIC** | 
IP-adressen (synthetische Nic's) | Msvm_GuestNetworkAdapterConfiguration | IP-adressen
DHCP ingeschakeld (synthetische Nic's) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-ID (synthetische Nic's) | Msvm_SyntheticEthernetPortSettingData | InstanceID
MAC-adres van NIC (synthetische Nic's) | Msvm_SyntheticEthernetPortSettingData | Adres
NIC-ID (verouderde Nic's) | MsvmEmulatedEthernetPortSetting-gegevens | InstanceID
MAC-ID van NIC (verouderde Nic's) | MsvmEmulatedEthernetPortSetting-gegevens | Adres




## <a name="discovery-and-collection-process"></a>Detectie-en verzamelings proces

Het apparaat communiceert met vCenter-servers en Hyper-V-hosts/cluster met behulp van het volgende proces.

1. **Detectie starten**:
    - Wanneer u de detectie op het Hyper-V-apparaat start, communiceert deze met de Hyper-V-hosts op WinRM-poorten 5985 (HTTP) en 5986 (HTTPS).
    - Wanneer u detectie op het VMware-apparaat start, communiceert het met de vCenter-Server op TCP-poort 443 standaard. Als de vCenter-Server op een andere poort luistert, kunt u deze configureren in de Web-App van het apparaat.
2. **Meta gegevens en prestatie gegevens verzamelen**:
    - Het apparaat maakt gebruik van een Common Information Model (CIM) om Hyper-V VM-gegevens te verzamelen van de Hyper-V-host op de poorten 5985 en 5986.
    - Het apparaat communiceert standaard met poort 443 om VMware-VM-gegevens van de vCenter Server te verzamelen.
3. **Gegevens verzenden**: het apparaat verzendt de verzamelde gegevens naar Azure migrate server-evaluatie en Azure migrate server migratie via SSL-poort 443. Het apparaat kan verbinding maken met Azure via internet of u kunt ExpressRoute gebruiken met open bare/micro soft-peering.
    - Het apparaat verzamelt gegevens over realtime gebruik voor prestatie gegevens.
        - De prestatie gegevens worden elke 20 seconden voor VMware verzameld, en elke 30 seconden voor Hyper-V, voor elke prestatie metriek.
        - De verzamelde gegevens worden samengevouwen om één gegevens punt gedurende tien minuten te maken.
        - De waarde piek gebruik wordt geselecteerd uit alle gegevens punten van 20/30 seconden en naar Azure verzonden voor de berekening van de evaluatie.
        - Op basis van de percentiel waarde die is opgegeven in de eigenschappen van de beoordeling (50e/negen/95e/99e), worden de tien-minuten punten in oplopende volg orde gesorteerd en wordt de juiste percentiel waarde gebruikt voor het berekenen van de evaluatie
    - Voor server migratie begint het apparaat met het verzamelen van VM-gegevens en repliceert het naar Azure.
4. **Beoordelen en migreren**: u kunt nu evaluaties maken op basis van de meta gegevens die worden verzameld door het apparaat met behulp van Azure migrate server-evaluatie. Daarnaast kunt u ook beginnen met het migreren van virtuele VMware-machines met Azure Migrate server migratie om replicatie zonder agents te organiseren.


![Architectuur](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Toestel-upgrades

Het apparaat wordt bijgewerkt wanneer de Azure Migrate agents die op het apparaat worden uitgevoerd, worden bijgewerkt.

- Dit gebeurt automatisch omdat de automatische update standaard is ingeschakeld op het apparaat.
- U kunt deze standaard instelling wijzigen om de agents hand matig bij te werken.
- Als u de automatische update wilt uitschakelen, gaat u naar de REGI ster-editor > HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance en stelt u de register sleutel-"auto update" in op 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Agent updates instellen op hand matig

Voor hand matige updates moet u ervoor zorgen dat u alle agents op het apparaat tegelijk bijwerkt met behulp van de knop **bijwerken** voor elke verouderde agent op het apparaat. U kunt de instelling van de update op elk gewenst moment naar automatische updates overschakelen.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](tutorial-assess-vmware.md#set-up-the-appliance-vm) het instellen van het apparaat voor VMware.
[Meer informatie over](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) het instellen van het apparaat voor Hyper-V.

