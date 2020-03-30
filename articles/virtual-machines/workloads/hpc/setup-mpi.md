---
title: Interface voor het doorgeven van berichten instellen voor HPC - Azure Virtual Machines | Microsoft Documenten
description: Meer informatie over het instellen van MPI voor HPC op Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023987"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Interface voor het doorgeven van berichten instellen voor HPC

Mpi-workloads (Message Passing Interface) zijn een belangrijk onderdeel van traditionele HPC-workloads. Met de Vm-formaten voor SR-IOV op Azure kan bijna elke smaak van MPI worden gebruikt. 

Voor het uitvoeren van MPI-taken op VM's moet u partitiesleutels (p-sleutels) instellen voor een tenant. Volg de stappen in de sectie [Partitiesleutels ontdekken](#discover-partition-keys) voor meer informatie over het bepalen van de p-toetswaarden.

## <a name="ucx"></a>UCX (UCX)

[UCX](https://github.com/openucx/ucx) biedt de beste prestaties op IB en werkt met MPICH en OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>Openmpi

Installeer UCX zoals eerder beschreven.

```bash
sudo yum install –y openmpi
```

OpenMPI bouwen.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

OpenMPI uitvoeren.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Controleer uw partitiesleutel zoals hierboven vermeld.

## <a name="mpich"></a>Mpich

Installeer UCX zoals eerder beschreven.

Bouw MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

Het runnen van MPICH.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Controleer uw partitiesleutel zoals hierboven vermeld.

## <a name="mvapich2"></a>MVAPICH2

Bouw MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Met MVAPICH2.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platform MPI Community Edition

Installeer vereiste pakketten voor Platform MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Volg het installatieproces.

## <a name="intel-mpi"></a>Intel MPI

[Intel MPI downloaden](https://software.intel.com/mpi-library/choose-download).

Wijzig de I_MPI_FABRICS omgevingsvariabele, afhankelijk van de versie. Voor Intel MPI 2018, gebruik `I_MPI_FABRICS=shm:ofa` en voor `I_MPI_FABRICS=shm:ofi`2019, gebruik .

Procesvast maken werkt standaard correct voor 15, 30 en 60 PPN.

## <a name="osu-mpi-benchmarks"></a>OSU MPI-benchmarks

[Download OSU MPI Benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) en untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Benchmarks maken met een bepaalde MPI-bibliotheek:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI Benchmarks `mpi/` staan onder map.


## <a name="discover-partition-keys"></a>Partitiesleutels ontdekken

Ontdek partitiesleutels (p-sleutels) voor het communiceren met andere VM's binnen dezelfde tenant (Beschikbaarheidsset of VM-schaalset).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

De grootste van de twee is de tenant sleutel die moet worden gebruikt met MPI. Voorbeeld: Als de volgende de p-toetsen zijn, moet 0x800b worden gebruikt met MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Gebruik de partitie anders dan de standaardpartitiesleutel (0x7fff). UCX vereist dat de MSB van p-key moet worden gewist. Stel bijvoorbeeld UCX_IB_PKEY in als 0x000b voor 0x800b.

Houd er ook rekening mee dat zolang de tenant (AVSet of VMSS) bestaat, de PKEYs hetzelfde blijven. Dit geldt zelfs wanneer knooppunten worden toegevoegd/verwijderd. Nieuwe huurders krijgen verschillende PKEY's.


## <a name="set-up-user-limits-for-mpi"></a>Gebruikerslimieten instellen voor MPI

Gebruikerslimieten instellen voor MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>SSH-toetsen instellen voor MPI

SSH-toetsen instellen voor MPI-typen waarvoor dit vereist is.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

De bovenstaande syntaxis gaat ervan uit dat een gedeelde thuismap, anders .ssh directory moet worden gekopieerd naar elk knooppunt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) op Azure.
