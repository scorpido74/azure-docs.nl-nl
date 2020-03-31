---
title: Een Azure Cloud Service controleren | Microsoft Documenten
description: Beschrijft wat het bewaken van een Azure Cloud Service inhoudt en wat sommige van uw opties zijn.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 01/29/2018
ms.author: tagore
ms.openlocfilehash: 61c794ba03934ae1828ba310f3f776bfb61b652b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273096"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Inleiding tot Cloud Service Monitoring

U belangrijke prestatiestatistieken voor elke cloudservice controleren. Elke cloudservicerol verzamelt minimale gegevens: CPU-gebruik, netwerkgebruik en schijfgebruik. Als de cloudservice `Microsoft.Azure.Diagnostics` de extensie heeft toegepast op een rol, kan die rol extra gegevenspunten verzamelen. In dit artikel vindt u een inleiding tot Azure Diagnostics for Cloud Services.

Met basisbewaking worden prestatiemetergegevens van rolinstanties bemonsterd en verzameld met intervallen van 3 minuten. Deze basiscontrolegegevens worden niet opgeslagen in uw opslagaccount en hebben geen extra kosten.

Met geavanceerde monitoring worden aanvullende statistieken bemonsterd en verzameld met intervallen van 5 minuten, 1 uur en 12 uur. De geaggregeerde gegevens worden opgeslagen in een opslagaccount, in tabellen, en worden na 10 dagen verwijderd. Het gebruikte opslagaccount is geconfigureerd op rol; u verschillende opslagaccounts gebruiken voor verschillende rollen. Dit is geconfigureerd met een verbindingstekenreeks in de [.csdef-](cloud-services-model-and-package.md#servicedefinitioncsdef) en [.cscfg-bestanden.](cloud-services-model-and-package.md#serviceconfigurationcscfg)


## <a name="basic-monitoring"></a>Basisbewaking

Zoals vermeld in de inleiding, verzamelt een cloudservice automatisch basisbewakingsgegevens van de virtuele hostmachine. Deze gegevens omvatten CPU-percentage, netwerk in/uit en schijflezen/schrijven. De verzamelde bewakingsgegevens worden automatisch weergegeven op de overzichts- en metrische gegevenspagina's van de cloudservice in de Azure-portal. 

Voor basisbewaking is geen opslagaccount vereist. 

![basisnetwerktegels voor het bewaken van cloudservices](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Geavanceerde controle

Geavanceerde bewaking omvat het gebruik van de **Azure Diagnostics-extensie** (en optioneel de Application Insights SDK) voor de rol die u wilt controleren. De diagnostische extensie maakt gebruik van een config-bestand (per rol) genaamd **diagnostics.wadcfgx** om de diagnostische statistieken te configureren die worden gecontroleerd. De Azure Diagnostic-extensie verzamelt en slaat gegevens op in een Azure Storage-account. Deze instellingen zijn geconfigureerd in de **.wadcfgx-** en [.csdef-](cloud-services-model-and-package.md#servicedefinitioncsdef)en [.cscfg-bestanden.](cloud-services-model-and-package.md#serviceconfigurationcscfg) Dit betekent dat er extra kosten verbonden zijn aan geavanceerde monitoring.

Als elke rol wordt gemaakt, voegt Visual Studio de Azure Diagnostics-extensie toe. Deze diagnostische extensie kan de volgende soorten informatie verzamelen:

* Aangepaste prestatiemeteritems
* Toepassingslogboeken
* Windows-gebeurtenislogboeken
* .NET-gebeurtenisbron
* IIS-logboeken
* ETW op basis van manifest
* Crashdumps
* Logboeken met klantfouten

> [!IMPORTANT]
> Hoewel al deze gegevens worden samengevoegd in het opslagaccount, **biedt** de portal geen native manier om de gegevens in kaart te brengen. Het is ten zeerste aan te raden om een andere service, zoals Application Insights, in uw toepassing te integreren.

## <a name="setup-diagnostics-extension"></a>Extensie voor diagnostische gegevens instellen

Ten eerste, als u geen **klassiek** opslagaccount hebt, [maakt u er een.](../storage/common/storage-account-create.md) Controleer of het opslagaccount is gemaakt met het **opgegeven implementatiemodel Classic.**

Navigeer vervolgens naar de bron van het **opslagaccount (klassieke)** bron. Selecteer De sleutels van **Instellingentoegang** > **Access keys** en kopieer de waarde van de tekenreeks **Primaire verbinding.** U hebt deze waarde nodig voor de cloudservice. 

Er zijn twee config-bestanden die u moet wijzigen om geavanceerde diagnose in te schakelen, **ServiceDefinition.csdef** en **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

Voeg in het bestand **ServiceDefinition.csdef** `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` een nieuwe instelling toe die is vernoemd naar elke rol die geavanceerde diagnoses gebruikt. Visual Studio voegt deze waarde toe aan het bestand wanneer u een nieuw project maakt. In het geval dat het ontbreekt, u het nu toevoegen. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Hiermee wordt een nieuwe instelling gedefinieerd die aan elk **Bestand ServiceConfiguration.cscfg** moet worden toegevoegd. 

Hoogstwaarschijnlijk hebt u twee **.cscfg-bestanden,** een met de naam **ServiceConfiguration.cloud.cscfg** voor implementatie naar Azure en een met de naam **ServiceConfiguration.local.cscfg** die wordt gebruikt voor lokale implementaties in de geëmuleerde omgeving. Open en wijzig elk **.cscfg-bestand.** Voeg een `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`instelling met de naam toe . Stel de waarde in op de **tekenreeks Primaire verbinding** van het klassieke opslagaccount. Als u de lokale opslag op uw `UseDevelopmentStorage=true`ontwikkelingsmachine wilt gebruiken, gebruikt u .

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Toepassingsinzichten gebruiken

Wanneer u de Cloud Service van Visual Studio publiceert, krijgt u de mogelijkheid om de diagnostische gegevens naar Application Insights te sturen. U de Azure-bron Application Insights op dat moment maken of de gegevens naar een bestaande Azure-bron verzenden. Uw cloudservice kan worden bewaakt door Application Insights voor beschikbaarheid, prestaties, storingen en gebruik. Aangepaste grafieken kunnen worden toegevoegd aan Application Insights, zodat u de gegevens zien die het belangrijkst zijn. Rolinstantiegegevens kunnen worden verzameld met behulp van de Application Insights SDK in uw cloudserviceproject. Zie [Application Insights met Cloud Services](../azure-monitor/app/cloudservices.md)voor meer informatie over het integreren van Application Insights.

Houd er rekening mee dat u application insights gebruiken om de prestatiemeteritems (en de andere instellingen) weer te geven die u hebt opgegeven via de Windows Azure Diagnostics-extensie, maar dat u alleen een rijkere ervaring krijgt door de Application Insights SDK in uw werknemer en webrollen.


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over applicatieinzichten met Cloud Services](../azure-monitor/app/cloudservices.md)
- [Prestatiemeteritems instellen](diagnostics-performance-counters.md)




