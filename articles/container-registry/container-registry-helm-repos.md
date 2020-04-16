---
title: Grafieken voor het beslaan van roerdiagrammen
description: Meer informatie over het opslaan van Helmdiagrammen voor uw Kubernetes-toepassingen met behulp van repositories in Azure Container Registry
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 3f1a68258b758380a66b63e3c3137f1d460d288c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399376"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Helmdiagrammen pushen en trekken naar een Azure-containerregister

Om applicaties voor Kubernetes snel te beheren en te implementeren, u de [open-source Helm package manager][helm]gebruiken. Met Helm worden toepassingspakketten gedefinieerd als [grafieken](https://helm.sh/docs/topics/charts/), die worden verzameld en opgeslagen in een [Helm-grafiekopslagplaats.](https://helm.sh/docs/topics/chart_repository/)

In dit artikel ziet u hoe u helmdiagrammenrepositories in een Azure-containerregister host, met behulp van een Helm 3- of Helm 2-installatie. In veel scenario's zou u uw eigen grafieken maken en uploaden voor de toepassingen die u ontwikkelt. Zie de [gids voor grafieken sjabloonontwikkelaar][develop-helm-charts]voor meer informatie over het maken van uw eigen Helm-diagrammen. U ook een bestaande Helm-grafiek opslaan vanuit een andere Helm-repo.

> [!IMPORTANT]
> Ondersteuning voor Helm-diagrammen in Azure Container Registry is momenteel in preview. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de aanvullende [gebruiksvoorwaarden.][terms-of-use] Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="helm-3-or-helm-2"></a>Roer 3 of Helm 2?

Om Helm-diagrammen op te slaan, te beheren en te installeren, gebruikt u een Helm-client en de Helm CLI. Belangrijke releases van de Helm klant zijn Helm 3 en Helm 2. Helm 3 ondersteunt een nieuwe grafiekindeling en installeert niet langer de kant van de Tiller-server. Zie de [versie FAQ](https://helm.sh/docs/faq/)voor meer informatie over de versieverschillen. Zie Helm v2 migreren [naar v3](https://helm.sh/docs/topics/v2_v3_migration/)als u eerder Helm 2-diagrammen hebt geïmplementeerd.

U Helm 3 of Helm 2 gebruiken om Helm-diagrammen te hosten in Azure Container Registry, met werkstromen die specifiek zijn voor elke versie:

* [Helm 3](#use-the-helm-3-client) client `helm chart` - gebruik opdrachten in de Helm CLI om grafieken in uw register te beheren als [OCI-artefacten](container-registry-image-formats.md#oci-artifacts)
* [Helm 2-client](#use-the-helm-2-client) - gebruik [az acr helm-opdrachten][az-acr-helm] in de Azure CLI om uw containerregister toe te voegen en te beheren als een Helm-grafiekopslagplaats

### <a name="additional-information"></a>Aanvullende informatie

* Voor de meeste scenario's raden we `helm chart` aan om de Helm 3-werkstroom te gebruiken met native opdrachten om grafieken te beheren als OCI-artefacten.
* U verouderde [az acr helm][az-acr-helm] Azure CLI-opdrachten en -werkstroom gebruiken met de Helm 3-client en -grafieken. Bepaalde opdrachten `az acr helm list` zijn echter niet compatibel met Helm 3-diagrammen.
* Vanaf Helm 3 worden [de az acr helm][az-acr-helm] commando's voornamelijk ondersteund voor compatibiliteit met de Helm 2 client en grafiek formaat. Toekomstige ontwikkeling van deze commando's is momenteel niet gepland.

## <a name="use-the-helm-3-client"></a>Gebruik de Helm 3 client

### <a name="prerequisites"></a>Vereisten

- **Een Azure-containerregister** in uw Azure-abonnement. Maak indien nodig een register met de [Azure-portal](container-registry-get-started-portal.md) of de [Azure CLI.](container-registry-get-started-azure-cli.md)
- **Helm client versie 3.1.0** `helm version` of hoger - Voer uit om uw huidige versie te vinden. Zie Helm installeren voor meer informatie over het installeren en upgraden [van Helm.][helm-install]
- **Een Kubernetes-cluster** waar u een Helm-diagram installeert. Maak indien nodig een [Azure Kubernetes Service-cluster][aks-quickstart]. 
- **Azure CLI-versie 2.0.71** `az --version` of hoger - Uitvoeren om de versie te vinden. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

### <a name="high-level-workflow"></a>Workflow op hoog niveau

Met **Helm 3** jij:

* Kan een of meer Helm-opslagplaatsen maken in een Azure-containerregister
* Sla Helm 3-diagrammen op in een register als [OCI-artefacten.](container-registry-image-formats.md#oci-artifacts) Momenteel is helm 3 ondersteuning voor OCI *experimenteel.*
* Verifieer met uw `helm registry login` register met behulp van de opdracht.
* Opdrachten `helm chart` in de Helm CLI gebruiken om Helmdiagrammen in een register te pushen, trekken en beheren
* Met `helm install` deze gegevens u grafieken installeren op een Kubernetes-cluster vanuit een lokale repository-cache.

Zie de volgende secties voor voorbeelden.

### <a name="enable-oci-support"></a>OCI-ondersteuning inschakelen

Stel de volgende omgevingsvariabele in om OCI-ondersteuning in de Helm 3-client mogelijk te maken. Momenteel is deze ondersteuning experimenteel. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Een voorbeelddiagram maken

Maak een testdiagram met de volgende opdrachten:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Als basisvoorbeeld wijzigt u `templates` de map en verwijdert u daar eerst de inhoud:

```console
cd hello-world/templates
rm -rf *
```

Maak `templates` in de map `configmap.yaml` een bestand met de volgende inhoud:

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

Zie [Aan de slag gaan](https://helm.sh/docs/chart_template_guide/getting_started/) in de Helmdocumenten voor meer informatie over het maken en uitvoeren van dit voorbeeld.

### <a name="save-chart-to-local-registry-cache"></a>Grafiek opslaan in lokale registercache

Directory wijzigen `hello-world` in de submap. Voer vervolgens `helm chart save` uit om een kopie van de grafiek lokaal op te slaan en maak ook een alias met de volledig gekwalificeerde naam van het register (alle kleine letters) en de doelopslagplaats en -tag. 

In het volgende voorbeeld is de registernaam *mycontainerregistry,* de doelrepo is *hello-world*en de doelgrafiektag *is v1*, maar substituutwaarden voor uw omgeving:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Voer `helm chart list` deze uit om te bevestigen dat u de grafieken hebt opgeslagen in de lokale registercache. De uitvoer ziet er ongeveer zo uit:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Verifiëren bij het register

Voer `helm registry login` de opdracht in de Helm 3 CLI uit om te [verifiëren met het register](container-registry-authentication.md) met referenties die geschikt zijn voor uw scenario.

Maak bijvoorbeeld een Azure Active [Directory-serviceprincipal met pull- en pushmachtigingen](container-registry-auth-service-principal.md#create-a-service-principal) (AcrPush-rol) naar het register. Lever vervolgens de servicehoofdreferenties aan `helm registry login`. In het volgende voorbeeld wordt het wachtwoord geleverd met behulp van een omgevingsvariabele:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Grafiek pushen naar Azure Container Registry

Voer `helm chart push` de opdracht uit in de Helm 3 CLI om de grafiek naar de volledig gekwalificeerde doelopslagplaats te duwen:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Na een succesvolle push is de output vergelijkbaar met:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Grafieken in de opslagplaats weergeven

Net als bij afbeeldingen die zijn opgeslagen in een Azure-containerregister, u az [acr-repository-opdrachten][az-acr-repository] gebruiken om de opslagplaatsen weer te geven die uw grafieken hosten, en grafiektags en manifesten. 

Voer bijvoorbeeld [de az acr-repository-show][az-acr-repository-show] uit om de eigenschappen te zien van de repo die u in de vorige stap hebt gemaakt:

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

Voer de opdracht [az acr repository show-manifests][az-acr-repository-show-manifests] uit om details te zien van de grafiek die is opgeslagen in de repository. Bijvoorbeeld:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

De output, afgekort in dit `configMediaType` voorbeeld, toont een van `application/vnd.cncf.helm.config.v1+json`:

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

### <a name="pull-chart-to-local-cache"></a>Grafiek naar lokale cache trekken

Als u een Helm-diagram wilt installeren op Kubernetes, moet de grafiek zich in de lokale cache bevinden. Voer in dit `helm chart remove` voorbeeld eerst de bestaande `mycontainerregistry.azurecr.io/helm/hello-world:v1`lokale grafiek met de naam :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Voer `helm chart pull` deze uit om de grafiek uit het Azure-containerregister naar uw lokale cache te downloaden:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Helmdiagram exporteren

Als u verder wilt werken met de `helm chart export`grafiek, exporteert u deze naar een lokale map met behulp van . Exporteer bijvoorbeeld de grafiek die `install` u naar de map hebt getrokken:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Als u informatie voor de geëxporteerde grafiek `helm show chart` in de repo wilt weergeven, voert u de opdracht uit in de map waar u de grafiek hebt geëxporteerd.

```console
cd install
helm show chart hello-world
```

Helm geeft gedetailleerde informatie terug over de nieuwste versie van uw grafiek, zoals weergegeven in de volgende voorbeelduitvoer:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>Helmdiagram installeren

Voer `helm install` uit om de Helm-grafiek te installeren die u naar de lokale cache hebt getrokken en hebt geëxporteerd. Geef een releasenaam op, zoals *myhelmtest,* of geef de `--generate-name` parameter door. Bijvoorbeeld:

```console
helm install myhelmtest ./hello-world
```

Uitvoer na succesvolle grafiekinstallatie is vergelijkbaar met:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Voer de `helm get manifest` opdracht uit om de installatie te verifiëren. De opdracht retourneert de `configmap.yaml` YAML-gegevens in uw sjabloonbestand.

Voer `helm uninstall` uit om de grafiekrelease op uw cluster te verwijderen:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Een Helm-diagram verwijderen uit de opslagplaats

Als u een grafiek uit de repository wilt verwijderen, gebruikt u de opdracht [az acr repository delete.][az-acr-repository-delete] Voer de volgende opdracht uit en bevestig de bewerking wanneer daarom wordt gevraagd:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>Gebruik de Helm 2 client

### <a name="prerequisites"></a>Vereisten

- **Een Azure-containerregister** in uw Azure-abonnement. Maak indien nodig een register met de [Azure-portal](container-registry-get-started-portal.md) of de [Azure CLI.](container-registry-get-started-azure-cli.md)
- **Helm client versie 2.11.0 (geen RC versie) of hoger** - Voer `helm version` uit om uw huidige versie te vinden. Je hebt ook een Helm server (Tiller) geparafeerd binnen een Kubernetes cluster. Maak indien nodig een [Azure Kubernetes Service-cluster][aks-quickstart]. Zie Helm installeren voor meer informatie over het installeren en upgraden [van Helm.][helm-install-v2]
- **Azure CLI-versie 2.0.46** `az --version` of hoger - Uitvoeren om de versie te vinden. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

### <a name="high-level-workflow"></a>Workflow op hoog niveau

Met **Helm 2** jij:

* Configureer uw Azure-containerregister als *één* Helm-diagramopslagplaats. Azure Container Registry beheert de indexdefinitie terwijl u grafieken toevoegt en verwijdert aan de opslagplaats.
* Verifieer met uw Azure-containerregister via de Azure CLI, die vervolgens uw Helm-client automatisch bijwerkt met de register-URI en referenties. U hoeft deze registergegevens niet handmatig op te geven, zodat de referenties niet worden weergegeven in de opdrachtgeschiedenis.
* Gebruik de opdrachten [van az acr-roer][az-acr-helm] in de Azure CLI om uw Azure-containerregister toe te voegen als een Helm-grafiekopslagplaats en om grafieken te pushen en te beheren. Met deze Azure CLI-opdrachten worden helm 2-clientopdrachten omwikkeld.
* Voeg de grafiekopslagplaats in uw Azure-containerregister toe aan uw lokale Helm-repo-index, ter ondersteuning van grafiekzoekopdrachten.
* Met `helm install` deze gegevens u grafieken installeren op een Kubernetes-cluster vanuit een lokale repository-cache.

Zie de volgende secties voor voorbeelden.

### <a name="add-repository-to-helm-client"></a>Repository toevoegen aan helmclient

Voeg uw Azure Container Registry Helm-diagramrepository toe aan uw Helm-client met de opdracht [az acr helm repo add.][az-acr-helm-repo-add] Met deze opdracht wordt een verificatietoken voor uw Azure-containerregister dat wordt gebruikt door de Helm-client. Het verificatietoken is 3 uur geldig. Net `docker login`als bij, u deze opdracht uitvoeren in toekomstige CLI-sessies om uw Helm-client te verifiëren met uw Azure Container Registry Helm-diagramrepository:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Een voorbeelddiagram toevoegen aan de opslagplaats

Maak eerst een lokale map bij *~/acr-helm*en download vervolgens de bestaande *stabiele/wordpress-grafiek:*

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Typ `ls` om de gedownloade grafiek weer te geven en let op de Wordpress-versie die in de bestandsnaam is opgenomen. De `helm fetch stable/wordpress` opdracht heeft geen bepaalde versie opgegeven, dus de *nieuwste* versie is opgehaald. In het volgende voorbeeld uitvoer, de Wordpress grafiek is versie *8.1.0:*

```output
wordpress-8.1.0.tgz
```

Duw de grafiek naar uw Helm-diagramrepository in Azure Container Registry met de pushopdracht [az acr helm][az-acr-helm-push] in de Azure CLI. Geef de naam op van uw Helm-diagram dat in de vorige stap is gedownload, zoals *wordpress-8.1.0.tgz:*

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Na enkele ogenblikken meldt de Azure CLI dat uw grafiek is opgeslagen, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Grafieken in de opslagplaats weergeven

Als u de grafiek wilt gebruiken die in de vorige stap is geüpload, moet de lokale Helm-repository-index worden bijgewerkt. U de repositories in de Helm-client opnieuw indexeren of de Azure CLI gebruiken om de repository-index bij te werken. Elke keer dat u een grafiek toevoegt aan uw opslagplaats, moet deze stap worden voltooid:

```azurecli
az acr helm repo add --name mycontainerregistry
```

Met een grafiek die is opgeslagen in uw opslagplaats en de bijgewerkte index die lokaal beschikbaar is, u de reguliere Helm-clientopdrachten gebruiken om te zoeken of te installeren. Als u alle grafieken in uw `helm search` opslagplaats wilt bekijken, gebruikt u de opdracht met de naam Azure Container Registry:

```console
helm search mycontainerregistry
```

De Wordpress-grafiek die in de vorige stap wordt gepusht, wordt weergegeven, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

U de grafieken ook aanbieden met de Azure CLI, met behulp van [az acr helm lijst:][az-acr-helm-list]

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Informatie weergeven voor een Helmdiagram

Als u informatie voor een specifieke grafiek in `helm inspect` de repo wilt weergeven, u de opdracht gebruiken.

```console
helm inspect mycontainerregistry/wordpress
```

Wanneer er geen versienummer is opgegeven, wordt de *nieuwste* versie gebruikt. Helm retourneert gedetailleerde informatie over uw grafiek, zoals weergegeven in de volgende verkorte voorbeelduitvoer:

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

U de informatie voor een grafiek ook weergeven met de opdracht Azure CLI [az acr helm show.][az-acr-helm-show] Nogmaals, de *nieuwste* versie van een grafiek wordt standaard geretourneerd. U kunt `--version` een specifieke versie van een grafiek aanbieden, zoals *8.1.0:*

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Een Helm-diagram installeren vanuit de opslagplaats

De Helm-grafiek in uw opslagplaats wordt geïnstalleerd door de naam van de opslagplaats en de grafieknaam op te geven. Gebruik de Helm-client om de Wordpress-grafiek te installeren:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Als u naar uw Azure Container Registry Helm-diagramrepository pusht en later terugkeert in een nieuwe CLI-sessie, heeft uw lokale Helm-client een bijgewerkt verificatietoken nodig. Gebruik de opdracht [az acr helm repo om][az-acr-helm-repo-add] een nieuw verificatietoken te verkrijgen.

De volgende stappen worden voltooid tijdens het installatieproces:

- De Helm-client doorzoekt de lokale repository-index.
- De bijbehorende grafiek wordt gedownload uit de Azure Container Registry-opslagplaats.
- De grafiek wordt geïmplementeerd met behulp van de Tiller in uw Kubernetes-cluster.

Volg naarmate de installatie vordert de instructies in de opdrachtuitvoer om de WorPress-URL's en -referenties te bekijken. U de `kubectl get pods` opdracht ook uitvoeren om de Kubernetes-bronnen te bekijken die zijn geïmplementeerd in de grafiek Helm:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Een Helm-diagram verwijderen uit de opslagplaats

Als u een grafiek uit de repository wilt verwijderen, gebruikt u de opdracht voor het verwijderen van het [AZ-acr-roer.][az-acr-helm-delete] Geef de naam van de grafiek op, zoals *wordpress*en de versie die moet worden verwijderd, zoals *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Als u alle versies van de benoemde grafiek `--version` wilt verwijderen, laat u de parameter weg.

De grafiek wordt nog steeds `helm search`geretourneerd wanneer u . Nogmaals, de Helm-client werkt de lijst met beschikbare grafieken in een opslagplaats niet automatisch bij. Als u de Repo-index van de Helm-client wilt bijwerken, gebruikt u de opdracht [az acr helm repo repo opnieuw:][az-acr-helm-repo-add]

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Volgende stappen

* Zie Helmdiagrammen ontwikkelen voor meer informatie over het maken en implementeren [van Helm-diagrammen.][develop-helm-charts]
* Meer informatie over het installeren van toepassingen met Helm in [Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* Helmdiagrammen kunnen worden gebruikt als onderdeel van het containerbouwproces. Zie [Azure Container Registry Tasks gebruiken][acr-tasks]voor meer informatie.

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
