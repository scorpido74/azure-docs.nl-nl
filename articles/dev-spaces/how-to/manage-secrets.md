---
title: Geheimen beheren wanneer u met een Azure Dev Space werkt
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Meer informatie over het gebruik van Kubernetes-geheimen tijdens het uitvoeren of het opbouwen van tijd bij het ontwikkelen van toepassingen met Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438466"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Geheimen beheren wanneer u met een Azure Dev Space werkt

Voor uw services zijn mogelijk bepaalde wachtwoorden, verbindingstekenreeksen en andere geheimen vereist, zoals voor databases of andere beveiligde Azure-services. Door de waarden van deze geheimen in configuratiebestanden in te stellen, u ze beschikbaar maken in uw code als omgevingsvariabelen.  Deze configuratiebestanden moeten met zorg worden behandeld om te voorkomen dat de beveiliging van de geheimen in het gedrang komt.

## <a name="storing-and-using-runtime-secrets"></a>Runtime-geheimen opslaan en gebruiken

Azure Dev Spaces biedt twee aanbevolen, gestroomlijnde opties voor het opslaan van geheimen in Helm-diagrammen die worden gegenereerd door de azure Dev Spaces-clienttooling: in het `values.dev.yaml` bestand en direct in `azds.yaml`. Het is niet aan te `values.yaml`raden om geheimen op te slaan in.

> [!NOTE]
> De volgende benaderingen laten u zien hoe u geheimen opslaan en gebruiken voor Helm-diagrammen die worden gegenereerd door de clienttooling. Als u uw eigen Helm-diagram maakt, u de Helm-grafiek rechtstreeks gebruiken om geheimen te beheren en op te slaan.

### <a name="using-valuesdevyaml"></a>Waarden.dev.yaml gebruiken

Maak in een project dat u al hebt `values.dev.yaml` voorbereid met Azure `azds.yaml` Dev Spaces een bestand in dezelfde map als om uw geheime sleutels en waarden te definiëren. Bijvoorbeeld:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Controleer `azds.yaml` de bestandsverwijzingen `values.dev.yaml` als `?`optioneel met behulp van een . Bijvoorbeeld:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Als u extra geheime bestanden hebt, u deze hier ook toevoegen.

Werk of verifieer uw service als omgevingsvariabelen als omgevingsvariabelen. Bijvoorbeeld:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Voer uw bijgewerkte `azds up`services uit met behulp van .

```console
azds up
```
 
Gebruik `kubectl` om te controleren of je geheimen zijn gemaakt.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Het is niet aan te raden om geheimen op te slaan in bronbeheer. Als u Git `values.dev.yaml` gebruikt, voegt u het `.gitignore` bestand toe om te voorkomen dat u geheimen in bronbeheer begaat.

### <a name="using-azdsyaml"></a>Met behulp van azds.yaml

Voeg in een project dat u al hebt voorbereid met Azure Dev Spaces geheime sleutels en `azds.yaml`waarde toe met behulp van *$PLACEHOLDER* syntaxis onder *configurations.develop.install.set* in . Bijvoorbeeld:

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
> U geheime waarden *$PLACEHOLDER* rechtstreeks invoeren `azds.yaml`zonder $PLACEHOLDER syntaxis te gebruiken in. Deze aanpak wordt echter niet `azds.yaml` aanbevolen omdat deze is opgeslagen in bronbeheer.
     
Maak `.env` een bestand in `azds.yaml` dezelfde map als om uw *$PLACEHOLDER* waarden te definiëren. Bijvoorbeeld:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Het is niet aan te raden om geheimen op te slaan in bronbeheer. Als u Git `.env` gebruikt, voegt u het `.gitignore` bestand toe om te voorkomen dat u geheimen in bronbeheer begaat.

Werk of verifieer uw service als omgevingsvariabelen als omgevingsvariabelen. Bijvoorbeeld:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Voer uw bijgewerkte `azds up`services uit met behulp van .

```console
azds up
```
 
Gebruik `kubectl` om te controleren of je geheimen zijn gemaakt.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Geheimen gebruiken als bouwargumenten

De vorige sectie liet zien hoe je geheimen opslaan en gebruiken om te gebruiken tijdens de runtime van containers. U ook elk geheim gebruiken tijdens de bouwtijd van containers, zoals een wachtwoord voor een privé-NuGet, met behulp van `azds.yaml`.

Stel `azds.yaml`in , stel de build tijd geheimen in *configuraties.develop.build.args* met behulp van de `<variable name>: ${secret.<secret name>.<secret key>}` syntaxis. Bijvoorbeeld:

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

In het bovenstaande voorbeeld, *mynugetsecret* is een bestaand geheim en *pattoken* is een bestaande sleutel.

>[!NOTE]
> Geheime namen en toetsen `.` kunnen het teken bevatten. Gebruik `\` om `.` te ontsnappen bij het doorgeven van geheimen als argumenten op te bouwen. Bijvoorbeeld om een geheim met de naam *foo.bar* door te geven met de sleutel van *token:* `MYTOKEN: ${secret.foo\.bar.token}`. Daarnaast kunnen geheimen worden geëvalueerd met voorvoegsel en postfixtekst. Bijvoorbeeld `MYURL: eus-${secret.foo\.bar.token}-version1`. Ook geheimen beschikbaar in ouder en grootouder ruimtes kunnen worden doorgegeven als argumenten te bouwen.

Gebruik in uw Dockerfile de *ARG-richtlijn* om het geheim te gebruiken en gebruik die zelfde variabele later in het Dockerfile. Bijvoorbeeld:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Werk de services die in uw cluster worden uitgevoerd bij met deze wijzigingen. Voer de opdracht uit op de opdrachtregel:

```
azds up
```

## <a name="next-steps"></a>Volgende stappen

Met deze methoden u nu veilig verbinding maken met een database, een Azure-cache voor Redis of toegang krijgen tot beveiligde Azure-services.
 
