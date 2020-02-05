---
title: Azure Active Directory self-service voor wachtwoord herstel inschakelen
description: In deze zelf studie leert u hoe u Azure Active Directory selfservice voor het opnieuw instellen van wacht woorden inschakelt voor een groep gebruikers en het proces voor het opnieuw instellen van het wacht woord te testen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71b9052f364dfbae205dd324ba69de9578ccc225
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027676"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Zelf studie: gebruikers in staat stellen hun account te ontgrendelen of wacht woorden opnieuw in te stellen met Azure Active Directory self-service voor wachtwoord herstel

Azure Active Directory (Azure AD) self-service voor wachtwoord herstel (SSPR) biedt gebruikers de mogelijkheid om hun wacht woord te wijzigen of opnieuw in te stellen, zonder de rol van beheerder of Help Desk. Als het account van een gebruiker is vergrendeld of als het wacht woord is verg eten, kunnen ze de aanwijzingen volgen om zichzelf te deblokkeren en terug te gaan naar het werk. Deze mogelijkheid vermindert helpdesk oproepen en productiviteits verlies wanneer een gebruiker zich niet kan aanmelden bij hun apparaat of een toepassing.

> [!IMPORTANT]
> In deze Quick start ziet u een beheerder hoe selfservice voor wachtwoord herstel moet worden ingeschakeld. Als u een eind gebruiker bent al geregistreerd voor de selfservice voor het opnieuw instellen van het wacht woord en deze wilt weer geven in uw account, gaat u naar https://aka.ms/sspr.
>
> Als uw IT-team de mogelijkheid om uw eigen wacht woord opnieuw in te stellen niet heeft ingeschakeld, kunt u contact met de Help Desk vinden voor meer informatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Selfservice voor wachtwoord herstel inschakelen voor een groep van Azure AD-gebruikers
> * Verificatie methoden en registratie opties configureren
> * Het SSPR-proces testen als een gebruiker

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources en bevoegdheden nodig om deze zelf studie te volt ooien:

* Een werkende Azure AD-tenant waarop minimaal een proeflicentie is ingeschakeld.
    * Maak indien nodig [een gratis versie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een account met *globale beheerders* bevoegdheden.
* Een niet-beheerders gebruiker met een wacht woord dat u kent, zoals *test User*. In deze zelf studie test u de SSPR-ervaring voor eind gebruikers met dit account.
    * Als u een gebruiker wilt maken, raadpleegt u [Quick Start: nieuwe gebruikers toevoegen aan Azure Active Directory](../add-users-azure-active-directory.md).
* Een groep waarvan de niet-beheerders gebruiker lid is, zoals *SSPR-test groep*. In deze zelf studie schakelt u SSPR in voor deze groep.
    * Als u een groep wilt maken, raadpleegt u [een groep maken en leden toevoegen in azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Selfservice voor wachtwoordherstel inschakelen

Met Azure AD kunt u SSPR inschakelen voor *geen*, *geselecteerd*of *alle* gebruikers. Met deze gedetailleerde mogelijkheid kunt u een subset van gebruikers kiezen om het registratie proces en de werk stroom van het SSPR te testen. Wanneer u vertrouwd bent met het proces en de vereisten kunt communiceren met een grotere set gebruikers, kunt u extra groepen gebruikers selecteren om in te scha kelen voor SSPR. U kunt ook SSPR inschakelen voor iedereen in de Azure AD-Tenant.

In deze zelf studie configureert u SSPR voor een set gebruikers in een test groep. In het volgende voor beeld wordt de groep Group *SSPR-test-Group* gebruikt. Geef uw eigen Azure AD-groep op als dat nodig is:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account met *globale beheerders* machtigingen.
1. Zoek en selecteer **Azure Active Directory**en kies vervolgens **wacht woord opnieuw instellen** in het menu aan de linkerkant.
1. Kies op de pagina **Eigenschappen** onder het selectie vakje *selfservice voor wacht woord opnieuw instellen is ingeschakeld*de optie **groep selecteren**
1. Blader naar en selecteer uw Azure AD-groep, zoals *SSPR-test groep*, en kies vervolgens *selecteren*.

    [![](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png "Select a group in the Azure portal to enable for self-service password reset")](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

    Als onderdeel van een bredere implementatie van SSPR worden geneste groepen ondersteund. Zorg ervoor dat aan de gebruikers in de groep (en) die u kiest de juiste licenties zijn toegewezen. Er is momenteel geen validatie proces van deze licentie vereisten.

1. Selecteer **Opslaan**om SSPR in te scha kelen voor de Select-gebruikers.

## <a name="select-authentication-methods-and-registration-options"></a>Verificatie methoden en registratie opties selecteren

Wanneer gebruikers hun account moeten ontgrendelen of hun wacht woord opnieuw moeten instellen, wordt ze gevraagd een aanvullende bevestigings methode op te vragen. Deze extra verificatie factor zorgt ervoor dat alleen goedgekeurde SSPR-gebeurtenissen zijn voltooid. U kunt kiezen welke verificatie methoden u wilt toestaan, op basis van de registratie gegevens die door de gebruiker worden verstrekt.

1. Stel op de pagina **verificatie methoden** in het menu aan de linkerkant het **aantal methoden** in dat moet worden ingesteld op *1*.

    Om de beveiliging te verbeteren, kunt u het aantal verificatie methoden verhogen dat nodig is voor SSPR.

1. Kies de **methoden die beschikbaar zijn voor gebruikers** die uw organisatie wil toestaan. Voor deze zelf studie schakelt u de selectie vakjes in om de volgende methoden in te scha kelen:

    * *Melding voor mobiele app*
    * *Mobiele app-code*
    * *E-mail*
    * *Mobiele telefoon*
    * *Telefoon (werk)*

1. Selecteer **Opslaan**om de verificatie methoden toe te passen.

Voordat gebruikers hun account kunnen ontgrendelen of een wacht woord opnieuw moeten instellen, moeten ze hun contact gegevens registreren. Deze contact gegevens worden gebruikt voor de verschillende verificatie methoden die in de vorige stappen zijn geconfigureerd.

Een beheerder kan deze contact gegevens hand matig opgeven, of gebruikers kunnen naar een registratie portal gaan om de informatie zelf op te geven. In deze zelf studie configureert u de gebruikers om om registratie te worden gevraagd wanneer ze zich de volgende keer aanmelden.

1. Op de **registratie** pagina in het menu aan de linkerkant selecteert u *Ja* voor **gebruikers die zich moeten registreren bij het aanmelden**.
1. Het is belang rijk dat contact gegevens up-to-date blijven. Als de contact gegevens verouderd zijn wanneer een SSPR-gebeurtenis wordt gestart, kan de gebruiker de account niet meer ontgrendelen of hun wacht woord opnieuw instellen.

    Stel **Aantal dagen waarna gebruikers wordt gevraagd om de verificatiegegevens opnieuw te bevestigen** in op *180*.
1. Selecteer **Opslaan**om de registratie-instellingen toe te passen.

## <a name="configure-notifications-and-customizations"></a>Meldingen en aanpassingen configureren

Om gebruikers op de hoogte te houden van account activiteit, kunt u e-mail meldingen configureren die moeten worden verzonden wanneer er een SSPR-gebeurtenis plaatsvindt. Deze meldingen kunnen zowel normale gebruikers accounts als beheerders accounts omvatten. Voor beheerders accounts bevat deze melding een extra beveiligingslaag wanneer een bevoegd beheerders account wacht woord opnieuw wordt ingesteld met behulp van SSPR.

1. Configureer op de pagina **meldingen** in het menu aan de linkerkant de volgende opties:

   * Stel **Gebruikers een melding tonen over het opnieuw instellen van hun wachtwoord** in op *Ja*.
   * Stel **Alle beheerders waarschuwen wanneer andere beheerders hun wachtwoord opnieuw instellen** in op *Ja*.

1. Selecteer **Opslaan**om de voor keuren voor meldingen toe te passen.

Als gebruikers extra hulp nodig hebben bij het SSPR-proces, kunt u de koppeling aanpassen voor ' Neem contact op met de beheerder '. Deze koppeling wordt gebruikt in het SSPR-registratie proces en wanneer een gebruiker hun account ontgrendelt of hun wacht woord opnieuw instelt. Om ervoor te zorgen dat uw gebruikers de benodigde ondersteuning krijgen, is het raadzaam een aangepaste Help Desk-e-mail of-URL op te geven.

1. Stel op de pagina **aanpassing** in het menu aan de linkerkant de *koppeling Help Desk aanpassen* in op **Ja**.
1. Geef in het **aangepaste e-mail adres of URL** -veld van de Help Desk een URL op voor de webpagina waar uw gebruikers extra hulp kunnen krijgen van uw organisatie, zoals *https://support.contoso.com/*
1. Selecteer **Opslaan**om de aangepaste koppeling toe te passen.

## <a name="test-self-service-password-reset"></a>Selfservice voor wachtwoord opnieuw instellen testen

Als SSPR is ingeschakeld en geconfigureerd, moet u het SSPR-proces testen met een gebruiker die deel uitmaakt van de groep die u in de vorige sectie hebt geselecteerd, zoals *test-SSPR-Group*. In het volgende voor beeld wordt het *test* account gebruikt. Geef uw eigen gebruikers account op dat deel uitmaakt van de groep die u hebt ingeschakeld voor SSPR in de eerste sectie van deze zelf studie.

> [!NOTE]
> Gebruik een niet-beheerders account als u de selfservice voor wachtwoord herstel test. Beheerders zijn altijd ingeschakeld voor selfservice voor wachtwoord herstel en zijn vereist voor het gebruik van twee verificatie methoden om hun wacht woord opnieuw in te stellen.

1. Als u het hand matige registratie proces wilt bekijken, opent u een nieuw browser venster in de modus InPrivate of incognito en bladert u naar [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup). Gebruikers moeten worden omgeleid naar deze registratie portal bij de volgende aanmelding.
1. Meld u aan met een gebruiker die geen beheerder is, zoals *test User, en*Registreer uw contact gegevens voor de verificatie methoden.
1. Zodra u klaar bent, selecteert u de gemarkeerde knop **goed** en sluit u het browser venster.
1. Open een nieuw browservenster in de InPrivate- of incognitomodus en browse naar [https://aka.ms/sspr](https://aka.ms/sspr).
1. Voer uw account gegevens voor gebruikers die geen beheerder zijn, in, zoals *test User*, de tekens van de captcha en selecteer vervolgens **volgende**.

    ![Informatie over gebruikers account opgeven om het wacht woord opnieuw in te stellen](media/tutorial-enable-sspr/password-reset-page.png)

1. Volg de verificaties tappen om uw wacht woord opnieuw in te stellen. Als u klaar bent, ontvangt u een e-mail melding dat uw wacht woord opnieuw is ingesteld.

## <a name="clean-up-resources"></a>Resources opschonen

In een van de volgende zelf studies in deze reeks configureert u het terugschrijven van wacht woorden. Deze functie schrijft wachtwoord wijzigingen van Azure AD SSPR terug naar een on-premises AD-omgeving. Als u wilt door gaan met deze zelf studie serie om het terugschrijven van wacht woorden te configureren, schakelt u SSPR nu niet uit.

Als u de SSPR-functionaliteit die u als onderdeel van deze zelf studie hebt geconfigureerd niet meer wilt gebruiken, stelt u de status SSPR in op **geen** door de volgende stappen uit te voeren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Zoek en selecteer **Azure Active Directory**en kies vervolgens **wacht woord opnieuw instellen** in het menu aan de linkerkant.
1. Kies op de pagina **Eigenschappen** onder het selectie vakje *selfservice voor wacht woord opnieuw instellen is ingeschakeld*de optie **geen**.
1. Selecteer **Opslaan**om de wijziging van de SSPR toe te passen.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u Azure AD self-service voor het opnieuw instellen van wacht woorden ingeschakeld voor een geselecteerde groep gebruikers. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Selfservice voor wachtwoord herstel inschakelen voor een groep van Azure AD-gebruikers
> * Verificatie methoden en registratie opties configureren
> * Het SSPR-proces testen als een gebruiker

> [!div class="nextstepaction"]
> [Azure Multi-Factor Authentication inschakelen](tutorial-mfa-applications.md)
