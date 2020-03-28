---
title: Zelfstudie - Uw Config Server-exemplaar instellen in Azure Spring Cloud
description: In deze zelfstudie leert u hoe u een Spring Cloud Config Server-exemplaar instelt voor uw Azure Spring Cloud op de Azure-portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.openlocfilehash: 5e0b5633a153583117cfe0d90ec5c0e7c5f2a147
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277534"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Zelfstudie: Een voorbeeld van Spring Cloud Config Server instellen voor uw service

In dit artikel ziet u hoe u een instantie van Spring Cloud Config Server verbinden met uw Azure Spring Cloud-service.

Spring Cloud Config biedt server- en client-side ondersteuning voor een externe configuratie in een gedistribueerd systeem. Met de instantie Config Server hebt u een centrale plek om externe eigenschappen voor toepassingen in alle omgevingen te beheren. Zie [Naslaginformatie over de Cloud Config Server](https://spring.io/projects/spring-cloud-config)voor meer informatie .

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint. 
* Een reeds ingerichte en draaiende Azure Spring Cloud-service. Zie [Quickstart: Een Java Spring-toepassing starten met azure CLI](spring-cloud-quickstart-launch-app-cli.md)als u een Azure Spring Cloud-service wilt instellen en starten.

## <a name="restriction"></a>Beperking

Er zijn enkele beperkingen wanneer u Config Server gebruikt met een Git back-end. Sommige eigenschappen worden automatisch geïnjecteerd in uw toepassingsomgeving om toegang te krijgen tot Config Server en Service Discovery. Als u deze eigenschappen ook configureert vanuit uw Config Server-bestanden, u conflicten en onverwacht gedrag ondervinden. De eigenschappen omvatten: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```

> [!CAUTION]
> We raden u ten zeerste aan de bovenstaande eigenschappen _niet_ in uw Config Server-toepassingsbestanden te plaatsen.

## <a name="create-your-config-server-files"></a>Uw Config Server-bestanden maken

Azure Spring Cloud ondersteunt Azure DevOps, GitHub, GitLab en Bitbucket voor het opslaan van uw Config Server-bestanden. Wanneer u uw repository gereed hebt, maakt u de configuratiebestanden met de volgende instructies en slaat u ze daar op.

Bovendien zijn sommige configureerbare eigenschappen alleen beschikbaar voor bepaalde typen. In de volgende subsecties worden de eigenschappen voor elk opslagplaatstype weergegeven.

### <a name="public-repository"></a>Openbare opslagplaats

Wanneer u een openbare opslagplaats gebruikt, zijn uw configureerbare eigenschappen beperkter.

Alle configureerbare eigenschappen die worden gebruikt om de openbare Git-opslagplaats in te stellen, worden in de volgende tabel weergegeven:

> [!NOTE]
> Het gebruik van een koppelteken (-) om woorden te scheiden is de enige naamgevingsconventie die momenteel wordt ondersteund. U bijvoorbeeld *het standaardlabel*gebruiken, maar niet *standaardLabel*.

| Eigenschap        | Vereist | Functie                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ja    | De URI van de Git-repository die wordt gebruikt als de Back-end van de Config Server begint met *http://,* *https://,* *git@* of *ssh://*. |
| `default-label` | Nee     | Het standaardlabel van de Git-repository moet de naam van de *branch,* *tagnaam*of *commit-id* van de repository zijn. |
| `search-paths`  | Nee     | Een array met tekenreeksen die worden gebruikt om submappen van de Git-repository te doorzoeken. |

------

### <a name="private-repository-with-ssh-authentication"></a>Private repository met SSH-verificatie

Alle configureerbare eigenschappen die worden gebruikt om privé-Git-repository met SSH in te stellen, worden in de volgende tabel weergegeven:

> [!NOTE]
> Het gebruik van een koppelteken (-) om woorden te scheiden is de enige naamgevingsconventie die momenteel wordt ondersteund. U bijvoorbeeld *het standaardlabel*gebruiken, maar niet *standaardLabel*.

| Eigenschap                   | Vereist | Functie                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Ja    | De URI van de Git repository die wordt gebruikt als de Config Server back-end, moet worden gestart met *http://,* *https://,* *git@* of *ssh://*. |
| `default-label`            | Nee     | Het standaardlabel van de Git-repository moet de naam van de *branch,* *tagnaam*of *commit-id* van de repository zijn. |
| `search-paths`             | Nee     | Een array met tekenreeksen die worden gebruikt om submappen van de Git-repository te doorzoeken. |
| `private-key`              | Nee     | De SSH-privésleutel voor toegang tot de Git-repository, _vereist_ wanneer de URI begint met *git@* of *ssh://.* |
| `host-key`                 | Nee     | De hostsleutel van de Git-repository-server mag het `host-key-algorithm`algoritmevoorvoegsel niet bevatten zoals gedekt door . |
| `host-key-algorithm`       | Nee     | De host key algoritme, moet *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*, of *ecdsa-sha2-nistp521*. *Alleen* vereist `host-key` als het bestaat. |
| `strict-host-key-checking` | Nee     | Geeft aan of de instantie Config Server niet `host-key`wordt gestart wanneer u de privé -instantie ingebruikneemt. Moet *waar* zijn (standaardwaarde) of *onwaar*. |

-----

### <a name="private-repository-with-basic-authentication"></a>Private repository met basisverificatie

Alle configureerbare eigenschappen die worden gebruikt om privé Git-repository in te stellen met basisverificatie, worden hieronder weergegeven.

> [!NOTE]
> Het gebruik van een koppelteken (-) om woorden te scheiden is de enige naamgevingsconventie die momenteel wordt ondersteund. Gebruik bijvoorbeeld *het standaardlabel*, niet *standaardLabel*.

| Eigenschap        | Vereist | Functie                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ja    | De URI van de Git-repository die wordt gebruikt als de Back-end van de Config Server moet worden gestart met *http://,* *https://,* *git@* of *ssh://*. |
| `default-label` | Nee     | Het standaardlabel van de Git-repository moet de naam van de *branch,* *tagnaam*of *commit-id* van de repository zijn. |
| `search-paths`  | Nee     | Een array met tekenreeksen die worden gebruikt om submappen van de Git-repository te doorzoeken. |
| `username`      | Nee     | De gebruikersnaam die wordt gebruikt om toegang te krijgen tot de Git-repository-server, _vereist_ wanneer de Git-repository-server ondersteuning biedt. `Http Basic Authentication` |
| `password`      | Nee     | Het wachtwoord dat wordt gebruikt om _required_ toegang te krijgen tot de `Http Basic Authentication`Git-repository-server, vereist wanneer de Git-repository-server ondersteunt. |

> [!NOTE]
> Veel `Git` repository servers ondersteunen het gebruik van tokens in plaats van wachtwoorden voor HTTP Basic Authentication. In sommige repositories, zoals GitHub, kunnen tokens voor onbepaalde tijd blijven bestaan. Sommige Git-repository-servers, waaronder Azure DevOps, dwingen tokens echter binnen een paar uur te verlopen. Repositories die ervoor zorgen dat tokens verlopen, mogen geen tokengebaseerde verificatie gebruiken met Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Git repositories met patroon

Alle configureerbare eigenschappen die worden gebruikt om Git-opslagplaatsen met patroon in te stellen, worden hieronder weergegeven.

> [!NOTE]
> Het gebruik van een koppelteken (-) om woorden te scheiden is de enige naamgevingsconventie die momenteel wordt ondersteund. Gebruik bijvoorbeeld *het standaardlabel*, niet *standaardLabel*.

| Eigenschap                           | Vereist         | Functie                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Nee             | Een kaart bestaande uit de instellingen voor een Git repository met een bepaalde naam. |
| `repos."uri"`                      | Ja op`repos` | De URI van de Git-repository die wordt gebruikt als de Back-end van de Config Server moet worden gestart met *http://,* *https://,* *git@* of *ssh://*. |
| `repos."name"`                     | Ja op`repos` | Een naam om te identificeren op _required_ de `repos` Git repository, vereist alleen als bestaat. Bijvoorbeeld *team-A,* *team-B*. |
| `repos."pattern"`                  | Nee             | Een array met tekenreeksen die worden gebruikt om een toepassingsnaam te matchen. Gebruik voor elk `{application}/{profile}` patroon de indeling met wildcards. |
| `repos."default-label"`            | Nee             | Het standaardlabel van de Git-repository moet de naam van de *branch,* *tagnaam*of *commit-id* van de repository zijn. |
| `repos."search-paths`"             | Nee             | Een array met tekenreeksen die worden gebruikt om submappen van de Git-repository te doorzoeken. |
| `repos."username"`                 | Nee             | De gebruikersnaam die wordt gebruikt om toegang te krijgen tot de Git-repository-server, _vereist_ wanneer de Git-repository-server ondersteuning biedt. `Http Basic Authentication` |
| `repos."password"`                 | Nee             | Het wachtwoord dat wordt gebruikt om _required_ toegang te krijgen tot de `Http Basic Authentication`Git-repository-server, vereist wanneer de Git-repository-server ondersteunt. |
| `repos."private-key"`              | Nee             | De SSH-privésleutel voor toegang tot git-repository, _vereist_ wanneer de URI begint met *git@* of *ssh://.* |
| `repos."host-key"`                 | Nee             | De hostsleutel van de Git-repository-server mag het `host-key-algorithm`algoritmevoorvoegsel niet bevatten zoals gedekt door . |
| `repos."host-key-algorithm"`       | Nee             | De host key algoritme, moet *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*, of *ecdsa-sha2-nistp521*. *Alleen* vereist `host-key` als het bestaat. |
| `repos."strict-host-key-checking"` | Nee             | Geeft aan of de instantie Config Server niet `host-key`wordt gestart wanneer u de privé -instantie ingebruikneemt. Moet *waar* zijn (standaardwaarde) of *onwaar*. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Uw Config Server-repository koppelen aan Azure Spring Cloud

Nu uw configuratiebestanden zijn opgeslagen in een opslagplaats, moet u Azure Spring Cloud ermee verbinden.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Ga naar de pagina Azure Spring Cloud **Overview.**

1. Selecteer de service die u wilt configureren.

1. Selecteer in het linkerdeelvenster van de servicepagina onder **Instellingen**het tabblad **Config Server.**

![Het venster Config-server](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Repository-gegevens rechtstreeks invoeren op de Azure-portal

#### <a name="default-repository"></a>Standaardopslagplaats

* **Openbare opslagplaats**: Plak in de sectie **Standaardopslagplaats** in het vak **Uri** de repository URI.  Stel het **label** in **op config**. Controleer of de **verificatieinstelling** **Openbaar**is en selecteer **Toepassen** om te voltooien. 

* **Private repository:** Azure Spring Cloud ondersteunt basiswachtwoord/tokengebaseerde authenticatie en SSH.

    * **Basisverificatie:** plak in de sectie **Standaardopslagplaats** in het vak **Uri** de repository URI en selecteer vervolgens de knop **Verificatie** ('potlood'-pictogram). Selecteer in het deelvenster **Verificatie bewerken** in de vervolgkeuzelijst **Verificatietype** de optie **HTTP Basic**en voer vervolgens uw gebruikersnaam en wachtwoord/token in om toegang te verlenen tot Azure Spring Cloud. Selecteer **OK**en selecteer **Toepassen** om het instellen van de Config Server-instantie te voltooien.

    ![Het deelvenster Verificatie bewerken](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Sommige Git-repository-servers, zoals GitHub, gebruiken een *persoonlijk token* of een *access-token*, zoals een wachtwoord, voor **Basisverificatie.** U dat soort token gebruiken als wachtwoord in Azure Spring Cloud, omdat het nooit zal verlopen. Maar voor andere Git-repository-servers, zoals Bitbucket en Azure DevOps, verloopt het *access-token* binnen een of twee uur. Dit betekent dat de optie niet levensvatbaar is wanneer u deze repository-servers met Azure Spring Cloud gebruikt.

    * **SSH:** Plak in de sectie **Standaardopslagplaats** in het vak **Uri** de repository URI en selecteer vervolgens de knop **Verificatie** ('potlood'-pictogram). Selecteer in het deelvenster **Verificatie bewerken** in de vervolgkeuzelijst **Verificatietype** de optie **SSH**en voer vervolgens uw **privésleutel**in . Geef eventueel de **hostsleutel** en **het algoritme hostsleutel op.** Zorg ervoor dat u uw openbare sleutel opneemt in uw Config Server-repository. Selecteer **OK**en selecteer **Toepassen** om het instellen van de Config Server-instantie te voltooien.

    ![Het deelvenster Verificatie bewerken](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Patroonopslagplaats

Als u een optionele **patroonopslagplaats** wilt gebruiken om uw service te configureren, geeft u de **URI** en **verificatie** op dezelfde manier op als de **standaardopslagplaats.** Zorg ervoor dat u een **naam** voor uw patroon opneemt en selecteer **Toepassen** om deze aan uw instantie toe te voegen. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Repository-informatie invoeren in een YAML-bestand

Als u een YAML-bestand hebt geschreven met uw repository-instellingen, u het bestand rechtstreeks vanuit uw lokale machine importeren in Azure Spring Cloud. Een eenvoudig YAML-bestand voor een privé-opslagplaats met basisverificatie ziet er als volgt uit:

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

Selecteer de knop **Instellingen importeren** en selecteer het YAML-bestand in uw projectmap. Selecteer **Importeren**en `async` vervolgens verschijnt er een bewerking van **uw meldingen.** Na 1-2 minuten, moet het verslag succes.

![Het deelvenster Meldingen van Config-server](media/spring-cloud-tutorial-config-server/local-yml-success.png)


De informatie uit uw YAML-bestand moet worden weergegeven in de Azure-portal. Selecteer **Toepassen** om te voltooien. 


## <a name="delete-your-app-configuration"></a>Uw app-configuratie verwijderen

Nadat u een configuratiebestand hebt opgeslagen, wordt de knop **App-configuratie verwijderen** weergegeven op het tabblad **Configuratie.** Als u deze knop selecteert, worden uw bestaande instellingen volledig gewist. U moet deze optie selecteren als u uw Config Server-exemplaar wilt verbinden met een andere bron, zoals de overstap van GitHub naar Azure DevOps.



## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u uw instantie Spring Cloud Config Server inschakelen en configureren. Ga voor meer informatie over het beheren van uw toepassing verder naar de zelfstudie over het handmatig schalen van uw app.

> [!div class="nextstepaction"]
> [Zelfstudie: Een toepassing schalen in Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md)
