---
title: Azure Cloud Services definition-schema (cscfg-bestand) | Microsoft Docs
description: Een service configuratie bestand (. cscfg) geeft aan hoeveel rolinstanties er moeten worden geïmplementeerd voor elke rol, configuratie waarden en certificaat vingerafdrukken voor een rol.
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 35
author: tgore03
ms.author: tagore
ms.openlocfilehash: cb77181e00c97b7f426429793f17af3cb5e84ebe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79534742"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services config-schema (cscfg-bestand)
Het configuratie bestand van de service specificeert het aantal rolinstanties dat moet worden geïmplementeerd voor elke rol in de service, de waarden van de configuratie-instellingen en de vinger afdrukken voor alle certificaten die aan een rol zijn gekoppeld. Als de service deel uitmaakt van een Virtual Network, moet de configuratie-informatie voor het netwerk worden verstrekt in het service configuratie bestand en in het configuratie bestand voor virtuele netwerken. De standaard extensie voor het service configuratie bestand is. cscfg.

Het service model wordt beschreven in het [definitie schema van Cloud service (klassiek)](schema-csdef-file.md).

Het Azure Diagnostics-configuratie schema bestand wordt standaard geïnstalleerd in de `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` map. Vervang `<version>` door de geïnstalleerde versie van de [Azure SDK](https://azure.microsoft.com/downloads/).

Zie [Wat is het Cloud service model](cloud-services-model-and-package.md)voor meer informatie over het configureren van rollen in een service.

## <a name="basic-service-configuration-schema"></a>Basis schema voor service configuratie
De basis indeling van het service configuratie bestand is als volgt.

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

## <a name="schema-definitions"></a>Schema definities
In de volgende onderwerpen wordt het schema voor `ServiceConfiguration` het-element beschreven:

- [Role-schema](schema-cscfg-role.md)
- [NetworkConfiguration-schema](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Service configuratie naam ruimte
De XML-naam ruimte voor het service configuratie bestand `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`is:.

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a>ServiceConfiguration-element
Het `ServiceConfiguration` element is het element op het hoogste niveau van het service configuratie bestand.

In de volgende tabel worden de kenmerken van `ServiceConfiguration` het element beschreven. Alle kenmerk waarden zijn teken reeks typen.

| Kenmerk | Beschrijving |
| --------- | ----------- |
|serviceName|Vereist. De naam van de Cloud service. De naam die u hier opgeeft, moet overeenkomen met de naam die is opgegeven in het service definitie bestand.|
|osFamily|Optioneel. Hiermee geeft u het gast besturingssysteem op dat wordt uitgevoerd op rolinstanties in de Cloud service. Zie [Azure Guest OS Releases and SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md)(Engelstalig) voor meer informatie over ondersteunde versies van gast besturingssystemen.<br /><br /> Als u geen `osFamily` waarde opgeeft en u het kenmerk niet hebt ingesteld op `osVersion` een specifieke versie van het gast besturingssysteem, wordt een standaard waarde van 1 gebruikt.|
|osVersion|Optioneel. Hiermee geeft u de versie van het gast besturingssysteem op dat wordt uitgevoerd op rolinstanties in de Cloud service. Zie [Azure Guest OS releases en SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md)(Engelstalig) voor meer informatie over de versies van het gast besturingssysteem.<br /><br /> U kunt opgeven dat het gast besturingssysteem automatisch moet worden bijgewerkt naar de nieuwste versie. Als u dit wilt doen, stelt u de `osVersion` waarde van `*`het kenmerk in op. Als deze functie `*`is ingesteld op, worden de rolinstanties geïmplementeerd met de nieuwste versie van het gast besturingssysteem voor de opgegeven besturingssysteem familie en worden deze automatisch bijgewerkt wanneer nieuwe versies van het gast besturingssysteem worden vrijgegeven.<br /><br /> Als u een specifieke versie hand matig wilt opgeven `Configuration String` , gebruikt u de sectie uit de tabel in de **toekomst, huidig en voorlopig gast besturingssysteem versies** van [Azure Guest OS releases en SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).<br /><br /> De standaard waarde voor het `osVersion` kenmerk is `*`.|
|schemaVersion|Optioneel. Hiermee geeft u de versie van het service configuratie schema op. Met de schema versie kan Visual Studio de juiste SDK-hulpprogram ma's selecteren die voor schema validatie moeten worden gebruikt als meer dan één versie van de SDK naast elkaar is geïnstalleerd. Zie [Azure Guest OS Releases and SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md) (Engelstalig) voor meer informatie over schema-en versie compatibiliteit|

Het service configuratie bestand moet één `ServiceConfiguration` element bevatten. Het `ServiceConfiguration` element kan een wille keurig `Role` aantal elementen en nul of `NetworkConfiguration` 1 elementen bevatten.