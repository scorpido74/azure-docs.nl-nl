---
title: Registerstatus controleren
description: Meer informatie over het uitvoeren van een snelle diagnostische opdracht om veelvoorkomende problemen te identificeren bij het gebruik van een Azure-containerregister, inclusief lokale Docker-configuratie en connectiviteit met het register
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: ea4432c9e92c4a0380517e39678814e2d1cb3bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456409"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>De status van een Azure-containerregister controleren

Wanneer u een Azure-containerregister gebruikt, u af en toe problemen ondervinden. U bijvoorbeeld geen containerafbeelding opvragen vanwege een probleem met Docker in uw lokale omgeving. Of een netwerkprobleem kan voorkomen dat u verbinding maakt met het register. 

Voer als eerste diagnostische stap de opdracht [az acr check-health][az-acr-check-health] uit om informatie te krijgen over de gezondheid van de omgeving en optioneel toegang te krijgen tot een doelregister. Deze opdracht is beschikbaar in Azure CLI-versie 2.0.67 of hoger. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli].

## <a name="run-az-acr-check-health"></a>Run az acr check-health

In de volgende voorbeelden worden `az acr check-health` verschillende manieren weergegeven om de opdracht uit te voeren.

> [!NOTE]
> Als u de opdracht uitvoert in Azure Cloud Shell, wordt de lokale omgeving niet gecontroleerd. U echter de toegang tot een doelregister controleren.

### <a name="check-the-environment-only"></a>Controleer alleen de omgeving

Als u de lokale Docker-daemon-, CLI-versie en Helm-clientconfiguratie wilt controleren, voert u de opdracht uit zonder extra parameters:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Controleer de omgeving en een doelregister

Als u de toegang tot een register wilt controleren en lokale omgevingscontroles wilt uitvoeren, geeft u de naam van een doelregister door. Bijvoorbeeld:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Foutrapportage

De opdracht registreert informatie aan de standaarduitvoer. Als er een probleem wordt gedetecteerd, bevat het een foutcode en -beschrijving. Zie de [foutverwijzing](container-registry-health-error-reference.md)voor meer informatie over de codes en mogelijke oplossingen.

Standaard stopt de opdracht wanneer er een fout wordt gevonden. U de opdracht ook uitvoeren, zodat deze uitvoer biedt voor alle statuscontroles, zelfs als er fouten worden gevonden. Voeg `--ignore-errors` de parameter toe, zoals in de volgende voorbeelden wordt weergegeven:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Voorbeelduitvoer:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Volgende stappen

Zie de [foutverwijzing statuscontrole](container-registry-health-error-reference.md)statuscontrole voor meer informatie over foutcodes die zijn geretourneerd door de opdracht [az acr check-health.][az-acr-check-health]

Zie de [veelgestelde vragen](container-registry-faq.md) voor veelgestelde vragen en andere bekende problemen over Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
