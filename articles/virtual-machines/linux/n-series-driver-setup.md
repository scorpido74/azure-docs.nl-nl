---
title: Azure N-serie GPU-stuurprogramma-installatie voor Linux
description: Nvidia GPU-stuurprogramma's instellen voor VM's uit de N-serie met Linux in Azure
services: virtual-machines-linux
author: cynthn
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.openlocfilehash: b424361f318504f96a57ee67722e725fbafc6561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944559"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Nvidia GPU-stuurprogramma's installeren op VM's uit de N-serie met Linux

Om te profiteren van de GPU-mogelijkheden van Azure N-serie VM's met Linux-serie, moeten NVIDIA GPU-stuurprogramma's worden geïnstalleerd. De [NVIDIA GPU Driver Extension](../extensions/hpccompute-gpu-linux.md) installeert de juiste NVIDIA CUDA- of GRID-stuurprogramma's op een VM uit de N-serie. Installeer of beheer de extensie met behulp van de Azure-portal of hulpprogramma's, zoals de Azure CLI- of Azure Resource Manager-sjablonen. Bekijk de [NVIDIA GPU Driver Extension-documentatie](../extensions/hpccompute-gpu-linux.md) voor ondersteunde distributies en implementatiestappen.

Als u ervoor kiest GPU-stuurprogramma's handmatig te installeren, biedt dit artikel ondersteunde distributies, stuurprogramma's en installatie- en verificatiestappen. Handmatige handleiding driver setup informatie is ook beschikbaar voor [Windows VM's](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zie [VM-formaten](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor VM-formaten uit de N-serie, opslagcapaciteiten en schijfdetails. 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>CUDA-stuurprogramma's installeren op VM's uit de N-serie

Hier volgen stappen om CUDA-stuurprogramma's te installeren vanuit de NVIDIA CUDA Toolkit op VM's uit de N-serie. 


C- en C++-ontwikkelaars kunnen optioneel de volledige Toolkit installeren om GPU-versnelde applicaties te bouwen. Zie voor meer informatie de [CUDA Installation Guide](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Als u CUDA-stuurprogramma's wilt installeren, maakt u een SSH-verbinding met elke virtuele machine. Voer de volgende opdracht uit om te controleren of het systeem een GPU met CUDA-status heeft:

```bash
lspci | grep -i NVIDIA
```
U ziet uitvoer vergelijkbaar met het volgende voorbeeld (met een NVIDIA Tesla K80-kaart):

![lspci-opdrachtuitvoer](./media/n-series-driver-setup/lspci.png)

Voer vervolgens installatieopdrachten uit die specifiek zijn voor uw distributie.

### <a name="ubuntu"></a>Ubuntu 

1. Download en installeer de CUDA drivers van de NVIDIA website. Bijvoorbeeld voor Ubuntu 16.04 LTS:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}

   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update

   sudo apt-get install cuda-drivers

   ```

   De installatie kan enkele minuten duren.

2. Als u de volledige CUDA-toolkit optioneel wilt installeren, typt u het:

   ```bash
   sudo apt-get install cuda
   ```

3. Start de VM opnieuw op en ga verder met het verifiëren van de installatie.

#### <a name="cuda-driver-updates"></a>CUDA-stuurprogramma-updates

We raden u aan CUDA-stuurprogramma's na de implementatie periodiek bij te werken.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS of Red Hat Enterprise Linux

1. De kernel bijwerken (aanbevolen). Als u ervoor kiest de kernel niet `kernel-devel` bij `dkms` te werken, moet u ervoor zorgen dat de versies van en geschikt zijn voor uw kernel.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
   sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

   ```bash
   wget https://aka.ms/lis
 
   tar xvzf lis
 
   cd LISISO
 
   sudo ./install.sh
 
   sudo reboot
   ```
 
3. Maak opnieuw verbinding met de virtuele machine en ga verder met de installatie met de volgende opdrachten:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

   sudo yum install dkms

   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

   wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   De installatie kan enkele minuten duren. 

4. Als u de volledige CUDA-toolkit optioneel wilt installeren, typt u het:

   ```bash
   sudo yum install cuda
   ```

5. Start de VM opnieuw op en ga verder met het verifiëren van de installatie.

### <a name="verify-driver-installation"></a>De installatie van het stuurprogramma controleren

Om de GPU-apparaatstatus op te vragen, gaat SSH naar de VM en voert u het hulpprogramma [voor de nvidia-smi-commandline](https://developer.nvidia.com/nvidia-system-management-interface) uit dat met het stuurprogramma is geïnstalleerd. 

Als het stuurprogramma is geïnstalleerd, ziet u uitvoer vergelijkbaar met het volgende. Houd er rekening mee dat **GPU-Util** 0% weergeeft, tenzij u momenteel een GPU-werkbelasting op de VM uitvoert. Uw stuurprogrammaversie en GPU-details kunnen afwijken van de weergegeven versie.

![NVIDIA-apparaatstatus](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA-netwerkconnectiviteit

RDMA-netwerkconnectiviteit kan worden ingeschakeld op VM's die geschikt zijn voor RDMA-compatibele N-serie, zoals NC24r die is geïmplementeerd in dezelfde beschikbaarheidsset of in één plaatsingsgroep in een VM-schaalset. Het RDMA-netwerk ondersteunt MPI-verkeer (Message Passing Interface) voor toepassingen die worden uitgevoerd met Intel MPI 5.x of een latere versie. Aanvullende eisen volgen:

### <a name="distributions"></a>Distributies

Implementeer VM's die geschikt zijn voor RDMA's uit een van de afbeeldingen in de Azure Marketplace die RDMA-connectiviteit op VM's uit de N-serie ondersteunt:
  
* **Ubuntu 16.04 LTS** - Rdma-stuurprogramma's configureren op de VM en registreer u bij Intel om Intel MPI te downloaden:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-gebaseerde 7.4 HPC** - RDMA drivers en Intel MPI 5.1 zijn geïnstalleerd op de VM.

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>GRID-stuurprogramma's installeren op VM's uit de NV- of NVv3-serie

Als u NVIDIA GRID-stuurprogramma's wilt installeren op VM's uit de NV- of NVv3-serie, maakt u een SSH-verbinding met elke VM en volgt u de stappen voor uw Linux-distributie. 

### <a name="ubuntu"></a>Ubuntu 

1. Voer de opdracht `lspci` uit. Controleer of de NVIDIA M60-kaart of -kaarten zichtbaar zijn als PCI-apparaten.

2. Updates installeren.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. Schakel de Nouveau kernel driver, die onverenigbaar is met de NVIDIA driver. (Gebruik alleen de NVIDIA-driver op NV- of NVv2 VM's.) Maak hiervoor een bestand `/etc/modprobe.d` met `nouveau.conf` de volgende inhoud:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Start de VM opnieuw op en maak opnieuw verbinding. Afsluiten X-server:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Download en installeer het GRID-stuurprogramma:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Wanneer u wordt gevraagd of u het nvidia-xconfig-hulpprogramma wilt uitvoeren om uw X-configuratiebestand bij te werken, selecteert u **Ja**.

7. Na installatie voltooid, kopiëren / etc / nvidia/ gridd.conf.template naar een nieuw bestand gridd.conf op locatie / etc / nvidia /

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Voeg het `/etc/nvidia/gridd.conf`volgende toe aan:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Verwijder het `/etc/nvidia/gridd.conf` volgende uit als het aanwezig is:
 
   ```
   FeatureType=0
   ```
10. Start de VM opnieuw op en ga verder met het verifiëren van de installatie.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS of Red Hat Enterprise Linux 

1. Werk de kernel en DKMS (aanbevolen) bij. Als u ervoor kiest de kernel niet `kernel-devel` bij `dkms` te werken, moet u ervoor zorgen dat de versies van en geschikt zijn voor uw kernel.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. Schakel de Nouveau kernel driver, die onverenigbaar is met de NVIDIA driver. (Gebruik alleen de NVIDIA-driver op NV- of NV2 VM's.) Maak hiervoor een bestand `/etc/modprobe.d` met `nouveau.conf` de volgende inhoud:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Start de VM opnieuw op, maak opnieuw verbinding en installeer de nieuwste [Linux Integration Services voor Hyper-V en Azure.](https://www.microsoft.com/download/details.aspx?id=55106)
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Maak opnieuw verbinding met `lspci` de virtuele machine en voer de opdracht uit. Controleer of de NVIDIA M60-kaart of -kaarten zichtbaar zijn als PCI-apparaten.
 
5. Download en installeer het GRID-stuurprogramma:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Wanneer u wordt gevraagd of u het nvidia-xconfig-hulpprogramma wilt uitvoeren om uw X-configuratiebestand bij te werken, selecteert u **Ja**.

7. Na installatie voltooid, kopiëren / etc / nvidia/ gridd.conf.template naar een nieuw bestand gridd.conf op locatie / etc / nvidia /
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Voeg het `/etc/nvidia/gridd.conf`volgende toe aan:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Verwijder het `/etc/nvidia/gridd.conf` volgende uit als het aanwezig is:
 
   ```
   FeatureType=0
   ```
10. Start de VM opnieuw op en ga verder met het verifiëren van de installatie.


### <a name="verify-driver-installation"></a>De installatie van het stuurprogramma controleren


Om de GPU-apparaatstatus op te vragen, gaat SSH naar de VM en voert u het hulpprogramma [voor de nvidia-smi-commandline](https://developer.nvidia.com/nvidia-system-management-interface) uit dat met het stuurprogramma is geïnstalleerd. 

Als het stuurprogramma is geïnstalleerd, ziet u uitvoer vergelijkbaar met het volgende. Houd er rekening mee dat **GPU-Util** 0% weergeeft, tenzij u momenteel een GPU-werkbelasting op de VM uitvoert. Uw stuurprogrammaversie en GPU-details kunnen afwijken van de weergegeven versie.

![NVIDIA-apparaatstatus](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11-server
Als u een X11-server nodig hebt voor externe verbindingen met een NV- of NVv2-vm, wordt [x11vnc](http://www.karlrunge.com/x11vnc/) aanbevolen omdat het hardwareversnelling van afbeeldingen mogelijk maakt. De BusID van het M60-apparaat moet handmatig worden toegevoegd aan `etc/X11/xorg.conf`het X11-configuratiebestand (meestal). Voeg `"Device"` een sectie toe die vergelijkbaar is met het volgende:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Bovendien werkt u `"Screen"` uw sectie bij om dit apparaat te gebruiken.
 
De decimale BusID kan worden gevonden door

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
De BusID kan veranderen wanneer een VM wordt herplaatst of opnieuw wordt opgestart. Daarom u een script maken om de BusID in de X11-configuratie bij te werken wanneer een VM opnieuw wordt opgestart. Maak bijvoorbeeld een script `busidupdate.sh` met de naam (of een andere naam die u kiest) met inhoud die vergelijkbaar is met het volgende:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Maak vervolgens een item voor `/etc/rc.d/rc3.d` uw updatescript in, zodat het script wordt aangeroepen als root op het opstarten.

## <a name="troubleshooting"></a>Problemen oplossen

* U de `nvidia-smi` persistentiemodus instellen met behulp van de uitvoer van de opdracht is sneller wanneer u kaarten moet opvragen. Als u de `nvidia-smi -pm 1`persistentiemodus wilt instellen, voert u uit . Houd er rekening mee dat als de vm opnieuw wordt opgestart, de modusinstelling verdwijnt. U altijd de modusinstelling scripten die u bij het opstarten wilt uitvoeren.
* Als u de NVIDIA CUDA-stuurprogramma's hebt bijgewerkt naar de nieuwste versie en merkt dat RDMA-connectivcity niet meer werkt, [installeert u de RDMA-stuurprogramma's opnieuw](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#rdma-network-connectivity) om die connectiviteit te bepalen. 

## <a name="next-steps"></a>Volgende stappen

* Als u een Linux VM-afbeelding wilt vastleggen met uw geïnstalleerde NVIDIA-stuurprogramma's, raadpleegt u [Hoe u een virtuele Linux-machine generaliseren en vastleggen.](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
