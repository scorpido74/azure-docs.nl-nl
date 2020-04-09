---
title: Versnelde handleiding voor het instellen van lokalen voor Azure Lab Services
description: Deze handleiding helpt labmakers om snel een labaccount in te stellen voor gebruik binnen hun school.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: e00b6dd5af1cb489aee9e8b4c9f3337eb02e4b14
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878099"
---
# <a name="classroom-lab-setup-guide"></a>Handleiding voor het instellen van klassikale laboratorium

Het proces voor het publiceren van een lab aan uw studenten kan enkele uren duren, afhankelijk van het aantal virtuele machines (VM's) dat in uw lab wordt gemaakt. Laat ten minste een dag om het opzetten van een lab, om ervoor te zorgen dat het goed werkt en om voldoende tijd om studenten 'VM's te publiceren.

## <a name="understand-the-lab-requirements-of-your-class"></a>Inzicht in de labvereisten van uw klas

Voordat u een nieuw lab instelt, moet u de volgende vragen overwegen.

### <a name="what-software-requirements-does-the-class-have"></a>Welke softwarevereisten heeft de klas?

Bepaal op basis van de leerdoelstellingen van uw klas welk besturingssysteem, toepassingen en hulpprogramma's op de VM's van het lab moeten worden geïnstalleerd. Als u lab-VM's wilt instellen, hebt u drie opties:

- **Gebruik een Azure Marketplace-afbeelding:** Azure Marketplace biedt honderden afbeeldingen die u gebruiken wanneer u een lab maakt. Voor sommige klassen bevat een van deze afbeeldingen mogelijk al alles wat u nodig hebt voor uw klas.

- **Een nieuwe aangepaste afbeelding maken:** u uw eigen aangepaste afbeelding maken door een Azure Marketplace-afbeelding als uitgangspunt te gebruiken en deze aan te werken door extra software te installeren en configuratiewijzigingen aan te brengen.

- **Gebruik een bestaande aangepaste afbeelding:** u bestaande aangepaste afbeeldingen die u eerder hebt gemaakt of die zijn gemaakt door andere beheerders of docenten op uw school, opnieuw gebruiken. Dit vereist dat uw beheerders een gedeelde afbeeldingsgalerie hebben geconfigureerd, een opslagplaats voor het opslaan van aangepaste afbeeldingen.

> [!NOTE]
> Uw beheerders zijn verantwoordelijk voor het inschakelen van Azure Marketplace-afbeeldingen en aangepaste afbeeldingen, zodat u ze gebruiken. Coördineer met uw IT-afdeling om ervoor te zorgen dat afbeeldingen die u nodig hebt, zijn ingeschakeld. Aangepaste afbeeldingen die u maakt, worden automatisch ingeschakeld voor gebruik binnen laboratoria die u bezit.

### <a name="what-hardware-requirements-does-the-class-have"></a>Welke hardwarevereisten heeft de klas?

Er zijn verschillende rekengroottes waaruit u kiezen:

- Geneste virtualisatieformaten, zodat u studenten toegang geven tot een VM die meerdere geneste VM's kan hosten. U deze rekengrootte bijvoorbeeld gebruiken voor netwerkcursussen.

- GPU-formaten, zodat uw studenten computerintensieve soorten toepassingen kunnen gebruiken. Deze keuze kan bijvoorbeeld geschikt zijn voor kunstmatige intelligentie en machine learning.

Raadpleeg de handleiding voor [VM-grootte](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) om de volledige lijst met beschikbare rekenformaten te bekijken.

> [!NOTE]
> Afhankelijk van de regio van uw lab, ziet u mogelijk minder rekengroottes beschikbaar, omdat dit per regio verschilt. Over het algemeen moet u de kleinste rekengrootte selecteren die het dichtst bij uw behoeften ligt. Met Azure Lab Services u later, indien nodig, een nieuw lab instellen met een andere rekencapaciteit.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Welke afhankelijkheden heeft de klasse van externe Azure- of netwerkbronnen?

Als uw lab-VM's externe bronnen moeten gebruiken, zoals een database, bestandsshare of licentieserver, coördineert u met uw beheerders om ervoor te zorgen dat uw lab toegang heeft tot deze bronnen.

Voor toegang tot Azure-bronnen die *niet* zijn beveiligd door een virtueel netwerk, hoeft u geen aanvullende configuratie door uw beheerders te zoeken. U toegang krijgen tot deze bronnen via het openbare internet.

> [!NOTE]
> U moet overwegen of u de afhankelijkheid van uw lab tot externe bronnen verminderen door de bron rechtstreeks op de VM te verstrekken. Om bijvoorbeeld gegevens uit een externe database te hoeven lezen, u de database rechtstreeks op de VM installeren.  

### <a name="how-will-costs-be-controlled"></a>Hoe worden de kosten onder controle?

Lab Services maakt gebruik van een pay-as-you-go-prijsmodel, wat betekent dat u alleen betaalt voor de tijd dat een lab-VM wordt uitgevoerd. Als u de kosten wilt beheersen, hebt u drie opties die doorgaans in combinatie met elkaar worden gebruikt:

- **Planning:** Met een planning u automatisch bepalen wanneer de VM's van uw labs worden gestart en afgesloten.
- **Quotum:** het quotum bepaalt het aantal uren dat studenten toegang hebben tot een virtuele machine buiten de geplande uren. Als het quotum wordt bereikt terwijl een student het gebruikt, wordt de vm automatisch afgesloten. De student kan de vm niet opnieuw starten, tenzij het quotum wordt verhoogd.
- **Automatisch afsluiten:** Wanneer ingeschakeld, zorgt de instelling voor automatisch afsluiten ervoor dat Windows VM's na een bepaalde tijd automatisch worden afgesloten, nadat een student de verbinding heeft verbroken met een RDP-sessie (Remote Desktop Protocol). Deze instelling is standaard uitgeschakeld.  

    > [!NOTE]
    > Deze instelling bestaat momenteel alleen voor Windows.

### <a name="how-will-students-save-their-work"></a>Hoe zullen studenten hun werk redden?

Studenten krijgen elk hun eigen VM toegewezen, die aan hen is toegewezen voor de levensduur van het lab. Ze kunnen ervoor kiezen om:

- Sla rechtstreeks op de VM.
- Opslaan op een externe locatie, zoals OneDrive of GitHub.

Het is mogelijk om OneDrive automatisch te configureren voor studenten op hun lab VM's.

> [!NOTE]
> Om ervoor te zorgen dat uw studenten toegang hebben tot hun opgeslagen werk buiten het lab en nadat de les is afgelopen, raden we studenten aan hun werk op te slaan in een externe opslagplaats.

### <a name="how-will-students-connect-to-their-vm"></a>Hoe maken studenten verbinding met hun VM?

Voor RDP naar Windows VM's raden we studenten aan de [Microsoft Remote Desktop-client te](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)gebruiken. De Extern bureaublad-client ondersteunt Macs, Chromebooks en Windows.

Voor Linux VM's kunnen studenten SSH of RDP gebruiken. Als u de studenten verbinding wilt laten maken met RDP, moet u de benodigde RDP- en GUI-pakketten installeren en configureren.

## <a name="set-up-your-lab"></a>Uw lab instellen

Nadat u de vereisten voor het lab van uw klas hebt begrepen, bent u klaar om het op te zetten. Volg de links in deze sectie om te zien hoe u uw lab instelt.

1. **Maak een lab.** Raadpleeg de zelfstudie over [het maken van een klaslokaallab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) voor instructies.

    > [!NOTE]
    > Als uw klas geneste virtualisatie vereist, raadpleegt u de stappen voor [het inschakelen van geneste virtualisatie.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)

1. **Afbeeldingen aanpassen en laboratorium-VM's publiceren.** Maak verbinding met een speciale VM genaamd de sjabloon-VM. Bekijk de stappen in de volgende hulplijnen:
    - [Een sjabloon-vm maken en beheren](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Een gedeelde installatiekopiegalerie gebruiken](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Als u Windows gebruikt, ziet u ook de instructies bij [het voorbereiden van een Windows-sjabloon-VM.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template) Deze instructies bevatten stappen voor het instellen van OneDrive en Office voor uw studenten om te gebruiken.

1. **Vm-pool en -capaciteit beheren.** U de VM-capaciteit eenvoudig op- of neerschalen, zoals nodig door uw klas. Houd er rekening mee dat het verhogen van de VM-capaciteit enkele uren kan duren, omdat dit het instellen van nieuwe VM's inhoudt. Bekijk de stappen in [het instellen en beheren van een VM-groep.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)

1. **Labgebruikers toevoegen en beheren.** Als u gebruikers aan uw lab wilt toevoegen, raadpleegt u stappen in de volgende zelfstudies:
   - [Gebruikers toevoegen aan het lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Uitnodigingen verzenden naar gebruikers](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Zie [Studentenaccounts](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)voor informatie over de typen accounts die studenten kunnen gebruiken.
  
1. **Kostenregelaars instellen.** Als u de kosten van uw lab wilt beheren, stelt u schema's, quota's en automatische afsluiting in. Zie de volgende zelfstudies:

   - [Een schema instellen](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > Afhankelijk van het type besturingssysteem dat u hebt geïnstalleerd, kan het enkele minuten duren voordat een VM is gestart. Om ervoor te zorgen dat een lab-VM klaar is voor gebruik tijdens uw geplande uren, raden we u aan vm's 30 minuten van tevoren te starten.

   - [Quota instellen voor gebruikers](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) en [extra quota instellen voor een specifieke gebruiker](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [De functie Automatisch afsluiten wanneer de verbinding is verbroken inschakelen](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Schema's, quota's en automatische afsluiting zijn niet van toepassing op de sjabloon-vm. Als gevolg hiervan moet u ervoor zorgen dat u de sjabloon-vm afsluit wanneer deze niet wordt gebruikt. Anders blijft het kosten met zich meebrengen. Wanneer u een lab maakt, wordt de sjabloon-VM standaard ook automatisch gestart. Zorg ervoor dat u het opzetten van het lab onmiddellijk hebt voltooid en de sjabloon-vm afsluit.

1. **Gebruik het dashboard.** Zie voor instructies [het dashboard van het lab gebruiken.](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)

    > [!NOTE]
    > De geschatte kosten in het dashboard zijn de maximale kosten die u verwachten voor studenten die het lab gebruiken. Er worden bijvoorbeeld *geen* kosten in rekening gebracht voor ongebruikte quotumuren door je studenten. De geschatte kosten weerspiegelen *geen* kosten voor het gebruik van de sjabloon-VM of de gedeelde afbeeldingsgalerie.

## <a name="next-steps"></a>Volgende stappen

- [Gebruik van een leslokaallab bijhouden](tutorial-track-usage.md)
  
- [Een leslokaallab openen](tutorial-connect-virtual-machine-classroom-lab.md)