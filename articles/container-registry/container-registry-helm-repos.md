---
title: Helm-grafieken opslaan
description: Meer informatie over het gebruik van een helm-opslag plaats met Azure Container Registry om grafieken voor uw toepassingen op te slaan
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 0c5e66d5f2fc3dd3c2d8c0a975c3e9d1c813732d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456340"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Azure Container Registry gebruiken als een helm-opslag plaats voor uw toepassings grafieken

Als u snel toepassingen wilt beheren en implementeren voor Kubernetes, kunt u het [open-source helm-pakket beheer][helm]gebruiken. Met helm worden toepassingen gedefinieerd als *grafieken* die zijn opgeslagen in een helm-grafiek opslagplaats. In deze grafieken worden configuraties en afhankelijkheden gedefinieerd, en kan de versie worden gedistribueerd gedurende de levens cyclus van de toepassing. Azure Container Registry kunnen als host worden gebruikt voor helm-grafiek opslagplaatsen.

Met Azure Container Registry hebt u een persoonlijke, beveiligde helm-grafiek opslagplaats die kan worden geïntegreerd met Build-pijp lijnen of andere Azure-Services. Helm-grafiek opslagplaatsen in Azure Container Registry functies van geo-replicatie bevatten om ervoor te zorgen dat uw grafieken dicht bij implementaties en voor redundantie worden bewaard. U betaalt alleen voor de opslag die wordt gebruikt door de grafieken en zijn beschikbaar in alle Azure Container Registry prijs categorieën.

In dit artikel leest u hoe u een helm-grafiek opslagplaats kunt gebruiken die is opgeslagen in Azure Container Registry.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functie worden mogelijk nog gewijzigd voordat de functie algemeen beschikbaar wordt.

## <a name="before-you-begin"></a>Voordat u begint

Als u de stappen in dit artikel wilt uitvoeren, moet aan de volgende vereisten worden voldaan:

- **Azure container Registry** : een container register maken in uw Azure-abonnement. Gebruik bijvoorbeeld de [Azure Portal](container-registry-get-started-portal.md) of de [Azure cli](container-registry-get-started-azure-cli.md).
- **Helm client versie 2.11.0 (geen RC-versie) of later** -Voer `helm version` uit om uw huidige versie te vinden. U hebt ook een helm-server (Tiller) nodig die is geïnitialiseerd in een Kubernetes-cluster. Als dat nodig is, kunt u [een Azure Kubernetes-service cluster maken][aks-quickstart]. Zie Installing [helm][helm-install](Engelstalig) voor meer informatie over het installeren en upgraden van helm.
- **Azure CLI-versie 2.0.46 of later** -Voer `az --version` uit om de versie te vinden. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="add-a-repository-to-helm-client"></a>Een opslag plaats toevoegen aan de helm-client

Een helm-opslag plaats is een HTTP-server die helm-grafieken kan opslaan. Azure Container Registry kunt deze opslag voor helm-grafieken opgeven en de index definitie beheren wanneer u grafieken toevoegt aan of verwijdert uit de opslag plaats.

Als u uw Azure Container Registry wilt toevoegen als een helm-grafiek opslagplaats, gebruikt u de Azure CLI. Met deze methode wordt uw helm-client bijgewerkt met de URI en referenties voor de opslag plaats die wordt ondersteund door Azure Container Registry. U hoeft deze opslagplaats gegevens niet hand matig op te geven, zodat de referenties niet worden weer gegeven in de opdracht geschiedenis.

Als dat nodig is, meldt u zich aan bij de Azure CLI en volgt u de prompts:

```azurecli
az login
```

Configureer de standaard instellingen van Azure CLI met de naam van uw Azure Container Registry met behulp van de opdracht [AZ configure][az-configure] . Vervang `<acrName>` in het volgende voor beeld door de naam van uw REGI ster:

```azurecli
az configure --defaults acr=<acrName>
```

Voeg nu uw Azure Container Registry helm-grafiek opslagplaats toe aan uw helm-client met behulp van de opdracht [AZ ACR helm opslag plaats add][az-acr-helm-repo-add] . Met deze opdracht wordt een verificatie token opgehaald voor uw Azure container Registry dat door de helm-client wordt gebruikt. Het verificatie token is 1 uur geldig. Net als bij `docker login`, kunt u deze opdracht uitvoeren in toekomstige CLI-sessies om uw helm-client te verifiëren met uw Azure Container Registry helm-grafiek opslagplaats:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Een grafiek toevoegen aan de opslag plaats

Voor dit artikel halen we een bestaand helm-diagram van de open bare helm *stabiele* opslag plaats. De *stabiele* opslag plaats is een gecuratore, open bare opslag plaats die algemene toepassings grafieken bevat. Pakket maintainers kunnen hun grafieken verzenden naar de *stabiele* opslag plaats, op dezelfde manier als docker hub een openbaar REGI ster voor algemene container installatie kopieën biedt. De grafiek die is gedownload van de open bare *stabiele* opslag plaats kan vervolgens worden gepusht naar uw persoonlijke Azure container Registry-opslag plaats. In de meeste scenario's bouwt en uploadt u uw eigen grafieken voor de toepassingen die u ontwikkelt. Zie [helm-grafieken ontwikkelen][develop-helm-charts]voor meer informatie over het bouwen van uw eigen helm-grafieken.

Maak eerst een directory op *~/ACR-helm*en down load vervolgens de bestaande *stabiele/WordPress-* grafiek:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Geef de gedownloade grafiek weer en noteer de WordPress-versie die is opgenomen in de bestands naam. De `helm fetch stable/wordpress`-opdracht heeft geen bepaalde versie opgegeven, waardoor de *meest recente* versie is opgehaald. Alle helm-grafieken bevatten een versie nummer in de bestands naam die volgt op de [SemVer 2][semver2] -standaard. In de volgende voorbeeld uitvoer is het WordPress-diagram versie *2.1.10*:

```
$ ls

wordpress-2.1.10.tgz
```

Push het diagram nu naar uw helm-grafiek opslagplaats in Azure Container Registry met behulp van de Azure CLI [AZ ACR helm push][az-acr-helm-push] opdracht. Geef de naam op van uw helm-grafiek die in de vorige stap is gedownload, zoals *WordPress-2.1.10. tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Na enkele ogen blikken rapporteert de Azure CLI dat uw grafiek is opgeslagen, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Grafieken weer geven in de opslag plaats

De helm-client onderhoudt een lokaal in cache opgeslagen kopie van de inhoud van externe opslag plaatsen. Wijzigingen in een externe opslag plaats worden de lijst met beschik bare grafieken die lokaal door de helm-client bekend zijn, niet automatisch bijgewerkt. Wanneer u zoekt naar grafieken in opslag plaatsen, gebruikt helm de lokale index in de cache. Als u de grafiek wilt gebruiken die in de vorige stap is geüpload, moet de lokale helm-opslagplaats index worden bijgewerkt. U kunt de opslag plaatsen in de helm-client opnieuw indexeren of de Azure CLI gebruiken om de opslag plaats-index bij te werken. Telkens wanneer u een grafiek aan uw opslag plaats toevoegt, moet deze stap worden voltooid:

```azurecli
az acr helm repo add
```

Als er een grafiek is opgeslagen in uw opslag plaats en de bijgewerkte index lokaal beschikbaar is, kunt u de reguliere helm-client opdrachten gebruiken om te zoeken of te installeren. Als u alle grafieken in uw opslag plaats wilt weer geven, gebruikt u `helm search <acrName>`. Geef uw eigen Azure Container Registry naam op:

```console
helm search <acrName>
```

Het WordPress-diagram dat in de vorige stap is gepusht, wordt weer gegeven, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

U kunt de grafieken ook weer geven met de Azure CLI met behulp van [AZ ACR helm List][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Informatie voor een helm-grafiek weer geven

Als u informatie wilt weer geven voor een specifieke grafiek in de opslag plaats, kunt u de gewone helm-client opnieuw gebruiken. Gebruik `helm inspect`om informatie weer te geven voor de grafiek met de naam *WordPress*.

```console
helm inspect <acrName>/wordpress
```

Wanneer er geen versie nummer wordt gegeven, wordt de *meest recente* versie gebruikt. Helm retourneert gedetailleerde informatie over uw grafiek, zoals wordt weer gegeven in de volgende verkorte voorbeeld uitvoer:

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

U kunt ook de informatie voor een grafiek weer geven met de Azure CLI [AZ ACR helm show][az-acr-helm-show] -opdracht. De *meest recente* versie van een grafiek wordt standaard geretourneerd. U kunt `--version` toevoegen om een specifieke versie van een grafiek weer te geven, zoals *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Een helm-grafiek installeren vanuit de opslag plaats

Het helm-diagram in uw opslag plaats wordt geïnstalleerd door de naam van de opslag plaats en de naam van de grafiek op te geven. Gebruik de helm-client om het WordPress-diagram te installeren:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Als u naar uw Azure Container Registry helm-grafiek opslagplaats pusht en later terugkeert in een nieuwe CLI-sessie, moet uw lokale helm-client een bijgewerkt verificatie token hebben. Als u een nieuw verificatie token wilt verkrijgen, gebruikt u de opdracht [AZ ACR helm opslag plaats add][az-acr-helm-repo-add] .

De volgende stappen zijn voltooid tijdens het installatie proces:

- De helm-client zoekt in de index van de lokale opslag plaats.
- De bijbehorende grafiek wordt gedownload uit de Azure Container Registry opslag plaats.
- De grafiek wordt geïmplementeerd met behulp van de Tiller in uw Kubernetes-cluster.

In de volgende gecomprimeerde voorbeeld uitvoer ziet u de Kubernetes-resources die zijn geïmplementeerd via de helm-grafiek:

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Een helm-grafiek verwijderen uit de opslag plaats

Als u een grafiek uit de opslag plaats wilt verwijderen, gebruikt u de opdracht [AZ ACR helm delete][az-acr-helm-delete] . Geef de naam op van de grafiek, zoals *WordPress*, en de versie die u wilt verwijderen, zoals *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Als u alle versies van het genoemde diagram wilt verwijderen, moet u de para meter `--version` weglaten.

De grafiek blijft in `helm search <acrName>`worden geretourneerd. De helm-client werkt de lijst met beschik bare grafieken in een opslag plaats niet automatisch bij. Als u de helm-client opslag plaats-index wilt bijwerken, gebruikt u de opdracht [AZ ACR helm opslag plaats add][az-acr-helm-repo-add] opnieuw:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt een bestaand helm-diagram gebruikt uit de open bare *stabiele* opslag plaats. Zie [helm-grafieken ontwikkelen][develop-helm-charts]voor meer informatie over het maken en implementeren van helm-grafieken.

Helm-grafieken kunnen worden gebruikt als onderdeel van het bouw proces van de container. Zie [Azure container Registry-taken gebruiken][acr-tasks]voor meer informatie.

Zie de [Best practices][acr-bestpractices]voor meer informatie over het gebruik en beheer van Azure container Registry.

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/topics/charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
