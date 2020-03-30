---
title: Azure HPC-cachegegevens inbeslagnemen - parallel kopieerscript
description: Een parallel kopieerscript gebruiken om gegevens naar een Blob-opslagdoel in Azure HPC-cache te verplaatsen
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 90e05ad3d42b1009b631630fe476669a9f418d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74166897"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Azure HPC-cachegegevens inbeslagnemen - parallelle kopieerscriptmethode

In dit artikel vindt ``parallelcp`` u instructies voor het maken van het script en wordt het gebruikt om gegevens naar een Blob-opslagcontainer te verplaatsen voor gebruik met Azure HPC-cache.

Lees [Gegevens verplaatsen naar Azure Blob-opslag voor](hpc-cache-ingest.md)meer informatie over het verplaatsen van gegevens naar Blob-opslag voor uw Azure HPC-cache.

## <a name="create-the-parallelcp-script"></a>Het parallelcp-script maken

Het onderstaande script voegt `parallelcp`het uitvoerbare toe. (Dit script is ontworpen voor Ubuntu; als ``parallel`` u een andere distributie gebruikt, moet u afzonderlijk installeren.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>Voorbeeld van parallelle kopie

In dit voorbeeld wordt het ``glibc`` parallelle kopieerscript gebruikt om te compileren met behulp van bronbestanden in de Azure HPC-cache.

De bronbestanden worden in de cache opgeslagen in het bevestigingspunt azure HPC-cache en de objectbestanden worden opgeslagen op de lokale harde schijf.

In dit voorbeeld wordt het ``-j`` parallelle ``make`` kopieerscript met de optie gebruikt en wordt parallelisatie uitgevoerd.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
