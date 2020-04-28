---
title: Poort nummer van een service opgeven met behulp van para meters
description: Laat zien hoe u para meters kunt gebruiken om de poort voor een toepassing op te geven in Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: a53626b8fd362397ba89df30b099fa3c9ff7b0a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75609856"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Het poort nummer van een service opgeven met behulp van para meters in Service Fabric

In dit artikel leest u hoe u het poort nummer van een service kunt opgeven met behulp van de para meters in Service Fabric met Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Procedure voor het opgeven van het poort nummer van een service met behulp van para meters

In dit voor beeld stelt u het poort nummer in voor uw asp.net core web-API met behulp van een para meter.

1. Open Visual Studio en maak een nieuwe Service Fabric-toepassing.
1. Kies de sjabloon staatloze ASP.NET Core.
1. Kies Web API.
1. Open het bestand ServiceManifest. XML.
1. Noteer de naam van het eind punt dat is opgegeven voor uw service. De standaardwaarde is `ServiceEndpoint`.
1. Open het bestand ApplicationManifest. XML
1. In het `ServiceManifestImport` element voegt u een nieuw `RessourceOverrides` -element toe met een verwijzing naar het eind punt in het bestand ServiceManifest. XML.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. In het `Endpoint` -element kunt u nu elk kenmerk overschrijven met een para meter. In dit voor beeld geeft `Port` u een parameter naam op met behulp van vier Kante haken, bijvoorbeeld`[MyWebAPI_PortNumber]`

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. In het bestand ApplicationManifest. XML geeft u vervolgens de para meter op in het `Parameters` element

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. En definieer een`DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Open de map Application parameters en het `Cloud.xml` bestand
1. Als u een andere poort wilt gebruiken bij het publiceren naar een extern cluster, voegt u de para meter met het poort nummer toe aan dit bestand.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Wanneer u uw toepassing publiceert vanuit Visual Studio met behulp van het publicatie profiel Cloud. XML, is uw service geconfigureerd voor het gebruik van poort 80. Als u de toepassing implementeert zonder de para meter MyWebAPI_PortNumber op te geven, gebruikt de service poort 8080.

## <a name="next-steps"></a>Volgende stappen
Zie de [artikelen toepassingen beheren voor meerdere omgevingen](service-fabric-manage-multiple-environment-app-configuration.md)voor meer informatie over een aantal van de belangrijkste concepten die in dit artikel worden besproken.

Zie [uw service Fabric-toepassingen beheren in Visual Studio](service-fabric-manage-application-in-visual-studio.md)voor informatie over andere mogelijkheden voor het beheren van apps die beschikbaar zijn in Visual Studio.
