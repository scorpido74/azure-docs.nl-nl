---
title: 'Zelf studie: een Azure Red Hat open Shift-cluster schalen'
description: Meer informatie over het schalen van een Microsoft Azure Red Hat open Shift-cluster met behulp van de Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: bf9172f0c84834c951446520ff0bfcc3ef756c9c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278313"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Zelf studie: een Azure Red Hat open Shift-cluster schalen

Deze zelfstudie is deel twee van een serie. U leert hoe u een Microsoft Azure Red Hat open Shift-cluster maakt met behulp van Azure CLI, hoe u het kunt schalen en hoe u het kunt verwijderen om de resources op te schonen.

In deel twee van de serie leert u het volgende:

> [!div class="checklist"]
> * Een Red Hat open Shift-cluster schalen

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een Azure Red Hat OpenShift-cluster maken](tutorial-create-cluster.md)
> * Een Azure Red Hat open Shift-cluster schalen
> * [Een Azure Red Hat OpenShift-cluster verwijderen](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Maak een cluster door de zelf studie [een Azure Red Hat open Shift-cluster maken](tutorial-create-cluster.md) te volgen.

## <a name="step-1-sign-in-to-azure"></a>Stap 1: aanmelden bij Azure

Als u de Azure CLI lokaal uitvoert, voert u `az login` uit om u aan te melden bij Azure.

```bash
az login
```

Als u toegang hebt tot meerdere abonnementen, voert u `az account set -s {subscription ID}` vervangen `{subscription ID}` uit met het abonnement dat u wilt gebruiken.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Stap 2: het cluster schalen met extra knoop punten

Stel op een bash-Terminal de variabele CLUSTER_NAME in op de naam van het cluster:

```bash
CLUSTER_NAME=yourclustername
```

Nu gaan we het cluster schalen naar vijf knoop punten met behulp van de Azure CLI:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Na een paar minuten wordt `az openshift scale` voltooid en wordt een JSON-document geretourneerd met de geschaalde cluster Details.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Azure Red Hat open Shift-cluster schalen

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Een Azure Red Hat OpenShift-cluster verwijderen](tutorial-delete-cluster.md)
