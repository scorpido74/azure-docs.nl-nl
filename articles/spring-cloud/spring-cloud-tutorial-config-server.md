---
title: 'Zelfstudie: uw instantie van een Config Server instellen in Azure Spring Cloud'
description: In deze zelfstudie leert u hoe u een instantie van een Spring Cloud Config Server instelt voor uw Azure Spring Cloud in het Azure-portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.openlocfilehash: 4c8b2e92cd7e88dde434e42971d091db689bfbc9
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791296"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Zelfstudie: Een instantie van een Spring Cloud Config Server instellen voor uw service

In dit artikel leest u hoe u een instantie van een Spring Cloud Config Server kunt verbinden met uw Azure Spring Cloud-service.

Spring Cloud Config biedt ondersteuning aan server- en clientzijde voor een geëxternaliseerde configuratie in een gedistribueerd systeem. Met de instantie van Config Server heeft u een centrale plek om externe eigenschappen te beheren voor toepassingen in alle omgevingen. Raadpleeg [Spring Cloud Config Server-referentie](https://spring.io/projects/spring-cloud-config) voor meer informatie.

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 
* Een reeds ingerichte en actieve Azure Spring Cloud-service. Als u een Azure Spring Cloud-service wilt instellen en opstarten, bekijk dan [Quickstart: Een Java Spring-toepassing starten met behulp van de Azure CLI](spring-cloud-quickstart-launch-app-cli.md).

## <a name="restriction"></a>Beperking

Er gelden enkele beperkingen op het gebruik van Config Server met een Git-back-end. Sommige eigenschappen worden automatisch in de omgeving van uw toepassing om toegang te krijgen tot Config Server en Service Discovery. Als u deze eigenschappen ook vanuit uw Config Server-bestanden configureert, kunnen er conflicten en onverwacht gedrag optreden. De eigenschappen zijn: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
spring.jmx.enabled
```

> [!CAUTION]
> We raden u ten zeerste aan de bovenstaande eigenschappen _niet_ op te nemen in de toepassingsbestanden van uw Config Server.

## <a name="create-your-config-server-files"></a>Uw Config Server-bestanden maken

Azure Spring Cloud biedt ondersteuning voor Azure DevOps, GitHub, GitLab en Bitbucket om uw Config Server-bestanden op te slaan. Wanneer uw opslagplaats klaar is, maakt u de configuratiebestanden aan met de volgende instructies en slaat u ze daar op.

Daarnaast zijn sommige configureerbare eigenschappen alleen beschikbaar voor bepaalde typen. In de volgende subsecties vindt u de eigenschappen van elk type opslagplaats.

### <a name="public-repository"></a>Openbare opslagplaats

Wanneer u een openbare opslagplaats gebruikt, zijn uw configureerbare eigenschappen beperkter.

In de volgende tabel vindt u alle configureerbare eigenschappen die worden gebruikt om de openbare Git-opslagplaats in te stellen:

> [!NOTE]
> Het gebruik van een afbreekstreepje (-) als scheidingsteken tussen woorden is momenteel de enige ondersteunde naamconventie. U kunt bijvoorbeeld *standaard-label* gebruiken, maar niet *standaardLabel*.

| Eigenschap        | Vereist | Functie                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ja    | De URI van de Git-opslagplaats die als back-end van de Config Server wordt gebruikt, begint met *http://* , *https://* , *git@* of *ssh://* . |
| `default-label` | Nee     | Het standaardlabel van de Git-opslagplaats moet de *vertakkingsnaam*, *tagnaam*of *commit-id* van de opslagplaat zijn. |
| `search-paths`  | Nee     | Een matrix van tekenreeksen die worden gebruikt om te zoeken in submappen van de Git-opslagplaats. |

------

### <a name="private-repository-with-ssh-authentication"></a>Persoonlijke opslagplaats met SSH-verificatie

In de volgende tabel vindt u alle configureerbare eigenschappen die worden gebruikt om de persoonlijke Git-opslagplaats in te stellen:

> [!NOTE]
> Het gebruik van een afbreekstreepje (-) als scheidingsteken tussen woorden is momenteel de enige ondersteunde naamconventie. U kunt bijvoorbeeld *standaard-label* gebruiken, maar niet *standaardLabel*.

| Eigenschap                   | Vereist | Functie                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Ja    | De URI van de Git-opslagplaats die als back-end van de Config Server wordt gebruikt, moet beginnen met *http://* , *https://* , *git@* of *ssh://* . |
| `default-label`            | Nee     | Het standaardlabel van de Git-opslagplaats moet de *vertakkingsnaam*, *tagnaam*of *commit-id* van de opslagplaat zijn. |
| `search-paths`             | Nee     | Een matrix van tekenreeksen gebruikt om te zoeken in submappen van de Git-opslagplaats. |
| `private-key`              | Nee     | De persoonlijke SSH-sleutel om de Git-opslagplaats te openen, _vereist_ wanneer de URI begint met *git@* of *ssh://* . |
| `host-key`                 | Nee     | De hostsleutel van de server voor de Git-opslagplaats mag het algoritmevoorvoegsel niet bevatten, zoals beschreven in `host-key-algorithm`. |
| `host-key-algorithm`       | Nee     | Het algoritme van de hostsleutel moet *ssh-dss-* zijn, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384* of *ecdsa-sha2-nistp521* zijn. Enkel *vereist* als `host-key` bestaat. |
| `strict-host-key-checking` | Nee     | Geeft aan of het opstarten van de instantie van de Config Server zal mislukken wanneer de persoonlijke `host-key` gebruikt wordt. Moet *true* (standaardwaarde) of *false* zijn. |

-----

### <a name="private-repository-with-basic-authentication"></a>Persoonlijke opslagplaats met basisverificatie

Hieronder vindt u alle configureerbare eigenschappen die worden gebruikt om de persoonlijke Git-opslagplaats met basisverificatie in te stellen.

> [!NOTE]
> Het gebruik van een afbreekstreepje (-) als scheidingsteken tussen woorden is momenteel de enige ondersteunde naamconventie. Gebruik bijvoorbeeld *standaard-label*, niet *standaardLabel*.

| Eigenschap        | Vereist | Functie                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ja    | De URI van de Git-opslagplaats die als back-end van de Config Server wordt gebruikt, moet beginnen met *http://* , *https://* , *git@* of *ssh://* . |
| `default-label` | Nee     | Het standaardlabel van de Git-opslagplaats moet de *vertakkingsnaam*, *tagnaam*of *commit-id* van de opslagplaat zijn. |
| `search-paths`  | Nee     | Een matrix van tekenreeksen gebruikt om te zoeken in submappen van de Git-opslagplaats. |
| `username`      | Nee     | De gebruikersnaam die wordt gebruikt om toegang te krijgen tot de server van de Git-opslagplaats, _vereist_ wanneer de server van de Git-opslagplaats `Http Basic Authentication`ondersteunt. |
| `password`      | Nee     | Het wachtwoord dat wordt gebruikt om toegang te krijgen tot de server van de Git-opslagplaats, _vereist_ wanneer de server van de Git-opslagplaats `Http Basic Authentication`ondersteunt. |

> [!NOTE]
> Veel servers voor `Git`-opslagplaatsen bieden ondersteuning voor het gebruik van tokens in plaats van wachtwoorden voor HTTP-basisverificatie. Sommige opslagplaatsen, zoals GitHub staan toe om tokens voor onbepaalde tijd te gebruiken. Sommige servers voor Git-opslagplaatsen, zoals Azure DevOps, dwingen tokens om binnen enkele uren te vervallen. Opslagplaatsen die ervoor zorgen dat tokens verlopen, mogen geen verificatie op basis van tokens gebruiken met Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Git-opslagplaatsen met patroon

Hieronder vindt u alle configureerbare eigenschappen die worden gebruikt om Git-opslagplaatsen in te stellen met een patroon.

> [!NOTE]
> Het gebruik van een afbreekstreepje (-) als scheidingsteken tussen woorden is momenteel de enige ondersteunde naamconventie. Gebruik bijvoorbeeld *standaard-label*, niet *standaardLabel*.

| Eigenschap                           | Vereist         | Functie                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Nee             | Een toewijzing bestaande uit de instellingen voor een Git-opslagplaats met een bepaalde naam. |
| `repos."uri"`                      | Ja voor `repos` | De URI van de Git-opslagplaats die als back-end van de Config Server wordt gebruikt, moet beginnen met *http://* , *https://* , *git@* of *ssh://* . |
| `repos."name"`                     | Ja voor `repos` | Een naam om te identificeren in de Git-opslagplaats, alleen _vereist_ als `repos` bestaat. Bijvoorbeeld *team-A*, *team-B*. |
| `repos."pattern"`                  | Nee             | Een matrix van tekenreeksen gebruikt om een toepassingsnaam te vinden. Gebruik het `{application}/{profile}`-formaat met jokertekens voor elk patroon. |
| `repos."default-label"`            | Nee             | Het standaardlabel van de Git-opslagplaats moet de *vertakkingsnaam*, *tagnaam*of *commit-id* van de opslagplaat zijn. |
| `repos."search-paths`"             | Nee             | Een matrix van tekenreeksen gebruikt om te zoeken in submappen van de Git-opslagplaats. |
| `repos."username"`                 | Nee             | De gebruikersnaam die wordt gebruikt om toegang te krijgen tot de server van de Git-opslagplaats, _vereist_ wanneer de server van de Git-opslagplaats `Http Basic Authentication`ondersteunt. |
| `repos."password"`                 | Nee             | Het wachtwoord dat wordt gebruikt om toegang te krijgen tot de server van de Git-opslagplaats, _vereist_ wanneer de server van de Git-opslagplaats `Http Basic Authentication`ondersteunt. |
| `repos."private-key"`              | Nee             | De persoonlijke SSH-sleutel om de Git-opslagplaats te openen, _vereist_ wanneer de URI begint met *git@* of *ssh://* . |
| `repos."host-key"`                 | Nee             | De hostsleutel van de server voor de Git-opslagplaats mag het algoritmevoorvoegsel niet bevatten, zoals beschreven in `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | Nee             | Het algoritme van de hostsleutel moet *ssh-dss-* zijn, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384* of *ecdsa-sha2-nistp521* zijn. Enkel *vereist* als `host-key` bestaat. |
| `repos."strict-host-key-checking"` | Nee             | Geeft aan of het opstarten van de instantie van de Config Server zal mislukken wanneer de persoonlijke `host-key` gebruikt wordt. Moet *true* (standaardwaarde) of *false* zijn. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Uw Config Server-opslagplaats toevoegen aan Azure Spring Cloud

Nu dat uw configuratiebestanden zijn opgeslagen in een opslagplaats, moet u Azure Spring Cloud ermee verbinden.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Ga naar de pagina **Overzicht** van Azure Spring Cloud.

1. Selecteer de service die u wilt configureren.

1. Selecteer onder **Instellingen** het tabblad **Config Server** in het linkervenster van de servicepagina.

![Het venster Config Server](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>De gegevens van de opslagplaats rechtstreeks invoeren bij de Azure-portal

#### <a name="default-repository"></a>Standaardopslagplaats

* **Openbare opslagplaats**: Plak de URI van de opslagplaats in het vak **URI** in de sectie **Standaardopslagplaats**.  Stel het **Label** in op **config**. Zorg ervoor dat de instelling **Verificatie** op **Openbaar** staat en selecteer vervolgen **Toepassen** om te voltooien. 

* **Persoonlijke opslagplaats**: Azure Spring Cloud biedt ondersteuning voor basisverificatie op basis van wachtwoorden/tokens en SSH.

    * **Basisverificatie**: Plak de URI van de opslagplaats in de sectie **Standaardopslagplaats** in het vak **URI** en selecteer vervolgens de knop **Verificatie** (pictogram van potlood). Selecteer **HTTP-basis** in het venster **Verificatie bewerken** in de vervolgkeuzelijst **Verificatietype** en voer vervolgens uw gebruikersnaam en wachtwoord/token in om toegang te verlenen tot Azure Spring Cloud. Selecteer **OK** en vervolgens **Toepassen** om de instelling van uw instantie van een Config Server te voltooien.

    ![Het deelvenster Verificatie bewerken](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Sommige servers voor Git-opslagplaatsen, zoals GitHub, gebruiken een *persoonlijk-token* of een *toegangs-token*, zoals een wachtwoord, voor **Basisverificatie**. U kunt dit soort token gebruiken als wachtwoord in Azure Spring Cloud aangezien het nooit verloopt. Maar voor andere servers voor Git-opslagplaatsen, zoals Bitbucket en Azure DevOps, verloopt het *toegangs-token* binnen één of twee uur. Dit betekent dat de optie niet werkbaar is wanneer u die servers voor opslagplaatsen gebruikt met Azure Spring Cloud.

    * **SSH**: Plak de URI van de opslagplaats in het vak **URI** in de sectie **Standaardopslagplaats** en selecteer vervolgens de knop **Verificatie** (pictogram van potlood). Selecteer **SSH** in de vervolgkeuzelijst **Verificatietype** in het deelvenster **Verificatie bewerken** en voer vervolgens uw **Persoonlijke sleutel** in. U kunt eventueel uw **Hostsleutel** en **Algoritme van de hostsleutel** opgeven. Zorg ervoor dat u uw openbare sleutel in uw opslagplaats voor de Config Server opneemt. Selecteer **OK** en vervolgens **Toepassen** om de instelling van uw instantie van een Config Server te voltooien.

    ![Het deelvenster Verificatie bewerken](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Opslagplaats voor patronen

Als u een optionele **Opslagplaats voor patronen** wilt gebruiken om uw service te configureren, geef dan de **URI** en **Verificatie** op zoals voor de **Standaardopslagplaats**. Vergeet geen **Naam** op te geven voor uw patroon en selecteer vervolgens **Toepassen** om deze aan uw instantie toe te voegen. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Informatie over en opslagplaats invoeren in een YAML-bestand

Als u een YAML-bestand heeft geschreven met instellingen voor uw opslagplaats, dan kunt u het bestand rechtstreeks importeren vanaf uw lokale machine naar Azure Spring Cloud. Een eenvoudig YAML-bestand voor een persoonlijke opslagplaats met basisverificatie ziet er als volgt uit:

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

Klik op de knop **Instellingen importeren** en selecteer vervolgens het YAML-bestand in uw projectmap. Selecteer **Importeren**, vervolgens verschijnt er een `async`-bewerking van uw **Meldingen**. Na 1-2 minuten zou de bewerking voltooid moeten zijn.

![Het deelvenster Meldingen van de Config Server](media/spring-cloud-tutorial-config-server/local-yml-success.png)


De informatie uit uw YAML-bestand zou moeten worden weergegeven in het Azure-portal. Selecteer **Toepassen** om te voltooien. 


## <a name="delete-your-app-configuration"></a>Uw app-configuratie verwijderen

Nadat u een configuratiebestand hebt opgeslagen verschijnt de knop **App-configuratie verwijderen** bij het tabblad **Configuratie**. Wanneer u deze knop selecteert wist u uw bestaande instellingen volledig. Gebruik deze om uw instantie van een Config Server te verbinden met een andere bron, bijvoorbeeld wanneer u overstapt van Github naar Azure DevOps.



## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie leert u hoe u uw instantie van een Spring Cloud Config Server kunt inschakelen en configureren. Ga verder met de zelfstudie over het handmatig schalen van uw app voor meer informatie over het beheer van uw toepassing.

> [!div class="nextstepaction"]
> [Zelfstudie: een toepassing schalen in Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md)
