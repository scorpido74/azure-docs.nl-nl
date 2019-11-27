---
title: Machtigingen voor opslag plaatsen
description: Een token maken met machtigingen die zijn gericht op specifieke opslag plaatsen in een REGI ster om installatie kopieën op te halen of te pushen
ms.topic: article
ms.date: 10/31/2019
ms.openlocfilehash: cf36a49ffd6c04897e6f44b844f0c813d0992b18
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454906"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Machtigingen voor opslag plaatsen in Azure Container Registry 

Azure Container Registry ondersteunt verschillende [verificatie opties](container-registry-authentication.md) met behulp van identiteiten die op [rollen gebaseerde toegang](container-registry-roles.md) tot een volledig REGI ster hebben. Voor bepaalde scenario's moet u echter mogelijk alleen toegang bieden tot specifieke *opslag* plaatsen in een REGI ster. 

In dit artikel wordt beschreven hoe u een toegangs token maakt en gebruikt dat machtigingen heeft voor het uitvoeren van acties op alleen specifieke opslag plaatsen in een REGI ster. Met een toegangs token kunt u gebruikers of Services voorzien van scoped, tijdgebonden toegang tot opslag plaatsen om installatie kopieën te halen of te pushen of andere acties uit te voeren. 

Zie [over machtigingen voor opslagplaatsen](#about-repository-scoped-permissions), verderop in dit artikel, voor achtergrond informatie over de concepten en scenario's van tokens.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er [zijn enkele beperkingen van toepassing](#preview-limitations). Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functie worden mogelijk nog gewijzigd voordat de functie algemeen beschikbaar wordt.

## <a name="preview-limitations"></a>Preview-beperkingen

* Deze functie is alleen beschikbaar in een **Premium** container Registry. Zie [Azure container Registry sku's](container-registry-skus.md)voor meer informatie over de service lagen en limieten voor het REGI ster.
* U kunt momenteel geen machtigingen voor opslag plaatsen toewijzen aan een Azure Active Directory-object zoals een service-principal of beheerde identiteit.

## <a name="prerequisites"></a>Vereisten

* **Azure cli** : in dit artikel is een lokale installatie van Azure cli (versie 2.0.76 of hoger) vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.
* **Docker** : voor verificatie met het REGI ster hebt u ook een lokale docker-installatie nodig. Docker biedt installatie-instructies voor de systemen [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Container register met opslag** plaatsen: als u er geen hebt, kunt u een container register maken in uw Azure-abonnement. Gebruik bijvoorbeeld de [Azure Portal](container-registry-get-started-portal.md) of de [Azure cli](container-registry-get-started-azure-cli.md). 

  [Push](container-registry-get-started-docker-cli.md) of [Importeer](container-registry-import-images.md) een of meer voorbeeld installatie kopieën naar het REGI ster voor test doeleinden. Voor beelden in dit artikel verwijzen naar de volgende installatie kopieën in twee opslag plaatsen: `samples/hello-world:v1` en `samples/nginx:v1`. 

## <a name="create-an-access-token"></a>Een toegangs token maken

Maak een token met behulp van de opdracht [AZ ACR token Create][az-acr-token-create] . Wanneer u een token maakt, geeft u een of meer opslag plaatsen en gekoppelde acties op elke opslag plaats op. u kunt ook een bestaande scope toewijzing met deze instellingen opgeven.

### <a name="create-access-token-and-specify-repositories"></a>Toegangs token maken en opslag plaatsen opgeven

In het volgende voor beeld wordt een toegangs token gemaakt met machtigingen voor het uitvoeren van `content/write` en `content/read` acties in de `samples/hello-world`-opslag plaats en de `content/read` actie op de `samples/nginx` opslag plaats. De opdracht genereert standaard twee wacht woorden. 

In dit voor beeld wordt de token status ingesteld op `enabled` (de standaard instelling), maar u kunt het token op elk gewenst moment bijwerken en de status instellen op `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

De uitvoer toont details over het token, met inbegrip van gegenereerde wacht woorden en bereik toewijzing. Het is raadzaam om de wacht woorden op een veilige plek op te slaan, zodat u deze later kunt gebruiken met `docker login`. Het wacht woord kan niet opnieuw worden opgehaald, maar er kunnen nieuwe worden gegenereerd.

In de uitvoer ziet u ook dat er automatisch een scope toewijzing wordt gemaakt met de naam `MyToken-scope-map`. U kunt de bereik toewijzing gebruiken om dezelfde opslagplaats acties toe te passen op andere tokens. Of werk de scope toewijzing later bij om de token machtigingen te wijzigen.

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
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

### <a name="create-a-scope-map-and-associated-token"></a>Een scope toewijzing en een bijbehorend token maken

U kunt ook een scope toewijzing met opslag plaatsen en gekoppelde acties opgeven bij het maken van een token. Als u een scope toewijzing wilt maken, gebruikt u de opdracht [AZ ACR Scope-map Create][az-acr-scope-map-create] .

Met de volgende voorbeeld opdracht maakt u een scope toewijzing met dezelfde machtigingen die in het vorige voor beeld worden gebruikt. Hiermee kunnen `content/write`-en `content/read` acties in de `samples/hello-world` opslag plaats en de `content/read` actie op de `samples/nginx` opslag plaats worden uitgevoerd:

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

De uitvoer lijkt op het volgende:

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

Voer [AZ ACR token Create][az-acr-token-create] uit om een token te maken dat is gekoppeld aan de *MyScopeMap* -bereik toewijzing. De opdracht genereert standaard twee wacht woorden. In dit voor beeld wordt de token status ingesteld op `enabled` (de standaard instelling), maar u kunt het token op elk gewenst moment bijwerken en de status instellen op `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

De uitvoer toont details over het token, met inbegrip van gegenereerde wacht woorden en de scope toewijzing die u hebt toegepast. Het is raadzaam om de wacht woorden op een veilige plek op te slaan, zodat u deze later kunt gebruiken met `docker login`. Het wacht woord kan niet opnieuw worden opgehaald, maar er kunnen nieuwe worden gegenereerd.

## <a name="generate-passwords-for-token"></a>Wacht woorden genereren voor token

Als er wacht woorden zijn gemaakt tijdens het maken van het token, gaat u verder [met authenticatie met het REGI ster](#authenticate-using-token).

Als u geen token wachtwoord hebt of als u nieuwe wacht woorden wilt genereren, voert u de opdracht [AZ ACR token Credential generate][az-acr-token-credential-generate] uit.

In het volgende voor beeld wordt een nieuw wacht woord gegenereerd voor het token dat u hebt gemaakt, met een verval periode van 30 dagen. Het wacht woord wordt opgeslagen in de omgevings variabele TOKEN_PWD. Dit voor beeld is opgemaakt voor de bash-shell.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>Verifiëren met token

Voer `docker login` uit om te verifiëren met het REGI ster met de token referenties. Voer de token naam in als de gebruikers naam en geef een wacht woord op. Het volgende voor beeld is opgemaakt voor de bash-shell en biedt de waarden met behulp van omgevings variabelen.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Bij uitvoer moet de verificatie slagen worden weer gegeven:

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>Scoped Access controleren

U kunt controleren of het token machtigingen met een bereik heeft voor de opslag plaatsen in het REGI ster. In dit voor beeld worden de volgende `docker pull` opdrachten voltooid om installatie kopieën te halen die beschikbaar zijn in de `samples/hello-world` en `samples/nginx` opslagplaatsen:

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

Omdat de voorbeeld token de `content/write` actie alleen op de `samples/hello-world` opslag plaats toestaat, is `docker push` mislukt voor die opslag plaats, maar mislukt de `samples/nginx`:

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>Bereik toewijzing en Token bijwerken

Als u de token machtigingen wilt bijwerken, werkt u de machtigingen in de bijbehorende toewijzing van het bereik bij met [AZ ACR Scope-map Update][az-acr-scope-map-update]. Als u bijvoorbeeld *MyScopeMap* wilt bijwerken, verwijdert u de `content/write` actie in de `samples/hello-world` opslag plaats:

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

Als de scope toewijzing is gekoppeld aan meer dan één token, wordt met de opdracht de machtiging van alle gekoppelde tokens bijgewerkt.

Als u een token wilt bijwerken met een andere scope toewijzing, voert u [AZ ACR token update][az-acr-token-update]uit. Bijvoorbeeld:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Na het bijwerken van een token of een bereik toewijzing die is gekoppeld aan een token, worden de gewijzigde machtigingen van kracht bij de volgende `docker login` of andere verificatie met behulp van het token.

Nadat u een token hebt bijgewerkt, wilt u mogelijk nieuwe wacht woorden genereren voor toegang tot het REGI ster. Voer [AZ ACR token Credential generate][az-acr-token-credential-generate]. Bijvoorbeeld:

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>Over machtigingen voor opslag plaatsen met een bereik

### <a name="concepts"></a>Concepten

Als u machtigingen voor opslag plaatsen wilt configureren, maakt u een *toegangs token* en een bijbehorend *bereik toewijzing* met behulp van opdrachten in de Azure cli.

* Een **toegangs token** is een referentie die wordt gebruikt met een wacht woord voor verificatie bij het REGI ster. Gekoppeld aan elk token zijn toegestane *acties* die zijn gericht op een of meer opslag plaatsen. U kunt een verloop tijd instellen voor elk token. 

* **Acties** voor elke opgegeven opslag plaats bevatten een of meer van de volgende.

  |Actie  |Beschrijving  |
  |---------|---------|
  |`content/read`     |  Gegevens uit de opslag plaats lezen. U kunt bijvoorbeeld een artefact ophalen.  |
  |`metadata/read`    | Lees de meta gegevens uit de opslag plaats. Bijvoorbeeld Tags lijst of manifest meta gegevens weer geven.   |
  |`content/write`     |  Gegevens schrijven naar de opslag plaats. Gebruik met `content/read` om een artefact te pushen.    |
  |`metadata/write`     |  Meta gegevens schrijven naar de opslag plaats. U kunt bijvoorbeeld manifest kenmerken bijwerken.  |
  |`content/delete`    | Gegevens uit de opslag plaats verwijderen. U kunt bijvoorbeeld een opslag plaats of een manifest verwijderen. |

* Een **Scope toewijzing** is een register object waarmee opslagplaats machtigingen worden gegroepeerd die u toepast op een token of die opnieuw kan worden toegepast op andere tokens. Als u geen scope toewijzing toepast tijdens het maken van een token, wordt er automatisch een scope toewijzing voor u gemaakt, om de machtigings instellingen op te slaan. 

  Een scope toewijzing helpt u bij het configureren van meerdere gebruikers met identieke toegang tot een set opslag plaatsen. Azure Container Registry biedt ook door het systeem gedefinieerde bereik toewijzingen die u kunt Toep assen bij het maken van toegangs tokens.

De volgende afbeelding bevat een overzicht van de relatie tussen tokens en Scope Maps. 

![Toewijzings-en tokens voor het bereik van het REGI ster](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>Scenario 's

Scenario's voor het gebruik van een toegangs token zijn onder andere:

* IoT-apparaten voorzien van afzonderlijke tokens om een installatie kopie uit een opslag plaats te halen
* Een externe organisatie voorzien van machtigingen voor een specifieke opslag plaats 
* Beperk de toegang tot opslag plaatsen tot specifieke gebruikers groepen in uw organisatie. U kunt bijvoorbeeld schrijf-en lees toegang bieden aan ontwikkel aars die installatie kopieën bouwen die gericht zijn op specifieke opslag plaatsen en lees toegang tot teams die vanuit deze opslag plaatsen worden geïmplementeerd.

### <a name="authentication-using-token"></a>Verificatie met behulp van token

Gebruik een token naam als gebruikers naam en een van de bijbehorende wacht woorden om te verifiëren met het doel register. De verificatie methode is afhankelijk van de geconfigureerde acties.

### <a name="contentread-or-contentwrite"></a>inhoud/lezen of inhoud/schrijven

Als het token alleen `content/read`-of `content/write` acties toestaat, geeft u de token referenties op in een van de volgende verificatie stromen:

* Verifiëren met docker met behulp van `docker login`
* Verifiëren met het REGI ster met de opdracht [AZ ACR login][az-acr-login] in de Azure cli

Met de volgende verificatie kunnen de geconfigureerde acties op de opslag plaats of opslag plaatsen in het bereik worden toegestaan met het token. Als het token bijvoorbeeld de `content/read` actie op een opslag plaats toestaat, zijn `docker pull` bewerkingen toegestaan voor installatie kopieën in die opslag plaats.

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>meta gegevens/lezen, meta gegevens/schrijven of inhoud/verwijderen

Als het token `metadata/read`, `metadata/write`of `content/delete` acties in een opslag plaats toestaat, moeten de token referenties worden opgegeven als para meters met de gerelateerde [AZ ACR repository][az-acr-repository] -opdrachten in de Azure cli.

Als `metadata/read` acties bijvoorbeeld zijn toegestaan in een opslag plaats, geeft u de token referenties door als u de opdracht [AZ ACR repository show-Tags][az-acr-repository-show-tags] uitvoert om tags weer te geven.

## <a name="next-steps"></a>Volgende stappen

* Als u Scope toewijzingen en toegangs tokens wilt beheren, gebruikt u aanvullende opdrachten in de opdracht [AZ ACR Scope-map][az-acr-scope-map] en [AZ ACR token][az-acr-token] Command groups.
* Zie [verificatie-overzicht](container-registry-authentication.md) voor scenario's voor verificatie met een Azure container Registry met behulp van een beheerders account of een Azure Active Directory identiteit.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
