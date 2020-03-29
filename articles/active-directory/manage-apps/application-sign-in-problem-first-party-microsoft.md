---
title: Problemen bij het aanmelden bij een Microsoft-toepassing | Microsoft Documenten
description: Veelvoorkomende problemen oplossen bij het aanmelden bij microsoft-toepassingen van de eerste partij met Azure AD (zoals Office 365)
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee8802aeb2a760e255ab4f5e99010dfedc45e0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108301"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problemen met het aanmelden bij een Microsoft-toepassing

Microsoft-toepassingen (zoals Office 365 Exchange, SharePoint, Yammer, enz.) worden een beetje anders toegewezen en beheerd dan SaaS-toepassingen van derden of andere toepassingen die u integreert met Azure AD voor éénmalige aanmelding.

Er zijn drie belangrijke manieren waarop een gebruiker toegang kan krijgen tot een door Microsoft gepubliceerde toepassing.

-   Voor toepassingen in de Office 365 of andere betaalde suites krijgen gebruikers toegang via **licentietoewijzing,** hetzij rechtstreeks tot hun gebruikersaccount, hetzij via een groep met behulp van onze groepsgebaseerde licentietoewijzingsmogelijkheid.

-   Voor toepassingen die Microsoft of een derde partij vrij publiceert voor iedereen om te gebruiken, kunnen gebruikers toegang krijgen via **toestemming van de gebruiker.** Dit betekent dat ze zich aanmelden bij de toepassing met hun Azure AD Work- of School-account en deze toegang geven tot een beperkte set gegevens op hun account.

-   Voor toepassingen die Microsoft of een derde partij vrij publiceert voor iedereen om te gebruiken, kunnen gebruikers ook toegang krijgen via **toestemming van de beheerder.** Dit betekent dat een beheerder heeft bepaald dat de toepassing kan worden gebruikt door iedereen in de organisatie, zodat ze zich aanmelden bij de toepassing met een Global Administrator-account en toegang verlenen aan iedereen in de organisatie.

Als u uw probleem wilt oplossen, begint u met de [algemene probleemgebieden met toepassingstoegang om](#general-problem-areas-with-application-access-to-consider) de walkthrough: stappen te overwegen om de toegang tot Microsoft-toepassingen op te lossen om in de details te komen.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Algemene probleemgebieden met toepassingstoegang om te overwegen

Hieronder volgt een lijst met de algemene probleemgebieden waarin u inzoomen als u een idee hebt van waar u moet beginnen, maar we raden u aan de walkthrough te lezen om snel aan de slag te gaan: Walkthrough: Stappen om de toegang tot Microsoft-toepassingen op te lossen.

-   [Problemen met het account van de gebruiker](#problems-with-the-users-account)

-   [Problemen met groepen](#problems-with-groups)

-   [Problemen met beleid voor voorwaardelijke toegang](#problems-with-conditional-access-policies)

-   [Problemen met de toestemming van de aanvraag](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Stappen om problemen met de toegang tot Microsoft-toepassingen op te lossen

Hieronder volgen enkele veelvoorkomende problemen die mensen tegenkomen wanneer hun gebruikers zich niet kunnen aanmelden bij een Microsoft-toepassing.

- Algemene problemen om eerst te controleren

  * Zorg ervoor dat de gebruiker zich aanmeldt bij de **juiste URL** en niet op een URL van de lokale toepassing.

  * Zorg ervoor dat het account van de gebruiker niet is **vergrendeld.**

  * Controleer of het account van de **gebruiker bestaat** in Azure Active Directory. [Controleren of er een gebruikersaccount bestaat in Azure Active Directory](#problems-with-the-users-account)

  * Controleer of het account van de gebruiker is **ingeschakeld** voor aanmeldingen. [Check a user’s account status](#problems-with-the-users-account)

  * Zorg ervoor dat het wachtwoord van de gebruiker **niet is verlopen of vergeten.** [Het wachtwoord van een gebruiker opnieuw instellen](#reset-a-users-password) of [selfservicewachtwoord opnieuw instellen](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

  * Controleer of **multi-factorauthenticatie** de toegang van gebruikers niet blokkeert. [Controleer de multifactorauthenticatiestatus van een gebruiker](#check-a-users-multi-factor-authentication-status) of [Controleer de contactgegevens van een gebruiker op de verificatie](#check-a-users-authentication-contact-info)

  * Zorg ervoor dat een **beleid voor voorwaardelijke toegang** of beleid voor **identiteitsbescherming** de toegang van gebruikers niet blokkeert. [Een specifiek beleid voor voorwaardelijke toegang controleren](#problems-with-conditional-access-policies) of het beleid voor voorwaardelijke toegang van een specifieke toepassing [controleren](#check-a-specific-applications-conditional-access-policy) of een specifiek beleid voor voorwaardelijke [toegang uitschakelen](#disable-a-specific-conditional-access-policy)

  * Zorg ervoor dat de contactgegevens van een gebruiker op de hoogte zijn van de gegevens over de **verificatie,** zodat het beleid voor meervoudige verificatie of voorwaardelijke toegang kan worden afgedwongen. [Controleer de multifactorauthenticatiestatus van een gebruiker](#check-a-users-multi-factor-authentication-status) of [Controleer de contactgegevens van een gebruiker op de verificatie](#check-a-users-authentication-contact-info)

- Voor **Microsoft** **Microsoft-toepassingen waarvoor een licentie vereist is** (zoals Office365), zijn hier enkele specifieke problemen om te controleren zodra u de algemene problemen hierboven hebt uitgesloten:

  * Zorg ervoor dat de gebruiker of een **licentie heeft toegewezen.** [De toegewezen licenties van een gebruiker controleren](#check-a-users-assigned-licenses) of de toegewezen licenties van een groep [controleren](#check-a-groups-assigned-licenses)

  * Als de licentie is **toegewezen aan een** statische **groep,** moet u ervoor zorgen dat de **gebruiker lid is** van die groep. [Groepslidmaatschappen van een gebruiker controleren](#check-a-users-group-memberships)

  * Als de licentie is **toegewezen aan een** dynamische **groep,** moet u ervoor zorgen dat de **dynamische groepsregel correct is ingesteld**. [De lidmaatschapscriteria van een dynamische groep controleren](#check-a-dynamic-groups-membership-criteria)

  * Als de licentie is **toegewezen aan een** dynamische **groep,** moet u ervoor zorgen dat de dynamische groep klaar is **met het verwerken van** het lidmaatschap en dat de gebruiker lid **is** (dit kan enige tijd duren). [Groepslidmaatschappen van een gebruiker controleren](#check-a-users-group-memberships)

  *  Zodra u ervoor zorgt dat de licentie is toegewezen, controleert u of de licentie niet is **verlopen.**

  *  Zorg ervoor dat de licentie is **voor de toepassing die** ze openen.

- Voor **Microsoft** **Microsoft-toepassingen waarvoor geen licentie nodig is,** vindt u hier enkele andere dingen om te controleren:

  * Als de toepassing **toestemmingen** op gebruikersniveau aanvraagt (bijvoorbeeld 'Toegang tot het postvak van deze gebruiker'), moet u ervoor zorgen dat de gebruiker zich bij de toepassing heeft aangemeld en een **toestemmingsbewerking op gebruikersniveau** heeft uitgevoerd om de toepassing toegang te geven tot hun gegevens.

  * Als de toepassing machtigingen op **administratorniveau** aanvraagt (bijvoorbeeld 'Toegang tot alle postvakken van de gebruiker'), moet u ervoor zorgen dat een globale beheerder namens alle gebruikers in de organisatie een **toestemmingsbewerking op administratorniveau** heeft uitgevoerd.

## <a name="problems-with-the-users-account"></a>Problemen met het account van de gebruiker

Toegang tot toepassingen kan worden geblokkeerd vanwege een probleem met een gebruiker die is toegewezen aan de toepassing. Hieronder volgen enkele manieren waarop u problemen met gebruikers en hun accountinstellingen oplossen en oplossen:

-   [Controleren of er een gebruikersaccount bestaat in Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [De accountstatus van een gebruiker controleren](#check-a-users-account-status)

-   [Het wachtwoord van een gebruiker opnieuw instellen](#reset-a-users-password)

-   [Selfservice voor wachtwoordherstel inschakelen](#enable-self-service-password-reset)

-   [De multifactorauthenticatiestatus van een gebruiker controleren](#check-a-users-multi-factor-authentication-status)

-   [Contactgegevens van een gebruiker controleren](#check-a-users-authentication-contact-info)

-   [Groepslidmaatschappen van een gebruiker controleren](#check-a-users-group-memberships)

-   [De toegewezen licenties van een gebruiker controleren](#check-a-users-assigned-licenses)

-   [Een gebruiker een licentie toewijzen](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Controleren of er een gebruikersaccount bestaat in Azure Active Directory

Voer de volgende stappen uit om te controleren of het account van een gebruiker aanwezig is:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  Controleer de eigenschappen van het object van de gebruiker om er zeker van te zijn dat ze eruit zien zoals u verwacht en dat er geen gegevens ontbreken.

### <a name="check-a-users-account-status"></a>De accountstatus van een gebruiker controleren

Voer de volgende stappen uit om de accountstatus van een gebruiker te controleren:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op **Profiel**.

8.  Controleer **onder Instellingen** of Aanmelden voor **blokkeren** is ingesteld op **Nee**.

### <a name="reset-a-users-password"></a>Het wachtwoord van een gebruiker opnieuw instellen

Voer de volgende stappen uit om het wachtwoord van een gebruiker opnieuw in te stellen:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op de knop **Wachtwoord opnieuw instellen** boven aan het gebruikersvenster.

8.  klik op de knop **Wachtwoord opnieuw instellen** in het deelvenster Wachtwoord opnieuw **instellen** dat wordt weergegeven.

9.  Kopieer het **tijdelijke wachtwoord** of voer een **nieuw wachtwoord** in voor de gebruiker.

10. Ze moeten dit wachtwoord aan de gebruiker meedelen, maar deze wachtwoord moeten worden gewijzigd tijdens hun volgende aanmelding in Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Selfservice voor wachtwoordherstel inschakelen

Volg de onderstaande implementatiestappen om het opnieuw instellen van selfservicewachtwoorden in te schakelen:

-   [Gebruikers in staat stellen hun Azure Active Directory-wachtwoorden opnieuw te instellen](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Gebruikers in staat stellen hun on-premises wachtwoorden van Active Directory opnieuw in te stellen of te wijzigen](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>De multifactorauthenticatiestatus van een gebruiker controleren

Voer de volgende stappen uit om de multifactorauthenticatiestatus van een gebruiker te controleren:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Gebruikers en groepen** in het navigatiemenu.

5. klik op **Alle gebruikers**.

6. klik op de knop **Multifactorverificatie** boven aan het deelvenster.

7. Zodra de **portal voor multi-factorverificatiebeheer** wordt geladen, moet u ervoor zorgen dat u zich op het tabblad **Gebruikers** bevindt.

8. Zoek de gebruiker in de lijst met gebruikers door te zoeken, te filteren of te sorteren.

9. Selecteer de gebruiker in de lijst met gebruikers en **Schakel**multifactorauthenticatie naar wens **in, schakel uit**of dwing multifactorauthenticatie **af.**

   * **Opmerking:** als een gebruiker zich in **een afgedwongen** toestand bevindt, u deze tijdelijk instellen **op Uitgeschakeld** om hem of haar terug te laten keren in zijn account. Zodra ze weer binnen zijn, u hun status opnieuw wijzigen **in Ingeschakeld** om te eisen dat ze hun contactgegevens opnieuw registreren tijdens hun volgende aanmelding. U ook de stappen volgen in de [contactgegevens van de verificatiegegevens van een gebruiker](#check-a-users-authentication-contact-info) controleren om deze gegevens voor hen te verifiëren of in te stellen.

### <a name="check-a-users-authentication-contact-info"></a>Contactgegevens van een gebruiker controleren

Voer de volgende stappen uit om de contactgegevens van een gebruiker te controleren die worden gebruikt voor multifactorauthenticatie, voorwaardelijke toegang, identiteitsbeveiliging en wachtwoordreset:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op **Profiel**.

8.  Schuif omlaag naar **Contactgegevens voor verificatie**.

9.  **Controleer** de gegevens die zijn geregistreerd voor de gebruiker en werk indien nodig bij.

### <a name="check-a-users-group-memberships"></a>Groepslidmaatschappen van een gebruiker controleren

Voer de volgende stappen uit om groepslidmaatschappen van een gebruiker te controleren:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op **Groepen** om te zien van welke groepen de gebruiker lid is.

### <a name="check-a-users-assigned-licenses"></a>De toegewezen licenties van een gebruiker controleren

Voer de volgende stappen uit om de toegewezen licenties van een gebruiker te controleren:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op **Licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

### <a name="assign-a-user-a-license"></a>Een gebruiker een licentie toewijzen 

Voer de volgende stappen uit om een licentie aan een gebruiker toe te wijzen:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op **Licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

8.  klik op de knop **Toewijzen.**

9.  Selecteer **een of meer producten** uit de lijst met beschikbare producten.

10. **Optioneel** klikt u op het item **toewijzingsopties** om producten gedetailleerd toe te wijzen. Klik **op Ok** wanneer dit is voltooid.

11. Klik **op** de knop Toewijzen om deze licenties aan deze gebruiker toe te wijzen.

## <a name="problems-with-groups"></a>Problemen met groepen

Toegang tot toepassingen kan worden geblokkeerd vanwege een probleem met een groep die is toegewezen aan de toepassing. Hieronder volgen enkele manieren waarop u problemen met groepen en groepslidmaatschappen oplossen en oplossen:

-   [Het lidmaatschap van een groep controleren](#check-a-groups-membership)

-   [De lidmaatschapscriteria van een dynamische groep controleren](#check-a-dynamic-groups-membership-criteria)

-   [De toegewezen licenties van een groep controleren](#check-a-groups-assigned-licenses)

-   [Licenties van een groep opnieuw verwerken](#reprocess-a-groups-licenses)

-   [Een groep een licentie toewijzen](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Het lidmaatschap van een groep controleren

Voer de volgende stappen uit om het lidmaatschap van een groep te controleren:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle groepen**.

6.  **Zoek naar** de groep waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op **Leden** om de lijst met gebruikers te bekijken die aan deze groep zijn toegewezen.

### <a name="check-a-dynamic-groups-membership-criteria"></a>De lidmaatschapscriteria van een dynamische groep controleren 

Voer de volgende stappen uit om de lidmaatschapscriteria van een dynamische groep te controleren:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle groepen**.

6.  **Zoek naar** de groep waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op **Dynamische lidmaatschapsregels.**

8.  Controleer de **eenvoudige** of **geavanceerde** regel die voor deze groep is gedefinieerd en controleer of de gebruiker die u lid wilt zijn van deze groep aan deze criteria voldoet.

### <a name="check-a-groups-assigned-licenses"></a>De toegewezen licenties van een groep controleren

Voer de volgende stappen uit om de toegewezen licenties van een groep te controleren:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle groepen**.

6.  **Zoek naar** de groep waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  Klik op **Licenties** om te zien welke licenties de groep momenteel heeft toegewezen.

### <a name="reprocess-a-groups-licenses"></a>Licenties van een groep opnieuw verwerken

Voer de volgende stappen uit om de toegewezen licenties van een groep opnieuw te verwerken:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Gebruikers en groepen** in het navigatiemenu.

5. klik op **Alle groepen**.

6. **Zoek naar** de groep waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7. Klik op **Licenties** om te zien welke licenties de groep momenteel heeft toegewezen.

8. klik **op** de knop Opnieuw verwerken om ervoor te zorgen dat de licenties die zijn toegewezen aan de leden van deze groep up-to-date zijn. Dit kan lang duren, afhankelijk van de grootte en complexiteit van de groep.

   >[!NOTE]
   >Om dit sneller te doen, u overwegen om een licentie rechtstreeks aan de gebruiker toe te wijsen. [Een gebruiker een licentie toewijzen](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Een groep een licentie toewijzen

Voer de volgende stappen uit om een licentie aan een groep toe te wijzen:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Gebruikers en groepen** in het navigatiemenu.

5. klik op **Alle groepen**.

6. **Zoek naar** de groep waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7. Klik op **Licenties** om te zien welke licenties de groep momenteel heeft toegewezen.

8. klik op de knop **Toewijzen.**

9. Selecteer **een of meer producten** uit de lijst met beschikbare producten.

10. **Optioneel** klikt u op het item **toewijzingsopties** om producten gedetailleerd toe te wijzen. Klik **op Ok** wanneer dit is voltooid.

11. Klik **op** de knop Toewijzen om deze licenties aan deze groep toe te wijzen. Dit kan lang duren, afhankelijk van de grootte en complexiteit van de groep.

    >[!NOTE]
    >Om dit sneller te doen, u overwegen om een licentie rechtstreeks aan de gebruiker toe te wijsen. [Een gebruiker een licentie toewijzen](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problemen met beleid voor voorwaardelijke toegang

### <a name="check-a-specific-conditional-access-policy"></a>Een specifiek beleid voor voorwaardelijke toegang controleren

Eén voorwaardelijk toegangsbeleid controleren of valideren:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Enterprise-toepassingen** in het navigatiemenu.

5. klik op het **navigatie-item Voorwaardelijke toegang.**

6. klik op het beleid dat u wilt inspecteren.

7. Controleer of er geen specifieke voorwaarden, toewijzingen of andere instellingen zijn die de toegang van gebruikers kunnen blokkeren.

   >[!NOTE]
   >U dit beleid tijdelijk uitschakelen om ervoor te zorgen dat het geen aanmeldingen beïnvloedt. Stel hiervoor het **inschakelen van** het beleid inschakelen in op **Nee** en klik op de knop **Opslaan.**
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Het beleid voor voorwaardelijke toegang van een specifieke toepassing controleren

Ga als lid van het momenteel geconfigureerde beleid voor voorwaardelijke toegang van één toepassing controleren of valideren:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Enterprise-toepassingen** in het navigatiemenu.

5.  klik op **Alle toepassingen**.

6.  Zoek naar de toepassing waarin u geïnteresseerd bent of de gebruiker probeert zich aan te melden op de naam of toepassings-id van de toepassing.

     >[!NOTE]
     >Als u de toepassing die u zoekt niet ziet, klikt u op de knop **Filter** en breidt u het bereik van de lijst uit naar **Alle toepassingen**. Als u meer kolommen wilt zien, klikt u op de knop **Kolommen** om extra details voor uw toepassingen toe te voegen.
     >
     >

7.  klik op het **navigatie-item Voorwaardelijke toegang.**

8.  klik op het beleid dat u wilt inspecteren.

9.  Controleer of er geen specifieke voorwaarden, toewijzingen of andere instellingen zijn die de toegang van gebruikers kunnen blokkeren.

     >[!NOTE]
     >U dit beleid tijdelijk uitschakelen om ervoor te zorgen dat het geen aanmeldingen beïnvloedt. Stel hiervoor het **inschakelen van** het beleid inschakelen in op **Nee** en klik op de knop **Opslaan.**
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Een specifiek beleid voor voorwaardelijke toegang uitschakelen

Eén voorwaardelijk toegangsbeleid controleren of valideren:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Enterprise-toepassingen** in het navigatiemenu.

5.  klik op het **navigatie-item Voorwaardelijke toegang.**

6.  klik op het beleid dat u wilt inspecteren.

7.  Schakel het beleid uit door het **inschakelen van** het beleid inschakelen in te stellen op **Nee** en klik op de knop **Opslaan.**

## <a name="problems-with-application-consent"></a>Problemen met de toestemming van de aanvraag

Toegang tot de toepassing kan worden geblokkeerd omdat de procedure voor de juiste toestemming sperdie niet is uitgevoerd. Hieronder volgen enkele manieren waarop u problemen met de toestemming van toepassingen oplossen en oplossen:

-   [Een toestemmingsbewerking op gebruikersniveau uitvoeren](#perform-a-user-level-consent-operation)

-   [Toestemmingsbewerking op administratorniveau uitvoeren voor elke toepassing](#perform-administrator-level-consent-operation-for-any-application)

-   [Toestemming op administratorniveau uitvoeren voor een toepassing met één tenant](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Toestemming op administratorniveau uitvoeren voor een toepassing met meerdere tenants](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Een toestemmingsbewerking op gebruikersniveau uitvoeren

-   Als u voor elke toepassing met Open ID Connect die machtigingen opvraagt, voert u naar het aanmeldingsscherm van de toepassing een toestemming op gebruikersniveau uit voor de toepassing voor de aangemelde gebruiker.

-   Zie [Individuele toestemming van de gebruiker aanvragen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent)als u dit programmatisch wilt doen.

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Toestemmingsbewerking op administratorniveau uitvoeren voor elke toepassing

-   Voor **alleen toepassingen die zijn ontwikkeld met behulp van het V1-toepassingsmodel,** u deze toestemming op administratorniveau afdwingen door **?prompt=admin\_consent**toe te voegen aan het einde van de aanmeldings-URL van een toepassing.

-   Voor **elke toepassing die is ontwikkeld met behulp van het V2-toepassingsmodel,** u deze toestemming op administratorniveau afdwingen door de instructies te volgen onder **de sectie Machtigingen aanvragen van een sectie directorybeheerder** van [Het eindpunt van de beheerderstoestemming gebruiken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Toestemming op administratorniveau uitvoeren voor een toepassing met één tenant

-   Voor **toepassingen met één tenant** die machtigingen aanvragen (zoals toepassingen die u in uw organisatie ontwikkelt of bezit), u namens alle gebruikers een **toestemmingsbewerking** op administratief niveau uitvoeren door u aan te melden als globale beheerder en te klikken op de knop Machtigingen **verlenen** boven aan het **aanvraagregister -&gt; Alle toepassingen -&gt; Selecteer een app -&gt; Vereiste machtigingenvenster.**

-   Voor **elke toepassing die is ontwikkeld met behulp van het V1- of V2-toepassingsmodel,** u deze toestemming op administratorniveau afdwingen door de instructies te volgen onder de **sectie Machtigingen aanvragen van een sectie directorybeheerder** van [Het eindpunt van de beheerderstoestemming gebruiken.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Toestemming op administratorniveau uitvoeren voor een toepassing met meerdere tenants

-   Voor **toepassingen met meerdere tenants** die machtigingen aanvragen (zoals een toepassing die een derde partij of Microsoft ontwikkelt), u een **toestemmingsbewerking op administratief niveau** uitvoeren. Meld u aan als globale beheerder en klik op de knop **Machtigingen verlenen** onder de **knop Ondernemingstoepassingen&gt; - Alle toepassingen -&gt; Selecteer een app -&gt; Machtigingen** (binnenkort beschikbaar).

-   U ook deze toestemming op administratorniveau afdwingen door de instructies te volgen onder het **verzoek de machtigingen op te volgen van een sectie directorybeheerder** van [Het eindpunt van de beheerderstoestemming gebruiken.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

## <a name="next-steps"></a>Volgende stappen
[Het eindpunt voor beheerderstoestemming gebruiken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

