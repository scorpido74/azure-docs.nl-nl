---
title: Azure Active Directory selfservicewachtwoord opnieuw instellen inschakelen
description: In deze zelfstudie leert u hoe u Azure Active Directory selfservice wachtwoordopnieuw instelling inschakelt voor een groep gebruikers en het proces voor het opnieuw instellen van wachtwoorden testen.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77027676"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Zelfstudie: Gebruikers in staat stellen hun account te ontgrendelen of wachtwoorden opnieuw in te stellen met Azure Active Directory selfservicewachtwoord opnieuw instellen

Azure Active Directory (Azure AD) selfservice password reset (SSPR) biedt gebruikers de mogelijkheid om hun wachtwoord te wijzigen of opnieuw in te stellen, zonder tussenkomst van de beheerder of helpdesk. Als het account van een gebruiker is vergrendeld of als hij zijn wachtwoord vergeet, kunnen ze aanwijzingen volgen om zichzelf te deblokkeren en weer aan het werk te gaan. Deze mogelijkheid vermindert helpdeskoproepen en productiviteitsverlies wanneer een gebruiker zich niet kan aanmelden bij zijn apparaat of toepassing.

> [!IMPORTANT]
> In deze quickstart ziet u een beheerder hoe u het opnieuw instellen van selfservicewachtwoorden inschakelt. Als u een eindgebruiker bent die al is geregistreerd voor het opnieuw instellen https://aka.ms/ssprvan het selfservicewachtwoord en u terug moet naar uw account, gaat u naar .
>
> Als uw IT-team de mogelijkheid om uw eigen wachtwoord opnieuw in te stellen niet heeft ingeschakeld, neemt u contact op met uw helpdesk voor extra hulp.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Selfservice-wachtwoordopnieuw instellen inschakelen voor een groep Azure AD-gebruikers
> * Verificatiemethoden en registratieopties configureren
> * Het SSPR-proces testen als gebruiker

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een werkende Azure AD-tenant waarop minimaal een proeflicentie is ingeschakeld.
    * Maak er indien nodig [gratis een.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Een account met *global administrator-bevoegdheden.*
* Een niet-beheerdersgebruiker met een wachtwoord dat u kent, zoals *testuser.* U test de sspr-ervaring van eindgebruikers met behulp van dit account in deze zelfstudie.
    * Zie [Snelstart: Nieuwe gebruikers toevoegen aan Azure Active Directory](../add-users-azure-active-directory.md)als u een gebruiker wilt maken.
* Een groep waarvan de niet-beheerdersgebruiker lid is, zoals *SSPR-Test-Group.* U schakelt SSPR voor deze groep in deze zelfstudie in.
    * Als u een groep wilt maken, [raadpleegt](../active-directory-groups-create-azure-portal.md)u hoe u een groep maakt en leden toevoegt in Azure Active Directory .

## <a name="enable-self-service-password-reset"></a>Selfservice voor wachtwoordherstel inschakelen

Met Azure AD u SSPR voor *Geen,* *Geselecteerde*of *Alle* gebruikers inschakelen. Met deze gedetailleerde mogelijkheid u een subset van gebruikers kiezen om het SSPR-registratieproces en de werkstroom te testen. Wanneer u vertrouwd bent met het proces en de vereisten communiceren met een bredere groep gebruikers, u extra groepen gebruikers selecteren om SSPR in te schakelen. U ook SSPR inschakelen voor iedereen in de Azure AD-tenant.

Configureer in deze zelfstudie SSPR voor een set gebruikers in een testgroep. In het volgende voorbeeld wordt de groep *SSPR-Test-Group* gebruikt. Geef indien nodig uw eigen Azure AD-groep op:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account met algemene *beheerdersmachtigingen.*
1. Zoek naar en selecteer **Azure Active Directory**en kies vervolgens Wachtwoord opnieuw **instellen** in het menu aan de linkerkant.
1. Kies op de pagina **Eigenschappen** onder de optie *Self service password reset ingeschakeld*de optie Groep **Selecteren**
1. Blader naar en selecteer uw Azure AD-groep, zoals *SSPR-Test-Group,* en kies *Selecteer Vervolgens Selecteren*.

    [![](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png "Select a group in the Azure portal to enable for self-service password reset")](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

    Als onderdeel van een bredere implementatie van SSPR worden geneste groepen ondersteund. Zorg ervoor dat de gebruikers in de groep(en) die u kiest, de juiste licenties hebben toegewezen. Er is momenteel geen validatieproces van deze licentievereisten.

1. Als u SSPR voor de geselecteerde gebruikers wilt inschakelen, selecteert u **Opslaan**.

## <a name="select-authentication-methods-and-registration-options"></a>Verificatiemethoden en registratieopties selecteren

Wanneer gebruikers hun account moeten ontgrendelen of hun wachtwoord opnieuw moeten instellen, wordt ze gevraagd om een extra bevestigingsmethode. Deze extra verificatiefactor zorgt ervoor dat alleen goedgekeurde SSPR-gebeurtenissen worden voltooid. U kiezen welke verificatiemethoden u wilt toestaan, op basis van de registratiegegevens die de gebruiker verstrekt.

1. Stel op de pagina **Verificatiemethoden** in het menu aan de linkerkant het aantal methoden in **dat nodig is om te resetten** op *1*.

    Om de beveiliging te verbeteren, u het aantal verificatiemethoden verhogen dat nodig is voor SSPR.

1. Kies de **methoden die beschikbaar zijn voor gebruikers** die uw organisatie wil toestaan. Schakel voor deze zelfstudie de selectievakjes in om de volgende methoden in te schakelen:

    * *Meldingen via mobiele app*
    * *Code van mobiele app*
    * *Email*
    * *Mobiele telefoon*
    * *Telefoon (werk)*

1. Als u de verificatiemethoden wilt toepassen, selecteert u **Opslaan**.

Voordat gebruikers hun account kunnen ontgrendelen of een wachtwoord kunnen resetten, moeten ze hun contactgegevens registreren. Deze contactgegevens worden gebruikt voor de verschillende verificatiemethoden die in de vorige stappen zijn geconfigureerd.

Een beheerder kan deze contactgegevens handmatig verstrekken of gebruikers kunnen naar een registratieportal gaan om de informatie zelf te verstrekken. Configureer in deze zelfstudie de gebruikers die worden gevraagd om registratie wanneer ze zich volgende aanmelden.

1. Selecteer **op** de pagina Registratie in het menu aan de linkerkant *de* optie Ja voor **gebruikers die zich moeten registreren bij het aanmelden.**
1. Het is belangrijk dat contactgegevens up-to-date worden gehouden. Als de contactgegevens verouderd zijn wanneer een SSPR-gebeurtenis wordt gestart, kan de gebruiker zijn account mogelijk niet ontgrendelen of zijn wachtwoord opnieuw instellen.

    Stel **Aantal dagen waarna gebruikers wordt gevraagd om de verificatiegegevens opnieuw te bevestigen** in op *180*.
1. Als u de registratie-instellingen wilt toepassen, selecteert u **Opslaan**.

## <a name="configure-notifications-and-customizations"></a>Meldingen en aanpassingen configureren

Als u gebruikers op de hoogte wilt houden van accountactiviteiten, u e-mailmeldingen configureren die moeten worden verzonden wanneer een SSPR-gebeurtenis plaatsvindt. Deze meldingen kunnen betrekking hebben op zowel gewone gebruikersaccounts als beheerdersaccounts. Voor beheerdersaccounts biedt deze melding een extra bewustzijnslaag wanneer een wachtwoord voor een geprivilegieerd beheerdersaccount opnieuw wordt ingesteld met SSPR.

1. Configureer op de pagina **Meldingen** in het menu aan de linkerkant de volgende opties:

   * Stel **Gebruikers een melding tonen over het opnieuw instellen van hun wachtwoord** in op *Ja*.
   * Stel **Alle beheerders waarschuwen wanneer andere beheerders hun wachtwoord opnieuw instellen** in op *Ja*.

1. Als u de meldingsvoorkeuren wilt toepassen, selecteert u **Opslaan**.

Als gebruikers extra hulp nodig hebben bij het SSPR-proces, u de koppeling voor 'Contact opnemen met uw beheerder' aanpassen. Deze koppeling wordt gebruikt in het SSPR-registratieproces en wanneer een gebruiker zijn account ontgrendelt of zijn wachtwoord opnieuw instelt. Om ervoor te zorgen dat uw gebruikers de ondersteuning krijgen die nodig is, is het ten zeerste aan te raden om een aangepaste helpdesk-e-mail of URL op te geven.

1. Stel **op** de pagina Aanpassen in het menu aan de linkerkant *de koppeling Helpdesk* aanpassen naar **Ja**in.
1. Geef in het **veld Aangepaste helpdeske-e-mail of URL** een e-mailadres of URL van webpagina op waar uw gebruikers extra hulp van uw organisatie kunnen krijgen, zoals*https://support.contoso.com/*
1. Als u de aangepaste koppeling wilt toepassen, selecteert u **Opslaan**.

## <a name="test-self-service-password-reset"></a>Selfservice voor wachtwoord opnieuw instellen testen

Als SSPR is ingeschakeld en geconfigureerd, test u het SSPR-proces met een gebruiker die deel uitmaakt van de groep die u in de vorige sectie hebt geselecteerd, zoals *Test-SSPR-Group.* In het volgende voorbeeld wordt het *testuseraccount* gebruikt. Geef uw eigen gebruikersaccount op dat deel uitmaakt van de groep die u hebt ingeschakeld voor SSPR in het eerste deel van deze zelfstudie.

> [!NOTE]
> Wanneer u de selfservicewachtwoordopnieuw instellen test, gebruikt u een niet-beheerdersaccount. Beheerders zijn altijd ingeschakeld voor het opnieuw instellen van selfservicewachtwoorden en moeten twee verificatiemethoden gebruiken om hun wachtwoord opnieuw in te stellen.

1. Als u het handmatige registratieproces wilt bekijken, opent u een [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)nieuw browservenster in de InPrivate- of incognitomodus en bladert u naar . Gebruikers moeten naar deze registratieportal worden geleid wanneer ze zich volgende aanmelden.
1. Meld u aan bij een niet-beheerderstestgebruiker, zoals *testuser,* en registreer de contactgegevens van uw verificatiemethoden.
1. Zodra u klaar bent, selecteert u de knop gemarkeerd **Ziet er goed uit** en sluit u het browservenster.
1. Open een nieuw browservenster in de InPrivate- of incognitomodus en blader naar [https://aka.ms/sspr](https://aka.ms/sspr).
1. Voer de accountgegevens van uw niet-beheerderstestvoor gebruikers in, zoals *testgebruiker,* de tekens uit de CAPTCHA en selecteer **Volgende**.

    ![Gebruikersaccountgegevens invoeren om het wachtwoord opnieuw in te stellen](media/tutorial-enable-sspr/password-reset-page.png)

1. Volg de verificatiestappen om uw wachtwoord opnieuw in te stellen. Als u klaar bent, ontvangt u een e-mailmelding dat uw wachtwoord opnieuw is ingesteld.

## <a name="clean-up-resources"></a>Resources opschonen

In een volgende zelfstudie in deze serie configureert u het terugschrijven van wachtwoorden. Met deze functie worden wachtwoordwijzigingen van Azure AD SSPR teruggeplaatst naar een on-premises AD-omgeving. Als u door wilt gaan met deze zelfstudiereeks om wachtwoordterugschrijfformulieren te configureren, schakelt u SSPR nu niet uit.

Als u de SSPR-functionaliteit die u hebt geconfigureerd als onderdeel van deze zelfstudie niet meer wilt gebruiken, stelt u de SSPR-status in op **Geen** met de volgende stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar en selecteer **Azure Active Directory**en kies vervolgens Wachtwoord opnieuw **instellen** in het menu aan de linkerkant.
1. Kies **Geen**op de pagina **Eigenschappen** onder de optie *Self service password reset ingeschakeld*.
1. Als u de SSPR-wijziging wilt toepassen, selecteert u **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u azure AD selfservice wachtwoordopnieuw instellen ingeschakeld voor een geselecteerde groep gebruikers. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Selfservice-wachtwoordopnieuw instellen inschakelen voor een groep Azure AD-gebruikers
> * Verificatiemethoden en registratieopties configureren
> * Het SSPR-proces testen als gebruiker

> [!div class="nextstepaction"]
> [Azure Multi-Factor Authentication inschakelen](tutorial-mfa-applications.md)
