---
title: 'Zelfstudie: een Azure Red Hat OpenShift 4-cluster maken'
description: Meer informatie over het maken van een Microsoft Azure Red Hat OpenShift-cluster met behulp van de Azure CLI
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: f8b34f1678d39471a1d0b91756ac93a01cbfedba
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800166"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Zelfstudie: Een Azure Red Hat OpenShift 4-cluster maken

In deze zelfstudie, deel een van drie, bereidt u uw omgeving voor op het maken van een Azure Red Hat OpenShift-cluster met OpenShift 4 en maakt u een cluster. U leert het volgende:
> [!div class="checklist"]
> * De vereisten instellen en het vereiste virtuele netwerk en subnetten maken
> * Een cluster implementeren

## <a name="before-you-begin"></a>Voordat u begint

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.75 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="verify-your-permissions"></a>Uw machtigingen controleren

Als u een Azure Red Hat OpenShift-cluster wilt maken, controleert u of u de volgende machtigingen heeft voor uw Azure-account en -gebruiker:

|Machtigingen|Resourcegroep die het VNet bevat|Gebruiker die `az aro create` uitvoert|Service-principal doorgegeven als `–client-id`|
|----|:----:|:----:|:----:|
|**Beheerder van gebruikerstoegang**|X|X| |
|**Inzender**|X|X|X|

### <a name="install-the-az-aro-extension"></a>De extensie `az aro` installeren
Met de `az aro`-extensie kunt u Azure Red Hat OpenShift-clusters rechtstreeks met de opdrachtregel maken, openen en verwijderen met behulp van de Azure CLI.

Voer de volgende opdracht uit om de `az aro`-extensie te installeren.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

Als de extensie al is geïnstalleerd, kunt u deze bijwerken met de volgende opdracht.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>De resourceprovider registreren

Vervolgens moet u de resourceprovider `Microsoft.RedHatOpenShift` registreren in uw abonnement.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Controleer of de extensie is geregistreerd.

```azurecli-interactive
az -v
```

  Als het goed is, wordt ongeveer de volgende uitvoer weergegeven.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Een pull-geheim voor Red Hat ophalen (optioneel)

Met een pull-geheim van Red Hat kan uw cluster toegang krijgen tot Red Hat-containerregisters en aanvullende inhoud. Deze stap is optioneel, maar wordt wel aanbevolen.

1. **[Ga naar de beheerportal van uw Red Hat OpenShift-cluster](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) en meld u aan.**

   U moet zich aanmelden bij uw Red Hat-account of een nieuw Red Hat-account maken met uw zakelijke e-mailadres en de algemene voorwaarden accepteren.

2. **Klik op pull-geheim downloaden.**

Bewaar het opgeslagen `pull-secret.txt`-bestand op een veilige plek; het wordt gebruikt bij het maken van elk cluster.

Wanneer u de opdracht `az aro create` uitvoert, kunt u verwijzen naar uw pull-geheim met behulp van de parameter `--pull-secret @pull-secret.txt`. Voer `az aro create` uit vanuit de map waarin u het `pull-secret.txt`-bestand hebt opgeslagen. Vervang anders `@pull-secret.txt` door `@<path-to-my-pull-secret-file>`.

Als u uw pull-geheim kopieert of ernaar verwijst in andere scripts, moet uw pull-geheim worden geformatteerd als een geldige JSON-tekenreeks.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Een virtueel netwerk met twee lege subnetten maken

Nu gaat u een virtueel netwerk met twee lege subnetten maken.

1. **Stel de volgende variabelen in.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **Een resourcegroep maken**

    Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Wanneer u een resourcegroep maakt, wordt u gevraagd een locatie op te geven. Op deze locatie zijn de metagegevens van de resourcegroep opgeslagen. Dit is ook de locatie waar uw resources worden uitgevoerd in Azure als u tijdens het maken van de resource geen andere regio opgeeft. Maak een resourcegroep met de opdracht [az group create][az-group-create].

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

    Azure Red Hat OpenShift-clusters met OpenShift 4 vereisen een virtueel netwerk met twee lege subnetten voor de hoofd- en werkknooppunten.

    Maak een nieuw virtueel netwerk in de resourcegroep die u eerder hebt gemaakt.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    In de volgende voorbeelduitvoer ziet u dat het virtuele netwerk is gemaakt:

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

3. **Voeg een leeg subnet toe voor de hoofdknooppunten.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Voeg een leeg subnet toe voor de werkknooppunten.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Beleid voor privé-eindpunten van subnet uitschakelen](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) op het hoofdsubnet.** Dit is vereist om verbinding te kunnen maken met het cluster en het te beheren.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Het cluster maken

Voer de volgende opdracht uit om een cluster te maken. U kunt eventueel [uw pull-geheim van Red Hat doorvoeren](#get-a-red-hat-pull-secret-optional), zodat uw cluster toegang krijgt tot Red Hat-containerregisters en aanvullende inhoud.

>[!NOTE]
> Als u de opdrachten kopieert en plakt en een van de optionele parameters gebruikt, moet u de oorspronkelijke hashtags en de navolgende opmerkingstekst verwijderen. Sluit ook het argument op de voorgaande regel van de opdracht af met een afsluitende backslash.

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

Nadat de `az aro create`-opdracht is uitgevoerd, duurt het doorgaans ongeveer 35 minuten om een cluster te maken.

>[!IMPORTANT]
> Als u ervoor kiest om een aangepast domein op te geven, bijvoorbeeld **foo.example.com**, is de OpenShift-console beschikbaar op een URL, zoals `https://console-openshift-console.apps.foo.example.com`, in plaats van in het ingebouwde domein `https://console-openshift-console.apps.<random>.<location>.aroapp.io`.
>
> OpenShift maakt standaard gebruik van zelfondertekende certificaten voor alle routes die op `*.apps.<random>.<location>.aroapp.io` worden gemaakt.  Als u ervoor kiest om aangepaste DNS te gebruiken nadat u verbinding hebt gemaakt met het cluster, moet u de OpenShift-documentatie volgen om [een aangepaste certificeringsinstantie voor uw ingangscontroller](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) en een [aangepaste certificeringsinstantie voor uw API-server te configureren](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).
>

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> * De vereisten instellen en het vereiste virtuele netwerk en subnetten maken
> * Een cluster implementeren

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Red Hat OpenShift-cluster](tutorial-connect-cluster.md)
