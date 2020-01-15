---
title: YAML-verwijzing-ACR-taken
description: Naslag voor het definiëren van taken in YAML voor ACR-taken, waaronder taak eigenschappen, stap typen, stap eigenschappen en ingebouwde variabelen.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: d86eb0e24233afb536d27f5d0938d4748941e88a
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945742"
---
# <a name="acr-tasks-reference-yaml"></a>Naslag informatie over ACR-taken: YAML

Met de definitie van een taak in ACR met meerdere stappen in de taken in een container wordt gestreefd over het bouwen, testen en patchen van containers. In dit artikel worden de opdrachten, para meters, eigenschappen en syntaxis beschreven voor de YAML-bestanden die uw taken met meerdere stappen definiëren.

Dit artikel bevat informatie over het maken van YAML-bestanden met meerdere stappen voor ACR-taken. Als u een inleiding wilt hebben over ACR-taken, raadpleegt u het [overzicht van ACR-taken](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR-taak. yaml-bestands indeling

ACR-taken bieden ondersteuning voor een taak declaratie met meerdere stappen in de standaard YAML-syntaxis. U definieert de stappen van een taak in een YAML-bestand. U kunt de taak vervolgens hand matig uitvoeren door het bestand door te geven aan de opdracht [AZ ACR run][az-acr-run] . U kunt ook het bestand gebruiken om een taak te maken met [AZ ACR Task Create][az-acr-task-create] die automatisch wordt geactiveerd door een update van Git of basis installatie kopie. Hoewel in dit artikel wordt verwezen naar `acr-task.yaml` als het bestand dat de stappen bevat, ondersteunt ACR-taken een geldige bestands naam met een [ondersteunde extensie](#supported-task-filename-extensions).

De op het hoogste niveau `acr-task.yaml` primitieven zijn **taak eigenschappen**, **typen stappen**en **stap-eigenschappen**:

* [Taak eigenschappen](#task-properties) zijn van toepassing op alle stappen in de taak uitvoering. Er zijn verschillende algemene taak eigenschappen, waaronder:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Taak stap typen](#task-step-types) vertegenwoordigen de typen acties die kunnen worden uitgevoerd in een taak. Er zijn drie typen stappen:
  * `build`
  * `push`
  * `cmd`
* De eigenschappen van de [taak stap](#task-step-properties) zijn para meters die van toepassing zijn op een afzonderlijke stap. Er zijn verschillende stap eigenschappen, waaronder:
  * `startDelay`
  * `timeout`
  * `when`
  * ... en nog veel meer.

De basis indeling van een `acr-task.yaml`-bestand, met inbegrip van enkele algemene stap eigenschappen, volgt. Hoewel een volledig overzicht van alle beschik bare stap-eigenschappen of het gebruik van het stap type niet wordt weer gegeven, biedt het een beknopt beeld van de Basic-bestands indeling.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Ondersteunde bestandsnaam extensies van de taak

ACR-taken hebben diverse bestandsnaam extensies gereserveerd, waaronder `.yaml`, die worden verwerkt als een taak bestand. Elke uitbrei ding die *niet* in de volgende lijst voor komt, wordt beschouwd door ACR-taken als een Dockerfile:. yaml,. yml,. toml,. json,. sh,. bash,. zsh,. ps1,. ps,. cmd,. bat,.

YAML is de enige bestands indeling die momenteel door ACR-taken wordt ondersteund. De andere bestandsnaam extensies zijn gereserveerd voor mogelijke toekomstige ondersteuning.

## <a name="run-the-sample-tasks"></a>De voorbeeld taken uitvoeren

In de volgende secties van dit artikel vindt u enkele voor beelden van taak bestanden waarnaar wordt verwezen. De voorbeeld taken bevinden zich in een open bare GitHub-opslag plaats, [Azure-samples/ACR-tasks][acr-tasks]. U kunt ze uitvoeren met de Azure CLI-opdracht [AZ ACR run][az-acr-run]. De voorbeeld opdrachten zijn vergelijkbaar met:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

In de opmaak van de voorbeeld opdrachten wordt ervan uitgegaan dat u een standaard register hebt geconfigureerd in de Azure CLI, zodat de para meter `--registry` weglaat. Als u een standaard register wilt configureren, gebruikt u de opdracht [AZ configure][az-configure] met de para meter `--defaults`, waarmee een `acr=REGISTRY_NAME` waarde wordt geaccepteerd.

Als u bijvoorbeeld de Azure CLI wilt configureren met een standaard register met de naam ' myregistry ':

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Taak eigenschappen

Taak eigenschappen worden meestal boven aan een `acr-task.yaml`-bestand weer gegeven en zijn algemene eigenschappen die van toepassing zijn tijdens de volledige uitvoering van de taak stappen. Sommige van deze algemene eigenschappen kunnen in een afzonderlijke stap worden overschreven.

| Eigenschap | Type | Optioneel | Beschrijving | Overschrijven ondersteund | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Ja | De versie van het `acr-task.yaml`-bestand zoals geparseerd door de service ACR tasks. Hoewel ACR-taken streven om achterwaartse compatibiliteit te behouden, kunnen met deze waarde ACR-taken de compatibiliteit binnen een gedefinieerde versie behouden blijven. Als u geen waarde opgeeft, wordt de meest recente versie gebruikt. | Nee | Geen |
| `stepTimeout` | int (seconden) | Ja | Het maximum aantal seconden dat een stap kan worden uitgevoerd. Als de eigenschap is opgegeven voor een taak, wordt de standaard eigenschap `timeout` van alle stappen. Als de eigenschap `timeout` is opgegeven in een stap, wordt de eigenschap die door de taak wordt opgegeven, overschreven. | Ja | 600 (10 minuten) |
| `workingDirectory` | string | Ja | De werkmap van de container tijdens runtime. Als de eigenschap is opgegeven voor een taak, wordt de standaard eigenschap `workingDirectory` van alle stappen. Als u een stap opgeeft, wordt de eigenschap die door de taak is opgegeven, overschreven. | Ja | `/workspace` |
| `env` | [teken reeks, teken reeks,...] | Ja |  Matrix van teken reeksen in `key=value` indeling waarmee de omgevings variabelen voor de taak worden gedefinieerd. Als de eigenschap is opgegeven voor een taak, wordt de standaard eigenschap `env` van alle stappen. Als u een stap opgeeft, worden de omgevings variabelen die zijn overgenomen van de taak, overschreven. | Geen |
| `secrets` | [geheim, geheim,...] | Ja | Matrix van [geheime](#secret) objecten. | Geen |
| `networks` | [netwerk, netwerk,...] | Ja | Matrix van [netwerk](#network) objecten. | Geen |

### <a name="secret"></a>geheim

Het geheim object heeft de volgende eigenschappen.

| Eigenschap | Type | Optioneel | Beschrijving | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | Nee | De id van het geheim. | Geen |
| `keyvault` | string | Ja | De Azure Key Vault geheime URL. | Geen |
| `clientID` | string | Ja | De client-ID van de door de [gebruiker toegewezen beheerde identiteit](container-registry-tasks-authentication-managed-identity.md) voor Azure-resources. | Geen |

### <a name="network"></a>netwerk

Het netwerk object heeft de volgende eigenschappen.

| Eigenschap | Type | Optioneel | Beschrijving | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | Nee | De naam van het netwerk. | Geen |
| `driver` | string | Ja | Het stuur programma voor het beheren van het netwerk. | Geen |
| `ipv6` | bool | Ja | Of IPv6-netwerken zijn ingeschakeld. | `false` |
| `skipCreation` | bool | Ja | Hiermee wordt aangegeven of het maken van netwerken moet worden overgeslagen. | `false` |
| `isDefault` | bool | Ja | Of het netwerk een standaard netwerk is dat wordt meegeleverd met Azure Container Registry | `false` |

## <a name="task-step-types"></a>Taak stap typen

ACR-taken ondersteunen drie stap typen. Elk type stap ondersteunt verschillende eigenschappen, die in de sectie voor elk type stap worden beschreven.

| Type stap | Beschrijving |
| --------- | ----------- |
| [`build`](#build) | Hiermee maakt u een container installatie kopie met vertrouwde `docker build`-syntaxis. |
| [`push`](#push) | Hiermee voert u een `docker push` van de zojuist gemaakte of gelabelde installatie kopieën naar een container register. Azure Container Registry, andere persoonlijke registers en de open bare docker-hub worden ondersteund. |
| [`cmd`](#cmd) | Voert een container als een opdracht uit met de para meters die zijn door gegeven aan de `[ENTRYPOINT]`van de container. Het stap type `cmd` biedt ondersteuning voor para meters zoals `env`, `detach`en andere bekende `docker run` opdracht Opties, het inschakelen van een eenheid en functionele test met gelijktijdige container uitvoering. |

## <a name="build"></a>bouwen

Een container installatie kopie bouwen. Het stap type `build` vertegenwoordigt een multi tenant, beveiligde methode voor het uitvoeren van `docker build` in de Cloud als een eerste klasse primitief.

### <a name="syntax-build"></a>Syntaxis: Build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Het stap type `build` ondersteunt de para meters in de volgende tabel. Het stap type `build` biedt ook ondersteuning voor alle opbouw opties van de opdracht [docker build](https://docs.docker.com/engine/reference/commandline/build/) , zoals `--build-arg` om opbouw tijd variabelen in te stellen.

| Parameter | Beschrijving | Optioneel |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Hiermee wordt de volledig gekwalificeerde `image:tag` van de ingebouwde installatie kopie gedefinieerd.<br /><br />Als installatie kopieën kunnen worden gebruikt voor interne taak validaties, zoals functionele tests, moeten niet alle installatie kopieën `push` een REGI ster. Als u echter een installatie kopie van een taak wilt laten uitvoeren, moet de installatie kopie een naam hebben waarnaar kan worden verwezen.<br /><br />In tegens telling tot `az acr build`biedt het uitvoeren van ACR-taken geen standaard push gedrag. Met ACR-taken wordt in het standaard scenario aangenomen dat u een installatie kopie kunt bouwen, valideren en vervolgens kunt pushen. Zie [Push](#push) voor instructies voor het pushen van ingebouwde installatie kopieën. | Ja |
| `-f` &#124; `--file` | Hiermee geeft u de Dockerfile door gegeven aan `docker build`. Als u niets opgeeft, wordt de standaard-Dockerfile in de hoofdmap van de context gebruikt. Als u een Dockerfile wilt opgeven, geeft u de bestands naam aan ten opzichte van de hoofdmap van de context. | Ja |
| `context` | De hoofdmap is door gegeven aan `docker build`. De hoofdmap van elke taak is ingesteld op een gedeelde [variabele workingdirectory](#task-step-properties)en bevat de hoofdmap van de gekoppelde Git-gekloonde map. | Nee |

### <a name="properties-build"></a>Eigenschappen: Build

Het stap type `build` ondersteunt de volgende eigenschappen. Meer informatie over deze eigenschappen vindt u in de sectie [taak stap eigenschappen](#task-step-properties) van dit artikel.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Optioneel |
| `disableWorkingDirectoryOverride` | bool | Optioneel |
| `entryPoint` | string | Optioneel |
| `env` | [teken reeks, teken reeks,...] | Optioneel |
| `expose` | [teken reeks, teken reeks,...] | Optioneel |
| `id` | string | Optioneel |
| `ignoreErrors` | bool | Optioneel |
| `isolation` | string | Optioneel |
| `keep` | bool | Optioneel |
| `network` | object | Optioneel |
| `ports` | [teken reeks, teken reeks,...] | Optioneel |
| `pull` | bool | Optioneel |
| `repeat` | int | Optioneel |
| `retries` | int | Optioneel |
| `retryDelay` | int (seconden) | Optioneel |
| `secret` | object | Optioneel |
| `startDelay` | int (seconden) | Optioneel |
| `timeout` | int (seconden) | Optioneel |
| `when` | [teken reeks, teken reeks,...] | Optioneel |
| `workingDirectory` | string | Optioneel |

### <a name="examples-build"></a>Voor beelden: Build

#### <a name="build-image---context-in-root"></a>Installatie kopie maken-context in hoofdmap

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Installatie kopie maken-context in submap

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Push een of meer ingebouwde of gelabelde installatie kopieën naar een container register. Ondersteunt het pushen naar persoonlijke registers zoals Azure Container Registry, of naar de open bare docker-hub.

### <a name="syntax-push"></a>Syntaxis: push

Het `push`-stap type ondersteunt een verzameling installatie kopieën. De syntaxis van de YAML-verzameling ondersteunt inline-en geneste notaties. Het pushen van één installatie kopie wordt gewoonlijk weer gegeven met inline-syntaxis:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Gebruik geneste syntaxis bij het pushen van meerdere installatie kopieën voor meer Lees baarheid:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Eigenschappen: push

Het stap type `push` ondersteunt de volgende eigenschappen. Meer informatie over deze eigenschappen vindt u in de sectie [taak stap eigenschappen](#task-step-properties) van dit artikel.

| | | |
| -------- | ---- | -------- |
| `env` | [teken reeks, teken reeks,...] | Optioneel |
| `id` | string | Optioneel |
| `ignoreErrors` | bool | Optioneel |
| `startDelay` | int (seconden) | Optioneel |
| `timeout` | int (seconden) | Optioneel |
| `when` | [teken reeks, teken reeks,...] | Optioneel |

### <a name="examples-push"></a>Voor beelden: push

#### <a name="push-multiple-images"></a>Meerdere installatie kopieën pushen

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Bouwen, pushen en uitvoeren

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Met het `cmd` stap type wordt een container uitgevoerd.

### <a name="syntax-cmd"></a>Syntaxis: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Eigenschappen: cmd

Het stap type `cmd` ondersteunt de volgende eigenschappen:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Optioneel |
| `disableWorkingDirectoryOverride` | bool | Optioneel |
| `entryPoint` | string | Optioneel |
| `env` | [teken reeks, teken reeks,...] | Optioneel |
| `expose` | [teken reeks, teken reeks,...] | Optioneel |
| `id` | string | Optioneel |
| `ignoreErrors` | bool | Optioneel |
| `isolation` | string | Optioneel |
| `keep` | bool | Optioneel |
| `network` | object | Optioneel |
| `ports` | [teken reeks, teken reeks,...] | Optioneel |
| `pull` | bool | Optioneel |
| `repeat` | int | Optioneel |
| `retries` | int | Optioneel |
| `retryDelay` | int (seconden) | Optioneel |
| `secret` | object | Optioneel |
| `startDelay` | int (seconden) | Optioneel |
| `timeout` | int (seconden) | Optioneel |
| `when` | [teken reeks, teken reeks,...] | Optioneel |
| `workingDirectory` | string | Optioneel |

Meer informatie over deze eigenschappen vindt u in de sectie [taak stap-eigenschappen](#task-step-properties) van dit artikel.

### <a name="examples-cmd"></a>Voor beelden: cmd

#### <a name="run-hello-world-image"></a>Installatie kopie van Hallo-wereld uitvoeren

Met deze opdracht wordt het `hello-world.yaml` taak bestand uitgevoerd, dat verwijst naar de [Hallo wereld-](https://hub.docker.com/_/hello-world/) installatie kopie op docker hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Bash-installatie kopie en echo ' Hallo wereld ' uitvoeren

Met deze opdracht wordt het `bash-echo.yaml` taak bestand uitgevoerd, dat verwijst naar de [bash](https://hub.docker.com/_/bash/) -installatie kopie op docker hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Specifieke bash-afbeeldings code uitvoeren

Als u een specifieke installatie kopie versie wilt uitvoeren, geeft u de tag op in het `cmd`.

Met deze opdracht wordt het `bash-echo-3.yaml` taak bestand uitgevoerd, dat verwijst naar de [bash: 3.0](https://hub.docker.com/_/bash/) -installatie kopie op docker hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Aangepaste installatie kopieën uitvoeren

Het `cmd`-stap type verwijst naar afbeeldingen met de standaard `docker run` indeling. Voor installatie kopieën die niet zijn voorzien van een REGI ster, wordt ervan uitgegaan dat deze afkomstig zijn van docker.io. Het vorige voor beeld kan ook als volgt worden weer gegeven:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Met behulp van de standaard `docker run`-referentie Conventie voor installatie kopieën kan `cmd` installatie kopie uit een persoonlijk REGI ster of de open bare docker-hub worden uitgevoerd. Als u verwijst naar afbeeldingen in hetzelfde REGI ster waarin de ACR-taak wordt uitgevoerd, hoeft u geen register referenties op te geven.

* Voer een installatie kopie uit vanuit een Azure container Registry. In het volgende voor beeld wordt ervan uitgegaan dat u een REGI ster met de naam `myregistry`en een aangepaste installatie kopie `myimage:mytag`.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* De register referentie met een uitvoer variabele of-alias generaliseren

    In plaats van de naam van uw REGI ster in een `acr-task.yaml`-bestand vast te maken, kunt u het apparaat versleutelen met behulp van een [Run-variabele](#run-variables) of- [alias](#aliases). De `Run.Registry` variabele of `$Registry` alias wordt tijdens runtime uitgebreid naar de naam van het REGI ster waarin de taak wordt uitgevoerd.

    Als u bijvoorbeeld de voor gaande taak wilt generaliseren zodat deze werkt in een Azure container Registry, verwijst u naar de $Registry variabele in de naam van de installatie kopie:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Eigenschappen van taak stap

Elk stap type ondersteunt verschillende eigenschappen die geschikt zijn voor het type. In de volgende tabel worden alle beschik bare stap eigenschappen gedefinieerd. Niet alle stap typen ondersteunen alle eigenschappen. Als u wilt zien welke van deze eigenschappen beschikbaar zijn voor elk stap type, raadpleegt u de sectie met Naslag informatie voor het type [cmd](#cmd), [Build](#build)en [Push](#push) .

| Eigenschap | Type | Optioneel | Beschrijving | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Ja | Hiermee wordt aangegeven of de container moet worden losgekoppeld wanneer deze wordt uitgevoerd. | `false` |
| `disableWorkingDirectoryOverride` | bool | Ja | Hiermee wordt aangegeven of `workingDirectory` onderdrukkings functionaliteit moet worden uitgeschakeld. Gebruik dit in combi natie met `workingDirectory` voor volledige controle over de werkmap van de container. | `false` |
| `entryPoint` | string | Ja | Onderdrukt de `[ENTRYPOINT]` van de container van een stap. | Geen |
| `env` | [teken reeks, teken reeks,...] | Ja | Matrix van teken reeksen in `key=value` indeling waarmee de omgevings variabelen voor de stap worden gedefinieerd. | Geen |
| `expose` | [teken reeks, teken reeks,...] | Ja | Matrix van poorten die vanuit de container worden weer gegeven. |  Geen |
| [`id`](#example-id) | string | Ja | Identificeert de stap in de taak uniek. Andere stappen in de taak kunnen verwijzen naar de `id`van een stap, zoals het controleren op afhankelijkheden met `when`.<br /><br />De `id` is ook de naam van de actieve container. Processen die worden uitgevoerd in andere containers in de taak, kunnen verwijzen naar de `id` als de DNS-hostnaam of om deze te openen met docker-Logboeken [id]. | `acb_step_%d`, waarbij `%d` de op 0 gebaseerde index van de stap top-down in het YAML-bestand is |
| `ignoreErrors` | bool | Ja | Hiermee wordt aangegeven of de stap moet worden gemarkeerd als geslaagd, ongeacht of er een fout is opgetreden tijdens het uitvoeren van de container. | `false` |
| `isolation` | string | Ja | Het isolatie niveau van de container. | `default` |
| `keep` | bool | Ja | Hiermee wordt aangegeven of de container van de stap na uitvoering moet worden bewaard. | `false` |
| `network` | object | Ja | Identificeert een netwerk waarin de container wordt uitgevoerd. | Geen |
| `ports` | [teken reeks, teken reeks,...] | Ja | Matrix van poorten die vanuit de container naar de host worden gepubliceerd. |  Geen |
| `pull` | bool | Ja | Hiermee wordt aangegeven of een pull van de container moet worden gedwongen voordat deze wordt uitgevoerd om het cache gedrag te voor komen. | `false` |
| `privileged` | bool | Ja | Hiermee wordt aangegeven of de container moet worden uitgevoerd in de beschermde modus. | `false` |
| `repeat` | int | Ja | Het aantal nieuwe pogingen om de uitvoering van een container te herhalen. | 0 |
| `retries` | int | Ja | Het aantal nieuwe pogingen dat wordt geprobeerd als de uitvoering van een container mislukt. Er wordt alleen geprobeerd een nieuwe poging uit te voeren als de afsluit code van een container niet gelijk is aan nul. | 0 |
| `retryDelay` | int (seconden) | Ja | De vertraging in seconden tussen nieuwe pogingen van de uitvoering van een container. | 0 |
| `secret` | object | Ja | Identificeert een Azure Key Vault geheime of [beheerde identiteit voor Azure-resources](container-registry-tasks-authentication-managed-identity.md). | Geen |
| `startDelay` | int (seconden) | Ja | Aantal seconden dat de uitvoering van een container moet worden vertraagd. | 0 |
| `timeout` | int (seconden) | Ja | Maximum aantal seconden dat een stap kan worden uitgevoerd voordat deze wordt beëindigd. | 600 |
| [`when`](#example-when) | [teken reeks, teken reeks,...] | Ja | Hiermee configureert u de afhankelijkheid van een stap voor een of meer andere stappen in de taak. | Geen |
| `user` | string | Ja | De gebruikers naam of UID van een container | Geen |
| `workingDirectory` | string | Ja | Hiermee stelt u de werkmap voor een stap in. ACR-taken maken standaard een hoofdmap als werkmap. Als uw build echter verschillende stappen heeft, kunnen eerdere stappen artefacten delen met latere stappen door dezelfde werkmap op te geven. | `/workspace` |

### <a name="examples-task-step-properties"></a>Voor beelden: taak stap eigenschappen

#### <a name="example-id"></a>Voor beeld: id

Maak twee installatie kopieën en pas een functionele test installatie kopie toe. Elke stap wordt geïdentificeerd door een unieke `id` die andere stappen in de taak verwijzing in hun `when` eigenschap.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Voor beeld: wanneer

De eigenschap `when` specificeert de afhankelijkheid van een stap op andere stappen in de taak. Het ondersteunt twee parameter waarden:

* `when: ["-"]`: geeft geen afhankelijkheid van andere stappen aan. Een stap waarin wordt aangegeven dat `when: ["-"]` onmiddellijk wordt uitgevoerd, en de uitvoering van gelijktijdige stappen mogelijk maakt.
* `when: ["id1", "id2"]`-geeft aan dat de stap afhankelijk is van de stappen met `id` ' id1 ' en `id` ' id2 '. Deze stap wordt pas uitgevoerd als de stappen ' id1 ' en ' id2 ' zijn voltooid.

Als `when` niet in een stap is opgegeven, is die stap afhankelijk van de voltooiing van de vorige stap in het `acr-task.yaml` bestand.

Uitvoering van de sequentiële stap zonder `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Uitvoering van de sequentiële stap met `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Parallelle installatie kopieën bouwen:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Parallelle installatie kopieën bouwen en afhankelijke tests:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Variabelen uitvoeren

ACR-taken bevatten een standaardset variabelen die beschikbaar zijn voor taak stappen wanneer ze worden uitgevoerd. U kunt deze variabelen openen met behulp van de notatie `{{.Run.VariableName}}`, waarbij `VariableName` een van de volgende is:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

De namen van variabelen zijn doorgaans zelf uitleg. Hier volgt een overzicht van de meest gebruikte variabelen. Vanaf YAML-versie `v1.1.0`, kunt u een verkorte, vooraf gedefinieerde [taak alias](#aliases) gebruiken in plaats van de meeste run-variabelen. Gebruik bijvoorbeeld in plaats van `{{.Run.Registry}}`de alias `$Registry`.

### <a name="runid"></a>Run.ID

Elke uitvoering, via `az acr run`of trigger op basis van de uitvoering van taken die via `az acr task create`zijn gemaakt, heeft een unieke ID. De ID vertegenwoordigt de uitvoering die momenteel wordt uitgevoerd.

Wordt meestal gebruikt voor een unieke code ring van een afbeelding:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

De volledig gekwalificeerde server naam van het REGI ster. Wordt doorgaans gebruikt om algemeen te verwijzen naar het REGI ster waarin de taak wordt uitgevoerd.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run. Registrynaam

De naam van het container register. Wordt doorgaans gebruikt in taak stappen waarvoor geen volledig gekwalificeerde server naam is vereist, bijvoorbeeld `cmd` stappen die Azure CLI-opdrachten uitvoeren op registers.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run. date

De huidige UTC-tijd waarop de uitvoering begon.

### <a name="runcommit"></a>Uitvoeren. door voeren

Voor een taak die wordt geactiveerd door een door Voer voor een GitHub-opslag plaats, de commit-id.

### <a name="runbranch"></a>Run. Branch

Voor een taak die wordt geactiveerd door een door Voer voor een GitHub-opslag plaats, de naam van de vertakking.

## <a name="aliases"></a>Aliassen

Vanaf `v1.1.0`ondersteunt ACR-taken aliassen die beschikbaar zijn voor taak stappen wanneer ze worden uitgevoerd. Aliassen zijn vergelijkbaar in het concept voor aliassen (opdracht snelkoppelingen) die worden ondersteund in bash en andere opdracht shells. 

Met een alias kunt u elke opdracht of groep opdrachten (inclusief opties en bestands namen) starten door één woord in te voeren.

ACR-taken bieden ondersteuning voor verschillende vooraf gedefinieerde aliassen en aangepaste aliassen die u maakt.

### <a name="predefined-aliases"></a>Vooraf gedefinieerde aliassen

De volgende taak aliassen zijn beschikbaar voor gebruik in plaats van [uitvoerings variabelen](#run-variables):

| Alias | Variabele uitvoeren |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

Plaats in taak stappen een alias met de `$`-instructie, zoals in dit voor beeld:

```yaml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Afbeeldings aliassen

Elk van de volgende aliassen wijst naar een stabiele afbeelding in micro soft Container Registry (MCR). U kunt deze in de sectie `cmd` van een taak bestand raadplegen zonder een instructie te gebruiken.

| Alias | afbeelding |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

In het volgende voor beeld wordt een aantal aliassen gebruikt om afbeeldings Tags te [verwijderen](container-registry-auto-purge.md) die ouder zijn dan 7 dagen in de opslag plaats-`samples/hello-world` in het uitvoerings register:

```yaml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Aangepaste alias

Definieer een aangepaste alias in uw YAML-bestand en gebruik deze zoals wordt weer gegeven in het volgende voor beeld. Een alias mag alleen alfanumerieke tekens bevatten. De standaard instructie voor het uitbreiden van een alias is het `$` teken.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

U kunt een koppeling maken naar een extern of lokaal YAML-bestand voor aangepaste alias definities. In het volgende voor beeld wordt een koppeling naar een YAML-bestand in Azure Blob Storage:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Volgende stappen

Voor een overzicht van taken met meerdere stappen raadpleegt u de [taken voor het maken, testen en bijwerken van meerdere stappen in ACR-taken](container-registry-tasks-multi-step.md).

Zie het [overzicht van ACR-taken](container-registry-tasks-overview.md)voor builds met één stap.



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
