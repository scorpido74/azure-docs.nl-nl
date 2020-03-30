---
title: Vm-netwerkdoorvoer optimaliseren | Microsoft Documenten
description: Meer informatie over het optimaliseren van de netwerkdoorvoer van Azure-virtuele machines.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: be5f38bdeaf51dbe23006ecf30b4deb66aa7402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75690891"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Netwerkdoorvoer optimaliseren voor virtuele Azure-machines

Azure virtual machines (VM) hebben standaardnetwerkinstellingen die verder kunnen worden geoptimaliseerd voor netwerkdoorvoer. In dit artikel wordt beschreven hoe u de netwerkdoorvoer voor Microsoft Azure Windows- en Linux-VM's optimaliseren, inclusief grote distributies zoals Ubuntu, CentOS en Red Hat.

## <a name="windows-vm"></a>Windows-VM

Als uw Windows VM [accelerated networking](create-vm-accelerated-networking-powershell.md)ondersteunt, is het inschakelen van die functie de optimale configuratie voor doorvoer. Voor alle andere Windows VM's kan het gebruik van Receive Side Scaling (RSS) een hogere maximale doorvoer bereiken dan een VM zonder RSS. RSS kan standaard worden uitgeschakeld in een Windows-vm. Voer de volgende stappen uit om te bepalen of RSS is ingeschakeld en deze in te schakelen als deze momenteel is uitgeschakeld:

1. Kijk of RSS is ingeschakeld voor `Get-NetAdapterRss` een netwerkadapter met de powershell-opdracht. In het volgende voorbeeld `Get-NetAdapterRss`is de uitvoer die wordt geretourneerd van de RSS niet ingeschakeld.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Voer de volgende opdracht in om RSS in te schakelen:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    De vorige opdracht heeft geen uitvoer. De opdracht wijzigde NIC-instellingen, waardoor tijdelijk verbindingsverlies voor ongeveer een minuut. Er wordt een dialoogvenster Opnieuw verbinden weergegeven tijdens het verbindingsverlies. Connectiviteit wordt meestal hersteld na de derde poging.
3. Controleer of RSS is ingeschakeld in `Get-NetAdapterRss` de VM door de opdracht opnieuw in te voeren. Als dit is gelukt, wordt de volgende voorbeelduitvoer geretourneerd:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Linux-VM

RSS is standaard ingeschakeld in een Azure Linux VM. Linux-kernels die sinds oktober 2017 zijn uitgebracht, bevatten nieuwe opties voor netwerkoptimalisaties waarmee een Linux-VM een hogere netwerkdoorvoer kan bereiken.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu voor nieuwe implementaties

De Ubuntu Azure-kernel biedt de beste netwerkprestaties op Azure en is sinds 21 september 2017 de standaardkernel. Om deze kernel te krijgen, installeer eerst de nieuwste ondersteunde versie van 16.04-LTS, als volgt:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

Nadat de creatie is voltooid, voert u de volgende opdrachten in om de nieuwste updates te ontvangen. Deze stappen werken ook voor VM's waarop de Ubuntu Azure-kernel wordt uitgevoerd.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

De volgende optionele opdrachtset kan handig zijn voor bestaande Ubuntu-implementaties die al over de Azure-kernel beschikken, maar die geen verdere updates met fouten hebben opgeleverd.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu Azure kernel upgrade voor bestaande VM's

Aanzienlijke doorvoerprestaties kunnen worden bereikt door te upgraden naar de Azure Linux-kernel. Als u wilt controleren of u deze kernel hebt, controleert u de kernelversie.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Als uw VM de Azure-kernel niet heeft, begint het versienummer meestal met '4.4'. Als de VM de Azure-kernel niet heeft, voert u de volgende opdrachten uit als hoofd:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Om de nieuwste optimalisaties te krijgen, is het het beste om een VM te maken met de nieuwste ondersteunde versie door de volgende parameters op te geven:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Nieuwe en bestaande VM's kunnen profiteren van het installeren van de nieuwste Linux Integration Services (LIS). De doorvoer optimalisatie is in LIS, vanaf 4.2.2-2, hoewel latere versies bevatten verdere verbeteringen. Voer de volgende opdrachten in om de nieuwste LIS te installeren:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Om de optimalisaties te krijgen, is het het beste om een VM te maken met de nieuwste ondersteunde versie door de volgende parameters op te geven:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Nieuwe en bestaande VM's kunnen profiteren van het installeren van de nieuwste Linux Integration Services (LIS). De doorvoeroptimalisatie bevindt zich in LIS, vanaf 4,2. Voer de volgende opdrachten in om LIS te downloaden en te installeren:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Lees meer over Linux Integration Services Versie 4.2 voor Hyper-V door de [downloadpagina](https://www.microsoft.com/download/details.aspx?id=55106)te bekijken.

## <a name="next-steps"></a>Volgende stappen
* Bekijk het geoptimaliseerde resultaat met Azure VM voor het [testen van bandbreedte/doorvoer](virtual-network-bandwidth-testing.md) voor uw scenario.
* Lees hoe [bandbreedte wordt toegewezen aan virtuele machines](virtual-machine-network-throughput.md)
* Meer informatie met veelgestelde vragen over [Azure Virtual Network (FAQ)](virtual-networks-faq.md)
