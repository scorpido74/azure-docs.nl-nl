---
title: 'Azure CLI-voorbeeldscript: een beheerde schijf maken op basis van een VHD-bestand in een opslagaccount in hetzelfde abonnement'
description: 'Azure CLI-voorbeeldscript: een beheerde schijf maken op basis van een VHD-bestand in een opslagaccount in hetzelfde abonnement'
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 12f8a8edc995a5b3e07244eb5c54835fa6dd832d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039947"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Maak een beheerde schijf op basis van een VHD-bestand in een opslagaccount in hetzelfde abonnement met CLI

Met dit script maakt u een beheerde schijf op basis van een VHD-bestand in een opslagaccount in hetzelfde abonnement. Gebruik dit script voor het importeren van een gespecialiseerde (geen gegeneraliseerde/op het systeem voorbereide) VHD naar een beheerde OS-schijf voor het maken van een virtuele machine. Of gebruik het script om een gegevens-VHD naar een beheerde gegevensschijf te importeren.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten om een beheerde schijf te maken op basis van een VHD. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Hiermee maakt u een beheerde schijf met behulp van de URI van een VHD in een opslagaccount in hetzelfde abonnement. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Meer informatie over de CLI-voorbeeld scripts voor virtuele machines en beheerde schijven vindt u in de documentatie van de [Azure Windows-VM](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
