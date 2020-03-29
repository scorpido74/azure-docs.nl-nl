---
title: Ongekoppelde Azure-beheerde en onbeheerde schijven zoeken en verwijderen
description: Onbeheerde en onbeheerde (VHD's/paginablobs) schijven zoeken en verwijderen met Azure CLI.
author: roygara
ms.service: virtual-machines
ms.topic: article
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6cf48c53e7b5c1cc8537abeda164460de66abddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945151"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Ongekoppelde Azure-beheerde en onbeheerde schijven zoeken en verwijderen met behulp van de Azure CLI
Wanneer u een virtuele machine (VM) in Azure verwijdert, worden standaard geen schijven verwijderd die aan de vm zijn gekoppeld. Deze functie helpt om gegevensverlies te voorkomen als gevolg van het onbedoeld verwijderen van VM's. Nadat een virtuele machine is verwijderd, blijft u betalen voor niet-gekoppelde schijven. In dit artikel ziet u hoe u niet-gekoppelde schijven vinden en verwijderen en onnodige kosten verlagen. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Beheerde schijven: niet-gekoppelde schijven zoeken en verwijderen 

In het volgende script wordt gezocht naar niet-gekoppelde [beheerde schijven](managed-disks-overview.md) door de waarde van de eigenschap **ManagedBy te** onderzoeken. Wanneer een beheerde schijf is gekoppeld aan een vm, bevat de eigenschap **ManagedBy** de bron-id van de vm. Wanneer een beheerde schijf niet is gekoppeld, is de eigenschap **ManagedBy** null. Het script onderzoekt alle beheerde schijven in een Azure-abonnement. Wanneer het script een beheerde schijf met de eigenschap **ManagedBy** op null lokaliseert, bepaalt het script dat de schijf niet is gekoppeld.

>[!IMPORTANT]
>Voer eerst het script uit door de variabele **deleteUnattachedDisks** in te stellen op 0. Met deze actie u alle niet-gekoppelde beheerde schijven vinden en bekijken.
>
>Nadat u alle niet-gekoppelde schijven hebt bekeken, voert u het script opnieuw uit en stelt u de variabele **deleteUnattachedDisks** in op 1. Met deze actie u alle niet-gekoppelde beheerde schijven verwijderen.
>

```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Niet-beheerde schijven: niet-gekoppelde schijven zoeken en verwijderen 

Onbeheerde schijven zijn VHD-bestanden die zijn opgeslagen als [paginablobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) in [Azure-opslagaccounts.](../../storage/common/storage-create-storage-account.md) In het volgende script wordt gezocht naar niet-gekoppelde niet-beheerde schijven (paginablobs) door de waarde van de eigenschap **LeaseStatus** te onderzoeken. Wanneer een niet-beheerde schijf is gekoppeld aan een vm, is de eigenschap **LeaseStatus** ingesteld op **Vergrendeld**. Wanneer een niet-beheerde schijf niet is gekoppeld, is de eigenschap **LeaseStatus** ingesteld op **Ontgrendeld**. Het script onderzoekt alle niet-beheerde schijven in alle Azure-opslagaccounts in een Azure-abonnement. Wanneer het script een onbeheerde schijf met een **eigenschap LeaseStatus** op **Ontgrendeld**vindt, bepaalt het script dat de schijf niet is gekoppeld.

>[!IMPORTANT]
>Voer eerst het script uit door de variabele **deleteUnattachedVHDs** in te stellen op 0. Met deze actie u alle niet-gekoppelde onbeheerde VHD's vinden en bekijken.
>
>Nadat u alle niet-gekoppelde schijven hebt bekeken, voert u het script opnieuw uit en stelt u de variabele **deleteUnattachedVHDs** in op 1. Met deze actie u alle niet-gekoppelde onbeheerde VHD's verwijderen.
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>Volgende stappen

[Opslagaccount verwijderen](../../storage/common/storage-create-storage-account.md)



