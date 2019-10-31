---
title: Uw configuratie server instellen in azure lente Cloud | Microsoft Docs
description: In deze zelf studie leert u hoe u een lente-Cloud configuratie server kunt instellen voor uw Azure lente-Cloud op de Azure Portal
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 10/18/2019
ms.openlocfilehash: 3a091c22f49ec31029a1808c10e675a4d0960fb4
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177893"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Zelf studie: een lente Cloud configuratie server instellen voor uw service

In deze zelf studie wordt uitgelegd hoe u een lente-Cloud configuratie server verbindt met uw Azure lente-Cloud service.

Lente Cloud configuratie biedt server-en client ondersteuning voor externe configuratie in een gedistribueerd systeem. Met de configuratie server beschikt u over een centrale plek voor het beheren van externe eigenschappen voor toepassingen in alle omgevingen. Ga voor meer informatie naar de [verwijzing naar de lente-Cloud configuratie server](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 
* Een reeds ingerichte en actieve Azure lente-Cloud service.  Voltooi [deze Snelstartgids](spring-cloud-quickstart-launch-app-cli.md) om een Azure lente-Cloud service in te richten en te starten.

## <a name="restriction"></a>Beperking

Er zijn enkele beperkingen bij het gebruik van een __Configuratie server__ met een Git-back-end. Sommige eigenschappen worden automatisch in uw toepassings omgeving ingevoegd om toegang te krijgen tot de __Configuratie server__ en de __service detectie__. Als u deze eigenschappen ook van uw **config server** -bestanden configureert, kunnen er conflicten en onverwacht gedrag optreden. De eigenschappen zijn onder andere: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```
> [!CAUTION]
> We raden u ten zeerste aan de bovenstaande eigenschappen __niet__ in uw __Configuratie server__ toepassings bestanden in te stellen.

## <a name="create-your-config-server-files"></a>Uw config server-bestanden maken

Azure lente Cloud ondersteunt Azure DevOps, GitHub, GitLab en bitbucket om uw config server-bestanden op te slaan. Wanneer u de opslag plaats gereed hebt, maakt u de configuratie bestanden met de onderstaande instructies en slaat u deze op.

Bovendien zijn sommige Configureer bare eigenschappen alleen beschikbaar voor sommige typen. In de volgende subsecties worden de eigenschappen van elk type opslag plaats weer geven.

### <a name="public-repository"></a>Open bare opslag plaats

Wanneer u een open bare opslag plaats gebruikt, zijn uw Configureer bare eigenschappen beperkter.

Hieronder vindt u alle Configureer bare eigenschappen die worden gebruikt voor het instellen van de open bare `Git`-opslag plaats.

> [!NOTE]
> Het gebruik van een koppel teken (-) om woorden te scheiden is de enige naamgevings Conventie die momenteel wordt ondersteund. U kunt bijvoorbeeld `default-label`gebruiken, maar niet `defaultLabel`.

| Eigenschap        | Verplicht | Functie                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | De `uri` van de `Git` opslag plaats die wordt gebruikt als back-end van de configuratie server, moet worden gestart met `http://`, `https://`, `git@`of `ssh://`. |
| `default-label` | `no`     | Het standaard label van de `Git` opslag plaats moet de `branch name`, `tag name`of `commit-id` van de opslag plaats zijn. |
| `search-paths`  | `no`     | Een matrix met teken reeksen die worden gebruikt voor het zoeken in submappen van de `Git` opslag plaats. |

------

### <a name="private-repository-with-ssh-authentication"></a>Privé-opslag plaats met SSH-verificatie

Alle Configureer bare eigenschappen die worden gebruikt voor het instellen van een persoonlijke `Git` opslagplaats met `Ssh` worden hieronder weer gegeven.

> [!NOTE]
> Het gebruik van een koppel teken (-) om woorden te scheiden is de enige naamgevings Conventie die momenteel wordt ondersteund. U kunt bijvoorbeeld `default-label`gebruiken, maar niet `defaultLabel`.

| Eigenschap                   | Verplicht | Functie                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | De `uri` van de `Git` opslag plaats die wordt gebruikt als back-end van de configuratie server, moet worden gestart met `http://`, `https://`, `git@`of `ssh://`. |
| `default-label`            | `no`     | Het standaard label van de `Git` opslag plaats moet de `branch name`, `tag name`of `commit-id` van de opslag plaats zijn. |
| `search-paths`             | `no`     | Een matrix met teken reeksen die worden gebruikt voor het zoeken in submappen van de `Git` opslag plaats. |
| `private-key`              | `no`     | De persoonlijke sleutel van `Ssh` voor toegang tot de `Git` opslag plaats, __vereist__ wanneer `uri` met `git@` of `ssh://`begint. |
| `host-key`                 | `no`     | De host-sleutel van de Git-opslagplaats server mag het algoritme voorvoegsel niet bevatten, zoals wordt gedekt door `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | Het algoritme van de host-sleutel moet `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`of `ecdsa-sha2-nistp521`zijn. Alleen vereist als `host-key` bestaat. |
| `strict-host-key-checking` | `no`     | Hiermee geeft u op of de configuratie server niet kan worden gestart wanneer de persoonlijke `host-key`worden uitgevoerd. Moet `true` (standaard waarde) of `false`zijn. |

-----

### <a name="private-repository-with-basic-authentication"></a>Privé-opslag plaats met basis verificatie

Alle Configureer bare eigenschappen die worden gebruikt voor het instellen van een persoonlijke Git-opslag plaats met basis verificatie, worden hieronder weer gegeven.

> [!NOTE]
> Het gebruik van een koppel teken (-) om woorden te scheiden is de enige naamgevings Conventie die momenteel wordt ondersteund. Gebruik bijvoorbeeld `default-label` niet `defaultLabel`.

| Eigenschap        | Verplicht | Functie                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | De `uri` van de `Git` opslag plaats die wordt gebruikt als back-end van de configuratie server, moet worden gestart met `http://`, `https://`, `git@`of `ssh://`. |
| `default-label` | `no`     | Het standaard label van de `Git` opslag plaats moet de `branch name`, `tag name`of `commit-id` van de opslag plaats zijn. |
| `search-paths`  | `no`     | Een matrix met teken reeksen die worden gebruikt voor het zoeken in submappen van de `Git` opslag plaats. |
| `username`      | `no`     | De `username` die wordt gebruikt voor toegang tot de `Git` opslagplaats server, __vereist__ wanneer de `Git` opslagplaats server `Http Basic Authentication`ondersteunt. |
| `password`      | `no`     | Het wacht woord dat wordt gebruikt voor toegang tot de `Git` opslagplaats server, __vereist__ wanneer de `Git` opslagplaats server `Http Basic Authentication`ondersteunt. |

> [!NOTE]
> Sommige `Git` opslagplaats servers, zoals GitHub, ondersteunen een ' persoonlijk token ' of ' toegangs token ' als wacht woord voor `HTTP Basic Authentication`. U kunt dit soort token ook als een wacht woord gebruiken en het ' persoonlijk token ' of ' toegangs token ' verloopt niet. Voor git-opslagplaats servers, zoals BitBucket en Azure DevOps, verloopt het token echter binnen een of twee uur, waardoor deze optie niet haalbaar is voor gebruik met Azure lente-Cloud.

### <a name="git-repositories-with-pattern"></a>Git-opslag plaatsen met patroon

Hieronder vindt u alle Configureer bare eigenschappen die worden gebruikt om Git-opslag plaatsen in te stellen met een patroon.

> [!NOTE]
> Het gebruik van een koppel teken (-) om woorden te scheiden is de enige naamgevings Conventie die momenteel wordt ondersteund. Gebruik bijvoorbeeld `default-label` niet `defaultLabel`.

| Eigenschap                           | Verplicht         | Functie                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | Een toewijzing die bestaat uit de instellingen voor een `Git` opslag plaats met een bepaalde naam. |
| `repos."uri"`                      | `yes` op `repos` | De `uri` van de `Git` opslag plaats die wordt gebruikt als back-end van de configuratie server, moet worden gestart met `http://`, `https://`, `git@`of `ssh://`. |
| `repos."name"`                     | `yes` op `repos` | Een naam voor het identificeren van één `Git` opslagplaats, alleen __vereist__ als `repos` bestaat. Bijvoorbeeld `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | Een matrix met teken reeksen die wordt gebruikt om een toepassings naam te vinden. Gebruik voor elk patroon de `{application}/{profile}` notatie met Joker tekens. |
| `repos."default-label"`            | `no`             | Het standaard label van de `Git` opslag plaats moet de `branch name`, `tag name`of `commit-id` van de opslag plaats zijn. |
| `repos."search-paths`"             | `no`             | Een matrix met teken reeksen die worden gebruikt voor het zoeken in submappen van de `Git` opslag plaats. |
| `repos."username"`                 | `no`             | De `username` die wordt gebruikt voor toegang tot de `Git` opslagplaats server, __vereist__ wanneer de `Git` opslagplaats server `Http Basic Authentication`ondersteunt. |
| `repos."password"`                 | `no`             | Het wacht woord dat wordt gebruikt voor toegang tot de `Git` opslagplaats server, __vereist__ wanneer de `Git` opslagplaats server `Http Basic Authentication`ondersteunt. |
| `repos."private-key"`              | `no`             | De persoonlijke sleutel van `Ssh` voor toegang tot `Git` opslag plaats, __vereist__ wanneer `uri` met `git@` of `ssh://`begint. |
| `repos."host-key"`                 | `no`             | De host-sleutel van de Git-opslagplaats server mag het algoritme voorvoegsel niet bevatten, zoals wordt gedekt door `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | Het algoritme van de host-sleutel moet `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`of `ecdsa-sha2-nistp521`zijn. Alleen __vereist__ als `host-key` bestaat. |
| `repos."strict-host-key-checking"` | `no`             | Hiermee geeft u op of de configuratie server niet kan worden gestart wanneer de persoonlijke `host-key`worden uitgevoerd. Moet `true` (standaard waarde) of `false`zijn. |

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>De opslag plaats van uw configuratie server koppelen aan Azure lente-Cloud

Nu u uw configuratie bestanden hebt opgeslagen in een opslag plaats, moet u er een Azure lente-cloud aan koppelen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Ga naar de **overzichts** pagina van Azure lente Cloud.

1. Ga naar het tabblad **config server** onder de kop **instellingen** in het menu aan de linkerkant.

![scherm afbeelding van venster](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="input-repository-information-directly-to-the-azure-portal"></a>Gegevens van de opslag plaats rechtstreeks naar de Azure Portal invoeren

#### <a name="default-repository"></a>Standaard opslagplaats

* Open bare opslag plaats: plak in de sectie **standaard opslagplaats** de URI van de opslag plaats in de sectie **URI** en zorg ervoor dat de **verificatie** -instelling **openbaar**is. Klik vervolgens op **Toep assen** om te volt ooien. 

* Privé-opslag plaats: Azure lente-Cloud ondersteunt basis verificatie op basis van wacht woord/tokens en SSH.

    * Basis verificatie: plak in de sectie **standaard opslagplaats** de URI van de opslag plaats in de sectie **URI** en klik vervolgens op de **verificatie**. Selecteer **Basic** als uw **verificatie type** en voer uw gebruikers naam en wacht woord/token in om toegang te verlenen tot Azure lente-Cloud. Klik op **OK** en **Toep assen** om het instellen van de configuratie server te volt ooien.

    ![scherm afbeelding van venster](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Sommige Git-opslagplaats servers zoals GitHub gebruiken een `personal-token` of een `access-token` als een wacht woord voor **basis verificatie**. U kunt dit type token als wacht woord gebruiken in azure lente-Cloud, omdat het nooit verloopt. Maar voor andere Git-opslagplaats servers, zoals BitBucket en Azure DevOps, verloopt de `access-token` binnen een of twee uur. Dit betekent dat de optie niet haalbaar is wanneer u deze opslagplaats servers gebruikt met Azure lente Cloud.]

    * SSH: plak in het gedeelte **standaard opslagplaats** de URI van de opslag plaats in de sectie **URI** en klik vervolgens op de **verificatie**. Selecteer **SSH** als **verificatie type** en voer uw **persoonlijke sleutel**in. U kunt desgewenst uw host- **sleutel** en **host-algoritme**opgeven. Zorg ervoor dat u uw open bare sleutel in uw opslag plaats voor de configuratie Server opneemt. Klik op **OK** en **Toep assen** om het instellen van de configuratie server te volt ooien.

    ![scherm afbeelding van venster](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Opslag plaats voor patronen

Als u een **opslag plaats** voor een optioneel patroon wilt gebruiken om uw service te configureren, geeft u de **URI** en **verificatie** op dezelfde manier op als de **standaard opslagplaats**. Zorg ervoor dat u een **naam** voor uw patroon opgeeft en klik vervolgens op **Toep assen** om deze aan uw exemplaar toe te voegen. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Opslagplaats gegevens invoeren in een YAML-bestand

Als u een YAML-bestand met de instellingen van uw opslag plaats hebt geschreven, kunt u uw YAML-bestand rechtstreeks vanaf uw lokale computer importeren in azure veer Cloud. Een eenvoudig YAML-bestand voor een privé opslagplaats met basis verificatie ziet er als volgt uit:

```yml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Klik op de knop **Instellingen importeren** en selecteer vervolgens het `.yml` bestand in de projectmap. Klik op **importeren**. vervolgens wordt een `async` bewerking uit uw **meldingen** weer gegeven. Na 1-2 minuten zou het succes moeten worden gerapporteerd.

![scherm afbeelding van venster](media/spring-cloud-tutorial-config-server/local-yml-success.png)


U ziet nu de gegevens uit uw YAML-bestand dat wordt weer gegeven in de Azure Portal. Klik op **Toep assen** om te volt ooien. 


## <a name="delete-your-app-configuration"></a>Uw app-configuratie verwijderen

Zodra u een configuratie bestand hebt opgeslagen, wordt de knop **app-configuratie verwijderen** weer gegeven op het tabblad **configuratie** . Hiermee worden de bestaande instellingen volledig gewist. U moet dit doen als u de configuratie server wilt verbinden met een andere bron, zoals het verplaatsen van GitHub naar Azure DevOps.



## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u de configuratie server inschakelt en configureert. Ga door naar de zelf studie voor het hand matig schalen van uw app voor meer informatie over het beheren van uw toepassing.

> [!div class="nextstepaction"]
> [Meer informatie over hoe u de Azure lente-Cloud toepassing hand matig kunt schalen](spring-cloud-tutorial-scale-manual.md).

