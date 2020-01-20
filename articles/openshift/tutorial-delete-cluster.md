---
title: Zelf studie-een Azure Red Hat open Shift-cluster verwijderen
description: In deze zelf studie leert u hoe u een Azure Red Hat open Shift-cluster kunt verwijderen met behulp van de Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278779"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Zelf studie: een Azure Red Hat open Shift-cluster verwijderen

Dit is het einde van de zelfstudie. Wanneer u klaar bent met het testen van het voorbeeld cluster, kunt u dit verwijderen en de bijbehorende resources, zodat u niet hoeft te betalen voor wat u niet gebruikt.

In deel drie van de serie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Red Hat open Shift-cluster verwijderen

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een Azure Red Hat OpenShift-cluster maken](tutorial-create-cluster.md)
> * [De schaal van een Azure Red Hat OpenShift-cluster wijzigen](tutorial-scale-cluster.md)
> * Een Azure Red Hat open Shift-cluster verwijderen

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Maak een cluster door de zelf studie [een Azure Red Hat open Shift-cluster maken](tutorial-create-cluster.md) te volgen.

## <a name="step-1-sign-in-to-azure"></a>Stap 1: aanmelden bij Azure

Als u de Azure CLI lokaal uitvoert, voert u `az login` uit om u aan te melden bij Azure.

```bash
az login
```

Als u toegang hebt tot meerdere abonnementen, voert u `az account set -s {subscription ID}` vervangen `{subscription ID}` uit met het abonnement dat u wilt gebruiken.

## <a name="step-2-delete-the-cluster"></a>Stap 2: het cluster verwijderen

Open een bash-Terminal en stel de variabele CLUSTER_NAME in op de naam van het cluster:

```bash
CLUSTER_NAME=yourclustername
```

Verwijder nu het cluster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

U wordt gevraagd of u het cluster wilt verwijderen. Nadat u met `y`hebt gecontroleerd, duurt het enkele minuten om het cluster te verwijderen. Wanneer de opdracht is voltooid, worden de hele resource groep en alle resources erin, inclusief het cluster, verwijderd.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Een cluster verwijderen met de Azure Portal

U kunt ook de gekoppelde resource groep van uw cluster verwijderen via de online Azure Portal. De naam van de resource groep is hetzelfde als de naam van uw cluster.

Op dit moment wordt de `Microsoft.ContainerService/openShiftManagedClusters` resource die wordt gemaakt tijdens het maken van het cluster, verborgen in de Azure Portal. Controleer in de weer gave `Resource group` `Show hidden types` om de resource groep weer te geven.

![Scherm afbeelding van het selectie vakje verborgen type](./media/aro-portal-hidden-type.png)

Als u de resource groep verwijdert, worden alle gerelateerde resources verwijderd die worden gemaakt wanneer u een Azure Red Hat open Shift-cluster bouwt.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> * Een Azure Red Hat open Shift-cluster verwijderen

Meer informatie over het gebruik van open SHIFT met de officiële [Red Hat open Shift-documentatie](https://docs.openshift.com/aro/welcome/index.html)
