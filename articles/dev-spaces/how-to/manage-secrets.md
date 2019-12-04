---
title: Geheimen beheren bij het werken met een Azure dev Space
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Snelle Kubernetes-ontwikkeling met containers en microservices in Azure
keywords: Docker, Kubernetes, azure, AKS, Azure Container Service, containers
ms.openlocfilehash: b184f72dfbbfe093443ab8a9b79bafbece3a3d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790168"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Geheimen beheren bij het werken met een Azure dev Space

Voor uw services zijn mogelijk bepaalde wacht woorden, verbindings reeksen en andere geheimen vereist, zoals voor data bases of andere beveiligde Azure-Services. Door de waarden van deze geheimen in configuratie bestanden in te stellen, kunt u ze beschikbaar maken in uw code als omgevings variabelen.  Deze moeten worden afgehandeld om te voor komen dat de beveiliging van de geheimen in gevaar komt.

Azure dev Spaces biedt twee aanbevolen, gestroomlijnde opties voor het opslaan van geheimen in helm-grafieken die worden gegenereerd door de Azure dev Spaces-client hulpprogramma's: in het `values.dev.yaml`-bestand en direct in `azds.yaml`. Het is niet raadzaam om geheimen op te slaan in `values.yaml`. Buiten de twee benaderingen voor helm-grafieken die worden gegenereerd door de client hulpprogramma's die in dit artikel zijn gedefinieerd, kunt u, als u uw eigen helm-grafiek maakt, direct het helm-diagram gebruiken om geheimen te beheren en op te slaan.

## <a name="method-1-valuesdevyaml"></a>Methode 1: values. dev. yaml
1. Open VS code met het project dat is ingeschakeld voor Azure dev Spaces.
2. Voeg een bestand toe met de naam _Values. dev. yaml_ in dezelfde map als bestaande _azds. yaml_ en Definieer uw geheime sleutel en waarden, zoals in het volgende voor beeld:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds. yaml_ verwijst al naar het bestand _Values. dev. yaml_ als dit bestaat. Als u de voor keur geeft aan een andere bestands naam, moet u de sectie install. values bijwerken:

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. Wijzig uw service code zodat deze geheimen als omgevings variabelen verwijzen, zoals in het volgende voor beeld:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Werk de services die in uw cluster worden uitgevoerd bij met deze wijzigingen. Voer op de opdracht regel de volgende opdracht uit:

    ```
    azds up
    ```
 
6. Beschrijving Controleer op de opdracht regel of deze geheimen zijn gemaakt:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Zorg ervoor dat u _waarden. dev. yaml_ toevoegt aan het _. gitignore_ -bestand om te voor komen dat geheimen in broncode beheer worden doorgevoerd.
 
 
## <a name="method-2-azdsyaml"></a>Methode 2: azds. yaml
1.  Stel in _azds. yaml_geheimen in onder de yaml-sectie configuraties/ontwikkelen/installeren. Hoewel u daar rechtstreeks geheime waarden kunt invoeren, wordt het niet aanbevolen omdat _azds. yaml_ is ingecheckt in broncode beheer. Voeg in plaats daarvan tijdelijke aanduidingen toe met de syntaxis ' $PLACEHOLDER '.

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
     
2.  Maak een _. env_ -bestand in dezelfde map als _azds. yaml_. Geef geheimen op met behulp van de standaard sleutel = waarde-notatie. Sla het _. env_ -bestand niet door aan broncode beheer. (Als u wilt weglaten van broncode beheer in op Git gebaseerde versie beheersysteem systemen, voegt u het toe aan het _. gitignore_ -bestand.) In het volgende voor beeld ziet u een _. env_ -bestand:

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  Wijzig de bron code van uw service om te verwijzen naar deze geheimen in code, zoals in het volgende voor beeld:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Werk de services die in uw cluster worden uitgevoerd bij met deze wijzigingen. Voer op de opdracht regel de volgende opdracht uit:

    ```
    azds up
    ```

4.  Beschrijving Geheimen van kubectl weer geven:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="passing-secrets-as-build-arguments"></a>Geheimen door geven als constructie argumenten

In de vorige secties is gebleken hoe geheimen moeten worden door gegeven tijdens de uitvoer tijd van de container. U kunt ook een geheim door geven in de bouw tijd van de container, zoals een wacht woord voor een persoonlijke NuGet, met behulp van `azds.yaml`.

In `azds.yaml`stelt u de aanmaak tijd geheimen in *configuraties. ontwikkelen. build. args* met de `<variable name>: ${secret.<secret name>.<secret key>}` syntaxis. Bijvoorbeeld:

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
> Geheime namen en sleutels kunnen het `.` teken bevatten. Gebruik `\` om `.` te escapen wanneer geheimen worden door gegeven als argumenten voor samen stellen. Als u bijvoorbeeld een geheim met de naam *foo. Bar* wilt door geven met de sleutel van *token*: `MYTOKEN: ${secret.foo\.bar.token}`. Bovendien kunnen geheimen worden geëvalueerd met voor voegsel en achtervoegsel tekst. Bijvoorbeeld `MYURL: eus-${secret.foo\.bar.token}-version1`. Ook kunnen geheimen die beschikbaar zijn in de ruimten voor ouders en groot ouders worden door gegeven als build-argumenten.

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
 
