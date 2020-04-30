---
title: Zelf studie-verbinding maken met een Azure Red Hat open Shift 4-cluster
description: Meer informatie over het verbinden van een Microsoft Azure Red Hat open Shift-cluster
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d7efe781f1ba2beb1fa7dd4fdaaad280fc789de2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82204384"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>Zelf studie: verbinding maken met een Azure Red Hat open Shift 4-cluster

In deze zelf studie, deel twee van drie, maakt u verbinding met een Azure Red Hat open Shift-cluster met openshift 4 als de kubeadmin-gebruiker met behulp van de open Shift-webconsole. Procedures voor:
> [!div class="checklist"]
> * Referenties `kubeadmin` voor uw cluster verkrijgen
> * De open Shift-CLI installeren
> * Verbinding maken met een Azure Red Hat open Shift-cluster met open Shift CLI

## <a name="before-you-begin"></a>Voordat u begint

In de vorige zelf studies is een Azure Red Hat open Shift-cluster gemaakt. Als u deze stappen niet hebt uitgevoerd en u wilt door gaan met de [zelf studie 1-een Azure Red Hat open Shift 4-cluster maken.](tutorial-create-cluster.md)

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelf studie gebruikmaken van de Azure CLI-versie 2.0.75 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

U kunt zich aanmelden bij het cluster met `kubeadmin` behulp van de gebruiker.  Voer de volgende opdracht uit om het wacht woord voor `kubeadmin` de gebruiker te zoeken.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

In de volgende voorbeeld uitvoer ziet u dat het wacht `kubeadminPassword`woord in wordt weer gegeven.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

U kunt de URL van de cluster console vinden door de volgende opdracht uit te voeren, die er als volgt uitziet:`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

Start de console-URL in een browser en meld u `kubeadmin` aan met de referenties.

![Aanmeldings scherm van Azure Red Hat open Shift](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>De open Shift-CLI installeren

Zodra u bent aangemeld bij de open Shift-webconsole, klikt u op de **?** in de rechter bovenhoek en vervolgens op **opdracht regel Programma's**. Down load de versie die geschikt is voor uw computer.

![Aanmeldings scherm van Azure Red Hat open Shift](media/aro4-download-cli.png)

U kunt ook de meest recente versie van de CLI die geschikt is voor uw <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>computer, downloaden van.

Als u de opdrachten uitvoert op de Azure Cloud Shell, downloadt u de nieuwste open Shift 4 CLI voor Linux.

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>Verbinding maken met behulp van open Shift CLI

Haal het adres van de API-server op.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

Meld u aan bij de API-server van het open Shift-cluster met de volgende opdracht. Vervang ** \<kubeadmin Password>** door het wacht woord dat u zojuist hebt opgehaald.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> * Referenties `kubeadmin` voor uw cluster verkrijgen
> * De open Shift-CLI installeren
> * Verbinding maken met een Azure Red Hat open Shift-cluster met open Shift CLI

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Een Azure Red Hat OpenShift-cluster verwijderen](tutorial-delete-cluster.md)