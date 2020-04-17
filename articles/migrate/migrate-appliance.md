---
title: Azure Migrate-apparaat
description: Biedt een overzicht van het Azure Migrate-toestel dat wordt gebruikt bij serverbeoordeling en -migratie.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 130de0824a1671fb0b0e3e980f06f4c3abc689d2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538219"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-apparaat

In dit artikel worden de vereisten en ondersteuningsvereisten voor het Azure Migrate-toestel samengevat. 

## <a name="deployment-scenarios"></a>Implementatiescenario's

Het Azure Migrate-toestel wordt gebruikt in de volgende scenario's.

**Scenario** | **Hulpprogramma** | **Gebruikt voor** 
--- | --- | ---
**VMware VM-beoordeling** | Azure Migreren:Serverbeoordeling | Ontdek VMware VM's<br/><br/> Machine-apps en -afhankelijkheden ontdekken<br/><br/> Verzamel metagegevens en prestatiemetadata voor beoordelingen.
**VMware VM agentless migratie** | Azure Migreren:Servermigratie | Ontdek VMware VM's <br/><br/> VMware VM's repliceren met agentloze migratie.
**Hyper-V VM-beoordeling** | Azure Migreren:Serverbeoordeling | Ontdek Hyper-V VM's<br/><br/> Verzamel metagegevens en prestatiemetadata voor beoordelingen.
**Fysieke machinebeoordeling** |  Azure Migreren:Serverbeoordeling |  Ontdek fysieke servers (of VM's die u als fysieke servers behandelt).<br/><br/> Verzamel metagegevens en prestatiemetadata voor beoordelingen.

## <a name="deployment-methods"></a>Implementatiemethoden

Het toestel kan met een aantal methoden worden geïmplementeerd:

- Het toestel kan worden geïmplementeerd met behulp van een sjabloon voor VMware VM's en Hyper-V VM's (OVA-sjabloon voor VMware of VHD voor Hyper-V).
- Als u geen sjabloon wilt gebruiken, u het toestel implementeren voor VMware of Hyper-V met behulp van een PowerShell-script.
- In Azure Government moet u het toestel implementeren met behulp van een script.
- Voor fysieke servers implementeert u het toestel altijd met behulp van een script.
- Download links zijn beschikbaar in de onderstaande tabellen.


## <a name="appliance---vmware"></a>Toestel - VMware 

In de volgende tabel worden de vereisten voor Azure Migrate-toestel voor VMware samengevat.

**Vereiste** | **Vmware** 
--- | ---
**Toestelonderdelen** | Het apparaat heeft de volgende onderdelen:<br/><br/> - **Beheer-app**: Dit is een web-app voor gebruikersinvoer tijdens de implementatie van apparaten. Wordt gebruikt bij het beoordelen van machines voor migratie naar Azure.<br/> - **Detectieagent**: De agent verzamelt machineconfiguratiegegevens. Wordt gebruikt bij het beoordelen van machines voor migratie naar Azure.<br/>- **Assessment agent**: De agent verzamelt prestatiegegevens. Wordt gebruikt bij het beoordelen van machines voor migratie naar Azure.<br/>- **Automatische updateservice**: Werkt toestelonderdelen bij (wordt elke 24 uur uitgevoerd).<br/>- **DRA-agent**: orkestreert VM-replicatie en coördineert de communicatie tussen gerepliceerde machines en Azure. Alleen gebruikt bij het repliceren van VMware VM's naar Azure met behulp van agentless migratie.<br/>- **Gateway:** stuurt gerepliceerde gegevens naar Azure. Alleen gebruikt bij het repliceren van VMware VM's naar Azure met behulp van agentless migratie.
**Ondersteunde implementatie** | Implementeren als VMware VM met OVA-sjabloon.<br/><br/> Implementeren als VMware VM of fysieke machine met PowerShell installatiescript.
**Projectondersteuning** |  Een toestel kan aan één project worden gekoppeld. <br/> Elk aantal apparaten kan aan één project worden gekoppeld.<br/> 
**Detectielimieten** | Een toestel kan tot 10.000 VMware VM's ontdekken op een vCenter-server.<br/> Een toestel kan verbinding maken met één vCenter-server.
**OVA-sjabloon** | Downloaden van portal https://aka.ms/migrate/appliance/vmwareof van .<br/><br/> Downloadgrootte is 11,2 GB.<br/><br/> De gedownloade toestelsjabloon wordt geleverd met een Windows Server 2016-evaluatielicentie, die 180 dagen geldig is. Als de evaluatieperiode bijna afloopt, raden we u aan een nieuw toestel te downloaden en te implementeren of de licentie van het besturingssysteem van de toestel-VM te activeren.
**PowerShell-script** | Script [downloaden](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Software/hardware** |  Het toestel moet draaien op de machine met Windows Server 2016, 32 GB RAM, 8 vCPU's, ongeveer 80 GB schijfopslag en een externe virtuele switch.<br/> Het toestel vereist toegang tot internet, rechtstreeks of via een proxy.<br/><br/> Als u het toestel op een VMware VM uitvoert, hebt u voldoende resources op de vCenterServer nodig om een VM toe te wijzen die aan de vereisten voldoet.<br/><br/> Als u het toestel op een fysieke machine uitvoert, controleert u of het toestel windows server 2016 gebruikt en voldoet aan de hardwarevereisten. 
**VMware-vereisten** | Als u het toestel implementeert als VMware VM, moet het worden geïmplementeerd op een ESXi-host met versie 5.5 of hoger.<br/><br/> vCenterServer met 5,5, 6,0, 6,5 of 6,7.
**VDDK (agentless migratie)** | Als u het toestel implementeert als vmware-vm en u een agentloze migratie uitvoert, moet de VMware vSphere VDDK op de vm van het toestel zijn geïnstalleerd.
**Hashwaarde-OVA** | [Controleer](tutorial-assess-vmware.md#verify-security) de hashwaarden van de OVA-sjabloon.
**Hash value-PowerShell-script** | [Controleer](deploy-appliance-script.md#verify-file-security) de hashwaarden van PowerShell-script.




## <a name="appliance---hyper-v"></a>Toestel - Hyper-V

**Vereiste** | **Hyper-V** 
--- | ---
**Toestelonderdelen** | Het apparaat heeft de volgende onderdelen:<br/><br/>- **Beheer-app**: Dit is een web-app voor gebruikersinvoer tijdens de implementatie van apparaten. Wordt gebruikt bij het beoordelen van machines voor migratie naar Azure.<br/> - **Detectieagent**: De agent verzamelt machineconfiguratiegegevens. Wordt gebruikt bij het beoordelen van machines voor migratie naar Azure.<br/>- **Assessment agent**: De agent verzamelt prestatiegegevens. Wordt gebruikt bij het beoordelen van machines voor migratie naar Azure.<br/>- **Automatische updateservice**: Werkt toestelonderdelen bij (wordt elke 24 uur uitgevoerd).
**Ondersteunde implementatie** | Implementeren als Hyper-V VM met behulp van een VHD-sjabloon.<br/><br/> Implementeren als Hyper-V VM of fysieke machine met behulp van een PowerShell-installatiescript.
**Projectondersteuning** |  Een toestel kan aan één project worden gekoppeld. <br/> Elk aantal apparaten kan aan één project worden gekoppeld.<br/> 
**Detectielimieten** | Een apparaat kan tot 5000 Hyper-V VM's ontdekken.<br/> Een toestel kan verbinding maken met maximaal 300 Hyper-V-hosts.
**VHD-sjabloon** | Gezipte map inclusief VHD. Downloaden van portal https://aka.ms/migrate/appliance/hypervof van .<br/><br/> Downloadgrootte is 10 GB.<br/><br/> De gedownloade toestelsjabloon wordt geleverd met een Windows Server 2016-evaluatielicentie, die 180 dagen geldig is. Als de evaluatieperiode bijna afloopt, raden we u aan een nieuw toestel te downloaden en te implementeren of de licentie van het besturingssysteem van de toestel-VM te activeren.
**PowerShell-script** | Script [downloaden](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Software/hardware***   |  Het toestel moet draaien op de machine met Windows Server 2016, 32 GB RAM, 8 vCPU's, ongeveer 80 GB schijfopslag en een externe virtuele switch.<br/> Het toestel heeft een statisch of dynamisch IP-adres nodig en vereist internettoegang, rechtstreeks of via een proxy.<br/><br/> Als u het toestel als Hyper-V-VM uitvoert, hebt u voldoende resources op de Hyper-V-host nodig om 16 GB RAM, 8 vCPU's, ongeveer 80 GB opslagruimte en een externe switch voor de toestel-VM toe te wijzen.<br/><br/> Als u het toestel op een fysieke machine uitvoert, controleert u of het toestel windows server 2016 gebruikt en voldoet aan de hardwarevereisten. 
**Hyper-V-vereisten** | Als u het toestel implementeert met de VHD-sjabloon, is de toestel-VM van Azure Migrate Hyper-V VM-versie 5.0.<br/><br/> De Hyper-V-host moet Windows Server 2012 R2 of hoger gebruiken. 
**Hashwaarde-VHD** | [Controleer](tutorial-assess-hyper-v.md#verify-security) de hashwaarden van de VHD-sjabloon.
**Hash value-PowerShell-script** | [Controleer](deploy-appliance-script.md#verify-file-security) de hashwaarden van PowerShell-script.


## <a name="appliance---physical"></a>Toestel - Fysiek

**Vereiste** | **Fysiek** 
--- | ---
**Toestelonderdelen** | Het apparaat heeft de volgende onderdelen: <br/><br/> - **Beheer-app**: Dit is een web-app voor gebruikersinvoer tijdens de implementatie van apparaten. Wordt gebruikt bij het beoordelen van machines voor migratie naar Azure.<br/> - **Detectieagent**: De agent verzamelt machineconfiguratiegegevens. Wordt gebruikt bij het beoordelen van machines voor migratie naar Azure.<br/>- **Assessment agent**: De agent verzamelt prestatiegegevens. Wordt gebruikt bij het beoordelen van machines voor migratie naar Azure.<br/>- **Automatische updateservice**: Werkt toestelonderdelen bij (wordt elke 24 uur uitgevoerd).
**Ondersteunde implementatie** | Implementeer als een speciale fysieke machine of een VM met behulp van een PowerShell-installatiescript. Het script is beschikbaar om te downloaden van de portal.
**Projectondersteuning** |  Een toestel kan aan één project worden gekoppeld. <br/> Elk aantal apparaten kan aan één project worden gekoppeld.<br/> 
**Detectielimieten** | Een toestel kan tot 250 fysieke servers detecteren.
**PowerShell-script** | Download het script (AzureMigrateInstaller.ps1) in een map met rits van de portal. [Meer informatie](tutorial-assess-physical.md#set-up-the-appliance). U ook [direct downloaden.](https://go.microsoft.com/fwlink/?linkid=2105112)<br/><br/> Downloadgrootte is 59,7 MB.
**Software/hardware** |  Het toestel moet draaien op de machine met Windows Server 2016, 32 GB RAM, 8 vCPU's, ongeveer 80 GB schijfopslag en een externe virtuele switch.<br/> Het toestel heeft een statisch of dynamisch IP-adres nodig en vereist internettoegang, rechtstreeks of via een proxy.<br/><br/> Als u het toestel op een fysieke machine uitvoert, controleert u of het toestel windows server 2016 gebruikt en voldoet aan de hardwarevereisten. 
**Hash-waarde** | [Controleer](deploy-appliance-script.md#verify-file-security) de hashwaarden van PowerShell-script.

## <a name="url-access"></a>URL-toegang

Het Azure Migrate-toestel heeft connectiviteit met internet nodig.

- Wanneer u het toestel implementeert, controleert Azure Migrate de vereiste URL's.
- Als u een op URL gebaseerde proxy gebruikt om verbinding te maken met internet, moet u toegang tot deze URL's toestaan, zodat de proxy alle CNAME-records oplost die zijn ontvangen terwijl u de URL's opzoekt.

### <a name="public-cloud-urls"></a>URL's in de openbare cloud

**Url** | **Details**  
--- | --- |
*.portal.azure.com  | Ga naar Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Meld u aan bij uw Azure-abonnement.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Maak AD-apps (Azure Active Directory) om te communiceren met Azure Migrate.
management.azure.com | Azure AD-apps maken om te communiceren met de Azure Migrate-service.
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne controle.
*.vault.azure.net | Geheimen beheren in de Azure Key Vault.
aka.ms/* | Geef toegang tot aka-links. Wordt gebruikt voor updates van Azure Migrate appliance.
download.microsoft.com/download | Toestaan downloads van Microsoft downloaden.
*.servicebus.windows.net | Communicatie tussen het toestel en de Azure Migrate-service.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Verbinding maken met URL's van Azure Migrate-service.
*.hypervrecoverymanager.windowsazure.com | **Gebruikt voor VMware agentless migratie**<br/><br/> Verbinding maken met URL's van Azure Migrate-service.
*.blob.core.windows.net |  **Gebruikt voor VMware agentless migratie**<br/><br/>Gegevens uploaden naar opslag voor migratie.

### <a name="government-cloud-urls"></a>URL's van overheidscloud

**Url** | **Details**  
--- | --- |
*.portal.azure.us  | Ga naar Azure Portal.
graph.windows.net | Meld u aan bij uw Azure-abonnement.
login.microsoftonline.us  | Maak AD-apps (Azure Active Directory) om te communiceren met Azure Migrate.
management.usgovcloudapi.net | Azure AD-apps maken om te communiceren met de Azure Migrate-service.
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne controle.
*.vault.usgovcloudapi.net | Geheimen beheren in de Azure Key Vault.
aka.ms/* | Geef toegang tot aka-links. Wordt gebruikt voor updates van Azure Migrate appliance.
download.microsoft.com/download | Toestaan downloads van Microsoft downloaden.
*.servicebus.usgovcloudapi.net  | Communicatie tussen het toestel en de Azure Migrate-service.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Verbinding maken met URL's van Azure Migrate-service.
*.hypervrecoverymanager.windowsazure.us | **Gebruikt voor VMware agentless migratie**<br/><br/> Verbinding maken met URL's van Azure Migrate-service.
*.blob.core.usgovcloudapi.net  |  **Gebruikt voor VMware agentless migratie**<br/><br/>Gegevens uploaden naar opslag voor migratie.
*.applicationinsights.us | Wordt gebruikt door de gateway-agent op het toestel om toegang te krijgen tot het Application Insights-eindpunt voor diagnostische monitoring.





## <a name="collected-data---vmware"></a>Verzamelde gegevens - VMware

Het toestel verzamelt metagegevens, prestatiegegevens en afhankelijkheidsanalysegegevens (als agentless [afhankelijkheidsanalyse](concepts-dependency-visualization.md) wordt gebruikt).

### <a name="metadata"></a>Metagegevens

Metagegevens die door het Azure Migrate-toestel zijn ontdekt, helpen u om erachter te komen of machines en apps klaar zijn voor migratie naar Azure, machines en apps van de juiste grootte, plannen kosten en het analyseren van toepassingsafhankelijkheden. Microsoft gebruikt deze gegevens niet bij een controle op de naleving van de licentie.

Hier vindt u de volledige lijst met VMware VM-metagegevens die het toestel verzamelt en naar Azure verzendt.

**Gegevens** | **Teller**
--- | --- 
**Machinegegevens** | 
VM-id | Vm. Config.InstanceUuid 
VM-naam | Vm. Config.Name
vCenter-server-id | VMwareClient.Instance.Uuid
VM-beschrijving | Vm. Samenvatting.Config.Annotatie
Naam van licentieproduct | Vm. Client.serviceContent.About.LicenseProductName
Besturingssysteemtype | Vm. SummaryConfig.GuestFullName
Opstarttype | Vm. Config.Firmware
Aantal kerngeheugens | Vm. Config.Hardware.NumCPU
Geheugen (MB) | Vm. Config.Hardware.GeheugenMB
Aantal schijven | Vm. Config.Hardware.Device.ToList(). FindAll(x => is VirtualDisk.telling
Lijst met schijfgrootte | Vm. Config.Hardware.Device.ToList(). FindAll(x => is VirtualDisk)
Lijst met netwerkadapters | Vm. Config.Hardware.Device.ToList(). FindAll(x => is VirtualEthernet.telling
CPU-gebruik | cpu.usage.average
Geheugengebruik |mem.usage.average
**Details per schijf** | 
Waarde van schijfsleutel | Schijf. Sleutel
Dikunit nummer | Schijf. Eenheidsnummer
Waarde van de sleutelwaarde van schijfcontroller | Schijf. ControllerKey.Waarde
Gigabytes ingericht | virtualDisk.DeviceInfo.Samenvatting
Schijfnaam | Waarde gegenereerd met schijf. UnitNumber, schijf. Sleutel, schijf. ControllerKey.VAlue
Leesbewerkingen per seconde | virtualDisk.numberReadAveraged.average
Schrijfbewerkingen per seconde | virtualDisk.numberWriteAveraged.average
Doorvoer lezen (MB per seconde) | virtualDisk.read.average
Schrijfdoorvoer (MB per seconde) | virtualDisk.write.average
**Per NIC-details** | 
Naam netwerkadapter | Nic. Sleutel
MAC-adres | ((VirtualEthernetCard)nic). MacAddress MacAddress
IPv4-adressen | Vm. Guest.Net
IPv6-adressen | Vm. Guest.Net
Doorvoer lezen (MB per seconde) | net.received.average
Schrijfdoorvoer (MB per seconde) | net.transmitted.average
**Details van het voorraadpad** | 
Naam | Container. GetType(). Naam
Type onderliggend object | Container. ChildType
Referentiegegevens | Container. MoRef (MoRef)
Bovenliggende gegevens | Container.Parent
Mapgegevens per VM | ((Map)container). Onderliggende entiteit.type
Datacentergegevens per VM | ((Datacenter)container). VmFolder
Datacentergegevens per hostmap | ((Datacenter)container). HostMap
Clustergegevens per host | ((container ClusterComputeResource)). Host
Hostgegevens per VM | ((HostSystem)container). Vm

### <a name="performance-data"></a>Prestatiegegevens


Hier volgen de VMware VM-prestatiegegevens die het toestel verzamelt en naar Azure verzendt.

**Gegevens** | **Teller** | **Effect beoordeling**
--- | --- | ---
CPU-gebruik | cpu.usage.average | Aanbevolen VM-grootte/-kosten
Geheugengebruik | mem.usage.average | Aanbevolen VM-grootte/-kosten
Doorvoer van schijflees (MB per seconde) | virtualDisk.read.average | Berekening voor schijfgrootte, opslagkosten, VM-grootte
Doorvoer van schijfschrijft (MB per seconde) | virtualDisk.write.average | Berekening voor schijfgrootte, opslagkosten, VM-grootte
Schijfleesbewerkingen per seconde | virtualDisk.numberReadAveraged.average | Berekening voor schijfgrootte, opslagkosten, VM-grootte
Schijfschrijft bewerkingen per seconde | virtualDisk.numberWriteAveraged.average  | Berekening voor schijfgrootte, opslagkosten, VM-grootte
NIC-leesdoorvoer (MB per seconde) | net.received.average | Berekening voor VM-grootte
NIC-schrijfdoorvoer (MB per seconde) | net.transmitted.average  |Berekening voor VM-grootte

### <a name="app-dependencies-metadata"></a>Metagegevens van app-afhankelijkheden

Agentless afhankelijkheidsanalyse verzamelt verbindings- en procesgegevens.

#### <a name="connection-data"></a>Verbindingsgegevens

Hier volgen de verbindingsgegevens die het toestel verzamelt van elke VM die is ingeschakeld voor analyse van agentloze afhankelijkheid. Deze gegevens worden verzonden naar Azure.

**Gegevens** | **Gebruikte opdracht** 
--- | --- 
Lokale poort | Netstat
Lokaal IP-adres | Netstat
Externe poort | Netstat
Extern IP-adres | Netstat
TCP-verbindingsstatus | Netstat
Proces-id | Netstat
Nee. actieve verbindingen | Netstat

#### <a name="process-data"></a>Gegevens verwerken
Hier volgen de procesgegevens die het toestel verzamelt van elke VM die is ingeschakeld voor analyse van agentloze afhankelijkheid. Deze gegevens worden verzonden naar Azure.

**Gegevens** | **WMI-klasse** | **WMI-klasse, eigenschap**
--- | --- | ---
Procesnaam | Win32_Process | Uitvoerbaar Pad
Procesargumenten | Win32_Process | Commandline
De naam van de toepassing | Win32_Process | VersionInfo.ProductName, parameter van de eigenschap ExecutablePath

#### <a name="linux-vm-data"></a>Linux VM-gegevens

Hier volgen de verbindings- en procesgegevens die het toestel verzamelt van elke Linux VM die is ingeschakeld voor analyse van de afhankelijkheid zonder agent. Deze gegevens worden verzonden naar Azure.

**Gegevens** | **Gebruikte opdracht** 
--- | ---
Lokale poort | Netstat 
Lokaal IP-adres | Netstat 
Externe poort | Netstat 
Extern IP-adres | Netstat 
TCP-verbindingsstatus | Netstat 
Nee. actieve verbindingen | Netstat
Proces-id  | Netstat 
Procesnaam | Ps
Procesargumenten | Ps
De naam van de toepassing | dpkg of rpm



## <a name="collected-data---hyper-v"></a>Verzamelde gegevens - Hyper-V

Het toestel verzamelt metagegevens, prestatiegegevens en afhankelijkheidsanalysegegevens (als agentless [afhankelijkheidsanalyse](concepts-dependency-visualization.md) wordt gebruikt).

### <a name="metadata"></a>Metagegevens
Metagegevens die door het Azure Migrate-toestel zijn ontdekt, helpen u om erachter te komen of machines en apps klaar zijn voor migratie naar Azure, machines en apps van de juiste grootte, plannen kosten en het analyseren van toepassingsafhankelijkheden. Microsoft gebruikt deze gegevens niet bij een controle op de naleving van de licentie.

Hier vindt u de volledige lijst met Hyper-V VM-metagegevens die het toestel verzamelt en naar Azure verzendt.

**Gegevens* | **WMI-KLASSE** | **WMI-KLASSE EIGENSCHAP**
--- | --- | ---
**Machinegegevens** | 
Serienummer van BIOS _ Msvm_BIOSElement | BIOSSerialNumber BIOSSerialNumber
VM-type (Gen 1 of 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType VirtualSystemSubType
VM-weergavenaam | Msvm_VirtualSystemSettingData | ElementName
VM-versie | Msvm_ProcessorSettingData | Virtueel aantal
Geheugen (bytes) | Msvm_MemorySettingData | Virtueel aantal
Maximaal geheugen dat kan worden verbruikt door VM | Msvm_MemorySettingData | Limiet
Dynamisch geheugen ingeschakeld | Msvm_MemorySettingData | DynamicMemoryEnabled
Naam/versie/FQDN besturingssysteem | Msvm_KvpExchangeComponent | Namengegevens van GuestIntrinsicExchangeItems
VM-vermogensstatus | Msvm_ComputerSystem | EnabledState
**Details per schijf** | 
Schijf-id | Msvm_VirtualHardDiskSettingData | VirtualDiskId VirtualDiskId
Type virtuele harde schijf | Msvm_VirtualHardDiskSettingData | Type
Virtuele harde schijfgrootte | Msvm_VirtualHardDiskSettingData | MaxInternalSize MaxInternalSize
Virtuele harde schijf ouder | Msvm_VirtualHardDiskSettingData | ParentPath
**Per NIC-details** | 
IP-adressen (synthetische NIC's) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP ingeschakeld (synthetische NIC's) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-id (synthetische NIC's) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC-adres (synthetische NIC's) | Msvm_SyntheticEthernetPortSettingData | Adres
NIC-id (oudere NIC's) | MsvmEmulatedEthernetPortSetting-gegevens | InstanceID
NIC MAC ID (oudere NIC's) | MsvmEmulatedEthernetPortSetting-gegevens | Adres

### <a name="performance-data"></a>Prestatiegegevens

Hier volgen de prestatiegegevens van Hyper VM die het toestel verzamelt en naar Azure verzendt.

**Prestatiemeterklasse** | **Teller** | **Effect beoordeling**
--- | --- | ---
Hyper-V Hypervisor Virtual Processor | % Gastruntijd | Aanbevolen VM-grootte/-kosten
Hyper-V Dynamische Geheugen-VM | Huidige druk (%)<br/> Zichtbaar fysiek geheugen voor gasten (MB) | Aanbevolen VM-grootte/-kosten
Hyper-V virtueel opslagapparaat | Bytes/Seconde lezen | Berekening voor schijfgrootte, opslagkosten, VM-grootte
Hyper-V virtueel opslagapparaat | Bytes/seconde schrijven | Berekening voor schijfgrootte, opslagkosten, VM-grootte
Virtuele netwerkadapter hyper-v | Ontvangen bytes/seconde | Berekening voor VM-grootte
Virtuele netwerkadapter hyper-v | Verzonden bytes/seconde | Berekening voor VM-grootte

- CPU-gebruik is de som van alle gebruik, voor alle virtuele processors gekoppeld aan een VM.
- Geheugengebruik is (Huidige druk * Gast zichtbaar fysiek geheugen) / 100.
- Schijf- en netwerkgebruikswaarden worden verzameld uit de vermelde Hyper-V-prestatiemeteritems.

## <a name="appliance-upgrades"></a>Toestelupgrades

Het toestel wordt bijgewerkt terwijl de Azure Migrate-agents die op het toestel worden uitgevoerd, worden bijgewerkt. Dit gebeurt automatisch omdat automatisch bijwerken standaard is ingeschakeld op het toestel. U deze standaardinstelling wijzigen om de agents handmatig bij te werken.

- **Automatische update uitschakelen:** U schakelt automatische update in het register uit door HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" sleutel in te stellen op 0 (DWORD). Als u besluit om handmatige updates te gebruiken, is het belangrijk om alle agents op het toestel tegelijkertijd bij te werken met behulp van de knop **Bijwerken** voor elke verouderde agent op het toestel.
- **Handmatig bijwerken**: Zorg er voor handmatige updates voor dat u alle agents op het toestel bijwerkt, met behulp van de knop **Bijwerken** voor elke verouderde agent op het toestel. U de update-instelling op elk gewenst moment terugzetten naar automatische updates.

![Toestel automatisch bijwerken](./media/migrate-appliance/autoupdate.png)

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het](how-to-set-up-appliance-vmware.md) instellen van het toestel voor VMware.
- [Meer informatie over het](how-to-set-up-appliance-hyper-v.md) instellen van het apparaat voor Hyper-V.
- [Meer informatie over het](how-to-set-up-appliance-physical.md) instellen van het toestel voor fysieke servers.

