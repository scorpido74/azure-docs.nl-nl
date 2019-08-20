---
title: 'Azure Active Directory Domain Services: Problemen met waarschuwingen oplossen | Microsoft Docs'
description: Problemen met waarschuwingen voor Azure AD Domain Services oplossen
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 5a8f3401de0dc452193efbcf79aef87a19aed081
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617088"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services-problemen met waarschuwingen oplossen
Dit artikel bevat richt lijnen voor probleem oplossing voor waarschuwingen die mogelijk optreden op uw beheerde domein.


Kies de stappen voor het oplossen van problemen die overeenkomen met de ID of het bericht in de waarschuwing.

| **Waarschuwings-ID** | **Waarschuwings bericht** | **Resolutie** |
| --- | --- | :--- |
| AADDS001 | *Secure LDAP via internet is ingeschakeld voor het beheerde domein. Toegang tot poort 636 is echter niet vergrendeld met behulp van een netwerk beveiligings groep. Hierdoor kunnen gebruikers accounts in het beheerde domein worden blootgesteld aan wacht woorden voor het afdwingen van wachtwoord aanvallen.* | [Onjuiste beveiligde LDAP-configuratie](alert-ldaps.md) |
| AADDS100 | *De Azure AD-Directory die aan uw beheerde domein is gekoppeld, is mogelijk verwijderd. Het beheerde domein bevindt zich niet meer in een ondersteunde configuratie. Micro soft kan uw beheerde domein niet bewaken, beheren, patchen en synchroniseren.* | [Ontbrekende map](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services kan niet worden ingeschakeld in een Azure AD B2C map.* | [Azure AD B2C wordt uitgevoerd in deze map](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Een service-principal die vereist is voor een goede werking van Azure AD Domain Services, is verwijderd uit uw Azure AD-adres lijst. Deze configuratie is van invloed op de mogelijkheid van micro soft om uw beheerde domein te bewaken, te beheren, te patchen en te synchroniseren.* | [Ontbrekende Service-Principal](alert-service-principal.md) |
| AADDS103 | *Het IP-adres bereik voor het virtuele netwerk waarin u Azure AD Domain Services hebt ingeschakeld, bevindt zich in een openbaar IP-bereik. Azure AD Domain Services moet zijn ingeschakeld in een virtueel netwerk met een privé-IP-adres bereik. Deze configuratie is van invloed op de mogelijkheid van micro soft om uw beheerde domein te bewaken, te beheren, te patchen en te synchroniseren.* | [Het adres bevindt zich in een openbaar IP-bereik](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Micro soft kan de domein controllers voor dit beheerde domein niet bereiken. Dit kan gebeuren als een netwerk beveiligings groep (NSG) die in uw virtuele netwerk is geconfigureerd, de toegang tot het beheerde domein blokkeert. Een andere mogelijke reden is als er een door de gebruiker gedefinieerde route is die inkomend verkeer van Internet blokkeert.* | [Netwerk fout](alert-nsg.md) |
| AADDS105 | *De service-principal met de toepassings-ID ' d87dcbc6-a371-462e-88e3-28ad15ec4e64 ' is verwijderd en opnieuw gemaakt. De recreatie verloopt achter inconsistente machtigingen voor Azure AD Domain Services resources die nodig zijn om uw beheerde domein te onderhouden. De synchronisatie van wacht woorden op uw beheerde domein kan worden beïnvloed.* | [De toepassing voor wachtwoord synchronisatie is verouderd](alert-service-principal.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *Uw Azure-abonnement dat is gekoppeld aan uw beheerde domein, is verwijderd.  Azure AD Domain Services moet een actief abonnement zijn om goed te kunnen werken.* | [Het Azure-abonnement is niet gevonden](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *Uw Azure-abonnement dat is gekoppeld aan uw beheerde domein, is niet actief.  Azure AD Domain Services moet een actief abonnement zijn om goed te kunnen werken.* | [Het Azure-abonnement is uitgeschakeld](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *Het abonnement dat door Azure AD Domain Services wordt gebruikt, is verplaatst naar een andere map. Azure AD Domain Services moet een actief abonnement hebben in dezelfde map om goed te kunnen functioneren.* | [Verplaatste directory's van abonnement](#aadds108-subscription-moved-directories) |
| AADDS109 | *Een resource die wordt gebruikt voor uw beheerde domein, is verwijderd. Deze resource is nodig om Azure AD Domain Services goed te laten functioneren.* | [Een resource is verwijderd](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *Het subnet dat is geselecteerd voor de implementatie van Azure AD Domain Services, is vol en heeft geen ruimte voor de extra domein controller die moet worden gemaakt.* | [Het subnet is vol](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | *Een service-principal die Azure AD Domain Services gebruikt voor het onderhouden van uw domein is niet gemachtigd om resources te beheren in het Azure-abonnement. De Service-Principal moet machtigingen krijgen om uw beheerde domein te onderhouden.* | [Service-Principal niet toegestaan](#aadds111-service-principal-unauthorized) |
| AADDS112 | *Er is vastgesteld dat het subnet van het virtuele netwerk in dit domein mogelijk niet voldoende IP-adressen heeft. Azure AD Domain Services moet mini maal twee beschik bare IP-adressen hebben in het subnet waarin deze is ingeschakeld. U kunt het beste ten minste 3-5 reserved IP-adressen binnen het subnet hebben. Dit kan zijn gebeurd als andere virtuele machines worden geïmplementeerd in het subnet, waardoor het aantal beschik bare IP-adressen wordt uitgeput of als er een beperking geldt voor het aantal beschik bare IP-adressen in het subnet.* | [Onvoldoende IP-adressen](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *De resources die door Azure AD Domain Services worden gebruikt, zijn in een onverwachte status gedetecteerd en kunnen niet worden hersteld.* | [Resources kunnen niet worden hersteld](#aadds113-resources-are-unrecoverable) |
| AADDS114 | *Het subnet dat is geselecteerd voor de implementatie van Azure AD Domain Services, is ongeldig en kan niet worden gebruikt.* | [Subnet ongeldig](#aadds114-subnet-invalid) |
| AADDS115 | *Een of meer netwerk bronnen die door het beheerde domein worden gebruikt, kunnen niet worden uitgevoerd omdat het doel bereik is vergrendeld.* | [Resources zijn vergrendeld](#aadds115-resources-are-locked) |
| AADDS116 | *Een of meer netwerk bronnen die door het beheerde domein worden gebruikt, kunnen niet worden uitgevoerd vanwege beleids beperking (en).* | [Resources zijn onbruikbaar](#aadds116-resources-are-unusable) |
| AADDS500 | *Het beheerde domein is voor het laatst gesynchroniseerd met Azure AD op [date]. Gebruikers kunnen zich mogelijk niet aanmelden bij het beheerde domein of groepslid maatschappen zijn mogelijk niet gesynchroniseerd met Azure AD.* | [De synchronisatie is niet in een tijdje gebeurd](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *Het beheerde domein is voor het laatst een back-up gemaakt op [date].* | [Er is geen back-up gemaakt tijdens een tijdje](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *Het beveiligde LDAP-certificaat voor het beheerde domein verloopt op [date].* | [Beveiligd LDAP-certificaat verloopt](alert-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *Het beheerde domein is onderbroken omdat het Azure-abonnement dat is gekoppeld aan het domein, niet actief is.* | [Opschorting vanwege uitgeschakeld abonnement](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *Het beheerde domein is onderbroken vanwege een ongeldige configuratie. De service kan de domein controllers voor uw beheerde domein lange tijd niet beheren, patchen of bijwerken.* | [Opschorting vanwege een ongeldige configuratie](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100: Ontbrekende map
**Waarschuwings bericht:**

*De Azure AD-Directory die aan uw beheerde domein is gekoppeld, is mogelijk verwijderd. Het beheerde domein bevindt zich niet meer in een ondersteunde configuratie. Micro soft kan uw beheerde domein niet bewaken, beheren, patchen en synchroniseren.*

**Opgelost**

Deze fout wordt meestal veroorzaakt door het onterecht verplaatsen van uw Azure-abonnement naar een nieuwe Azure AD-map en het verwijderen van de oude Azure AD-adres lijst die nog steeds is gekoppeld aan Azure AD Domain Services.

Deze fout kan niet worden hersteld. U kunt dit oplossen door [uw bestaande beheerde domein te verwijderen](delete-aadds.md) en opnieuw te maken in de nieuwe map. Als u problemen ondervindt met verwijderen, neemt u contact op met het Azure Active Directory Domain Services-product team [voor ondersteuning](contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C wordt uitgevoerd in deze map
**Waarschuwings bericht:**

*Azure AD Domain Services kan niet worden ingeschakeld in een Azure AD B2C map.*

**Opgelost**

>[!NOTE]
>Als u Azure AD Domain Services wilt blijven gebruiken, moet u uw Azure AD Domain Services-exemplaar opnieuw maken in een andere map dan Azure AD B2C.

Voer de volgende stappen uit om uw service te herstellen:

1. [Verwijder uw beheerde domein](delete-aadds.md) uit uw bestaande Azure AD-adres lijst.
2. Maak een nieuwe map die geen Azure AD B2C Directory is.
3. Volg de [aan](tutorial-create-instance.md) de slag-hand leiding voor het opnieuw maken van een beheerd domein.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Het adres bevindt zich in een openbaar IP-bereik

**Waarschuwings bericht:**

*Het IP-adres bereik voor het virtuele netwerk waarin u Azure AD Domain Services hebt ingeschakeld, bevindt zich in een openbaar IP-bereik. Azure AD Domain Services moet zijn ingeschakeld in een virtueel netwerk met een privé-IP-adres bereik. Deze configuratie is van invloed op de mogelijkheid van micro soft om uw beheerde domein te bewaken, te beheren, te patchen en te synchroniseren.*

**Opgelost**

> [!NOTE]
> Om dit probleem op te lossen, moet u uw bestaande beheerde domein verwijderen en het opnieuw maken in een virtueel netwerk met een privé-IP-adres bereik. Dit proces is verstoord.

Lees voordat u begint de sectie **persoonlijke IP v4-adres ruimte** in [dit artikel](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

Binnen het virtuele netwerk kunnen computers aanvragen indienen voor Azure-resources die zich in hetzelfde IP-adres bereik bevinden als de instellingen die zijn geconfigureerd voor het subnet. Omdat het virtuele netwerk echter is geconfigureerd voor dit bereik, worden deze aanvragen gerouteerd binnen het virtuele netwerk en worden de beoogde webbronnen niet bereikt. Deze configuratie kan leiden tot onvoorspelbare fouten met Azure AD Domain Services.

**Als u het IP-adres bereik in het Internet hebt dat is geconfigureerd in het virtuele netwerk, kan deze waarschuwing worden genegeerd. Azure AD Domain Services kan echter niet door voeren naar de [Sla](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] met deze configuratie, omdat deze kan leiden tot onvoorspelbare fouten.**


1. [Verwijder uw beheerde domein](delete-aadds.md) uit uw Directory.
2. Het IP-adres bereik voor het subnet herstellen
   1. Ga naar de [pagina Virtuele netwerken op de Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Selecteer het virtuele netwerk dat u voor de Azure AD Domain Services wilt gebruiken.
   3. Klik op **adres ruimte** onder instellingen
   4. Werk het adres bereik bij door op het bestaande adres bereik te klikken en dit te bewerken of een extra adres bereik toe te voegen. Zorg ervoor dat het nieuwe adres bereik zich in een privé-IP-bereik bevindt. Sla uw wijzigingen op.
   5. Klik op **subnetten** in de linkernavigatiebalk.
   6. Klik op het subnet dat u wilt bewerken in de tabel.
   7. Werk het adres bereik bij en sla uw wijzigingen op.
3. Volg [de hand leiding aan de slag met Azure AD Domain Services](tutorial-create-instance.md) om uw beheerde domein opnieuw te maken. Zorg ervoor dat u een virtueel netwerk met een privé-IP-adres bereik kiest.
4. Als u een domein wilt toevoegen aan uw nieuwe domein, volgt u [deze hand leiding](join-windows-vm.md).
8. Om ervoor te zorgen dat de waarschuwing wordt opgelost, controleert u de status van uw domein in twee uur.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Uw Azure-abonnement is niet gevonden

**Waarschuwings bericht:**

*Uw Azure-abonnement dat is gekoppeld aan uw beheerde domein, is verwijderd.  Azure AD Domain Services moet een actief abonnement zijn om goed te kunnen werken.*

**Opgelost**

Azure AD Domain Services moet een abonnement hebben op de functie en kan niet worden verplaatst naar een ander abonnement. Omdat het Azure-abonnement waaraan uw beheerde domein is gekoppeld, is verwijderd, moet u opnieuw een Azure-abonnement en Azure AD Domain Services maken.

1. Een Azure-abonnement maken
2. [Verwijder uw beheerde domein](delete-aadds.md) uit uw bestaande Azure AD-adres lijst.
3. Volg de [aan](tutorial-create-instance.md) de slag-hand leiding voor het opnieuw maken van een beheerd domein.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Het Azure-abonnement is uitgeschakeld

**Waarschuwings bericht:**

*Uw Azure-abonnement dat is gekoppeld aan uw beheerde domein, is niet actief.  Azure AD Domain Services moet een actief abonnement zijn om goed te kunnen werken.*

**Opgelost**


1. [Verleng uw Azure-abonnement](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Zodra het abonnement is vernieuwd, ontvangt Azure AD Domain Services een melding van Azure om uw beheerde domein opnieuw in te scha kelen.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Verplaatste directory's van abonnement

**Waarschuwings bericht:**

*Het abonnement dat door Azure AD Domain Services wordt gebruikt, is verplaatst naar een andere map. Azure AD Domain Services moet een actief abonnement hebben in dezelfde map om goed te kunnen functioneren.*

**Opgelost**

U kunt het abonnement dat is gekoppeld aan Azure AD Domain Services, verplaatsen naar de vorige map of u moet [uw beheerde domein verwijderen](delete-aadds.md) uit de bestaande map en het opnieuw maken in de gekozen map (met een nieuw abonnement of Wijzig de map waarin uw Azure AD Domain Services-exemplaar zich bevindt).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Kan geen resources vinden voor uw beheerde domein

**Waarschuwings bericht:**

*Een resource die wordt gebruikt voor uw beheerde domein, is verwijderd. Deze resource is nodig om Azure AD Domain Services goed te laten functioneren.*

**Opgelost**

Azure AD Domain Services maakt specifieke resources tijdens de implementatie om goed te kunnen functioneren, met inbegrip van open bare IP-adressen, Nic's en een load balancer. Als een van de namen worden verwijderd, wordt de status van uw beheerde domein niet ondersteund, zodat uw domein niet kan worden beheerd. Deze waarschuwing wordt weer gegeven wanneer iemand die de Azure AD Domain Services resources kan bewerken, een benodigde resource verwijdert. In de volgende stappen wordt uitgelegd hoe u uw beheerde domein herstelt.

1. Ga naar de pagina Azure AD Domain Services status
   1.    Reis naar de [Azure AD Domain Services pagina](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) in de Azure Portal.
   2.    Klik in de linkernavigatiebalk op **status**
2. Controleren of de waarschuwing minder dan 4 uur oud is
   1.    Klik op de pagina status op de waarschuwing met de ID **AADDS109**
   2.    De waarschuwing heeft een tijds tempel voor de eerste keer dat deze is gevonden. Als de tijds tempel minder dan vier uur geleden is, is er een kans dat Azure AD Domain Services de verwijderde resource opnieuw kunt maken.
3. Als de waarschuwing meer dan vier uur oud is, heeft het beheerde domein een onherstelbare status. U moet Azure AD Domain Services verwijderen en opnieuw maken.


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: Het subnet dat is gekoppeld aan uw beheerde domein, is vol

**Waarschuwings bericht:**

*Het subnet dat is geselecteerd voor de implementatie van Azure AD Domain Services, is vol en heeft geen ruimte voor de extra domein controller die moet worden gemaakt.*

**Opgelost**

Deze fout kan niet worden hersteld. Als u dit wilt oplossen, moet u [uw bestaande beheerde domein verwijderen](delete-aadds.md) en [uw beheerde domein opnieuw maken](tutorial-create-instance.md)

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Service-Principal niet toegestaan

**Waarschuwings bericht:**

*Een service-principal die Azure AD Domain Services gebruikt voor het onderhouden van uw domein is niet gemachtigd om resources te beheren in het Azure-abonnement. De Service-Principal moet machtigingen krijgen om uw beheerde domein te onderhouden.*

**Opgelost**

Onze service-principals hebben toegang nodig om resources te kunnen beheren en maken op uw beheerde domein. Iemand heeft de toegang tot de Service-Principal geweigerd en nu kan de resources niet worden beheerd. Volg de stappen om toegang te verlenen aan uw service-principal.

1. Meer informatie over [RBAC-besturings element en het verlenen van toegang tot toepassingen op de Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. Controleer de toegang die de service-principal met de ```abba844e-bc0e-44b0-947a-dc74e5d09022``` id heeft en verleen de toegang die op een eerdere datum is geweigerd.


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Er is onvoldoende IP-adres in het beheerde domein

**Waarschuwings bericht:**

*Er is vastgesteld dat het subnet van het virtuele netwerk in dit domein mogelijk niet voldoende IP-adressen heeft. Azure AD Domain Services moet mini maal twee beschik bare IP-adressen hebben in het subnet waarin deze is ingeschakeld. U kunt het beste ten minste 3-5 reserved IP-adressen binnen het subnet hebben. Dit kan zijn gebeurd als andere virtuele machines worden geïmplementeerd in het subnet, waardoor het aantal beschik bare IP-adressen wordt uitgeput of als er een beperking geldt voor het aantal beschik bare IP-adressen in het subnet.*

**Opgelost**

1. Verwijder uw beheerde domein uit uw Tenant.
2. Het IP-adres bereik voor het subnet herstellen
   1. Ga naar de [pagina Virtuele netwerken op de Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Selecteer het virtuele netwerk dat u voor de Azure AD Domain Services wilt gebruiken.
   3. Klik op **adres ruimte** onder instellingen
   4. Werk het adres bereik bij door op het bestaande adres bereik te klikken en dit te bewerken of een extra adres bereik toe te voegen. Sla uw wijzigingen op.
   5. Klik op **subnetten** in de linkernavigatiebalk.
   6. Klik op het subnet dat u wilt bewerken in de tabel.
   7. Werk het adres bereik bij en sla uw wijzigingen op.
3. Volg [de hand leiding aan de slag met Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) om uw beheerde domein opnieuw te maken. Zorg ervoor dat u een virtueel netwerk met een privé-IP-adres bereik kiest.
4. Als u een domein wilt toevoegen aan uw nieuwe domein, volgt u [deze hand leiding](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).
5. Controleer de status van uw domein in twee uur om ervoor te zorgen dat u de juiste stappen hebt voltooid.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Resources kunnen niet worden hersteld

**Waarschuwings bericht:**

*De resources die door Azure AD Domain Services worden gebruikt, zijn in een onverwachte status gedetecteerd en kunnen niet worden hersteld.*

**Opgelost**

Deze fout kan niet worden hersteld. Als u dit wilt oplossen, moet u [uw bestaande beheerde domein verwijderen](delete-aadds.md) en [uw beheerde domein opnieuw maken](tutorial-create-instance.md).

## <a name="aadds114-subnet-invalid"></a>AADDS114: Subnet ongeldig

**Waarschuwings bericht:**

*Het subnet dat is geselecteerd voor de implementatie van Azure AD Domain Services, is ongeldig en kan niet worden gebruikt.*

**Opgelost**

Deze fout kan niet worden hersteld. Als u dit wilt oplossen, moet u [uw bestaande beheerde domein verwijderen](delete-aadds.md) en [uw beheerde domein opnieuw maken](tutorial-create-instance.md).

## <a name="aadds115-resources-are-locked"></a>AADDS115: Resources zijn vergrendeld

**Waarschuwings bericht:**

*Een of meer netwerk bronnen die door het beheerde domein worden gebruikt, kunnen niet worden uitgevoerd omdat het doel bereik is vergrendeld.*

**Opgelost**

1.  Bekijk de bewerkings logboeken van de Resource Manager op de netwerk bronnen (dit moet informatie geven over welke vergren deling verhindert dat wijzigingen worden gewijzigd).
2.  Verwijder de vergren delingen van de resources, zodat de Azure AD Domain Services Service-Principal hierop kan worden toegepast.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Resources zijn onbruikbaar

**Waarschuwings bericht:**

*Een of meer netwerk bronnen die door het beheerde domein worden gebruikt, kunnen niet worden uitgevoerd vanwege beleids beperking (en).*

**Opgelost**

1.  Controleer de bewerkings logboeken van de Resource Manager op de netwerk resources voor uw beheerde domein.
2.  Verzwak de beleids beperkingen op de resources zodat de AAD-DS-Service-Principal hierop kan worden gebruikt.



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: De synchronisatie is niet in een tijdje voltooid

**Waarschuwings bericht:**

*Het beheerde domein is voor het laatst gesynchroniseerd met Azure AD op [date]. Gebruikers kunnen zich mogelijk niet aanmelden bij het beheerde domein of groepslid maatschappen zijn mogelijk niet gesynchroniseerd met Azure AD.*

**Opgelost**

[Controleer de status van uw domein](check-health.md) op waarschuwingen die kunnen wijzen op problemen in uw configuratie van uw beheerde domein. Soms kan de mogelijkheid van micro soft om uw beheerde domein te synchroniseren, worden geblokkeerd door problemen met uw configuratie. Als u waarschuwingen kunt oplossen, wacht u twee uur en controleert u of de synchronisatie is voltooid.

Hier volgen enkele veelvoorkomende redenen waarom synchronisatie stopt voor beheerde domeinen:
- De netwerk verbinding is geblokkeerd op het beheerde domein. Lees voor meer informatie over het controleren van uw netwerk op problemen het [oplossen van problemen met netwerk beveiligings groepen](alert-nsg.md) en [netwerk vereisten voor Azure AD Domain Services](network-considerations.md).
-  Wachtwoord synchronisatie is nooit ingesteld of voltooid. Lees [dit artikel](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)voor informatie over het instellen van wachtwoord synchronisatie.

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Er is geen back-up gemaakt tijdens een tijdje

**Waarschuwings bericht:**

*Het beheerde domein is voor het laatst een back-up gemaakt op [date].*

**Opgelost**

[Controleer de status van uw domein](check-health.md) op waarschuwingen die kunnen wijzen op problemen in uw configuratie van uw beheerde domein. Soms kan de mogelijkheid van micro soft om een back-up van uw beheerde domein te maken, worden geblokkeerd door problemen met uw configuratie. Als u waarschuwingen kunt oplossen, wacht u twee uur en controleert u of er een back-up is voltooid.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Opschorting vanwege uitgeschakeld abonnement

**Waarschuwings bericht:**

*Het beheerde domein is onderbroken omdat het Azure-abonnement dat is gekoppeld aan het domein, niet actief is.*

**Opgelost**

> [!WARNING]
> Als uw beheerde domein gedurende lange tijd wordt onderbroken, wordt het gevaar om te worden verwijderd. U kunt de onderbreking het beste zo snel mogelijk aanpakken. Ga naar [dit artikel](suspension.md)voor meer informatie.

[Vernieuw uw Azure-abonnement](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) dat is gekoppeld aan uw beheerde domein om uw service te herstellen.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Opschorting vanwege een ongeldige configuratie

**Waarschuwings bericht:**

*Het beheerde domein is onderbroken vanwege een ongeldige configuratie. De service kan de domein controllers voor uw beheerde domein lange tijd niet beheren, patchen of bijwerken.*

**Opgelost**

> [!WARNING]
> Als uw beheerde domein gedurende lange tijd wordt onderbroken, wordt het gevaar om te worden verwijderd. U kunt de onderbreking het beste zo snel mogelijk aanpakken. Ga naar [dit artikel](suspension.md)voor meer informatie.

[Controleer de status van uw domein](check-health.md) op waarschuwingen die kunnen wijzen op problemen in uw configuratie van uw beheerde domein. Als u een van deze waarschuwingen kunt oplossen, doet u dat. Neem contact op met de ondersteuning om uw abonnement opnieuw in te scha kelen.


## <a name="contact-us"></a>Contact opnemen
Neem contact op met het product team van Azure Active Directory Domain Services om [feedback te delen of voor ondersteuning](contact-us.md).
