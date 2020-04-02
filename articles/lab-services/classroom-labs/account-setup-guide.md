---
title: Handleiding voor het instellen van versnelde labaccountvoor Azure Lab Services
description: Met deze handleiding kunnen beheerders snel een labaccount instellen voor gebruik binnen hun school.
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
ms.openlocfilehash: 88b37ea4ff717689f05afbb41d33a56a8cbb2c22
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547624"
---
# <a name="lab-account-setup-guide"></a>Handleiding voor het instellen van lab-account

De eerste stap die beheerders moeten voltooien, is het instellen van een labaccount binnen uw Azure-abonnement.  Een lab account is een container voor uw klas labs en duurt slechts een paar minuten in te stellen.

## <a name="understand-your-schools-lab-account-requirements"></a>Begrijp de vereisten van uw school voor labaccount

Als u wilt weten hoe u uw labaccount configureren op basis van de behoeften van uw school, moet u rekening houden met de volgende vragen:

**Heb ik toegang tot een Azure-abonnement?**

Als u een labaccount wilt maken, hebt u toegang nodig tot een Azure-abonnement dat is geconfigureerd voor uw school. uw school kan een of meer abonnementen hebben.  Een abonnement wordt gebruikt voor het beheren van facturering en beveiliging voor al uw Azure-resources\services die erin worden gebruikt, inclusief labaccounts.

**Hoeveel labaccounts moeten worden gemaakt?**

Om snel aan de slag te gaan, is een redelijke aanpak het maken van één labaccount en later extra Lab-accounts maken als dat nodig is.  U bijvoorbeeld uiteindelijk evolueren naar het hebben van één labaccount per afdeling.

**Wie moeten eigenaren en bijdragers van het lab account?**

Uw beheerders zijn meestal de eigenaren\bijdragers voor een labaccount, omdat ze verantwoordelijk zijn voor het beheer van het beleid dat van toepassing is op alle labs in het labaccount.  De persoon die het labaccount maakt, is automatisch eigenaar.  U extra eigenaren toevoegen\bijdragers (meestal van de AAD-tenant die aan uw abonnement is gekoppeld) om een labaccount te beheren door de rol Eigenaar\Bijdragers op labaccountniveau toe te wijst.

**Wie mag labs maken\beheren?**

U ervoor kiezen om uw beheerders en faculteitsleden labs te laten maken en beheren; deze gebruikers (meestal van de AAD-tenant die aan uw abonnement is gekoppeld) worden toegewezen aan de labcreator-rol binnen het labaccount.

**Wilt u makers van laboratoria de mogelijkheid bieden om afbeeldingen op te slaan die in laboratoria kunnen worden gedeeld?**

Een gedeelde afbeeldingsgalerie is een opslagplaats die u gebruiken voor het opslaan en delen van afbeeldingen.  Het voordeel hiervan is dat als je meerdere klassen hebt die dezelfde afbeeldingen nodig hebben, labmakers het beeld één keer kunnen maken en delen in laboratoria.  Echter, om te beginnen, is het volkomen redelijk om te beginnen zonder een Shared Image Gallery; en u er altijd voor kiezen om er later een toe te voegen.

Als je 'Ja' hebt geantwoord op deze vraag, moet je een Gedeelde Afbeeldingsgalerij maken en koppelen aan je labaccount.  Als u antwoordt: 'Ik weet het niet', dan u dit besluit uitstellen tot later.

Wanneer u een gedeelde afbeeldingsgalerie aan uw labaccount hebt gekoppeld

**Welke afbeeldingen in de Azure Marketplace gebruiken uw klassikale laboratoria?**

De Azure Marketplace biedt honderden afbeeldingen die u inschakelen, zodat makers van het lab de afbeelding kunnen gebruiken voor het maken van hun lab.  Sommige afbeeldingen kunnen alles bevatten wat een lab al nodig heeft.  In andere gevallen u een afbeelding als uitgangspunt gebruiken en vervolgens kan de maker van het lab deze aanpassen door extra toepassingen, hulpprogramma's, enz.

Als u niet weet welke afbeeldingen u moet gebruiken, u hier later altijd op terugkomen om ze in te schakelen.  Ook de beste manier om te zien welke afbeeldingen beschikbaar zijn, is door eerst een labaccount aan te maken - dit geeft je toegang, zodat je de lijst met beschikbare afbeeldingen en de inhoud ervan bekijken.  Meer informatie vindt u hieronder.
  
**Moeten de virtuele machines (VM's) van het lab toegang hebben tot andere Azure- of on-prem-bronnen?**

Wanneer u een labaccount instelt, hebt u ook de mogelijkheid om te peeren met een virtueel netwerk (VNet).  Als u wilt bepalen of u met een VNet moet gekeken worden, u rekening houden met de volgende vragen:

- **Moet u toegang bieden tot een licentieserver?**
  
   Als u Azure Marketplace-afbeeldingen wilt gebruiken, worden de kosten van de OS-licentie gebundeld in de prijzen voor Lab Services, zodat u *geen* licenties hoeft te verstrekken voor het besturingssysteem zelf.  Voor extra software\applicaties die zijn geïnstalleerd, moet u echter een licentie leveren.

- **Heeft het lab VM's toegang nodig tot andere on-prem bronnen, zoals een bestandsshare, database, enz.?**

   Er moet een VNet worden gemaakt om toegang te bieden tot on-prem-bronnen, meestal met behulp van een site-to-site Virtual Network Gateway.  Als u geen VNet-geconfigureerd hebt, moet hiervoor extra tijd worden geïnvesteerd.  Hieronder vindt u meer informatie over het instellen van dit bedrijf.

- **Hebben de VM's van het lab toegang nodig tot andere Azure-bronnen die zich in een VNet bevinden?**

    Als u toegang nodig hebt tot Azure-bronnen die *niet* zijn beveiligd binnen een VNet, u toegang krijgen tot deze bronnen via het openbare internet zonder peering te doen.

    Als je 'Ja' hebt geantwoord op een of meer vragen, moet je het labaccount op een VNet plaatsen.  Als je antwoordde: 'Ik weet het niet', dan kun je deze beslissing uitstellen tot later, omdat je er altijd voor kiezen om een VNet te peeren na het aanmaken van het labaccount.

## <a name="set-up-your-lab-account"></a>Uw labaccount instellen

Zodra u de vereisten voor uw labaccount begrijpt, bent u klaar om het in te stellen.  Volg de links in deze sectie om te zien hoe u uw labaccount instelt:

1. **Uw labaccount maken**

   Raadpleeg de zelfstudie over [het maken van een labaccount](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) voor instructies.

   Bij het maken van een labaccount u het nuttig vinden om vertrouwd te raken met de Azure-bronnen die betrokken zijn. raadpleeg de volgende lijst voor meer informatie en richtlijnen voor het maken van deze bronnen:

   - [Abonnement](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Resourcegroep](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Lab-account](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Classroom Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Een regio selecteren\Locatie](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Naamgevingsrichtlijnen voor resources](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Gebruikers toevoegen aan de rol Lab Creator**

   Raadpleeg de zelfstudie over [het toevoegen van gebruikers aan de lab creator-rol](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role) voor instructies.

   Zie ook de [handleiding voor het beheren van identiteit](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)voor meer informatie over de verschillende rollen die kunnen worden toegewezen aan gebruikers die labaccounts en -labs beheren.

3. **Verbinding maken met een vnet-collega**

   Raadpleeg de handleiding voor [het verbinden van het netwerk van uw lab met een peer VNet](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) voor instructies.

   Het kan ook nodig zijn om te verwijzen naar instructies voor [het configureren van het adresbereik van de lab-VM's.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)

4. **Afbeeldingen inschakelen en bekijken**

    Raadpleeg de handleiding voor [het inschakelen van Marketplace-afbeeldingen voor makers van labs](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images) voor instructies.

    Als u de inhoud van elke Marketplace-afbeelding wilt bekijken, klikt u op de afbeeldingsnaam.  Raadpleeg bijvoorbeeld de volgende schermafbeelding die de details voor de Ubuntu Data Science VM-afbeelding weergeeft:

    ![Marketplace-afbeeldingen bekijken](../media/setup-guide/review-marketplace-images.png)

    Als je een Gedeelde Afbeeldingengalerij hebt gekoppeld aan je labaccount en je aangepaste afbeeldingen wilt laten delen door makers van het lab, moet je vergelijkbare stappen uitvoeren zoals in de volgende schermafbeelding wordt weergegeven:

    ![Aangepaste afbeeldingen inschakelen in gedeelde afbeeldingsgalerie](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen:

- [Lab-accounts beheren](how-to-manage-lab-accounts.md)

- [Handleiding voor het instellen van klassikale laboratorium](setup-guide.md)
