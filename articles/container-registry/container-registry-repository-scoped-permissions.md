---
title: Machtigingen voor opslagplaatsen in Azure Container Registry
description: Een token maken met machtigingen die zijn afgestemd op specifieke opslagplaatsen in een register om afbeeldingen op te halen of te pushen of andere acties uit te voeren
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 7d390bf4d97561e374c70f184534ac4f98a40611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444280"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Een token maken met machtigingen met opslagplaatsen

In dit artikel wordt beschreven hoe u tokens en scopemaps maakt om machtigingen met opslagplaatsen in uw containerregister te beheren. Door tokens te maken, kan een registereigenaar gebruikers of services voorzien van scoped, tijdbeperkte toegang tot repositories om afbeeldingen op te halen of te pushen of andere acties uit te voeren. Een token biedt meer fijnmazige machtigingen dan andere [registerverificatieopties](container-registry-authentication.md), welke machtigingen voor een heel register. 

Scenario's voor het maken van een token zijn:

* IoT-apparaten met afzonderlijke tokens toestaan om een afbeelding uit een opslagplaats te halen
* Een externe organisatie voorzien van machtigingen voor een specifieke opslagplaats 
* Beperk de toegang tot repository tot verschillende gebruikersgroepen in uw organisatie. Geef bijvoorbeeld schrijf- en leestoegang aan ontwikkelaars die afbeeldingen bouwen die zich richten op specifieke repositories en lees toegang tot teams die vanuit die repositories worden geïmplementeerd.

> [!IMPORTANT]
> Deze functie is momenteel in preview en er zijn enkele [beperkingen van toepassing.](#preview-limitations) Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="preview-limitations"></a>Preview-beperkingen

* Deze functie is alleen **Premium** beschikbaar in een Premium-containerregister. Zie [SKU's](container-registry-skus.md)voor Azure Container Registry voor informatie over lagen en limieten voor registerservice.
* U momenteel geen machtigingen met opslagplaatsen toewijzen aan een Azure Active Directory-identiteit, zoals een serviceprincipal of beheerde identiteit.

## <a name="concepts"></a>Concepten

Als u machtigingen met opslagplaatsen wilt configureren, maakt u een *token* met een bijbehorende *scopemap*. 

* Met een **token** samen met een gegenereerd wachtwoord kan de gebruiker zich verifiëren bij het register. U op elk gewenst moment een vervaldatum instellen voor een tokenwachtwoord of een token uitschakelen.  

  Nadat de gebruiker of service een of meer acties heeft geauthenticeerd met een token, kan hij een of meer *acties* uitvoeren die zijn uitgevoerd op een of meer opslagplaatsen.

  |Actie  |Beschrijving  | Voorbeeld |
  |---------|---------|--------|
  |`content/delete`    | Gegevens uit de opslagplaats verwijderen  | Een opslagplaats of een manifest verwijderen |
  |`content/read`     |  Gegevens uit de repository lezen |  Trek een artefact |
  |`content/write`     |  Gegevens naar de opslagplaats schrijven     | Gebruik `content/read` met om een artefact te duwen |
  |`metadata/read`    | Metagegevens uit de repository lezen   | Lijsttags of manifesten |
  |`metadata/write`     |  Metagegevens naar de opslagplaats schrijven  | Bewerkingen lezen, schrijven of verwijderen in- of uitschakelen |

* Een **scopemap** groepeert de repository-machtigingen die u toepast op een token en kan opnieuw worden toegepast op andere tokens. Elk token is gekoppeld aan één scopemap. 

   Met een scope map:

    * Meerdere tokens configureren met identieke machtigingen voor een set opslagplaatsen
    * Tokenmachtigingen bijwerken wanneer u opslagplaatsen toevoegt of verwijdert in de scopemap of een andere scopemap toepast 

  Azure Container Registry biedt ook verschillende systeemgedefinieerde scopemaps die u toepassen, met vaste machtigingen voor alle opslagplaatsen.

De volgende afbeelding toont de relatie tussen tokens en scopemaps. 

![Registertokens en scopemaps](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Vereisten

* **Azure CLI** - Azure CLI-opdrachten voor het maken en beheren van tokens zijn beschikbaar in Azure CLI-versie 2.0.76 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).
* **Docker** - Als u wilt verifiëren met het register om afbeeldingen te trekken of te pushen, hebt u een lokale Docker-installatie nodig. Docker biedt installatie-instructies voor de systemen [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Containerregister** - Als u er geen hebt, maakt u een Premium-containerregister in uw Azure-abonnement of upgradet u een bestaand register. Gebruik bijvoorbeeld de [Azure-portal](container-registry-get-started-portal.md) of de [Azure CLI](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Token maken - CLI

### <a name="create-token-and-specify-repositories"></a>Token maken en opslagplaatsen opgeven

Maak een token met de opdracht [az acr-token maken.][az-acr-token-create] Bij het maken van een token u een of meer opslagplaatsen en bijbehorende acties opgeven voor elke opslagplaats. De repositories hoeven nog niet in het register te staan. Zie de volgende sectie om een token te maken door een bestaande scopemap op te geven.

In het volgende voorbeeld wordt een token in het `samples/hello-world` register *myregistry aanmaken* met de volgende machtigingen op de repo: `content/write` en `content/read`. Standaard stelt de opdracht de standaardtokenstatus in op `enabled`, `disabled` maar u de status op elk gewenst moment bijwerken.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

De uitvoer toont details over het token, waaronder twee gegenereerde wachtwoorden. Het wordt aanbevolen om de wachtwoorden op een veilige plaats op te slaan om later te gebruiken voor verificatie. De wachtwoorden kunnen niet opnieuw worden opgehaald, maar er kunnen wel nieuwe worden gegenereerd.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

De uitvoer bevat details over de scopekaart die de opdracht heeft gemaakt. U de scopemap, `MyToken-scope-map`hier met de naam, gebruiken om dezelfde repository-acties toe te passen op andere tokens. Of werk de scopemap later bij om de machtigingen van de bijbehorende tokens te wijzigen.

### <a name="create-token-and-specify-scope-map"></a>Token maken en bereikkaart opgeven

Een alternatieve manier om een token te maken is door een bestaande scopemap op te geven. Als u nog geen scopemap hebt, maakt u er eerst een door opslagplaatsen en bijbehorende acties op te geven. Geef vervolgens de bereikkaart op bij het maken van een token. 

Als u een scopemap wilt maken, gebruikt u de opdracht [az acr scope-map create.][az-acr-scope-map-create] Met de volgende opdracht wordt een bereikkaart `samples/hello-world` gemaakt met dezelfde machtigingen voor de eerder gebruikte opslagplaats. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Voer [az acr-token maken][az-acr-token-create] om een token te maken, met vermelding van de *MyScopeMap-scopekaart.* Net als in het vorige voorbeeld stelt `enabled`de opdracht de standaardtokenstatus in op .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

De uitvoer toont details over het token, waaronder twee gegenereerde wachtwoorden. Het wordt aanbevolen om de wachtwoorden op een veilige plaats op te slaan om later te gebruiken voor verificatie. De wachtwoorden kunnen niet opnieuw worden opgehaald, maar er kunnen wel nieuwe worden gegenereerd.

## <a name="create-token---portal"></a>Token - portal maken

U de Azure-portal gebruiken om tokens en scopemaps te maken. Net als `az acr token create` bij de opdracht CLI u een bestaande scopemap toepassen of een bereikkaart maken wanneer u een token maakt door een of meer opslagplaatsen en bijbehorende acties op te geven. De repositories hoeven nog niet in het register te staan. 

In het volgende voorbeeld wordt een token gemaakt en wordt `samples/hello-world` een `content/write` scopemap gemaakt met de volgende machtigingen in de repository: en `content/read`.

1. Navigeer in de portal naar uw containerregister.
1. Selecteer **onder Services**de optie Tokens **(Voorvertoning) > +Toevoegen**.
  ![Token maken in portal](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Voer een tokennaam in.
1. Selecteer **onder Scopemap**de optie **Nieuw maken**.
1. De scopekaart configureren:
    1. Voer een naam en beschrijving in voor de scopemap. 
    1. Selecteer en onder `samples/hello-world`Repositories onder **Repositories**en onder **Machtigingen**, selecteer `content/read` en `content/write`. Selecteer vervolgens **+Toevoegen**.  
    ![Scopemap maken in portal](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. Nadat u opslagplaatsen en machtigingen hebt toegevoegd, selecteert u **Toevoegen** om de bereikkaart toe te voegen.
1. Accepteer de **standaardtokenstatus** van **Ingeschakeld** en selecteer **Vervolgens Maken**.

Nadat het token is gevalideerd en gemaakt, worden tokendetails weergegeven in het **scherm Tokens.**

### <a name="add-token-password"></a>Tokenwachtwoord toevoegen

Een wachtwoord genereren nadat u een token hebt gemaakt. Als u wilt verifiëren bij het register, moet het token zijn ingeschakeld en een geldig wachtwoord hebben.

U één of twee wachtwoorden genereren en een vervaldatum instellen voor elke wachtwoord. 

1. Navigeer in de portal naar uw containerregister.
1. Selecteer **onder Services**Tokens **(Voorbeeld)** en selecteer een token.
1. Selecteer in de tokendetails **wachtwoord1** of **wachtwoord2**en selecteer het pictogram Genereren.
1. Stel in het wachtwoordscherm optioneel een vervaldatum in voor het wachtwoord en selecteer **Genereren**.
1. Nadat u een wachtwoord hebt genereren, kopieert en slaat u het op een veilige locatie op. U een gegenereerd wachtwoord niet ophalen nadat u het scherm hebt gesloten, maar u wel een nieuw wachtwoord genereren.

    ![Tokenwachtwoord maken in portal](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Verifiëren met token

Wanneer een gebruiker of service een token gebruikt om te verifiëren met het doelregister, wordt de tokennaam als gebruikersnaam en een van de gegenereerde wachtwoorden vermeld. De verificatiemethode is afhankelijk van de geconfigureerde actie of acties die aan het token zijn gekoppeld.

|Actie  |Authenticeren  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`in Azure CLI |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`in Azure CLI  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`in Azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`in Azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`in Azure CLI |

## <a name="examples-use-token"></a>Voorbeelden: Token gebruiken

In de volgende voorbeelden wordt het token dat eerder in dit artikel is gemaakt, gebruikt om algemene bewerkingen uit te voeren op een opslagplaats: afbeeldingen pushen en trekken, afbeeldingen verwijderen en repository-tags aanbieden. Het token is in eerste instantie`content/write` ingesteld `content/read` met pushmachtigingen (en acties) op de `samples/hello-world` repository.

### <a name="pull-and-tag-test-images"></a>Testafbeeldingen trekken en taggen

Trek de afbeeldingen `hello-world` en `alpine` afbeeldingen van Docker Hub en tag ze voor uw register en opslagplaats voor de volgende voorbeelden.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Verifiëren met token

Voer `docker login` uit om te verifiëren met het register, Geef de tokennaam op als gebruikersnaam en geef een van de wachtwoorden op. Het token moet `Enabled` de status hebben.

Het volgende voorbeeld is opgemaakt voor de bashshell en biedt de waarden met behulp van omgevingsvariabelen.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Uitvoer moet succesvolle verificatie weergeven:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Installatiekopieën naar het register pushen

Probeer na een succesvolle aanmelding de gelabelde afbeeldingen naar het register te duwen. Omdat het token machtigingen heeft om `samples/hello-world` afbeeldingen naar de opslagplaats te pushen, slaagt de volgende push:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Het token heeft geen machtigingen `samples/alpine` voor de repo, dus de volgende `requested access to the resource is denied`pushpoging mislukt met een fout die vergelijkbaar is met:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Machtigingen voor push/pull wijzigen

Als u de machtigingen van een token wilt bijwerken, werkt u de machtigingen in de bijbehorende scopemap bij. De bijgewerkte scopemap wordt onmiddellijk toegepast op alle bijbehorende tokens. 

Werk bijvoorbeeld `MyToken-scope-map` bij `content/write` `content/read` met en `samples/alpine` acties op de `content/write` repository `samples/hello-world` en verwijder de actie op de repository.  

Als u de Azure CLI wilt gebruiken, voert u [de update van de AZ ACR-scopemap][az-acr-scope-map-update] uit om de scopemap bij te werken:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

In Azure Portal:

1. Navigeer naar uw containerregister.
1. Selecteer **onder Services** **Scope-kaarten (Voorbeeld)** en selecteer de bereiktoewijzing die u wilt bijwerken.
1. Selecteer en onder `samples/alpine`Repositories onder **Repositories**en onder **Machtigingen**, selecteer `content/read` en `content/write`. Selecteer vervolgens **+Toevoegen**.
1. Selecteer en schakel `samples/hello-world` onder Repositories de `content/write`selectie uit onder **Repositories**. **Permissions** Selecteer vervolgens **Opslaan**.

Na het bijwerken van de scopemap slaagt de volgende push:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Omdat de scopemap `content/read` alleen de `samples/hello-world` toestemming heeft voor de `samples/hello-world` repository, mislukt een pushpoging naar de repo nu:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Het ophalen van afbeeldingen uit beide repo's lukt, omdat de scopemap machtigingen biedt `content/read` voor beide repositories:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Installatiekopieën verwijderen

Werk de scopemap `content/delete` bij door `alpine` de actie toe te voegen aan de repository. Met deze actie kunnen afbeeldingen in de repository worden verwijderd of de hele opslagplaats worden verwijderd.

Voor beknoptheid tonen we alleen de opdracht voor het bijwerken van de [az acr-scope-map][az-acr-scope-map-update] om de scopemap bij te werken:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Zie de vorige sectie om de scopemap bij te werken met behulp van de portal.

Gebruik de volgende [opdracht az acr repository delete][az-acr-repository-delete] om de `samples/alpine` repository te verwijderen. Als u afbeeldingen of opslagplaatsen wilt verwijderen, wordt het token niet geverifieerd via `docker login`. Geef in plaats daarvan de naam en het wachtwoord van het token door aan de opdracht. In het volgende voorbeeld worden de omgevingsvariabelen gebruikt die eerder in het artikel zijn gemaakt:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Repo-tags weergeven 

Werk de scopemap `metadata/read` bij door `hello-world` de actie toe te voegen aan de repository. Deze actie maakt het lezen van manifest- en taggegevens in de repository mogelijk.

Voor beknoptheid tonen we alleen de opdracht voor het bijwerken van de [az acr-scope-map][az-acr-scope-map-update] om de scopemap bij te werken:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Zie de vorige sectie om de scopemap bij te werken met behulp van de portal.

Om metadata in `samples/hello-world` de repository te lezen, voer je de [opdracht az acr repository show-manifests][az-acr-repository-show-manifests] of [az acr repository show-tags][az-acr-repository-show-tags] uit. 

Als u metagegevens wilt lezen, wordt het token niet geverifieerd via `docker login`. Geef in plaats daarvan de naam en het wachtwoord van het token door aan beide opdrachten. In het volgende voorbeeld worden de omgevingsvariabelen gebruikt die eerder in het artikel zijn gemaakt:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Voorbeelduitvoer:

```console
[
  "v1"
]
```
## <a name="manage-tokens-and-scope-maps"></a>Tokens en scopekaarten beheren

### <a name="list-scope-maps"></a>Lijstbereikkaarten

Gebruik de opdracht [lijst met az acr-scopekaarten][az-acr-scope-map-list] of het scherm **Scope maps (Preview)** in de portal om alle scopemaps op te sommen die in een register zijn geconfigureerd. Bijvoorbeeld:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

De uitvoer toont de scopekaarten die u hebt gedefinieerd en verschillende systeemgedefinieerde scopemaps die u gebruiken om tokens te configureren:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Tokendetails weergeven

Als u de details van een token wilt bekijken, zoals de status en de vervaldatumvan het wachtwoord, voert u de opdracht [az acr-tokenshow uit][az-acr-token-show] of selecteert u het token in het scherm **Tokens (Preview)** in de portal. Bijvoorbeeld:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Gebruik de opdracht [az acr-tokenlijst][az-acr-token-list] of het scherm **Tokens (Preview)** in de portal om alle tokens die in een register zijn geconfigureerd, op te sommen. Bijvoorbeeld:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Wachtwoorden genereren voor token

Als u geen tokenwachtwoord hebt of als u nieuwe wachtwoorden wilt genereren, voert u de opdracht [az acr-tokengenereren uit.][az-acr-token-credential-generate] 

In het volgende voorbeeld wordt een nieuwe waarde gegenereerd voor wachtwoord1 voor het *MyToken-token,* met een vervaldatum van 30 dagen. Het slaat het wachtwoord `TOKEN_PWD`op in de omgevingsvariabele. Dit voorbeeld is opgemaakt voor de bash shell.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Als u de Azure-portal wilt gebruiken om een tokenwachtwoord te genereren, raadpleegt u de stappen in [Token maken - portal](#create-token---portal) eerder in dit artikel.

### <a name="update-token-with-new-scope-map"></a>Token bijwerken met nieuwe scopemap

Als u een token met een andere scopemap wilt bijwerken, voert u [de AZ ACR-token-update][az-acr-token-update] uit en geeft u de nieuwe scopemap op. Bijvoorbeeld:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Selecteer in de portal op het scherm **Tokens (preview)** het token en selecteer onder **Scopemap**een andere scopemap.

> [!TIP]
> Nadat u een token hebt bijgewerkt met een nieuwe scopemap, u nieuwe tokenwachtwoorden genereren. Gebruik de [az acr-token-referentie genereren][az-acr-token-credential-generate] opdracht of regenereren van een token wachtwoord in de Azure portal.

## <a name="disable-or-delete-token"></a>Token uitschakelen of verwijderen

Mogelijk moet u het gebruik van de tokenreferenties voor een gebruiker of service tijdelijk uitschakelen. 

Voer met de Azure CLI de opdracht [az acr-tokenupdate][az-acr-token-update] uit om de `status` opdracht : `disabled`

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

Selecteer in de portal het token in het scherm **Tokens (Preview)** en selecteer **Uitgeschakeld** onder **Status**.

Als u een token wilt verwijderen om de toegang door iedereen die zijn referenties gebruikt, definitief ongeldig te maken, voert u de opdracht [voor het verwijderen van AZ acr-token uit.][az-acr-token-delete] 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

Selecteer in de portal het token in het scherm **Tokens (Preview)** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* Gebruik extra opdrachten in de [opdrachtgroepen AZ ACR Acr scope][az-acr-scope-map] en [az acr token][az-acr-token] om scopemaps en tokens te beheren.
* Zie het [verificatieoverzicht](container-registry-authentication.md) voor andere opties die u verifiëren met een Azure-containerregister, inclusief het gebruik van een Azure Active Directory-identiteit, een serviceprincipal of een beheerdersaccount.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
