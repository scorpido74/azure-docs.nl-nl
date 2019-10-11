---
title: Azure HPC-cache preview gegevens opname-parallel kopiëren script
description: Een script voor parallel kopiëren gebruiken om gegevens naar een Blob Storage-doel te verplaatsen in de Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: rohogue
ms.openlocfilehash: 4899f946cb358693c969def3fa740af64675d934
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254527"
---
# <a name="azure-hpc-cache-preview-data-ingest---parallel-copy-script-method"></a>Azure HPC-cache (preview) gegevens opname-parallelle kopie script methode

Dit artikel bevat instructies voor het maken van het script ``parallelcp`` en het gebruiken om gegevens naar een BLOB storage-container te verplaatsen voor gebruik met de Azure HPC-cache.

Lees voor meer informatie over het verplaatsen van gegevens naar Blob Storage voor uw Azure HPC-cache [gegevens verplaatsen naar Azure Blob-opslag voor Azure HPC-cache](hpc-cache-ingest.md).

## <a name="create-the-parallelcp-script"></a>Het parallelcp-script maken

In het onderstaande script wordt het uitvoer bare `parallelcp` toegevoegd. (Dit script is ontworpen voor Ubuntu; als u een andere distributie gebruikt, moet u ``parallel`` afzonderlijk installeren.)

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

## <a name="parallel-copy-example"></a>Voor beeld van parallel kopiëren

In dit voor beeld wordt het script voor parallelle kopieën gebruikt om ``glibc`` te compileren met behulp van de bron bestanden in de Azure HPC-cache.

De bron bestanden worden in het cache geheugen van de Azure HPC-cache geplaatst en de object bestanden worden opgeslagen op de lokale vaste schijf.

In dit voor beeld wordt het script voor parallelle kopieën gebruikt met de optie ``-j`` en ``make`` om parallel Lise ring te krijgen.

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
