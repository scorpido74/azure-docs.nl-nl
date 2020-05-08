---
title: Zelf studie-een Azure Red Hat open Shift 4-cluster maken
description: Meer informatie over het maken van een Microsoft Azure Red Hat open Shift-cluster met behulp van de Azure CLI
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 78ec45f5e6c354644e4303db53f276343225eff9
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858829"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Zelf studie: een Azure Red Hat open Shift 4-cluster maken

In deze zelf studie, deel een van drie, bereid u uw omgeving voor op het maken van een Azure Red Hat open Shift-cluster met openshift 4 en het maken van een cluster. U leert het volgende:
> [!div class="checklist"]
> * Stel de vereisten in en maak het vereiste virtuele netwerk en subnetten
> * Een cluster implementeren

## <a name="before-you-begin"></a>Voordat u begint

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelf studie gebruikmaken van de Azure CLI-versie 2.0.75 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="install-the-az-aro-extension"></a>De `az aro` uitbrei ding installeren
Met `az aro` de extensie kunt u Azure Red Hat open Shift-clusters rechtstreeks maken, openen en verwijderen vanaf de opdracht regel met behulp van de Azure cli.

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

1. **[Navigeer naar uw Red Hat open Shift cluster manager-Portal en meld u aan](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) .**

   U moet zich aanmelden bij uw Red Hat-account of een nieuw Red Hat-account maken met uw zakelijke e-mail adres en de voor waarden accepteren.

2. **Klik op pull Secret downloaden.**

Bewaar het opgeslagen `pull-secret.txt` bestand ergens anders. het wordt gebruikt bij het maken van een cluster.

Wanneer u de `az aro create` opdracht uitvoert, kunt u naar uw pull-geheim `--pull-secret @pull-secret.txt` verwijzen met behulp van de para meter. Voer `az aro create` uit in de map waar u het `pull-secret.txt` bestand hebt opgeslagen. Vervang `@pull-secret.txt` anders door `@<path-to-my-pull-secret-file>`.

Als u uw pull-geheim kopieert of naar een ander script verwijst, moet uw pull-geheim worden geformatteerd als een geldige JSON-teken reeks.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Een virtueel netwerk met twee lege subnetten maken

Vervolgens maakt u een virtueel netwerk met twee lege subnetten.

1. **Stel de volgende variabelen in.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
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
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

Nadat de `az aro create` opdracht is uitgevoerd, duurt het ongeveer 35 minuten om een cluster te maken.

>[!IMPORTANT]
> Als u ervoor kiest om een aangepast domein op te geven, bijvoorbeeld **foo.example.com**, is de open Shift-console beschikbaar op een URL `https://console-openshift-console.apps.foo.example.com`, zoals in plaats van het ingebouwde domein `https://console-openshift-console.apps.<random>.<location>.aroapp.io`.
>
> Open SHIFT maakt standaard gebruik van zelfondertekende certificaten voor alle routes die zijn gemaakt op `*.apps.<random>.<location>.aroapp.io`.  Als u ervoor kiest om aangepaste DNS te gebruiken nadat u verbinding hebt gemaakt met het cluster, moet u de open Shift-documentatie volgen om [een aangepaste certificerings instantie voor uw ingangs controller](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) en een [aangepaste certificerings instantie voor uw API-server](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)te configureren.
>

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> * Stel de vereisten in en maak het vereiste virtuele netwerk en subnetten
> * Een cluster implementeren

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Red Hat open Shift-cluster](tutorial-connect-cluster.md)
