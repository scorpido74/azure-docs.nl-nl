---
title: Parameterize config-bestanden in Azure Service Fabric
description: Meer informatie over het parameteriseren van configuratiebestanden in Service Fabric, een handige techniek bij het beheren van meerdere omgevingen.
author: mikkelhegn
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 4e96a732cffd70b0a5c24e7ebafe214297a72720
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644627"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Configuratiebestanden parameteriseren in Service Fabric

In dit artikel ziet u hoe u een configuratiebestand parameteriseren in Service Fabric.  Als u nog niet bekend bent met de kernconcepten van het beheren van toepassingen voor meerdere omgevingen, leest u [Toepassingen beheren voor meerdere omgevingen.](service-fabric-manage-multiple-environment-app-configuration.md)

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedure voor het parameteriseren van configuratiebestanden

In dit voorbeeld overschrijft u een configuratiewaarde met behulp van parameters in de implementatie van uw toepassing.

1. Open * \<het bestand MyService>\PackageRoot\Config\Settings.xml* in uw serviceproject.
1. Stel een naam en waarde van de configuratieparameter in, bijvoorbeeld cachegrootte gelijk aan 25, door de volgende XML toe te voegen:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Sla het bestand op en sluit het.
1. Open het * \<bestand MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml.*
1. Declareer in het bestand ApplicationManifest.xml een `Parameters` parameter en standaardwaarde in het element.  Het wordt aanbevolen dat de parameternaam de naam van de service bevat (bijvoorbeeld 'MyService').

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. Voeg `ServiceManifestImport` in de sectie van het bestand `ConfigOverrides` `ConfigOverride` ApplicationManifest.xml een en element toe, waarbij wordt verwezen naar het configuratiepakket, de sectie en de parameter.

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
> In het geval dat u een ConfigOverride toevoegt, kiest Service Fabric altijd de toepassingsparameters of de standaardwaarde die is opgegeven in het toepassingsmanifest.
>
>

## <a name="next-steps"></a>Volgende stappen
Zie [Uw Service Fabric-toepassingen beheren in Visual Studio](service-fabric-manage-application-in-visual-studio.md)voor informatie over andere mogelijkheden voor app-beheer die beschikbaar zijn in Visual Studio.
