---
title: Poortnummer van een service opgeven met behulp van parameters
description: Toont u hoe u parameters gebruikt om de poort voor een toepassing in Service Fabric op te geven
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: a53626b8fd362397ba89df30b099fa3c9ff7b0a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609856"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Het poortnummer van een service opgeven met behulp van parameters in Service Fabric

In dit artikel ziet u hoe u het poortnummer van een service opgeeft met behulp van parameters in Service Fabric met behulp van Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Procedure voor het opgeven van het poortnummer van een service aan de hand van parameters

In dit voorbeeld stelt u het poortnummer in voor uw asp.net kernweb-API met behulp van een parameter.

1. Open Visual Studio en maak een nieuwe Service Fabric-toepassing.
1. Kies de sjabloon Stateless ASP.NET Core.
1. Kies Web-API.
1. Open het bestand ServiceManifest.xml.
1. Let op de naam van het eindpunt dat voor uw service is opgegeven. De standaardwaarde is `ServiceEndpoint`.
1. Het bestand ApplicationManifest.xml openen
1. Voeg `ServiceManifestImport` in het element `RessourceOverrides` een nieuw element toe met een verwijzing naar het eindpunt in het bestand ServiceManifest.xml.

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

1. In `Endpoint` het element u nu elk kenmerk overschrijven met behulp van een parameter. In dit voorbeeld `Port` geeft u deze op en stelt u deze in op een parameternaam met behulp van vierkante haakjes-`[MyWebAPI_PortNumber]`

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

1. Nog steeds in het bestand ApplicationManifest.xml geeft `Parameters` u vervolgens de parameter in het element op

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

1. De map ApplicationParameters `Cloud.xml` en het bestand openen
1. Als u een andere poort wilt opgeven die moet worden gebruikt bij het publiceren naar een extern cluster, voegt u de parameter met het poortnummer toe aan dit bestand.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Wanneer u uw toepassing publiceert vanuit Visual Studio met het publicatieprofiel van Cloud.xml, is uw service geconfigureerd om poort 80 te gebruiken. Als u de toepassing implementeert zonder de parameter MyWebAPI_PortNumber op te geven, gebruikt de service poort 8080.

## <a name="next-steps"></a>Volgende stappen
Zie de [toepassingen beheren voor meerdere omgevingen voor](service-fabric-manage-multiple-environment-app-configuration.md)meer informatie over enkele van de kernconcepten die in dit artikel worden besproken.

Zie [Uw Service Fabric-toepassingen beheren in Visual Studio](service-fabric-manage-application-in-visual-studio.md)voor informatie over andere mogelijkheden voor app-beheer die beschikbaar zijn in Visual Studio.
