---
title: Uw configuratie server instellen in azure lente Cloud | Microsoft Docs
description: In deze zelf studie leert u hoe u een lente-Cloud configuratie server kunt instellen voor uw Azure lente-Cloud op de Azure Portal
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 08/08/2019
ms.openlocfilehash: f959d216859ded54de52de474ee3150f91bcfff1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038921"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Zelfstudie: Stel een lente-Cloud configuratie server in voor uw service

In deze zelf studie wordt uitgelegd hoe u een lente-Cloud configuratie server verbindt met uw Azure lente-Cloud service.

Lente Cloud configuratie biedt server-en client ondersteuning voor externe configuratie in een gedistribueerd systeem. Met de configuratie server beschikt u over een centrale plek voor het beheren van externe eigenschappen voor toepassingen in alle omgevingen. Ga voor meer informatie naar de [verwijzing naar de lente-Cloud configuratie server](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 
* Een reeds ingerichte en actieve Azure lente-Cloud service.  Voltooi [deze Snelstartgids](spring-cloud-quickstart-launch-app-cli.md) om een Azure lente-Cloud service in te richten en te starten.


## <a name="restriction"></a>Beperking

Er zijn enkele beperkingen bij het gebruik van een __Configuratie server__ met een Git-back-end. Sommige eigenschappen worden automatisch toegevoegd aan uw toepassings omgeving om toegang te krijgen tot de __Configuratie server__ en de __service detectie__. Als u deze eigenschappen ook van uw **config server** -bestanden configureert, kunnen er conflicten en onverwacht gedrag optreden.  De eigenschappen zijn onder andere: 

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

Azure lente Cloud ondersteunt Azure DevOps, GitHub, GitLab en bitbucket om uw config server-bestanden op te slaan.  Wanneer u de opslag plaats gereed hebt, maakt u de configuratie bestanden met de onderstaande instructies en slaat u deze op.

Bovendien zijn sommige Configureer bare eigenschappen alleen beschikbaar voor sommige typen. In de volgende subsecties worden de eigenschappen van elk type opslag plaats weer geven.


### <a name="public-repository"></a>Open bare opslag plaats

Wanneer u een open bare opslag plaats gebruikt, zijn uw Configureer bare eigenschappen beperkter.

Alle Configureer bare eigenschappen die worden gebruikt voor het instellen van de opslag plaats Public `Git`, worden hieronder weer gegeven.

> [!NOTE]
> Alleen afbreek streepjes (-) de naam Conventie gescheiden woorden wordt nu ondersteund. U kunt bijvoorbeeld `default-label` gebruiken, maar niet `defaultLabel`.

| Eigenschap        | Verplicht | Functie                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | De `uri` van de `Git`-opslag plaats die als back-end van de configuratie server wordt gebruikt, moet worden gestart met `http://`, `https://`, `git@` of `ssh://`. |
| `default-label` | `no`     | Het standaard label van de opslag plaats @no__t 0 moet `branch name`, `tag name` of `commit-id` aan opslag plaats zijn. |
| `search-paths`  | `no`     | Een matrix met teken reeksen die worden gebruikt voor het zoeken in submappen van de opslag plaats @no__t 0. |

------

### <a name="private-repository-with-ssh-authentication"></a>Privé-opslag plaats met SSH-verificatie

Alle Configureer bare eigenschappen die worden gebruikt voor het instellen van een persoonlijke `Git`-opslag plaats met `Ssh`, worden hieronder weer gegeven.

> [!NOTE]
> Alleen afbreek streepjes (-) de naam Conventie gescheiden woorden wordt nu ondersteund. U kunt bijvoorbeeld `default-label` gebruiken, maar niet `defaultLabel`.

| Eigenschap                   | Verplicht | Functie                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | De `uri` van de `Git`-opslag plaats die als back-end van de configuratie server wordt gebruikt, moet worden gestart met `http://`, `https://`, `git@` of `ssh://`. |
| `default-label`            | `no`     | Het standaard label van de opslag plaats @no__t 0 moet `branch name`, `tag name` of `commit-id` aan opslag plaats zijn. |
| `search-paths`             | `no`     | Een matrix met teken reeksen die wordt gebruikt om submappen van de opslag plaats `Git` te doorzoeken. |
| `private-key`              | `no`     | De `Ssh` persoonlijke sleutel voor toegang tot de `Git`-opslag plaats, __vereist__ wanneer `uri` is gestart met `git@` of `ssh://`. |
| `host-key`                 | `no`     | De host-sleutel van de Git-opslagplaats server mag het algoritme voorvoegsel niet bevatten, zoals wordt gedekt door `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | Het algoritme van de host-sleutel moet een van `ssh-dss` zijn. `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` en `ecdsa-sha2-nistp521`. Alleen vereist als `host-key` bestaat. |
| `strict-host-key-checking` | `no`     | Hiermee geeft u op of de configuratie server niet kan worden gestart wanneer de levering `host-key` wordt gebruikt, moet `true` (standaard waarde) of `false` zijn. |

-----

### <a name="private-repository-with-basic-authentication"></a>Privé-opslag plaats met basis verificatie

Alle Configureer bare eigenschappen die worden gebruikt voor het instellen van een persoonlijke Git-opslag plaats met basis verificatie, worden hieronder weer gegeven.

> [!NOTE]
> Alleen afbreek streepjes (-) de naam Conventie gescheiden woorden wordt nu ondersteund. U kunt bijvoorbeeld `default-label` gebruiken, maar niet `defaultLabel`.

| Eigenschap        | Verplicht | Functie                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | De `uri` van de `Git`-opslag plaats die als back-end van de configuratie server wordt gebruikt, moet worden gestart met `http://`, `https://`, `git@` of `ssh://`. |
| `default-label` | `no`     | Het standaard label van de opslag plaats @no__t 0 moet `branch name`, `tag name` of `commit-id` aan opslag plaats zijn. |
| `search-paths`  | `no`     | Een matrix met teken reeksen die wordt gebruikt om submappen van de opslag plaats `Git` te doorzoeken. |
| `username`      | `no`     | De `username` die wordt gebruikt om toegang te krijgen tot de `Git`-opslagplaats server, __vereiste__ `Git` opslagplaats server ondersteuning `Http Basic Authentication`. |
| `password`      | `no`     | Het wacht woord dat wordt gebruikt voor toegang tot `Git`-opslagplaats server, __vereiste__ `Git`-opslagplaats server ondersteuning `Http Basic Authentication`. |

> [!NOTE]
> Sommige opslag servers met @no__t 0, zoals GitHub, ondersteunen een ' persoonlijk token ' of ' toegangs token ' als wacht woord voor `HTTP Basic Authentication`. U kunt dit type token gebruiken als wacht woord en het ' persoonlijk token ' of ' toegangs token ' verloopt niet. Maar voor git-opslagplaats servers, zoals BitBucket en Azure DevOps, verloopt het token binnen een of twee uur, waardoor deze optie niet haalbaar is voor gebruik met Azure lente-Cloud.

### <a name="git-repositories-with-pattern"></a>Git-opslag plaatsen met patroon

Hieronder vindt u alle Configureer bare eigenschappen die worden gebruikt om Git-opslag plaatsen in te stellen met een patroon.

> [!NOTE]
> Alleen afbreek streepjes (-) de naam Conventie gescheiden woorden wordt nu ondersteund. U kunt bijvoorbeeld `default-label` gebruiken, maar niet `defaultLabel`.

| Eigenschap                           | Verplicht         | Functie                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | Een kaart bestaat uit `Git` opslag plaatsen instellingen met de opgegeven naam. |
| `repos."uri"`                      | `yes` op `repos` | De `uri` van de `Git`-opslag plaats die als back-end van de configuratie server wordt gebruikt, moet worden gestart met `http://`, `https://`, `git@` of `ssh://`. |
| `repos."name"`                     | `yes` op `repos` | Een naam voor het identificeren van een opslag plaats van @no__t 0, alleen __vereist__ als `repos` bestaat. Bijvoorbeeld van boven, `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | Een matrix van teken reeks die wordt gebruikt om de naam van de toepassing overeen te laten komen voor elk patroon `{application}/{profile}`-indeling met Joker tekens. |
| `repos."default-label"`            | `no`             | Het standaard label van de opslag plaats @no__t 0 moet `branch name`, `tag name` of `commit-id` aan opslag plaats zijn. |
| `repos."search-paths`"             | `no`             | Een matrix met teken reeksen die wordt gebruikt om submappen van de opslag plaats `Git` te doorzoeken. |
| `repos."username"`                 | `no`             | De `username` die wordt gebruikt om toegang te krijgen tot de `Git`-opslagplaats server, __vereiste__ `Git` opslagplaats server ondersteuning `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | Het wacht woord dat wordt gebruikt voor toegang tot `Git`-opslagplaats server, __vereiste__ `Git`-opslagplaats server ondersteuning `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | De `Ssh` persoonlijke sleutel voor toegang tot de `Git`-opslag plaats, __vereist__ wanneer `uri` is gestart met `git@` of `ssh://`. |
| `repos."host-key"`                 | `no`             | De host-sleutel van de Git-opslagplaats server mag het algoritme voorvoegsel niet bevatten, zoals wordt gedekt door `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | Het algoritme van de host-sleutel moet een van `ssh-dss` zijn. `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` en `ecdsa-sha2-nistp521`. Alleen __vereist__ als `host-key` bestaat. |
| `repos."strict-host-key-checking"` | `no`             | Hiermee geeft u op of de configuratie server niet kan worden gestart wanneer de levering `host-key` wordt gebruikt, moet `true` (standaard waarde) of `false` zijn. |

### <a name="import-applicationyml-file-from-spring-cloud-config"></a>@No__t-0-bestand importeren uit lente-Cloud configuratie

U kunt een aantal standaard instellingen voor de configuratie server rechtstreeks importeren vanuit de [lente-Cloud configuratie](https://spring.io/projects/spring-cloud-config) website. U kunt dit rechtstreeks doen vanuit de Azure Portal, dus u hoeft geen stappen te nemen om de bestanden of opslag plaats van uw config server voor te bereiden.

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>De opslag plaats van uw configuratie server koppelen aan Azure lente-Cloud

Nu u uw configuratie bestanden hebt opgeslagen in een opslag plaats, moet u er een Azure lente-cloud aan koppelen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Ga naar de **overzichts** pagina van Azure lente Cloud.

1. Ga naar het tabblad **config server** onder de kop **instellingen** in het menu aan de linkerkant.

### <a name="public-repository"></a>Open bare opslag plaats

Als uw opslag plaats openbaar is, klikt u gewoon op de knop **openbaar** en plakt u de URL.

### <a name="private-repository"></a>Privé-opslag plaats

Azure lente-Cloud ondersteunt SSH-verificatie. Volg de instructies op de Azure Portal voor het toevoegen van de open bare sleutel aan uw opslag plaats. Zorg er vervolgens voor dat u uw persoonlijke sleutel in het configuratie bestand opneemt.

Klik op **Toep assen** om het instellen van uw configuratie server te volt ooien.


## <a name="delete-your-app-configuration"></a>Uw app-configuratie verwijderen

Zodra u een configuratie bestand hebt opgeslagen, wordt de knop **app-configuratie verwijderen** weer gegeven op het tabblad **configuratie** . Hiermee worden de bestaande instellingen volledig gewist. U moet dit doen als u de configuratie server wilt verbinden met een andere bron, zoals het verplaatsen van GitHub naar Azure DevOps.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u de configuratie server inschakelt en configureert. Ga door naar de zelf studie voor het hand matig schalen van uw app voor meer informatie over het beheren van uw toepassing.

> [!div class="nextstepaction"]
> [Meer informatie over hoe u de Azure lente-Cloud toepassing hand matig kunt schalen](spring-cloud-tutorial-scale-manual.md).

