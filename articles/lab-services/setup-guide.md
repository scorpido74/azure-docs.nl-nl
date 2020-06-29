---
title: Gids voor de installatie van een versneld leslokaal Lab voor Azure Lab Services
description: Deze hand leiding helpt Lab-makers snel een Lab-account in te stellen voor gebruik op hun school.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad1a679c556316c2d23a713ffa5ac5dfe86cce0e
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445573"
---
# <a name="classroom-lab-setup-guide"></a>Setup-hand leiding voor klas Lab

Het proces voor het publiceren van een lab naar uw studenten kan tot enkele uren duren, afhankelijk van het aantal virtuele machines (Vm's) dat in uw Lab wordt gemaakt. Zorg ervoor dat er ten minste één dag een Lab is ingesteld, om er zeker van te zijn dat het goed werkt en voldoende tijd heeft om de Vm's van studenten te publiceren.

## <a name="understand-the-lab-requirements-of-your-class"></a>Meer informatie over de Lab-vereisten van uw klasse

Voordat u een nieuw Lab instelt, moet u rekening houden met de volgende vragen.

### <a name="what-software-requirements-does-the-class-have"></a>Welke software vereisten heeft de klasse?

Bepaal op basis van de leer doelen van uw klasse welk besturings systeem, welke toepassingen en hulpprogram ma's moeten worden geïnstalleerd op de virtuele machines van het lab. Als u virtuele lab-Vm's wilt instellen, hebt u drie opties:

- **Een Azure Marketplace-installatie kopie gebruiken**: Azure Marketplace biedt honderden installatie kopieën die u kunt gebruiken bij het maken van een lab. Voor sommige klassen bevat een van deze installatie kopieën mogelijk al alles wat u nodig hebt voor uw klasse.

- **Een nieuwe aangepaste installatie kopie maken**: u kunt uw eigen aangepaste installatie kopie maken met behulp van een Azure Marketplace-installatie kopie als uitgangs punt, en deze aanpassen door extra software te installeren en wijzigingen in de configuratie aan te brengen.

- **Een bestaande aangepaste installatie kopie gebruiken**: u kunt bestaande aangepaste installatie kopieën die u eerder hebt gemaakt, opnieuw gebruiken of die zijn gemaakt door andere beheerders of faculteiten op uw school. Hiervoor moeten uw beheerders een gedeelde installatie kopie galerie hebben geconfigureerd. Dit is een opslag plaats voor het opslaan van aangepaste installatie kopieën.

> [!NOTE]
> Uw beheerders zijn verantwoordelijk voor het inschakelen van Azure Marketplace-installatie kopieën en aangepaste installatie kopieën, zodat u ze kunt gebruiken. Coördineer met uw IT-afdeling om ervoor te zorgen dat de installatie kopieën die u nodig hebt, zijn ingeschakeld. Aangepaste installatie kopieën die u maakt, worden automatisch ingeschakeld voor gebruik in Labs waarvan u de eigenaar bent.

### <a name="what-hardware-requirements-does-the-class-have"></a>Welke hardwarevereisten heeft de klasse?

U kunt kiezen uit verschillende reken grootten:

- Geneste virtualisatie-grootten, zodat u toegang krijgt tot een virtuele machine die kan worden gehost op meerdere geneste Vm's. U kunt deze reken grootte bijvoorbeeld gebruiken voor netwerk cursussen.

- GPU-grootten zodat uw studenten computer toepassingen kunnen gebruiken. Deze keuze kan bijvoorbeeld geschikt zijn voor kunst matige intelligentie en machine learning.

Raadpleeg de hand leiding over [VM-grootte](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) om de volledige lijst met beschik bare reken grootten weer te geven.

> [!NOTE]
> Afhankelijk van de regio van uw lab ziet u mogelijk minder berekenings grootten, omdat dit varieert per regio. Over het algemeen moet u de kleinste reken grootte selecteren die het dichtst bij uw behoeften ligt. Met Azure Lab Services kunt u later, indien nodig, een nieuwe lab instellen met een andere reken capaciteit.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Welke afhankelijkheden heeft de klasse op externe Azure-of netwerk bronnen?

Als uw Lab-Vm's externe bronnen moeten gebruiken, zoals een Data Base, bestands share of licentie server, moet u met uw beheerders instemmen om ervoor te zorgen dat uw Lab toegang heeft tot deze resources.

Voor toegang tot Azure-bronnen die *niet* zijn beveiligd door een virtueel netwerk, hoeft u geen aanvullende configuratie te zoeken door uw beheerders. U kunt toegang krijgen tot deze bronnen via het open bare Internet.

> [!NOTE]
> U kunt overwegen om de afhankelijkheden van uw Lab te verlagen voor externe bronnen door de resource rechtstreeks op de virtuele machine op te geven. Als u bijvoorbeeld wilt voor komen dat gegevens uit een externe data base hoeven te worden gelezen, kunt u de data base rechtstreeks op de virtuele machine installeren.  

### <a name="how-will-costs-be-controlled"></a>Hoe worden de kosten beheerd?

Lab Services maakt gebruik van een prijs model voor betalen per gebruik, wat betekent dat u alleen betaalt voor de tijd dat een Lab-VM wordt uitgevoerd. Als u de kosten wilt beheren, hebt u drie opties die doorgaans samen met elkaar worden gebruikt:

- **Planning**: met een schema kunt u automatisch bepalen wanneer de virtuele machines van uw Labs worden gestart en afgesloten.
- **Quotum**: het quotum bepaalt het aantal uren dat studenten buiten de geplande uren toegang hebben tot een virtuele machine. Als het quotum is bereikt terwijl een student het gebruikt, wordt de virtuele machine automatisch afgesloten. De student kan de virtuele machine niet opnieuw opstarten, tenzij het quotum is verhoogd.
- **Automatisch afsluiten**: wanneer deze optie is ingeschakeld, worden Windows-vm's na een bepaalde periode automatisch afgesloten nadat een student is losgekoppeld van een Remote Desktop Protocol-sessie (RDP). Deze instelling is standaard uitgeschakeld.  

    > [!NOTE]
    > Deze instelling bestaat momenteel alleen voor Windows.

### <a name="how-will-students-save-their-work"></a>Hoe kunnen studenten hun werk opslaan?

Studenten krijgen elk hun eigen virtuele machine, die aan hen is toegewezen voor de levens duur van het lab. Ze kunnen kiezen uit:

- Sla het bestand rechtstreeks op de virtuele machine op.
- Sla het bestand op een externe locatie op, zoals OneDrive of GitHub.

Het is mogelijk om OneDrive automatisch te configureren voor studenten op hun Lab-Vm's.

> [!NOTE]
> Om ervoor te zorgen dat uw studenten geen toegang meer hebben tot hun opgeslagen werk buiten het lab, wordt u aangeraden hun werk op te slaan in een externe opslag plaats.

### <a name="how-will-students-connect-to-their-vm"></a>Hoe maken studenten verbinding met hun VM?

Voor RDP-naar-Windows-Vm's wordt aanbevolen dat studenten de [Microsoft extern bureaublad-client](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)gebruiken. Extern bureaublad-client ondersteunt Macs, Chromebooks en Windows.

Voor Linux-Vm's kunnen studenten ofwel SSH ofwel RDP gebruiken. Als u wilt dat de studenten verbinding maken met behulp van RDP, moet u de benodigde RDP-en GUI-pakketten installeren en configureren.

## <a name="set-up-your-lab"></a>Uw lab instellen

Nadat u de vereisten voor het lab van uw klasse hebt begrepen, bent u klaar om deze in te stellen. Volg de koppelingen in deze sectie om te zien hoe u uw lab kunt instellen.

1. **Maak een lab.** Raadpleeg de zelf studie over het [maken van een leslokaal Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) voor instructies.

    > [!NOTE]
    > Als uw klasse geneste virtualisatie vereist, raadpleegt u de stappen in het [inschakelen van geneste virtualisatie](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Afbeeldingen aanpassen en Lab-Vm's publiceren.** Maak verbinding met een speciale virtuele machine met de naam VM van de sjabloon. Zie de stappen in de volgende hand leidingen:
    - [Een sjabloon-VM maken en beheren](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Een gedeelde installatiekopiegalerie gebruiken](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Als u Windows gebruikt, moet u ook de instructies in de [virtuele machine van een Windows-sjabloon voorbereiden](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). Deze instructies omvatten stappen voor het instellen van OneDrive en Office die uw studenten kunnen gebruiken.

1. **Groep en capaciteit van de virtuele machine beheren.** U kunt de capaciteit van de virtuele machine eenvoudig omhoog of omlaag schalen, afhankelijk van uw klasse. Houd er rekening mee dat het verhogen van de capaciteit van de virtuele machine enkele uren kan duren, omdat hiervoor nieuwe Vm's moeten worden ingesteld. Zie de stappen voor het [instellen en beheren van een VM-groep](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Test gebruikers toevoegen en beheren.** Raadpleeg de stappen in de volgende zelf studies om gebruikers toe te voegen aan uw Lab:
   - [Gebruikers toevoegen aan het lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Uitnodigingen voor gebruikers verzenden](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Zie [studenten accounts](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)voor meer informatie over de typen accounts die studenten kunnen gebruiken.
  
1. **Stel kosten besturings elementen in.** U kunt de kosten van uw Lab bepalen door schema's, quota's en automatisch afsluiten in te stellen. Zie de volgende zelfstudies:

   - [Een planning instellen](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > Afhankelijk van het type besturings systeem dat u hebt geïnstalleerd, kan het enkele minuten duren voordat de virtuele machine wordt gestart. Om ervoor te zorgen dat een Lab-VM gereed is voor gebruik tijdens de geplande uren, raden we u aan om de Vm's 30 minuten vooraf te starten.

   - [Quota instellen voor gebruikers](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) en [extra quota instellen voor een specifieke gebruiker](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [De functie Automatisch afsluiten wanneer de verbinding is verbroken inschakelen](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Schema's en quota zijn niet van toepassing op de sjabloon-VM, maar de instellingen voor automatisch afsluiten zijn van toepassing. 
        > 
        > Wanneer u een Lab maakt, wordt de VM van de sjabloon gemaakt, maar niet gestart. U kunt het starten, er verbinding mee maken en eventuele vereiste software voor het lab installeren en vervolgens publiceren. Wanneer u de sjabloon-VM publiceert, wordt deze automatisch afgesloten als u dat nog niet hebt gedaan. 
        > 
        > Voor sjabloon-VM’s worden **kosten** in rekening gebracht wanneer ze worden uitgevoerd, dus zorg ervoor dat de sjabloon-VM wordt gesloten wanneer deze niet hoeft worden uitgevoerd. 


1. **Gebruik het dash board.** Zie [het dash board van het lab gebruiken](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)voor instructies.

    > [!NOTE]
    > De geschatte kosten die in het dash board worden weer gegeven, zijn de maximale kosten die u kunt verwachten voor het gebruik van studenten van het lab. Er worden bijvoorbeeld *geen* kosten in rekening gebracht voor ongebruikte quotum uren door uw studenten. De geschatte kosten zijn *niet* van toepassing op de kosten voor het gebruik van de sjabloon-VM, de galerie met gedeelde afbeeldingen of wanneer de test Maker een gebruikers computer start.

## <a name="next-steps"></a>Volgende stappen

- [Gebruik van een leslokaallab bijhouden](tutorial-track-usage.md)
  
- [Een leslokaallab openen](tutorial-connect-virtual-machine-classroom-lab.md)