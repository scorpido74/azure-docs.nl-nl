---
title: YAML-referentie - ACR-taken
description: Raadpleeg voor het definiëren van taken in YAML voor ACR-taken, inclusief taakeigenschappen, staptypen, stapeigenschappen en ingebouwde variabelen.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: 9558f698b4a9dbca46431fc02ced6ae30de29121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246979"
---
# <a name="acr-tasks-reference-yaml"></a>ACR-taken referentie: YAML

Multi-step taakdefinitie in ACR-taken biedt een containergerichte compute-primitieve focus op het bouwen, testen en patchen van containers. In dit artikel worden de opdrachten, parameters, eigenschappen en syntaxis voor de YAML-bestanden die uw taken in meerdere stappen definiëren, gedefinieerd.

Dit artikel bevat referentie voor het maken van YAML-bestanden voor meerdere stappen voor ACR-taken. Zie het [overzicht ACR-taken](container-registry-tasks-overview.md)als u een inleiding wilt tot ACR-taken.

## <a name="acr-taskyaml-file-format"></a>acr-task.yaml-bestandsindeling

ACR-taken ondersteunt taakdeclaratie in meerdere stappen in de standaard YAML-syntaxis. U definieert de stappen van een taak in een YAML-bestand. U de taak vervolgens handmatig uitvoeren door het bestand door te geven aan de opdracht [AZ ACR Run.][az-acr-run] Of gebruik het bestand om een taak te maken met [az acr-taakmaken][az-acr-task-create] die automatisch wordt geactiveerd op een Git commit of basisafbeeldingsupdate. Hoewel dit artikel `acr-task.yaml` het bestand met de stappen noemt, ondersteunt ACR Tasks elke geldige bestandsnaam met een [ondersteunde extensie](#supported-task-filename-extensions).

De primitieven `acr-task.yaml` op het hoogste niveau zijn **taakeigenschappen,** **staptypen**en **stapeigenschappen:**

* [Taakeigenschappen](#task-properties) zijn van toepassing op alle stappen tijdens taakuitvoering. Er zijn verschillende algemene taakeigenschappen, waaronder:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Taakstaptypen](#task-step-types) vertegenwoordigen de typen acties die in een taak kunnen worden uitgevoerd. Er zijn drie staptypen:
  * `build`
  * `push`
  * `cmd`
* [Eigenschappen van taakstappen](#task-step-properties) zijn parameters die van toepassing zijn op een afzonderlijke stap. Er zijn verschillende stapeigenschappen, waaronder:
  * `startDelay`
  * `timeout`
  * `when`
  * ... en nog veel meer.

De basisindeling `acr-task.yaml` van een bestand, inclusief enkele algemene stap-eigenschappen, volgt. Hoewel het niet een uitputtende weergave is van alle beschikbare stapeigenschappen of staptypegebruik, biedt het een snel overzicht van de basisbestandsindeling.

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

### <a name="supported-task-filename-extensions"></a>Ondersteunde bestandsnaamextensies voor taken

ACR Tasks heeft verschillende bestandsnaamextensies gereserveerd, waaronder `.yaml`die als taakbestand worden verwerkt. Elke extensie *die niet* in de volgende lijst staat, wordt door ACR-taken beschouwd als een Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML is de enige bestandsindeling die momenteel wordt ondersteund door ACR Tasks. De andere bestandsnaamextensies zijn gereserveerd voor mogelijke toekomstige ondersteuning.

## <a name="run-the-sample-tasks"></a>De voorbeeldtaken uitvoeren

Er zijn verschillende voorbeeldtaakbestanden waarnaar wordt verwezen in de volgende secties van dit artikel. De voorbeeldtaken bevinden zich in een openbare GitHub-repository, [Azure-Samples/acr-taken.][acr-tasks] U ze uitvoeren met de Azure CLI-opdracht [az acr-run][az-acr-run]. De voorbeeldopdrachten zijn vergelijkbaar met:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

De opmaak van de voorbeeldopdrachten gaat ervan uit dat u een standaardregister `--registry` hebt geconfigureerd in de Azure CLI, zodat ze de parameter weglaten. Als u een standaardregister wilt configureren, `--defaults` gebruikt u de `acr=REGISTRY_NAME` opdracht [AZ configureren][az-configure] met de parameter, die een waarde accepteert.

U bijvoorbeeld de Azure CLI configureren met een standaardregister met de naam 'myregistry':

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Taakeigenschappen

Taakeigenschappen worden doorgaans boven aan `acr-task.yaml` een bestand weergegeven en zijn algemene eigenschappen die van toepassing zijn tijdens de volledige uitvoering van de taakstappen. Sommige van deze globale eigenschappen kunnen binnen een afzonderlijke stap worden overschreven.

| Eigenschap | Type | Optioneel | Beschrijving | Ondersteunde ondersteuning overschrijven | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | tekenreeks | Ja | De versie `acr-task.yaml` van het bestand zoals ontleed door de ACR Tasks-service. Hoewel ACR Tasks ernaar streeft om achterwaartse compatibiliteit te behouden, stelt deze waarde ACR-taken in staat om compatibiliteit te behouden binnen een gedefinieerde versie. Als dit niet is opgegeven, wordt de nieuwste versie standaard weergegeven. | Nee | Geen |
| `stepTimeout` | int (seconden) | Ja | Het maximum aantal seconden dat een stap kan uitvoeren. Als de eigenschap is opgegeven voor een `timeout` taak, wordt de standaardeigenschap van alle stappen ingesteld. Als `timeout` de eigenschap in een stap is opgegeven, wordt de eigenschap die door de taak wordt geleverd, overschreven. | Ja | 600 (10 minuten) |
| `workingDirectory` | tekenreeks | Ja | De werkmap van de container tijdens runtime. Als de eigenschap is opgegeven voor een `workingDirectory` taak, wordt de standaardeigenschap van alle stappen ingesteld. Als deze in een stap is opgegeven, wordt de eigenschap die door de taak wordt geleverd, overschreven. | Ja | `/workspace` |
| `env` | [tekenreeks, tekenreeks, ...] | Ja |  Array met `key=value` tekenreeksen in indeling die de omgevingsvariabelen voor de taak definiëren. Als de eigenschap is opgegeven voor een `env` taak, wordt de standaardeigenschap van alle stappen ingesteld. Als deze in een stap is opgegeven, worden alle omgevingsvariabelen die van de taak zijn overgenomen, overschreven. | Geen |
| `secrets` | [geheim, geheim, ...] | Ja | Array van [geheime](#secret) objecten. | Geen |
| `networks` | [netwerk, netwerk, ...] | Ja | Array van [netwerkobjecten.](#network) | Geen |

### <a name="secret"></a>geheim

Het geheime object heeft de volgende eigenschappen.

| Eigenschap | Type | Optioneel | Beschrijving | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | tekenreeks | Nee | De identificatie van het geheim. | Geen |
| `keyvault` | tekenreeks | Ja | De URL van Azure Key Vault Secret. | Geen |
| `clientID` | tekenreeks | Ja | De client-id van de door de gebruiker toegewezen beheerde identiteit voor [Azure-resources.](container-registry-tasks-authentication-managed-identity.md) | Geen |

### <a name="network"></a>network

Het netwerkobject heeft de volgende eigenschappen.

| Eigenschap | Type | Optioneel | Beschrijving | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | tekenreeks | Nee | De naam van het netwerk. | Geen |
| `driver` | tekenreeks | Ja | De chauffeur om het netwerk te beheren. | Geen |
| `ipv6` | Booleaanse waarde | Ja | Of IPv6-netwerken is ingeschakeld. | `false` |
| `skipCreation` | Booleaanse waarde | Ja | Of u het maken van netwerken wilt overslaan. | `false` |
| `isDefault` | Booleaanse waarde | Ja | Of het netwerk een standaardnetwerk is dat is voorzien van Azure Container Registry | `false` |

## <a name="task-step-types"></a>Taakstaptypen

ACR Tasks ondersteunt drie staptypen. Elk staptype ondersteunt verschillende eigenschappen, die in de sectie voor elk staptype worden beschreven.

| Staptype | Beschrijving |
| --------- | ----------- |
| [`build`](#build) | Hiermee bouwt u `docker build` een containerafbeelding met behulp van een bekende syntaxis. |
| [`push`](#push) | Hiermee wordt `docker push` een van nieuw gebouwde of opnieuw gelabelde afbeeldingen uitgevoerd naar een containerregister. Azure Container Registry, andere privéregisters en de openbare Docker Hub worden ondersteund. |
| [`cmd`](#cmd) | Hiermee wordt een container uitgevoerd als een opdracht, met parameters die worden doorgegeven aan die van `[ENTRYPOINT]`de container. Het `cmd` staptype ondersteunt `env` `detach`parameters zoals `docker run` , en andere vertrouwde opdrachtopties, waardoor eenheids- en functionele tests met gelijktijdige containeruitvoering mogelijk zijn. |

## <a name="build"></a>bouwen

Een containerafbeelding maken. Het `build` staptype vertegenwoordigt een multi-tenant, `docker build` veilige manier om in de cloud te worden uitgevoerd als een eersteklas primitieve.

### <a name="syntax-build"></a>Syntaxis: bouwen

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Het `build` staptype ondersteunt de parameters in de volgende tabel. Het `build` staptype ondersteunt ook alle buildopties van de `--build-arg` [buildopdracht docker,](https://docs.docker.com/engine/reference/commandline/build/) zoals het instellen van build-time variabelen.

| Parameter | Beschrijving | Optioneel |
| --------- | ----------- | :-------: |
| `-t`&#124;`--image` | Hiermee definieert `image:tag` u de volledig gekwalificeerde van de gebouwde afbeelding.<br /><br />Aangezien afbeeldingen kunnen worden gebruikt voor validaties van binnentaken, `push` zoals functionele tests, hoeven niet alle afbeeldingen naar een register te worden uitgevoerd. Als u echter een afbeelding in een taakuitvoering wilt instantie, heeft de afbeelding wel een naam nodig om naar te verwijzen.<br /><br />In `az acr build`tegenstelling tot het uitvoeren van ACR-taken biedt niet standaard pushgedrag. Met ACR-taken wordt in het standaardscenario uitgegaan van de mogelijkheid om een afbeelding te bouwen, valideren en vervolgens te pushen. Zie [push](#push) voor hoe je optioneel ingebouwde afbeeldingen pusht. | Ja |
| `-f`&#124;`--file` | Hiermee geeft u het `docker build`Dockerbestand op dat is doorgegeven aan . Als dit niet is opgegeven, wordt het standaard Dockerfile in de hoofdmap van de context aangenomen. Als u een Dockerfile wilt opgeven, geeft u de bestandsnaam ten opzichte van de hoofdmap van de context door. | Ja |
| `context` | De hoofdmap `docker build`is doorgegeven aan . De hoofdmap van elke taak is ingesteld op een gedeelde [workingDirectory](#task-step-properties)en bevat de hoofdmap van de bijbehorende Git-map met gekloonde map. | Nee |

### <a name="properties-build"></a>Eigenschappen: bouwen

Het `build` staptype ondersteunt de volgende eigenschappen. Meer informatie over deze eigenschappen vindt u in de sectie Eigenschappen van [taakstappen](#task-step-properties) in dit artikel.

| | | |
| -------- | ---- | -------- |
| `detach` | Booleaanse waarde | Optioneel |
| `disableWorkingDirectoryOverride` | Booleaanse waarde | Optioneel |
| `entryPoint` | tekenreeks | Optioneel |
| `env` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `expose` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `id` | tekenreeks | Optioneel |
| `ignoreErrors` | Booleaanse waarde | Optioneel |
| `isolation` | tekenreeks | Optioneel |
| `keep` | Booleaanse waarde | Optioneel |
| `network` | object | Optioneel |
| `ports` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `pull` | Booleaanse waarde | Optioneel |
| `repeat` | int | Optioneel |
| `retries` | int | Optioneel |
| `retryDelay` | int (seconden) | Optioneel |
| `secret` | object | Optioneel |
| `startDelay` | int (seconden) | Optioneel |
| `timeout` | int (seconden) | Optioneel |
| `when` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `workingDirectory` | tekenreeks | Optioneel |

### <a name="examples-build"></a>Voorbeelden: bouwen

#### <a name="build-image---context-in-root"></a>Afbeelding maken - context in root

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Afbeelding maken - context in submap

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>Push

Een of meer ingebouwde of opnieuw gelabelde afbeeldingen naar een containerregister duwen. Ondersteunt pushen naar privéregisters zoals Azure Container Registry of naar de openbare Docker Hub.

### <a name="syntax-push"></a>Syntaxis: push

Het `push` staptype ondersteunt een verzameling afbeeldingen. De syntaxis van yAML-verzameling ondersteunt inline- en geneste opmaak. Als u één afbeelding duwt, wordt deze doorgaans weergegeven met inline-syntaxis:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Gebruik geneste syntaxis voor een betere leesbaarheid wanneer u meerdere afbeeldingen pusht:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Eigenschappen: pushen

Het `push` staptype ondersteunt de volgende eigenschappen. Meer informatie over deze eigenschappen vindt u in de sectie Eigenschappen van [taakstappen](#task-step-properties) in dit artikel.

| | | |
| -------- | ---- | -------- |
| `env` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `id` | tekenreeks | Optioneel |
| `ignoreErrors` | Booleaanse waarde | Optioneel |
| `startDelay` | int (seconden) | Optioneel |
| `timeout` | int (seconden) | Optioneel |
| `when` | [tekenreeks, tekenreeks, ...] | Optioneel |

### <a name="examples-push"></a>Voorbeelden: pushen

#### <a name="push-multiple-images"></a>Meerdere afbeeldingen pushen

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Bouwen, duwen en uitvoeren

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Het `cmd` staptype voert een container uit.

### <a name="syntax-cmd"></a>Syntaxis: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Eigenschappen: cmd

Het `cmd` staptype ondersteunt de volgende eigenschappen:

| | | |
| -------- | ---- | -------- |
| `detach` | Booleaanse waarde | Optioneel |
| `disableWorkingDirectoryOverride` | Booleaanse waarde | Optioneel |
| `entryPoint` | tekenreeks | Optioneel |
| `env` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `expose` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `id` | tekenreeks | Optioneel |
| `ignoreErrors` | Booleaanse waarde | Optioneel |
| `isolation` | tekenreeks | Optioneel |
| `keep` | Booleaanse waarde | Optioneel |
| `network` | object | Optioneel |
| `ports` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `pull` | Booleaanse waarde | Optioneel |
| `repeat` | int | Optioneel |
| `retries` | int | Optioneel |
| `retryDelay` | int (seconden) | Optioneel |
| `secret` | object | Optioneel |
| `startDelay` | int (seconden) | Optioneel |
| `timeout` | int (seconden) | Optioneel |
| `when` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `workingDirectory` | tekenreeks | Optioneel |

Details van deze eigenschappen vindt u in de sectie Eigenschappen van [taakstappen](#task-step-properties) in dit artikel.

### <a name="examples-cmd"></a>Voorbeelden: cmd

#### <a name="run-hello-world-image"></a>Run hello-world image

Met deze opdracht `hello-world.yaml` wordt het taakbestand uitgevoerd, dat verwijst naar de [hello-world-afbeelding](https://hub.docker.com/_/hello-world/) op Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Run bash beeld en echo "hallo wereld"

Met deze opdracht `bash-echo.yaml` wordt het taakbestand uitgevoerd, dat verwijst naar de [bash-afbeelding](https://hub.docker.com/_/bash/) op Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Specifieke bash-afbeeldingstag uitvoeren

Als u een specifieke afbeeldingsversie `cmd`wilt uitvoeren, geeft u de tag op in de .

Met deze opdracht `bash-echo-3.yaml` wordt het taakbestand uitgevoerd, dat verwijst naar de [afbeelding bash:3.0](https://hub.docker.com/_/bash/) op Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Aangepaste afbeeldingen uitvoeren

Het `cmd` staptype verwijst naar `docker run` afbeeldingen met behulp van de standaardindeling. Afbeeldingen die niet voorafzijn gegaan met een register, worden verondersteld afkomstig te zijn van docker.io. Het vorige voorbeeld kan ook worden weergegeven als:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Door gebruik `docker run` te maken `cmd` van de standaard referentieconventie voor afbeeldingen, kunnen afbeeldingen worden uitgevoerd vanuit elk privéregister of de openbare Docker Hub. Als u verwijst naar afbeeldingen in hetzelfde register waarin ACR-taak wordt uitgevoerd, hoeft u geen registerreferenties op te geven.

* Voer een afbeelding uit die van een Azure-containerregister. In het volgende voorbeeld wordt `myregistry`ervan uitgegaan dat `myimage:mytag`u een register met de naam en een aangepaste afbeelding hebt.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* De registerverwijzing generaliseren met een variabele uitvoeren of alias

    In plaats van uw registernaam `acr-task.yaml` in een bestand hard te coderen, u deze draagbaarder maken met behulp van een [variabele uitvoeren](#run-variables) of [alias.](#aliases) De `Run.Registry` variabele `$Registry` of alias wordt tijdens runtime uitgebreid naar de naam van het register waarin de taak wordt uitgevoerd.

    Als u bijvoorbeeld de vorige taak wilt generaliseren zodat deze in een Azure-containerregister werkt, verwijst u naar de $Registry variabele in de afbeeldingsnaam:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Eigenschappen van taakstap

Elk staptype ondersteunt verschillende eigenschappen die geschikt zijn voor het type. In de volgende tabel worden alle beschikbare stapeigenschappen gedefinieerd. Niet alle staptypen ondersteunen alle eigenschappen. Zie de referentiesecties [cmd,](#cmd) [build](#build)en [push](#push) step type om te zien welke van deze eigenschappen beschikbaar zijn voor elk staptype.

| Eigenschap | Type | Optioneel | Beschrijving | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | Booleaanse waarde | Ja | Of de container moet worden losgemaakt tijdens het hardlopen. | `false` |
| `disableWorkingDirectoryOverride` | Booleaanse waarde | Ja | Of u `workingDirectory` de overschrijffunctionaliteit wilt uitschakelen. Gebruik dit in `workingDirectory` combinatie met volledige controle over de werkmap van de container. | `false` |
| `entryPoint` | tekenreeks | Ja | Overschrijft `[ENTRYPOINT]` de container van een stap. | Geen |
| `env` | [tekenreeks, tekenreeks, ...] | Ja | Array met `key=value` tekenreeksen in indeling die de omgevingsvariabelen voor de stap definiëren. | Geen |
| `expose` | [tekenreeks, tekenreeks, ...] | Ja | Array van poorten die worden blootgesteld vanuit de container. |  Geen |
| [`id`](#example-id) | tekenreeks | Ja | Identificeert op unieke wijze de stap binnen de taak. Andere stappen in de taak kunnen `id`verwijzen naar een stap, bijvoorbeeld voor afhankelijkheidscontrole met `when`.<br /><br />Het `id` is ook de naam van de lopende container. Processen die in andere containers in `id` de taak worden uitgevoerd, kunnen bijvoorbeeld verwijzen naar de DNS-hostnaam of voor toegang tot deze met dockerlogs [id], bijvoorbeeld. | `acb_step_%d`, `%d` waar is de 0-gebaseerde index van de stap top-down in het YAML-bestand |
| `ignoreErrors` | Booleaanse waarde | Ja | Of u de stap als succesvol wilt markeren, ongeacht of er een fout is opgetreden tijdens de uitvoering van containers. | `false` |
| `isolation` | tekenreeks | Ja | Het isolatieniveau van de container. | `default` |
| `keep` | Booleaanse waarde | Ja | Of de container van de stap na de uitvoering moet worden bewaard. | `false` |
| `network` | object | Ja | Hiermee identificeert u een netwerk waarin de container wordt uitgevoerd. | Geen |
| `ports` | [tekenreeks, tekenreeks, ...] | Ja | Array van poorten die worden gepubliceerd van de container naar de host. |  Geen |
| `pull` | Booleaanse waarde | Ja | Of u een trekkracht van de container moet forceren voordat u deze uitvoert om cachinggedrag te voorkomen. | `false` |
| `privileged` | Booleaanse waarde | Ja | Of u de container in de bevoorrechte modus wilt uitvoeren. | `false` |
| `repeat` | int | Ja | Het aantal pogingen om de uitvoering van een container te herhalen. | 0 |
| `retries` | int | Ja | Het aantal pogingen om te proberen als een container de uitvoering ervan niet haalt. Een nieuwe poging wordt alleen geprobeerd als de exitcode van een container niet-nul is. | 0 |
| `retryDelay` | int (seconden) | Ja | De vertraging in seconden tussen de uitvoering van een container. | 0 |
| `secret` | object | Ja | Hiermee identificeert u een geheim of beheerde azure key [vault-geheim voor Azure-bronnen.](container-registry-tasks-authentication-managed-identity.md) | Geen |
| `startDelay` | int (seconden) | Ja | Aantal seconden om de uitvoering van een container uit te stellen. | 0 |
| `timeout` | int (seconden) | Ja | Maximaal aantal seconden dat een stap kan uitvoeren voordat deze wordt beëindigd. | 600 |
| [`when`](#example-when) | [tekenreeks, tekenreeks, ...] | Ja | Hiermee configureert u de afhankelijkheid van een stap van een of meer andere stappen binnen de taak. | Geen |
| `user` | tekenreeks | Ja | De gebruikersnaam of UID van een container | Geen |
| `workingDirectory` | tekenreeks | Ja | Hiermee stelt u de werkmap in voor een stap. ACR Tasks maakt standaard een hoofdmap als werkmap. Als uw build echter meerdere stappen heeft, kunnen eerdere stappen artefacten delen met latere stappen door dezelfde werkmap op te geven. | `/workspace` |

### <a name="examples-task-step-properties"></a>Voorbeelden: eigenschappen taakstap

#### <a name="example-id"></a>Voorbeeld: id

Bouw twee afbeeldingen en maak een functionele testafbeelding. Elke stap wordt geïdentificeerd `id` door een unieke die andere `when` stappen in de taakverwijzing in hun eigenschap.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Voorbeeld: wanneer

De `when` eigenschap geeft de afhankelijkheid van een stap op van andere stappen binnen de taak. Het ondersteunt twee parameterwaarden:

* `when: ["-"]`- Geeft geen afhankelijkheid van andere stappen aan. Een stapsgewijze `when: ["-"]` uitvoering wordt onmiddellijk gestart en maakt gelijktijdige uitvoering mogelijk.
* `when: ["id1", "id2"]`- Geeft aan dat de `id` stap afhankelijk `id` is van stappen met "id1" en "id2". Deze stap wordt pas uitgevoerd als de stappen 'id1' en 'id2' zijn voltooid.

Als `when` deze stap niet in een stap is opgegeven, is `acr-task.yaml` deze stap afhankelijk van de voltooiing van de vorige stap in het bestand.

Sequentiële uitvoering `when`van stappen zonder :

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Sequentiële uitvoering `when`van stappen met :

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Parallelle beelden bouwen:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Parallelle image build en afhankelijke testen:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Variabelen uitvoeren

ACR-taken bevat een standaardset variabelen die beschikbaar zijn voor taakstappen wanneer ze worden uitgevoerd. Deze variabelen kunnen worden benaderd met `{{.Run.VariableName}}`behulp `VariableName` van de indeling , waar is een van de volgende:

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

De variabele namen spreken over het algemeen voor zich. Details volgen voor veelgebruikte variabelen. Met de YAML-versie `v1.1.0`u een verkorte, vooraf gedefinieerde [taakalias](#aliases) gebruiken in plaats van de meeste runvariabelen. Gebruik bijvoorbeeld in `{{.Run.Registry}}`plaats van `$Registry` , gebruik de alias.

### <a name="runid"></a>Run.ID

Elke run, `az acr run`door, of trigger gebaseerde uitvoering van taken gemaakt door `az acr task create`, heeft een unieke ID. De ID vertegenwoordigt de run die momenteel wordt uitgevoerd.

Meestal gebruikt voor een unieke taging van een afbeelding:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

De volledig gekwalificeerde servernaam van het register. Meestal gebruikt om generiek verwijzen naar het register waar de taak wordt uitgevoerd.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run.RegistryName

De naam van het containerregister. Meestal gebruikt in taakstappen waarvoor geen volledig gekwalificeerde servernaam `cmd` vereist is, bijvoorbeeld stappen die Azure CLI-opdrachten uitvoeren op registers.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

De huidige UTC-tijd dat de run begon.

### <a name="runcommit"></a>Run.Commit Run.Commit

Voor een taak die wordt geactiveerd door een commit naar een GitHub-repository, de commit-id.

### <a name="runbranch"></a>Run.Branch

Voor een taak die wordt geactiveerd door een commit naar een GitHub-repository, de naam van de branch.

## <a name="aliases"></a>Aliassen

Vanaf `v1.1.0`: ACR-taken ondersteunt aliassen die beschikbaar zijn voor taakstappen wanneer ze worden uitgevoerd. Aliassen zijn qua concept vergelijkbaar met aliassen (opdrachtsnelkoppelingen) die worden ondersteund in bash en enkele andere opdrachtshells. 

Met een alias u elke opdracht of groep opdrachten (inclusief opties en bestandsnamen) starten door één woord in te voeren.

ACR Tasks ondersteunt verschillende vooraf gedefinieerde aliassen en ook aangepaste aliassen die u maakt.

### <a name="predefined-aliases"></a>Vooraf gedefinieerde aliassen

De volgende taakaliassen zijn beschikbaar voor gebruik in plaats van [runvariabelen:](#run-variables)

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

Ga in taakstappen vooraf aan `$` een alias met de richtlijn, zoals in dit voorbeeld:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Afbeeldingsaliassen

Elk van de volgende aliassen wijst naar een stabiele afbeelding in Microsoft Container Registry (MCR). U naar elk van `cmd` hen verwijzen in het gedeelte van een taakbestand zonder een richtlijn te gebruiken.

| Alias | Installatiekopie |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

In de volgende voorbeeldtaak worden verschillende aliassen gebruikt om `samples/hello-world` afbeeldingstags te verwijderen [die](container-registry-auto-purge.md) ouder zijn dan 7 dagen in de repo in het run-register:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Aangepaste alias

Definieer een aangepaste alias in uw YAML-bestand en gebruik deze zoals in het volgende voorbeeld wordt weergegeven. Een alias kan alleen alfanumerieke tekens bevatten. De standaardrichtlijn om een `$` alias uit te vouwen is het teken.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

U een koppeling maken naar een extern of lokaal YAML-bestand voor aangepaste aliasdefinities. In het volgende voorbeeld wordt koppelingen naar een YAML-bestand in Azure blob-opslag:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Volgende stappen

Zie de [taken Met meerdere stappen uitvoeren in ACR-taken](container-registry-tasks-multi-step.md)voor een overzicht van taken in meerdere stappen.

Zie het [overzicht ACR-taken](container-registry-tasks-overview.md)voor builds in één stap.



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
