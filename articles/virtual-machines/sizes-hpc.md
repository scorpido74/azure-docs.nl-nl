---
title: Azure VM-grootten-HPC | Microsoft Docs
description: Geeft een lijst van de verschillende beschik bare grootten voor High Performance Computing virtuele machines in Azure. Bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's en de opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 961e5a0febc0212b8a747b052b3fd6f696689351
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678627"
---
# <a name="high-performance-computing-vm-sizes"></a>High Performance Computing VM-grootten

Virtuele machines (Vm's) van de H-serie van Azure zijn ontworpen voor het leveren van prestaties van een leiderschaps klasse, MPI-schaal baarheid en rendabele efficiëntie voor diverse realistische HPC-workloads.

[HBv2-serie](hbv2-series.md) De functie Vm's 200 GB/sec Mellanox HDR InfiniBand, terwijl zowel de virtuele machines van de HB-als de HC-serie 100 GB/sec Mellanox EDR InfiniBand zijn. Elk van deze VM-typen is verbonden met een niet-blokkerende Fat-structuur voor geoptimaliseerde en consistente RDMA-prestaties. HBv2 Vm's ondersteunen adaptieve route ring en het dynamisch verbonden Trans Port (DCT, in aanvulling op standaard-RC-en UD-transporten). Deze functies verbeteren de prestaties, schaal baarheid en consistentie van toepassingen, en het gebruik ervan wordt ten zeerste aanbevolen.

[HB-serie](hb-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door geheugen bandbreedte, zoals een Hydraulic-dynamiek, een expliciete, beperkte element analyse en weer modellen. HB Vm's feature 60 AMD EPYC 7551-processor kernen, 4 GB RAM per CPU-kern en geen hyperthreading. Het AMD EPYC-platform biedt meer dan 260 GB/sec. geheugen bandbreedte.

[HC-serie](hc-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door een compacte reken kracht, zoals impliciete, geeindigd element analyse, moleculaire dynamiek en reken kundige schei kunde. HC Vm's feature 44 Intel Xeon Platinum 8168-processor kernen, 8 GB aan RAM per CPU-kern en geen hyperthreading. Het Intel Xeon Platinum-platform biedt ondersteuning voor een uitgebreid ecosysteem van Intel-software Programma's zoals de Intel math-kernelmodus.

[H-serie](h-series.md) Vm's zijn geoptimaliseerd voor toepassingen die worden aangedreven door hoge CPU-frequenties of grote geheugen per kern vereisten. Met de H-serie Vm's functie 8 of 16 Intel Xeon E5 2667 v3 processor cores, 7 of 14 GB RAM per CPU-kern en zonder hyperthreading. Functies van de H-serie 56 GB/sec Mellanox FDR InfiniBand in een niet-blokkerende Fat-structuur configuratie voor consistente RDMA-prestaties. Virtuele machines uit de H-serie ondersteunen Intel MPI 5. x en MS-MPI.

> [!NOTE]
> De A8-A11-Vm's worden gepland voor buiten gebruiks telling op 3/2021. Zie voor meer informatie [HPC-migratie handleiding](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Met RDMA compatibele exemplaren

De meeste HPC VM-grootten (HBv2, HB, HC, H16r, H16mr, A8 en A9) beschikken over een netwerk interface voor RDMA-verbindingen (Remote Direct Memory Access). De geselecteerde [N-serie-](https://docs.microsoft.com/azure/virtual-machines/nc-series) grootten die zijn aangewezen met r, zoals de NC24rs-configuraties (NC24rs_v3, NC24rs_v2 en NC24r), zijn ook geschikt voor RDMA. Deze interface is een aanvulling op de standaard Azure-netwerk interface die beschikbaar is in de andere VM-grootten.

Met deze interface kunnen de RDMA-compatibele instanties communiceren via een InfiniBand (IB)-netwerk, op basis van de HDR-tarieven voor HBv2, EDR-tarieven voor HB, HC, FDR-tarieven voor H16r, H16mr en een RDMA-functionaliteit voor A8-en A9-Vm's. Deze RDMA-mogelijkheden kunnen de schaal baarheid en prestaties van bepaalde MPI-toepassingen (Message Passing Interface) verhogen. Zie de details in de tabellen op deze pagina voor meer informatie over de snelheid.

> [!NOTE]
> In azure HPC zijn er twee klassen Vm's, afhankelijk van het feit of SR-IOV is ingeschakeld voor InfiniBand. Momenteel zijn de SR-IOV voor InfiniBand ingeschakelde Vm's: HBv2, HB, HC en NCv3. Voor de rest van de InfiniBand ingeschakelde Vm's is SR-IOV niet ingeschakeld.
> RDMA via IB wordt ondersteund voor alle virtuele machines met RDMA-functionaliteit.
> IP over IB wordt alleen ondersteund op Vm's met SR-IOV-functionaliteit.

- **Besturings systeem** : Linux wordt zeer goed ondersteund voor HPC-vm's, distributies zoals CENTOS, RHEL, Ubuntu, SuSE. Windows Server 2016 wordt ondersteund op alle virtuele machines van de HPC-serie met betrekking tot Windows-ondersteuning. Windows Server 2012 R2, Windows Server 2012 wordt ook ondersteund op Vm's waarvoor geen SR-IOV is ingeschakeld.

- **Mpi** : met de VM-grootten van SR-IOV op Azure (HBV2, HB, HC, NCv3) kunt u bijna alle mpi gebruiken met Mellanox OFED.
Op Vm's waarvoor geen SR-IOV is ingeschakeld, wordt in ondersteunde MPI-implementaties de micro soft Network direct (ND)-interface gebruikt voor communicatie tussen Vm's. Daarom worden alleen micro soft MPI (MS-MPI) 2012 R2 of hoger en Intel MPI 5. x-versies ondersteund. Latere versies (2017, 2018) van de Intel MPI runtime-bibliotheek kunnen al dan niet compatibel zijn met de Azure RDMA-Stuur Programma's.

- **InfiniBandDriver<Linux | Windows> VM-extensie** -op RDMA-compatibele vm's, voeg de InfiniBandDriver<Linux toe | Extensie voor Windows-> om InfiniBand in te scha kelen. Op Linux installeert de InfiniBandDriverLinux-VM-extensie de Mellanox OFED-Stuur programma's (op SR-IOV-Vm's) voor RDMA-connectiviteit. In Windows installeert de InfiniBandDriverWindows-VM-extensie Windows-netwerk directe Stuur programma's (op niet-SR-IOV-Vm's) of Mellanox OFED-Stuur programma's (op SR-IOV-Vm's) voor RDMA-connectiviteit.
In bepaalde implementaties van A8-en A9-instanties wordt de uitbrei ding HpcVmDrivers automatisch toegevoegd. Houd er rekening mee dat de HpcVmDrivers VM-extensie wordt afgeschaft. het wordt niet bijgewerkt.
U kunt [Azure PowerShell](/powershell/azure/overview) -cmdlets gebruiken om de VM-extensie toe te voegen aan een virtuele machine. 

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

  Zie [extensies en functies van virtuele machines](./extensions/overview.md)voor meer informatie. U kunt ook werken met uitbrei dingen voor virtuele machines die zijn geïmplementeerd in het [klassieke implementatie model](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **RDMA-netwerk adres ruimte** : het RDMA-netwerk in azure behoudt de adres ruimte 172.16.0.0/16. Als u MPI-toepassingen wilt uitvoeren op instanties die zijn geïmplementeerd in een virtueel Azure-netwerk, moet u ervoor zorgen dat de adres ruimte van het virtuele netwerk het RDMA-netwerk niet overlapt.

## <a name="cluster-configuration-options"></a>Opties voor cluster configuratie

Azure biedt verschillende opties voor het maken van clusters van Windows HPC-Vm's die kunnen communiceren met behulp van het RDMA-netwerk, waaronder: 

- **Virtuele machines** : implementeer de met RDMA compatibele HPC-vm's in dezelfde schaalset of beschikbaarheidsset (wanneer u het Azure Resource Manager-implementatie model gebruikt). Als u het klassieke implementatie model gebruikt, implementeert u de virtuele machines in dezelfde Cloud service.

- **Virtuele-machine schaal sets** : in een VMSS (virtuele-machine schaalset) moet u ervoor zorgen dat u de implementatie beperkt tot één plaatsings groep voor InfiniBand-communicatie in de VMSS. Stel bijvoorbeeld in een resource manager-sjabloon de eigenschap in `singlePlacementGroup` op `true` . Houd er rekening mee dat de maximale VMSS-grootte die met de eigenschap kan worden ingesteld `singlePlacementGroup` , `true` Standaard wordt beperkt bij 100 vm's. Als uw HPC-taken schaal behoeften hoger zijn dan 100 Vm's in één VMSS-Tenant, kunt u een verhoging aanvragen, [een online klant ondersteunings aanvraag openen](../azure-supportability/how-to-create-azure-support-request.md) . De limiet voor het aantal virtuele machines in één VMSS kan worden verhoogd tot 300. Houd er rekening mee dat bij het implementeren van Vm's met beschikbaarheids sets de maximum limiet is 200 Vm's per Beschikbaarheidsset.

- **Mpi tussen virtuele machines** : als RDMA (bijvoorbeeld met MPI-communicatie) is vereist tussen virtuele machines (vm's), moet u ervoor zorgen dat de vm's zich in dezelfde virtuele-machine schaalset of beschik bare set bevinden.

- **Azure CycleCloud** : Maak een HPC-cluster in [Azure CycleCloud](/azure/cyclecloud/) om mpi-taken uit te voeren.

- **Azure batch** : maak een [Azure batch](/azure/batch/) groep voor het uitvoeren van MPI-workloads. Als u computerintensieve instanties wilt gebruiken bij het uitvoeren van MPI-toepassingen met Azure Batch, raadpleegt u [taken met meerdere instanties gebruiken om mpi-toepassingen (Message Passing Interface) uit te voeren in azure batch](../batch/batch-mpi.md).

- **Micro soft HPC Pack**  -  [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) bevat een runtime-omgeving voor MS-mpi die gebruikmaakt van het Azure RDMA-netwerk wanneer het is GEÏMPLEMENTEERD op RDMA-compatibele Linux-vm's. Zie voor beelden van implementaties [een Linux RDMA-cluster met HPC Pack instellen voor het uitvoeren van MPI-toepassingen](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

- **Azure-abonnement** : als u meer dan een aantal computerintensieve exemplaren wilt implementeren, kunt u een abonnement op basis van betalen naar gebruik of andere aankoop opties overwegen. Als u een [gratis account van Azure](https://azure.microsoft.com/free/) gebruikt, kunt u slechts een paar Azure Compute-resources van Azure gebruiken.

- **Prijzen en beschik baarheid** : deze VM-grootten worden alleen aangeboden in de prijs categorie Standard. Controleer of de beschik [bare producten per regio beschikbaar zijn](https://azure.microsoft.com/global-infrastructure/services/) in azure-regio's.

- **Quotum voor kernen** : u moet mogelijk het quotum voor kernen in uw Azure-abonnement verhogen van de standaard waarde. Uw abonnement kan ook het aantal kernen beperken dat u kunt implementeren in bepaalde VM-grootte families, inclusief de H-serie. Als u een quotum toename wilt aanvragen, opent u gratis [een aanvraag voor een online klant ondersteuning](../azure-supportability/how-to-create-azure-support-request.md) . (De standaard limieten kunnen variëren, afhankelijk van de categorie abonnement.)

  > [!NOTE]
  > Neem contact op met de ondersteuning van Azure als er grootschalige capaciteits behoeften zijn. Azure-quota zijn krediet limieten, geen capaciteits garanties. Ongeacht uw quotum worden er alleen kosten in rekening gebracht voor kernen die u gebruikt.
  
- **Virtueel netwerk** : een virtueel Azure- [netwerk](https://azure.microsoft.com/documentation/services/virtual-network/) is niet vereist voor het gebruik van de compute-intensieve exemplaren. Voor veel implementaties hebt u echter mini maal een Azure Virtual Network op basis van de Cloud of een site-naar-site-verbinding nodig als u toegang nodig hebt tot on-premises resources. Als dat nodig is, maakt u een nieuw virtueel netwerk om de exemplaren te implementeren. Het toevoegen van Compute-intensieve Vm's aan een virtueel netwerk in een affiniteits groep wordt niet ondersteund.

- **Grootte wijzigen** : vanwege hun gespecialiseerde hardware kunt u alleen Compute-intensieve instanties binnen dezelfde grootte familie (H-serie of COMPUTE-intensieve A-serie) wijzigen. U kunt bijvoorbeeld alleen het formaat van een VM van de H-serie wijzigen van de grootte van de h-serie in een andere. Bovendien wordt het wijzigen van de grootte van een niet-reken intensief formaat naar een berekenings intensief formaat niet ondersteund.  


## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het optimaliseren van uw HPC-toepassing voor Azure en enkele voor beelden in [HPC-workloads](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/overview) 

- Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
