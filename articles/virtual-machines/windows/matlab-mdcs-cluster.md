---
title: MATLAB-clusters op virtuele machines | Microsoft Docs
description: Microsoft Azure virtuele machines gebruiken om MATLAB gedistribueerde Computing Server clusters te maken voor het uitvoeren van uw reken intensief parallelle MATLAB-workloads
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: gwallace
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: d57d9bfa964759e639c2cf40d86bd603b1900ce7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103014"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>MATLAB-clusters voor gedistribueerde computing maken op virtuele Azure-machines
Gebruik Microsoft Azure virtuele machines om een of meer MATLAB gedistribueerde Computing Server clusters te maken voor het uitvoeren van uw reken intensief parallelle MATLAB-workloads. Installeer uw MATLAB-software voor gedistribueerde Computing Server op een virtuele machine om te gebruiken als basis installatie kopie en gebruik een Azure Quick Start-sjabloon of Azure PowerShell script (beschikbaar op [github](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) om het cluster te implementeren en te beheren. Maak na de implementatie verbinding met het cluster om uw workloads uit te voeren.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Over MATLAB en MATLAB gedistribueerde Computing Server
Het [MATLAB](https://www.mathworks.com/products/matlab/) -platform is geoptimaliseerd voor het oplossen van technische en weten schappelijke problemen. MATLAB-gebruikers met grootschalige simulaties en gegevens verwerkings taken kunnen gebruikmaken van MathWorks parallel computing-producten om hun computerintensieve workloads te versnellen door gebruik te maken van compute clusters en Grid-Services. Met de werk [balk voor parallelle computing](https://www.mathworks.com/products/parallel-computing/) kunnen MATLAB-gebruikers parallelliseren toepassingen gebruiken en profiteren van multicore-processors, gpu's en reken clusters. Met de functie voor gedistribueerde [Computing van MATLAB](https://www.mathworks.com/products/distriben/) kunnen MATLAB-gebruikers gebruikmaken van veel computers in een berekenings cluster.

Met behulp van Azure virtual machines kunt u clusters voor gedistribueerde computing servers maken met dezelfde mechanismen die beschikbaar zijn voor het verzenden van parallelle werken als on-premises clusters, zoals interactieve taken, batch taken, onafhankelijke taken en communicatie stappen. Het gebruik van Azure in combi natie met het MATLAB-platform heeft veel voor delen ten opzichte van het inrichten en gebruiken van traditionele on-premises hardware: een reeks groottes van virtuele machines, het maken van clusters op aanvraag, zodat u alleen betaalt voor de reken bronnen die u gebruikt, en de de mogelijkheid om modellen op schaal te testen.  

## <a name="prerequisites"></a>Vereisten
* **Client computer** : u hebt een Windows-client computer nodig om te communiceren met Azure en het MATLAB gedistribueerde Computing Server-cluster na de implementatie.
* **Azure PowerShell** -Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) om het op de client computer te installeren.
* **Azure-abonnement** : als u nog geen abonnement hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/free/) maken. Voor grotere clusters kunt u een abonnement op basis van betalen naar gebruik of andere aankoop opties overwegen.
* **vcpu's-quotum** : u moet mogelijk het vCPU quotum verhogen voor het implementeren van een groot cluster of meer dan één MATLAB gedistribueerd Computing Server-cluster. Als u een quotum wilt verhogen, opent u gratis [een aanvraag voor een online klant ondersteuning](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) .
* **MATLAB-, parallelle reken-en MATLAB-server licenties** voor gedistribueerde Computing: de scripts gaan ervan uit dat de gehoste [licentie Manager MathWorks](https://www.mathworks.com/help/install/license-management.html) wordt gebruikt voor alle licenties.  
* **MATLAB gedistribueerde Computing Server-software** : wordt geïnstalleerd op een virtuele machine die wordt gebruikt als de basis-VM-installatie kopie voor de cluster-vm's.

## <a name="high-level-steps"></a>Stappen op hoog niveau
De volgende stappen op hoog niveau zijn vereist om virtuele Azure-machines te gebruiken voor uw MATLAB-clusters voor gedistribueerde computing. Gedetailleerde instructies vindt u in de documentatie bij de Quick Start-sjabloon en scripts op [github](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Een basis installatie kopie van een virtuele machine maken**  

   * Down load en installeer MATLAB gedistribueerde Computing Server-software op deze VM.

     > [!NOTE]
     > Dit proces kan een paar uur duren, maar u hoeft dit slechts één keer te doen voor elke versie van MATLAB die u gebruikt.   
     >
     >
2. **Een of meer clusters maken**  

   * Gebruik het meegeleverde Power shell-script of gebruik de Quick Start-sjabloon om een cluster te maken op basis van de installatie kopie van de virtuele machine.   
   * Beheer de clusters met behulp van het opgegeven Power shell-script, waarmee u clusters kunt weer geven, onderbreken, hervatten en verwijderen.

## <a name="cluster-configurations"></a>Cluster configuraties
Op dit moment kunt u met het script en de sjabloon voor het maken van het cluster een topologie met één gedistribueerde computing-server maken. Als u wilt, maakt u een of meer extra clusters, waarbij elk cluster een verschillend aantal werk-Vm's heeft, met behulp van verschillende VM-grootten, enzovoort.

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB-client en-cluster in azure
De knoop punten van het MATLAB-client knooppunt, het MATLAB-taak planner en de MATLAB gedistribueerde Computing Server worden allemaal geconfigureerd als Azure-Vm's in een virtueel netwerk, zoals wordt weer gegeven in de volgende afbeelding.


* Als u het cluster wilt gebruiken, maakt u verbinding via Extern bureaublad met het client knooppunt. Het client knooppunt voert de MATLAB-client uit.
* Het client knooppunt heeft een bestands share die toegankelijk is voor alle werk nemers.
* MathWorks gehoste licentie Manager wordt gebruikt voor de licentie controles voor alle MATLAB-software.
* Standaard wordt één MATLAB gedistribueerde Computing Server worker per vCPU gemaakt op de virtuele machines van de werk nemer, maar u kunt een wille keurig aantal opgeven.

## <a name="use-an-azure-based-cluster"></a>Een cluster op basis van Azure gebruiken
Net als bij andere typen clusters voor gedistribueerde Computing Server moet u het cluster Profiel beheer in de MATLAB-client (op de client-VM) gebruiken om een MATLAB-taak planner-cluster profiel te maken.

![Cluster Profiel beheer](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Volgende stappen
* Voor gedetailleerde instructies voor het implementeren en beheren van MATLAB gedistribueerde Computing Server clusters in azure, zie de [github](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) -opslag plaats die de sjablonen en scripts bevat.
* Ga naar de [MathWorks-site](https://www.mathworks.com/) voor gedetailleerde documentatie voor de gedistribueerde Computing-Server van MATLAB en MATLAB.
