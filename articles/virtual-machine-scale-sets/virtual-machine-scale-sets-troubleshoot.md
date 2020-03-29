---
title: Problemen met automatisch schalen oplossen met virtuele machineschaalsets
description: Problemen met automatisch schalen oplossen met virtuele machineschaalsets. Begrijp typische problemen die zich voordoen en hoe u deze oplossen.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: windows
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 923967a902f611ce845fbdc096fd2c02e681bb6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76272435"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Problemen met automatisch schalen met schaalsets voor virtuele machines oplossen
**Probleem** : u hebt een infrastructuur voor automatisch schalen gemaakt in Azure Resource Manager met behulp https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale van virtuele machineschaalsets , bijvoorbeeld door een sjabloon als deze te implementeren: - u hebt uw schaalregels gedefinieerd en het werkt geweldig, behalve hoeveel belasting u op de VM's plaatst, het wordt niet automatisch geschaald.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
Enkele dingen om te overwegen zijn:

* Hoeveel vCPU's heeft elke VM en laad je elke vCPU?
  De voorgaande sjabloon Azure Quickstart heeft een do_work.php-script, waarmee één vCPU wordt geladen. Als u een VM gebruikt die groter is dan een VM-grootte van één vCPU, zoals Standard_A1 of D1, moet u deze belasting meerdere keren uitvoeren. Controleren hoeveel vCPU's voor uw VM's door [grootte voor virtuele Windows-machines in Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) te bekijken
* Hoeveel VM's in de virtuele machine schaal set, bent u bezig met het werk op elk een?
  
    Een scale-outgebeurtenis vindt alleen plaats wanneer de gemiddelde CPU in **alle** VM's in een schaalset de drempelwaarde overschrijdt, gedurende de interne tijd die is gedefinieerd in de regels voor autoscalen.
* Heb je schaalevenementen gemist?
  
    Controleer de controlelogboeken in de Azure-portal op schaalgebeurtenissen. Misschien was er een schaal vergroting en een schaal naar beneden die werd gemist. U filteren op Schalen.
  
    ![Auditlogboeken][audit]
* Zijn uw schaal- en schaaldrempels voldoende verschillend?
  
    Stel dat u een regel instelt om uit te schalen wanneer de gemiddelde CPU groter is dan 50% over vijf minuten, en om te schalen wanneer de gemiddelde CPU minder dan 50% is. Deze instelling zou leiden tot een "flapping" probleem wanneer CPU-gebruik is dicht bij de drempel, met schaal acties voortdurend toenemende en het verminderen van de grootte van de set. Vanwege deze instelling probeert de autoscale-service te voorkomen dat "flapperen" wordt gebruikt, wat zich kan manifesteren als niet-schalen. Zorg er daarom voor dat uw scale-out- en scale-indrempels voldoende verschillend zijn om wat ruimte tussen schalen mogelijk te maken.
* Heb je je eigen JSON template geschreven?
  
    Het is gemakkelijk om fouten te maken, dus begin met een sjabloon zoals hierboven waarvan is bewezen dat het werkt, en breng kleine incrementele wijzigingen aan. 
* Kun je handmatig in- of uitschalen?
  
    Probeer de bron met een andere 'capaciteitsinstelling' opnieuw in te zetten om het aantal VM's handmatig te wijzigen. Een voorbeeldsjabloon is https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing hier: – u moet de sjabloon mogelijk bewerken om te kunnen weten of deze dezelfde machinegrootte heeft als uw schaalset. Als u het aantal VM's handmatig wijzigen, weet u dat het probleem is geïsoleerd om automatisch te schalen.
* Controleer uw Microsoft.Compute/virtualMachineScaleSet- en Microsoft.Insights-bronnen in de [Azure Resource Explorer](https://resources.azure.com/)
  
    De Azure Resource Explorer is een onmisbaar hulpprogramma voor probleemoplossing dat u de status van uw Azure Resource Manager-resources weergeeft. Klik op uw abonnement en bekijk de resourcegroep die u probleemoplossing biedt. Bekijk onder de compute resource provider de virtuele machineschaalset die u hebt gemaakt en controleer de instantieweergave, waarin de status van een implementatie wordt weergegeven. Controleer ook de instantieweergave van VM's in de virtuele machineschaalset. Ga vervolgens naar de Microsoft.Insights-bronprovider en controleer of de regels voor automatische schaal er goed uitzien.
* Werkt de diagnostische extensie en worden prestatiegegevens uitgestoten?
  
    **Update:** Azure autoscale is verbeterd om een host-gebaseerde metrische pijplijn te gebruiken, waarvoor niet langer een diagnostische extensie hoeft te worden geïnstalleerd. De volgende alinea's zijn niet meer van toepassing als u een toepassing voor automatisch schalen maakt met de nieuwe pijplijn. Een voorbeeld van Azure-sjablonen die zijn geconverteerd https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscaleom de hostpijplijn te gebruiken, is hier beschikbaar: . 
  
    Het gebruik van host-gebaseerde statistieken voor autoscale is beter om de volgende redenen:
  
  * Minder bewegende onderdelen omdat er geen diagnostische extensies hoeven te worden geïnstalleerd.
  * Eenvoudigere sjablonen. Voeg alleen regels voor automatisch schalen toe aan een bestaande sjabloon voor schaalsets.
  * Betrouwbaardere rapportage en snellere lancering van nieuwe VM's.
    
    De enige redenen waarom u een diagnostische extensie wilt blijven gebruiken, is als u rapportage/schaling voor geheugendiagnostiek nodig hebt. Statistieken op basis van host rapporteren geen geheugen.
    
    Met dat in gedachten, volg alleen de rest van dit artikel als u diagnostische extensies gebruikt voor uw automatisch schalen.
    
    Autoscale in Azure Resource Manager kan werken (maar hoeft niet meer) door middel van een VM-extensie genaamd de Diagnostics Extension. Het zendt prestatiegegevens uit aan een opslagaccount dat u definieert in de sjabloon. Deze gegevens worden vervolgens samengevoegd door de Azure Monitor-service.
    
    Als de Insights-service geen gegevens van de VM's kan lezen, moet deze u een e-mail sturen. U krijgt bijvoorbeeld een e-mail als de VM's zijn uitgeschakeld. Controleer uw e-mail op het e-mailadres dat u hebt opgegeven toen u uw Azure-account hebt gemaakt.
    
    U ook zelf naar de gegevens kijken. Bekijk het Azure-opslagaccount met behulp van een cloudexplorer. Meld u bijvoorbeeld aan met de [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2)en kies het Azure-abonnement dat u gebruikt. Kijk vervolgens naar de naam van het opslagaccount Diagnostics waarnaar wordt verwezen in de definitie van de extensie Diagnostische gegevens in uw implementatiesjabloon.
    
    ![Cloud Explorer][explorer]
    
    U ziet een aantal tabellen waarin de gegevens van elke virtuele machine worden opgeslagen. Als u Linux en de CPU-statistiek als voorbeeld neemt, kijkt u naar de meest recente rijen. De Visual Studio cloud explorer ondersteunt een querytaal, zodat u een query uitvoeren. U bijvoorbeeld een query uitvoeren voor 'Timestamp gt datetime'2016-02-02T21:20:00Z' om ervoor te zorgen dat u de meest recente gebeurtenissen krijgt. De tijdzone komt overeen met UTC. Komen de gegevens die u daar ziet overeen met de schaalregels die u hebt ingesteld? In het volgende voorbeeld, de CPU voor machine 20 begon te stijgen tot 100% in de afgelopen vijf minuten.
    
    ![Opslagtabellen][tables]
    
    Als de gegevens er niet zijn, impliceert dit dat het probleem is dat de diagnostische extensie in de VM's wordt uitgevoerd. Als de gegevens er zijn, houdt dit in dat er een probleem is met uw schaalregels of met de Insights-service. [Azure-status inteformeer](https://azure.microsoft.com/status/).
    
    Als u deze stappen hebt doorlopen en u nog steeds problemen hebt met automatisch schalen, u de volgende bronnen proberen: 
    * Lees de forums op [MSDN,](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows)of [Stack overflow](https://stackoverflow.com/questions/tagged/azure) 
    * Log een ondersteuningsgesprek in. Wees bereid om de sjabloon en een weergave van uw prestatiegegevens te delen.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
