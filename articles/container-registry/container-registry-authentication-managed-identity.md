---
title: Verifiëren met beheerde identiteit
description: Geef toegang tot afbeeldingen in uw privécontainerregister met behulp van een door de gebruiker toegewezen of door het systeem toegewezen beheerde Azure-identiteit.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 9b8bed78629d3a9739ec00772ad5c8216a04c122
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456489"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Een door Azure beheerde identiteit gebruiken om te verifiëren naar een Azure-containerregister 

Gebruik een [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) om te verifiëren voor een Azure-containerregister vanuit een andere Azure-bron, zonder dat u registerreferenties hoeft op te geven of beheren. Stel bijvoorbeeld een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit in op een Linux-vm om containerafbeeldingen vanuit uw containerregister te openen, net zo gemakkelijk als een openbaar register.

Voor dit artikel vindt u meer informatie over beheerde identiteiten en hoe u:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen of door het systeem toegewezen identiteit inschakelen op een Azure VM
> * De identiteit toegang verlenen tot een Azure-containerregister
> * De beheerde identiteit gebruiken om toegang te krijgen tot het register en een containerafbeelding op te halen 

Als u de Azure-resources wilt maken, moet u in dit artikel de Azure CLI-versie 2.0.55 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli].

Als u een containerregister wilt instellen en er een containerafbeelding op wilt duwen, moet docker ook lokaal zijn geïnstalleerd. Docker biedt pakketten die eenvoudig Docker configureren op elk [Mac][docker-mac]-, [Windows][docker-windows]- of [Linux][docker-linux]-systeem.

## <a name="why-use-a-managed-identity"></a>Waarom een beheerde identiteit gebruiken?

Een beheerde identiteit voor Azure-resources biedt Azure-services een automatisch beheerde identiteit in Azure Active Directory (Azure AD). U [bepaalde Azure-resources](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), waaronder virtuele machines, configureren met een beheerde identiteit. Gebruik vervolgens de identiteit om toegang te krijgen tot andere Azure-bronnen, zonder referenties door te geven in code of scripts.

Beheerde identiteiten zijn van twee soorten:

* *Door de gebruiker toegewezen identiteiten*, die u aan meerdere resources toewijzen en zo lang als u wilt blijven bestaan. Door de gebruiker toegewezen identiteiten bevinden zich momenteel in een voorbeeld.

* Een *door het systeem beheerde identiteit*, die uniek is voor een specifieke bron zoals een enkele virtuele machine en de levensduur van die resource duurt.

Nadat u een Azure-bron met een beheerde identiteit hebt ingesteld, geeft u de identiteit de gewenste toegang tot een andere bron, net als elke beveiligingsprincipal. Wijs bijvoorbeeld een beheerde identiteit een rol toe met pull, push en pull of andere machtigingen aan een privéregister in Azure. (Zie [Azure Container Registry-rollen en machtigingen](container-registry-roles.md)voor een volledige lijst met registerrollen .) U een identiteit toegang geven tot een of meer bronnen.

Gebruik vervolgens de identiteit om te verifiëren voor elke [service die Azure AD-verificatie ondersteunt,](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)zonder referenties in uw code. Als u de identiteit wilt gebruiken om toegang te krijgen tot een Azure-containerregister vanaf een virtuele machine, verifieert u met Azure Resource Manager. Kies hoe u verifieert met de beheerde identiteit, afhankelijk van uw scenario:

* [Een Azure AD-toegangstoken](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programmatisch aanschaffen met HTTP- of REST-aanroepen

* De [Azure SDK's gebruiken](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Meld u aan bij Azure CLI of PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) met de identiteit. 

## <a name="create-a-container-registry"></a>Een containerregister maken

Als u nog geen Azure-containerregister hebt, maakt u een register en duwt u een voorbeeldcontainerafbeelding ernaar. Zie [Snelstart: Een privécontainerregister maken met azure cli](container-registry-get-started-azure-cli.md)voor stappen.

In dit artikel wordt `aci-helloworld:v1` ervan uitgegaan dat de containerafbeelding is opgeslagen in uw register. De voorbeelden gebruiken een registernaam van *myContainerRegistry*. Vervang in latere stappen door uw eigen register en afbeeldingsnamen.

## <a name="create-a-docker-enabled-vm"></a>Een VM met Docker maken

Maak een Docker-enabled Ubuntu virtuele machine. U moet ook de [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) op de virtuele machine installeren. Als u al een virtuele Azure-machine hebt, slaat u deze stap over om de virtuele machine te maken.

Implementeer een standaard Ubuntu Azure virtuele machine met [az vm maken][az-vm-create]. In het volgende voorbeeld wordt een VM met de naam *myDockerVM* in een bestaande resourcegroep met de naam *myResourceGroup gemaakt:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt. Wanneer de opdracht is voltooid, `publicIpAddress` neemt u nota van de weergave door de Azure CLI. Gebruik dit adres om SSH-verbindingen met de VM te maken.

### <a name="install-docker-on-the-vm"></a>Docker installeren op de VM

Nadat de VM is uitgevoerd, maakt u een SSH-verbinding met de VM. Vervang *publicIpAddress* door het openbare IP-adres van uw vm.

```bash
ssh azureuser@publicIpAddress
```

Voer de volgende opdracht uit om Docker op de VM te installeren:

```bash
sudo apt install docker.io -y
```

Voer na de installatie de volgende opdracht uit om te controleren of Docker correct op de VM wordt uitgevoerd:

```bash
sudo docker run -it hello-world
```

Uitvoer:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure-CLI installeren

Volg de stappen in [Azure CLI installeren met de toepasselijke inteom](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) de Azure CLI op uw virtuele Ubuntu-machine te installeren. Zorg ervoor dat u versie 2.0.55 of hoger installeert voor dit artikel.

Sluit de SSH-sessie af.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Voorbeeld 1: Toegang met een door de gebruiker toegewezen identiteit

### <a name="create-an-identity"></a>Een identiteit maken

Maak een identiteit in uw abonnement met behulp van de opdracht [AZ-identiteit maken.](/cli/azure/identity?view=azure-cli-latest#az-identity-create) U dezelfde resourcegroep gebruiken die u eerder hebt gebruikt om het containerregister of de virtuele machine of een andere te maken.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Als u de identiteit in de volgende stappen wilt configureren, gebruikt u de opdracht [AZ-identiteitsshow][az-identity-show] om de bron-id en servicehoofd-id van de identiteit in variabelen op te slaan.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Omdat u de id van de identiteit in een latere stap nodig hebt wanneer u zich vanaf uw virtuele machine aanmeldt bij de CLI, toont u de waarde:

```bash
echo $userID
```

De ID is van het formulier:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>De VM configureren met de identiteit

Met de volgende opdracht az [vm-identiteittoewijzing][az-vm-identity-assign] configureert u de Docker VM met de door de gebruiker toegewezen identiteit:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Identiteitstoegang verlenen tot het containerregister

Configureer nu de identiteit om toegang te krijgen tot uw containerregister. Gebruik eerst de [opdracht az acr show][az-acr-show] om de resource-ID van het register te krijgen:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Gebruik de opdracht [Voor het maken van az-roltoewijzing][az-role-assignment-create] om de AcrPull-rol aan het register toe te wijzen. Deze rol biedt [pull-machtigingen](container-registry-roles.md) voor het register. Als u zowel pull- als pushmachtigingen wilt geven, wijst u de Rol ACRPush toe.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>De identiteit gebruiken om toegang te krijgen tot het register

SSH in de Docker virtuele machine die is geconfigureerd met de identiteit. Voer de volgende Azure CLI-opdrachten uit met de Azure CLI die op de VM is geïnstalleerd.

Verifieer eerst naar de Azure CLI met [az-login,][az-login]met behulp van de identiteit die u op de VM hebt geconfigureerd. Vervang `<userID>`bij de wijziging de id van de identiteit die u in een vorige stap hebt opgehaald. 

```azurecli
az login --identity --username <userID>
```

Vervolgens, authenticeren naar het register met [az acr login][az-acr-login]. Wanneer u deze opdracht gebruikt, gebruikt de CLI `az login` het Active Directory-token dat is gemaakt wanneer u bent uitgevoerd om uw sessie naadloos te verifiëren met het containerregister. (Afhankelijk van de installatie van uw vm moet u deze opdracht- `sudo`en dockeropdrachten mogelijk uitvoeren met .)

```azurecli
az acr login --name myContainerRegistry
```

Je zou `Login succeeded` een bericht moeten zien. U `docker` vervolgens opdrachten uitvoeren zonder referenties op te geven. Voer bijvoorbeeld [docker pull][docker-pull] uit `aci-helloworld:v1` om de afbeelding op te trekken, waarbij de naam van de inlogserver van uw register wordt opgegeven. De naam van de inlogserver bestaat uit de `.azurecr.io` naam van `mycontainerregistry.azurecr.io`uw containerregister (alle kleine letters) gevolgd door - bijvoorbeeld .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Voorbeeld 2: Toegang met een door het systeem toegewezen identiteit

### <a name="configure-the-vm-with-a-system-managed-identity"></a>De VM configureren met een door het systeem beheerde identiteit

Met de volgende opdracht [az vm-identiteittoewijzing][az-vm-identity-assign] configureert u de Docker VM met een door het systeem toegewezen identiteit:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Gebruik de opdracht [az vm show][az-vm-show] om `principalId` een variabele in te stellen op de waarde van (de service principal ID) van de identiteit van de VM, om in latere stappen te gebruiken.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Identiteitstoegang verlenen tot het containerregister

Configureer nu de identiteit om toegang te krijgen tot uw containerregister. Gebruik eerst de [opdracht az acr show][az-acr-show] om de resource-ID van het register te krijgen:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Gebruik de opdracht [Voor het maken van az-roltoewijzing][az-role-assignment-create] om de AcrPull-rol aan de identiteit toe te wijzen. Deze rol biedt [pull-machtigingen](container-registry-roles.md) voor het register. Als u zowel pull- als pushmachtigingen wilt geven, wijst u de Rol ACRPush toe.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>De identiteit gebruiken om toegang te krijgen tot het register

SSH in de Docker virtuele machine die is geconfigureerd met de identiteit. Voer de volgende Azure CLI-opdrachten uit met de Azure CLI die op de VM is geïnstalleerd.

Verifieer eerst de Azure CLI met [az-login][az-login], met behulp van de door het systeem toegewezen identiteit op de VM.

```azurecli
az login --identity
```

Vervolgens, authenticeren naar het register met [az acr login][az-acr-login]. Wanneer u deze opdracht gebruikt, gebruikt de CLI `az login` het Active Directory-token dat is gemaakt wanneer u bent uitgevoerd om uw sessie naadloos te verifiëren met het containerregister. (Afhankelijk van de installatie van uw vm moet u deze opdracht- `sudo`en dockeropdrachten mogelijk uitvoeren met .)

```azurecli
az acr login --name myContainerRegistry
```

Je zou `Login succeeded` een bericht moeten zien. U `docker` vervolgens opdrachten uitvoeren zonder referenties op te geven. Voer bijvoorbeeld [docker pull][docker-pull] uit `aci-helloworld:v1` om de afbeelding op te trekken, waarbij de naam van de inlogserver van uw register wordt opgegeven. De naam van de inlogserver bestaat uit de `.azurecr.io` naam van `mycontainerregistry.azurecr.io`uw containerregister (alle kleine letters) gevolgd door - bijvoorbeeld .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over het gebruik van beheerde identiteiten met Azure Container Registry en hoe u:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen of door het systeem toegewezen identiteit inschakelen in een Azure VM
> * De identiteit toegang verlenen tot een Azure-containerregister
> * De beheerde identiteit gebruiken om toegang te krijgen tot het register en een containerafbeelding op te halen

* Meer informatie over [beheerde identiteiten voor Azure-bronnen](/azure/active-directory/managed-identities-azure-resources/).


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
