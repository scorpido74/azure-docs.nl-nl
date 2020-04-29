---
title: Implementatievoorbeeld van een Azure CLI-script
description: Een beveiligd Service Fabric Linux-cluster maken in azure met behulp van de Azure-opdracht regel interface (CLI).
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 2ef8f322ff17eeb5d75d3cc8e4f8604f02d4ef0e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80366546"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Een beveiligd Service Fabric Linux-cluster maken in Azure

Met deze opdracht wordt een zelfondertekend certificaat gemaakt, wordt dit toegevoegd aan een sleutelkluis en wordt het certificaat lokaal gedownload.  Het nieuwe certificaat wordt gebruikt om het cluster te beveiligen wanneer het wordt geïmplementeerd.  U kunt ook een bestaand certificaat gebruiken in plaats van een nieuw te maken.  In beide gevallen moet de onderwerpnaam van het certificaat overeenkomen met het domein dat u gebruikt om toegang te krijgen tot het Service Fabric-cluster. Deze overeenkomst is vereist om TLS te bieden voor de HTTPS-beheer eindpunten en Service Fabric Explorer van het cluster. U kunt geen TLS/SSL-certificaat verkrijgen van een certificerings `.cloudapp.azure.com` instantie voor het domein. U hebt voor uw cluster een aangepaste domeinnaam nodig. Wanneer u een certificaat van een CA aanvraagt, moet de onderwerpnaam van het certificaat overeenkomen met de aangepaste domeinnaam die u voor uw cluster gebruikt.

Installeer zo nodig [Azure CLI](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sample-script"></a>Voorbeeldscript

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Nadat het voorbeeldscript is uitgevoerd, kan de volgende opdracht worden gebruikt om de resourcegroep, het cluster en alle gerelateerde resources te verwijderen.

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az sf cluster create](https://docs.microsoft.com/cli/azure/sf/cluster?view=azure-cli-latest) | Hiermee wordt een nieuw Service Fabric-cluster gemaakt.  |

## <a name="next-steps"></a>Volgende stappen

Meer Service Fabric CLI-voorbeelden voor Azure Service Fabric zijn te vinden in de [Voorbeelden van Azure Service Fabric CLI](../samples-cli.md).
