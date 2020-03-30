---
title: Containerinstantie implementeren op GitHub-actie
description: Een GitHub-actie configureren die stappen automatiseert om een containerafbeelding te maken, te pushen en te implementeren in Azure Container Instances
ms.topic: article
ms.date: 03/18/2020
ms.custom: ''
ms.openlocfilehash: 13397cee8197afc65b93c587ae1505e59cfdebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258036"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Een GitHub-actie configureren om een containerinstantie te maken

[GitHub Actions](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) is een reeks functies in GitHub om uw softwareontwikkelingsworkflows te automatiseren op dezelfde plaats waar u code opslaat en samenwerkt aan pull-verzoeken en -problemen.

Gebruik de GitHub-actie [Deploy to Azure Container Instances](https://github.com/azure/aci-deploy) om de implementatie van een container naar Azure Container Instances te automatiseren. Met de actie u eigenschappen instellen voor een containerinstantie die vergelijkbaar is met die in de opdracht [AZ-container maken.][az-container-create]

In dit artikel ziet u hoe u een werkstroom instelt in een GitHub-repo waarmee de volgende acties worden uitgevoerd:

* Een afbeelding maken vanuit een Dockerfile
* De afbeelding naar een Azure-containerregister duwen
* De containerafbeelding implementeren in een Azure-containerinstantie

In dit artikel worden twee manieren weergegeven om de werkstroom in te stellen:

* Configureer zelf een werkstroom in een GitHub-repo met de actie Implementeren naar Azure Container Instances en andere acties.  
* Gebruik `az container app up` de opdracht in de [extensie Implementeren naar Azure](https://github.com/Azure/deploy-to-azure-cli-extension) in de Azure CLI. Deze opdracht stroomlijnt het maken van de GitHub-werkstroom en implementatiestappen.

> [!IMPORTANT]
> De GitHub-actie voor Azure Container Instances bevindt zich momenteel in de preview. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="prerequisites"></a>Vereisten

* **GitHub-account** - Maak https://github.com een account aan als je er nog geen hebt.
* **Azure CLI** - U de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de Azure CLI-stappen uit te voeren. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].
* **Azure-containerregister** - Als u er geen hebt, maakt u een Azure-containerregister in de basislaag met behulp van de [Azure CLI, Azure-portal](../container-registry/container-registry-get-started-azure-cli.md)of andere methoden. [Azure portal](../container-registry/container-registry-get-started-portal.md) Neem nota van de resourcegroep die wordt gebruikt voor de implementatie, die wordt gebruikt voor de GitHub-werkstroom.

## <a name="set-up-repo"></a>Repo instellen

* Voor de voorbeelden in dit artikel gebruikt u GitHub om de volgende opslagplaats te forken:https://github.com/Azure-Samples/acr-build-helloworld-node

  Deze repo bevat een Dockerfile en bronbestanden om een containerafbeelding van een kleine web-app te maken.

  ![Schermafbeelding van de knop Fork (gemarkeerd) in GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Zorg ervoor dat Acties zijn ingeschakeld voor uw opslagplaats. Navigeer naar uw gevorkte opslagplaats en selecteer > **Instellingenacties**. **Settings** Zorg er in **machtigingen voor acties**voor dat Lokale en derde acties voor deze **opslagplaats** inschakelen is geselecteerd.

## <a name="configure-github-workflow"></a>GitHub-werkstroom configureren

### <a name="create-service-principal-for-azure-authentication"></a>Serviceprincipal maken voor Azure-verificatie

In de GitHub-werkstroom moet u Azure-referenties leveren om te verifiëren aan de Azure CLI. In het volgende voorbeeld wordt een serviceprincipal gemaakt met de rol Inzender die is afgestemd op de resourcegroep voor uw containerregister.

Download eerst de resource-id van uw resourcegroep. Vervang de naam van je groep in de volgende opdracht [az-groepshow:][az-acr-show]

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Gebruik [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] om de serviceprincipal te maken:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

De uitvoer ziet er ongeveer zo uit:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Sla de JSON-uitvoer op omdat deze in een latere stap wordt gebruikt. Neem ook nota `clientId`van de , die u nodig hebt om de service principal bij te werken in de volgende sectie.

### <a name="update-service-principal-for-registry-authentication"></a>Serviceprincipal bijwerken voor registerverificatie

Werk de hoofdreferenties van de Azure-service bij om push- en pull-machtigingen toe te staan in uw containerregister. Met deze stap kan de GitHub-werkstroom de serviceprincipal gebruiken om te [verifiëren met uw containerregister.](../container-registry/container-registry-auth-service-principal.md) 

Haal de resource-id van uw containerregister op. Vervang de naam van uw register in de volgende [az acr show][az-acr-show] opdracht:

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Gebruik [het maken van az-rollenom][az-role-assignment-create] de AcrPush-rol toe te wijzen, die push- en pull-toegang geeft tot het register. Vervang de client-ID van uw serviceprincipal:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Referenties opslaan in GitHub repo

1. Navigeer in de GitHub-gebruikersinterface naar uw opslagplaats voor vorken en selecteer > **Instellingengeheimen.** **Settings** 

1. Selecteer **Een nieuw geheim toevoegen** om de volgende geheimen toe te voegen:

|Geheim  |Waarde  |
|---------|---------|
|`AZURE_CREDENTIALS`     | De volledige JSON-uitvoer van de serviceprincipal-creatie |
|`REGISTRY_LOGIN_SERVER`   | De naam van de loginserver van uw register (alle kleine letters). Voorbeeld: *myregistry.azure.cr.io*        |
|`REGISTRY_USERNAME`     |  De `clientId` van de JSON output van de service principal creatie       |
|`REGISTRY_PASSWORD`     |  De `clientSecret` van de JSON output van de service principal creatie |
| `RESOURCE_GROUP` | De naam van de resourcegroep die u hebt gebruikt om de serviceprincipal te scopen |

### <a name="create-workflow-file"></a>Werkstroombestand maken

1. Selecteer in de GitHub-gebruikersinterface de optie**Nieuwe werkstroom** **voor acties** > .
1. Selecteer **Zelf een werkstroom instellen**.
1. Plak in **Nieuw bestand bewerken**de volgende YAML-inhoud om de voorbeeldcode te overschrijven. Accepteer de standaardbestandsnaam `main.yml`of geef een door u gekozen bestandsnaam op.
1. Selecteer **Start commit,** geef optioneel korte en uitgebreide beschrijvingen van je commit en selecteer **Commit nieuw bestand.**

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@master
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Werkstroom valideren

Nadat u het werkstroombestand hebt vastgemaakt, wordt de werkstroom geactiveerd. Als u de voortgang van de werkstroom wilt controleren, navigeert u naar **Werkstromen voor** > **acties**. 

![Werkstroomvoortgang weergeven](./media/container-instances-github-action/github-action-progress.png)

Zie [Een werkstroom uitvoeren](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run) beheren voor informatie over het weergeven van de status en resultaten van elke stap in uw werkstroom.

Wanneer de werkstroom is voltooid, krijgt u informatie over de containerinstantie met de naam *aci-sampleapp* door de opdracht [AZ-containershow uit te][az-container-show] voeren. Vervang de naam van uw resourcegroep: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

De uitvoer ziet er ongeveer zo uit:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

Nadat de instantie is ingericht, navigeert u naar de FQDN van de container in uw browser om de draaiende web-app te bekijken.

![Web-app uitvoeren in browser](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Deploy naar Azure-extensie gebruiken

U ook de [extensie Implementeren naar Azure](https://github.com/Azure/deploy-to-azure-cli-extension) in de Azure CLI gebruiken om de werkstroom te configureren. De `az container app up` opdracht in de extensie neemt invoerparameters van u over om een werkstroom in te stellen die moet worden geïmplementeerd in Azure Container Instances. 

De werkstroom die is gemaakt door de Azure CLI is vergelijkbaar met de werkstroom die u handmatig [maken met GitHub.](#configure-github-workflow)

### <a name="additional-prerequisite"></a>Aanvullende voorwaarde

Naast de [vereisten](#prerequisites) en [repo-installatie](#set-up-repo) voor dit scenario, moet u de **extensie Deploy naar Azure** voor de Azure CLI installeren.

Voer de [opdracht AZ-extensie toevoegen uit][az-extension-add] om de extensie te installeren:

```azurecli
az extension add \
  --name deploy-to-azure
```

Zie [Extensies gebruiken met Azure CLI](/cli/azure/azure-cli-extensions-overview)voor informatie over het zoeken, installeren en beheren van extensies.

### <a name="run-az-container-app-up"></a>Voer `az container app up` uit.

Geef op om de opdracht [az-container-app uit te][az-container-app-up] voeren:

* De naam van uw Azure-containerregister, bijvoorbeeld *myregistry*
* De URL naar uw GitHub repo, bijvoorbeeld,`https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Voorbeeldopdracht:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Opdrachtvoortgang

* Geef desgevraagd uw GitHub-referenties op of geef u een [GitHub-token](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) voor persoonlijke toegang (PAT) met *repo-* en *gebruikersscopes* om te verifiëren bij uw register. Als u GitHub-referenties opgeeft, maakt de opdracht een PAT voor u.

* De opdracht maakt repo-geheimen voor de werkstroom:

  * Servicehoofdreferenties voor de Azure CLI
  * Referenties voor toegang tot het Azure-containerregister

* Nadat de opdracht het werkstroombestand aan uw repo heeft verbonden, wordt de werkstroom geactiveerd. 

De uitvoer ziet er ongeveer zo uit:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

### <a name="validate-workflow"></a>Werkstroom valideren

De werkstroom implementeert een Azure-containerinstantie met de basisnaam van uw GitHub-repo, in dit geval *acr-build-helloworld-node.* In uw browser u naar de meegeleverde koppeling bladeren om de web-app te bekijken. Als uw app luistert op een andere poort dan 8080, geeft u dat op in de URL.

Zie Een werkstroom uitvoeren beheren als u de werkstroomstatus en resultaten van elke stap in de GitHub-gebruikersinterface [wilt weergeven.](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)

## <a name="clean-up-resources"></a>Resources opschonen

Stop de containerinstantie met de opdracht [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Als u de brongroep en alle bronnen in de groep wilt verwijderen, voert u de opdracht verwijderen van de [AZ-groep uit:][az-group-delete]

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Volgende stappen

Blader door de [GitHub Marketplace](https://github.com/marketplace?type=actions) voor meer acties om uw ontwikkelingsworkflow te automatiseren


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
