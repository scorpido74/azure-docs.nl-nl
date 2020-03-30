---
title: Verwijzing naar Azure Service Fabric Mesh Maven
description: Bevat de referentie voor het gebruik van de Maven-plug-in voor Service Fabric Mesh
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.openlocfilehash: bcc3fb7c6c3adce0997d0960c4d98227089b048b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75459016"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Maven Plugin voor Service Fabric Mesh

## <a name="prerequisites"></a>Vereisten

- Java SDK
- Maven
- Azure CLI met mesh-extensie
- Service Fabric-CLI

## <a name="goals"></a>Doelstellingen

### `azure-sfmesh:init`
- Hiermee `servicefabric` maakt u `appresources` een map `application.yaml` met een map met het bestand. 

### `azure-sfmesh:addservice`
- Hiermee maakt `servicefabric` u een map in de map met de servicenaam en wordt het YAML-bestand van de service gemaakt. 

### `azure-sfmesh:addnetwork`
- Genereert `network` een YAML met de `appresources` opgegeven netwerknaam in de map 

### `azure-sfmesh:addgateway`
- Genereert `gateway` een YAML met de `appresources` opgegeven gatewaynaam in de map 

#### `azure-sfmesh:addvolume`
- Hiermee `volume` genereert u een YAML `appresources` met de opgegeven volumenaam in de map.

### `azure-sfmesh:addsecret`
- Genereert `secret` een YAML met de `appresources` opgegeven geheime naam in de map 

### `azure-sfmesh:addsecretvalue`
- Genereert `secretvalue` een YAML met de opgegeven geheime `appresources` en geheime waardenaam in de map 

### `azure-sfmesh:deploy`
- Hiermee worden de yamls uit de `servicefabric` map samengevoegd en wordt een Json met Azure Resource Manager-sjabloon JSON gemaakt in de huidige map.
- Implementeert alle resources naar de Azure Service Fabric Mesh-omgeving 

### `azure-sfmesh:deploytocluster`
- Hiermee maakt`meshDeploy`u een map ( ) die de implementatie-JSON's bevat die zijn gegenereerd uit yamls die van toepassing zijn op clusters van servicefabric
- Implementeert alle resources in het cluster ServiceFabric
 

## <a name="usage"></a>Gebruik

Als u de Maven-invoegtoepassing in uw Maven Java-app wilt gebruiken, voegt u het volgende codefragment aan het bestand pom.xml toe:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Algemene configuratie

De Maven-plug-in biedt momenteel geen ondersteuning voor algemene configuraties van Maven-plug-ins voor Azure.

## <a name="how-to"></a>Uitleg:

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Initialize Maven-project voor Azure Service Fabric Mesh
Voer de volgende opdracht uit om het YAML-bestand van de toepassingsbron te maken.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Hiermee maakt `servicefabric->appresources` u een map met de naam YAML van de toepassing YAML`app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Bron toevoegen aan uw toepassing

#### <a name="add-a-new-network-to-your-application"></a>Een nieuw netwerk toevoegen aan uw toepassing
Voer de opdracht hieronder uit om een netwerkbronyaml te maken. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Maakt een netwerk YAML in map `servicefabric->appresources` met de naam`network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Een nieuwe service toevoegen aan uw toepassing
Voer de opdracht hieronder uit om een serviceyaml te maken. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Hiermee maakt u een `servicefabric->helloworldservice` service `service_helloworldservice` YAML in een map met de naam met de naam & `helloworldservicenetwork` de `helloworldserver` app
- De dienst zou luisteren op poort 8080
- De service zou worden met behulp van ***helloworldserver: laatste*** als het container beeld.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Een nieuwe gatewaybron toevoegen aan uw toepassing
Voer de opdracht hieronder uit om een gatewayresource yaml te maken. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Maakt een nieuwe gateway YAML in map `servicefabric->appresources` met de naam`gateway_helloworldgateway`
- Verwijzingen `helloworldservicelistener` als de servicelistener die luistert naar oproepen van deze gateway. Verwijst ook `helloworldservice` naar de `helloworldservicenetwork` als de `helloworldserver` dienst, als het netwerk en als de toepassing. 
- Luistert naar aanvragen op poort 80

#### <a name="add-a-new-volume-to-your-application"></a>Een nieuw volume toevoegen aan uw toepassing
Voer de opdracht hieronder uit om een volumebronyaml te maken. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Maakt een yaml-volume in map `servicefabric->appresources` met de naam`volume_vol1`
- Hiermee stelt u `volumeAccountKey`eigenschappen `volumeShareName` in voor vereiste parameters en als hierboven
- Ga voor meer informatie over het verwijzen naar dit gemaakte volume naar het volgende: [App implementeren met Azure Files Volume](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Een nieuwe geheime bron toevoegen aan uw toepassing
Voer de opdracht hieronder uit om een geheime resource yaml te maken. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Maakt een geheime YAML in map `servicefabric->appresources` met de naam`secret_secret1`
- Ga voor meer informatie over het verwijzen naar dit gemaakte geheim naar het [volgende, Geheimen beheren](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Een nieuwe secretvalue resource toevoegen aan uw toepassing
Voer de opdracht hieronder uit om een secretvalue resource yaml te maken. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Een yaml met geheime `servicefabric->appresources` waarde maken in map met de naam`secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>De toepassing lokaal uitvoeren

Met behulp van `azure-sfmesh:deploytocluster`doel u de toepassing lokaal uitvoeren met de onderstaande opdracht:

```cmd
mvn azure-sfmesh:deploytocluster
```

Standaard implementeert dit doel resources naar lokaal cluster. Als u implementeert in lokaal cluster, wordt ervan uitgegaan dat u een lokaal cluster van Service Fabric hebt. Het cluster Lokale servicestructuur voor resources wordt momenteel alleen ondersteund in [Windows.](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

- Maakt JSONs uit yamls die van toepassing zijn op servicefabricclusters
- Implementeert vervolgens naar het eindpunt van het cluster

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Toepassing implementeren in Azure Service Fabric-mesh

Met behulp van `azure-sfmesh:deploy`doel u de netomgeving van Service Fabric implementeren door de onderstaande opdracht uit te voeren:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Hiermee maakt u `todoapprg` een resourcegroep die wordt aangeroepen als deze niet bestaat.
- Hiermee maakt u een JSON met Azure Resource Manager-sjabloon door de YAMLs samen te voegen. 
- Implementeert de JSON naar de Azure Service Fabric Mesh-omgeving.
