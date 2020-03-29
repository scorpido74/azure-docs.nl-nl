---
title: Claimmogelijkheden gebruiken in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over verschillende scenario's voor het gebruik van claim/claimmogelijkheden van Azure DevTest Labs
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: a15148260bccadc59966c86031100f0e0332b0f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67861428"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Claimmogelijkheden gebruiken in Azure DevTest Labs
De Azure DevTest Labs-service verbetert de effectiviteit en efficiëntie van ontwikkelaars en testers. Dit artikel richt zich op de mogelijkheid om virtuele machines te claimen of te claimen in Azure DevTest Labs. Het bevat ook verschillende manieren waarop deze functie de gebruikerservaring verbetert. Voordat we kijken naar verschillende scenario's waar deze functie kan worden gebruikt, laten we eens kijken naar wat **claimen** is en hoe het werkt.

## <a name="claimable-machines"></a>Claimbare machines
Een claimable machine is een virtuele machine (VM) die is gemaakt in een lab zonder eigenaar. Zodra de machine is geclaimd, heeft de gebruiker een volledig scala aan opties voor die VM. Wanneer een gebruiker een machine claimt, worden een paar wijzigingen aangebracht. De VM wordt verplaatst van de lijst **met eerbare virtuele machines** naar de lijst Mijn virtuele **machines** in de Azure-portal. 

De gebruiker kan verbinding maken met de VM, artefacten aanpassen, de machine opnieuw starten, stoppen of claimen. Er zijn een paar manieren om een VM claimable te maken:

- Maak een machine en claim deze zo ongedaan dat deze naar de claimbare pool wordt verplaatst. 
- Maak een vm en plaats in de gedeelde groep met geavanceerde [instellingen](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Er zijn twee gevallen waarin de claim/un-claim-mogelijkheden effectief kunnen worden gebruikt. Het eerste geval vereist meer voorbedachte rade en planning, om goed te worden ontworpen en uitgevoerd. En, de tweede is meer situationeel. Hieronder volgen enkele voorbeelden van de verschillende gevallen.

## <a name="designed-use-of-claimable-machines"></a>Ontworpen gebruik van claimable machines

- **Software ontwikkeling / testen:** Laat ontwikkelaars of testers productiever zijn door machines gereed en in een niet-opgeëiste staat te laten staan. Met een set VM's met verschillende configuraties, benodigde tools en met de nieuwste code kunnen gebruikers een VM claimen en aan de slag gaan zonder de tijd te hoeven besteden aan het opzetten van een machine. Voordat de VM's worden geclaimd, worden de machines ingericht, maar worden de kosten van het hebben van machines die minder vaak worden gebruikt, uitgeschakeld. Wanneer de VM's nodig zijn, een gebruiker gewoon beweert dat de VM, die de machine start. De unclaim-optie is in dit geval niet zo handig, omdat het maken van een nieuwe virtuele machine vaak eenvoudiger en goedkoper is.
- **Klaslokaal/Labs:** Vm's vooraf configureren voor een klas of een lab, zodat studenten onmiddellijk verbinding kunnen maken met een machine via de Azure-portal.  Zodra een student een VM claimt, zorgt het lab ervoor dat niemand dezelfde machine kan claimen. Het automatiseren van dit proces zorgt ervoor dat het vereiste aantal machines met de opgegeven omgeving beschikbaar is. Als studenten niet komen opdagen of te laat komen, kunnen de niet-opgeëiste machines beschikbaar blijven totdat de sessie voorbij is met minimale kosten. De claimoptie is in dit scenario niet zo effectief omdat de vm zich in een onbekende staat bevindt wanneer de vorige gebruiker klaar is.
- **Demonstraties:** Gebruik machines voor demonstraties, waarbij de machines in het lab zijn opgezet met specifieke omgevingen. Deze mogelijkheid is handig wanneer meerdere mensen tegelijkertijd of op willekeurige tijdstippen een demonstratie geven, zoals op een conferentie. De unclaim-optie kan in deze situatie nuttig zijn, omdat de demo de status van de machine niet mag wijzigen, zodat gebruikers een VM kunnen terugsturen naar de claimbare pool voor de volgende demonstratie. Met de niet-opgeëiste machine wordt ont-ingericht en het maken van minimale kosten, VM's kunnen worden achtergelaten in het lab voor langere tijd.
- **Uitzendkrachten/uitzendkrachten:** Gebruikers toestaan een machine te gebruiken. Wanneer ze vertrekken, keren ze de VM terug naar de claimable pool zonder verlies van gegevens. Als de VM niet wordt opgeëist, kan een andere gebruiker de VM claimen en de machine voortzetten of controleren op aanvullende informatie.
- **In het algemeen:** De mogelijkheid om een enige bron automatisch vm's te configureren en te implementeren, op een specifieke cadans, is handig in veel verschillende situaties. Er zijn verschillende situaties waarin de claim/unclaim-functie gebruikers helpt efficiënter te zijn door een geautomatiseerd proces te hebben om de VM's in een niet-opgeëiste status te bouwen met een vaste configuratie. De configuratie(en) kan verschillende besturingssystemen, talen, schijven of [andere software (artefacten)](devtest-lab-artifact-author.md) bevatten, afhankelijk van uw behoeften. De mogelijkheid om een VM te claimen uit het lab stelt de labgebruiker in staat om een goed geconfigureerd systeem te krijgen zonder de tijd of moeite te besteden aan het configureren van de machine. De labmanager kan de geclaimde status van de VM's gebruiken om het aantal gegenereerde machines te verbeteren, machines op te schonen en de prioriteit van configuraties te bepalen. De [Image factory](image-factory-create.md) is een goed voorbeeld van een geautomatiseerd proces om VM's en afbeeldingen te bouwen voor meerdere labs. De scripts kunnen worden gewijzigd om een van de volgende situaties uit te voeren met de juiste wijzigingen of worden gebruikt als referentie voor het maken van een aangepast systeem.

## <a name="situational-use-of-claimable-machines"></a>Situationeel gebruik van claimbare machines

- Gebruik de claim/un-claim-mogelijkheid waarmee gebruikers de besturing van machines van de ene naar de andere kunnen doorgeven en niet expliciet hoeven te weten wie de machine vervolgens oppakt.
- Ontwikkeling, testen en debuggen van een scenario waarbij een specifieke machineconfiguratie een bug kan reproduceren, dan kan de machine niet worden opgeëist waardoor een andere ontwikkelaar de machine kan claimen en het werk kan voortzetten. Deze functie is vooral handig omdat meer mensen op afstand werken in verschillende gebieden van de wereld. 
- Teamleden kunnen met één omgeving werken. U bijvoorbeeld handmatig een complexe omgeving instellen die niet kan worden geautomatiseerd of resources maken die alleen wijzigingen voor één invoer, zoals afbeeldingen, kunnen verwerken. In het verleden werd dit probleem opgelost door een speciale machine in bedrijf te hebben. De claimable functie is een verbetering ten opzichte van het handmatige proces door het hebben van ingebouwde gebruiker toegangscontrole, en visuele identificatie indien beschikbaar. Wanneer niet opgeëist, wordt de VM gede-ingericht om de kosten te verlagen.
- Heb een gegevensschijf die is gekoppeld aan een vm. Elke schijf tot ~ 1 TB aan gegevens maakt het mogelijk een groot volume van de gegevens te worden doorgegeven zonder te kopiëren of dupliceren van de gegevens. De VM zou in eerste instantie worden gemaakt met een aangesloten schijf die het grote volume van de gegevens had.  Elke gebruiker kan dan aanspraak maken op de machine en toegang krijgen tot de gegevens. Wanneer u klaar bent, ontzegt u de VM om andere gebruikers toe te staan aan de machine.

Er zijn enkele kanttekeningen bij het gebruik van claimable machines, meestal rond het verkrijgen van toegang tot de machine. Als de machine is domein samengevoegd, dan is de gebruiker die beweert dat de machine moet al toegang hebben gekregen, meestal wordt dit gedaan door het verlenen van toegang tot een groep die alle gebruikers in het lab omvat wanneer de VM wordt gemaakt. Als de machine niet is samengevoegd met het domein, moet het artefact **VM-wachtwoord opnieuw instellen** in de openbare opslagplaats worden uitgevoerd om de gebruiker als beheerder toe te voegen.  Artefacten kunnen worden toegepast, zelfs nadat de machine is gestart of geclaimd.

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel: [Eisbare VM's maken en beheren in Azure DevTest Labs](devtest-lab-add-claimable-vm.md)
