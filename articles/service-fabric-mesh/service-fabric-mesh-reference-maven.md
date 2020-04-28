---
title: Naslag informatie over Azure Service Fabric mesh maven
description: Bevat de referentie voor het gebruik van de Maven-invoeg toepassing voor Service Fabric mesh
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.openlocfilehash: bcc3fb7c6c3adce0997d0960c4d98227089b048b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75459016"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Maven-invoeg toepassing voor Service Fabric mesh

## <a name="prerequisites"></a>Vereisten

- Java-SDK
- Maven
- Azure CLI met net-extensie
- Service Fabric-CLI

## <a name="goals"></a>Doelstellingen

### `azure-sfmesh:init`
- Hiermee maakt `servicefabric` u een map met `appresources` een map die het `application.yaml` bestand bevat. 

### `azure-sfmesh:addservice`
- Hiermee maakt u een `servicefabric` map in de map met de naam van de service en maakt u het yaml-bestand van de service. 

### `azure-sfmesh:addnetwork`
- Hiermee wordt `network` een yaml met de gegeven netwerk naam in `appresources` de map gegenereerd 

### `azure-sfmesh:addgateway`
- Hiermee wordt `gateway` een yaml met de naam van de door `appresources` gegeven gateway in de map gegenereerd 

#### `azure-sfmesh:addvolume`
- Hiermee wordt `volume` een yaml met de naam van het volume `appresources` in de map gegenereerd.

### `azure-sfmesh:addsecret`
- Hiermee wordt `secret` een yaml met de naam van het geheim `appresources` in de map gegenereerd 

### `azure-sfmesh:addsecretvalue`
- Hiermee wordt `secretvalue` een yaml met de naam van de geheime en geheime waarde `appresources` in de map gegenereerd 

### `azure-sfmesh:deploy`
- Hiermee worden de yamls samengevoegd vanuit `servicefabric` de map en wordt een Azure Resource Manager-sjabloon JSON gemaakt in de huidige map.
- Hiermee worden alle resources naar de Azure Service Fabric-netwerk omgeving geïmplementeerd 

### `azure-sfmesh:deploytocluster`
- Hiermee maakt u een`meshDeploy`map () die de implementatie-jsons bevat die zijn gegenereerd op basis van yamls die van toepassing zijn op service Fabric clusters
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

- Hiermee maakt u een `servicefabric->appresources` map met de naam in de hoofdmap die een toepassings yaml bevat`app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Resource toevoegen aan uw toepassing

#### <a name="add-a-new-network-to-your-application"></a>Een nieuw netwerk aan uw toepassing toevoegen
Voer de onderstaande opdracht uit om een netwerk bron yaml te maken. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Hiermee maakt u een netwerk- `servicefabric->appresources` yaml in de map met de naam`network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Een nieuwe service toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een service yaml te maken. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Hiermee maakt u een service- `servicefabric->helloworldservice` yaml `service_helloworldservice` in de `helloworldservicenetwork` map met `helloworldserver` de naam die verwijst naar & de app
- De service luistert op poort 8080
- De service gebruikt ***helloworldserver: nieuwste*** als container installatie kopie.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Een nieuwe gateway resource toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een gateway resource yaml te maken. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Hiermee maakt u een nieuwe gateway YAML `servicefabric->appresources` in de map met de naam`gateway_helloworldgateway`
- Verwijst `helloworldservicelistener` naar de service-listener die luistert naar aanroepen vanaf deze gateway. Verwijst ook naar `helloworldservice` de als de service `helloworldservicenetwork` , als het netwerk `helloworldserver` en als de toepassing. 
- Luistert naar aanvragen op poort 80

#### <a name="add-a-new-volume-to-your-application"></a>Een nieuw volume toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een volume resource yaml te maken. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Hiermee maakt u een volume YAML `servicefabric->appresources` in de map met de naam`volume_vol1`
- Hiermee stelt u de eigenschappen in `volumeAccountKey`voor de `volumeShareName` vereiste para meters, en als hierboven
- Voor meer informatie over het verwijzen naar dit gemaakte volume gaat u naar de volgende, [implementeert u de app met Azure files volume](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Een nieuwe geheime resource toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een geheime resource yaml te maken. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Hiermee maakt u een geheime YAML `servicefabric->appresources` in de map met de naam`secret_secret1`
- Voor meer informatie over het verwijzen naar dit gemaakte geheim gaat u naar het volgende: [geheimen beheren](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Een nieuwe secretvalue-resource toevoegen aan uw toepassing
Voer de onderstaande opdracht uit om een secretvalue-resource yaml te maken. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Maak een secretvalue-YAML in `servicefabric->appresources` de map met de naam`secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>De toepassing lokaal uitvoeren

Met behulp van doel `azure-sfmesh:deploytocluster`stellingen kunt u de toepassing lokaal uitvoeren met behulp van de volgende opdracht:

```cmd
mvn azure-sfmesh:deploytocluster
```

Standaard implementeert dit doel resources voor het lokale cluster. Als u implementeert naar een lokaal cluster, wordt ervan uitgegaan dat u een lokale Service Fabric cluster actief hebt. Het lokale Service Fabric cluster voor bronnen wordt momenteel alleen ondersteund in [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Maakt JSONs van yamls die van toepassing zijn op Service Fabric clusters
- Implementeert vervolgens naar het cluster eindpunt

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Toepassing implementeren in azure Service Fabric net

U kunt met behulp `azure-sfmesh:deploy`van doel einden implementeren naar service Fabric-netwerk omgeving door de onderstaande opdracht uit te voeren:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Hiermee maakt u een resource `todoapprg` groep met de naam als deze niet bestaat.
- Hiermee maakt u een Azure Resource Manager sjabloon JSON door de YAMLs samen te voegen. 
- Implementeert de JSON naar de Azure Service Fabric-netwerk omgeving.
