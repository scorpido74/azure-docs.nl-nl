---
title: De stroom in cloudservicestoepassing traceren met Azure Diagnostics
titleSuffix: Azure Cloud Services
description: Voeg traceringsberichten toe aan een Azure-toepassing om foutopsporing, het meten van prestaties, monitoring, verkeersanalyse en meer te helpen.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: tagore
ms.openlocfilehash: 47a33ba27dd6d2df626d93695c421303bace6a0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386507"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>De stroom van een Cloud Services-toepassing traceren met Azure Diagnostics
Tracering is een manier voor u om de uitvoering van uw toepassing te controleren terwijl deze wordt uitgevoerd. U de klassen [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace), [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug)en [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) gebruiken om informatie over fouten en uitvoering van toepassingen op te nemen in logboeken, tekstbestanden of andere apparaten voor latere analyse. Zie [Tracering en instrumenting applications](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications)voor meer informatie over tracering.

## <a name="use-trace-statements-and-trace-switches"></a>Tracestatements en traceswitches gebruiken
Implementeer tracering in uw Cloud Services-toepassing door de [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) toe te voegen aan de toepassingsconfiguratie en te bellen naar System.Diagnostics.Trace of System.Diagnostics.Debug in uw toepassingscode. Gebruik het configuratiebestand *app.config* voor werknemersrollen en *web.config* voor webrollen. Wanneer u een nieuwe gehoste service maakt met behulp van een Visual Studio-sjabloon, wordt Azure Diagnostics automatisch aan het project toegevoegd en wordt het DiagnosticMonitorTraceListener toegevoegd aan het juiste configuratiebestand voor de rollen die u toevoegt.

Zie [Tracestatements toevoegen aan toepassingscode](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)voor informatie over het plaatsen van traceringsinstructies.

Door [Trace Switches](/dotnet/framework/debug-trace-profile/trace-switches) in uw code te plaatsen, u bepalen of tracering plaatsvindt en hoe uitgebreid deze is. Hiermee u de status van uw toepassing in een productieomgeving controleren. Dit is vooral belangrijk in een bedrijfstoepassing die meerdere componenten gebruikt die op meerdere computers worden uitgevoerd. Zie [Traceswitches configureren voor](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches)meer informatie.

## <a name="configure-the-trace-listener-in-an-azure-application"></a>De traceringslistener in een Azure-toepassing configureren
Traceer, Debug en TraceSource, vereisen dat u het instellen van "luisteraars" om te verzamelen en opnemen van de berichten die worden verzonden. Luisteraars verzamelen, slaan en route-tracering berichten. Ze leiden de traceringsuitvoer naar een geschikt doel, zoals een logboek, venster of tekstbestand. Azure Diagnostics maakt gebruik van de klasse [DiagnosticMonitorTraceListener.](/previous-versions/azure/reference/ee758610(v=azure.100))

Voordat u de volgende procedure voltooit, moet u de Azure-diagnostische monitor initialiseren. Zie [Diagnostische gegevens inschakelen in Microsoft Azure](cloud-services-dotnet-diagnostics.md)om dit te doen.

Houd er rekening mee dat als u de sjablonen gebruikt die door Visual Studio worden geleverd, de configuratie van de listener automatisch voor u wordt toegevoegd.

### <a name="add-a-trace-listener"></a>Een traceringslistener toevoegen
1. Open het web.config- of app.config-bestand voor uw rol.
2. Voeg de volgende code toe aan het bestand. Wijzig het kenmerk Versie om het versienummer van de assemblage te gebruiken waarnaar u verwijst. De assemblageversie verandert niet noodzakelijkerwijs bij elke Azure SDK-release, tenzij er updates aan worden gegeven.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Zorg ervoor dat u een projectverwijzing hebt naar de microsoft.WindowsAzure.Diagnostics-assemblage. Werk het versienummer in de xml hierboven bij aan de versie van de microsoft.WindowsAzure.Diagnostics-assemblage waarnaar wordt verwezen.
   > 
   > 
3. Sla het config-bestand op.

Zie [Luisteraars traceren voor](/dotnet/framework/debug-trace-profile/trace-listeners)meer informatie over luisteraars .

Nadat u de stappen hebt voltooid om de listener toe te voegen, u traceringsinstructies aan uw code toevoegen.

### <a name="to-add-trace-statement-to-your-code"></a>Trace-instructie toevoegen aan uw code
1. Open een bronbestand voor uw toepassing. Het \<bestand RoleName>.cs voor de werkrol of webrol.
2. Voeg de volgende gebruiksrichtlijn toe als deze nog niet is toegevoegd:
    ```
        using System.Diagnostics;
    ```
3. Voeg Traceringsinstructies toe waarin u informatie wilt vastleggen over de status van uw toepassing. U verschillende methoden gebruiken om de uitvoer van de instructie Trace op te maken. Zie [Traceerverklaringen toevoegen aan toepassingscode voor](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)meer informatie.
4. Sla het bronbestand op.




