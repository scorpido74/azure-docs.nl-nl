---
title: Azure VM-grootten-HPC | Microsoft Docs
description: Geeft een lijst van de verschillende beschik bare grootten voor High Performance Computing virtuele machines in Azure. Bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's en de opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
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
ms.openlocfilehash: dc2086223dea9bff311aac9e7d4771b5273f0e91
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493551"
---
# <a name="high-performance-compute-vm-sizes"></a>High Performance Compute-VM-grootten

Virtuele machines (Vm's) van de H-serie van Azure zijn ontworpen voor het leveren van prestaties van een leiderschaps klasse, MPI-schaal baarheid en rendabele efficiëntie voor diverse realistische HPC-workloads.

[HBv2-serie](hbv2-series.md) De functie Vm's 200 GB/sec Mellanox HDR InfiniBand, terwijl zowel de virtuele machines van de HB-als de HC-serie 100 GB/sec Mellanox EDR InfiniBand zijn. Elk van deze VM-typen is verbonden met een niet-blokkerende Fat-structuur voor geoptimaliseerde en consistente RDMA-prestaties. HBv2 Vm's ondersteunen adaptieve route ring en het dynamisch verbonden Trans Port (DCT, in aanvulling op standaard-RC-en UD-transporten). Deze functies verbeteren de prestaties, schaal baarheid en consistentie van toepassingen, en het gebruik ervan wordt ten zeerste aanbevolen.

[HB-serie](hb-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door geheugen bandbreedte, zoals een Hydraulic-dynamiek, een expliciete, beperkte element analyse en weer modellen. HB Vm's feature 60 AMD EPYC 7551-processor kernen, 4 GB RAM per CPU-kern en geen hyperthreading. Het AMD EPYC-platform biedt meer dan 260 GB/sec. geheugen bandbreedte.

[HC-serie](hc-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door een compacte reken kracht, zoals impliciete, geeindigd element analyse, moleculaire dynamiek en reken kundige schei kunde. HC Vm's feature 44 Intel Xeon Platinum 8168-processor kernen, 8 GB aan RAM per CPU-kern en geen hyperthreading. Het Intel Xeon Platinum-platform biedt ondersteuning voor een uitgebreid ecosysteem van Intel-software Programma's zoals de Intel math-kernelmodus.

[H-serie](h-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door hoge CPU-frequenties of grote geheugen per kern vereisten. Met de H-serie Vm's functie 8 of 16 Intel Xeon E5 2667 v3 processor cores, 7 of 14 GB RAM per CPU-kern en zonder hyperthreading. Functies van de H-serie 56 GB/sec Mellanox FDR InfiniBand in een niet-blokkerende Fat-structuur configuratie voor consistente RDMA-prestaties. Virtuele machines uit de H-serie ondersteunen Intel MPI 5. x en MS-MPI.

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

- **Azure-abonnement** : als u meer dan een aantal computerintensieve exemplaren wilt implementeren, kunt u een abonnement op basis van betalen naar gebruik of andere aankoop opties overwegen. Als u een [gratis account van Azure](https://azure.microsoft.com/free/) gebruikt, kunt u slechts een paar Azure Compute-resources van Azure gebruiken.

- **Prijzen en beschik baarheid** : deze VM-grootten worden alleen aangeboden in de prijs categorie Standard. Controleer of de beschik [bare producten per regio beschikbaar zijn](https://azure.microsoft.com/global-infrastructure/services/) in azure-regio's.
- **Quotum voor kernen** : u moet mogelijk het quotum voor kernen in uw Azure-abonnement verhogen van de standaard waarde. Uw abonnement kan ook het aantal kernen beperken dat u kunt implementeren in bepaalde VM-grootte families, inclusief de H-serie. Als u een quotum toename wilt aanvragen, opent u gratis [een aanvraag voor een online klant ondersteuning](../azure-supportability/how-to-create-azure-support-request.md) . (De standaard limieten kunnen variëren, afhankelijk van de categorie abonnement.)

  > [!NOTE]
  > Neem contact op met de ondersteuning van Azure als er grootschalige capaciteits behoeften zijn. Azure-quota zijn krediet limieten, geen capaciteits garanties. Ongeacht uw quotum worden er alleen kosten in rekening gebracht voor kernen die u gebruikt.
  
- **Virtueel netwerk** : een virtueel Azure- [netwerk](https://azure.microsoft.com/documentation/services/virtual-network/) is niet vereist voor het gebruik van de compute-intensieve exemplaren. Voor veel implementaties hebt u echter mini maal een Azure Virtual Network op basis van de Cloud of een site-naar-site-verbinding nodig als u toegang nodig hebt tot on-premises resources. Als dat nodig is, maakt u een nieuw virtueel netwerk om de exemplaren te implementeren. Het toevoegen van Compute-intensieve Vm's aan een virtueel netwerk in een affiniteits groep wordt niet ondersteund.
- **Grootte wijzigen** : vanwege hun gespecialiseerde hardware kunt u alleen Compute-intensieve instanties binnen dezelfde grootte familie (H-serie of COMPUTE-intensieve A-serie) wijzigen. U kunt bijvoorbeeld alleen het formaat van een VM van de H-serie wijzigen van de grootte van de h-serie in een andere. Bovendien wordt het wijzigen van de grootte van een niet-reken intensief formaat naar een berekenings intensief formaat niet ondersteund.  
## <a name="rdma-capable-instances"></a>Met RDMA compatibele exemplaren

Een subset van de computerintensieve instanties (A8, A9, H16r, H16mr, HB en HC) functie een netwerk interface voor RDMA-connectiviteit (Remote Direct Memory Access). De geselecteerde N-serie-grootten die zijn aangewezen met r, zoals de NC24rs-configuraties (NC24rs_v2 en NC24rs_v3), zijn ook geschikt voor RDMA. Deze interface is een aanvulling op de standaard Azure-netwerk interface die beschikbaar is voor andere VM-grootten.

Met deze interface kunnen de RDMA-compatibele instanties via een InfiniBand (IB)-netwerk communiceren, met behulp van EDR-tarieven voor HB, HC, FDR-tarieven voor virtuele machines van de N-serie van H16r, H16mr en RDMA, en zijn de prijzen voor A8 en A9 virtual machines. Deze RDMA-mogelijkheden kunnen de schaal baarheid en prestaties van bepaalde MPI-toepassingen (Message Passing Interface) verhogen. Zie de details in de tabellen op deze pagina voor meer informatie over de snelheid.

> [!NOTE]
> In azure wordt IP over IB alleen ondersteund op de SR-IOV ingeschakelde Vm's (SR-IOV voor InfiniBand, momenteel HB en HC). RDMA via IB wordt ondersteund voor alle exemplaren met RDMA-functionaliteit.
- **Besturings systeem** : Windows Server 2016 op alle bovenstaande vm's van de HPC-serie. Windows Server 2012 R2, Windows Server 2012 wordt ook ondersteund op Vm's waarvoor geen SR-IOV is ingeschakeld (dus met uitzonde ring van HB en HC).

- **Mpi** : met de VM-grootten van SR-IOV op Azure (HB, HC) kunt u bijna elke mpi gebruiken met Mellanox OFED.
Op Vm's waarvoor geen SR-IOV is ingeschakeld, gebruiken ondersteunde MPI-implementaties de micro soft Network direct (ND)-interface om te communiceren tussen instanties. Daarom worden alleen micro soft MPI (MS-MPI) 2012 R2 of hoger en Intel MPI 5. x-versies ondersteund. Latere versies (2017, 2018) van de Intel MPI runtime-bibliotheek kunnen al dan niet compatibel zijn met de Azure RDMA-Stuur Programma's.

- **INFINIBANDDRIVERWINDOWS VM extension** -op RDMA-compatibele vm's, voegt u de InfiniBandDriverWindows-extensie toe om InfiniBand in te scha kelen. Met deze Windows VM-extensie worden Windows-netwerk directe Stuur programma's (op niet-SR-IOV-Vm's) of Mellanox OFED-Stuur programma's (op SR-IOV-Vm's) geïnstalleerd voor RDMA-connectiviteit.
In bepaalde implementaties van A8-en A9-instanties wordt de uitbrei ding HpcVmDrivers automatisch toegevoegd. Houd er rekening mee dat de HpcVmDrivers VM-extensie wordt afgeschaft. het wordt niet bijgewerkt. U kunt [Azure PowerShell](/powershell/azure/overview) -cmdlets gebruiken om de VM-extensie toe te voegen aan een virtuele machine. 

  Met de volgende opdracht wordt de meest recente versie 1,0 InfiniBandDriverWindows-extensie geïnstalleerd op een bestaande virtuele RDMA-VM met de naam *myVM* die is geïmplementeerd in de resource groep met de naam *myResourceGroup* in de regio *VS-West* :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  VM-extensies kunnen ook worden opgenomen in Azure Resource Manager sjablonen voor een eenvoudige implementatie, met het volgende JSON-element:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Met de volgende opdracht wordt de meest recente versie 1,0 InfiniBandDriverWindows-extensie geïnstalleerd op alle virtuele machines met RDMA-functionaliteit in een bestaande VM-schaalset met de naam *myVMSS* geïmplementeerd in de resource groep met de naam *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Zie [extensies en functies van virtuele machines](/extensions/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie. U kunt ook werken met uitbrei dingen voor virtuele machines die zijn geïmplementeerd in het [klassieke implementatie model](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **RDMA-netwerk adres ruimte** : het RDMA-netwerk in azure behoudt de adres ruimte 172.16.0.0/16. Als u MPI-toepassingen wilt uitvoeren op instanties die zijn geïmplementeerd in een virtueel Azure-netwerk, moet u ervoor zorgen dat de adres ruimte van het virtuele netwerk het RDMA-netwerk niet overlapt.

## <a name="cluster-configuration-options"></a>Opties voor cluster configuratie

Azure biedt verschillende opties voor het maken van clusters van Windows HPC-Vm's die kunnen communiceren met behulp van het RDMA-netwerk, waaronder: 

- **Virtuele machines** : implementeer de met RDMA geschikte HPC-vm's in dezelfde beschikbaarheidsset (wanneer u het Azure Resource Manager-implementatie model gebruikt). Als u het klassieke implementatie model gebruikt, implementeert u de virtuele machines in dezelfde Cloud service. 

- **Virtuele-machine schaal sets** : in een schaalset voor virtuele machines moet u ervoor zorgen dat u de implementatie beperkt tot één plaatsings groep. Stel bijvoorbeeld in een resource manager-sjabloon de eigenschap `singlePlacementGroup` in op `true`. 

- **Mpi tussen virtuele machines** : als er een mpi-communicatie nodig is tussen virtuele machines (vm's), moet u ervoor zorgen dat de vm's zich in dezelfde beschikbaarheidsset of op dezelfde schaalset van de virtuele machine bevinden.

- **Azure CycleCloud** : Maak een HPC-cluster in [Azure CycleCloud](/azure/cyclecloud/) om mpi-taken uit te voeren op Windows-knoop punten.

- **Azure batch** : maak een [Azure batch](/azure/batch/) groep om mpi-workloads uit te voeren op Windows Server Compute-knoop punten. Zie voor meer informatie [gebruik van RDMA-compatibele of GPU-ingeschakelde instanties in batch-Pools](../batch/batch-pool-compute-intensive-sizes.md). Zie ook het [batch Shipyard](https://github.com/Azure/batch-shipyard) -project, voor het uitvoeren van op containers gebaseerde workloads op batch.

- **Micro soft HPC pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) bevat een runtime-omgeving voor MS-mpi die gebruikmaakt van het Azure RDMA-netwerk wanneer het is geïmplementeerd op RDMA-compatibele Linux-vm's. Zie voor beelden van implementaties [een Linux RDMA-cluster met HPC Pack instellen voor het uitvoeren van MPI-toepassingen](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

- Zie [een Linux RDMA-cluster met HPC Pack instellen](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)voor het uitvoeren van MPI-toepassingen voor controle lijsten voor het gebruik van de compute-intensieve instanties met HPC Pack op Windows Server.

- Als u computerintensieve instanties wilt gebruiken bij het uitvoeren van MPI-toepassingen met Azure Batch, raadpleegt u [taken met meerdere instanties gebruiken om mpi-toepassingen (Message Passing Interface) uit te voeren in azure batch](../batch/batch-mpi.md).

- Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.