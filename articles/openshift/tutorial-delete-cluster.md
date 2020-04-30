---
title: Zelf studie-een Azure Red Hat open Shift-cluster verwijderen
description: In deze zelf studie leert u hoe u een Azure Red Hat open Shift-cluster kunt verwijderen met behulp van de Azure CLI
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 2de60b90eb6fb75ef013a2fd8785f1b8b616fba6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232135"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Zelf studie: een Azure Red Hat open Shift 4-cluster verwijderen

In deze zelf studie, deel drie van drie, wordt een Azure Red Hat open Shift-cluster met openshift 4 verwijderd. Procedures voor:

> [!div class="checklist"]
> * Een Azure Red Hat OpenShift-cluster verwijderen


## <a name="before-you-begin"></a>Voordat u begint

In de vorige zelf studies is een Azure Red Hat open Shift-cluster gemaakt en verbonden met behulp van de open Shift-webconsole. Als u deze stappen niet hebt uitgevoerd en u wilt door gaan met de [zelf studie 1-een Azure Red Hat open Shift 4-cluster maken.](tutorial-create-cluster.md)

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelf studie gebruikmaken van de Azure CLI-versie 2.0.75 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u de Azure CLI lokaal uitvoert, voert u `az login` uit om u aan te melden bij Azure.

```bash
az login
```

Als u toegang hebt tot meerdere abonnementen, voert `az account set -s {subscription ID}` u `{subscription ID}` de vervanging uit met het abonnement dat u wilt gebruiken.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

In de vorige zelf studies zijn de volgende variabelen ingesteld. 

```bash
CLUSTER=yourclustername
RESOURCE_GROUP=yourresourcegroup
```

Verwijder uw cluster met behulp van deze waarden:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

U wordt vervolgens gevraagd om te bevestigen of u het cluster wilt verwijderen. Nadat u hebt bevestigd `y`met, duurt het enkele minuten om het cluster te verwijderen. Wanneer de opdracht is voltooid, wordt de hele resource groep en alle resources erin, inclusief het cluster, verwijderd.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> * Een Azure Red Hat open Shift 4-cluster verwijderen

Meer informatie over het gebruik van open SHIFT met de officiële [Red Hat open Shift-documentatie](https://www.openshift.com/try)
