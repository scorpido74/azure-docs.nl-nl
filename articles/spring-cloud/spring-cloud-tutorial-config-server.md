---
title: Uw configuratie Server-exemplaar instellen in azure lente-Cloud | Microsoft Docs
description: In deze zelf studie leert u hoe u een lente-Cloud configuratie Server-exemplaar instelt voor uw Azure lente-Cloud op de Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/18/2019
ms.openlocfilehash: 7589a3a750e2fe04736bb3c8fc072c7a2c0a7358
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147550"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Zelf studie: een lente Cloud config server-exemplaar instellen voor uw service

In dit artikel wordt beschreven hoe u een bron van een lente-Cloud configuratie server verbindt met uw Azure lente-Cloud service.

Lente Cloud configuratie biedt server-en client ondersteuning voor een externe configuratie in een gedistribueerd systeem. Met het configuratie Server-exemplaar hebt u een centrale plaats voor het beheren van externe eigenschappen voor toepassingen in alle omgevingen. Zie voor meer informatie de [verwijzing lente-Cloud configuratie server](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 
* Een reeds ingerichte en actieve Azure lente-Cloud service. Als u een Azure lente-Cloud service wilt instellen en starten, raadpleegt u [Quick Start: een Java-lente toepassing starten met behulp van de Azure cli](spring-cloud-quickstart-launch-app-cli.md).

## <a name="restriction"></a>Beperking

Er zijn enkele beperkingen bij het gebruik van de configuratie server met een Git-back-end. Sommige eigenschappen worden automatisch in uw toepassings omgeving geïnjecteerd om toegang te krijgen tot de configuratie server en de service detectie. Als u deze eigenschappen ook van uw config server-bestanden configureert, kunnen er conflicten optreden en onverwachte gedrag. De eigenschappen zijn onder andere: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```

> [!CAUTION]
> We raden u ten zeerste aan de bovenstaande eigenschappen _niet_ in uw configuratie server toepassings bestanden in te stellen.

## <a name="create-your-config-server-files"></a>Uw config server-bestanden maken

Azure lente Cloud ondersteunt Azure DevOps, GitHub, GitLab en bitbucket om uw config server-bestanden op te slaan. Wanneer u de opslag plaats gereed hebt, maakt u de configuratie bestanden met de volgende instructies en slaat u ze daar op.

Daarnaast zijn sommige Configureer bare eigenschappen alleen beschikbaar voor bepaalde typen. In de volgende subsecties worden de eigenschappen van elk type opslag plaats weer geven.

### <a name="public-repository"></a>Open bare opslag plaats

Wanneer u een open bare opslag plaats gebruikt, zijn uw Configureer bare eigenschappen beperkter.

In de volgende tabel vindt u alle Configureer bare eigenschappen die worden gebruikt voor het instellen van de open bare Git-opslag plaats:

> [!NOTE]
> Het gebruik van een afbreek streepje (-) voor het scheiden van woorden is de enige naam Conventie die momenteel wordt ondersteund. U kunt bijvoorbeeld *standaard label*gebruiken, maar niet *defaultLabel*.

| Eigenschap        | Vereist | Functie                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ja    | De URI van de Git-opslag plaats die wordt gebruikt als back-end van de configuratie server begint met *http://* , *https://* , *Git@* of *SSH://* . |
| `default-label` | Nee     | Het standaard label van de Git-opslag plaats moet de *vertakkings naam*, de naam van de *tag*of de *door Voer-id* van de opslag plaats zijn. |
| `search-paths`  | Nee     | Een matrix met teken reeksen die worden gebruikt voor het zoeken in submappen van de Git-opslag plaats. |

------

### <a name="private-repository-with-ssh-authentication"></a>Privé-opslag plaats met SSH-verificatie

Alle Configureer bare eigenschappen die worden gebruikt voor het instellen van een persoonlijke Git-opslag plaats met SSH, worden weer gegeven in de volgende tabel:

> [!NOTE]
> Het gebruik van een afbreek streepje (-) voor het scheiden van woorden is de enige naam Conventie die momenteel wordt ondersteund. U kunt bijvoorbeeld *standaard label*gebruiken, maar niet *defaultLabel*.

| Eigenschap                   | Vereist | Functie                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Ja    | De URI van de Git-opslag plaats die wordt gebruikt als de back-end van de configuratie server, moet worden gestart met *http://* , *https://* , *Git@* of *SSH://* . |
| `default-label`            | Nee     | Het standaard label van de Git-opslag plaats moet de *vertakkings naam*, de naam van de *tag*of de *door Voer-id* van de opslag plaats zijn. |
| `search-paths`             | Nee     | Een matrix met teken reeksen die worden gebruikt voor het zoeken in submappen van de Git-opslag plaats. |
| `private-key`              | Nee     | De persoonlijke SSH-sleutel voor toegang tot de Git-opslag plaats, _vereist_ wanneer de URI begint met *Git@* of *SSH://* . |
| `host-key`                 | Nee     | De host-sleutel van de Git-opslagplaats server mag het algoritme voorvoegsel niet bevatten, zoals wordt gedekt door `host-key-algorithm`. |
| `host-key-algorithm`       | Nee     | Het algoritme van de host-sleutel moet *ssh-DSS*, *ssh-rsa*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384*of *ECDSA-SHA2-nistp521*zijn. Alleen *vereist* als `host-key` bestaat. |
| `strict-host-key-checking` | Nee     | Hiermee wordt aangegeven of het config server-exemplaar niet kan worden gestart wanneer de privé-`host-key`worden uitgevoerd. Moet *True* (standaard waarde) of *False*zijn. |

-----

### <a name="private-repository-with-basic-authentication"></a>Privé-opslag plaats met basis verificatie

Alle Configureer bare eigenschappen die worden gebruikt voor het instellen van een persoonlijke Git-opslag plaats met basis verificatie, worden hieronder weer gegeven.

> [!NOTE]
> Het gebruik van een afbreek streepje (-) voor het scheiden van woorden is de enige naam Conventie die momenteel wordt ondersteund. Gebruik bijvoorbeeld *standaard label*, niet *defaultLabel*.

| Eigenschap        | Vereist | Functie                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ja    | De URI van de Git-opslag plaats die als back-end van de configuratie server wordt gebruikt, moet worden gestart met *http://* , *https://* , *Git@* of *SSH://* . |
| `default-label` | Nee     | Het standaard label van de Git-opslag plaats moet de *vertakkings naam*, de naam van de *tag*of de *door Voer-id* van de opslag plaats zijn. |
| `search-paths`  | Nee     | Een matrix met teken reeksen die worden gebruikt voor het zoeken in submappen van de Git-opslag plaats. |
| `username`      | Nee     | De gebruikers naam die wordt gebruikt voor toegang tot de Git-opslagplaats server, _vereist_ wanneer de Git-opslagplaats server `Http Basic Authentication`ondersteunt. |
| `password`      | Nee     | Het wacht woord dat wordt gebruikt voor toegang tot de Git-opslagplaats server, _vereist_ wanneer de Git-opslagplaats server `Http Basic Authentication`ondersteunt. |

> [!NOTE]
> Veel `Git` opslagplaats servers bieden ondersteuning voor het gebruik van tokens in plaats van wacht woorden voor HTTP-basis verificatie. Voor sommige opslag plaatsen, zoals GitHub, kunnen tokens voor onbepaalde tijd blijven bestaan. Sommige Git-opslagplaats servers, met inbegrip van Azure DevOps, moeten tokens binnen een paar uur laten verlopen. Opslag plaatsen die ervoor zorgen dat tokens verlopen, mogen geen authenticatie op basis van tokens gebruiken met Azure veer Cloud.

### <a name="git-repositories-with-pattern"></a>Git-opslag plaatsen met patroon

Hieronder vindt u alle Configureer bare eigenschappen die worden gebruikt om Git-opslag plaatsen in te stellen met een patroon.

> [!NOTE]
> Het gebruik van een afbreek streepje (-) voor het scheiden van woorden is de enige naam Conventie die momenteel wordt ondersteund. Gebruik bijvoorbeeld *standaard label*, niet *defaultLabel*.

| Eigenschap                           | Vereist         | Functie                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Nee             | Een toewijzing die bestaat uit de instellingen voor een Git-opslag plaats met een opgegeven naam. |
| `repos."uri"`                      | Ja op `repos` | De URI van de Git-opslag plaats die als back-end van de configuratie server wordt gebruikt, moet worden gestart met *http://* , *https://* , *Git@* of *SSH://* . |
| `repos."name"`                     | Ja op `repos` | Een naam voor het identificeren van de Git-opslag plaats, alleen _vereist_ als `repos` bestaat. Bijvoorbeeld *team a*, *Team-B*. |
| `repos."pattern"`                  | Nee             | Een matrix met teken reeksen die wordt gebruikt om een toepassings naam te vinden. Gebruik voor elk patroon de `{application}/{profile}` notatie met Joker tekens. |
| `repos."default-label"`            | Nee             | Het standaard label van de Git-opslag plaats moet de *vertakkings naam*, de naam van de *tag*of de *door Voer-id* van de opslag plaats zijn. |
| `repos."search-paths`"             | Nee             | Een matrix met teken reeksen die worden gebruikt voor het zoeken in submappen van de Git-opslag plaats. |
| `repos."username"`                 | Nee             | De gebruikers naam die wordt gebruikt voor toegang tot de Git-opslagplaats server, _vereist_ wanneer de Git-opslagplaats server `Http Basic Authentication`ondersteunt. |
| `repos."password"`                 | Nee             | Het wacht woord dat wordt gebruikt voor toegang tot de Git-opslagplaats server, _vereist_ wanneer de Git-opslagplaats server `Http Basic Authentication`ondersteunt. |
| `repos."private-key"`              | Nee             | De persoonlijke SSH-sleutel voor toegang tot de Git-opslag plaats, _vereist_ wanneer de URI begint met *Git@* of *SSH://* . |
| `repos."host-key"`                 | Nee             | De host-sleutel van de Git-opslagplaats server mag het algoritme voorvoegsel niet bevatten, zoals wordt gedekt door `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | Nee             | Het algoritme van de host-sleutel moet *ssh-DSS*, *ssh-rsa*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384*of *ECDSA-SHA2-nistp521*zijn. Alleen *vereist* als `host-key` bestaat. |
| `repos."strict-host-key-checking"` | Nee             | Hiermee wordt aangegeven of het config server-exemplaar niet kan worden gestart wanneer de privé-`host-key`worden uitgevoerd. Moet *True* (standaard waarde) of *False*zijn. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Uw opslag plaats voor de configuratie server koppelen aan Azure lente-Cloud

Nu uw configuratie bestanden zijn opgeslagen in een opslag plaats, moet u er verbinding mee maken met Azure lente.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Ga naar de **overzichts** pagina van Azure lente Cloud.

1. Selecteer de service die u wilt configureren.

1. Selecteer in het linkerdeel venster van de service pagina onder **instellingen**het tabblad **Configuratie server** .

![Het venster van de configuratie server](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Voer de gegevens van de opslag plaats rechtstreeks in bij de Azure Portal

#### <a name="default-repository"></a>Standaard opslagplaats

* **Open bare opslag plaats**: plak in het vak **URI** in de sectie **standaard opslagplaats** de URI van de opslag plaats.  Stel het **Label** in op **config**. Zorg ervoor dat de **verificatie** -instelling **openbaar**is en selecteer vervolgens **Toep assen** om te volt ooien. 

* **Privé-opslag plaats**: Azure lente-Cloud ondersteunt basis verificatie op basis van wacht woorden/tokens en SSH.

    * **Basis verificatie**: in de sectie **standaard opslagplaats** , in het vak **URI** , PLAKT u de opslag plaats-URI en selecteert u vervolgens het pictogram voor **verificatie** (potlood). Selecteer in het deel venster **verificatie bewerken** in de vervolg keuzelijst **verificatie type** de optie **http Basic**en voer uw gebruikers naam en wacht woord/token in om toegang te verlenen tot de Azure lente-Cloud. Selecteer **OK**en selecteer vervolgens **Toep assen** om het instellen van uw configuratie Server-exemplaar te volt ooien.

    ![Het deel venster verificatie bewerken](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Sommige Git-opslagplaats servers, zoals GitHub, gebruiken een *persoonlijk token* of een *toegangs token*, zoals een wacht woord, voor **basis verificatie**. U kunt dit type token als wacht woord gebruiken in azure lente-Cloud, omdat het nooit verloopt. Maar voor andere Git-opslagplaats servers, zoals bitbucket en Azure DevOps, verloopt het *toegangs token* in één of twee uur. Dit betekent dat de optie niet haalbaar is wanneer u deze opslagplaats servers gebruikt met Azure veer Cloud.

    * **SSH**: in de sectie **standaard opslagplaats** , in het vak **URI** , PLAKT u de opslag plaats-URI en selecteert u vervolgens het pictogram voor **verificatie** (potlood). In het deel venster **verificatie bewerken** in de vervolg keuzelijst **verificatie type** selecteert u **SSH**en voert u vervolgens uw **persoonlijke sleutel**in. U kunt desgewenst uw **host-sleutel** en **host-algoritme**opgeven. Zorg ervoor dat u uw open bare sleutel in uw opslag plaats voor de configuratie Server opneemt. Selecteer **OK**en selecteer vervolgens **Toep assen** om het instellen van uw configuratie Server-exemplaar te volt ooien.

    ![Het deel venster verificatie bewerken](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Opslag plaats voor patronen

Als u een **opslag plaats** voor een optioneel patroon wilt gebruiken om uw service te configureren, geeft u de **URI** en **verificatie** op dezelfde manier op als de **standaard opslagplaats**. Zorg ervoor dat u een **naam** voor het patroon opgeeft en selecteer vervolgens **Toep assen** om de app aan uw exemplaar toe te voegen. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Opslagplaats gegevens invoeren in een YAML-bestand

Als u een YAML-bestand hebt geschreven met de instellingen van uw opslag plaats, kunt u het bestand rechtstreeks importeren van uw lokale computer naar Azure veer Cloud. Een eenvoudig YAML-bestand voor een privé opslagplaats met basis verificatie ziet er als volgt uit:

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

Selecteer de knop **Instellingen importeren** en selecteer vervolgens het yaml-bestand in de projectmap. Selecteer **importeren**en vervolgens wordt een `async` bewerking uit uw **meldingen** weer gegeven. Na 1-2 minuten zou het succes moeten worden gerapporteerd.

![Het deel venster Configuratie server meldingen](media/spring-cloud-tutorial-config-server/local-yml-success.png)


De gegevens uit uw YAML-bestand moeten worden weer gegeven in de Azure Portal. Selecteer **Toep assen** om te volt ooien. 


## <a name="delete-your-app-configuration"></a>Uw app-configuratie verwijderen

Nadat u een configuratie bestand hebt opgeslagen, wordt de knop **app-configuratie verwijderen** weer gegeven op het tabblad **configuratie** . Als u deze knop selecteert, worden uw bestaande instellingen volledig gewist. U moet deze selecteren als u het config server-exemplaar wilt verbinden met een andere bron, zoals het verplaatsen van GitHub naar Azure DevOps.



## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw lente Cloud config server-exemplaar kunt inschakelen en configureren. Ga verder met de zelf studie over het hand matig schalen van uw app voor meer informatie over het beheren van uw toepassing.

> [!div class="nextstepaction"]
> [Zelf studie: een toepassing schalen in een Azure lente-Cloud](spring-cloud-tutorial-scale-manual.md)
