---
title: Containerafbeelding implementeren vanuit Azure Container Registry
description: Meer informatie over het implementeren van containers in Azure Container Instances door containerafbeeldingen uit een Azure-containerregister te halen.
services: container-instances
ms.topic: article
ms.date: 02/18/2020
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 212624b857d65297830995018603c2627f83369b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453520"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Implementeren in Azure Container Instances vanuit Azure Container Registry

[Azure Container Registry](../container-registry/container-registry-intro.md) is een op Azure gebaseerde, beheerde containerregisterservice die wordt gebruikt voor het opslaan van privéDocker-containerafbeeldingen. In dit artikel wordt beschreven hoe u containerafbeeldingen ophaalt die zijn opgeslagen in een Azure-containerregister bij het implementeren in Azure Container Instances. Een aanbevolen manier om registertoegang te configureren is door een Azure Active Directory-serviceprincipal en wachtwoord te maken en de inloggegevens op te slaan in een Azure-sleutelkluis.

## <a name="prerequisites"></a>Vereisten

**Azure-containerregister**: U hebt een Azure-containerregister nodig - en ten minste één containerafbeelding in het register - om de stappen in dit artikel uit te voeren. Zie [Een containerregister maken met azure cli](../container-registry/container-registry-get-started-azure-cli.md)als u een register nodig hebt.

**Azure CLI:** de opdrachtregelvoorbeelden in dit artikel gebruiken de [Azure CLI](/cli/azure/) en zijn opgemaakt voor de Shell Bash. U [de Azure CLI](/cli/azure/install-azure-cli) lokaal installeren of de Azure Cloud [Shell][cloud-shell-bash]gebruiken.

## <a name="configure-registry-authentication"></a>Registerverificatie configureren

In een productiescenario waarin u toegang biedt tot 'headless'-services en -toepassingen, wordt aanbevolen om registertoegang te configureren met behulp van een [serviceprincipal.](../container-registry/container-registry-auth-service-principal.md) Met een serviceprincipal u [op rollen gebaseerde toegangscontrole](../container-registry/container-registry-roles.md) bieden voor uw containerafbeeldingen. U kunt bijvoorbeeld een service-principal configureren met alleen pull-toegang tot een register.

Azure Container Registry biedt extra [verificatieopties.](../container-registry/container-registry-authentication.md)

> [!NOTE]
> U niet verifiëren naar Azure Container Registry om afbeeldingen op te halen tijdens de implementatie van containergroepen met behulp van een [beheerde identiteit](container-instances-managed-identity.md) die is geconfigureerd in dezelfde containergroep.

In de volgende sectie maakt u een Azure-sleutelkluis en een serviceprincipal en slaat u de referenties van de serviceprincipal op in de kluis. 

### <a name="create-key-vault"></a>Sleutelkluis maken

Als u nog geen kluis hebt in [Azure Key Vault](../key-vault/general/overview.md), kunt u met de volgende opdrachten één maken met de Azure CLI.

Werk `RES_GROUP` de variabele bij met de naam van een bestaande `ACR_NAME` resourcegroep waarin de sleutelkluis kan worden gemaakt en met de naam van uw containerregister. Voor beknoptheid gaan opdrachten in dit artikel ervan uit dat uw register, sleutelkluis en containerexemplaren allemaal in dezelfde resourcegroep zijn gemaakt.

 Geef een naam op voor `AKV_NAME`uw nieuwe sleutelkluis in . De naam van de kluis moet uniek zijn binnen Azure en moet 3-24 alfanumerieke tekens lang zijn, beginnen met een letter, eindigen met een letter of cijfer en geen opeenvolgende koppeltekens bevatten.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Service-principal maken en referenties opslaan

Maak nu een serviceprincipal en bewaar de referenties ervan in uw sleutelkluis.

De volgende opdracht maakt gebruik [van az ad sp create-for-rbac][az-ad-sp-create-for-rbac] om de serviceprincipal te maken en az [keyvault secret set][az-keyvault-secret-set] om het **wachtwoord** van de serviceprincipal in de kluis op te slaan.

```azurecli
# Create service principal, store its password in vault (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Het argument `--role` in de voorgaande opdracht configureert de service-principal met de rol *acrpull*, die de principal alleen-push-toegang tot het register geeft. Als u zowel push-als pull-toegang wilt geven, wijzigt u het argument `--role` in *acrpush*.

Sla vervolgens de *appId* van de serviceprincipal op in de kluis, de **gebruikersnaam** die u doorgeeft aan Azure Container Registry voor verificatie.

```azurecli
# Store service principal ID in vault (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

U hebt een Azure-sleutelkluis gemaakt en er twee geheimen in opgeslagen:

* `$ACR_NAME-pull-usr`: de service principal-ID, voor gebruik als de **gebruikersnaam** van het containerregister.
* `$ACR_NAME-pull-pwd`: het service principal-wachtwoord, voor gebruik als het **wachtwoord** van het containerregister.

U kunt nu op naam naar deze geheime gegevens verwijzen wanneer u of uw toepassingen en services installatiekopieën uit het register halen.

## <a name="deploy-container-with-azure-cli"></a>Container implementeren met Azure CLI

Nu de hoofdreferenties van de service zijn opgeslagen in Azure Key Vault-geheimen, kunnen uw toepassingen en services deze gebruiken om toegang te krijgen tot uw privéregister.

Eerst krijgen van het register login server naam met behulp van de [az acr show][az-acr-show] commando. De naam van de inlogserver `myregistry.azurecr.io`is allemaal kleine letters en vergelijkbaar met .

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Voer de volgende [az container create][az-container-create]-opdracht in om een containerinstantie te implementeren. De opdracht gebruikt de referenties van de serviceprincipal die zijn opgeslagen in Azure Key Vault om te verifiëren bij uw containerregister en gaat ervan uit dat u eerder de [aci-helloworld-afbeelding](container-instances-quickstart.md) naar uw register hebt gepusht. Werk `--image` de waarde bij als u een andere afbeelding wilt gebruiken dan uw register.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

De `--dns-name-label` waarde moet uniek zijn binnen Azure, dus de vorige opdracht voegt een willekeurig nummer toe aan het DNS-naamlabel van de container. De uitvoer van de opdracht geeft bijvoorbeeld de Fully Qualified Domain Name (FQDN) van de container weer:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Zodra de container is gestart, u naar de FQDN in uw browser navigeren om te controleren of de toepassing succesvol wordt uitgevoerd.

## <a name="deploy-with-azure-resource-manager-template"></a>Implementeren met Azure Resource Manager-sjabloon

U de eigenschappen van uw Azure-containerregister opgeven `imageRegistryCredentials` in een Azure Resource Manager-sjabloon door de eigenschap op te nemen in de definitie van containergroep. U bijvoorbeeld de registerreferenties rechtstreeks opgeven:

```JSON
[...]
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
[...]
```

Zie de verwijzing naar de [sjabloon resourcebeheer](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups)voor volledige instellingen voor containergroepen.    

Zie [Azure Key Vault gebruiken om veilige parameterwaarde tijdens](../azure-resource-manager/templates/key-vault-parameter.md)de implementatie door te geven voor meer informatie over het verwijzen naar Azure Key Vault-geheimen in een Resource Manager-sjabloon.

## <a name="deploy-with-azure-portal"></a>Implementeren met Azure-portal

Als u containerafbeeldingen in een Azure-containerregister onderhoudt, u eenvoudig een container maken in Azure Container Instances met behulp van de Azure-portal. Wanneer u de portal gebruikt om een containerinstantie vanuit een containerregister te implementeren, moet u het [beheerdersaccount](../container-registry/container-registry-authentication.md#admin-account)van het register inschakelen. Het beheerdersaccount is ontworpen voor één gebruiker om toegang te krijgen tot het register, voornamelijk voor testdoeleinden. 

1. Navigeer in de Azure-portal naar uw containerregister.

1. Als u wilt controleren of het beheerdersaccount is ingeschakeld, selecteert u **Toegangssleutels**en selecteert u onder **Beheerder-gebruiker** **Inschakelen**.

1. Selecteer **Repositories**en selecteer vervolgens de opslagplaats waaruit u wilt implementeren, klik met de rechtermuisknop op de tag voor de containerafbeelding die u wilt implementeren en selecteer **Instantie uitvoeren**.

    ![Instantie uitvoeren in Azure Container Registry in de Azure-portal][acr-runinstance-contextmenu]

1. Voer een naam in voor de container en een naam voor de resourcegroep. U desgewenst ook de standaardwaarden wijzigen.

    ![Menu maken voor Azure Container Instances][acr-create-deeplink]

1. Zodra de implementatie is voltooid, u vanuit het deelvenster Meldingen naar de containergroep navigeren om het IP-adres en andere eigenschappen te vinden.

    ![Detailsweergave voor containergroep Azure Container Instances][aci-detailsview]

## <a name="next-steps"></a>Volgende stappen

Zie [Verifiëren met een Azure-containerregister](../container-registry/container-registry-authentication.md)voor meer informatie over azure containerregistry-verificatie.

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
