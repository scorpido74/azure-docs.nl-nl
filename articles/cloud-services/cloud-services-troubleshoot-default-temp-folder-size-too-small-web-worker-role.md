---
title: Standaard TEMP-mapgrootte is te klein voor een rol | Microsoft Documenten
description: Een cloudservicerol heeft een beperkte hoeveelheid ruimte voor de MAP TEMP. Dit artikel geeft een aantal suggesties over hoe te voorkomen dat opraken van de ruimte.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 0b869b73a79872d9263058bedfead018e18721c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154985"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Standaard temp-mapgrootte is te klein voor een web/worker-rol van een cloudservice
De standaardtijdelijke map van een cloudservicemedewerker of webrol heeft een maximale grootte van 100 MB, die op een gegeven moment vol kan worden. In dit artikel wordt beschreven hoe u voorkomen dat er geen ruimte meer is voor de tijdelijke map.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Waarom heb ik geen ruimte meer?
De standaard Windows-omgevingsvariabelen TEMP en TMP zijn beschikbaar voor code die in uw toepassing wordt uitgevoerd. Zowel TEMP als TMP wijzen naar een enkele map met een maximale grootte van 100 MB. Gegevens die in deze map zijn opgeslagen, blijven niet bestaan gedurende de levenscyclus van de cloudservice. als de rolexemplaren in een cloudservice worden gerecycled, wordt de map schoongemaakt.

## <a name="suggestion-to-fix-the-problem"></a>Suggestie om het probleem op te lossen
Implementeer een van de volgende alternatieven:

* Configureer een lokale opslagbron en krijg rechtstreeks toegang tot deze resource in plaats van TEMP of TMP te gebruiken. Als u toegang wilt krijgen tot een lokale opslagbron van code die binnen uw toepassing wordt uitgevoerd, roept u de methode [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) aan.
* Configureer een lokale opslagbron en wijs de TEMP- en TMP-mappen aan om het pad van de lokale opslagbron aan te wijzen. Deze wijziging moet worden uitgevoerd binnen de methode [RoleEntryPoint.OnStart.](/previous-versions/azure/reference/ee772851(v=azure.100))

In het volgende codevoorbeeld ziet u hoe u de doelmappen voor TEMP en TMP wijzigen vanuit de Methode OnStart:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Lees een blog waarin wordt beschreven [hoe u de grootte van de Azure-webrol ASP.NET tijdelijke map vergroten.](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx)

Bekijk meer [artikelen over probleemoplossing](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) voor cloudservices.

Bekijk de [blogreeks van Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)voor meer informatie over het oplossen van problemen met de functieproblemen met de cloudservice met behulp van azure PaaS-computerdiagnosegegevens.
