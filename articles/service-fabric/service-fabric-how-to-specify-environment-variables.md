---
title: Omgevingsvariabelen voor services opgeven
description: Laat zien hoe u omgevingsvariabelen gebruiken voor toepassingen in Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f4c4f2a1c140e3d0f181c4fd55482056f9f91b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614312"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Omgevingsvariabelen voor services in Service Fabric opgeven

In dit artikel ziet u hoe u omgevingsvariabelen opgeeft voor een service of container in Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedure voor het specificeren van omgevingsvariabelen voor diensten

In dit voorbeeld stelt u een omgevingsvariabele in voor een container. Het artikel gaat ervan uit dat u al een aanvraag- en servicemanifest hebt.

1. Open het bestand ServiceManifest.xml.
2. Voeg `CodePackage` in het element `EnvironmentVariables` een `EnvironmentVariable` nieuw element en een element toe voor elke omgevingsvariabele.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   Omgevingsvariabelen kunnen worden overschreven in het toepassingsmanifest.

3. Als u de omgevingsvariabelen in het `EnvironmentOverrides` toepassingsmanifest wilt overschrijven, gebruikt u het element.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Omgevingsvariabelen dynamisch opgeven met Docker Compose

Service Fabric ondersteunt de mogelijkheid om Docker Compose te [gebruiken voor implementatie.](service-fabric-docker-compose.md#supported-compose-directives) Bestanden opstellen kan omgevingsvariabelen uit de shell bron. Dit gedrag kan worden gebruikt om gewenste omgevingswaarden dynamisch te vervangen:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Volgende stappen
Zie de [toepassingen beheren voor meerdere omgevingen voor](service-fabric-manage-multiple-environment-app-configuration.md)meer informatie over enkele van de kernconcepten die in dit artikel worden besproken.

Zie [Uw Service Fabric-toepassingen beheren in Visual Studio](service-fabric-manage-application-in-visual-studio.md)voor informatie over andere mogelijkheden voor app-beheer die beschikbaar zijn in Visual Studio.
