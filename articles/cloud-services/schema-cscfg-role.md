---
title: Functieschema Azure Cloud Services | Microsoft Documenten
description: Het functie-element van een serviceconfiguratiebestand geeft aan hoeveel rolinstanties moeten worden geïmplementeerd voor elke rol, configuratiewaarden en duimafdrukken van certificaten.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: b64f9d27e382a39b132593502fed32c565af473a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528418"
---
# <a name="azure-cloud-services-config-role-schema"></a>Azure Cloud Services Config-rolschema

Het `Role` element van het configuratiebestand geeft het aantal rolinstanties op dat moet worden geïmplementeerd voor elke rol in de service, de waarden van alle configuratie-instellingen en de duimafdrukken voor certificaten die aan een rol zijn gekoppeld.

Zie [Cloud Service (klassiek) configuratieschema voor](schema-cscfg-file.md)meer informatie over het Azure Service Configuration Schema. Zie [Cloud Service (klassiek) definitieschema voor](schema-csdef-file.md)meer informatie over het Azure Service Definition-schema .

##  <a name="role-element"></a><a name="Role"></a>Rolelement
In het volgende `Role` voorbeeld worden het element en de onderliggende elementen weergegeven.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

In de volgende tabel worden `Role` de kenmerken voor het element beschreven.

| Kenmerk | Beschrijving |
| --------- | ----------- |
| name   | Vereist. Hiermee geeft u de naam van de rol op. De naam moet overeenkomen met de naam die is opgegeven voor de rol in het servicedefinitiebestand.|
| vmName | Optioneel. Hiermee geeft u de DNS-naam voor een virtuele machine op. De naam moet 10 tekens of minder zijn.|

In de volgende tabel worden `Role` de onderliggende elementen van het element beschreven.

| Element | Beschrijving |
| ------- | ----------- |
| exemplaren | Vereist. Hiermee geeft u het aantal exemplaren op dat moet worden geïmplementeerd voor de rol. Het aantal instanties wordt gedefinieerd door `count` een geheel getal voor het kenmerk.|
| Instelling   | Optioneel. Hiermee geeft u een instellingsnaam en waarde op in een verzameling instellingen voor een rol. De instellingsnaam wordt gedefinieerd door `name` een tekenreeks voor het kenmerk en `value` de instellingswaarde wordt gedefinieerd door een tekenreeks voor het kenmerk.|
| Certificaat | Optioneel. Hiermee geeft u de naam, duimafdruk en het algoritme op van een servicecertificaat dat aan de rol moet worden gekoppeld. De certificaatnaam wordt gedefinieerd door `name` een tekenreeks voor het kenmerk. De duimafdruk van het certificaat wordt gedefinieerd door een tekenreeks `thumbprint` van hexadecimale getallen die geen spaties voor het kenmerk bevatten. De hexadecimale getallen moeten worden weergegeven met cijfers en alfatekens in hoofdletters. Het certificaatalgoritme wordt gedefinieerd door `thumbprintAlgorithm` een tekenreeks voor het kenmerk.|

## <a name="see-also"></a>Zie ook
[Cloud Service (klassiek) configuratieschema](schema-cscfg-file.md)