---
title: Een persoonlijk Azure Red Hat open Shift 4-cluster maken
description: Meer informatie over het maken van een Azure Red Hat open Shift private-cluster waarop open Shift 4 wordt uitgevoerd
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: ms-jasondel
ms.author: jasondel
keywords: Aro, open Shift, AZ Aro, Red Hat, cli
ms.custom: mvc
ms.openlocfilehash: 581587382c3bfd03ed329672e5c6ca065554d1c7
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727639"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Een persoonlijk Azure Red Hat open Shift 4-cluster maken

In dit artikel maakt u een voor bereiding van uw omgeving voor het maken van Azure Red Hat open Shift private-clusters waarop open Shift 4 wordt uitgevoerd. U leert het volgende:

> [!div class="checklist"]
> * Stel de vereisten in en maak het vereiste virtuele netwerk en subnetten
> * Een cluster met een persoonlijk API-server eindpunt en een persoonlijke ingangs controller implementeren

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelf studie gebruikmaken van de Azure CLI-versie 2.0.75 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Voordat u begint

### <a name="install-the-az-aro-extension"></a>De ' AZ Aro-extensie installeren
`az aro`Met de extensie kunt u Azure Red Hat open Shift-clusters rechtstreeks maken, openen en verwijderen vanaf de opdracht regel met behulp van de Azure cli.

Voer de volgende opdracht uit om de `az aro` extensie te installeren.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

Als u de uitbrei ding al hebt geïnstalleerd, kunt u bijwerken door de volgende opdracht uit te voeren.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>De resourceprovider registreren

Vervolgens moet u de `Microsoft.RedHatOpenShift` resource provider registreren in uw abonnement.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Controleer of de extensie is geregistreerd.

```azurecli-interactive
az -v
```

  U ziet een uitvoer die er ongeveer als volgt uitziet.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Een pull-geheim voor Red Hat ophalen (optioneel)

Met een Red Hat pull-geheim kan uw cluster toegang krijgen tot Red Hat-container registers, samen met aanvullende inhoud. Deze stap is optioneel, maar wordt aanbevolen.

1. **[Ga naar uw Red Hat open Shift cluster manager-Portal en meld u aan](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) .**

   U moet zich aanmelden bij uw Red Hat-account of een nieuw Red Hat-account maken met uw zakelijke e-mail adres en de voor waarden accepteren.

2. **Klik op pull Secret downloaden.**

Bewaar het opgeslagen `pull-secret.txt` bestand ergens anders. het wordt gebruikt bij het maken van een cluster.

Wanneer u de `az aro create` opdracht uitvoert, kunt u naar uw pull-geheim verwijzen met behulp van de `--pull-secret @pull-secret.txt` para meter. Voer `az aro create` uit in de map waar u het `pull-secret.txt` bestand hebt opgeslagen. Vervang anders door `@pull-secret.txt` `@<path-to-my-pull-secret-file` .

Als u uw pull-geheim kopieert of naar een ander script verwijst, moet uw pull-geheim worden geformatteerd als een geldige JSON-teken reeks.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Een virtueel netwerk met twee lege subnetten maken

Vervolgens maakt u een virtueel netwerk met twee lege subnetten.

1. **Stel de volgende variabelen in.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **Een resource groep maken**

    Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Wanneer u een resourcegroep maakt, wordt u gevraagd een locatie op te geven. Op deze locatie worden de meta gegevens van de resource groep opgeslagen, maar ook de resources die in Azure worden uitgevoerd als u geen andere regio opgeeft tijdens het maken van resources. Maak een resource groep met de opdracht [AZ Group Create] [AZ-Group-Create].

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    In de volgende voorbeelduitvoer ziet u dat de resourcegroep is gemaakt:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Maak een virtueel netwerk.**

    Azure Red Hat open Shift-clusters met open Shift 4 vereisen een virtueel netwerk met twee lege subnetten voor de hoofd-en worker-knoop punten.

    Maak een nieuw virtueel netwerk in dezelfde resource groep die u eerder hebt gemaakt.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    In de volgende voorbeeld uitvoer ziet u het virtuele netwerk dat is gemaakt:

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **Voeg een leeg subnet toe voor de hoofd knooppunten.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Voeg een leeg subnet toe voor de worker-knoop punten.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Beleid voor privé-eind punten van subnet uitschakelen](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) op het hoofd-subnet.** Dit is vereist om verbinding te kunnen maken met het cluster en deze te beheren.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Het cluster maken

Voer de volgende opdracht uit om een cluster te maken. U kunt ook [uw Red Hat pull Secret door geven](#get-a-red-hat-pull-secret-optional) , zodat uw cluster toegang kan krijgen tot Red Hat-container registers en aanvullende inhoud.

>[!NOTE]
> Als u de opdrachten kopiëren/plakken en een van de optionele para meters gebruikt, moet u ervoor zorgen dat u de oorspronkelijke Hashtags en de achterstallige tekst van de opmerking verwijdert. U kunt ook het argument op de voor gaande regel van de opdracht sluiten met een afsluitende back slash.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

Nadat de `az aro create` opdracht is uitgevoerd, duurt het ongeveer 35 minuten om een cluster te maken.

>[!IMPORTANT]
> Als u ervoor kiest om een aangepast domein op te geven, bijvoorbeeld **foo.example.com**, is de open Shift-console beschikbaar op een URL `https://console-openshift-console.apps.foo.example.com` , zoals in plaats van het ingebouwde domein `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .
>
> Open SHIFT maakt standaard gebruik van zelfondertekende certificaten voor alle routes die zijn gemaakt op `*.apps.<random>.<location>.aroapp.io` .  Als u aangepaste DNS kiest nadat u verbinding hebt gemaakt met het cluster, moet u de open Shift-documentatie volgen om [een aangepaste certificerings instantie voor uw ingangs controller](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) en [aangepaste certificerings instantie voor uw API-server](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)te configureren.

## <a name="connect-to-the-private-cluster"></a>Verbinding maken met het persoonlijke cluster

U kunt zich aanmelden bij het cluster met behulp van de `kubeadmin` gebruiker.  Voer de volgende opdracht uit om het wacht woord voor de gebruiker te zoeken `kubeadmin` .

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

In de volgende voorbeeld uitvoer ziet u dat het wacht woord in wordt weer gegeven `kubeadminPassword` .

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

>[!IMPORTANT]
> Als u verbinding wilt maken met een persoonlijk Azure Red Hat open Shift-cluster, moet u de volgende stap uitvoeren vanaf een host die zich bevindt in de Virtual Network die u hebt gemaakt of in een Virtual Network dat is gekoppeld aan de Virtual Network het [cluster is geïmplementeerd](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

Start de console-URL in een browser en meld u aan met de `kubeadmin` referenties.

![Aanmeldings scherm van Azure Red Hat open Shift](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>De open Shift-CLI installeren

Zodra u bent aangemeld bij de open Shift-webconsole, klikt u op de **?** in de rechter bovenhoek en vervolgens op **opdracht regel Programma's**. Down load de versie die geschikt is voor uw computer.

![Aanmeldings scherm van Azure Red Hat open Shift](media/aro4-download-cli.png)

U kunt ook de meest recente versie van de CLI die geschikt is voor uw computer, downloaden van <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> .

## <a name="connect-using-the-openshift-cli"></a>Verbinding maken met behulp van open Shift CLI

Haal het adres van de API-server op.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> Als u verbinding wilt maken met een persoonlijk Azure Red Hat open Shift-cluster, moet u de volgende stap uitvoeren vanaf een host die zich bevindt in de Virtual Network die u hebt gemaakt of in een Virtual Network dat is gekoppeld aan de Virtual Network het [cluster is geïmplementeerd](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

Meld u aan bij de API-server van het open Shift-cluster met de volgende opdracht. Vervang ** \< kubeadmin Password>** door het wacht woord dat u zojuist hebt opgehaald.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel is een Azure Red Hat open Shift-cluster met open Shift 4 geïmplementeerd. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Stel de vereisten in en maak het vereiste virtuele netwerk en subnetten
> * Een cluster implementeren
> * Verbinding maken met het cluster met behulp van de `kubeadmin` gebruiker

Ga naar het volgende artikel voor meer informatie over het configureren van het cluster voor verificatie met behulp van Azure Active Directory.


* [Verificatie met Azure Active Directory configureren via de opdracht regel](configure-azure-ad-cli.md)


* [Verificatie met Azure Active Directory configureren met behulp van de Azure Portal en open Shift web console](configure-azure-ad-cli.md)
