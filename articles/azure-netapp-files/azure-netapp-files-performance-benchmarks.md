---
title: Resultaten van de prestatie benchmark test voor Azure NetApp Files | Microsoft Docs
description: Beschrijft resultaten van benchmark tests voor de prestaties van Azure NetApp Files op volume niveau.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "68881743"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Resultaten van prestatiebenchmarks voor Azure NetApp Files

In dit artikel worden de resultaten beschreven van benchmark tests voor Azure NetApp Files op volume niveau. 

## <a name="sample-application-used-for-the-tests"></a>Voorbeeld toepassing die wordt gebruikt voor de tests

Prestatie testen zijn uitgevoerd met een voorbeeld toepassing die gebruikmaakt van Azure NetApp Files. De toepassing heeft de volgende kenmerken: 

* Een op Linux gebaseerde toepassing die is gebouwd voor de Cloud
* Kan lineair schalen met toegevoegde virtuele machines (Vm's) om de reken kracht zo nodig te verhogen
* Vereist een snelle toegankelijkheid van de data Lake
* Bevat I/O-patronen die soms wille keurig en soms opeenvolgend zijn 
    * Een wille keurig patroon vereist een lage latentie voor grote hoeveel heden I/O. 
    * Voor een sequentieel patroon is een grote hoeveelheid band breedte vereist. 

## <a name="about-the-workload-generator"></a>Over de workload generator

De resultaten zijn afkomstig van Vdbench-samenvattings bestanden. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) is een opdracht regel programma waarmee schijf-I/O-workloads worden gegenereerd voor het valideren van de opslag prestaties. De gebruikte client-server configuratie is schaalbaar.  Het bevat één gemengde Master/client en 14 gereserveerde client-Vm's.

## <a name="about-the-tests"></a>Over de tests

De tests zijn ontworpen om de limieten te identificeren die de voorbeeld toepassing kan hebben en de reactie tijd die tot de limieten buigt.  

De volgende tests zijn uitgevoerd: 

* 100% 8-KiB wille keurige Lees bewerking
* 100% 8-KiB wille keurige schrijf bewerking
* 100% 64-KiB sequentiële Lees bewerking
* 100% 64-KiB sequentiële Schrijf bewerking
* 50% 64-KiB sequentiële Lees, 50% 64-KiB sequentiële Schrijf bewerking
* 50% 8-KiB wille keurig lezen, 50% 8-KiB wille keurige schrijf bewerking

## <a name="bandwidth"></a>Bandbreedte

Azure NetApp Files biedt meerdere [service niveaus](azure-netapp-files-service-levels.md). Elk service niveau biedt een verschillende hoeveelheid band breedte per TiB van ingerichte capaciteit (volume quota). De bandbreedte limiet voor een volume wordt ingericht op basis van de combi natie van het service niveau en het volume quotum. De bandbreedte limiet is slechts één factor bij het bepalen van de werkelijke hoeveelheid door Voer die wordt gerealiseerd.  

Momenteel is 4.500 MiB de hoogste door Voer die is behaald door een workload ten opzichte van één volume dat wordt getest.  Met het Premium-service niveau kan een volume quotum van 70,31 TiB voldoende band breedte inrichten om deze door voer te realiseren volgens de onderstaande berekening: 

![Bandbreedte formule](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Quotum en service niveau](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Door Voer intensieve workloads

De door Voer gebruikte Vdbench en een combi natie van 12xD32s v3 opslag-Vm's. Het voorbeeld volume in de test heeft de volgende doorvoer nummers bereikt:

![Doorvoer test](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>I/O-intensieve workloads

De I/O-test gebruikt Vdbench en een combi natie van 12xD32s v3-opslag-Vm's. Het voor beeld van het volume in de test heeft de volgende I/O-nummers bereikt:

![I/O-test](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latentie

De afstand tussen de virtuele machines van de test en het Azure NetApp Files volume heeft invloed op de I/O-prestaties.  De onderstaande grafiek vergelijkt de IOPS versus latentie respons curven voor twee verschillende sets Vm's.  Een set virtuele machines is bijna Azure NetApp Files en de andere set is verder weg.  De verhoogde latentie voor de nieuwe set Vm's heeft invloed op de hoeveelheid IOPS die wordt behaald op een bepaald niveau van parallelle uitvoering.  Lees-en schrijf bewerkingen voor een volume kunnen de 300.000 IOPS overschrijden, zoals hieronder wordt geïllustreerd: 

![Latentie studie](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Samenvatting

Latentie gevoelige werk belastingen (data bases) kunnen een reactie tijd van één milliseconde hebben. De transactionele prestaties kunnen meer dan een 300-IOPS voor één volume zijn.

Door Voer gevoelige toepassingen (voor streaming en imaging) kunnen de door Voer van 4,5 GiB/s hebben.

## <a name="example-scripts"></a>Voorbeeld scripts

De volgende voorbeeld scripts zijn alleen bedoeld voor demonstratie doeleinden.  Ze mogen niet worden gebruikt voor productie doeleinden.  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
