---
title: Naslag informatie over Azure Service Fabric mesh maven
description: Bevat de referentie voor het gebruik van de Maven-invoeg toepassing voor Service Fabric mesh
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.openlocfilehash: bcc3fb7c6c3adce0997d0960c4d98227089b048b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459016"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Maven-invoeg toepassing voor Service Fabric mesh

## <a name="prerequisites"></a>Vereisten

- Java SDK
- Maven
- Azure CLI met net-extensie
- Service Fabric-CLI

## <a name="goals"></a>Doelstellingen

### `azure-sfmesh:init`
- Hiermee maakt u een `servicefabric` map met een `appresources`-map die het `application.yaml`-bestand bevat. 

### `azure-sfmesh:addservice`
- Hiermee maakt u een map in `servicefabric` map met de service naam en maakt u het YAML-bestand van de service. 

### `azure-sfmesh:addnetwork`
- Hiermee wordt een `network` YAML met de gegeven netwerk naam in de map `appresources` gegenereerd 

### `azure-sfmesh:addgateway`
- Hiermee wordt een `gateway` YAML gegenereerd met de naam van de door gegeven gateway in de map `appresources` 

#### `azure-sfmesh:addvolume`
- Hiermee genereert u een `volume` YAML met de naam van het volume in de map `appresources`.

### `azure-sfmesh:addsecret`
- Hiermee genereert u een `secret` YAML met de naam van het geheim in de map `appresources` 

### `azure-sfmesh:addsecretvalue`
- Hiermee genereert u een `secretvalue` YAML met de naam van de geheime en geheime waarde in de map `appresources` 

### `azure-sfmesh:deploy`
- Hiermee worden de yamls samengevoegd vanuit de map `servicefabric` en wordt een Azure Resource Manager sjabloon JSON gemaakt in de huidige map.
- Hiermee worden alle resources naar de Azure Service Fabric-netwerk omgeving geïmplementeerd 

### `azure-sfmesh:deploytocluster`
- Hiermee maakt u een map (`meshDeploy`) die de implementatie-JSONs bevat die zijn gegenereerd op basis van yamls die van toepassing zijn op Service Fabric-clusters
- Hiermee worden alle resources naar het Service Fabric cluster geïmplementeerd
 

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

De Maven-invoeg toepassing biedt momenteel geen ondersteuning voor algemene configuraties van Maven-invoeg toepassingen voor Azure.

## <a name="how-to"></a>Uitleg:

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Maven-project initialiseren voor Azure Service Fabric mesh
Voer de volgende opdracht uit om het YAML-bestand van de toepassings bron te maken.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Hiermee maakt u een map met de naam `servicefabric->appresources` in de hoofdmap die een toepassings YAML bevat met de naam `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Resource toevoegen aan uw toepassing

#### <a name="add-a-new-network-to-your-application"></a>Een nieuw netwerk aan uw toepassing toevoegen
Voer de onderstaande opdracht uit om een netwerk bron yaml te maken. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Hiermee maakt u een netwerk YAML in de map `servicefabric->appresources` met de naam `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Een nieuwe service toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een service yaml te maken. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Hiermee maakt u een service-YAML in de map `servicefabric->helloworldservice` met de naam `service_helloworldservice` die verwijst naar `helloworldservicenetwork` & de `helloworldserver`-app
- De service luistert op poort 8080
- De service gebruikt ***helloworldserver: nieuwste*** als container installatie kopie.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Een nieuwe gateway resource toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een gateway resource yaml te maken. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Hiermee maakt u een nieuwe gateway YAML in de map `servicefabric->appresources` met de naam `gateway_helloworldgateway`
- Verwijst naar `helloworldservicelistener` als service-listener die luistert naar aanroepen vanaf deze gateway. Verwijst ook naar de `helloworldservice` als de service, `helloworldservicenetwork` als het netwerk en `helloworldserver` als de toepassing. 
- Luistert naar aanvragen op poort 80

#### <a name="add-a-new-volume-to-your-application"></a>Een nieuw volume toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een volume resource yaml te maken. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Hiermee maakt u een volume YAML in de map `servicefabric->appresources` met de naam `volume_vol1`
- Hiermee stelt u de eigenschappen in voor de vereiste para meters, `volumeAccountKey`en `volumeShareName` zoals hierboven
- Voor meer informatie over het verwijzen naar dit gemaakte volume gaat u naar de volgende, [implementeert u de app met Azure files volume](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Een nieuwe geheime resource toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een geheime resource yaml te maken. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Hiermee maakt u een geheim YAML in de map `servicefabric->appresources` met de naam `secret_secret1`
- Voor meer informatie over het verwijzen naar dit gemaakte geheim gaat u naar het volgende: [geheimen beheren](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Een nieuwe secretvalue-resource toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een secretvalue-resource yaml te maken. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Maak een secretvalue-YAML in de map `servicefabric->appresources` met de naam `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>De toepassing lokaal uitvoeren

Met behulp van doel `azure-sfmesh:deploytocluster`kunt u de toepassing lokaal uitvoeren met behulp van de volgende opdracht:

```cmd
mvn azure-sfmesh:deploytocluster
```

Standaard implementeert dit doel resources voor het lokale cluster. Als u implementeert naar een lokaal cluster, wordt ervan uitgegaan dat u een lokale Service Fabric cluster actief hebt. Het lokale Service Fabric cluster voor bronnen wordt momenteel alleen ondersteund in [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Maakt JSONs van yamls die van toepassing zijn op Service Fabric clusters
- Implementeert vervolgens naar het cluster eindpunt

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Toepassing implementeren in azure Service Fabric net

Met behulp van doel `azure-sfmesh:deploy`, kunt u implementeren in Service Fabric net Environment door de onderstaande opdracht uit te voeren:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Hiermee maakt u een resource groep met de naam `todoapprg` als deze niet bestaat.
- Hiermee maakt u een Azure Resource Manager sjabloon JSON door de YAMLs samen te voegen. 
- Implementeert de JSON naar de Azure Service Fabric-netwerk omgeving.
