---
title: Helm-grafieken opslaan
description: Meer informatie over het opslaan van helm-grafieken voor uw Kubernetes-toepassingen met behulp van opslag plaatsen in Azure Container Registry
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 04ba3aaf312188ab77c04a97ab960cf9b9af078f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82857606"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Helm-grafieken pushen en pullen naar een Azure container Registry

Als u snel toepassingen wilt beheren en implementeren voor Kubernetes, kunt u het [open-source helm-pakket beheer][helm]gebruiken. Met helm worden toepassings pakketten gedefinieerd als [grafieken](https://helm.sh/docs/topics/charts/), die worden verzameld en opgeslagen in een [helm-grafiek opslagplaats](https://helm.sh/docs/topics/chart_repository/).

Dit artikel laat u zien hoe u helm-grafieken kunt hosten in een Azure container Registry met behulp van een installatie van helm 3 of helm 2. In veel scenario's bouwt en uploadt u uw eigen grafieken voor de toepassingen die u ontwikkelt. Zie de [hand leiding voor de gids voor grafiek sjablonen][develop-helm-charts]voor meer informatie over het bouwen van uw eigen helm-grafieken. U kunt ook een bestaand helm-diagram opslaan vanuit een andere helm-opslag plaats.

> [!IMPORTANT]
> Ondersteuning voor helm-grafieken in Azure Container Registry is momenteel beschikbaar als preview-versie. Previews worden aan u beschikbaar gesteld op voor waarde dat u akkoord gaat met de aanvullende [gebruiks voorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="helm-3-or-helm-2"></a>Helm 3 of helm 2?

Als u helm-grafieken wilt opslaan, beheren en installeren, gebruikt u een helm-client en de helm CLI. Grote releases van de helm-client zijn onder andere helm 3 en helm 2. Helm 3 ondersteunt een nieuwe grafiek indeling en installeert niet langer het onderdeel Tiller aan server zijde. Raadpleeg de [Veelgestelde vragen](https://helm.sh/docs/faq/)over de versie voor meer informatie over de versie verschillen. Als u eerder helm 2-grafieken hebt geïmplementeerd, raadpleegt u [helm v2 migreren naar v3](https://helm.sh/docs/topics/v2_v3_migration/).

U kunt helm 3 of helm 2 gebruiken om helm-grafieken in Azure Container Registry te hosten, met werk stromen die specifiek zijn voor elke versie:

* [Helm 3 client](#use-the-helm-3-client) - `helm chart` opdrachten in de helm-CLI gebruiken om grafieken in uw REGI ster te beheren als [OCI-artefacten](container-registry-image-formats.md#oci-artifacts)
* [Helm 2-client](#use-the-helm-2-client) : gebruik [AZ ACR helm][az-acr-helm] -opdrachten in de Azure CLI om uw container register toe te voegen en te beheren als een helm-grafiek opslagplaats

### <a name="additional-information"></a>Aanvullende informatie

* Voor de meeste scenario's wordt u aangeraden de helm 3-werk stroom te gebruiken met systeem eigen `helm chart` opdrachten voor het beheren van grafieken als OCI-artefacten.
* Vanaf helm 3 kunnen [AZ ACR helm][az-acr-helm] -opdrachten worden ondersteund voor compatibiliteit met de helm 2-client en grafiek indeling. Toekomstige ontwikkeling van deze opdrachten is momenteel niet gepland. Raadpleeg de [product routekaart](https://github.com/Azure/acr/blob/master/docs/acr-roadmap.md#acr-helm-ga).
* Helm 2-grafieken kunnen niet worden weer gegeven of beheerd met behulp van de Azure Portal.

## <a name="use-the-helm-3-client"></a>De helm 3-client gebruiken

### <a name="prerequisites"></a>Vereisten

- **Een Azure container Registry** in uw Azure-abonnement. Maak, indien nodig, een REGI ster met de [Azure Portal](container-registry-get-started-portal.md) of de [Azure cli](container-registry-get-started-azure-cli.md).
- **Helm-client versie 3.1.0 of hoger** -Voer uit `helm version` om uw huidige versie te vinden. Zie Installing [helm][helm-install](Engelstalig) voor meer informatie over het installeren en upgraden van helm.
- **Een Kubernetes-cluster** waarop u een helm-grafiek wilt installeren. Maak indien nodig een [Azure Kubernetes service-cluster][aks-quickstart]. 
- **Azure CLI-versie 2.0.71 of hoger** : Voer uit `az --version` om de versie te vinden. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="high-level-workflow"></a>Werk stroom op hoog niveau

Met **helm 3** kunt u:

* Kan een of meer helm-opslag plaatsen in een Azure container Registry maken
* Sla helm 3-grafieken in een REGI ster op als [OCI-artefacten](container-registry-image-formats.md#oci-artifacts). Momenteel is helm 3-ondersteuning voor OCI *experimenteel*.
* Verificatie met het REGI ster met behulp van de `helm registry login` opdracht.
* `helm chart`Opdrachten in de helm CLI gebruiken om helm-grafieken in een REGI ster te pushen, te verzamelen en te beheren
* Gebruiken `helm install` voor het installeren van grafieken naar een Kubernetes-cluster vanuit een lokale opslagplaats cache.

Zie de volgende secties voor voor beelden.

### <a name="enable-oci-support"></a>OCI-ondersteuning inschakelen

Stel de volgende omgevings variabele in om OCI-ondersteuning in te scha kelen in de helm 3-client. Op dit moment is dit een experimenteel product. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Een voorbeeld grafiek maken

Maak een test diagram met behulp van de volgende opdrachten:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Als eenvoudig voor beeld wijzigt u de map in de `templates` map en verwijdert u eerst de inhoud.

```console
cd hello-world/templates
rm -rf *
```

Maak in de `templates` map een bestand `configmap.yaml` met de naam met de volgende inhoud:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Zie [aan](https://helm.sh/docs/chart_template_guide/getting_started/) de slag in de helm-documenten voor meer informatie over het maken en uitvoeren van dit voor beeld.

### <a name="save-chart-to-local-registry-cache"></a>Grafiek opslaan naar lokale register cache

Wijzig de map naar de submap `hello-world` . Vervolgens voert `helm chart save` u uit om een kopie van de grafiek lokaal op te slaan en maakt u ook een alias met de volledig gekwalificeerde naam van het REGI ster (alle kleine letters) en de doel opslagplaats en-tag. 

In het volgende voor beeld is de register naam *mycontainerregistry*, de doel-opslag plaats is *Hallo wereld*en de label van de doel grafiek is *v1*, maar vervangt waarden voor uw omgeving:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Voer uit `helm chart list` om te bevestigen dat u de grafieken in de lokale register cache hebt opgeslagen. De uitvoer ziet er ongeveer zo uit:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Verifiëren met het REGI ster

Voer de `helm registry login` opdracht in de helm 3 CLI uit om [met het REGI ster te verifiëren](container-registry-authentication.md) met behulp van de referenties die relevant zijn voor uw scenario.

Maak bijvoorbeeld een Azure Active Directory service- [principal met pull-en push-machtigingen](container-registry-auth-service-principal.md#create-a-service-principal) (AcrPush Role) naar het REGI ster. Geef vervolgens de referenties voor de Service-Principal op `helm registry login` . In het volgende voor beeld wordt het wacht woord door een omgevings variabele geleverd:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Push diagram voor Azure Container Registry

Voer de `helm chart push` opdracht in de helm 3 CLI uit om de grafiek te pushen naar de volledig gekwalificeerde doel opslagplaats:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Na een succes volle Push is de uitvoer vergelijkbaar met:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Grafieken weer geven in de opslag plaats

Net als bij afbeeldingen die zijn opgeslagen in een Azure container Registry kunt u [AZ ACR repository][az-acr-repository] -opdrachten gebruiken om de opslag plaatsen weer te geven die als host fungeren voor uw grafieken, en grafiek Tags en-manifesten. 

Voer bijvoorbeeld [AZ ACR repository show][az-acr-repository-show] uit om de eigenschappen te zien van de opslag plaats die u in de vorige stap hebt gemaakt:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

De uitvoer ziet er ongeveer zo uit:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Voer de opdracht [AZ ACR repository show-manifests][az-acr-repository-show-manifests] uit om de details te bekijken van de grafiek die in de opslag plaats is opgeslagen. Bijvoorbeeld:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Output, afgekort in dit voor beeld toont een `configMediaType` van `application/vnd.cncf.helm.config.v1+json` :

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

### <a name="pull-chart-to-local-cache"></a>Een pull-grafiek naar een lokale cache

Als u een helm-grafiek wilt installeren op Kubernetes, moet de grafiek zich in de lokale cache bevinden. In dit voor beeld voert u eerst uit `helm chart remove` om de bestaande lokale grafiek te verwijderen met de naam `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Voer `helm chart pull` uit om de grafiek te downloaden van het Azure container Registry naar uw lokale cache:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Helm-grafiek exporteren

Als u verder wilt werken met de grafiek, exporteert u deze naar een lokale map met behulp van `helm chart export` . Exporteer bijvoorbeeld het diagram dat u naar de map hebt opgehaald `install` :

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Als u informatie wilt weer geven voor de geëxporteerde grafiek in de opslag plaats, voert `helm show chart` u de opdracht uit in de map waarnaar u de grafiek hebt geëxporteerd.

```console
cd install
helm show chart hello-world
```

Helm retourneert gedetailleerde informatie over de nieuwste versie van uw grafiek, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>Helm-grafiek installeren

Voer uit `helm install` om de helm-grafiek te installeren die u hebt opgehaald naar de lokale cache en hebt geëxporteerd. Geef een release naam op zoals *myhelmtest*of voer de `--generate-name` para meter door. Bijvoorbeeld:

```console
helm install myhelmtest ./hello-world
```

Uitvoer nadat de grafiek is geïnstalleerd, is vergelijkbaar met:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Voer de opdracht uit om de installatie te controleren `helm get manifest` . De opdracht retourneert de YAML-gegevens in het `configmap.yaml` sjabloon bestand.

Uitvoeren `helm uninstall` om de grafiek release in het cluster te verwijderen:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Een helm-grafiek verwijderen uit de opslag plaats

Als u een grafiek uit de opslag plaats wilt verwijderen, gebruikt u de opdracht [AZ ACR repository delete][az-acr-repository-delete] . Voer de volgende opdracht uit en bevestig de bewerking wanneer u hierom wordt gevraagd:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>De helm 2-client gebruiken

### <a name="prerequisites"></a>Vereisten

- **Een Azure container Registry** in uw Azure-abonnement. Maak, indien nodig, een REGI ster met de [Azure Portal](container-registry-get-started-portal.md) of de [Azure cli](container-registry-get-started-azure-cli.md).
- **Helm client versie 2.11.0 (geen RC-versie) of later** -Voer uit `helm version` om uw huidige versie te vinden. U hebt ook een helm-server (Tiller) nodig die is geïnitialiseerd in een Kubernetes-cluster. Maak indien nodig een [Azure Kubernetes service-cluster][aks-quickstart]. Zie Installing [helm][helm-install-v2](Engelstalig) voor meer informatie over het installeren en upgraden van helm.
- **Azure CLI-versie 2.0.46 of hoger** : Voer uit `az --version` om de versie te vinden. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="high-level-workflow"></a>Werk stroom op hoog niveau

Met **helm 2** kunt u:

* Configureer uw Azure container Registry als *één* helm-opslag plaats. Azure Container Registry beheert de definitie van de index wanneer u grafieken aan de opslag plaats toevoegt en verwijdert.
* Verifieer met uw Azure container Registry via de Azure CLI, waarna uw helm-client automatisch wordt bijgewerkt met de register-URI en referenties. U hoeft deze register gegevens niet hand matig op te geven, zodat de referenties niet worden weer gegeven in de opdracht geschiedenis.
* Gebruik de opdracht [AZ ACR helm][az-acr-helm] in de Azure CLI om uw Azure container Registry toe te voegen als een helm-grafiek opslagplaats en om grafieken te pushen en te beheren. Deze Azure CLI-opdrachten teruglopen helm 2-client opdrachten.
* Voeg de grafiek opslagplaats in uw Azure container Registry toe aan uw lokale helm opslag plaats-index, zodat de zoek functie voor grafieken wordt ondersteund.
* Gebruiken `helm install` voor het installeren van grafieken naar een Kubernetes-cluster vanuit een lokale opslagplaats cache.

Zie de volgende secties voor voor beelden.

### <a name="add-repository-to-helm-client"></a>Opslag plaats toevoegen aan helm-client

Voeg uw Azure Container Registry helm-grafiek opslagplaats toe aan uw helm-client met behulp van de opdracht [AZ ACR helm opslag plaats add][az-acr-helm-repo-add] . Met deze opdracht wordt een verificatie token opgehaald voor uw Azure container Registry dat door de helm-client wordt gebruikt. Het verificatie token is 3 uur geldig. Vergelijkbaar met `docker login` kunt u deze opdracht uitvoeren in toekomstige cli-sessies om uw helm-client te verifiëren met uw Azure container Registry helm-grafiek opslagplaats:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Een voorbeeld grafiek toevoegen aan de opslag plaats

Maak eerst een lokale map op *~/ACR-helm*en down load vervolgens de bestaande *stabiele/WordPress-* grafiek:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Typ `ls` om de gedownloade grafiek weer te geven en noteer de WordPress-versie die is opgenomen in de bestands naam. De `helm fetch stable/wordpress` opdracht heeft geen specifieke versie opgegeven, waardoor de *meest recente* versie is opgehaald. In de volgende voorbeeld uitvoer is het WordPress-diagram versie *8.1.0*:

```output
wordpress-8.1.0.tgz
```

Push het diagram naar uw helm-grafiek opslagplaats in Azure Container Registry met behulp van de opdracht [AZ ACR helm push][az-acr-helm-push] in de Azure cli. Geef de naam op van uw helm-grafiek die in de vorige stap is gedownload, zoals *WordPress-8.1.0. tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Na enkele ogen blikken rapporteert de Azure CLI dat uw grafiek is opgeslagen, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Grafieken weer geven in de opslag plaats

Als u de grafiek wilt gebruiken die in de vorige stap is geüpload, moet de lokale helm-opslagplaats index worden bijgewerkt. U kunt de opslag plaatsen in de helm-client opnieuw indexeren of de Azure CLI gebruiken om de opslag plaats-index bij te werken. Telkens wanneer u een grafiek aan uw opslag plaats toevoegt, moet deze stap worden voltooid:

```azurecli
az acr helm repo add --name mycontainerregistry
```

Als er een grafiek is opgeslagen in uw opslag plaats en de bijgewerkte index lokaal beschikbaar is, kunt u de reguliere helm-client opdrachten gebruiken om te zoeken of te installeren. Als u alle grafieken in uw opslag plaats wilt weer geven, gebruikt u de `helm search` opdracht om uw eigen Azure container Registry naam op te geven:

```console
helm search mycontainerregistry
```

Het WordPress-diagram dat in de vorige stap is gepusht, wordt weer gegeven, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

U kunt de grafieken ook weer geven met de Azure CLI met behulp van [AZ ACR helm List][az-acr-helm-list]:

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Informatie voor een helm-grafiek weer geven

Als u informatie wilt weer geven voor een specifieke grafiek in de opslag plaats, kunt u de `helm inspect` opdracht gebruiken.

```console
helm inspect mycontainerregistry/wordpress
```

Wanneer er geen versie nummer wordt gegeven, wordt de *meest recente* versie gebruikt. Helm retourneert gedetailleerde informatie over uw grafiek, zoals wordt weer gegeven in de volgende verkorte voorbeeld uitvoer:

```output
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
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
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

U kunt ook de informatie voor een grafiek weer geven met de Azure CLI [AZ ACR helm show][az-acr-helm-show] -opdracht. De *meest recente* versie van een grafiek wordt standaard geretourneerd. U kunt toevoegen `--version` om een specifieke versie van een grafiek weer te geven, zoals *8.1.0*:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Een helm-grafiek installeren vanuit de opslag plaats

Het helm-diagram in uw opslag plaats wordt geïnstalleerd door de naam van de opslag plaats en de naam van de grafiek op te geven. Gebruik de helm-client om het WordPress-diagram te installeren:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Als u naar uw Azure Container Registry helm-grafiek opslagplaats pusht en later terugkeert in een nieuwe CLI-sessie, moet uw lokale helm-client een bijgewerkt verificatie token hebben. Als u een nieuw verificatie token wilt verkrijgen, gebruikt u de opdracht [AZ ACR helm opslag plaats add][az-acr-helm-repo-add] .

De volgende stappen zijn voltooid tijdens het installatie proces:

- De helm-client zoekt in de index van de lokale opslag plaats.
- De bijbehorende grafiek wordt gedownload uit de Azure Container Registry opslag plaats.
- De grafiek wordt geïmplementeerd met behulp van de Tiller in uw Kubernetes-cluster.

Als de installatie wordt uitgevoerd, volgt u de instructies in de uitvoer van de opdracht om de WorPress-Url's en-referenties weer te geven. U kunt ook de `kubectl get pods` opdracht uitvoeren om de Kubernetes-resources te zien die zijn geïmplementeerd via de helm-grafiek:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Een helm-grafiek verwijderen uit de opslag plaats

Als u een grafiek uit de opslag plaats wilt verwijderen, gebruikt u de opdracht [AZ ACR helm delete][az-acr-helm-delete] . Geef de naam op van de grafiek, zoals *WordPress*, en de versie die u wilt verwijderen, zoals *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Als u alle versies van het genoemde diagram wilt verwijderen, houdt u de `--version` para meter weg.

De grafiek wordt nog steeds geretourneerd wanneer u uitvoert `helm search` . De helm-client werkt de lijst met beschik bare grafieken in een opslag plaats niet automatisch bij. Als u de helm-client opslag plaats-index wilt bijwerken, gebruikt u de opdracht [AZ ACR helm opslag plaats add][az-acr-helm-repo-add] opnieuw:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Volgende stappen

* Zie [helm-grafieken ontwikkelen][develop-helm-charts]voor meer informatie over het maken en implementeren van helm-grafieken.
* Meer informatie over het installeren van toepassingen met helm in [Azure Kubernetes service (AKS)](../aks/kubernetes-helm.md).
* Helm-grafieken kunnen worden gebruikt als onderdeel van het bouw proces van de container. Zie [Azure container Registry-taken gebruiken][acr-tasks]voor meer informatie.

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
