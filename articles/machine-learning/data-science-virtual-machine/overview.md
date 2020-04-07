---
title: Wat is de Azure Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: Overzicht van Azure Data Science Virtual Machine - Een eenvoudig te maken en te gebruiken virtuele machine op het Azure-cloudplatform met vooraf geïnstalleerde en geconfigureerde tools en bibliotheken voor het doen van data science en het ontwikkelen van intelligente toepassingen.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 03bfee258fe96d90c32b6a305b99856a11d9a087
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754977"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Wat is de Azure Data Science Virtual Machine voor Linux en Windows?

De Data Science Virtual Machine (DSVM) is een aangepaste VM-afbeelding op het Azure-cloudplatform dat speciaal is gebouwd voor het doen van data science. Het heeft veel populaire data science-tools vooraf geïnstalleerd en vooraf geconfigureerd om te jumpstart bouwen van intelligente toepassingen voor geavanceerde analytics. 

De DSVM is beschikbaar op:

+ **Windows Server 2019**
+ **Ubuntu 18.04 LTS**
+ Windows Server 2016
+ Ubuntu 16.04 LTS

> [!NOTE]
> Alle VM-tools voor deep learning zijn opgevouwen in de Data Science Virtual Machine. 

## <a name="why-choose-the-dsvm"></a>Waarom kiezen voor de DSVM?

Het doel van de Data Science Virtual Machine is om dataprofessionals van alle niveaus en in verschillende sectoren te voorzien van een frictievrije, vooraf geconfigureerde data science-omgeving. In plaats van zelf een vergelijkbare werkruimte uit te rollen, u een DSVM inrichten. Deze keuze kan u dagen of zelfs _weken_ besparen op de installatie-, configuratie- en pakketbeheerprocessen. Nadat uw DSVM is toegewezen, kunt u meteen aan uw datatechnologieproject gaan werken.

## <a name="sample-use-cases"></a>Voorbeeldgebruiksaanvragen

Hieronder illustreren we enkele veelvoorkomende use cases voor DSVM-klanten.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Data science-workloads verplaatsen naar de cloud

De DSVM biedt een basislijnconfiguratie voor data science-teams die hun lokale desktops willen vervangen door een beheerde clouddesktop, zodat alle gegevenswetenschappers in een team een consistente installatie hebben om experimenten te verifiëren en samenwerking te bevorderen. Het verlaagt ook de kosten door het verminderen van de sysadmin last. Deze lastenreductie bespaart op de tijd die nodig is om softwarepakketten voor geavanceerde analyses te evalueren, installeren en onderhouden.

### <a name="data-science-training-and-education"></a>Training en onderwijs voor datatechnologie

Enterprise trainers en opvoeders die les geven in data science klassen bieden meestal een virtuele machine beeld. De afbeelding zorgt ervoor dat studenten een consistente instelling hebben en dat de monsters voorspelbaar werken. 

De DSVM creëert een on-demand omgeving met een consistente setup die de uitdagingen voor ondersteuning en onverenigbaarheid verlicht. In gevallen waarin deze omgevingen vaak moeten worden gemaakt, met name voor kortere trainingen, levert dit veel voordelen op.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Flexibele capaciteit op aanvraag voor grootschalige projecten

Data science hackathons/competities of grootschalige datamodellering en -exploratie vereisen geschaalde hardwarecapaciteit, meestal voor korte duur. De DSVM kan helpen de data science-omgeving snel op aanvraag te repliceren, op geschaalde servers waarmee experimenten kunnen worden uitgevoerd die krachtige computerbronnen kunnen uitvoeren.

### <a name="custom-compute-power-for-azure-notebooks"></a>Aangepaste rekenkracht voor Azure-laptops

[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) is een gratis gehoste service voor het ontwikkelen, uitvoeren en delen van Jupyter-notitieblokken in de cloud zonder installatie. De gratis servicelaag is beperkt tot 4 GB geheugen en 1 GB aan gegevens. 

Als u alle limieten wilt vrijgeven, u een project Notitieblokken koppelen aan een DSVM of een andere VM die op een Jupyter-server wordt uitgevoerd. Als u zich aanmeldt bij Azure Notebooks met een account met Azure Active Directory (zoals een bedrijfsaccount), worden in Notitieblokken automatisch DSVM's weergegeven in abonnementen die aan dat account zijn gekoppeld. U [een DSVM koppelen aan Azure-notitieblokken](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) om de beschikbare rekenkracht uit te breiden.

### <a name="short-term-experimentation-and-evaluation"></a>Experimenten en evaluatie op korte termijn

U de DSVM gebruiken om nieuwe data [science-tools](./tools-included.md)te evalueren of te leren, vooral door enkele van onze gepubliceerde [voorbeelden en walkthroughs te](./dsvm-samples-and-walkthroughs.md)doorlopen.


### <a name="deep-learning-with-gpus"></a>Deep learning met GPU's

In de DSVM kunnen uw trainingsmodellen deep learning-algoritmen gebruiken op hardware die is gebaseerd op grafische verwerkingseenheden (GPU's). Door gebruik te maken van de VM-schalingsmogelijkheden van het Azure-platform, helpt de DSVM u gpu-gebaseerde hardware in de cloud te gebruiken volgens uw behoeften. U overschakelen naar een VM op basis van GPU wanneer u grote modellen traint of wanneer u snelle berekeningen nodig hebt terwijl u dezelfde schijf van het besturingssysteem houdt. U kiezen uit een van de N-serie GPU ingeschakeld virtuele machine SKU's met DSVM. Houd er rekening mee dat gratis Azure-accounts geen ONDERSTEUNING bieden voor GPU-enabled virtual machine SKU's.

De Windows-edities van de DSVM worden vooraf geïnstalleerd met GPU-stuurprogramma's, frameworks en GPU-versies van deep learning-frameworks. Op de Linux-editie is deep learning op GPU's ingeschakeld op de Ubuntu DSVMs. 

U de Ubuntu- of Windows-edities van de DSVM ook implementeren op een virtuele Azure-machine die niet is gebaseerd op GPU's. In dit geval zullen alle deep learning frameworks terugvallen naar de CPU-modus.

[Meer informatie over beschikbare deep learning- en AI-frameworks](dsvm-tools-deep-learning-frameworks.md).

<a name="included"></a>
## <a name="whats-included-on-the-dsvm"></a>Wat staat er op de DSVM?

Bekijk [hier](tools-included.md)een volledige lijst met tools op zowel de Windows als Linux DSVM's.

## <a name="next-steps"></a>Volgende stappen

Meer informatie met deze artikelen:

+ Windows:
  + [Een Windows-DSVM instellen](provision-vm.md)
  + [Tien dingen die u doen op een Windows DSVM](vm-do-ten-things.md)

+ Linux:
  + [Een Linux DSVM (Ubuntu) instellen](dsvm-ubuntu-intro.md)
  + [Data science op een Linux DSVM](linux-dsvm-walkthrough.md)
