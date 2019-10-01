---
title: Instructies voor het opgeven van omgevings variabelen voor services in azure Service Fabric | Microsoft Docs
description: Laat zien hoe u omgevings variabelen gebruikt voor toepassingen in Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: df9b199c24301016b9f9da8a8dec52129bbf94bd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703542"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Omgevings variabelen opgeven voor services in Service Fabric

In dit artikel leest u hoe u omgevings variabelen kunt opgeven voor een service of container in Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedure voor het opgeven van omgevings variabelen voor services

In dit voor beeld stelt u een omgevings variabele in voor een container. In dit artikel wordt ervan uitgegaan dat u al een toepassings-en service manifest hebt.

1. Open het bestand ServiceManifest. XML.
2. Voeg in het element `CodePackage` een nieuw `EnvironmentVariables`-element en een `EnvironmentVariable`-element toe voor elke omgevings variabele.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   Omgevings variabelen kunnen worden overschreven in het toepassings manifest.

3. Als u de omgevings variabelen in het manifest van de toepassing wilt overschrijven, gebruikt u het element `EnvironmentOverrides`.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Omgevings variabelen dynamisch opgeven met docker opstellen

Service Fabric ondersteunt de mogelijkheid om [docker samen te gebruiken voor implementatie](service-fabric-docker-compose.md#supported-compose-directives). Het opstellen van bestanden kan variabelen van de bron omgeving van de shell zijn. Dit gedrag kan worden gebruikt om de gewenste omgevings waarden dynamisch te vervangen:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Volgende stappen
Zie de [artikelen toepassingen beheren voor meerdere omgevingen](service-fabric-manage-multiple-environment-app-configuration.md)voor meer informatie over een aantal van de belangrijkste concepten die in dit artikel worden besproken.

Zie [uw service Fabric-toepassingen beheren in Visual Studio](service-fabric-manage-application-in-visual-studio.md)voor informatie over andere mogelijkheden voor het beheren van apps die beschikbaar zijn in Visual Studio.
