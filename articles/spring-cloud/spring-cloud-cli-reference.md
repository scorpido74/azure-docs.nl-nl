---
title: AZ lente-Cloud
description: Azure lente-Cloud beheren met de Azure CLI
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80298781"
---
# <a name="az-spring-cloud"></a>AZ lente-Cloud

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Azure lente-Cloud beheren met de Azure CLI

>[!Note]
> Er is momenteel een preview-versie van Azure lente Cloud beschikbaar.  Deze opdrachten kunnen worden gewijzigd of verwijderd in een toekomstige versie.

| AZ lente-Cloud |  |
|------|------:|
| [AZ lente-Cloud Create](#az-spring-cloud-create) | Een Azure veer Cloud-exemplaar maken. |
| [AZ lente-Cloud verwijderen](#az-spring-cloud-delete) | Een Azure lente Cloud-exemplaar verwijderen. |
| [AZ lente-Cloud lijst](#az-spring-cloud-list) | Alle Azure lente-Cloud exemplaren in de opgegeven resource groep weer geven, anders de abonnements-Id's weer geven. |
| [AZ lente-Cloud show](#az-spring-cloud-show) | De details van een Azure lente-Cloud weer geven. |

| AZ lente-Cloud-app | Opdrachten voor het beheren van apps in de Azure lente-Cloud.  |
| ---- | ----: |
| [AZ lente-Cloud app Create](#az-spring-cloud-app-create) | Maak een nieuwe app met een standaard implementatie in de Azure lente-Cloud. |
| [AZ lente-Cloud-app verwijderen](#az-spring-cloud-app-delete) | Een app verwijderen in de Azure lente-Cloud. |
| [AZ lente-Cloud-app implementeren](#az-spring-cloud-app-deploy) | Implementeren vanuit een bron code of een vooraf gebouwd binair bestand voor een app en gerelateerde configuraties bijwerken. |
| [AZ lente-lijst met Cloud-apps](#az-spring-cloud-app-list) | Alle apps in de Azure lente-Cloud weer geven. |
| [AZ lente-Cloud app restart](#az-spring-cloud-app-restart) | Start exemplaren van de app opnieuw met behulp van de standaard instellingen voor productie-implementatie. |
| [AZ lente-Cloud app Scale](#az-spring-cloud-app-scale) | Schaal een app of de implementaties hand matig. |
| [AZ lente-Cloud app set-Deployment](#az-spring-cloud-app-set-deployment) | Stel de productie-implementatie van een app in. |
| [AZ lente-Cloud-app weer geven](#az-spring-cloud-app-show) | De details van een app weer geven in de Azure lente-Cloud. |
| [AZ lente-Cloud-app weer geven-implementeren-logboek](#az-spring-cloud-app-show-deploy-log) | Build-logboeken weer geven voor de meest recente implementatie van de bron. Wordt standaard ingesteld op productie-implementatie. |
| [AZ lente-start van Cloud app](#az-spring-cloud-app-start) | Start exemplaren van de app met de standaard instellingen voor productie-implementatie. |
| [AZ lente-Cloud app stop](#az-spring-cloud-app-stop) | Stop exemplaren van de app met de standaard instellingen voor productie-implementatie. |
| [AZ lente-Cloud App Update](#az-spring-cloud-app-update) | De configuratie van de opgegeven app bijwerken. |

| AZ lente-Cloud app binding | Opdrachten voor het beheren van bindingen met Azure Data Services.  De app moet opnieuw worden gestart om deze instellingen van kracht te laten worden. |
| --- | ---: |
| [AZ lente-Cloud app binding List](#az-spring-cloud-app-binding-list) | Alle service bindingen in een app weer geven. |
| [AZ lente-Cloud app binding verwijderen](#az-spring-cloud-app-binding-remove) | Een service binding uit de app verwijderen. |
| [AZ lente-Cloud app binding show](#az-spring-cloud-app-binding-show) | De details van een service binding weer geven. |
| [AZ lente-Cloud app binding Cosmos add](#az-spring-cloud-app-binding-cosmos-add) | Een Azure-CosmosDB binden met de app. |
| [AZ lente-Cloud app binding Cosmos update](#az-spring-cloud-app-binding-cosmos-update) | Een Azure CosmosDB service-binding bijwerken. |
| [AZ lente-Cloud app binding mysql toevoegen](#az-spring-cloud-app-binding-mysql-add) | Een Azure Database for MySQL binden met de app. |
| [AZ lente-Cloud app binding mysql update](#az-spring-cloud-app-binding-mysql-update) | Een Azure Database for MySQL service binding bijwerken. |
| [AZ lente-Cloud app binding redis add](#az-spring-cloud-app-binding-redis-add) | Bind een Azure-cache voor redis met de app. |
| [AZ lente-Cloud app binding redis update](#az-spring-cloud-app-binding-redis-update) | Een Azure-cache bijwerken voor de redis-service binding. |

| AZ lente-implementatie van de Cloud-app | Opdrachten voor het beheren van de implementatie levenscyclus van een app in azure lente Cloud. |
| --- | ---: |
| [AZ lente-implementatie van de Cloud-app maken](#az-spring-cloud-app-deployment-create) | Maak een faserings implementatie voor de app. |
| [AZ lente-Cloud app Deployment verwijderen](#az-spring-cloud-app-deployment-delete) | Een implementatie van de app verwijderen. |
| [AZ lente-implementatie lijst voor Cloud-apps](#az-spring-cloud-app-deployment-list) | Alle implementaties in een app weer geven. |
| [AZ lente-implementatie van de Cloud-app weer geven](#az-spring-cloud-app-deployment-show) | Details van de implementatie weer geven. |

| AZ lente-Cloud configuratie-server | Opdrachten voor het beheren van de Azure lente-Cloud configuratie server. |
| --- | ---: |
| [AZ lente-Cloud configuratie-server wissen](#az-spring-cloud-config-server-clear) | Alle instellingen in de configuratie server wissen. |
| [AZ lente-Cloud configuratie-serverset](#az-spring-cloud-config-server-set) | Definieer de configuratie server vanuit een YAML-bestand. |
| [AZ lente-Cloud configuratie-server show](#az-spring-cloud-config-server-show) | Configuratie server configureren. |
| [AZ lente-set Git-Cloud config server](#az-spring-cloud-config-server-git-set) | Bepaal de Git-eigenschappen voor de configuratie server.  De vorige waarden worden overschreven. |
| [AZ lente-Cloud config server Git opslag plaats add](#az-spring-cloud-config-server-git-repo-add) | Voeg een nieuwe Git-opslagplaats configuratie toe aan de configuratie server. |
| [AZ lente-Cloud config server Git opslag plaats List](#az-spring-cloud-config-server-git-repo-list) | Alle configuraties van Git-opslag plaatsen weer geven voor de configuratie server. |
| [AZ lente-Cloud config server Git opslag plaats Remove](#az-spring-cloud-config-server-git-repo-remove) | Verwijder de opgegeven Git-opslag plaats van de configuratie server. |

| AZ lente-Cloud test-Endpoint | Opdrachten voor het beheren van eindpunt tests in azure lente-Cloud |
| --- | ---: |
| [AZ lente-Cloud test-endpoint Disable](#az-spring-cloud-test-endpoint-disable) | Test eindpunt uitschakelen. |
| [AZ lente-Cloud test-endpoint Enable](#az-spring-cloud-test-endpoint-enable) | Test eindpunt inschakelen. |
| [AZ lente-Cloud test-endpoint List](#az-spring-cloud-test-endpoint-list) | Test eindpunt sleutels weer geven. |
| [AZ lente-Cloud test-endpoint renew-Key](#az-spring-cloud-test-endpoint-renew-key) | Genereer een test-eindpunt sleutel opnieuw. |

## <a name="az-spring-cloud-create"></a>AZ lente-Cloud Create

Maak een nieuwe app met een standaard implementatie in de Azure lente-Cloud.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --naam-n | Naam voor dit Azure veer Cloud-exemplaar. |
| --Resource-Group-g | Hiermee geeft u de resource groep voor deze app op.  De standaard groep configureren met`az configure --defaults group=<name>` |

| Optionele parameters | |
| --- | ---: |
| --locatie-l | Hiermee geeft u de server locatie op voor deze app.  Geldige locaties zoeken met`az account list-locations` |
| --geen wacht tijd | Zorg dat er geen langlopende bewerkingen worden uitgevoerd.

### <a name="examples"></a>Voorbeelden

Een nieuwe Azure lente-Cloud maken in Westus

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>AZ lente-Cloud verwijderen

Een Azure lente Cloud-exemplaar verwijderen.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --naam-n | De naam van het Azure veer Cloud-exemplaar dat moet worden verwijderd. |
| --Resource-Group-g | De naam van de resource groep waartoe de Azure lente-Cloud behoort. |

| Optionele parameters | |
| --- | ---: |
| -geen wacht tijd | Wacht niet totdat langlopende bewerkingen zijn voltooid. |

### <a name="example"></a>Voorbeeld

Verwijder een Azure lente-Cloud instantie met de naam ' MyService ' in ' MyResourceGroup '.

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>AZ lente-Cloud lijst

Alle Azure lente-Cloud exemplaren weer geven die zijn gekoppeld aan de opgegeven resource groep. Als er geen resource groep is opgegeven, vermeldt u de abonnement-Id's.

```azurecli
az spring-cloud list --resource-group -g
```

| Vereiste parameters | |
| --- | ---: |
| --Resource-Group-g | De naam van de resourcegroep. |

## <a name="az-spring-cloud-show"></a>AZ lente-Cloud show

De details weer geven voor het opgegeven Azure veer Cloud-exemplaar.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Vereiste parameters | |
| --- | ---: |
| --naam-n | De naam van het Azure veer Cloud-exemplaar. |
| --Resource-Group-g | De naam van de resource groep waartoe het Azure veer Cloud-exemplaar behoort.

## <a name="az-spring-cloud-app-create"></a>AZ lente-Cloud app Create

Een nieuwe app maken in een Azure lente-Cloud.

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
| --naam-n | Naam van de app. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --CPU | Aantal virtuele kernen per exemplaar.  Standaard: 1. |
| --Enable-persistent-storage | Booleaanse waarde.  Indien waar, wordt een 50 GB-schijf gekoppeld aan het standaardpad. |
| --exemplaar-aantal | Aantal exemplaren.  Standaard: 1. |
| --is-openbaar | Booleaanse waarde.  Indien waar, wordt een openbaar domein toegewezen. |
| --geheugen | Aantal GB aan geheugen per instantie.  Standaard: 1. |

### <a name="examples"></a>Voorbeelden

Maak een app met de standaard configuratie.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Maak een openbaar toegankelijke app met drie exemplaren.  Elk exemplaar heeft 3 GB geheugen en twee CPU-kernen.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>AZ lente-Cloud-app verwijderen

Hiermee verwijdert u een app in de Azure lente-Cloud.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --naam-n | Naam van de app. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deploy"></a>AZ lente-Cloud-app implementeren

Implementeer een app in de Azure lente-Cloud vanuit de bron code of een vooraf gemaakte binaire en update-gerelateerde configuraties.

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
| --naam-n | Naam van de app. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --CPU | Aantal virtuele CPI-kernen per exemplaar. |
| --implementatie-d | Naam van een bestaande app-implementatie.  De standaard instelling is de productie-implementatie als deze niet is opgegeven. |
| --env | Met ruimte gescheiden omgevings variabelen in de indeling ' sleutel [= waarde] '. |
| --exemplaar-aantal | Aantal exemplaren. |
| --jar-pad | Implementeer, indien opgegeven, jar vanaf gegeven pad. Als dat niet het geval is, implementeert u de huidige map als een tar. |
| --JVM-opties | Een teken reeks met JVM-opties.  Gebruik ' = ' in plaats van ' ' om fouten bij het parseren van shells te voor komen. Bijvoorbeeld, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --geheugen | Aantal GB aan geheugen per instantie. |
| --geen wacht tijd | Wacht niet totdat langlopende bewerkingen zijn voltooid. |
| --runtime-versie | Runtime versie van de taal die in de app wordt gebruikt.  Toegestane waarden: `Java_11`, `Java_8`. |
| --doel module | De onderliggende module die moet worden geïmplementeerd.  Vereist wanneer meerdere jar-pakketten zijn gebouwd op basis van de bron code. |
| --versie | Implementatie versie.  Ongewijzigd als dit niet is ingesteld. |

### <a name="examples"></a>Voorbeelden

Bron code implementeren in een app. Hiermee wordt de huidige map ingepakt, wordt een binair element gemaakt met de service Pivotal build en vervolgens geïmplementeerd in de app.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Implementeer een vooraf gebouwde jar voor een app met behulp van JVM-opties en omgevings variabelen.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

De bron code implementeren naar een specifieke implementatie van een app.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>AZ lente-lijst met Cloud-apps

Alle apps weer geven in het Azure lente Cloud-exemplaar.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Vereiste parameters | |
| --- | ---: |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-restart"></a>AZ lente-Cloud app restart

Exemplaren van de app opnieuw starten.  Wordt standaard ingesteld op de productie-implementatie.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --naam-n | Naam van de app. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --implementatie-d | De naam van de bestaande implementatie van de app.  De standaard instelling is de productie-implementatie als deze niet is opgegeven. |
| --geen wacht tijd | Wacht niet totdat langlopende bewerkingen zijn voltooid. |

## <a name="az-spring-cloud-app-scale"></a>AZ lente-Cloud app Scale

Schaal een app of de implementaties hand matig.

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
| --naam-n | Naam van de app. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --CPU | Aantal virtuele CPU-kernen per app-exemplaar. |
| --implementatie-d | De naam van de bestaande implementatie van de app.  De standaard instelling is de productie-implementatie als deze niet is opgegeven. |
| --exemplaar-aantal | Aantal exemplaren van deze app. |
| --geheugen | Aantal GB aan geheugen per app-exemplaar. |
| --geen wacht tijd | Wacht niet totdat langlopende bewerkingen zijn voltooid. |

### <a name="examples"></a>Voorbeelden

Omhoog schalen van een app naar 4 CPU-kernen en 8 GB geheugen per instantie.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Uitschalen van een implementatie van de app naar 5 exemplaren.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>AZ lente-Cloud app set-Deployment

Stel de configuratie opties in voor de productie-implementatie van de app.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --implementatie-d | De naam van een bestaande implementatie van de app. |
| --naam-n | Naam van de app. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --geen wacht tijd | Wacht niet totdat langlopende bewerkingen zijn voltooid. |

### <a name="examples"></a>Voorbeelden

Een staging-implementatie van de app naar de productie swap.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>AZ lente-Cloud-app weer geven

De details van een app weer geven in de Azure lente-Cloud.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --naam-n | Naam van de app. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>AZ lente-Cloud-app weer geven-implementeren-logboek

Het build-logboek van de laatste implementatie van de bron code weer geven.  Wordt standaard ingesteld op productie omgeving.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Vereiste parameters | |
| --- | ---: |
| --naam-n | Naam van de app. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --implementatie-d | De naam van een bestaande implementatie van de app.  Wordt standaard ingesteld op de productie omgeving. |

## <a name="az-spring-cloud-app-start"></a>AZ lente-start van Cloud app

Start exemplaren van de app.  Wordt standaard ingesteld op productie omgeving.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --naam-n | Naam van de app. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --implementatie-d | De naam van een bestaande implementatie van de app.  Wordt standaard ingesteld op de productie omgeving. |
| --geen wacht tijd | Wacht niet totdat langlopende bewerkingen zijn voltooid. |

## <a name="az-spring-cloud-app-stop"></a>AZ lente-Cloud app stop

Stop de exemplaren van de app.  Wordt standaard ingesteld op de productie omgeving.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --naam-n | Naam van de app. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --implementatie-d | De naam van een bestaande implementatie van de app.  Wordt standaard ingesteld op de productie omgeving. |
| --geen wacht tijd | Wacht niet totdat langlopende bewerkingen zijn voltooid. |

## <a name="az-spring-cloud-app-update"></a>AZ lente-Cloud App Update

De opgeslagen configuratie van een app bijwerken.

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
| --naam-n | Naam van de app. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --implementatie-d | De naam van een bestaande implementatie van de app.  Wordt standaard ingesteld op de productie omgeving. |
| --Enable-persistent-storage | True.  Als dit het geval is, koppelt u een 50 GB-schijf aan het standaardpad. |
| --env | Met ruimte gescheiden omgevings variabelen in de indeling ' sleutel [= waarde] '. |
| --is-openbaar | True.  Indien waar, wordt een openbaar domein toegewezen aan de app. |
| --JVM-opties | Een teken reeks met JVM-opties.  Gebruik ' = ' in plaats van ' ' om fouten bij het parseren van shells te voor komen. Bijvoorbeeld, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --geen wacht tijd | Wacht niet totdat langlopende bewerkingen zijn voltooid. |
| --runtime-versie | Runtime versie van de taal die in de app wordt gebruikt.  Toegestane waarden: `Java_11`, `Java_8`. |

### <a name="example"></a>Voorbeeld

Een omgevings variabele voor de app toevoegen.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>AZ lente-Cloud app binding List

Alle service bindingen in een app weer geven.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-remove"></a>AZ lente-Cloud app binding verwijderen

Een service binding uit de app verwijderen.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | De naam van de service binding die moet worden verwijderd. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-show"></a>AZ lente-Cloud app binding show

De details van een service binding weer geven.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | De naam van de service binding. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>AZ lente-Cloud app binding Cosmos add

Een Azure Cosmos DB binden met de app.

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
| --API-type | Geef het API-type op met een van de volgende waarden: Cassandra, Gremlin, Mongo, SQL, Table. |
| --app | Naam van de app. |
| --naam | De naam van de service binding. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

|Optionele parameters | |
| --- | ---: |
| --verzameling-naam | De naam van de verzameling.  Vereist wanneer Gremlin wordt gebruikt. |
| --database naam | De naam van de data base.  Vereist wanneer Mongo, SQL en Gremlin worden gebruikt. |
| --sleutel ruimte | Cassandra-sleutel ruimte.  Vereist wanneer Cassandra wordt gebruikt. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>AZ lente-Cloud app binding Cosmos update

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
| --naam | De naam van de service binding. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

|Optionele parameters | |
| --- | ---: |
| --verzameling-naam | De naam van de verzameling.  Vereist wanneer Gremlin wordt gebruikt. |
| --database naam | De naam van de data base.  Vereist wanneer Mongo, SQL en Gremlin worden gebruikt. |
| --sleutel ruimte | Cassandra-sleutel ruimte.  Vereist wanneer Cassandra wordt gebruikt. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>AZ lente-Cloud app binding mysql toevoegen

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
| --database naam | De naam van de data base. |
| --sleutel | API-sleutel van de service. |
| --naam | De naam van de service binding. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --resource-id | De Azure-Resource-ID van de service waarmee moet worden verbonden. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |
| --gebruikersnaam | Gebruikers naam voor toegang tot de data base. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>AZ lente-Cloud app binding mysql update

Werk de service bindings verbinding met de app bij naar een Azure Database for MySQL.

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
| --naam | De naam van de service binding. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --database naam | De naam van de data base. |
| --sleutel | API-sleutel van de service. |
| --gebruikersnaam | Gebruikers naam voor toegang tot de data base. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>AZ lente-Cloud app binding redis add

Bind een Azure-cache voor redis met de app.

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
| --naam | De naam van de service binding. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --resource-id | De Azure-Resource-ID van de service waarmee u een binding wilt maken. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --uitschakelen-SSL | Schakel TLS uit. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>AZ lente-Cloud app binding redis update

Een service binding voor Azure cache bijwerken voor redis.

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | De naam van de service binding. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --uitschakelen-SSL | Schakel TLS uit. |

## <a name="az-spring-cloud-app-deployment-create"></a>AZ lente-implementatie van de Cloud-app maken

Maak een faserings implementatie voor de app.

Voor het implementeren van code of het bijwerken van instellingen voor een bestaande `az spring-cloud app deploy --deployment <staging-deployment>` implementatie, gebruik of ' AZ lente-Cloud App Update <staging deployment>--Deployment.

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | De naam van de service binding. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --CPU | Aantal virtuele CPU-kernen per exemplaar.  Standaardinstelling: 1 |
| --env | Met ruimte gescheiden omgevings variabelen in de indeling ' sleutel [= waarde] '. |
| --exemplaar-aantal | Aantal exemplaren. Standaard: 1. |
| --jar-pad | Implementeer jar als dit wordt gegeven.  Als dat niet het geval is, implementeert u de huidige map als een tar. |
| --JVM-opties | Een teken reeks met JVM-opties.  Gebruik ' = ' in plaats van ' ' om fouten bij het parseren van shells te voor komen. Bijvoorbeeld, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --geheugen | Aantal GB aan geheugen per instantie. |
| --geen wacht tijd | Wacht niet totdat langlopende bewerkingen zijn voltooid. |
| --runtime-versie | Runtime versie van de taal die in de app wordt gebruikt.  Toegestane waarden: `Java_11`, `Java_8`. |
| --overs Laan-klonen-instellingen | Maak een staging-implementatie door de huidige instellingen voor productie-implementatie te klonen. |
| --doel module | De onderliggende module die moet worden geïmplementeerd.  Vereist wanneer meerdere jar-pakketten zijn gebouwd op basis van de bron code. |
| --versie | Implementatie versie.  Ongewijzigd als dit niet is ingesteld. |

### <a name="examples"></a>Voorbeelden

Implementeer de bron code naar een nieuwe implementatie van de app.  Hiermee wordt de huidige map ingepakt, wordt een binair element gemaakt met behulp van het PivotTable build-systeem en vervolgens geïmplementeerd.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Implementeer een vooraf gebouwde jar voor een app met JVM-opties en omgevings variabelen.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>AZ lente-Cloud app Deployment verwijderen

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
| --naam | De naam van de implementatie. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-list"></a>AZ lente-implementatie lijst voor Cloud-apps

Alle implementaties in een app weer geven.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-show"></a>AZ lente-implementatie van de Cloud-app weer geven

Details van een implementatie weer geven.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Vereiste parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --naam | De naam van de implementatie. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --Service-s | De naam van de Azure lente-Cloud.  U kunt de standaard service configureren met `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-config-server-clear"></a>AZ lente-Cloud configuratie-server wissen

Wis alle configuratie-instellingen in de configuratie server.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Vereiste parameters | |
| --- | ---: |
| --naam | De naam van de Azure lente-Cloud. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-set"></a>AZ lente-Cloud configuratie-serverset

Stel configuratie-instellingen op de configuratie server in met behulp van een YAML-bestand.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Vereiste parameters | |
| --- | ---: |
| --config-bestand | Bestandspad naar een YAML-manifest voor de configuratie van de configuratie server. |
| --naam | De naam van de Azure lente-Cloud. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --geen wacht tijd | Zorg dat er geen langlopende bewerkingen worden uitgevoerd.

## <a name="az-spring-cloud-config-server-show"></a>AZ lente-Cloud configuratie-server show

De instellingen van de configuratie server weer geven.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Vereiste parameters | |
| --- | ---: |
| --naam | De naam van de Azure lente-Cloud. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-git-set"></a>AZ lente-Cloud configuratie-server git set

Stel de Git-eigenschappen in voor de configuratie server.  Hiermee worden alle bestaande Git-eigenschappen overschreven.

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
| --naam | De naam van de Azure lente-Cloud. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --URI | De URI van de toegevoegde configuratie. |

| Optionele parameters | |
| --- | ---: |
| --defer | Sla het object tijdelijk op in de lokale cache in plaats van naar Azure te verzenden.  Gebruiken `az cache` om weer te geven/wissen. |
| --host-sleutel | Host-sleutel voor de toegevoegde configuratie. |
| --host-sleutel-algoritme | Het algoritme van de host-sleutel voor de toegevoegde configuratie. |
| --Label | Label van de toegevoegde configuratie. |
| --password | Wacht woord van de toegevoegde configuratie. |
| --persoonlijke sleutel | De persoonlijke sleutel van de toegevoegde configuratie. |
| --zoeken-paden | Zoek naar paden van de toegevoegde configuratie.  Gebruik scheidings tekens voor meerdere paden. |
| --strikte host-sleutel controle | Hiermee wordt strikte controle van de host-sleutel van de toegevoegde configuratie ingeschakeld. |
| --gebruikersnaam | Gebruikers naam van de toegevoegde configuratie. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>AZ lente-Cloud configuratie-Server Git opslag plaats toevoegen

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
| --naam | De naam van de Azure lente-Cloud. |
| --opslag plaats-naam | De URI van de opslag plaats. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --URI | De URI van de toegevoegde configuratie. |

| Optionele parameters | |
| --- | ---: |
| --defer | Sla het object tijdelijk op in de lokale cache in plaats van naar Azure te verzenden.  Gebruiken `az cache` om weer te geven/wissen. |
| --host-sleutel | Host-sleutel voor de toegevoegde configuratie. |
| --host-sleutel-algoritme | Het algoritme van de host-sleutel voor de toegevoegde configuratie. |
| --Label | Label van de toegevoegde configuratie. |
| --password | Wacht woord van de toegevoegde configuratie. |
| --patroon | Patroon voor de opslag plaats.  Gebruik scheidings tekens voor meerdere paden.|
| --persoonlijke sleutel | De persoonlijke sleutel van de toegevoegde configuratie. |
| --zoeken-paden | Zoek naar paden van de toegevoegde configuratie.  Gebruik scheidings tekens voor meerdere paden. |
| --strikte host-sleutel controle | Hiermee wordt strikte controle van de host-sleutel van de toegevoegde configuratie ingeschakeld. |
| --gebruikersnaam | Gebruikers naam van de toegevoegde configuratie. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>AZ lente-Cloud configuratie-Server Git opslag plaats-lijst

Alle Git-opslag plaatsen weer geven die zijn gedefinieerd op de configuratie server

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Vereiste parameters | |
| --- | ---: |
| --naam | De naam van de Azure lente-Cloud. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --defer | Sla het object tijdelijk op in de lokale cache in plaats van naar Azure te verzenden.  Gebruiken `az cache` om weer te geven/wissen. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>AZ lente-Cloud configuratie-Server Git opslag plaats Remove

Een bestaande Git opslag plaats-configuratie verwijderen van de configuratie server.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Vereiste parameters | |
| --- | ---: |
| --naam | De naam van de Azure lente-Cloud. |
| --opslag plaats-naam | De URI van de opslag plaats. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --defer | Sla het object tijdelijk op in de lokale cache in plaats van naar Azure te verzenden.  Gebruiken `az cache` om weer te geven/wissen. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>AZ lente-Cloud test-endpoint Disable

Test eindpunt van de Azure lente-Cloud uitschakelen

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Vereiste parameters | |
| --- | ---: |
| --naam | De naam van de Azure lente-Cloud. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>AZ lente-Cloud test-endpoint Enable

Schakel het test eindpunt in voor de Azure lente-Cloud. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Vereiste parameters | |
| --- | ---: |
| --naam | De naam van de Azure lente-Cloud. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-list"></a>AZ lente-Cloud test-endpoint List 

Geef een lijst weer met de beschik bare test eindpunt sleutels voor de Azure lente-Cloud.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Vereiste parameters | |
| --- | ---: |
| --naam | De naam van de Azure lente-Cloud. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |

| Optionele parameters | |
| --- | ---: |
| --app | Naam van de app. |
| --implementatie-d | De naam van een bestaande implementatie van de app.  De standaard instelling is productie als niet opgegeven. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>AZ lente-Cloud test-endpoint renew-Key

Genereer een test-endpoint-sleutel voor de Azure lente-Cloud opnieuw.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Vereiste parameters | |
| --- | ---: |
| --naam | De naam van de Azure lente-Cloud. |
| --Resource-Group-g | De naam van de resourcegroep.  U kunt de standaard groep configureren met `az configure --defaults group=<name>`. |
| --type | Type test eindpunt sleutel.  Toegestane waarden: primair, secundair. |
