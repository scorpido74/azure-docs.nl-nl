---
title: Geheimen beheren bij het werken met een Azure dev Space
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Meer informatie over hoe u Kubernetes-geheimen kunt gebruiken tijdens het uitvoeren of bouwen tijdens het ontwikkelen van toepassingen met Azure dev Spaces
keywords: Docker, Kubernetes, azure, AKS, Azure Container Service, containers
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75438466"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Geheimen beheren bij het werken met een Azure dev Space

Voor uw services zijn mogelijk bepaalde wacht woorden, verbindings reeksen en andere geheimen vereist, zoals voor data bases of andere beveiligde Azure-Services. Door de waarden van deze geheimen in configuratie bestanden in te stellen, kunt u ze beschikbaar maken in uw code als omgevings variabelen.  Deze configuratie bestanden moeten worden verwerkt om te voor komen dat de beveiliging van de geheimen in gevaar komt.

## <a name="storing-and-using-runtime-secrets"></a>Opslaan en gebruiken van runtime geheimen

Azure dev Spaces biedt twee aanbevolen, gestroomlijnde opties voor het opslaan van geheimen in helm-grafieken die worden gegenereerd door de Azure dev `values.dev.yaml` Spaces-client hulpprogramma's: in `azds.yaml`het bestand en inline direct in. Het is niet raadzaam om geheimen op te `values.yaml`slaan in.

> [!NOTE]
> De volgende benaderingen laten zien hoe u geheimen kunt opslaan en gebruiken voor helm-grafieken die door het client hulpprogramma worden gegenereerd. Als u uw eigen helm-grafiek maakt, kunt u het helm-diagram rechtstreeks gebruiken om geheimen te beheren en op te slaan.

### <a name="using-valuesdevyaml"></a>Waarden. dev. yaml gebruiken

In een project dat u al hebt voor bereid met Azure dev Spaces, `values.dev.yaml` maakt u een bestand in dezelfde `azds.yaml` map als om uw geheime sleutels en waarden te definiëren. Bijvoorbeeld:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Controleer de `azds.yaml` verwijzingen naar `values.dev.yaml` het bestand met behulp van een `?`. Bijvoorbeeld:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Als u aanvullende geheime bestanden hebt, kunt u deze hier ook toevoegen.

Werk uw service bij of Controleer uw geheimen als omgevings variabelen. Bijvoorbeeld:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Voer uw bijgewerkte services uit `azds up`met.

```console
azds up
```
 
Gebruiken `kubectl` om te controleren of uw geheimen zijn gemaakt.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Het is niet raadzaam om geheimen op te slaan in broncode beheer. Als u Git gebruikt, `values.dev.yaml` voegt u `.gitignore` toe aan het bestand om te voor komen dat geheimen in broncode beheer worden vastgelegd.

### <a name="using-azdsyaml"></a>Azds. yaml gebruiken

In een project dat u al hebt voor bereid met Azure-ontwikkel ruimten, voegt u geheime sleutels en waarde toe met *$PLACEHOLDER* syntaxis onder *configurations. developer. install. set* in `azds.yaml`. Bijvoorbeeld:

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> U kunt geheime waarden rechtstreeks invoeren zonder *$PLACEHOLDER* syntaxis in `azds.yaml`te gebruiken. Deze methode wordt echter niet aanbevolen, omdat `azds.yaml` deze is opgeslagen in broncode beheer.
     
Maak een `.env` bestand in dezelfde map als `azds.yaml` om uw *$PLACEHOLDER* waarden te definiëren. Bijvoorbeeld:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Het is niet raadzaam om geheimen op te slaan in broncode beheer. Als u Git gebruikt, `.env` voegt u `.gitignore` toe aan het bestand om te voor komen dat geheimen in broncode beheer worden vastgelegd.

Werk uw service bij of Controleer uw geheimen als omgevings variabelen. Bijvoorbeeld:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Voer uw bijgewerkte services uit `azds up`met.

```console
azds up
```
 
Gebruiken `kubectl` om te controleren of uw geheimen zijn gemaakt.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Geheimen gebruiken als bouw argumenten

In de vorige sectie wordt uitgelegd hoe u geheimen opslaat en gebruikt om te gebruiken tijdens de uitvoer tijd van de container. U kunt ook een geheim gebruiken in de bouw tijd van de container, zoals een wacht woord voor een persoonlijke `azds.yaml`NuGet, met behulp van.

Stel `azds.yaml`in de aanmaak tijd geheimen in *configuraties. ontwikkelen. build. args* in met `<variable name>: ${secret.<secret name>.<secret key>}` behulp van de syntaxis. Bijvoorbeeld:

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

In het bovenstaande voor beeld is *mynugetsecret* een bestaand geheim en is *pattoken* een bestaande sleutel.

>[!NOTE]
> Geheime namen en sleutels kunnen het `.` teken bevatten. Gebruiken `\` om te `.` escapen wanneer geheimen worden door gegeven als build-argumenten. Als u bijvoorbeeld een geheim met de naam *foo. Bar* wilt door geven met *token*de sleutel `MYTOKEN: ${secret.foo\.bar.token}`van token:. Bovendien kunnen geheimen worden geëvalueerd met voor voegsel en achtervoegsel tekst. Bijvoorbeeld `MYURL: eus-${secret.foo\.bar.token}-version1`. Ook kunnen geheimen die beschikbaar zijn in de ruimten voor ouders en groot ouders worden door gegeven als build-argumenten.

Gebruik in uw Dockerfile de *ARG* -instructie om het geheim te verbruiken en gebruik dezelfde variabele later in de Dockerfile. Bijvoorbeeld:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Werk de services die in uw cluster worden uitgevoerd bij met deze wijzigingen. Voer op de opdracht regel de volgende opdracht uit:

```
azds up
```

## <a name="next-steps"></a>Volgende stappen

Met deze methoden kunt u nu veilig verbinding maken met een Data Base, een Azure-cache voor redis of beveiligde Azure-Services openen.
 
