---
title: Containerinstallatiekopieën importeren
description: Container installatie kopieën importeren in een Azure container Registry met behulp van Azure Api's, zonder dat u docker-opdrachten hoeft uit te voeren.
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: e649447d7b9280dbebef1ae332c1f25910f5a516
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456303"
---
# <a name="import-container-images-to-a-container-registry"></a>Container installatie kopieën importeren in een container register

U kunt container installatie kopieën eenvoudig importeren (kopiëren) naar een Azure container Registry zonder gebruik te maken van docker-opdrachten. Importeer bijvoorbeeld installatie kopieën van een ontwikkelings register naar een productie register of kopieer basis installatie kopieën uit een openbaar REGI ster.

Azure Container Registry verwerkt een aantal algemene scenario's voor het kopiëren van installatie kopieën uit een bestaand REGI ster:

* Importeren uit een openbaar REGI ster

* Importeren vanuit een ander Azure container registry in hetzelfde of een ander Azure-abonnement

* Importeren uit een persoonlijk niet-Azure-container register

Het importeren van afbeeldingen in een Azure container Registry heeft de volgende voor delen ten opzichte van het gebruik van docker CLI-opdrachten:

* Omdat uw client omgeving geen lokale docker-installatie nodig heeft, importeert u een container installatie kopie, ongeacht het ondersteunde type besturings systeem.

* Bij het importeren van installatie kopieën met meerdere architecturen (zoals officiële docker-installatie kopieën), worden installatie kopieën voor alle architecturen en platformen die zijn opgegeven in de manifest lijst gekopieerd.

Als u container installatie kopieën wilt importeren, moet u de Azure CLI in Azure Cloud Shell of lokaal uitvoeren (versie 2.0.55 of hoger aanbevolen). Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!NOTE]
> Als u identieke container installatie kopieën wilt distribueren over meerdere Azure-regio's, ondersteunt Azure Container Registry ook [geo-replicatie](container-registry-geo-replication.md). Door Geo-replicatie van een REGI ster (Premium SKU vereist) kunt u meerdere regio's met identieke installatie kopieën en label namen uit één REGI ster gebruiken.
>

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure container Registry hebt, maakt u een REGI ster. Zie [Quick Start: een persoonlijk container register maken met behulp van de Azure cli](container-registry-get-started-azure-cli.md)voor stappen.

Als u een installatie kopie naar een Azure container Registry wilt importeren, moet uw identiteit schrijf machtigingen hebben voor het doel register (ten minste de rol van bijdrager). Zie [Azure container Registry-rollen en-machtigingen](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Importeren uit een openbaar REGI ster

### <a name="import-from-docker-hub"></a>Importeren uit docker hub

Gebruik bijvoorbeeld de opdracht [AZ ACR import][az-acr-import] om de multi-Architecture `hello-world:latest` installatie kopie van docker hub te importeren in een REGI ster met de naam *myregistry*. Omdat `hello-world` een officiële installatie kopie van docker hub is, bevindt deze installatie kopie zich in de standaard `library` opslag plaats. Neem de naam van de opslag plaats en eventueel een tag op in de waarde van de para meter `--source` image. (U kunt een installatie kopie eventueel identificeren met de samen vatting van het manifest in plaats van met een tag, waarmee een bepaalde versie van een installatie kopie wordt gegarandeerd.)
 
```azurecli
az acr import --name myregistry --source docker.io/library/hello-world:latest --image hello-world:latest
```

U kunt controleren of er meerdere manifesten zijn gekoppeld aan deze installatie kopie door de `az acr repository show-manifests` opdracht uit te voeren:

```azurecli
az acr repository show-manifests --name myregistry --repository hello-world
```

In het volgende voor beeld wordt een open bare installatie kopie uit de `tensorflow` opslag plaats in docker hub geïmporteerd:

```azurecli
az acr import --name myregistry --source docker.io/tensorflow/tensorflow:latest-gpu --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importeren uit micro soft Container Registry

U kunt bijvoorbeeld de meest recente installatie kopie van Windows Server importeren uit de `windows` opslag plaats in micro soft Container Registry.

```azurecli
az acr import --name myregistry --source mcr.microsoft.com/windows/servercore:latest --image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importeren vanuit een ander Azure container Registry

U kunt een installatie kopie vanuit een ander Azure container Registry importeren met behulp van geïntegreerde Azure Active Directory-machtigingen.

* Uw identiteit moet Azure Active Directory machtigingen hebben voor het lezen van het bron register (rol van lezer) en voor het schrijven naar het doel register (rol Inzender).

* Het REGI ster kan zich in hetzelfde of een ander Azure-abonnement bevindt als de Tenant van hetzelfde Active Directory.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importeren uit een REGI ster in hetzelfde abonnement

Importeer bijvoorbeeld de `aci-helloworld:latest`-installatie kopie vanuit een bron register *mysourceregistry* naar *Myregistry* in hetzelfde Azure-abonnement.

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld:latest --image hello-world:latest
```

In het volgende voor beeld wordt een installatie kopie geïmporteerd door de manifest Digest (SHA-256-Hash, weer gegeven als `sha256:...`) in plaats van tag:

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importeren uit een REGI ster in een ander abonnement

In het volgende voor beeld bevindt *mysourceregistry* zich in een ander abonnement dan *myregistry* in dezelfde Active Directory Tenant. Geef de bron-ID van het bron register op met de para meter `--registry`. U ziet dat de para meter `--source` alleen de bron opslagplaats en de naam van de installatie kopie opgeeft, niet de naam van de aanmeldings server van het REGI ster.
 
```azurecli
az acr import --name myregistry --source sourcerepo/aci-helloworld:latest --image aci-hello-world:latest --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importeren uit een REGI ster met de referenties van de Service-Principal

Als u wilt importeren uit een REGI ster waartoe u geen toegang hebt met behulp van Active Directory machtigingen, kunt u de referenties van de Service-Principal gebruiken (indien beschikbaar). Geef de appID en het wacht woord op van een Active Directory [Service-Principal](container-registry-auth-service-principal.md) die toegang heeft tot het bron register ACRPull. Het gebruik van een Service-Principal is handig voor het bouwen van systemen en andere systemen zonder toezicht die installatie kopieën naar uw REGI ster moeten importeren.

```azurecli
az acr import --name myregistry --source sourceregistry.azurecr.io/sourcerepo/sourceimage:tag --image targetimage:tag --username <SP_App_ID> –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importeren uit een persoonlijk niet-Azure-container register

Importeer een installatie kopie uit een persoonlijk REGI ster door referenties op te geven waarmee toegang tot het REGI ster wordt ingeschakeld. U kunt bijvoorbeeld een installatie kopie uit een privé-docker-REGI ster halen: 

```azurecli
az acr import --name myregistry --source docker.io/sourcerepo/sourceimage:tag --image sourceimage:tag --username <username> --password <password>
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over het importeren van container installatie kopieën naar een Azure container Registry vanuit een openbaar REGI ster of een ander privé register. Zie voor aanvullende opties voor het importeren van installatie kopieën de verwijzing [AZ ACR import][az-acr-import] Command. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
