---
title: Automatisch schalen in VM's, Cloud Services en Web Apps
description: Automatisch schalen in Microsoft Azure. Van toepassing op virtuele machines, virtuele machineschaalsets, cloudservices en webapps.
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: eeb8b301bf087efa164a7864cdce3a04952f45ed
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114142"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Overzicht van automatische schaalaanpassing in Microsoft Azure Virtual Machines, Cloud Services en Web Apps
In dit artikel wordt beschreven wat Microsoft Azure autoscale is, wat de voordelen ervan zijn en hoe u hiermee aan de slag.  

Azure Monitor autoscale is alleen van toepassing op [Virtual Machine Scale Sets,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [Cloud Services](https://azure.microsoft.com/services/cloud-services/), App Service - [Web Apps](https://azure.microsoft.com/services/app-service/web/)en API [Management services](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

> [!NOTE]
> Azure heeft twee methoden voor automatisch schalen. Een oudere versie van autoscale is van toepassing op virtuele machines (beschikbaarheidssets). Deze functie heeft beperkte ondersteuning en we raden u aan te migreren naar virtuele machineschaalsets voor snellere en betrouwbaardere ondersteuning voor automatisch schalen. Een link over het gebruik van de oudere technologie is opgenomen in dit artikel.  
>
>

## <a name="what-is-autoscale"></a>Wat is autoscale?
Met Autoscale u de juiste hoeveelheid resources uitvoeren om de belasting van uw toepassing af te handelen. Hiermee u resources toevoegen om de toename van de belasting aan te kunnen en ook geld besparen door resources te verwijderen die niet actief zijn. U geeft een minimum- en maximumaantal exemplaren op dat vm's automatisch moet uitvoeren en toevoegen of verwijderen op basis van een set regels. Het hebben van een minimum zorgt ervoor dat uw toepassing altijd wordt uitgevoerd, zelfs onder geen belasting. Het hebben van een maximale beperkt uw totale mogelijke uurkosten. U schaalt automatisch tussen deze twee uitersten met behulp van regels die u maakt.

 ![Autoscale uitgelegd. VM's toevoegen en verwijderen](./media/autoscale-overview/AutoscaleConcept.png)

Wanneer aan de regelvoorwaarden is voldaan, worden een of meer acties voor automatisch schalen geactiveerd. U VM's toevoegen en verwijderen of andere acties uitvoeren. Het volgende conceptuele diagram toont dit proces.  

 ![Stroomdiagram automatisch schalen](./media/autoscale-overview/Autoscale_Overview_v4.png)

De volgende uitleg is van toepassing op de stukken van het vorige diagram.   

## <a name="resource-metrics"></a>Metrische gegevens voor resource
Bronnen zenden statistieken uit, deze statistieken worden later verwerkt door regels. Metrics komen via verschillende methoden.
Virtuele machineschaalsets gebruiken telemetriegegevens van Azure-diagnostische agents, terwijl telemetrie voor web-apps en cloudservices rechtstreeks afkomstig zijn van de Azure-infrastructuur. Enkele veelgebruikte statistieken zijn CPU-gebruik, geheugengebruik, threadtellingen, wachtrijlengte en schijfgebruik. Zie [Algemene statistieken automatisch schalen](../../azure-monitor/platform/autoscale-common-metrics.md)voor een lijst met welke telemetriegegevens u gebruiken.

## <a name="custom-metrics"></a>Aangepaste statistieken
U ook gebruikmaken van uw eigen aangepaste statistieken die uw toepassing(en) mogelijk uitzendt. Als u uw toepassing(en) hebt geconfigureerd om statistieken naar Application Insights te verzenden, u deze statistieken gebruiken om beslissingen te nemen over het al dan niet schalen.

## <a name="time"></a>Time
Regels op basis van schema's zijn gebaseerd op UTC. U moet uw tijdzone goed instellen bij het instellen van uw regels.  

## <a name="rules"></a>Regels
Het diagram toont slechts één regel voor automatisch schalen, maar u er veel hebben. U complexe overlappende regels maken als dat nodig is voor uw situatie.  Regeltypen zijn o.a.  

* Op metrische basis - Doe deze actie bijvoorbeeld wanneer het **CPU-gebruik** boven de 50% ligt.
* **Time-based** - Bijvoorbeeld, trigger een webhook elke 8 uur op zaterdag in een bepaalde tijdzone.

Op metrische gegevens gebaseerde regels meten de belasting van toepassingen en voegen vm's toe of verwijder op basis van die belasting. Met regels op basis van schema's u schalen wanneer u tijdpatronen in uw belasting ziet en wilt schalen voordat er een mogelijke belastingsverhoging of -afname optreedt.  

## <a name="actions-and-automation"></a>Acties en automatisering
Regels kunnen een of meer soorten acties activeren.

* **Schaal** - VM's schalen in of uit
* **E-mail** - E-mail verzenden naar abonnementsbeheerders, medebeheerders en/of extra e-mailadres dat u opgeeft
* **Automatiseer via webhooks** - Call webhooks, die meerdere complexe acties binnen of buiten Azure kunnen activeren. Binnen Azure u een Azure Automation-runbook, Azure-functie of Azure Logic App starten. Een voorbeeld van URL van derden buiten Azure zijn services zoals Slack en Twilio.

## <a name="autoscale-settings"></a>Instellingen voor automatisch schalen
Autoscale gebruikt de volgende terminologie en structuur.

- Een **instelling voor automatisch schalen** wordt door de autoscale-engine gelezen om te bepalen of u wilt opschalen of omlaag wilt. Het bevat een of meer profielen, informatie over de doelbron en meldingsinstellingen.

  - Een **autoscale profiel** is een combinatie van:

    - **capaciteitsinstelling**, die de minimum-, maximum- en standaardwaarden voor het aantal exemplaren aangeeft.
    - **set regels**, die elk een trigger (tijd of metrische) en een schaalactie (omhoog of omlaag) bevatten.
    - **herhaling**, wat aangeeft wanneer autoscale dit profiel moet uitvoeren.

      U meerdere profielen hebben, waarmee u verschillende overlappende vereisten verzorgen. U bijvoorbeeld verschillende autoscaleprofielen hebben voor verschillende tijdstippen van de dag of dagen van de week.

  - Met een **meldingsinstelling** bepaalt u welke meldingen moeten optreden wanneer een gebeurtenis op automatische schaal plaatsvindt op basis van het voldoen aan de criteria van een van de profielen van de automatische schaalinstelling. Autoscale kan een of meer e-mailadressen op de hoogte stellen of bellen naar een of meer webhooks.


![Instelling, profiel en regelstructuur voor Azure-autoscale](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

De volledige lijst met configureerbare velden en beschrijvingen is beschikbaar in de [AUTOscale REST API.](https://msdn.microsoft.com/library/dn931928.aspx)

Zie voor codevoorbeelden

* [Geavanceerde autoscale-configuratie met Resource Manager-sjablonen voor VM-schaalsets](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [REST-API automatisch schalen](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Horizontaal versus verticaal schalen
Automatisch schalen schaalt alleen horizontaal, wat een toename is ("out") of een afname ("in") in het aantal VM-exemplaren.  Horizontaal is flexibeler in een cloudsituatie, omdat u hiermee mogelijk duizenden VM's uitvoeren om de belasting te verwerken.

Verticale schaling is daarentegen anders. Het houdt hetzelfde aantal VM's, maar maakt de VM's meer ("up") of minder ("down") krachtig. Power wordt gemeten in het geheugen, CPU-snelheid, schijfruimte, enz.  Verticale schaling heeft meer beperkingen. Het is afhankelijk van de beschikbaarheid van grotere hardware, die snel een bovengrens bereikt en per regio kan verschillen. Verticale schaling vereist meestal ook een VM om te stoppen en opnieuw op te starten.


## <a name="methods-of-access"></a>Toegangsmethoden
U automatisch schalen instellen via

* [Azure Portal](../../azure-monitor/platform/autoscale-get-started.md)
* [PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)
* [Platformonafhankelijke opdrachtregelinterface (CLI)](../../azure-monitor/platform/cli-samples.md#autoscale)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Ondersteunde services voor automatisch schalen
| Service | Schema & Documenten |
| --- | --- |
| Web Apps |[Web-apps schalen](../../azure-monitor/platform/autoscale-get-started.md) |
| Cloud Services |[Een cloudservice automatisch schalen](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtuele machines: Klassiek |[Beschikbaarheidssets voor klassieke virtuele machines schalen](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtuele machines: Windows-schaalsets |[Virtuele machineschaalsets schalen in Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Virtuele machines: Linux-schaalsets |[Virtuele machineschaalsets schalen in Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Virtuele machines: Windows-voorbeeld |[Geavanceerde autoscale-configuratie met Resource Manager-sjablonen voor VM-schaalsets](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| API-beheerservice|[Exemplaar van Azure API Management automatisch schalen](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over autoscale, gebruikt u de eerder vermelde Walkthroughs automatisch schalen of raadpleegt u de volgende bronnen:

* [Algemene statistieken voor Azure Monitor automatisch schalen](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Best practices voor automatische schaalaanpassing in Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Automatische schaalacties gebruiken om meldingen van e-mail en webhook te verzenden](../../azure-monitor/platform/autoscale-webhook-email.md)
* [REST-API automatisch schalen](https://msdn.microsoft.com/library/dn931953.aspx)
* [Problemen met virtuele machineschaalsets automatisch schalen oplossen](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

