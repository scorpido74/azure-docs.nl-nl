---
title: Message Passing Interface instellen voor HPC-Azure-Virtual Machines | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77023987"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Bericht interface voor het door geven van HPC instellen

MPI-workloads (Message Passing Interface) zijn een belang rijk onderdeel van traditionele HPC-workloads. Met de VM-grootten van SR-IOV in azure kunt u bijna alle MPI gebruiken. 

Voor het uitvoeren van MPI-taken op Vm's is het instellen van partitie sleutels (p-sleutels) in een Tenant vereist. Volg de stappen in de sectie [partitie sleutels detecteren](#discover-partition-keys) voor meer informatie over het bepalen van de p-sleutel waarden.

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) biedt de beste prestaties op IB en werkt met mpich en openmpi.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Installeer UCX zoals eerder is beschreven.

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

Voer OpenMPI uit.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Controleer de hierboven vermelde partitie sleutel.

## <a name="mpich"></a>MPICH

Installeer UCX zoals eerder is beschreven.

MPICH bouwen.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

MPICH uitvoeren.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Controleer de hierboven vermelde partitie sleutel.

## <a name="mvapich2"></a>MVAPICH2

MVAPICH2 bouwen.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

MVAPICH2 uitvoeren.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platform MPI Community Edition

Installeer de vereiste pakketten voor platform MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Volg het installatie proces.

## <a name="intel-mpi"></a>Intel MPI

[Down load Intel mpi](https://software.intel.com/mpi-library/choose-download).

Wijzig de I_MPI_FABRICS omgevings variabele, afhankelijk van de versie. Voor Intel MPI 2018, gebruik `I_MPI_FABRICS=shm:ofa` en voor 2019, gebruikt `I_MPI_FABRICS=shm:ofi`u.

Proces vastmaken werkt standaard goed voor 15, 30 en 60 PPN.

## <a name="osu-mpi-benchmarks"></a>OSU MPI-benchmarks

[Down load OSU mpi-benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) en untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Benchmarks bouwen met een bepaalde MPI-bibliotheek:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI-benchmarks bevinden zich in `mpi/` de map.


## <a name="discover-partition-keys"></a>Partitie sleutels detecteren

Detectie van partitie sleutels (p-sleutels) voor communicatie met andere Vm's binnen dezelfde Tenant (Beschikbaarheidsset of VM-Schaalset).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Hoe groter de twee is, is de Tenant sleutel die moet worden gebruikt met MPI. Voor beeld: als het volgende de p-sleutels zijn, moet 0x800b worden gebruikt met MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Gebruik de partitie sleutel anders dan standaard (0x7fff). UCX vereist dat de MSB van p-sleutel moet worden gewist. Stel bijvoorbeeld UCX_IB_PKEY in als 0x000b voor 0x800b.

Houd er ook rekening mee dat als de Tenant (AVSet of VMSS) bestaat, de PKEYs hetzelfde blijft. Dit geldt ook wanneer er knoop punten worden toegevoegd of verwijderd. Nieuwe tenants krijgen verschillende PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Gebruikers limieten instellen voor MPI

Gebruikers limieten instellen voor MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>SSH-sleutels voor MPI instellen

Stel SSH-sleutels voor MPI-typen in waarvoor deze nodig is.

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

De bovenstaande syntaxis gaat ervan uit dat een gedeelde basismap, else. ssh-map, naar elk knoop punt moet worden gekopieerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) op Azure.
