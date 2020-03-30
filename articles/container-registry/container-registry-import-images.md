---
title: Containerinstallatiekopieën importeren
description: Containerafbeeldingen importeren in een Azure-containerregister met Azure API's, zonder dat dockeropdrachten hoeven uit te voeren.
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: caf7a47ac8f7ff0e72d2e049a7013542d274a225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051927"
---
# <a name="import-container-images-to-a-container-registry"></a>Containerafbeeldingen importeren in een containerregister

U containerafbeeldingen eenvoudig importeren (kopiëren) naar een Azure-containerregister, zonder docker-opdrachten te gebruiken. Importeer bijvoorbeeld afbeeldingen uit een ontwikkelingsregister naar een productieregister of kopieer basisafbeeldingen uit een openbaar register.

Azure Container Registry verwerkt een aantal veelvoorkomende scenario's om afbeeldingen uit een bestaand register te kopiëren:

* Importeren uit een openbaar register

* Importeren uit een ander Azure-containerregister, in hetzelfde of een ander Azure-abonnement

* Importeren uit een niet-Azure-privécontainerregister

Het importeren van afbeeldingen in een Azure-containerregister heeft de volgende voordelen ten opzichte van het gebruik van Docker CLI-opdrachten:

* Omdat uw clientomgeving geen lokale Docker-installatie nodig heeft, importeert u een containerafbeelding, ongeacht het ondersteunde besturingssysteemtype.

* Wanneer u afbeeldingen met meerdere architectuur importeert (zoals officiële Docker-afbeeldingen), worden afbeeldingen voor alle architecturen en platforms die in de manifestlijst zijn opgegeven, gekopieerd.

Als u containerafbeeldingen wilt importeren, moet u in dit artikel de Azure CLI uitvoeren in Azure Cloud Shell of lokaal (versie 2.0.55 of hoger aanbevolen). Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli].

> [!NOTE]
> Als u identieke containerafbeeldingen over meerdere Azure-regio's moet distribueren, ondersteunt Azure Container Registry ook [georeplicatie.](container-registry-geo-replication.md) Door een register te geo-repliceren (Premium-servicelaag vereist), u meerdere regio's met identieke afbeeldings- en tagnamen uit één register weergeven.
>

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-containerregister hebt, maakt u een register. Zie [Snelstart: Een privécontainerregister maken met azure cli](container-registry-get-started-azure-cli.md)voor stappen.

Als u een afbeelding wilt importeren in een Azure-containerregister, moet uw identiteit schrijfmachtigingen hebben voor het doelregister (ten minste de rol van inzender). Zie [rollen en machtigingen voor Azure Container Registry](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Importeren uit een openbaar register

### <a name="import-from-docker-hub"></a>Importeren vanuit Docker Hub

Gebruik bijvoorbeeld de opdracht [az acr-import][az-acr-import] om `hello-world:latest` de afbeelding met meerdere architectuur van Docker Hub te importeren in een register met de naam *myregistry.* Omdat `hello-world` het een officiële afbeelding van Docker Hub `library` is, bevindt deze afbeelding zich in de standaardrepository. Voeg de naam van de opslagplaats en eventueel `--source` een tag toe in de waarde van de afbeeldingsparameter. (U optioneel een afbeelding identificeren aan de doorsnede ervan in plaats van op tag, wat een bepaalde versie van een afbeelding garandeert.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

U controleren of aan deze afbeelding meerdere `az acr repository show-manifests` manifesten zijn gekoppeld door de opdracht uit te voeren:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

In het volgende voorbeeld wordt `tensorflow` een openbare afbeelding uit de opslagplaats in Docker Hub geïmporteerd:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importeren uit Microsoft Container Registry

Importeer bijvoorbeeld de nieuwste Windows Server `windows` Core-afbeelding uit de opslagplaats in Microsoft Container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:latest \
--image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importeren uit een ander Azure-containerregister

U een afbeelding importeren uit een ander Azure-containerregister met geïntegreerde Azure Active Directory-machtigingen.

* Uw identiteit moet azure Active Directory-machtigingen hebben om uit het bronregister (leesfunctie) te lezen en te schrijven naar het doelregister (rol inzender).

* Het register kan zich in dezelfde of een ander Azure-abonnement in dezelfde Active Directory-tenant bevinden.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importeren uit een register in hetzelfde abonnement

Importeer de `aci-helloworld:latest` afbeelding bijvoorbeeld uit een bronregister *mysourceregistry* naar *myregistry* in hetzelfde Azure-abonnement.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

In het volgende voorbeeld wordt een afbeelding geïmporteerd op manifestdigest `sha256:...`(SHA-256 hash, weergegeven als ) in plaats van op tag:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importeren uit een register in een ander abonnement

In het volgende voorbeeld bevindt *mysourceregistry* zich in een ander abonnement dan *myregistry* in dezelfde Active Directory-tenant. Lever de resource-id van `--registry` het bronregister met de parameter. Merk op `--source` dat de parameter alleen de bronopslagplaats en -tag opgeeft, niet de naam van de registerinlogserver.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importeren uit een register met behulp van hoofdreferenties van de service

Als u wilt importeren uit een register dat u niet openen met Active Directory-machtigingen, u serviceprincipal-referenties gebruiken (indien beschikbaar). Geef de appID en het wachtwoord van een Active [Directory-serviceprincipal](container-registry-auth-service-principal.md) met ACRPull-toegang tot het bronregister. Het gebruik van een serviceprincipal is handig voor het bouwen van systemen en andere onbeheerde systemen die afbeeldingen in uw register moeten importeren.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importeren uit een niet-Azure-privécontainerregister

Importeer een afbeelding uit een privéregister door referenties op te geven waarmee u toegang tot het register ophalen. Trek bijvoorbeeld een afbeelding uit een privé Docker-register: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over het importeren van containerafbeeldingen naar een Azure-containerregister vanuit een openbaar register of een ander privéregister. Zie de opdrachtreferentie [az acr import][az-acr-import] voor extra opties voor het importeren van afbeeldingen. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
