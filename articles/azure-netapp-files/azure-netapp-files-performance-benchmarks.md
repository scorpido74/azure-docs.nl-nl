---
title: Prestatiebenchmarktestresultaten voor Azure NetApp-bestanden | Microsoft Documenten
description: Beschrijft de resultaten van prestatiebenchmarktests voor Azure NetApp-bestanden op volumeniveau.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68881743"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Resultaten van prestatiebenchmarks voor Azure NetApp Files

In dit artikel worden de resultaten van prestatiebenchmarktests voor Azure NetApp-bestanden op volumeniveau beschreven. 

## <a name="sample-application-used-for-the-tests"></a>Voorbeeldtoepassing die voor de tests wordt gebruikt

Prestatietests zijn uitgevoerd met een voorbeeldtoepassing met Azure NetApp-bestanden. De aanvraag heeft de volgende kenmerken: 

* Een Linux-gebaseerde applicatie gebouwd voor de cloud
* Kan lineair schalen met toegevoegde virtuele machines (VM's) om de rekenkracht te verhogen als dat nodig is
* Vereist een snelle toegankelijkheid van het datalake
* Heeft I/O patronen die soms willekeurig en soms sequentiële 
    * Een willekeurig patroon vereist een lage latentie voor grote hoeveelheden I/O. 
    * Een sequentiële patroon vereist grote hoeveelheden bandbreedte. 

## <a name="about-the-workload-generator"></a>Over de werkbelastinggenerator

De resultaten komen uit Vdbench samenvatting bestanden. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) is een command-line hulpprogramma dat schijf-I/O-workloads genereert voor het valideren van opslagprestaties. De gebruikte client-serverconfiguratie is schaalbaar.  Het omvat een enkele gemengde master / client en 14 dedicated client VM's.

## <a name="about-the-tests"></a>Over de tests

De tests zijn ontworpen om de limieten te identificeren die de steekproeftoepassing zou kunnen hebben en de reactietijd die tot de limieten toert.  

De volgende tests werden uitgevoerd: 

* 100% 8-KiB random read
* 100% 8-KiB random write
* 100% 64-KiB sequentiële lezen
* 100% 64-KiB sequentiële schrijven
* 50% 64-KiB sequentiële lezen, 50% 64-KiB sequentiële schrijven
* 50% 8-KiB random read, 50% 8-KiB random write

## <a name="bandwidth"></a>Bandbreedte

Azure NetApp Files biedt meerdere [serviceniveaus.](azure-netapp-files-service-levels.md) Elk serviceniveau biedt een andere hoeveelheid bandbreedte per TiB van de ingerichte capaciteit (volumequotum). De bandbreedtelimiet voor een volume wordt ingericht op basis van de combinatie van het serviceniveau en het volumequotum. De bandbreedtelimiet is slechts één factor bij het bepalen van de werkelijke hoeveelheid doorvoer die zal worden gerealiseerd.  

Momenteel is 4.500 MiB de hoogste doorvoer die is bereikt door een werkbelasting ten opzichte van één volume in het testen.  Met het Premium-serviceniveau biedt een volumequotum van 70,31 TiB voldoende bandbreedte om deze doorvoer te realiseren volgens de onderstaande berekening: 

![Bandbreedteformule](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Quotum en serviceniveau](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Doorvoerintensieve workloads

De doorvoertest gebruikte Vdbench en een combinatie van 12xD32s V3-opslagVM's. Het monstervolume in de test bereikte de volgende doorvoeraantallen:

![Doorvoertest](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>I/O-intensieve workloads

Bij de I/O-test werd Vdbench gebruikt en een combinatie van 12xD32s V3-opslagVM's. Het monstervolume in de test bereikte de volgende I/O-nummers:

![I/O-test](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latentie

De afstand tussen de test-VM's en het Azure NetApp-bestandenvolume heeft een impact op de I/O-prestaties.  In de onderstaande grafiek worden de IOPS versus latentieresponscurven voor twee verschillende sets VM's vergeleken.  Een set VM's is in de buurt van Azure NetApp-bestanden en de andere set is verder weg.  De verhoogde latentie voor de verdere set VM's heeft een impact op de hoeveelheid IOPS die op een bepaald niveau van parallellisme wordt bereikt.  Ongeacht, leest tegen een volume kan meer dan 300.000 IOPS zoals hieronder geïllustreerd: 

![Latentiestudie](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Samenvatting

Latentiegevoelige workloads (databases) kunnen een responstijd van één milliseconde hebben. De transactionele prestaties kunnen meer dan 300k IOPS zijn voor één volume.

Doorvoergevoelige toepassingen (voor streaming en imaging) kunnen een doorvoersnelheid van 4,5GiB/s hebben.

## <a name="example-scripts"></a>Voorbeeldscripts

De volgende voorbeeldmanuscripten zijn slechts voor demonstratiedoel.  Ze mogen niet worden gebruikt voor productiedoeleinden.  

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
