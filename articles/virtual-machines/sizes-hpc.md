---
title: Azure VM-formaten - HPC | Microsoft Documenten
description: Geeft een lijst van de verschillende formaten die beschikbaar zijn voor virtuele machines met een hoge prestaties in Azure. Bevat informatie over het aantal vCPU's, gegevensschijven en NIC's, evenals opslagdoorvoer en netwerkbandbreedte voor formaten in deze serie.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: df22c857571e51bb886ff1d25db185a306999540
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420861"
---
# <a name="high-performance-computing-vm-sizes"></a>Vm-formaten met hoge prestaties

Azure H-serie virtuele machines (VM's) zijn ontworpen om prestaties van leiderschapsklasse, MPI-schaalbaarheid en kostenefficiëntie te leveren voor een verscheidenheid aan echte HPC-workloads.

[HBv2-serie](hbv2-series.md) Vm's zijn voorzien van 200 Gb/sec Mellanox HDR InfiniBand, terwijl zowel HB- als HC-serie VM's beschikken over 100 Gb/sec Mellanox EDR InfiniBand. Elk van deze VM-typen zijn verbonden in een niet-blokkerende vetstructuur voor geoptimaliseerde en consistente RDMA-prestaties. HBv2 VM's ondersteunen Adaptive Routing en de Dynamic Connected Transport (DCT, naast standaard RC- en UD-transporten). Deze functies verbeteren de prestaties, schaalbaarheid en consistentie van toepassingen en het gebruik ervan wordt ten zeerste aanbevolen.

[HB-serie](hb-series.md) VM's zijn geoptimaliseerd voor toepassingen die worden aangedreven door geheugenbandbreedte, zoals vloeistofdynamica, expliciete eindige elementenanalyse en weermodellering. HB VM's zijn voorzien van 60 AMD EPYC 7551 processorcores, 4 GB RAM per CPU-kern en geen hyperthreading. Het AMD EPYC-platform biedt meer dan 260 GB/s geheugenbandbreedte.

[HC-serie](hc-series.md) VM's zijn geoptimaliseerd voor toepassingen die worden aangedreven door dichte berekeningen, zoals impliciete eindige elementenanalyse, moleculaire dynamica en computationele chemie. HC VM's zijn voorzien van 44 Intel Xeon Platinum 8168-processorcores, 8 GB RAM per CPU-kern en geen hyperthreading. Het Intel Xeon Platinum-platform ondersteunt Intel's rijke ecosysteem van softwaretools zoals de Intel Math Kernel Library.

[H-serie](h-series.md) VM's zijn geoptimaliseerd voor toepassingen die worden aangedreven door hoge CPU-frequenties of grote geheugen-per-core-vereisten. Vm's uit de H-serie zijn voorzien van 8 of 16 Intel Xeon E5 2667 v3-processorcores, 7 of 14 GB RAM per CPU-kern en geen hyperthreading. H-serie beschikt over 56 Gb/sec Mellanox FDR InfiniBand in een niet-blokkerende vetboom configuratie voor consistente RDMA prestaties. Vm's uit de H-serie ondersteunen Intel MPI 5.x en MS-MPI.

> [!NOTE]
> De A8 – A11 VM's zijn gepland voor pensionering op 3/2021. Zie [HPC-migratiegids](https://azure.microsoft.com/resources/hpc-migration-guide/)voor meer informatie.

## <a name="rdma-capable-instances"></a>Met RDMA compatibele exemplaren

De meeste HPC VM-formaten (HBv2, HB, HC, H16r, H16mr, A8 en A9) zijn voorzien van een netwerkinterface voor RDMA-connectiviteit (Remote Direct Memory Access). Geselecteerde [N-serie]https://docs.microsoft.com/azure/virtual-machines/nc-series) ( maten aangeduid met 'r' zoals de NC24rs configuraties (NC24rs_v3, NC24rs_v2 en NC24r) zijn ook RDMA-geschikt. Deze interface is een aanvulling op de standaard Azure-netwerkinterface die beschikbaar is in de andere VM-formaten.

Met deze interface kunnen de RDMA-geschikte exemplaren communiceren via een IB-netwerk (InfiniBand), dat werkt tegen HDR-snelheden voor HBv2, EDR-tarieven voor HB-, HC-, FDR-tarieven voor H16r-, H16mr- en RDMA-compatibele virtuele machines uit de N-serie en QDR-tarieven voor A8- en A9-VM's. Deze RDMA-mogelijkheden kunnen de schaalbaarheid en prestaties van bepaalde MPI-toepassingen (Message Passing Interface) verbeteren. Zie voor meer informatie over de snelheid de details in de tabellen op deze pagina.

> [!NOTE]
> In Azure HPC zijn er twee klassen vm's, afhankelijk van of ze SR-IOV zijn ingeschakeld voor InfiniBand. Momenteel zijn de SR-IOV voor InfiniBand enabled VM's: HBv2, HB, HC en NCv3. De rest van de infiniBand ingeschakelde VM's zijn niet SR-IOV ingeschakeld.
> RDMA over IB wordt ondersteund voor alle RDMA-geschikte VM's.
> IP over IB wordt alleen ondersteund op de VMs met SR-IOV.IP over IB is only supported on the SR-IOV enabled VM's.

- **Besturingssysteem** - Linux wordt zeer goed ondersteund voor HPC VM's, distro's zoals CentOS, RHEL, Ubuntu, SUSE zijn gebruikelijk. Wat de ondersteuning van Windows betreft, wordt Windows Server 2016 ondersteund op alle VM's uit de HPC-serie. Windows Server 2012 R2, Windows Server 2012 worden ook ondersteund op de vm's die niet zijn ingeschakeld voor SR-IOV.

- **MPI** - De SR-IOV ingeschakeld VM maten op Azure (HBv2, HB, HC, NCv3) kunnen bijna elke smaak van MPI worden gebruikt met Mellanox OFED.
Op niet-SR-IOV-enabled VM's gebruiken ondersteunde MPI-implementaties de Microsoft Network Direct (ND)-interface om te communiceren tussen VM's. Daarom worden alleen Microsoft MPI (MS-MPI) 2012 R2 of hoger en Intel MPI 5.x-versies ondersteund. Latere versies (2017, 2018) van de Intel MPI-runtime-bibliotheek zijn mogelijk al dan niet compatibel met de Azure RDMA-stuurprogramma's.

- **InfiniBandDriver<Linux| Windows> VM-extensie** - Voeg op VM's met RDMA de InfiniBandDriver-<Linux toe| Windows> extensie om InfiniBand in te schakelen. Op Linux installeert de InfiniBandDriverLinux VM-extensie de Mellanox OFED-drivers (op SR-IOV VM's) voor RDMA-connectiviteit. Op Windows installeert de InfiniBandDriverWindows VM-extensie Windows Network Direct-stuurprogramma's (op niet-SR-IOV VM's) of Mellanox OFED-stuurprogramma's (op SR-IOV VM's) voor RDMA-connectiviteit.
In bepaalde implementaties van A8- en A9-exemplaren wordt de HpcVmDrivers-extensie automatisch toegevoegd. Houd er rekening mee dat de VM-extensie HpcVmDrivers wordt afgeschaft; het wordt niet bijgewerkt.
Als u de VM-extensie aan een VM wilt toevoegen, u [Azure PowerShell-cmdlets](/powershell/azure/overview) gebruiken. 

  Met de volgende opdracht wordt de nieuwste versie 1.0 InfiniBandDriverWindows-extensie geïnstalleerd op een bestaande VM met RDMA-geschikte VM met de naam *myVM* die is geïmplementeerd in de resourcegroep genaamd *myResourceGroup* in de *regio West-VS:*

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  Vm-extensies kunnen ook worden opgenomen in Azure Resource Manager-sjablonen voor eenvoudige implementatie, met het volgende JSON-element:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Met de volgende opdracht wordt de nieuwste versie 1.0 InfiniBandDriverWindows-extensie geïnstalleerd op alle VM's die geschikt zijn voor RDMA in een bestaande virtuele machineschaalset met de naam *myVMSS* geïmplementeerd in de brongroep *myResourceGroup:*

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Zie [Virtuele machine-extensies en -functies voor](./extensions/overview.md)meer informatie. U ook werken met extensies voor VM's die zijn geïmplementeerd in het [klassieke implementatiemodel.](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)

- **RDMA-netwerkadresruimte** - Het RDMA-netwerk in Azure reserveert de adresruimte 172.16.0.0/16. Als u MPI-toepassingen wilt uitvoeren op instanties die zijn geïmplementeerd in een virtueel Azure-netwerk, moet u ervoor zorgen dat de virtuele netwerkadresruimte het RDMA-netwerk niet overlapt.

## <a name="cluster-configuration-options"></a>Clusterconfiguratieopties

Azure biedt verschillende opties voor het maken van clusters van Windows HPC-VM's die kunnen communiceren via het RDMA-netwerk, waaronder: 

- **Virtuele machines** - Implementeer de HPC-vm's met RDMA in dezelfde schaalset of beschikbaarheidsset (wanneer u het implementatiemodel azure resource manager gebruikt). Als u het klassieke implementatiemodel gebruikt, implementeert u de VM's in dezelfde cloudservice.

- **Virtuele machineschaalsets** - Controleer in een vmss (virtual machine scale set) of u de implementatie beperkt tot één plaatsingsgroep. Stel bijvoorbeeld in een resourcemanagersjabloon `singlePlacementGroup` de `true`eigenschap in op . Houd er rekening mee dat de maximale `singlePlacementGroup` VMSS-grootte die kan worden opgebouwd met eigenschap is `true` beperkt tot 100 VM's standaard. Als de behoeften aan uw HPC-taakschaal hoger zijn dan 100 VM's in één VMSS-tenant, u een verhoging aanvragen en kosteloos [een online verzoek voor klantenondersteuning](../azure-supportability/how-to-create-azure-support-request.md) openen.

- **MPI onder virtuele machines** - Als RDMA (bijvoorbeeld met behulp van MPI-communicatie) tussen virtuele machines (VM's) vereist is, moet u ervoor zorgen dat de VM's zich in dezelfde virtuele machineschaalset of beschikbaarheidsset bevinden.

- **Azure CycleCloud** - Maak een HPC-cluster in [Azure CycleCloud](/azure/cyclecloud/) om MPI-taken uit te voeren.

- **Azure Batch** - Maak een [Azure Batch-groep](/azure/batch/) om MPI-workloads uit te voeren. Zie [Multi-instance taken gebruiken om MPI-toepassingen (Message Passing Interface) uit te voeren in Azure Batch](../batch/batch-mpi.md)als u computerintensieve exemplaren wilt gebruiken bij het uitvoeren van MPI-toepassingen met Azure Batch.

- **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) bevat een runtime-omgeving voor MS-MPI die het Azure RDMA-netwerk gebruikt wanneer het wordt geïmplementeerd op Linux-VM's die geschikt zijn voor RDMA. Zie Bijvoorbeeld implementaties [een Linux RDMA-cluster instellen met HPC Pack om MPI-toepassingen uit te voeren.](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

- **Azure-abonnement** : als u meer dan een paar rekenintensieve exemplaren wilt implementeren, u rekening houden met een abonnement op basis van betalen per gebruik of andere aankoopopties. Als u een [gratis account van Azure](https://azure.microsoft.com/free/) gebruikt, kunt u slechts een paar Azure Compute-resources van Azure gebruiken.

- **Prijzen en beschikbaarheid** - Deze VM-formaten worden alleen aangeboden in de standaardprijslaag. Controleer [producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/) op beschikbaarheid in Azure-regio's.

- **Kernenquotum** : mogelijk moet u het coresquotum in uw Azure-abonnement verhogen ten opzichte van de standaardwaarde. Uw abonnement kan ook het aantal cores beperken dat u implementeren in bepaalde VM-groottefamilies, waaronder de H-serie. Als u een quotumverhoging wilt aanvragen, opent u kosteloos [een online verzoek om klantenondersteuning.](../azure-supportability/how-to-create-azure-support-request.md) (Standaardlimieten kunnen variëren afhankelijk van uw abonnementscategorie.)

  > [!NOTE]
  > Neem contact op met Azure Support als u op grote schaal capaciteitsbehoeften hebt. Azure-quota zijn kredietlimieten, geen capaciteitsgaranties. Ongeacht uw quotum worden er alleen kosten in rekening gebracht voor kernen die u gebruikt.
  
- **Virtueel netwerk** : een [virtueel Azure-netwerk](https://azure.microsoft.com/documentation/services/virtual-network/) is niet vereist om de rekenintensieve exemplaren te gebruiken. Voor veel implementaties hebt u echter ten minste een virtueel Cloud-netwerk of een site-to-site-verbinding nodig als u toegang moet krijgen tot on-premises bronnen. Maak indien nodig een nieuw virtueel netwerk om de exemplaren te implementeren. Het toevoegen van computerintensieve VM's aan een virtueel netwerk in een affiniteitsgroep wordt niet ondersteund.

- **Grootte wijzigen** - Vanwege hun gespecialiseerde hardware u alleen het formaat van rekenintensieve exemplaren binnen dezelfde grootte-reeksen (H-serie of rekenintensieve A-reeksen) wijzigen. U bijvoorbeeld het formaat van een H-serie VM alleen wijzigen van de ene H-serie naar de andere. Bovendien wordt het wijzigen van het formaat van een niet-rekenintensieve grootte naar een rekenintensieve grootte niet ondersteund.  


## <a name="other-sizes"></a>Andere maten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het optimaliseren van uw HPC-toepassing voor Azure en enkele voorbeelden op [HPC-workloads] (https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/overview) 

- Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.
