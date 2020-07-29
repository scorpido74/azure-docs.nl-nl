---
title: 'Zelfstudie: Een Azure Red Hat OpenShift-cluster verwijderen'
description: In deze zelfstudie leert u hoe u een Azure Red Hat OpenShift-cluster verwijdert met behulp van de Azure CLI
author: sakthi-vetrivel
ms.custom: fasttrack-edit
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 6fdc36dbf270be61457dcd00dd1988808a80d062
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030093"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Zelfstudie: Een Azure Red Hat OpenShift 4-cluster verwijderen

In deze zelfstudie, deel drie van een driedelige reeks, wordt een Azure Red Hat OpenShift-cluster met OpenShift 4 verwijderd. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een Azure Red Hat OpenShift-cluster verwijderen


## <a name="before-you-begin"></a>Voordat u begint

In de vorige zelfstudies is een Azure Red Hat OpenShift-cluster gemaakt en verbonden met behulp van de OpenShift-webconsole. Als u deze stappen niet hebt uitgevoerd en u deze zelfstudie toch wilt volgen, begint u met [Zelfstudie 1: Een Azure Red Hat OpenShift 4-cluster maken](tutorial-create-cluster.md).

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.75 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u de Azure CLI lokaal uitvoert, voert u `az login` uit om u aan te melden bij Azure.

```bash
az login
```

Als u toegang hebt tot meerdere abonnementen, voert u `az account set -s {subscription ID}` uit en vervangt u `{subscription ID}` door het abonnement dat u wilt gebruiken.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

In de vorige zelfstudies zijn de volgende variabelen ingesteld. 

```bash
CLUSTER=yourclustername
RESOURCE_GROUP=yourresourcegroup
```

Verwijder uw cluster met behulp van deze waarden:

```bash
az aro delete --resource-group $RESOURCE_GROUP --name $CLUSTER
```

U wordt vervolgens gevraagd om te bevestigen of u het cluster wilt verwijderen. Nadat u hebt bevestigd met `y`, duurt het enkele minuten om het cluster te verwijderen. Wanneer de opdracht is voltooid, wordt de hele resourcegroep en alle resources erin, inclusief het cluster, verwijderd.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> * Een Azure Red Hat OpenShift 4-cluster verwijderen

Meer informatie over het gebruik van OpenShift vindt u in de officiële [Red Hat OpenShift-documentatie](https://www.openshift.com/try)
