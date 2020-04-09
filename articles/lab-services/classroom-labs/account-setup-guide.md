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
ms.openlocfilehash: 8fcc46487e7f7c2d075639f10a30cae9950ff31b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879578"
---
# <a name="lab-account-setup-guide"></a>Handleiding voor het instellen van lab-account

Als eerste stap moeten beheerders een labaccount instellen binnen uw Azure-abonnement. Een labaccount is een container voor uw klassikale laboratoria en het duurt slechts enkele minuten om het in te stellen.

## <a name="understand-your-schools-lab-account-requirements"></a>Begrijp de vereisten van uw school voor labaccount

Als u wilt weten hoe u uw labaccount configureren op basis van de behoeften van uw school, moet u deze vragen in overweging nemen.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Heb ik toegang tot een Azure-abonnement?

Als u een labaccount wilt maken, hebt u toegang nodig tot een Azure-abonnement dat is geconfigureerd voor uw school. Uw school heeft mogelijk een of meer abonnementen. U gebruikt een abonnement om facturering en beveiliging te beheren voor al uw Azure-bronnen en -services, inclusief labaccounts.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Hoeveel labaccounts moeten worden gemaakt?

Maak snel aan de slag om snel aan de slag te gaan en maak later extra labaccounts aan als dat nodig is. U bijvoorbeeld uiteindelijk één labaccount per afdeling hebben.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Wie moeten eigenaren en bijdragers van het lab account?

Uw beheerders zijn meestal de eigenaren en bijdragers voor een labaccount. Zij zijn verantwoordelijk voor het beheer van het beleid dat van toepassing is op alle labs in het labaccount. De persoon die het labaccount maakt, is automatisch eigenaar. U extra eigenaren en bijdragers toevoegen, meestal vanuit de Azure AD-tenant (Azure Directory) die aan uw abonnement is gekoppeld. Dit kan handig zijn om een labaccount te beheren door de eigenaar of de rol van de bijdrager op het labaccountniveau toe te wijsen.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Wie mag labs maken en beheren?

U ervoor kiezen om uw beheerders en faculteitsleden labs te laten maken en beheren. Deze gebruikers (meestal vanuit de Azure AD-tenant die aan uw abonnement is gekoppeld) worden toegewezen aan de labcreator-rol binnen het labaccount.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Wilt u makers van laboratoria de mogelijkheid bieden om afbeeldingen op te slaan die in laboratoria kunnen worden gedeeld?

Een gedeelde afbeeldingsgalerie is een opslagplaats die u gebruiken voor het opslaan en delen van afbeeldingen. Als je meerdere klassen hebt die dezelfde afbeeldingen nodig hebben, kunnen makers van laboratoria de afbeelding één keer maken en delen in laboratoria. Om aan de slag te gaan, hebt u echter niet per se een gedeelde afbeeldingsgalerie nodig, omdat u er altijd later een toevoegen.

Als je 'ja' hebt geantwoord op deze vraag, moet je een gedeelde afbeeldingsgalerie aan maken of koppelen aan je labaccount. Als je antwoordde : "Ik weet het niet", kun je dit besluit uitstellen tot later.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Welke afbeeldingen in Azure Marketplace gebruiken uw klaslabs?

Azure Marketplace biedt honderden afbeeldingen die u inschakelen, zodat makers van het lab de afbeelding kunnen gebruiken voor het maken van hun lab. Sommige afbeeldingen kunnen alles bevatten wat een lab al nodig heeft. In andere gevallen u een afbeelding als uitgangspunt gebruiken en vervolgens kan de maker van het lab deze aanpassen door extra toepassingen of hulpprogramma's te installeren.

Als u niet weet welke afbeeldingen u moet gebruiken, u hier later altijd op terugkomen om ze in te schakelen. Ook is de beste manier om te zien welke afbeeldingen beschikbaar zijn, door eerst een labaccount aan te maken. Dit geeft u toegang, zodat u de lijst met beschikbare afbeeldingen en de inhoud ervan bekijken.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Moeten de virtuele machines van het lab toegang hebben tot andere Azure- of on-premises bronnen?

Wanneer u een labaccount instelt, hebt u ook de mogelijkheid om te peeren met een virtueel netwerk. Als u wilt beslissen of u dit nodig hebt, u rekening houden met de volgende vragen:

- **Moet u toegang bieden tot een licentieserver?**
  
   Als u Azure Marketplace-afbeeldingen wilt gebruiken, worden de kosten van de licentie voor het besturingssysteem gebundeld in de prijzen voor labservices. Daarom hoeft u geen licenties te verstrekken voor het besturingssysteem zelf. Voor extra software en toepassingen die zijn geïnstalleerd, moet u echter een licentie bieden.

- **Hebben de VM's van het lab toegang nodig tot andere on-premises bronnen, zoals een bestandsshare of database?**

   U maakt een virtueel netwerk om toegang te bieden tot on-premises bronnen, meestal met behulp van een site-to-site virtuele netwerkgateway. Als u geen virtueel netwerk hebt geconfigureerd, moet u hiervoor extra tijd investeren.

- **Hebben de VM's van het lab toegang nodig tot andere Azure-bronnen die zich binnen een virtueel netwerk bevinden?**

   Als u toegang nodig hebt tot Azure-bronnen die *niet* zijn beveiligd binnen een virtueel netwerk, u toegang krijgen tot deze bronnen via het openbare internet, zonder peering te doen.

Als u "ja" hebt geantwoord op een of meer vragen, moet u het lab-account naar een virtueel netwerk turen. Als je antwoordde : Ik weet het niet, dan u dit besluit uitstellen tot later. U er altijd voor kiezen om een virtueel netwerk te peeren nadat u het labaccount hebt gemaakt.

## <a name="set-up-your-lab-account"></a>Uw labaccount instellen

Nadat u de vereisten voor uw labaccount hebt begrepen, bent u klaar om het in te stellen.

1. **Maak je labaccount aan.** Raadpleeg de zelfstudie over [het maken van een labaccount](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) voor instructies.

   Wanneer u een labaccount maakt, u het nuttig vinden om vertrouwd te raken met de Azure-bronnen die ermee gemoeid zijn. Raadpleeg voor meer informatie de volgende artikelen:

   - [Abonnement](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Resourcegroep](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Lab-account](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Klaslokaal lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Een regio en locatie selecteren](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Naamgevingsrichtlijnen voor resources](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Gebruikers toevoegen aan de rol van de maker van het lab.** Zie Gebruikers [toevoegen aan de rol van de maker van het lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)voor instructies.

   Zie ook de [handleiding voor het beheren van identiteit](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)voor meer informatie over de verschillende rollen die kunnen worden toegewezen aan gebruikers die labaccounts en labs beheren.

3. **Verbinding maken met een virtueel netwerk met een peer.** Zie voor instructies het [netwerk van uw lab verbinden met een virtueel netwerk met een peer.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)

   Mogelijk moet u ook verwijzen naar instructies voor [het configureren van het adresbereik van de vm's in het lab.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)

4. **Afbeeldingen inschakelen en bekijken.** Zie Azure [Marketplace-afbeeldingen inschakelen voor makers van labservices voor](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)instructies.

   Als u de inhoud van elke Azure Marketplace-afbeelding wilt bekijken, selecteert u de naam van de afbeelding. In de volgende schermafbeelding worden bijvoorbeeld de details voor de Ubuntu Data Science VM-afbeelding weergegeven:

   ![Schermafbeelding van Azure Marketplace-afbeeldingen bekijken](../media/setup-guide/review-marketplace-images.png)

   Als u een gedeelde afbeeldingsgalerie aan uw labaccount hebt gekoppeld en aangepaste afbeeldingen wilt kunnen delen door makers van het lab, voert u stappen uit die vergelijkbaar zijn met de stappen die worden weergegeven in de volgende schermafbeelding:

   ![Schermafbeelding van Aangepaste afbeeldingen inschakelen in een gedeelde afbeeldingsgalerie](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Volgende stappen

- [Lab-accounts beheren](how-to-manage-lab-accounts.md)

- [Handleiding voor het instellen van klassikale laboratorium](setup-guide.md)
