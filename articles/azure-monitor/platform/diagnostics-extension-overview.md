---
title: Overzicht van de uitbrei ding Azure Diagnostics
description: Diagnostische gegevens van Azure gebruiken voor het opsporen van fouten, het meten van prestaties, bewaking, verkeers analyse in Cloud Services, virtuele machines en service Fabric
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: e0325a3bda912c95d8d27646bc1e80fff5ce10a8
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639432"
---
# <a name="what-is-azure-diagnostics-extension"></a>Wat is Azure Diagnostics extensie?
De uitbrei ding Azure Diagnostics is een agent in azure die het verzamelen van diagnostische gegevens op een geïmplementeerde toepassing mogelijk maakt. U kunt de diagnostische uitbrei ding van een aantal verschillende bronnen gebruiken. Dit wordt momenteel ondersteund: de web-en werk rollen van Azure Cloud service (klassiek), Virtual Machines, schaal sets voor virtuele machines en Service Fabric. Andere Azure-Services hebben verschillende diagnostische methoden. Zie [overzicht van bewaking in azure](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Linux-agent
Er is een [Linux-versie van de uitbrei ding](../../virtual-machines/extensions/diagnostics-linux.md) beschikbaar voor virtual machines Linux uitvoeren. De statistieken die worden verzameld en het gedrag kunnen verschillen van de Windows-versie.

## <a name="data-you-can-collect"></a>Gegevens die u kunt verzamelen
De uitbrei ding Azure Diagnostics kan de volgende typen gegevens verzamelen:

| Gegevensbron | Description |
| --- | --- |
| Metrische gegevens van prestatie meter item |Besturings systeem en aangepaste prestatie meter items |
| Toepassingslogboeken |Berichten traceren die zijn geschreven door uw toepassing |
| Windows-gebeurtenislogboeken |Gegevens die worden verzonden naar het Windows-systeem voor gebeurtenis logboek registratie |
| .NET Event source-logboeken |Code schrijven van gebeurtenissen met de .NET [Event source](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) -klasse |
| IIS-logboeken |Informatie over IIS-websites |
| [Op Manifest gebaseerde ETW-logboeken](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Event Tracing for Windows gebeurtenissen die door een proces worden gegenereerd. i |
| Crash dumps (Logboeken) |Informatie over de status van het proces als een toepassing vastloopt |
| Aangepaste foutenlogboeken |Logboeken die zijn gemaakt door uw toepassing of service |
| Logboeken met diagnostische Azure-infra structuur |Informatie over Azure Diagnostics zichzelf |

(1) als u een lijst met etw-providers wilt `c:\Windows\System32\logman.exe query providers` ophalen, voert u uit in een console venster op de computer waarvan u gegevens wilt verzamelen.

## <a name="data-storage"></a>Gegevensopslag
De gegevens worden opgeslagen in een [Azure Storage account](diagnostics-extension-to-storage.md) dat u opgeeft.

U kunt deze ook naar [Application Insights](../../azure-monitor/app/cloudservices.md)verzenden. 

Een andere optie is het streamen naar [Event hub](../../event-hubs/event-hubs-about.md), waarmee u het vervolgens kunt verzenden naar niet-Azure-bewakings Services.

U kunt uw gegevens ook verzenden naar Azure Monitor Data Base van de time-series. Op dit moment is deze Sink alleen van toepassing op prestatie meter items. Hiermee kunt u prestatie meter items verzenden in als aangepaste metrische gegevens. Deze functie is beschikbaar als preview-versie. De Azure Monitor Sink ondersteunt:
* Ophalen van alle prestatie meter items die zijn verzonden naar Azure Monitor via de [API voor Azure monitor metrische gegevens.](https://docs.microsoft.com/rest/api/monitor/)
* Er wordt een waarschuwing gegeven voor alle prestatie meter items die naar Azure Monitor worden verzonden via de [metrische waarschuwingen](../../azure-monitor/platform/alerts-overview.md) in azure monitor
* De operator wild behandelen in prestatie meter items als de dimensie ' instance ' op uw metrische waarde.  Als u bijvoorbeeld het prestatie meter item ' logische\*schijf ()/DiskWrites/sec ' hebt verzameld, kunt u op de dimensie ' instance ' filteren en splitsen om de schrijf bewerkingen per seconde voor elke logische schijf op de virtuele machine (bijvoorbeeld C:) te maken.

Raadpleeg de documentatie van het [Azure Diagnostics-schema](diagnostics-extension-schema-1dot3.md) voor meer informatie over het configureren van deze sink.

## <a name="costs"></a>Kosten
Met elk van de bovenstaande opties kunnen kosten in rekening worden gebracht. Zorg ervoor dat u ze onderzoekt om onverwachte facturen te voor komen.  Application Insights, Event hub en Azure Storage hebben afzonderlijke kosten die zijn gekoppeld aan opname en de opgeslagen tijd. In het bijzonder houdt Azure Storage altijd alle gegevens over, zodat u na een bepaalde periode oudere gegevens kunt verwijderen om uw kosten lager te houden.    

## <a name="versioning-and-configuration-schema"></a>Schema voor versie beheer en configuratie
Zie [Azure Diagnostics-versie geschiedenis en-schema](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Volgende stappen
Kies de service die u wilt gebruiken voor het verzamelen van diagnostische gegevens en gebruik de volgende artikelen om aan de slag te gaan. Gebruik de algemene Azure diagnostische koppelingen voor naslag informatie voor specifieke taken.

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services Azure Diagnostics gebruiken
* Als u Visual Studio gebruikt, raadpleegt u [Visual Studio gebruiken om een Cloud Services toepassing](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) te traceren om aan de slag te gaan. Anders gaat u naar
* [Cloud Services controleren met behulp van Azure Diagnostics](../../cloud-services/cloud-services-how-to-monitor.md)
* [Azure Diagnostics instellen in een Cloud Services toepassing](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Zie voor meer geavanceerde onderwerpen

* [Azure Diagnostics gebruiken met Application Insights voor Cloud Services](../../azure-monitor/app/cloudservices.md)
* [De stroom van een Cloud Services toepassing traceren met Azure Diagnostics](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Power shell gebruiken om diagnostische gegevens in te stellen voor Cloud Services](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtuele machines
* Als u Visual Studio gebruikt, raadpleegt u [Visual Studio gebruiken om Azure virtual machines](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) te traceren om aan de slag te gaan. Anders gaat u naar
* [Azure Diagnostics instellen op een virtuele machine van Azure](/azure/virtual-machines/extensions/diagnostics-windows)

Zie voor meer geavanceerde onderwerpen

* [Power shell gebruiken om diagnostische gegevens in te stellen op Azure Virtual Machines](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Een virtuele Windows-machine met controle en diagnostische gegevens maken met behulp van Azure Resource Manager sjabloon](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Ga aan de slag om [een service Fabric toepassing](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)te bewaken. Er zijn veel andere Service Fabric diagnostische artikelen beschikbaar in de navigatie structuur aan de linkerkant zodra u dit artikel ontvangt.

## <a name="general-articles"></a>Algemene artikelen
* Meer informatie over het [gebruik van prestatie meter items in azure Diagnostics](../../cloud-services/diagnostics-performance-counters.md).
* Zie [problemen oplossen Azure Diagnostics](diagnostics-extension-troubleshooting.md) als u problemen ondervindt met het starten of vinden van uw gegevens in azure Storage-tabellen

