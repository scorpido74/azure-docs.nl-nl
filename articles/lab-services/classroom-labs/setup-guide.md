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
ms.openlocfilehash: d58e11551c2857c269e8985e81f84138f6d389ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370812"
---
# <a name="classroom-lab-setup-guide"></a>Handleiding voor het instellen van klassikale laboratorium

Het proces voor het publiceren van een lab aan uw studenten kan enkele uren duren, afhankelijk van het aantal virtuele machines (VM's) dat in uw lab wordt gemaakt.  Je moet jezelf ten minste een dag om het opzetten van een lab om ervoor te zorgen dat het goed werkt en om voldoende tijd om studenten 'VM's te publiceren.

## <a name="understand-your-classs-lab-requirements"></a>Begrijp de labvereisten van uw klas

Voordat u een nieuw lab instelt, moet u rekening houden met de volgende vragen:

**Welke softwarevereisten heeft de klas?**

Op basis van de leerdoelstellingen van uw klas moet u beslissen welk besturingssysteem, toepassingen, tools, enz.   Als u lab-VM's wilt instellen, hebt u drie opties:

- **Gebruik een Azure Marketplace-afbeelding** : Marketplace biedt honderden afbeeldingen die u gebruiken bij het maken van een lab.  Voor sommige klassen kan een marktplaatsafbeelding al alles bevatten wat u nodig hebt voor uw klas.

- **Een nieuwe aangepaste afbeelding maken** - U uw eigen aangepaste afbeelding maken door een marketplace-afbeelding als uitgangspunt te gebruiken en deze aan te werken door extra software te installeren, configuratiewijzigingen aan te brengen, enz.

- **Een bestaande aangepaste afbeelding gebruiken** : u bestaande aangepaste afbeeldingen die u eerder hebt gemaakt of die zijn gemaakt door andere beheerders of docenten op uw school, opnieuw gebruiken. Dit vereist dat uw beheerders een gedeelde afbeeldingsgalerie hebben geconfigureerd, een opslagplaats voor het opslaan van aangepaste afbeeldingen.

> [!NOTE]
> Uw beheerders zijn verantwoordelijk voor het inschakelen van Marketplace en aangepaste afbeeldingen, zodat u ze gebruiken; u moet coördineren met uw IT-afdeling om ervoor te zorgen dat afbeeldingen die u nodig hebt, zijn ingeschakeld.  Aangepaste afbeeldingen die u maakt, worden automatisch ingeschakeld voor gebruik binnen laboratoria die u bezit.

**Welke hardwarevereisten heeft de klas?**

Er zijn verschillende rekengroottes waaruit u kiezen, waaronder:

- Geneste virtualisatieformaten, zodat u studenten toegang geven tot een VM die meerdere geneste VM's kan hosten; Deze rekengrootte wordt bijvoorbeeld vaak gebruikt voor netwerkcursussen.

- GPU-formaten zodat uw studenten computerintensieve soorten toepassingen kunnen gebruiken, zoals voor kunstmatige intelligentie en machine learning.

Raadpleeg de handleiding voor [VM-grootte](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) om de volledige lijst met beschikbare rekenformaten te bekijken.

> [!NOTE]
> Afhankelijk van de regio waarin uw lab wordt gemaakt, zijn er mogelijk minder rekengroottes beschikbaar, omdat dit per regio verschilt.  Onze algemene aanbeveling is om de kleinste rekengrootte te selecteren die het dichtst bij uw behoeften ligt.  Met Azure Lab Services u later een nieuw lab instellen met een andere rekencapaciteit als dat nodig is.

**Welke afhankelijkheden heeft de klasse van externe Azure- of netwerkbronnen?**

Als uw lab VM's externe bronnen moeten gebruiken, zoals een database, bestandsshare, licentieserver, enz.

Voor toegang tot Azure-bronnen die *niet* zijn beveiligd door een virtueel netwerk, u deze bronnen openen via het openbare internet zonder extra configuratie door uw beheerders.

> [!NOTE]
> U moet overwegen of u de afhankelijkheid van uw lab tot externe bronnen verminderen door de bron rechtstreeks op de VM te verstrekken.  Als u bijvoorbeeld niet meer nodig hebt om gegevens uit een externe database te lezen, u de database rechtstreeks op de vm installeren.  

**Hoe worden de kosten onder controle?**

Lab Services maakt gebruik van een pay-as-you-go-prijsmodel, wat betekent dat u alleen betaalt voor de tijd dat een lab-VM wordt uitgevoerd.  Als u de kosten wilt beheersen, hebt u drie opties die doorgaans in combinatie met elkaar worden gebruikt:

- **Schema** - Met een schema u automatisch bepalen wanneer de VM's van uw labs worden gestart en afgesloten.
- **Quotum** : het quotum bepaalt het aantal uren dat studenten toegang hebben tot een virtuele machine buiten de geplande uren.  Als het quotum wordt bereikt terwijl een student het gebruikt, wordt de vm automatisch afgesloten en kan de student de vm niet opnieuw starten, tenzij het quotum wordt verhoogd.
- **Automatisch afsluiten** - Wanneer ingeschakeld, zorgt de instelling voor automatisch afsluiten ervoor dat Windows VM's na een bepaalde tijd automatisch worden afgesloten zodra een student de verbinding met zijn RDP-sessie heeft verbroken.  Deze instelling is standaard uitgeschakeld.  

    > [!NOTE]
    > Deze instelling bestaat momenteel alleen voor Windows.

**Hoe zullen studenten hun werk redden?**

Studenten krijgen elk hun eigen VM toegewezen die aan hen is toegewezen voor de levensduur van het lab.  Ze kunnen ervoor kiezen om:

- Sla rechtstreeks op de VM.
- Opslaan in een externe opslagplaats, zoals OneDrive, GitHub, enz.

Als u OneDrive wilt gebruiken, u ervoor kiezen dit automatisch te configureren voor studenten op hun lab-VM's.  Aanvullende informatie hierover vindt u hieronder.

> [!NOTE]
> Om ervoor te zorgen dat uw studenten nog steeds toegang hebben tot hun opgeslagen werk buiten het lab, waaronder na afloop van de les, raden we studenten aan hun werk op te slaan in een externe opslagplaats.

**Hoe maken studenten verbinding met hun VM?**

Voor RDP naar Windows VM's raden we studenten aan [microsoft Remote Desktop-client te](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)gebruiken.  De Extern bureaublad-client ondersteunt Macs, Chromebooks en Windows.

Voor Linux VM's kunnen studenten SSH of RDP gebruiken.   Om verbinding te maken via RDP, bent u verantwoordelijk voor het installeren en configureren van de benodigde RDP- en GUI-pakketten.  Meer details hierover vindt u hieronder.

## <a name="set-up-your-lab"></a>Uw lab instellen

Zodra u de vereisten voor het lab van uw klas begrijpt, bent u klaar om het op te zetten.  Volg de links in deze sectie om te zien hoe u uw lab instelt.

1. **Een lab maken**

   Raadpleeg de zelfstudie over [het maken van een klaslokaallab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) voor instructies.

    > [!NOTE]
    > Als uw klas geneste virtualisatie vereist, raadpleegt u stappen in de handleiding die [geneste virtualisatie inschakelt.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)

1. **Afbeeldingen aanpassen en vm's van labv's publiceren**

    Als u afbeeldingen wilt aanpassen en VM's in uw lab wilt publiceren, maakt u verbinding met een speciale VM, de sjabloon-VM; raadpleeg de volgende stappen:
    - [Een sjabloon-vm maken en beheren](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Een gedeelde afbeeldingsgalerie gebruiken](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Als u Windows gebruikt, moet u ook verwijzen naar instructies in de handleiding voor [het voorbereiden van een Windows-sjabloonVM.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template)  Deze instructies bevatten stappen voor het instellen van OneDrive en Office voor uw studenten om te gebruiken.

1. **VM-pool en -capaciteit beheren**

   U de VM-capaciteit eenvoudig op- of neerschalen als dat nodig is voor uw klas.  Houd er rekening mee dat het verhogen van de VM-capaciteit enkele uren kan duren, omdat dit het instellen van nieuwe VM's inhoudt.  Raadpleeg stappen in de handleiding voor [het instellen en beheren van een VM-groep.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)

1. **Labgebruikers toevoegen en beheren**

   Als u gebruikers aan uw lab wilt toevoegen, raadpleegt u stappen in de volgende zelfstudies:
   - [Gebruikers toevoegen aan het lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Uitnodigingen verzenden naar gebruikers](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Raadpleeg ook het volgende artikel voor informatie over de typen accounts die studenten kunnen gebruiken:
    - [Studentenaccounts](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)
  
1. **Kostenregelaars instellen**

    Als u de kosten van uw lab wilt beheren, stelt u schema's, quota's en automatische afsluiting in. raadpleeg de instructies in de volgende tutorials:

   - [Een schema instellen](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > Afhankelijk van het type besturingssysteem dat u hebt geïnstalleerd, kan het enkele minuten duren voordat een VIRTUELE V.D.  Om ervoor te zorgen dat een lab-VM klaar is voor gebruik tijdens uw geplande uren, raden we u aan vm's 30 minuten van tevoren te starten om ervoor te zorgen dat VM's worden uitgevoerd en klaar voor gebruik.

   - [Quota instellen voor gebruikers](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) en [extra quota instellen voor een specifieke gebruiker](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quota-for-a-specific-user)
  
   - [De functie Automatisch afsluiten wanneer de verbinding is verbroken inschakelen](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Schema's, quota's en automatische afsluiting zijn *niet* van toepassing op de sjabloon-vm.  Als gevolg hiervan moet u ervoor zorgen dat u de sjabloon-vm afsluit wanneer deze niet wordt gebruikt, anders blijft deze kosten maken.  Wanneer u een lab maakt, wordt de sjabloon-VM standaard automatisch gestart, dus u wilt ervoor zorgen dat u het opzetten van het lab onmiddellijk hebt voltooid en de sjabloon-VM afsluit.

1. **Dashboard gebruiken**

    Raadpleeg de handleiding voor [het gebruik van het dashboard van het lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard) voor instructies.

    > [!NOTE]
    > De geschatte kosten in het dashboard zijn de maximale kosten die u verwachten voor studenten die het lab gebruiken.  Er worden bijvoorbeeld *geen* kosten in rekening gebracht voor ongebruikte quotumuren door je studenten.  De geschatte kosten weerspiegelen *geen* kosten voor het gebruik van de sjabloon-VM of de gedeelde afbeeldingsgalerie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen:

- [Gebruik van een leslokaallab bijhouden](tutorial-track-usage.md)
  
- [Een leslokaallab openen](tutorial-connect-virtual-machine-classroom-lab.md)
