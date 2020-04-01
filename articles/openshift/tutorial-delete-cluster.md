---
title: Zelfstudie - Een Azure Red Hat OpenShift-cluster verwijderen
description: Lees in deze zelfstudie hoe u een Azure Red Hat OpenShift-cluster verwijdert met de Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76278779"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Zelfstudie: Een Azure Red Hat OpenShift-cluster verwijderen

Dit is het einde van de zelfstudie. Wanneer u klaar bent met het testen van het voorbeeldcluster, u het cluster en de bijbehorende bronnen als u niet in rekening worden gebracht voor wat u niet gebruikt.

In deel drie van de serie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Red Hat OpenShift-cluster verwijderen

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een Azure Red Hat OpenShift-cluster maken](tutorial-create-cluster.md)
> * [De schaal van een Azure Red Hat OpenShift-cluster wijzigen](tutorial-scale-cluster.md)
> * Een Azure Red Hat OpenShift-cluster verwijderen

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Maak een cluster door de [zelfstudie van Een Azure Red Hat OpenShift-cluster te](tutorial-create-cluster.md) volgen.

## <a name="step-1-sign-in-to-azure"></a>Stap 1: Aanmelden bij Azure

Als u de Azure CLI lokaal `az login` uitvoert, voert u uit om u aan te melden bij Azure.

```bash
az login
```

Als u toegang hebt tot `az account set -s {subscription ID}` meerdere `{subscription ID}` abonnementen, voert u de vervanging uit van het abonnement dat u wilt gebruiken.

## <a name="step-2-delete-the-cluster"></a>Stap 2: Het cluster verwijderen

Open een Bash-terminal en stel de variabele CLUSTER_NAME in op de naam van uw cluster:

```bash
CLUSTER_NAME=yourclustername
```

Verwijder nu uw cluster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

U krijgt de vraag of u het cluster wilt verwijderen. Nadat u `y`het hebt bevestigd met , duurt het enkele minuten om het cluster te verwijderen. Wanneer de opdracht is voltooid, worden de volledige resourcegroep en alle bronnen erin, inclusief het cluster, verwijderd.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Een cluster verwijderen met de Azure-portal

U de bijbehorende brongroep van uw cluster ook verwijderen via de online Azure-portal. De naam van de resourcegroep is dezelfde als de naam van uw cluster.

Momenteel is `Microsoft.ContainerService/openShiftManagedClusters` de bron die wordt gemaakt bij het maken van het cluster verborgen in de Azure-portal. Controleer `Resource group` in de `Show hidden types` weergave of u de resourcegroep wilt weergeven.

![Schermafbeelding van het selectievakje Verborgen tekst](./media/aro-portal-hidden-type.png)

Als u de brongroep verwijdert, worden alle gerelateerde resources verwijderd die worden gemaakt wanneer u een Azure Red Hat OpenShift-cluster bouwt.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> * Een Azure Red Hat OpenShift-cluster verwijderen

Meer informatie over het gebruik van OpenShift met de officiële [Red Hat OpenShift-documentatie](https://docs.openshift.com/aro/welcome/index.html)
