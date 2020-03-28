---
title: Zelfstudie - Een Azure Red Hat OpenShift-cluster schalen
description: Meer informatie over het schalen van een Microsoft Azure Red Hat OpenShift-cluster met de Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c6334aa20b543dfbf87fedcfe45d54bbcf7a219a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477014"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Zelfstudie: Een Azure Red Hat OpenShift-cluster schalen

Deze zelfstudie is deel twee van een serie. U leert hoe u een Microsoft Azure Red Hat OpenShift-cluster maakt met behulp van het Azure CLI, deze schaalt en vervolgens verwijdert om resources op te schonen.

In deel twee van de serie leert u het volgende:

> [!div class="checklist"]
> * Een OpenShift-cluster voor rode hoed schalen

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een Azure Red Hat OpenShift-cluster maken](tutorial-create-cluster.md)
> * De schaal van een Azure Red Hat OpenShift-cluster wijzigen
> * [Een Azure Red Hat OpenShift-cluster verwijderen](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Maak een cluster door de [zelfstudie van Een Azure Red Hat OpenShift-cluster te](tutorial-create-cluster.md) volgen.

## <a name="step-1-sign-in-to-azure"></a>Stap 1: Aanmelden bij Azure

Als u de Azure CLI lokaal `az login` uitvoert, voert u uit om u aan te melden bij Azure.

```azurecli
az login
```

Als u toegang hebt tot `az account set -s {subscription ID}` meerdere `{subscription ID}` abonnementen, voert u de vervanging uit van het abonnement dat u wilt gebruiken.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Stap 2: Het cluster schalen met extra knooppunten

Stel vanaf een Bash-terminal de variabele CLUSTER_NAME in op de naam van uw cluster:

```bash
CLUSTER_NAME=yourclustername
```

Laten we het cluster nu schalen naar vijf knooppunten met de Azure CLI:

```azurecli
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Na een paar `az openshift scale` minuten wordt het een JSON-document met de geschaalde clusterdetails voltooid en wordt een JSON-document retourneren.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * De schaal van een Azure Red Hat OpenShift-cluster wijzigen

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Een Azure Red Hat OpenShift-cluster verwijderen](tutorial-delete-cluster.md)
