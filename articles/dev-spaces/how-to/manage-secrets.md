---
title: Geheimen beheren bij het werken met een Azure dev Space
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Snelle Kubernetes-ontwikkeling met containers en microservices in Azure
keywords: Docker, Kubernetes, azure, AKS, Azure Container Service, containers
ms.openlocfilehash: 49f53683b2499e790414d139dcb0bc0833005647
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280011"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Geheimen beheren bij het werken met een Azure dev Space

Uw services kunnen bepaalde wachtwoorden, connectiestrings en andere geheimen vereisen, zoals voor databases of andere beveiligde Azure-services. Door de waarden van deze geheimen in configuratiebestanden in te stellen, kunt u ze als omgevingsvariabelen beschikbaar maken in uw code.  Deze moeten met zorg worden behandeld om te voorkomen dat de veiligheid van de geheimen in gevaar komt.

Azure dev Spaces biedt twee aanbevolen, gestroomlijnde opties voor het opslaan van geheimen in helm-grafieken die worden gegenereerd door de Azure dev Spaces-client hulpprogramma's: in het bestand values. dev. yaml en direct in azds. yaml. Het is niet aan te raden om geheimen op te slaan in values.yaml. Buiten de twee benaderingen voor helm-grafieken die worden gegenereerd door de client hulpprogramma's die in dit artikel zijn gedefinieerd, kunt u, als u uw eigen helm-grafiek maakt, direct het helm-diagram gebruiken om geheimen te beheren en op te slaan.

## <a name="method-1-valuesdevyaml"></a>Methode 1: values. dev. yaml
1. Open VS Code met uw project dat is ingeschakeld voor Azure Dev Spaces.
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
 
4. Wijzig de servicecode om te verwijzen naar deze geheime gegevens als omgevingsvariabelen, zoals in het volgende voorbeeld:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Werk de services die in uw cluster worden uitgevoerd bij met deze wijzigingen. Voer de volgende opdracht op de opdrachtregel uit:

    ```
    azds up
    ```
 
6. (Optioneel) Controleer of deze geheime gegevens zijn gemaakt via de opdrachtregel:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Zorg ervoor dat u _values.dev.yaml_ toevoegt aan het bestand _.gitignore_ om te voorkomen dat geheimen in broncodebeheer worden vastgelegd.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Methode 2: inline direct in azds. yaml
1.  In _azds.yaml_, stel geheimen in onder de yaml sectie configuraties/ontwikkeling/installatie. U kunt wel geheime waarden direct invoeren, maar het is niet aanbevolen omdat _azds.yaml_ wordt opgenomen in broncodebeheer. In plaats daarvan voegt u met de syntaxis '$PLACEHOLDER' tijdelijke aanduidingen toe.

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
     
2.  Maak een _. env_ -bestand in dezelfde map als _azds. yaml_. Geef geheimen op met behulp van de standaard sleutel = waarde-notatie. Neem het _.env_ bestand niet op in broncodebeheer. (Als u wilt weglaten van broncode beheer in op Git gebaseerde versie beheersysteem systemen, voegt u het toe aan het _. gitignore_ -bestand.) In het volgende voor beeld ziet u een _. env_ -bestand:

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  Wijzig uw servicecode om te verwijzen naar deze geheimen in code, zoals in het volgende voorbeeld:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Werk de services die in uw cluster worden uitgevoerd bij met deze wijzigingen. Voer de volgende opdracht op de opdrachtregel uit:

    ```
    azds up
    ```

4.  (optioneel) Bekijk de geheimen van kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>Volgende stappen

Met deze methoden kunt u nu veilig verbinding maken met een Data Base, een Azure-cache voor redis of beveiligde Azure-Services openen.
 
