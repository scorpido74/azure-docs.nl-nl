---
title: Architectuur van Azure Migrate apparaat | Microsoft Docs
description: Biedt een overzicht van het Azure Migrate apparaat
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 249cbea173afe1671118446e0714b721b8c7f72b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685093"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-apparaat

In dit artikel wordt het Azure Migrate apparaat beschreven. U implementeert het apparaat wanneer u Azure Migrate-hulpprogram ma's voor evaluatie en migratie gebruikt om apps, infra structuur en werk belastingen te detecteren, te evalueren en te migreren naar Microsoft Azure. 

[Azure migrate](migrate-services-overview.md) biedt een centrale hub voor het bijhouden van detectie, evaluatie en migratie van uw on-premises apps en werk belastingen, en persoonlijke/open bare Cloud-Vm's naar Azure. De hub biedt Azure Migrate-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV'S-aanbiedingen (Independent Software Vendor) van derden.



## <a name="appliance-overview"></a>Overzicht van apparaten

De typen apparaten en het gebruik van Azure Migrate zijn als volgt.

**Geïmplementeerd als** | **Gebruikt voor** | **Details**
--- | --- |  ---
VMware-VM | VMware VM-evaluatie met het hulp programma voor het evalueren van Azure Migrate.<br/><br/> VMware VM zonder agent migratie met het hulp programma voor migratie van Azure Migrate-server | Down load de eicellen-sjabloon en importeer deze naar vCenter Server om de apparaat-VM te maken.
Virtuele Hyper-V-machine | De Hyper-V-VM-evaluatie met het hulp programma voor het evalueren van Azure Migrate. | Down load de gezipte VHD en importeer deze naar Hyper-V om de apparaat-VM te maken.

## <a name="appliance-access"></a>Toegang tot het apparaat

Nadat u het apparaat hebt geconfigureerd, kunt u op afstand toegang krijgen tot de VM van het apparaat via TCP-poort 3389. U kunt ook op afstand toegang krijgen tot de web management-app voor het apparaat, op poort 44368 met URL: `https://<appliance-ip-or-name>:44368`.

## <a name="appliance-license"></a>Toestel licentie
Het apparaat wordt geleverd met een evaluatie licentie voor Windows Server 2016, die voor 180 dagen geldig is. Als de evaluatie periode bijna is verlopen, raden wij aan dat u een nieuw apparaat downloadt en implementeert, of dat u de licentie voor het besturings systeem van de apparaat-VM activeert.

## <a name="appliance-agents"></a>Toestel agenten
Deze agents zijn geïnstalleerd op het apparaat.

**Agent** | **Details**
--- | ---
Detectie agent | Verzamelt configuratie gegevens van on-premises virtuele machines
Evaluatieagent | Profielen de on-premises omgeving voor het verzamelen van prestatie gegevens van de virtuele machine.
Migratie adapter | Organiseert de replicatie van de virtuele machine en coördineert de communicatie tussen Vm's en Azure.
Migratie gateway | Hiermee worden gerepliceerde VM-gegevens naar Azure verzonden.


## <a name="appliance-deployment-requirements"></a>Vereisten voor de implementatie van het apparaat

- [Bekijk](migrate-support-matrix-vmware.md#assessment-appliance-requirements) de implementatie vereisten voor een VMware-apparaat en de url's waartoe het apparaat toegang moet hebben.
- [Controleer](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) de implementatie vereisten voor een Hyper-V-apparaat en de url's waartoe het apparaat toegang moet hebben.


## <a name="collected-performance-data-vmware"></a>Verzamelde prestatie gegevens-VMware

Dit zijn de prestatie gegevens van de VMware-VM die door het apparaat worden verzameld en naar Azure worden verzonden.

**Gegevens** | **Item** | **Beoordelings impact**
--- | --- | ---
CPU-gebruik | CPU. usage. Average | Aanbevolen VM-grootte/kosten
Geheugen gebruik | mem. usage. Average | Aanbevolen VM-grootte/kosten
Lees doorvoer schijf (MB per seconde) | virtualDisk. Read. Average | Berekening voor schijf grootte, opslag kosten, VM-grootte
Schrijf doorvoer schijf (MB per seconde) | virtualDisk. write. Average | Berekening voor schijf grootte, opslag kosten, VM-grootte
Lees bewerkingen op de schijf per seconde | virtualDisk. numberReadAveraged. Average | Berekening voor schijf grootte, opslag kosten, VM-grootte
Schrijf bewerkingen per seconde van schijf | virtualDisk. numberWriteAveraged. Average  | Berekening voor schijf grootte, opslag kosten, VM-grootte
Lees doorvoer van NIC (MB per seconde) | net. received. Average | Berekening voor VM-grootte
Schrijf doorvoer van NIC (MB per seconde) | net. verzonden. gemiddeld  |Berekening voor VM-grootte


## <a name="collected-metadata-vmware"></a>Verzamelde meta gegevens-VMware

> [!NOTE]
> Meta gegevens die door het Azure Migrate apparaat worden gedetecteerd, worden gebruikt om u te helpen uw toepassingen van rechts formaat te migreren naar Azure, Azure-geschiktheids analyse, analyse van toepassings afhankelijkheden en kosten planning uit te voeren. Micro soft gebruikt deze gegevens niet ten opzichte van een controle op naleving van licenties.

Hier ziet u de volledige lijst met virtuele VMware-VM-meta gegevens die door het apparaat worden verzameld en naar Azure worden verzonden.

**Gegevens** | **Item**
--- | --- 
**Computer Details** | 
VM-ID | VM. Config. InstanceUuid 
VM-naam | VM. Config.Name
vCenter Server-ID | VMwareClient.Instance.Uuid
VM-beschrijving | VM. Summary. config. annotatie
Licentie product naam | VM. Client. ServiceContent. about. LicenseProductName
Type besturings systeem | vm.SummaryConfig.GuestFullName
Opstart type | VM. Config. firmware
Aantal kerngeheugens | VM. Config. hardware. NumCPU
Geheugen (MB) | VM. Config. hardware. MemoryMB
Aantal schijven | VM. Config. hardware. device. ToList (). FindAll (x = > is VirtualDisk). Count
Lijst met schijf grootte | VM. Config. hardware. device. ToList (). FindAll (x = > is VirtualDisk)
Lijst met netwerk adapters | VM. Config. hardware. device. ToList (). FindAll (x = > is VirtualEthernet). Count
CPU-gebruik | CPU. usage. Average
Geheugen gebruik |mem. usage. Average
**Details per schijf** | 
Waarde van schijf sleutel | schijf. Prestatie
Dikunit-nummer | schijf. UnitNumber
Sleutel waarde van schijf controller | schijf. ControllerKey. waarde
Gigabytes ingericht | virtualDisk. DeviceInfo. summary
Schijf naam | De waarde die is gegenereerd met de schijf. UnitNumber, schijf. Sleutel, schijf. ControllerKey. waarde
Lees bewerkingen per seconde | virtualDisk. numberReadAveraged. Average
Schrijf bewerkingen per seconde | virtualDisk. numberWriteAveraged. Average
Lees doorvoer (MB per seconde) | virtualDisk. Read. Average
Schrijf doorvoer (MB per seconde) | virtualDisk. write. Average
**Details van de NIC** | 
Naam van netwerk adapter | adapter. Prestatie
MAC-adres | ((VirtualEthernetCard) NIC). MacAddress
IPv4-adressen | VM. Guest.Net
IPv6-adressen | VM. Guest.Net
Lees doorvoer (MB per seconde) | net. received. Average
Schrijf doorvoer (MB per seconde) | net. verzonden. gemiddeld
**Details van configuratiepad** | 
Naam | verpakking. GetType (). Naam
Type onderliggend object | verpakking. ChildType
Referentie Details | verpakking. MoRef
Details van bovenliggend item | Container. Parent
Details van map per VM | ((Map) container). ChildEntity. type
Details van Data Center per VM | (Container (Data Center)). VmFolder
Details van Data Center per host-map | (Container (Data Center)). HostFolder
Cluster Details per host | ((ClusterComputeResource) container). Hostsite
Details van host per VM | ((HostSystem) container). VM



## <a name="collected-performance-data-hyper-v"></a>Verzamelde prestatie gegevens-Hyper-V

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

## <a name="collected-metadata-hyper-v"></a>Verzamelde meta gegevens-Hyper-V

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
3. **Gegevens verzenden**: het apparaat verzendt de verzamelde gegevens naar Azure migrate server-evaluatie en Azure migrate server migratie via SSL-poort 443.
    - Het apparaat verzamelt gegevens over realtime gebruik voor prestatie gegevens.
        - De prestatie gegevens worden elke 20 seconden voor VMware verzameld, en elke 30 seconden voor Hyper-V, voor elke prestatie metriek.
        - De verzamelde gegevens worden samengevouwen om één gegevens punt gedurende tien minuten te maken.
        - De waarde piek gebruik wordt geselecteerd voor alle gegevens punten van 20/30 tweede en verzonden naar Azure voor evaluatie berekening.
        - Op basis van de percentiel waarde die is opgegeven in de eigenschappen van de beoordeling (50e/negen/95e/99e), worden de tien-minuten punten in oplopende volg orde gesorteerd en wordt de juiste percentiel waarde gebruikt voor het berekenen van de evaluatie
    - Voor server migratie begint het apparaat met het verzamelen van VM-gegevens en repliceert het naar Azure.
4. **Beoordelen en migreren**: u kunt nu evaluaties maken op basis van de meta gegevens die worden verzameld door het apparaat met behulp van Azure migrate server-evaluatie. Daarnaast kunt u ook beginnen met het migreren van virtuele VMware-machines met Azure Migrate server migratie om replicatie zonder agents te organiseren.


![Architectuur](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Toestel-upgrades

Het apparaat wordt bijgewerkt wanneer de Azure Migrate agents die op het apparaat worden uitgevoerd, worden bijgewerkt.

- Dit gebeurt automatisch omdat de automatische update standaard is ingeschakeld op het apparaat.
- U kunt deze standaard instelling wijzigen om de agents hand matig bij te werken.
- Als u de automatische update wilt uitschakelen, gaat u naar de REGI ster-editor > HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance en stelt u de register sleutel-"auto update" in op 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Agent updates instellen op hand matig

Voor hand matige updates moet u ervoor zorgen dat u alle agents op het apparaat tegelijk bijwerkt met behulp van de knop **bijwerken** voor elke verouderde agent op het apparaat. U kunt de instelling van de update op elk gewenst moment naar automatische updates overschakelen.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](tutorial-assess-vmware.md#set-up-the-appliance-vm) het instellen van het apparaat voor VMware.
[Meer informatie over](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) het instellen van het apparaat voor Hyper-V.

