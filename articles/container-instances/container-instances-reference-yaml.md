---
title: Naslag informatie over Azure Container Instances YAML
description: Verwijzing voor het YAML-bestand dat wordt ondersteund door Azure Container Instances voor het configureren van een container groep
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/12/2019
ms.author: danlep
ms.openlocfilehash: 2e6be18371cf3ff96d1ce91d4dde26ff1f14021b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179902"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-verwijzing: Azure Container Instances

In dit artikel worden de syntaxis en eigenschappen beschreven voor het YAML-bestand dat wordt ondersteund door Azure Container Instances om een [container groep](container-instances-container-groups.md)te configureren. Gebruik een YAML-bestand om de groeps configuratie in te voeren op de opdracht [AZ container Create][az-container-create] in de Azure cli. 

Een YAML-bestand is een handige manier om een container groep te configureren voor reproduceer bare implementaties. Het is een beknopt alternatief voor het gebruik van een [Resource Manager-sjabloon](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) of de Azure container instances sdk's om een container groep te maken of bij te werken.

> [!NOTE]
> Deze verwijzing is van toepassing op YAML-bestanden voor `2018-10-01`Azure container instances rest API-versie.

## <a name="schema"></a>Schema 

Het schema voor het YAML-bestand volgt, inclusief opmerkingen om de sleutel eigenschappen te markeren. Zie de sectie [eigenschaps waarden](#property-values) voor een beschrijving van de eigenschappen in dit schema.

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # Exposed ports on the instance
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>Waarden van eigenschappen

De volgende tabellen bevatten een beschrijving van de waarden die u moet instellen in het schema.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Micro soft. ContainerInstance/containerGroups-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Ja | De naam van de container groep. |
|  apiVersion | enum | Ja | 10-01-2018 |
|  location | string | Nee | De resource locatie. |
|  tags | object | Nee | De resource Tags. |
|  identity | object | Nee | De identiteit van de container groep, indien geconfigureerd. - [ContainerGroupIdentity-object](#ContainerGroupIdentity) |
|  properties | object | Ja | [ContainerGroupProperties-object](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>ContainerGroupIdentity-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  type | enum | Nee | Het type identiteit dat voor de container groep wordt gebruikt. Het type ' SystemAssigned, UserAssigned ' bevat zowel een impliciet gemaakte identiteit als een set door de gebruiker toegewezen identiteiten. Met het type geen worden identiteiten uit de container groep verwijderd. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, geen |
|  userAssignedIdentities | object | Nee | De lijst met gebruikers-id's die zijn gekoppeld aan de container groep. De sleutel verwijzingen van de gebruikers-id-woorden lijst worden Azure Resource Manager bron-Id's in de vorm: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName }'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>ContainerGroupProperties-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  containers | array | Ja | De containers in de container groep. - [Container object](#Container) |
|  imageRegistryCredentials | array | Nee | De register referenties voor de installatie kopie waarmee de container groep wordt gemaakt. - [ImageRegistryCredential-object](#ImageRegistryCredential) |
|  restartPolicy | enum | Nee | Start het beleid voor alle containers binnen de container groep opnieuw. - `Always`Altijd opnieuw opstarten `OnFailure` opnieuw opstarten bij fout `Never` -nooit opnieuw opstarten. -Always, OnFailure, Never |
|  IPAdres | object | Nee | Het IP-adres type van de container groep. - [IpAddress-object](#IpAddress) |
|  besturingssysteemtype | enum | Ja | Het type besturings systeem dat vereist is voor de containers in de container groep. -Windows of Linux |
|  volumes | array | Nee | De lijst met volumes die kunnen worden gekoppeld door containers in deze container groep. - [Volume object](#Volume) |
|  diagnostische gegevens | object | Nee | De diagnostische gegevens voor een container groep. - [ContainerGroupDiagnostics-object](#ContainerGroupDiagnostics) |
|  networkProfile | object | Nee | De netwerk profiel gegevens voor een container groep. - [ContainerGroupNetworkProfile-object](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | Nee | De DNS-configuratie-informatie voor een container groep. - [DnsConfiguration-object](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Container object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Ja | De door de gebruiker gegeven naam van het container exemplaar. |
|  properties | object | Ja | De eigenschappen van het container exemplaar. - [ContainerProperties-object](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>ImageRegistryCredential-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  server | string | Ja | De register server van de docker-installatie kopie zonder een protocol zoals http en HTTPS. |
|  username | string | Ja | De gebruikers naam voor het persoonlijke REGI ster. |
|  password | string | Nee | Het wacht woord voor het persoonlijke REGI ster. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  ports | array | Ja | De lijst met poorten die worden weer gegeven in de container groep. - [Poort object](#Port) |
|  type | enum | Ja | Hiermee geeft u op of het IP-adres beschikbaar is voor het open bare Internet of het persoonlijke VNET. -Openbaar of privé |
|  ip | string | Nee | Het IP-adres dat toegankelijk is voor het open bare Internet. |
|  dnsNameLabel | string | Nee | Het DNS-naam label voor het IP-adres. |


<a id="Volume" />

### <a name="volume-object"></a>Volume object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Ja | De naam van het volume. |
|  AzureFile | object | Nee | Het Azure-bestands volume. - [AzureFileVolume-object](#AzureFileVolume) |
|  emptyDir | object | Nee | Het lege directory volume. |
|  secret | object | Nee | Het geheime volume. |
|  gitRepo | object | Nee | Het git opslag plaats-volume. - [GitRepoVolume-object](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Nee | Informatie over logboek analyse van container groep. - [LogAnalytics-object](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  id | string | Ja | De id voor een netwerk profiel. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsConfiguration-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  Naam servers | array | Ja | De DNS-servers voor de container groep. -teken reeks |
|  searchDomains | string | Nee | De DNS-Zoek domeinen voor het opzoeken van hostnamen in de container groep. |
|  opties | string | Nee | De DNS-opties voor de container groep. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>ContainerProperties-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  image | string | Ja | De naam van de installatie kopie die wordt gebruikt om het container exemplaar te maken. |
|  Opdracht | array | Nee | De opdrachten die moeten worden uitgevoerd binnen het container exemplaar in het formulier exec. -teken reeks |
|  ports | array | Nee | De weer gegeven poorten op het container exemplaar. - [ContainerPort-object](#ContainerPort) |
|  Omgevings variabelen | array | Nee | De omgevings variabelen die in het container exemplaar moeten worden ingesteld. - [EnvironmentVariable-object](#EnvironmentVariable) |
|  bronnen | object | Ja | De resource vereisten van het container exemplaar. - [ResourceRequirements-object](#ResourceRequirements) |
|  volumeMounts | array | Nee | Het volume koppelt beschikbaar voor het container exemplaar. - [VolumeMount-object](#VolumeMount) |
|  livenessProbe | object | Nee | De duur van de liveiteit. - [ContainerProbe-object](#ContainerProbe) |
|  readinessProbe | object | Nee | De gereedheids test. - [ContainerProbe-object](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Poort object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | Nee | Het protocol dat is gekoppeld aan de poort. -TCP of UDP |
|  port | integer | Ja | Het poort nummer. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  shareName | string | Ja | De naam van de Azure-bestands share die moet worden gekoppeld als een volume. |
|  Kenmerk | boolean | Nee | De vlag waarmee wordt aangegeven of het Azure-bestand dat als een volume is gedeeld, alleen-lezen is. |
|  storageAccountName | string | Ja | De naam van het opslag account dat de Azure-bestands share bevat. |
|  storageAccountKey | string | Nee | De toegangs sleutel voor het opslag account die wordt gebruikt om toegang te krijgen tot de Azure-bestands share. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  map | string | Nee | Naam van de doel directory. Mag niet bevatten of beginnen met...  Als '. ' is opgegeven, wordt de map van het volume de Git-opslag plaats.  Als dat niet het geval is, bevat het volume de Git-opslag plaats in de submap met de opgegeven naam. |
|  repository | string | Ja | URL van opslag plaats |
|  feedback | string | Nee | Hash voor de opgegeven revisie door voeren. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>LogAnalytics-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | Ja | De werk ruimte-id voor log Analytics |
|  workspaceKey | string | Ja | De werkruimte sleutel voor log Analytics |
|  logType | enum | Nee | Het logboek type dat moet worden gebruikt. -ContainerInsights of ContainerInstanceLogs |
|  metagegevens | object | Nee | Meta gegevens voor log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>ContainerPort-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | Nee | Het protocol dat is gekoppeld aan de poort. -TCP of UDP |
|  port | integer | Ja | Het poort nummer dat wordt weer gegeven in de container groep. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>EnvironmentVariable-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Ja | De naam van de omgevings variabele. |
|  value | string | Nee | De waarde van de omgevings variabele. |
|  Securevalue op | string | Nee | De waarde van de variabele beveiligde omgeving. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>ResourceRequirements-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  aanvragen | object | Ja | De resource aanvragen van dit container exemplaar. - [ResourceRequests-object](#ResourceRequests) |
|  Limieten | object | Nee | De resource limieten van dit container exemplaar. - [ResourceLimits-object](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>VolumeMount-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | Ja | De naam van het volume koppeling. |
|  mountPath | string | Ja | Het pad binnen de container waarin het volume moet worden gekoppeld. Mag geen dubbele punt (:)) bevatten. |
|  Kenmerk | boolean | Nee | De vlag waarmee wordt aangegeven of de koppeling van het volume alleen-lezen is. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>ContainerProbe-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  exec | object | Nee | De opdracht uitvoeren om te testen- [ContainerExec-object](#ContainerExec) |
|  httpGet | object | Nee | De HTTP Get-instellingen voor het probe- [ContainerHttpGet-object](#ContainerHttpGet) |
|  initialDelaySeconds | integer | Nee | De eerste vertraging in seconden. |
|  periodSeconds | integer | Nee | De periode seconden. |
|  failureThreshold | integer | Nee | De drempel waarde voor fouten. |
|  successThreshold | integer | Nee | De drempel waarde voor geslaagde pogingen. |
|  timeoutSeconds | integer | Nee | De time-outwaarde seconden. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>ResourceRequests-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | nummer | Ja | De geheugen aanvraag in GB van dit container exemplaar. |
|  cpu | nummer | Ja | De CPU-aanvraag van dit container exemplaar. |
|  gpu | object | Nee | De GPU-aanvraag van dit container exemplaar. - [GpuResource-object](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>ResourceLimits-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | nummer | Nee | De geheugen limiet in GB van dit container exemplaar. |
|  cpu | nummer | Nee | De CPU-limiet van dit container exemplaar. |
|  gpu | object | Nee | De GPU-limiet van dit container exemplaar. - [GpuResource-object](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>ContainerExec-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  Opdracht | array | Nee | De opdrachten die moeten worden uitgevoerd in de container. -teken reeks |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>ContainerHttpGet-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  path | string | Nee | Het pad dat moet worden getest. |
|  port | integer | Ja | Het poort nummer dat moet worden getest. |
|  schema | enum | Nee | Het schema. -http of https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource-object

|  Name | type | Vereist | Value |
|  ---- | ---- | ---- | ---- |
|  count | integer | Ja | Het aantal GPU-resources. |
|  sku | enum | Ja | De SKU van de GPU-resource. -K80, P100, V100 |


## <a name="next-steps"></a>Volgende stappen

Zie de zelf studie [een groep met meerdere containers implementeren met behulp van een yaml-bestand](container-instances-multi-container-yaml.md).

Zie voor beelden van het gebruik van een YAML-bestand voor het implementeren van container groepen in een [virtueel netwerk](container-instances-vnet.md) of het [koppelen van een extern volume](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

