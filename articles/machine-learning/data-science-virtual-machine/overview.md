---
title: Wat is Azure Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: Belangrijke analysescenario's en componenten voor Windows en Linux Data Science Virtual Machines.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 12/31/2019
ms.openlocfilehash: ea53ee3f3c1d39652982b6343b1e634dac1fbc75
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360610"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Wat is de Azure-Data Science Virtual Machine voor Linux en Windows?

De Data Science Virtual Machine (DSVM) is een aangepaste VM-installatie kopie op het Azure-Cloud platform dat speciaal is gebouwd voor het uitvoeren van data Science. Er zijn veel populaire hulpprogram ma's voor gegevens wetenschap vooraf geïnstalleerd en vooraf geconfigureerd om intelligente toepassingen voor geavanceerde analyse te bouwen. 

De DSVM is beschikbaar op:
+ **Windows Server 2019 (preview-versie)**
+ **Ubuntu 18,04 LTS (preview-versie)**
+ Windows Server 2016
+ Ubuntu 16,04 LTS en CentOS 7,4


> [!NOTE]
> Alle VM-hulpprogram ma's voor uitgebreid leren zijn gevouwen in de Data Science Virtual Machine. 


## <a name="why-choose-the-dsvm"></a>Waarom kiest u de DSVM?
Het doel van de Data Science Virtual Machine is om gegevens specialisten van alle vaardigheids niveaus en allerlei branches te voorzien van een beschik bare, vooraf geconfigureerde data Science-omgeving met frictie. In plaats van zelf een vergelijk bare werk ruimte te implementeren, kunt u een DSVM inrichten. Met deze optie kunt u dagen of zelfs _weken_ besparen op de installatie-, configuratie-en pakket beheer processen. Nadat uw DSVM is toegewezen, kunt u meteen aan uw datatechnologieproject gaan werken.

## <a name="sample-use-cases"></a>Voorbeeld Cases gebruiken

Hieronder ziet u enkele veelvoorkomende gebruiks cases voor DSVM-klanten.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Data Science-workloads verplaatsen naar de Cloud

De DSVM biedt een basislijn configuratie voor data Science-teams die hun lokale Desk tops willen vervangen door een beheerde Cloud Desktop, zodat alle gegevens wetenschappers van een team een consistente installatie hebben waarmee experimenten kunnen worden gecontroleerd en de samen werking kan worden bevorderd. De kosten worden ook verlaagd door de overhead van sysadmin te verminderen. Deze last reductie bespaart op de tijd die nodig is om software pakketten voor geavanceerde analyse te evalueren, te installeren en te onderhouden.

### <a name="data-science-training-and-education"></a>Training en onderwijs voor datatechnologie
Enter prise trainers en docenten die data Science branches leren, hebben meestal een installatie kopie van een virtuele machine. De installatie kopie zorgt ervoor dat studenten een consistente installatie hebben en dat de voor beelden werken zoals verwacht. 

De DSVM maakt een omgeving op aanvraag met een consistente installatie die de uitdagingen voor de ondersteuning en incompatibiliteit vereenvoudigt. In gevallen waarin deze omgevingen vaak moeten worden gemaakt, met name voor kortere trainingen, levert dit veel voordelen op.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Flexibele capaciteit op aanvraag voor grootschalige projecten
Data Science hackathons trappen/wedstrijden of grootschalige gegevens modellering en-exploratie vereisen geschaalde hardware-capaciteit, meestal voor korte duur. De DSVM kan helpen de data Science-omgeving snel op aanvraag te repliceren, op scale-out servers die experimenten toestaan dat IT-bronnen met hoge prestaties kunnen worden uitgevoerd.

### <a name="custom-compute-power-for-azure-notebooks"></a>Aangepaste reken kracht voor Azure Notebooks
[Azure notebooks](../../notebooks/azure-notebooks-overview.md) is een gratis gehoste service voor het ontwikkelen, uitvoeren en delen van Jupyter-notebooks in de Cloud zonder installatie. De gratis service laag is beperkt tot 4 GB aan geheugen en 1 GB aan gegevens. 

Als u alle limieten wilt vrijgeven, kunt u een notitieblok project koppelen aan een DSVM of een andere virtuele machine die wordt uitgevoerd op een Jupyter-server. Als u zich aanmeldt bij Azure Notebooks met een account met behulp van Azure Active Directory (zoals een bedrijfs account), worden in notitie blokken automatisch Dsvm weer gegeven in de abonnementen die aan dat account zijn gekoppeld. U kunt [een DSVM aan Azure notebooks koppelen](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) om de beschik bare reken kracht uit te breiden.

### <a name="short-term-experimentation-and-evaluation"></a>Experimenten en evaluatie op korte termijn
U kunt de DSVM gebruiken om nieuwe [hulp middelen](./tools-included.md)voor gegevens wetenschap te evalueren of te leren, met name door een aantal gepubliceerde voor [beelden en scenario's](./dsvm-samples-and-walkthroughs.md)te door lopen.


### <a name="deep-learning-with-gpus"></a>Dieper leren met Gpu's
In de DSVM kunnen uw trainings modellen diepe leer algoritmen gebruiken op hardware die is gebaseerd op Gpu's (graphics processing units). Door gebruik te maken van de VM-schaal mogelijkheden van het Azure-platform, helpt de DSVM u bij het gebruik van op GPU gebaseerde hardware in de Cloud op basis van uw behoeften. U kunt overschakelen naar een op GPU gebaseerde VM wanneer u grote modellen bijwerkt of wanneer u hoge snelheids berekeningen nodig hebt terwijl dezelfde besturingssysteem schijf wordt bewaard. U kunt een van de virtuele machines van de N-serie GPU die met DSVM zijn ingeschakeld, kiezen. Houd er rekening mee dat gratis Azure-accounts voor GPU ingeschakelde Sku's voor virtuele machines niet ondersteunen.

De Windows Server 2016-versie van de DSVM wordt vooraf geïnstalleerd met GPU-Stuur Programma's, frameworks en GPU-versies van diepe leer raamwerken. Op de Linux-editie is dieper leren op Gpu's ingeschakeld op zowel de CentOS-als Ubuntu-Dsvm. 

U kunt ook de Ubuntu-, CentOS-of Windows 2016-versie van de DSVM implementeren op een virtuele machine van Azure die niet is gebaseerd op Gpu's. In dit geval worden alle diepe leer raamwerken terugvallen op de CPU-modus.
 
Meer [informatie over beschik bare diep gaande lessen en AI-Frameworks](dsvm-tools-deep-learning-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Wat is opgenomen in de DSVM?

Bekijk [hier](tools-included.md)een volledige lijst met hulpprogram ma's voor de Windows-en Linux-DSVM.

## <a name="next-steps"></a>Volgende stappen

Meer informatie vindt u in deze artikelen:

+ Windows:
  + [Een Windows-DSVM instellen](provision-vm.md)
  + [Tien dingen die u kunt doen op een Windows-DSVM](vm-do-ten-things.md)

+ Linux:
  + [Een Linux-DSVM instellen (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Een Linux-DSVM instellen (CentOS)](linux-dsvm-intro.md)
  + [Data wetenschappen op een Linux-DSVM](linux-dsvm-walkthrough.md)
