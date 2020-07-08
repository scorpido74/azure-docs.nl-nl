---
title: Gids voor het instellen van een versneld Lab-account voor Azure Lab Services
description: Deze hand leiding helpt beheerders snel een Lab-account in te stellen voor gebruik op hun school.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c186560b27ebcb543a23785dc5fbc556614f64b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445420"
---
# <a name="lab-account-setup-guide"></a>Installatie handleiding voor het lab-account

Als eerste stap moeten beheerders een Lab-account instellen binnen uw Azure-abonnement. Een Lab-account is een container voor uw klassikale Labs en duurt slechts enkele minuten om in te stellen.

## <a name="understand-your-schools-lab-account-requirements"></a>Meer informatie over de vereisten van uw school lab-account

Als u wilt weten hoe u uw Lab-account kunt configureren op basis van de behoeften van uw school, moet u rekening houden met de volgende vragen.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Heb ik toegang tot een Azure-abonnement?

Als u een Lab-account wilt maken, moet u toegang hebben tot een Azure-abonnement dat is geconfigureerd voor uw school. Uw school heeft mogelijk een of meer abonnementen. U gebruikt een abonnement voor het beheren van facturering en beveiliging voor al uw Azure-resources en-services, met inbegrip van Lab-accounts.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Hoeveel lab-accounts moet er worden gemaakt?

Als u snel aan de slag wilt gaan, maakt u één Lab-account en maakt u later extra Lab-accounts. Zo kunt u uiteindelijk één Lab-account per afdeling hebben.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Wie moet eigen aren zijn en mede werkers van het lab-account?

Uw beheerders zijn doorgaans de eigen aren en mede werkers voor een Lab-account. Ze zijn verantwoordelijk voor het beheer van de beleids regels die van toepassing zijn op alle Labs in het lab-account. De persoon die het lab-account maakt, wordt automatisch een eigenaar. U kunt extra eigen aren en mede werkers toevoegen, doorgaans van de Azure Active Directory Azure AD-Tenant die aan uw abonnement is gekoppeld. Dit kan handig zijn bij het beheren van een Lab-account door de rol eigenaar of Inzender op het niveau van het lab-account toe te wijzen.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Wie kan Labs maken en beheren?

U kunt ervoor kiezen om de leden van uw beheerders en docenten te laten maken en beheren van Labs. Deze gebruikers (doorgaans van de Azure AD-Tenant die aan uw abonnement is gekoppeld) worden toegewezen aan de rol Lab Creator binnen het lab-account.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Wilt u Lab-makers de mogelijkheid geven om afbeeldingen op te slaan die kunnen worden gedeeld in Labs?

Een galerie met gedeelde installatie kopieën is een opslag plaats die u kunt gebruiken voor het opslaan en delen van installatie kopieën. Als u meerdere klassen hebt die dezelfde installatie kopieën nodig hebben, kunnen Lab-makers de installatie kopie eenmaal maken en deze delen in Labs. Om aan de slag te gaan, hebt u echter niet per se een galerie met gedeelde afbeeldingen nodig, omdat u er later altijd een kunt toevoegen.

Als u ' ja ' hebt geantwoord op deze vraag, moet u een galerie met gedeelde afbeeldingen maken of koppelen aan uw Lab-account. Als u ' Ik weet niet weet ' hebt beantwoord, kunt u deze beslissing uitstellen tot later.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Welke installatie kopieën in azure Marketplace worden gebruikt in uw klas lokaal?

Azure Marketplace biedt honderden installatie kopieën die u kunt inschakelen zodat Lab-makers de installatie kopie kunnen gebruiken om hun Lab te maken. Sommige installatie kopieën bevatten mogelijk alles wat een Lab al nodig heeft. In andere gevallen kunt u een afbeelding gebruiken als uitgangs punt, waarna de maker van het lab deze kan aanpassen door aanvullende toepassingen of hulpprogram ma's te installeren.

Als u niet weet welke installatie kopieën u moet gebruiken, kunt u dit later alsnog doen om ze weer in te scha kelen. Het is ook de beste manier om te zien welke installatie kopieën er beschikbaar zijn om eerst een Lab-account te maken. Hiermee krijgt u toegang tot, zodat u de lijst met beschik bare afbeeldingen en de bijbehorende inhoud kunt bekijken.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Moeten de virtuele machines van het lab toegang hebben tot andere Azure-of on-premises bronnen?

Wanneer u een Lab-account instelt, hebt u ook de optie peering met een virtueel netwerk. Houd rekening met de volgende vragen om te bepalen of u dit nodig hebt:

- **Moet u toegang bieden tot een licentie server?**
  
   Als u van plan bent om Azure Marketplace-installatie kopieën te gebruiken, worden de kosten van de licentie voor het besturings systeem gebundeld in de prijzen voor Lab-Services. Daarom hoeft u geen licenties voor het besturings systeem zelf op te geven. Voor aanvullende software en toepassingen die zijn geïnstalleerd, moet u echter een licentie opgeven.

- **Hebben de Lab-Vm's toegang nodig tot andere on-premises resources, zoals een bestands share of data base?**

   U maakt een virtueel netwerk om toegang te bieden tot on-premises resources, meestal door gebruik te maken van een site-naar-site-gateway van een virtueel netwerk. Als er geen virtueel netwerk is geconfigureerd, moet u hiervoor extra tijd investeren.

- **Moeten de Lab-Vm's toegang hebben tot andere Azure-resources die zich in een virtueel netwerk bevinden?**

   Als u toegang nodig hebt tot Azure-resources die *niet* zijn beveiligd binnen een virtueel netwerk, kunt u toegang krijgen tot deze bronnen via het open bare Internet, zonder dat u een peering hoeft te doen.

Als u ' ja ' hebt gereageerd op een of meer vragen, moet u het lab-account koppelen aan een virtueel netwerk. Als u ' Ik weet niet weet ' hebt beantwoord, kunt u deze beslissing uitstellen tot later. U kunt er altijd voor kiezen om een virtueel netwerk te peeren nadat u het lab-account hebt gemaakt.

## <a name="set-up-your-lab-account"></a>Uw Lab-account instellen

Nadat u de vereisten voor uw Lab-account hebt begrepen, kunt u deze instellen.

1. **Maak een Lab-account.** Raadpleeg de zelf studie over het [maken van een Lab-account](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) voor instructies.

   Wanneer u een Lab-account maakt, kan het handig zijn om vertrouwd te raken met de betrokken Azure-resources. Raadpleeg voor meer informatie de volgende artikelen:

   - [Abonnement](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Resourcegroep](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Lab-account](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Klas Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Een regio en locatie selecteren](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Naamgevings richtlijnen voor bronnen](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Gebruikers toevoegen aan de rol Lab Creator.** Zie [gebruikers toevoegen aan de rol Lab Creator](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)voor instructies.

   Zie ook de [hand leiding over het beheren van identiteit](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)voor meer informatie over de verschillende rollen die kunnen worden toegewezen aan gebruikers die Lab-accounts en-Labs gaan beheren.

3. **Verbinding maken met een virtueel netwerk op hetzelfde niveau.** Zie [verbinding maken tussen het netwerk van uw Lab en een virtueel netwerk van een peer](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)voor instructies.

   Mogelijk moet u ook verwijzen naar instructies over [het configureren van het adres bereik van de virtuele machine voor het lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Afbeeldingen inschakelen en controleren.** Zie [installatie kopieën van Azure Marketplace inschakelen voor Lab-makers](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)voor instructies.

   Als u de inhoud van elke Azure Marketplace-installatie kopie wilt bekijken, selecteert u de naam van de installatie kopie. Op de volgende scherm afbeelding ziet u bijvoorbeeld de details van de Ubuntu-Data Science VM:

   ![Scherm opname van Azure Marketplace-installatie kopieën controleren](./media/setup-guide/review-marketplace-images.png)

   Als u een galerie met gedeelde afbeeldingen hebt gekoppeld aan uw Lab-account en u aangepaste installatie kopieën wilt laten delen door Lab-makers, voltooit u de stappen die vergelijkbaar zijn met die in de volgende scherm afbeelding:

   ![Scherm opname van het inschakelen van aangepaste installatie kopieën in een galerie met gedeelde afbeeldingen](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Volgende stappen

- [Lab-accounts beheren](how-to-manage-lab-accounts.md)

- [Setup-hand leiding voor klas Lab](setup-guide.md)
