---
title: MATLAB clusters op virtuele machines
description: Gebruik virtuele machines van Microsoft Azure om MATLAB Distributed Computing Server-clusters te maken om uw computerintensieve parallelle MATLAB-workloads uit te voeren
author: mscurrell
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: d34481587fd48e2eddfd268c39f6bc4f7c4e0c76
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869421"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>MATLAB Distributed Computing Server-clusters maken op Azure VM's
Gebruik virtuele machines van Microsoft Azure om een of meer MATLAB Distributed Computing Server-clusters te maken om uw computerintensieve parallelle MATLAB-workloads uit te voeren. Installeer uw MATLAB Distributed Computing Server-software op een VM om als basisafbeelding te gebruiken en gebruik een Azure quickstart-sjabloon of Azure PowerShell-script (beschikbaar op [GitHub)](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)om het cluster te implementeren en te beheren. Na implementatie maakt u verbinding met het cluster om uw workloads uit te voeren.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Over MATLAB en MATLAB Distributed Computing Server
Het [MATLAB](https://www.mathworks.com/products/matlab/) platform is geoptimaliseerd voor het oplossen van technische en wetenschappelijke problemen. MATLAB-gebruikers met grootschalige simulaties en gegevensverwerkingstaken kunnen parallelcomputerproducten van MathWorks gebruiken om hun rekenintensieve workloads te versnellen door gebruik te maken van compute clusters en grid services. [Met de Parallel Computing Toolbox](https://www.mathworks.com/products/parallel-computing/) kunnen MATLAB-gebruikers toepassingen parallellopen en profiteren van multicore-processors, GPU's en compute-clusters. [MATLAB Distributed Computing Server](https://www.mathworks.com/products/distriben/) stelt MATLAB-gebruikers in staat om veel computers in een compute cluster te gebruiken.

Door virtuele Azure-machines te gebruiken, u MATLAB Distributed Computing Server-clusters maken die allemaal dezelfde mechanismen beschikbaar hebben om parallel werk in te dienen als on-premises clusters, zoals interactieve taken, batchtaken, onafhankelijke taken en communicerende taken. Het gebruik van Azure in combinatie met het MATLAB-platform heeft veel voordelen in vergelijking met het inrichten en gebruiken van traditionele on-premises hardware: een reeks virtuele machineformaten, het on-demand maken van clusters, zodat u alleen betaalt voor de rekenbronnen die u gebruikt en de mogelijkheid om modellen op schaal te testen.  

## <a name="prerequisites"></a>Vereisten
* **Clientcomputer** - U hebt een Windows-clientcomputer nodig om na implementatie te communiceren met Azure en het MATLAB Distributed Computing Server-cluster.
* **Azure PowerShell** - Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) om het op uw clientcomputer te installeren.
* **Azure-abonnement** - Als u geen abonnement hebt, u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/free/) maken. Voor grotere clusters u een abonnement per gebruik of andere aankoopopties overwegen.
* **vCPU-quotum** - Mogelijk moet u het vCPU-quotum verhogen om een groot cluster of meer dan één MATLAB Distributed Computing Server-cluster te implementeren. Als u een quotum wilt verhogen, opent u kosteloos [een online verzoek om klantenondersteuning.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* **MATLAB, Parallel Computing Toolbox en MATLAB Distributed Computing Server-licenties** - De scripts gaan ervan uit dat de [MathWorks Hosted License Manager](https://www.mathworks.com/help/install/license-management.html) wordt gebruikt voor alle licenties.  
* **MATLAB Distributed Computing Server-software** - Wordt geïnstalleerd op een VM die wordt gebruikt als de basis-VM-afbeelding voor de cluster-VM's.

## <a name="high-level-steps"></a>Stappen op hoog niveau
Als u virtuele Azure-machines wilt gebruiken voor uw MATLAB Distributed Computing Server-clusters, zijn de volgende stappen op hoog niveau vereist. Gedetailleerde instructies staan in de documentatie bij de quickstartsjabloon en scripts op [GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)

1. **Een basis-VM-afbeelding maken**  

   * Download en installeer MATLAB Distributed Computing Server software op deze VM.

     > [!NOTE]
     > Dit proces kan een paar uur duren, maar je hoeft het maar één keer te doen voor elke versie van MATLAB die je gebruikt.   
     >
     >
2. **Een of meer clusters maken**  

   * Gebruik het meegeleverde PowerShell-script of gebruik de snelstartsjabloon om een cluster te maken op basis-VM-afbeelding.   
   * Beheer de clusters met behulp van het meegeleverde PowerShell-script waarmee u clusters aanbieden, onderbreken, hervatten en verwijderen.

## <a name="cluster-configurations"></a>Clusterconfiguraties
Met het script en de sjabloon voor het maken van het clustermaken u momenteel één MATLAB Distributed Computing Server-topologie maken. Maak desgevraagd een of meer extra clusters, waarbij elk cluster een verschillend aantal werknemers-VM's heeft, verschillende VM-formaten gebruikt, enzovoort.

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB-client en -cluster in Azure
Het matlab-clientknooppunt, het knooppunt matlab-taakplanner en de "worker"-knooppunten van MATLAB Distributed Computing Server zijn allemaal geconfigureerd als Azure VM's in een virtueel netwerk, zoals in de volgende afbeelding wordt weergegeven.


* Als u het cluster wilt gebruiken, maakt u verbinding via Extern bureaublad met het clientknooppunt. Het clientknooppunt beheert de MATLAB-client.
* Het clientknooppunt heeft een bestandsshare die toegankelijk is voor alle werknemers.
* MathWorks Hosted License Manager wordt gebruikt voor de licentiecontroles voor alle MATLAB-software.
* Standaard wordt er één MATLAB Distributed Computing Server-worker per vCPU gemaakt op de VM's van de werknemer, maar u elk nummer opgeven.

## <a name="use-an-azure-based-cluster"></a>Een Azure-gebaseerd cluster gebruiken
Net als bij andere typen MATLAB Distributed Computing Server-clusters moet u de clusterprofielbeheer in de MATLAB-client (op de client-VM) gebruiken om een MATLAB Job Scheduler-clusterprofiel te maken.

![Clusterprofielbeheerder](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Volgende stappen
* Zie de [GitHub-repository](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) met de sjablonen en scripts voor gedetailleerde instructies voor het implementeren en beheren van MATLAB Distributed Computing Server-clusters in Azure.
* Ga naar de [MathWorks-site](https://www.mathworks.com/) voor gedetailleerde documentatie voor MATLAB en MATLAB Distributed Computing Server.
