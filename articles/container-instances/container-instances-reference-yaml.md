---
title: YAML-referentie voor containergroep
description: Referentie voor het YAML-bestand dat wordt ondersteund door Azure Container Instances om een containergroep te configureren
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896573"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-verwijzing: Azure Container Instances

In dit artikel worden de syntaxis en eigenschappen voor het YAML-bestand dat wordt ondersteund door Azure Container Instances om een [containergroep](container-instances-container-groups.md)te configureren. Gebruik een YAML-bestand om de groepsconfiguratie in te voeren op de opdracht [az-containermaken][az-container-create] in de Azure CLI. 

Een YAML-bestand is een handige manier om een containergroep te configureren voor reproduceerbare implementaties. Het is een beknopt alternatief voor het gebruik van een [Resource Manager-sjabloon](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) of de Azure Container Instances-SDK's om een containergroep te maken of bij te werken.

> [!NOTE]
> Deze verwijzing is van toepassing op YAML-bestanden voor azure container instances rest api-versie `2018-10-01`.

## <a name="schema"></a>Schema 

Het schema voor het YAML-bestand volgt, inclusief opmerkingen om belangrijke eigenschappen te markeren. Zie de sectie [Eigenschapwaarden](#property-values) voor een beschrijving van de eigenschappen in dit schema.

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
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
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

## <a name="property-values"></a>Eigenschapswaarden

In de volgende tabellen worden de waarden beschreven die u in het schema moet instellen.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Object Microsoft.ContainerInstance/containerGroups

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  name | tekenreeks | Ja | De naam van de containergroep. |
|  apiVersion | Enum | Ja | 2018-10-01 |
|  location | tekenreeks | Nee | De locatie van de bron. |
|  tags | object | Nee | De resource tags. |
|  identity | object | Nee | De identiteit van de containergroep, indien geconfigureerd. - [Object ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | object | Ja | [Object ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Object ContainerGroupIdentity

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  type | Enum | Nee | Het type identiteit dat wordt gebruikt voor de containergroep. Het type 'SystemAssigned, UserAssigned' bevat zowel een impliciet gemaakte identiteit als een set door gebruikers toegewezen identiteiten. Het type 'Geen' verwijdert alle identiteiten uit de containergroep. - SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | object | Nee | De lijst met gebruikersidentiteiten die zijn gekoppeld aan de containergroep. De belangrijkste referenties van het gebruikersidentiteitswoordenboek zijn Azure Resource Manager-bron-id's in de vorm: '/abonnementen/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Object ContainerGroupProperties

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  containers | matrix | Ja | De containers binnen de containergroep. - [Containerobject](#Container) |
|  imageRegistryCredentials | matrix | Nee | De referenties van het afbeeldingsregister waarmee de containergroep wordt gemaakt. - [ImageRegistryCredential-object](#ImageRegistryCredential) |
|  opnieuw opstartenBeleid | Enum | Nee | Start het beleid opnieuw op voor alle containers binnen de containergroep. - `Always`Altijd opnieuw `OnFailure` opstarten- `Never` Opnieuw opstarten op mislukking- Nooit opnieuw opstarten. - Altijd, OnFailure, Nooit |
|  ipAddress (ipAddress) | object | Nee | Het IP-adrestype van de containergroep. - [IpAddress-object](#IpAddress) |
|  osType | Enum | Ja | Het type besturingssysteem dat vereist is door de containers in de containergroep. - Windows of Linux |
|  volumes | matrix | Nee | De lijst met volumes die kunnen worden gemonteerd door containers in deze containergroep. - [Volumeobject](#Volume) |
|  diagnostische gegevens | object | Nee | De diagnostische informatie voor een containergroep. - [ContainerGroupDiagnostics, object](#ContainerGroupDiagnostics) |
|  netwerkProfiel | object | Nee | De netwerkprofielinformatie voor een containergroep. - [Object ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | Nee | De DNS-config-informatie voor een containergroep. - [DnsConfiguration object](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Containerobject

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  name | tekenreeks | Ja | De door de gebruiker opgegeven naam van de containerinstantie. |
|  properties | object | Ja | De eigenschappen van de containerinstantie. - [Object ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>ImageRegistryCredential-object

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  server | tekenreeks | Ja | De Docker image registry server zonder protocol zoals "http" en "https". |
|  gebruikersnaam | tekenreeks | Ja | De gebruikersnaam voor het privéregister. |
|  wachtwoord | tekenreeks | Nee | Het wachtwoord voor het privéregister. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress-object

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  ports | matrix | Ja | De lijst met poorten die op de containergroep worden weergegeven. - [Poortobject](#Port) |
|  type | Enum | Ja | Hiermee geeft u op of het IP wordt blootgesteld aan het openbare internet of het particuliere VNET. - Publiek of particulier |
|  IP | tekenreeks | Nee | Het IP blootgesteld aan het openbare internet. |
|  dnsNameLabel | tekenreeks | Nee | Het dns-naamlabel voor het IP-adres. |


<a id="Volume" />

### <a name="volume-object"></a>Volumeobject

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  name | tekenreeks | Ja | De naam van het volume. |
|  azureFile | object | Nee | Het azure-bestandsvolume. - [AzureFileVolume-object](#AzureFileVolume) |
|  emptyDir | object | Nee | Het lege mapvolume. |
|  geheim | object | Nee | Het geheime volume. |
|  gitRepo | object | Nee | Het git repo volume. - [GitRepoVolume object](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics, object

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Nee | Informatie over het analysevan containergroepenlogboeken. - [LogAnalytics-object](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Object ContainerGroupNetworkProfile

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  id | tekenreeks | Ja | De id voor een netwerkprofiel. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsConfiguration object

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  Nameservers | matrix | Ja | De DNS-servers voor de containergroep. - tekenreeks |
|  zoekenNaar zoeken | tekenreeks | Nee | De DNS-zoekdomeinen voor hostname opzoeken in de containergroep. |
|  opties | tekenreeks | Nee | De DNS-opties voor de containergroep. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Object ContainerProperties

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  installatiekopie | tekenreeks | Ja | De naam van de afbeelding die wordt gebruikt om de containerinstantie te maken. |
|  command | matrix | Nee | De opdrachten die binnen de containerinstantie in exec-vorm moeten worden uitgevoerd. - tekenreeks |
|  ports | matrix | Nee | De blootgestelde poorten op de containerinstantie. - [ContainerPort-object](#ContainerPort) |
|  omgevingVariabelen | matrix | Nee | De omgevingsvariabelen die u in de containerinstantie moet instellen. - [Object Omgevingsvariabele](#EnvironmentVariable) |
|  resources | object | Ja | De resourcevereisten van de containerinstantie. - [Object ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | matrix | Nee | Het volume wordt gemonteerd voor de containerinstantie. - [Object VolumeMount](#VolumeMount) |
|  levendigheidSonde | object | Nee | De levendigheidsssonde. - [Object ContainerProbe](#ContainerProbe) |
|  gereedheidSonde | object | Nee | De gereedheidssssssonde. - [Object ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Poortobject

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  Protocol | Enum | Nee | Het protocol dat is gekoppeld aan de poort. - TCP of UDP |
|  poort | geheel getal | Ja | het poortnummer. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume-object

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  Sharenaam | tekenreeks | Ja | De naam van het Azure-bestandsaandeel dat moet worden gemonteerd als een volume. |
|  Readonly | booleaans | Nee | De vlag die aangeeft of het Azure-bestand dat is gedeeld als een volume alleen-lezen is. |
|  storageAccountName | tekenreeks | Ja | De naam van het opslagaccount dat de Azure File-share bevat. |
|  storageAccountKey | tekenreeks | Nee | De toegangssleutel voor het opslagaccount die wordt gebruikt om toegang te krijgen tot het Azure-bestandsaandeel. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume object

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  map | tekenreeks | Nee | Doelmapnaam. Mag niet bevatten of beginnen met '..'.  Als '.' wordt geleverd, is de volumemap de git-repository.  Anders bevat het volume, indien opgegeven, de git-repository in de submap met de voornaam. |
|  Repository | tekenreeks | Ja | URL van opslagplaats |
|  Revisie | tekenreeks | Nee | Hash vastleggen voor de opgegeven revisie. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>LogAnalytics-object

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  workspaceId | tekenreeks | Ja | De werkruimte-id voor logboekanalyses |
|  werkruimteSleutel | tekenreeks | Ja | De werkruimtesleutel voor logboekanalyses |
|  logType (logType) | Enum | Nee | Het te gebruiken logboektype. - ContainerInsights of ContainerInstanceLogs |
|  metagegevens | object | Nee | Metagegevens voor loganalytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>ContainerPort-object

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  Protocol | Enum | Nee | Het protocol dat is gekoppeld aan de poort. - TCP of UDP |
|  poort | geheel getal | Ja | Het poortnummer dat binnen de containergroep wordt weergegeven. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Object Omgevingsvariabele

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  name | tekenreeks | Ja | De naam van de omgevingsvariabele. |
|  waarde | tekenreeks | Nee | De waarde van de omgevingsvariabele. |
|  secureValue secureValue | tekenreeks | Nee | De waarde van de variabele veilige omgeving. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Object ResourceRequirements

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  Verzoeken | object | Ja | De resourceaanvragen van deze containerinstantie. - [Object ResourceRequests](#ResourceRequests) |
|  Grenzen | object | Nee | De resourcelimieten van deze containerinstantie. - [Object ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Object VolumeMount

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  name | tekenreeks | Ja | De naam van de volumemount. |
|  mountPath | tekenreeks | Ja | Het pad in de container waar het volume moet worden gemonteerd. Mag geen dikke darm (:) bevatten. |
|  Readonly | booleaans | Nee | De vlag die aangeeft of de volumehouder alleen-lezen is. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Object ContainerProbe

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  Uitvoerend | object | Nee | De uitvoeringsopdracht voor sonde - [Object ContainerExec](#ContainerExec) |
|  httpGet | object | Nee | De instellingen voor Desonde van Http-get - [Object ContainerHttpGet](#ContainerHttpGet) |
|  initialDelaySeconds initialDelaySeconds | geheel getal | Nee | De eerste vertraging seconden. |
|  periodSeconden | geheel getal | Nee | De periodeseconden. |
|  failureThreshold | geheel getal | Nee | De faaldrempel. |
|  succesDrempel | geheel getal | Nee | De succesdrempel. |
|  time-outSeconden | geheel getal | Nee | De time-out seconden. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Object ResourceRequests

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | getal | Ja | De geheugenaanvraag in GB van deze containerinstantie. |
|  Cpu | getal | Ja | De CPU-aanvraag van deze containerinstantie. |
|  Gpu | object | Nee | De GPU-aanvraag van deze containerinstantie. - [GpuResource-object](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Object ResourceLimits

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | getal | Nee | De geheugenlimiet in GB van deze containerinstantie. |
|  Cpu | getal | Nee | De CPU-limiet van deze containerinstantie. |
|  Gpu | object | Nee | De GPU-limiet van deze containerinstantie. - [GpuResource-object](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Object ContainerExec

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  command | matrix | Nee | De opdrachten die binnen de container moeten worden uitgevoerd. - tekenreeks |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Object ContainerHttpGet

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  path | tekenreeks | Nee | Het pad naar sonde. |
|  poort | geheel getal | Ja | Het poortnummer om te onderzoeken. |
|  Regeling | Enum | Nee | De regeling. - http of https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource-object

|  Name | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  count | geheel getal | Ja | De telling van de GPU-bron. |
|  sku | Enum | Ja | De SKU van de GPU-bron. - K80, P100, V100 |


## <a name="next-steps"></a>Volgende stappen

Zie de zelfstudie [Een groep meerdere containers implementeren met behulp van een YAML-bestand](container-instances-multi-container-yaml.md).

Zie voorbeelden van het gebruik van een YAML-bestand om containergroepen in een [virtueel netwerk](container-instances-vnet.md) te implementeren of die [een extern volume monteren](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

