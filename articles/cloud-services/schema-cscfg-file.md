---
title: Azure Cloud Services Definition Schema (.cscfg-bestand) | Microsoft Documenten
description: Een serviceconfiguratiebestand (.cscfg) geeft aan hoeveel rolinstanties moeten worden geïmplementeerd voor elke rol, configuratiewaarden en duimafdrukken van certificaten voor een rol.
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 35
author: tgore03
ms.author: tagore
ms.openlocfilehash: cb77181e00c97b7f426429793f17af3cb5e84ebe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534742"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services Config-schema (.cscfg-bestand)
Het serviceconfiguratiebestand geeft het aantal rolinstanties op dat moet worden geïmplementeerd voor elke rol in de service, de waarden van alle configuratie-instellingen en de duimafdrukken voor certificaten die aan een rol zijn gekoppeld. Als de service deel uitmaakt van een virtueel netwerk, moet configuratie-informatie voor het netwerk worden verstrekt in het serviceconfiguratiebestand en in het configuratiebestand voor virtuele netwerken. De standaardextensie voor het serviceconfiguratiebestand is .cscfg.

Het servicemodel wordt beschreven door het [Cloud Service (klassieke) definitieschema.](schema-csdef-file.md)

Standaard is het configuratieschemabestand azure diagnostics `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` geïnstalleerd in de map. Vervang `<version>` door de geïnstalleerde versie van de [Azure SDK](https://azure.microsoft.com/downloads/).

Zie [Wat is het Cloud Service-model](cloud-services-model-and-package.md)voor meer informatie over het configureren van rollen in een service.

## <a name="basic-service-configuration-schema"></a>Configuratieschema voor basisservice
De basisindeling van het serviceconfiguratiebestand is als volgt.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Schemadefinities
De volgende onderwerpen beschrijven `ServiceConfiguration` het schema voor het element:

- [Role-schema](schema-cscfg-role.md)
- [NetworkConfiguration-schema](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Naamruimte voor serviceconfiguratie
De XML-naamruimte voor het `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`serviceconfiguratiebestand is: .

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a>Element ServiceConfiguratie
Het `ServiceConfiguration` element is het element op het hoogste niveau van het serviceconfiguratiebestand.

In de volgende tabel worden `ServiceConfiguration` de kenmerken van het element beschreven. Alle kenmerkenwaarden zijn tekenreekstypen.

| Kenmerk | Beschrijving |
| --------- | ----------- |
|Servicenaam|Vereist. De naam van de cloudservice. De hier opgegeven naam moet overeenkomen met de naam die is opgegeven in het servicedefinitiebestand.|
|osFamily|Optioneel. Hiermee geeft u het besturingssysteem voor gasten op dat wordt uitgevoerd op rolexemplaren in de cloudservice. Zie Azure Guest OS Releases [en SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md)voor informatie over ondersteunde gastosreleases.<br /><br /> Als u geen `osFamily` waarde opneemt en `osVersion` u het kenmerk niet hebt ingesteld op een specifieke gastversie, wordt een standaardwaarde van 1 gebruikt.|
|osVersion|Optioneel. Hiermee geeft u de versie op van het besturingssysteem gast dat wordt uitgevoerd op rolexemplaren in de cloudservice. Zie [Azure Guest OS Releases en SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md)voor meer informatie over gastbesturingssysteemversies.<br /><br /> U opgeven dat het gastbesturingssysteem automatisch moet worden geüpgraded naar de nieuwste versie. Stel hiervoor de waarde van `osVersion` het `*`kenmerk in op . Wanneer u `*`bent ingesteld op , worden de rolinstanties geïmplementeerd met behulp van de nieuwste versie van het gastbesturingssysteem voor de opgegeven besturingssysteemfamilie en worden ze automatisch bijgewerkt wanneer nieuwe versies van het gastbesturingssysteem worden uitgebracht.<br /><br /> Als u een specifieke versie `Configuration String` handmatig wilt opgeven, gebruikt u de sectie van de sectie Van de tabel in de **toekomst, huidige en overgangsgast-besturingssysteemversies** van [Azure Guest OS Releases en SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).<br /><br /> De standaardwaarde `osVersion` voor `*`het kenmerk is .|
|schemaVersie|Optioneel. Hiermee geeft u de versie van het serviceconfiguratieschema op. Met de schemaversie kan Visual Studio de juiste SDK-hulpprogramma's selecteren die u gebruiken voor schemavalidatie als er meer dan één versie van de SDK naast elkaar is geïnstalleerd. Zie [Azure Guest OS Releases en SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md) voor meer informatie over schema- en versiecompatibiliteit|

Het serviceconfiguratiebestand moet `ServiceConfiguration` één element bevatten. Het `ServiceConfiguration` element kan een `Role` willekeurig aantal `NetworkConfiguration` elementen en nul of 1 elementen bevatten.