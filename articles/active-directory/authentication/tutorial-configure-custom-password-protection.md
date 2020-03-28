---
title: Aangepaste Azure Active Directory-wachtwoordbeveiligingslijsten configureren
description: In deze zelfstudie leert u hoe u aangepaste lijsten voor verboden wachtwoordbeveiliging voor Azure Active Directory configureert om veelvoorkomende woorden in uw omgeving te beperken.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78252843"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Zelfstudie: Aangepaste geblokkeerde wachtwoorden configureren voor Azure Active Directory-wachtwoordbeveiliging

Gebruikers maken vaak wachtwoorden die veelvoorkomende lokale woorden gebruiken, zoals een school, sportteam of beroemde persoon. Deze wachtwoorden zijn gemakkelijk te raden, en zwak tegen woordenboek-gebaseerde aanvallen. Als u sterke wachtwoorden in uw organisatie wilt afdwingen, u met de aangepaste lijst met aangepaste geblokkeerde wachtwoordreeksen voor Azure Active Directory (Azure AD) specifieke tekenreeksen toevoegen om te evalueren en te blokkeren. Een verzoek om wachtwoordwijziging mislukt als er een overeenkomst is in de aangepaste lijst met verboden wachtwoorden.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Aangepaste geblokkeerde wachtwoorden inschakelen
> * Items toevoegen aan de lijst met aangepaste verboden wachtwoorden
> * Wachtwoordwijzigingen testen met een verboden wachtwoord

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een werkende Azure AD-tenant waarop minimaal een proeflicentie is ingeschakeld.
    * Maak er indien nodig [gratis een.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Een account met *algemene beheerdersbevoegdheden.*
* Een niet-beheerdersgebruiker met een wachtwoord dat u kent, zoals *testuser.* U test een gebeurtenis voor wachtwoordwijziging met dit account in deze zelfstudie.
    * Zie [Snelstart: Nieuwe gebruikers toevoegen aan Azure Active Directory](../add-users-azure-active-directory.md)als u een gebruiker wilt maken.
    * Als u de bewerking voor wachtwoordwijziging wilt testen met een verboden wachtwoord, moet de Azure [AD-tenant zijn geconfigureerd voor het opnieuw instellen van het zelfservicewachtwoord.](tutorial-enable-sspr.md)

## <a name="what-are-banned-password-lists"></a>Wat zijn verboden wachtwoordlijsten?

Azure AD bevat een algemene lijst met verboden wachtwoorden. De inhoud van de lijst met verboden wachtwoorden is niet gebaseerd op externe gegevensbronnen. In plaats daarvan is de lijst met algemene verboden wachtwoorden gebaseerd op de lopende resultaten van telemetrie en analyse van Azure AD-beveiliging. Wanneer een gebruiker of beheerder zijn referenties probeert te wijzigen of opnieuw in te stellen, wordt het gewenste wachtwoord gecontroleerd op de lijst met verboden wachtwoorden. Het verzoek om wachtwoordwijziging mislukt als er een overeenkomst is in de lijst met algemene geblokkeerde wachtwoorden.

Om u flexibiliteit te geven in welke wachtwoorden zijn toegestaan, u ook een aangepaste lijst met verboden wachtwoorden definiëren. De aangepaste lijst met verboden wachtwoorden werkt samen met de wereldwijde lijst met verboden wachtwoorden om sterke wachtwoorden in uw organisatie af te dwingen. Organisatiespecifieke termen kunnen worden toegevoegd aan de aangepaste lijst met verboden wachtwoorden, zoals de volgende voorbeelden:

* Merknamen
* Productnamen
* Locaties, zoals het hoofdkantoor van het bedrijf
* Bedrijfsspecifieke interne voorwaarden
* Afkortingen die specifieke bedrijfsbetekenis hebben

Wanneer een gebruiker probeert een wachtwoord opnieuw in te stellen naar iets dat op de algemene of aangepaste lijst met geblokkeerde wachtwoorden staat, ziet hij een van de volgende foutmeldingen:

* *Helaas bevat uw wachtwoord een woord, woordgroep of patroon dat uw wachtwoord gemakkelijk te raden maakt. Probeer het opnieuw met een ander wachtwoord.*
* *Helaas u dat wachtwoord niet gebruiken omdat het woorden of tekens bevat die door uw beheerder zijn geblokkeerd. Probeer het opnieuw met een ander wachtwoord.*

De aangepaste lijst met verboden wachtwoorden is beperkt tot maximaal 1000 termen. Het is niet ontworpen voor het blokkeren van grote lijsten met wachtwoorden. Als u de voordelen van de aangepaste lijst met verboden wachtwoorden wilt maximaliseren, bekijkt u de [aangepaste lijstconcepten voor verboden wachtwoorden](concept-password-ban-bad.md#custom-banned-password-list) en het overzicht van [wachtwoordevaluatiealgoritmen.](concept-password-ban-bad.md#how-are-passwords-evaluated)

## <a name="configure-custom-banned-passwords"></a>Aangepaste geblokkeerde wachtwoorden configureren

Laten we de aangepaste lijst met verboden wachtwoorden inschakelen en enkele vermeldingen toevoegen. U op elk gewenst moment extra vermeldingen toevoegen aan de lijst met aangepaste geblokkeerde wachtwoorden.

Voer de volgende stappen uit om de lijst met aangepaste geblokkeerde wachtwoorden in te schakelen en er vermeldingen aan toe te voegen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account met algemene *beheerdersmachtigingen.*
1. Zoek naar en selecteer **Azure Active Directory**en kies vervolgens **Beveiliging** in het menu aan de linkerkant.
1. Selecteer onder de **koptekst Van het** menu Beheren de optie **Verificatiemethoden**en **vervolgens Wachtwoordbeveiliging**.
1. Stel de optie voor **Aangepaste lijst afdwingen** in op *Ja*.
1. Tekenreeksen toevoegen aan de **lijst Aangepast verboden wachtwoord**, één tekenreeks per regel. De volgende overwegingen en beperkingen zijn van toepassing op de aangepaste lijst met verboden wachtwoorden:

    * De aangepaste lijst met verboden wachtwoorden kan maximaal 1000 termen bevatten.
    * De aangepaste lijst met verboden wachtwoorden is hoofdletter-ongevoelig.
    * De aangepaste lijst met verboden wachtwoorden houdt rekening met gemeenschappelijke tekenvervanging, zoals 'o' en '0', of 'a' en '@'.
    * De minimale tekenreekslengte is vier tekens en het maximum is 16 tekens.

    Geef uw eigen aangepaste wachtwoorden op om te verbieden, zoals in het volgende voorbeeld wordt weergegeven

    [![](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png "Modify the custom banned password list under Authentication Methods in the Azure portal")](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Laat de optie voor **Wachtwoordbeveiliging inschakelen op Windows Server Active Directory** op *nee*.
1. Als u de aangepaste geblokkeerde wachtwoorden en uw vermeldingen wilt inschakelen, selecteert u **Opslaan**.

Het kan enkele uren duren voordat updates van de aangepaste lijst met verboden wachtwoorden worden toegepast.

Voor een hybride omgeving u azure [AD-wachtwoordbeveiliging ook implementeren in een on-premises omgeving.](howto-password-ban-bad-on-premises-deploy.md) Dezelfde globale en aangepaste geblokkeerde wachtwoordlijsten worden gebruikt voor zowel cloud- als on-prem-wachtwoordwijzigingsverzoeken.

## <a name="test-custom-banned-password-list"></a>Aangepaste lijst met verboden wachtwoorden testen

Als u de lijst met aangepaste verboden wachtwoorden in actie wilt zien, probeert u het wachtwoord te wijzigen in een variant van een lijst die u in de vorige sectie hebt toegevoegd. Wanneer Azure AD de wachtwoordwijziging probeert te verwerken, wordt het wachtwoord gekoppeld aan een vermelding in de aangepaste lijst met verboden wachtwoorden. Er wordt vervolgens een fout weergegeven aan de gebruiker.

> [!NOTE]
> Voordat een gebruiker zijn wachtwoord opnieuw kan instellen in de webportal, moet de Azure [AD-tenant zijn geconfigureerd voor het opnieuw instellen van het selfservicewachtwoord.](tutorial-enable-sspr.md)

1. Ga naar de pagina [https://myapps.microsoft.com](https://myapps.microsoft.com)Mijn **apps** op .
1. Selecteer in de rechterbovenhoek uw naam en kies **Profiel** in het vervolgkeuzemenu.

    ![Selecteer het profiel](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. Selecteer **op** de profielpagina **Wachtwoord wijzigen**.
1. Voer **op** de pagina Wachtwoord wijzigen het bestaande (oude) wachtwoord in. Voer een nieuw wachtwoord in en bevestig deze op de aangepaste lijst met verboden wachtwoorden die u in de vorige sectie hebt gedefinieerd en selecteer **Vervolgens Verzenden**.
1. Er wordt een foutbericht geretourneerd dat aangeeft dat het wachtwoord is geblokkeerd door de beheerder, zoals in het volgende voorbeeld wordt weergegeven:

    ![Foutbericht weergegeven wanneer u een wachtwoord probeert te gebruiken dat deel uitmaakt van de aangepaste lijst met verboden wachtwoorden](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de aangepaste lijst met verboden wachtwoorden die u hebt geconfigureerd als onderdeel van deze zelfstudie niet meer wilt gebruiken, voert u de volgende stappen uit:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar en selecteer **Azure Active Directory**en kies vervolgens **Beveiliging** in het menu aan de linkerkant.
1. Selecteer onder de **koptekst Van het** menu Beheren de optie **Verificatiemethoden**en **vervolgens Wachtwoordbeveiliging**.
1. Stel de optie voor **Aangepaste lijst afdwingen** in op *Nee*.
1. Als u de aangepaste geblokkeerde wachtwoordconfiguratie wilt bijwerken, selecteert u **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u aangepaste lijsten voor wachtwoordbeveiliging voor Azure AD ingeschakeld en geconfigureerd. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Aangepaste geblokkeerde wachtwoorden inschakelen
> * Items toevoegen aan de lijst met aangepaste verboden wachtwoorden
> * Wachtwoordwijzigingen testen met een verboden wachtwoord

> [!div class="nextstepaction"]
> [Op risicogebeurtenissen gebaseerde Azure Multi-Factor Authentication inschakelen](tutorial-mfa-applications.md)
