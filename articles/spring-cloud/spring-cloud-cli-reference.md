---
title: az lentewolk
description: Azure Spring Cloud beheren met de Azure CLI
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298781"
---
# <a name="az-spring-cloud"></a>az lente-wolk

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Azure Spring Cloud beheren met de Azure CLI

>[!Note]
> Azure Spring Cloud is momenteel in preview.  Deze opdrachten kunnen worden gewijzigd of verwijderd in een toekomstige release.

| az lente-wolk |  |
|------|------:|
| [az spring-cloud maken](#az-spring-cloud-create) | Maak een Azure Spring Cloud-exemplaar. |
| [az spring-cloud verwijderen](#az-spring-cloud-delete) | Een Azure Spring Cloud-exemplaar verwijderen. |
| [az spring-cloud lijst](#az-spring-cloud-list) | Vermeld alle Azure Spring Cloud-exemplaren in de opgegeven brongroep, anders worden de abonnements-id's vermeld. |
| [az lente-wolk show](#az-spring-cloud-show) | Toon de details voor een Azure Spring Cloud. |

| az spring-cloud app | Opdrachten voor het beheren van apps in de Azure Spring Cloud.  |
| ---- | ----: |
| [az spring-cloud app maken](#az-spring-cloud-app-create) | Maak een nieuwe app met een standaardimplementatie in de Azure Spring Cloud. |
| [az spring-cloud app verwijderen](#az-spring-cloud-app-delete) | Een app verwijderen in de Azure Spring Cloud. |
| [az spring-cloud app implementeren](#az-spring-cloud-app-deploy) | Implementeren van broncode of een vooraf gebouwde binaire naar een app en gerelateerde configuraties bijwerken. |
| [lijst met az spring-cloud-apps](#az-spring-cloud-app-list) | Vermeld alle apps in de Azure Spring Cloud. |
| [az spring-cloud app opnieuw opgestart](#az-spring-cloud-app-restart) | Start exemplaren van de app opnieuw met behulp van standaardinstellingen voor productie-implementatie. |
| [az spring-cloud app schaal](#az-spring-cloud-app-scale) | Een app of de implementaties handmatig schalen. |
| [az spring-cloud app set-deployment](#az-spring-cloud-app-set-deployment) | De productie-implementatie van een app instellen. |
| [az spring-cloud app show](#az-spring-cloud-app-show) | De details van een app weergeven in de Azure Spring Cloud. |
| [az spring-cloud app show-deploy-log](#az-spring-cloud-app-show-deploy-log) | Build logs weergeven voor de nieuwste implementatie van de bron. Standaard voor productie-implementatie. |
| [az spring-cloud app van start](#az-spring-cloud-app-start) | Start exemplaren van de app met standaardinstellingen voor productie-implementatie. |
| [az spring-cloud app stoppen](#az-spring-cloud-app-stop) | Exemplaren van de app stoppen met standaardinstellingen voor productie-implementatie. |
| [update az spring-cloud app](#az-spring-cloud-app-update) | Werk de configuratie van de opgegeven app bij. |

| az spring-cloud app binding | Opdrachten voor het beheren van bindingen met Azure Data Services.  De app moet opnieuw worden opgestart voordat deze instellingen van kracht worden. |
| --- | ---: |
| [bindende lijst van az spring-cloud-apps](#az-spring-cloud-app-binding-list) | Vermeld alle servicebindingen in een app. |
| [az spring-cloud app binding verwijderen](#az-spring-cloud-app-binding-remove) | Verwijder een servicebinding uit de app. |
| [az spring-cloud app binding show](#az-spring-cloud-app-binding-show) | Toon de details van een servicebinding. |
| [az spring-cloud app binding cosmos toevoegen](#az-spring-cloud-app-binding-cosmos-add) | Bind een Azure CosmosDB met de app. |
| [az spring-cloud app bindende cosmos update](#az-spring-cloud-app-binding-cosmos-update) | Een Azure CosmosDB-servicebinding bijwerken. |
| [az spring-cloud app binding mysql add](#az-spring-cloud-app-binding-mysql-add) | Bind een Azure Database voor MySQL met de app. |
| [az spring-cloud app binding mysql update](#az-spring-cloud-app-binding-mysql-update) | Een Azure Database voor MySQL-servicebinding bijwerken. |
| [az spring-cloud app binding redis add az spring-cloud app binding redis add az spring-cloud app binding redis add az spring](#az-spring-cloud-app-binding-redis-add) | Bind een Azure-cache voor Redis met de app. |
| [az spring-cloud app binding redis update](#az-spring-cloud-app-binding-redis-update) | Een Azure-cache voor Redis-servicebinding bijwerken. |

| inzet AZ Spring-Cloud App | Opdrachten voor het beheren van de implementatielevenscyclus van een app in Azure Spring Cloud. |
| --- | ---: |
| [az spring-cloud app-implementatie maken](#az-spring-cloud-app-deployment-create) | Maak een faseringsimplementatie voor de app. |
| [implementatie az spring-cloud-app verwijderen](#az-spring-cloud-app-deployment-delete) | Een implementatie van de app verwijderen. |
| [az spring-cloud app deployment list](#az-spring-cloud-app-deployment-list) | Alle implementaties in een app weergeven. |
| [uitzending az spring-cloud app show](#az-spring-cloud-app-deployment-show) | Details van de implementatie weergeven. |

| az spring-cloud config-server | Opdrachten voor het beheer van de Azure Spring Cloud Config Server. |
| --- | ---: |
| [az spring-cloud config-server duidelijk](#az-spring-cloud-config-server-clear) | Wis alle instellingen in de Config-server. |
| [az spring-cloud config-server set](#az-spring-cloud-config-server-set) | Definieer Config-server uit een YAML-bestand. |
| [az spring-cloud config-server show](#az-spring-cloud-config-server-show) | Config Server-configuratie weergeven. |
| [az spring-cloud config server git set](#az-spring-cloud-config-server-git-set) | Definieer git-eigenschappen voor de Config-server.  Eerdere waarden worden overschreven. |
| [az spring-cloud config server git repo toevoegen](#az-spring-cloud-config-server-git-repo-add) | Voeg een nieuwe git repository config toe aan de Config Server. |
| [az spring-cloud config server git repo lijst](#az-spring-cloud-config-server-git-repo-list) | Vermeld alle git repository configs voor de Config Server. |
| [az spring-cloud config server git repo verwijderen](#az-spring-cloud-config-server-git-repo-remove) | Verwijder de opgegeven git-repository van de Config-server. |

| az spring-cloud test-endpoint | Opdrachten voor het beheren van endpoint-tests in Azure Spring Cloud |
| --- | ---: |
| [az spring-cloud test-endpoint uitschakelen](#az-spring-cloud-test-endpoint-disable) | Testeindpunt uitschakelen. |
| [az spring-cloud test-endpoint enable](#az-spring-cloud-test-endpoint-enable) | Testeindpunt inschakelen. |
| [az spring-cloud test-endpoint lijst](#az-spring-cloud-test-endpoint-list) | Lijst testeindpunttoetsen. |
| [az spring-cloud test-endpoint renew-key](#az-spring-cloud-test-endpoint-renew-key) | Regenereren van een test-eindpuntsleutel. |

## <a name="az-spring-cloud-create"></a>az spring-cloud maken

Maak een nieuwe app met een standaardimplementatie in de Azure Spring Cloud.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --naam -n | Naam voor deze Azure Spring Cloud-instantie. |
| --resource-groep -g | Hiermee geeft u de resourcegroep voor deze app op.  De standaardgroep configureren met`az configure --defaults group=<name>` |

| Optionele parameters | |
| --- | ---: |
| --locatie -l | Hiermee geeft u de serverlocatie voor deze app op.  Geldige locaties zoeken met`az account list-locations` |
| --no-wait | Niet voor langdurige bewerkingen te voltooien.

### <a name="examples"></a>Voorbeelden

Een nieuwe Azure Spring Cloud maken in WestUS

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az spring-cloud verwijderen

Een Azure Spring Cloud-exemplaar verwijderen.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --naam -n | Naam van de instantie Azure Spring Cloud die moet worden verwijderd. |
| --resource-groep -g | Naam van de brongroep waartoe de Azure Spring Cloud behoort. |

| Optionele parameters | |
| --- | ---: |
| -no-wait | Wacht niet tot de langlopende bewerkingen zijn voltooid. |

### <a name="example"></a>Voorbeeld

Een Azure Spring Cloud-instantie genaamd 'MyService' verwijderen uit 'MyResourceGroup'.

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az spring-cloud lijst

Vermeld alle Azure Spring Cloud-exemplaren die zijn gekoppeld aan de opgegeven brongroep. Als er geen resourcegroep is opgegeven, vermeldt u de abonnements-id's.

```azurecli
az spring-cloud list --resource-group -g
```

| Vereiste parameters | |
| --- | ---: |
| --resource-groep -g | De naam van de resourcegroep. |

## <a name="az-spring-cloud-show"></a>az lente-wolk show

De details weergeven voor de opgegeven Azure Spring Cloud-instantie.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Vereiste parameters | |
| --- | ---: |
| --naam -n | Naam van de instantie Azure Spring Cloud. |
| --resource-groep -g | Naam van de Resourcegroep waartoe de instantie Azure Spring Cloud behoort.

## <a name="az-spring-cloud-app-create"></a>az spring-cloud app maken

Maak een nieuwe app in een Azure Spring Cloud.

```azurecli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| Vereiste parameters | |
| --- | ---: |
| --naam -n | Naam van de app. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --cpu | Aantal virtuele kernen per exemplaar.  Standaard: 1. |
| --enable-persistent-storage | Booleaanse waarde.  Als dit waar is, monteert u een schijf van 50 GB met standaardpad. |
| aantal --instance- | Aantal gevallen.  Standaard: 1. |
| --is-publiek | Booleaanse waarde.  Als dit waar is, wijst u een publiek domein toe. |
| --geheugen | Aantal GB geheugen per exemplaar.  Standaard: 1. |

### <a name="examples"></a>Voorbeelden

Maak een app met de standaardconfiguratie.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Maak een openbaar toegankelijke app met 3 exemplaren.  Elke instantie heeft 3 GB geheugen en 2 CPU-cores.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az spring-cloud app verwijderen

Hiermee verwijdert u een app in de Azure Spring Cloud.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --naam -n | Naam van de app. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deploy"></a>az spring-cloud app implementeren

Implementeer een app naar de Azure Spring Cloud vanuit de broncode of een vooraf gebouwde binaire, en update gerelateerde configuraties.

```azurecli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| Vereiste parameters | |
| --- | ---: |
| --naam -n | Naam van de app. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --cpu | Aantal virtuele CPI-kernen per exemplaar. |
| --implementatie -d | Naam van een bestaande app-implementatie.  Standaard de productie-implementatie als deze niet is opgegeven. |
| --env | Ruimtegescheiden omgevingsvariabelen in de indeling 'sleutel[=waarde]'. |
| aantal --instance- | Aantal exemplaren. |
| --jar-pad | Indien voorzien, implementeren pot van bepaalde pad. Implementeer anders de huidige map als teer. |
| --jvm-opties | Een tekenreeks met JVM-opties.  Gebruik '=' in plaats van ' ' om shell parsing fouten te voorkomen. B.v., `--jvm-options='-Xms1024m -Xmx2048m`. |
| --geheugen | Aantal GB geheugen per exemplaar. |
| --no-wait | Wacht niet tot de langlopende bewerkingen zijn voltooid. |
| --runtime-versie | Runtime-versie van de taal die in de app wordt gebruikt.  Toegestane waarden: `Java_11` `Java_8`, . |
| --target-module | Onderliggende module die moet worden geïmplementeerd.  Vereist wanneer meerdere potpakketten zijn opgebouwd uit de broncode. |
| --versie | Implementatieversie.  Ongewijzigd als niet ingesteld. |

### <a name="examples"></a>Voorbeelden

Broncode implementeren in een app. Hiermee wordt de huidige map verpakt, een binaire functie gebouwd met behulp van de Pivotal Build-service en vervolgens geïmplementeerd in de app.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Implementeer een vooraf gebouwde pot in een app met JVM-opties en omgevingsvariabelen.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Broncode implementeren voor een specifieke implementatie van een app.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>lijst met az spring-cloud-apps

Vermeld alle apps in de instantie Azure Spring Cloud.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Vereiste parameters | |
| --- | ---: |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-restart"></a>az spring-cloud app opnieuw opgestart

Opnieuw starten van instanties van de app.  Standaard de productie-implementatie.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --naam -n | Naam van de app. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --implementatie -d | Naam van de bestaande implementatie van de app.  Standaard de productie-implementatie als deze niet is opgegeven. |
| --no-wait | Wacht niet tot de langlopende bewerkingen zijn voltooid. |

## <a name="az-spring-cloud-app-scale"></a>az spring-cloud app schaal

Een app of de implementaties handmatig schalen.

```azurecli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --naam -n | Naam van de app. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --cpu | Aantal virtuele CPU-kernen per app-exemplaar. |
| --implementatie -d | Naam van de bestaande implementatie van de app.  Standaard de productie-implementatie als deze niet is opgegeven. |
| aantal --instance- | Aantal exemplaren van deze app. |
| --geheugen | Aantal GB geheugen per app-exemplaar. |
| --no-wait | Wacht niet tot langlopende bewerkingen zijn voltooid. |

### <a name="examples"></a>Voorbeelden

Schaal een app op naar 4 CPU-cores en 8 GB geheugen per exemplaar.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Schaal een implementatie van de app uit naar 5 exemplaren.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az spring-cloud app set-deployment

Stel de configuratieopties in voor de productie-implementatie van de app.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --implementatie -d | Naam van een bestaande implementatie van de app. |
| --naam -n | Naam van de app. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --no-wait | Wacht niet tot langlopende bewerkingen zijn voltooid. |

### <a name="examples"></a>Voorbeelden

Een tijdelijke implementatie van de app omwisselen naar productie.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az spring-cloud app show

De details van een app weergeven in de Azure Spring Cloud.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --naam -n | Naam van de app. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az spring-cloud app show-deploy-log

Het buildlogboek van de laatste implementatie van de broncode weergeven.  Standaard in de productieomgeving.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Vereiste parameters | |
| --- | ---: |
| --naam -n | Naam van de app. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --implementatie -d | Naam van een bestaande implementatie van de app.  Standaard in de productieomgeving. |

## <a name="az-spring-cloud-app-start"></a>az spring-cloud app van start

Hiermee start u exemplaren van de app.  Standaard in de productieomgeving.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --naam -n | Naam van de app. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --implementatie -d | Naam van een bestaande implementatie van de app.  Standaard in de productieomgeving. |
| --no-wait | Wacht niet tot langlopende bewerkingen zijn voltooid. |

## <a name="az-spring-cloud-app-stop"></a>az spring-cloud app stoppen

Exemplaren van de app stoppen.  Standaard in de productieomgeving.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --naam -n | Naam van de app. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --implementatie -d | Naam van een bestaande implementatie van de app.  Standaard in de productieomgeving. |
| --no-wait | Wacht niet tot langlopende bewerkingen zijn voltooid. |

## <a name="az-spring-cloud-app-update"></a>update az spring-cloud app

Werk de opgeslagen configuratie van een app bij.

```azurecli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| Vereiste parameters | |
| --- | ---: |
| --naam -n | Naam van de app. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --implementatie -d | Naam van een bestaande implementatie van de app.  Standaard in de productieomgeving. |
| --enable-persistent-storage | Booleaanse.  Als dit waar is, monteert u een schijf van 50 GB met het standaardpad. |
| --env | Ruimtegescheiden omgevingsvariabelen in de indeling 'sleutel[=waarde]'. |
| --is-publiek | Booleaanse.  Als dit waar is, wijst u een openbaar domein toe aan de app. |
| --jvm-opties | Een tekenreeks met JVM-opties.  Gebruik '=' in plaats van ' ' om shell parsing fouten te voorkomen. B.v., `--jvm-options='-Xms1024m -Xmx2048m`. |
| --no-wait | Wacht niet tot langlopende bewerkingen zijn voltooid. |
| --runtime-versie | Runtime-versie van de taal die in de app wordt gebruikt.  Toegestane waarden: `Java_11` `Java_8`, . |

### <a name="example"></a>Voorbeeld

Voeg een omgevingsvariabele toe voor de app.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>bindende lijst van az spring-cloud-apps

Vermeld alle servicebindingen in een app.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-remove"></a>az spring-cloud app binding verwijderen

Verwijder een servicebinding uit de app.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | Naam van de te verwijderen servicebinding. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-show"></a>az spring-cloud app binding show

Toon de details van een servicebinding.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | Naam van de servicebinding. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az spring-cloud app binding cosmos toevoegen

Bind een Azure Cosmos DB met de app.

```azurecli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --api-type | Geef het API-type op met een van de volgende waarden: cassandra, gremlin, mongo, sql, tabel. |
| --app | Naam van de app. |
| --naam | Naam van de servicebinding. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

|Optionele parameters | |
| --- | ---: |
| --collectie-naam | Naam van de collectie.  Vereist bij het gebruik van Gremlin. |
| --database-naam | Naam van de database.  Vereist bij het gebruik van Mongo, SQL en Gremlin. |
| --key-space | Cassandra sleutel-ruimte.  Vereist bij het gebruik van Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az spring-cloud app bindende cosmos update

```azurecli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | Naam van de servicebinding. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

|Optionele parameters | |
| --- | ---: |
| --collectie-naam | Naam van de collectie.  Vereist bij het gebruik van Gremlin. |
| --database-naam | Naam van de database.  Vereist bij het gebruik van Mongo, SQL en Gremlin. |
| --key-space | Cassandra sleutel-ruimte.  Vereist bij het gebruik van Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az spring-cloud app binding mysql add

```azurecli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --database-naam | Naam van de database. |
| --toets | API-sleutel van de service. |
| --naam | Naam van de servicebinding. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --resource-id | Azure-bron-id van de service om mee te verbinden. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |
| --gebruikersnaam | Gebruikersnaam voor toegang tot de database. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az spring-cloud app binding mysql update

Werk de servicebinding van de app bij aan een Azure Database voor MySQL.

```azurecli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | Naam van de servicebinding. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --database-naam | Naam van de database. |
| --toets | API-sleutel van de service. |
| --gebruikersnaam | Gebruikersnaam voor toegang tot de database. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az spring-cloud app binding redis add az spring-cloud app binding redis add az spring-cloud app binding redis add az spring

Bind een Azure-cache voor Redis met de app.

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | Naam van de servicebinding. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --resource-id | Azure-bron-id van de service waarmee u wilt binden. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --disable-ssl -disable-ssl -disable-ssl -disable-ssl | TLS uitschakelen. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az spring-cloud app binding redis update

Werk een servicebinding voor Azure Cache voor Redis bij.

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | Naam van de servicebinding. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --disable-ssl -disable-ssl -disable-ssl -disable-ssl | TLS uitschakelen. |

## <a name="az-spring-cloud-app-deployment-create"></a>az spring-cloud app-implementatie maken

Maak een faseringsimplementatie voor de app.

Gebruik `az spring-cloud app deploy --deployment <staging-deployment>` of 'az spring-cloud app update --deployment <staging deployment>om code te implementeren of instellingen bij te werken naar een bestaande implementatie.

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | Naam van de servicebinding. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --cpu | Aantal virtuele CPU-cores per exemplaar.  Standaardinstelling: 1 |
| --env | Ruimtegescheiden omgevingsvariabelen in de indeling 'sleutel[=waarde]'. |
| aantal --instance- | Aantal exemplaren. Standaard: 1. |
| --jar-pad | Indien voorzien, zet pot.  Implementeer anders de huidige map als teer. |
| --jvm-opties | Een tekenreeks met JVM-opties.  Gebruik '=' in plaats van ' ' om shell parsing fouten te voorkomen. B.v., `--jvm-options='-Xms1024m -Xmx2048m`. |
| --geheugen | Aantal GB geheugen per exemplaar. |
| --no-wait | Wacht niet tot de langlopende bewerkingen zijn voltooid. |
| --runtime-versie | Runtime-versie van de taal die in de app wordt gebruikt.  Toegestane waarden: `Java_11` `Java_8`, . |
| --skip-clone-instellingen | Maak een faseringsimplementatie door de huidige productie-implementatie-instellingen te klonen. |
| --target-module | Onderliggende module die moet worden geïmplementeerd.  Vereist wanneer meerdere potpakketten zijn opgebouwd uit de broncode. |
| --versie | Implementatieversie.  Ongewijzigd als niet ingesteld. |

### <a name="examples"></a>Voorbeelden

Broncode implementeren voor een nieuwe implementatie van de app.  Hiermee wordt de huidige map verpakt, een binaire map gemaakt met behulp van het Pivotal Build System en vervolgens geïmplementeerd.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Implementeer een vooraf gebouwde pot in een app met JVM-opties en omgevingsvariabelen.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>implementatie az spring-cloud-app verwijderen

Een implementatie van de app verwijderen.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | Naam van de implementatie. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-list"></a>az spring-cloud app deployment list

Alle implementaties in een app weergeven.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-show"></a>uitzending az spring-cloud app show

Details van een implementatie weergeven.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | Naam van de implementatie. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --service -s | Naam van de Azure Spring Cloud.  U de standaardservice configureren met behulp van `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-config-server-clear"></a>az spring-cloud config-server duidelijk

Wis alle configuratie-instellingen in de Config-server.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Vereiste parameters | |
| --- | ---: |
| --naam | Naam van de Azure Spring Cloud. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-set"></a>az spring-cloud config-server set

Stel configuratie-instellingen in op de Config-server met een YAML-bestand.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --config-bestand | Bestandspad naar een YAML-manifest voor de configuratie van de Config-server. |
| --naam | Naam van de Azure Spring Cloud. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --no-wait | Niet voor langdurige bewerkingen te voltooien.

## <a name="az-spring-cloud-config-server-show"></a>az spring-cloud config-server show

De instellingen van de Config-server weergeven.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Vereiste parameters | |
| --- | ---: |
| --naam | Naam van de Azure Spring Cloud. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-git-set"></a>az spring-cloud config-server git set

Stel de git-eigenschappen in voor de Config-server.  Hiermee worden alle bestaande git-eigenschappen overschreven.

```azurecli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| Vereiste parameters | |
| --- | ---: |
| --naam | Naam van de Azure Spring Cloud. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --uri | URI van de toegevoegde config. |

| Optionele parameters | |
| --- | ---: |
| --uitstellen | Sla het object tijdelijk op in de lokale cache in plaats van naar Azure te verzenden.  Gebruiken `az cache` om te bekijken / wissen. |
| --host-key | Host-toets voor de toegevoegde config. |
| --host-key-algoritme | Host key algoritme voor de toegevoegde config. |
| --label | Etiket van de toegevoegde config. |
| --password | Wachtwoord van de toegevoegde config. |
| --private-key | Privésleutel van de toegevoegde config. |
| --zoekpaden | Zoek paden van de toegevoegde config.  Gebruik kommadebegrenzes voor meerdere paden. |
| --strict-host-key-checking | Maakt strikte host key controle van de toegevoegde config. |
| --gebruikersnaam | Gebruikersnaam van de toegevoegde config. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az spring-cloud config-server git repo toevoegen

```azurecli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| Vereiste parameters | |
| --- | ---: |
| --naam | Naam van de Azure Spring Cloud. |
| --repo-naam | URI van de repo. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --uri | URI van de toegevoegde config. |

| Optionele parameters | |
| --- | ---: |
| --uitstellen | Sla het object tijdelijk op in de lokale cache in plaats van naar Azure te verzenden.  Gebruiken `az cache` om te bekijken / wissen. |
| --host-key | Host-toets voor de toegevoegde config. |
| --host-key-algoritme | Host key algoritme voor de toegevoegde config. |
| --label | Etiket van de toegevoegde config. |
| --password | Wachtwoord van de toegevoegde config. |
| --patroon | Patroon voor de repo.  Gebruik kommadebegrenzes voor meerdere paden.|
| --private-key | Privésleutel van de toegevoegde config. |
| --zoekpaden | Zoek paden van de toegevoegde config.  Gebruik kommadebegrenzes voor meerdere paden. |
| --strict-host-key-checking | Maakt strikte host key controle van de toegevoegde config. |
| --gebruikersnaam | Gebruikersnaam van de toegevoegde config. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az spring-cloud config-server git repo lijst

Alle git repos weergeven die is gedefinieerd in de Config-server

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Vereiste parameters | |
| --- | ---: |
| --naam | Naam van de Azure Spring Cloud. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --uitstellen | Sla het object tijdelijk op in de lokale cache in plaats van naar Azure te verzenden.  Gebruiken `az cache` om te bekijken / wissen. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az spring-cloud config-server git repo verwijderen

Verwijder een bestaande git repo-configuratie van de Config-server.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Vereiste parameters | |
| --- | ---: |
| --naam | Naam van de Azure Spring Cloud. |
| --repo-naam | URI van de repo. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --uitstellen | Sla het object tijdelijk op in de lokale cache in plaats van naar Azure te verzenden.  Gebruiken `az cache` om te bekijken / wissen. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az spring-cloud test-endpoint uitschakelen

Testeindpunt van de Azure Spring Cloud uitschakelen

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Vereiste parameters | |
| --- | ---: |
| --naam | Naam van de Azure Spring Cloud. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az spring-cloud test-endpoint enable

Schakel testeindpunt in voor de Azure Spring Cloud. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Vereiste parameters | |
| --- | ---: |
| --naam | Naam van de Azure Spring Cloud. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-list"></a>az spring-cloud test-endpoint lijst 

Vermeld de beschikbare testeindpunttoetsen voor de Azure Spring Cloud.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Vereiste parameters | |
| --- | ---: |
| --naam | Naam van de Azure Spring Cloud. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --implementatie -d | Naam van een bestaande implementatie van de app.  Standaard productie als deze niet is opgegeven. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az spring-cloud test-endpoint renew-key

Regenereert een testeindpuntsleutel voor de Azure Spring Cloud.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Vereiste parameters | |
| --- | ---: |
| --naam | Naam van de Azure Spring Cloud. |
| --resource-groep -g | De naam van de resourcegroep.  U de standaardgroep configureren met behulp van `az configure --defaults group=<name>`. |
| --type | Type toets testeindpunt.  Toegestane waarden: Primair, Secundair. |
