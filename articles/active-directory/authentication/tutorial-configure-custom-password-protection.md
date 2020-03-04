---
title: Aangepaste Azure Active Directory-wachtwoord beveiligings lijsten configureren
description: In deze zelf studie leert u hoe u aangepaste lijsten met verboden wacht woorden kunt configureren voor Azure Active Directory om veelvoorkomende woorden in uw omgeving te beperken.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4abb15462689470c87e9cf5ba8d5be8af2e45bfd
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252843"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Zelf studie: aangepaste verboden wacht woorden voor Azure Active Directory wachtwoord beveiliging configureren

Gebruikers maken vaak wacht woorden die gebruikmaken van veelgebruikte lokale woorden, zoals een school, een sport team of een beroemde persoon. Deze wacht woorden zijn gemakkelijk te raden en zwak tegen Woordenboek aanvallen. Voor het afdwingen van sterke wacht woorden in uw organisatie, kunt u met de aangepaste lijst met geblokkeerde inconsistenties van de Azure Active Directory (Azure AD) specifieke teken reeksen toevoegen om te evalueren en blok keren. Een aanvraag voor het wijzigen van het wacht woord is mislukt als er een overeenkomst is gevonden in de lijst met aangepaste verboden wacht woorden.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Aangepaste verboden wacht woorden inschakelen
> * Vermeldingen toevoegen aan de lijst met aangepaste verboden wacht woorden
> * Wachtwoord wijzigingen testen met een verboden wacht woord

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources en bevoegdheden nodig om deze zelf studie te volt ooien:

* Een werkende Azure AD-tenant waarop minimaal een proeflicentie is ingeschakeld.
    * Maak indien nodig [een gratis versie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een account met *globale beheerders* bevoegdheden.
* Een niet-beheerders gebruiker met een wacht woord dat u kent, zoals *test User*. In deze zelf studie kunt u een wachtwoord wijzigings gebeurtenis testen met dit account.
    * Als u een gebruiker wilt maken, raadpleegt u [Quick Start: nieuwe gebruikers toevoegen aan Azure Active Directory](../add-users-azure-active-directory.md).
    * Als u de bewerking voor het wijzigen van het wacht woord wilt testen met een verboden wacht woord, moet de Azure AD-Tenant worden [geconfigureerd voor selfservice voor het opnieuw instellen van een wacht woord](tutorial-enable-sspr.md).

## <a name="what-are-banned-password-lists"></a>Wat zijn verboden wachtwoord lijsten?

Azure AD bevat een algemene lijst met geblokkeerde wacht woorden. De inhoud van de lijst met globale verboden wacht woorden is niet gebaseerd op een externe gegevens bron. In plaats daarvan is de algemene lijst met verboden wacht woorden gebaseerd op de lopende resultaten van de telemetrie en analyse van Azure AD-beveiliging. Wanneer een gebruiker of beheerder hun referenties probeert te wijzigen of opnieuw in te stellen, wordt het gewenste wacht woord gecontroleerd op basis van de lijst met verboden wacht woorden. De aanvraag voor het wijzigen van het wacht woord is mislukt als er een overeenkomst is gevonden in de lijst met globale verboden wacht woorden.

U kunt ook een aangepaste lijst met geblokkeerde wacht woorden definiëren om u flexibiliteit te bieden bij het toestaan van het toegestane wacht woord. De aangepaste lijst met geblokkeerde wacht woorden werkt samen met de lijst globaal verboden wacht woorden af voor het afdwingen van een sterk wacht woord in uw organisatie. Organisatie-specifieke voor waarden kunnen worden toegevoegd aan de lijst met aangepaste geblokkeerde wacht woorden, zoals de volgende voor beelden:

* Merk namen
* Product namen
* Locaties, zoals het hoofd kantoor van het bedrijf
* Bedrijfsspecifieke interne termen
* Afkortingen met specifieke bedrijfs betekenis

Wanneer een gebruiker probeert om een wacht woord opnieuw in te stellen op een waarde in de lijst globaal of aangepast verboden wacht woord, zien ze een van de volgende fout berichten:

* *Uw wacht woord bevat helaas een woord, woord groep of patroon waarmee uw wacht woord gemakkelijk kan worden geraden. Probeer het opnieuw met een ander wacht woord.*
* *Helaas kunt u dit wacht woord niet gebruiken omdat het woorden of tekens bevat die door uw beheerder zijn geblokkeerd. Probeer het opnieuw met een ander wacht woord.*

De aangepaste lijst met verboden wacht woorden is beperkt tot een maximum van 1000 voor waarden. Het is niet ontworpen om grote lijsten met wacht woorden te blok keren. Als u de voor delen van de aangepaste lijst met geblokkeerde wacht woorden wilt maximaliseren, raadpleegt u het overzicht van de [aangepaste lijst met verboden wacht woorden](concept-password-ban-bad.md#custom-banned-password-list) en de [algoritme voor wachtwoord evaluatie](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="configure-custom-banned-passwords"></a>Aangepaste verboden wacht woorden configureren

Laten we de aangepaste lijst met uitgesloten wacht woorden inschakelen en enkele vermeldingen toevoegen. U kunt op elk gewenst moment aanvullende vermeldingen toevoegen aan de lijst met aangepaste geblokkeerde wacht woorden.

Voer de volgende stappen uit om de aangepaste lijst met uitgesloten wacht woorden in te scha kelen en vermeldingen toe te voegen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account met *globale beheerders* machtigingen.
1. Zoek en selecteer **Azure Active Directory**en kies vervolgens **beveiliging** in het menu aan de linkerkant.
1. Selecteer in de menu-header beheren **verificatie methoden**en vervolgens **wachtwoord beveiliging**.
1. Stel de optie voor het **afdwingen van aangepaste lijst** in op *Ja*.
1. Teken reeksen toevoegen aan de **lijst met aangepaste geblokkeerde wacht woorden**, één teken reeks per regel. De volgende overwegingen en beperkingen zijn van toepassing op de aangepaste lijst met uitgesloten wacht woorden:

    * De aangepaste lijst met verboden wacht woorden kan Maxi maal 1000 voor waarden bevatten.
    * De aangepaste lijst met verboden wacht woorden is hoofdletter gevoelig.
    * De aangepaste lijst met geblokkeerde wacht woorden beschouwt veelvoorkomende teken vervanging, zoals "o" en "0" of "a" en "@".
    * De minimum lengte van de teken reeks is vier tekens en de maximum waarde is 16 tekens.

    Geef uw eigen aangepaste wacht woorden op voor verbieden, zoals wordt weer gegeven in het volgende voor beeld

    [![](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png "Modify the custom banned password list under Authentication Methods in the Azure portal")](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. De optie voor het **inschakelen van wachtwoord beveiliging op Windows Server Active Directory** op *Nee*.
1. Selecteer **Opslaan**om de aangepaste verboden wacht woorden en uw vermeldingen in te scha kelen.

Het kan enkele uren duren voordat updates voor de aangepaste lijst met geblokkeerde wacht woorden worden toegepast.

Voor een hybride omgeving kunt u [Azure AD-wachtwoord beveiliging ook implementeren in een on-premises omgeving](howto-password-ban-bad-on-premises-deploy.md). Dezelfde algemene en aangepaste lijst met verboden wacht woorden worden gebruikt voor zowel Cloud-als on-premises aanvragen voor het wijzigen van wacht woorden.

## <a name="test-custom-banned-password-list"></a>Aangepaste lijst met verboden wacht woorden testen

Als u de aangepaste lijst met geblokkeerde wacht woorden in actie wilt zien, probeert u het wacht woord te wijzigen in een variant die u in de vorige sectie hebt toegevoegd. Wanneer Azure AD probeert de wachtwoord wijziging te verwerken, wordt het wacht woord vergeleken met een vermelding in de lijst met geblokkeerde wacht woorden. Vervolgens wordt er een fout weer gegeven voor de gebruiker.

> [!NOTE]
> Voordat een gebruiker het wacht woord opnieuw kan instellen in de webportal, moet de Azure AD-Tenant worden [geconfigureerd voor de selfservice voor wachtwoord herstel](tutorial-enable-sspr.md).

1. Ga naar de pagina **mijn apps** op [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Selecteer in de rechter bovenhoek uw naam en kies vervolgens **profiel** in de vervolg keuzelijst.

    ![Selecteer het profiel](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. Selecteer op de pagina **profiel** de optie **wacht woord wijzigen**.
1. Voer op de pagina **wacht woord wijzigen** het bestaande (oude) wacht woord in. Voer een nieuw wacht woord in en bevestig dit op de aangepaste lijst met verboden wacht woorden dat u in de vorige sectie hebt gedefinieerd en selecteer vervolgens **verzenden**.
1. Er wordt een fout bericht weer gegeven waarin wordt aangegeven dat het wacht woord door de beheerder is geblokkeerd, zoals in het volgende voor beeld:

    ![Er wordt een fout bericht weer gegeven wanneer u een wacht woord probeert te gebruiken dat deel uitmaakt van de aangepaste lijst met uitgesloten wacht woorden](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de aangepaste lijst met verboden wacht woorden die u hebt geconfigureerd als onderdeel van deze zelf studie niet meer wilt gebruiken, voert u de volgende stappen uit:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek en selecteer **Azure Active Directory**en kies vervolgens **beveiliging** in het menu aan de linkerkant.
1. Selecteer in de menu-header beheren **verificatie methoden**en vervolgens **wachtwoord beveiliging**.
1. Stel de optie voor het **afdwingen van aangepaste lijst** in op *Nee*.
1. Selecteer **Opslaan**om de aangepaste configuratie voor verboden wacht woorden bij te werken.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u aangepaste wachtwoord beveiligings lijsten voor Azure AD ingeschakeld en geconfigureerd. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Aangepaste verboden wacht woorden inschakelen
> * Vermeldingen toevoegen aan de lijst met aangepaste verboden wacht woorden
> * Wachtwoord wijzigingen testen met een verboden wacht woord

> [!div class="nextstepaction"]
> [Op risico gebaseerde Azure-Multi-Factor Authentication inschakelen](tutorial-mfa-applications.md)
