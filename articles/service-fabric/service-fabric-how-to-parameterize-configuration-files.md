---
title: Para meters-configuratie bestanden in azure Service Fabric
description: Meer informatie over het para meters van configuratie bestanden in Service Fabric, een handige techniek bij het beheer van meerdere omgevingen.
author: mikkelhegn
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 4e96a732cffd70b0a5c24e7ebafe214297a72720
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75644627"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Configuratie bestanden in Service Fabric para meters

In dit artikel leest u hoe u een configuratie bestand in Service Fabric kunt para meters.  Als u nog niet bekend bent met de basis concepten van het beheren van toepassingen voor meerdere omgevingen, lees dan [toepassingen beheren voor meerdere omgevingen](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedure voor parameterizing-configuratie bestanden

In dit voor beeld overschrijft u een configuratie waarde met behulp van para meters in de implementatie van uw toepassing.

1. Open het * \<bestand MyService> \packageroot\config\settings.XML* in uw service project.
1. Stel een naam en waarde voor de configuratie parameter in, bijvoorbeeld cache grootte gelijk aan 25, door het volgende XML-bestand toe te voegen:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Sla het bestand op en sluit het.
1. Open het * \<bestand mijn toepassing> \applicationpackageroot\applicationmanifest.XML* .
1. In het bestand ApplicationManifest. XML declareert u een para meter en standaard waarde `Parameters` in het-element.  Het is aan te bevelen de naam van de service (bijvoorbeeld "MyService") in de parameter naam te vinden.

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. Voeg in `ServiceManifestImport` de sectie van het ApplicationManifest. XML-bestand een `ConfigOverrides` - `ConfigOverride` element toe, dat verwijst naar het configuratie pakket, de sectie en de para meter.

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> Als u een ConfigOverride toevoegt, kiest Service Fabric altijd de para meters van de toepassing of de standaard waarde die is opgegeven in het toepassings manifest.
>
>

## <a name="next-steps"></a>Volgende stappen
Zie [uw service Fabric-toepassingen beheren in Visual Studio](service-fabric-manage-application-in-visual-studio.md)voor informatie over andere mogelijkheden voor het beheren van apps die beschikbaar zijn in Visual Studio.
