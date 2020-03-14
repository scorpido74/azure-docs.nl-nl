---
title: Gebruikers en groepen toewijzen aan een toepassing | Microsoft Docs
description: Gebruikers toewijzen aan de toepassing om toegang te verlenen
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd6b13dc56f8f948d50e2b3564712ed8f5b1476
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253388"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Gebruikers en groepen toewijzen aan een toepassing in Azure Active Directory
Dit artikel leest u hoe gebruikers of groepen toewijzen aan een toepassing in Azure Active Directory (Azure AD). Gebruikers moeten eerst worden toegewezen aan een toepassing voordat u een beheerder kan hun toegang wordt verleend om het volgende doen:

-   Toegang tot een toepassing door **rechtstreeks naar de URL van de toepassing te navigeren** (ook wel bekend als door SP geïnitieerde aanmelding).

-   Toegang tot een toepassing met behulp van de **URL voor gebruikers toegang** op de **Eigenschappen** pagina van een toepassing (ook wel bekend als IDP-started sign on).

-   Een toepassing weer geven op het [toegangs venster](https://myapps.microsoft.com/) of mobiele toepassing van de toepassing.

-   Een toepassing weer geven op het [Start programma van de Office 365-toepassing](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

De beschik baarheid van een toewijzing op basis van een groep wordt bepaald door de licentie overeenkomst. Toewijzing op basis van een groep wordt alleen ondersteund voor beveiligings groepen. Geneste groepslid maatschappen en O365-groepen worden momenteel niet ondersteund.

## <a name="configure-the-application-to-require-assignment"></a>De toepassing configureren voor het vereisen van de toewijzing

Een toepassing kan zo worden geconfigureerd dat de toewijzing wordt vereist voordat deze kan worden geopend. Toewijzing vereist:

1. Meld u aan bij de Azure Portal met een beheerders account of als eigenaar van de app onder **Enter prise-apps**.
2. Klik op het item **alle services** in het hoofd menu.
3. Kies de map die u voor de toepassing gebruikt.
4. Klik op het tabblad **bedrijfs toepassingen** .
5. Selecteer de toepassing uit de lijst met toepassingen die zijn gekoppeld aan deze map.
6. Klik op het tabblad **Eigenschappen** .
7. Wijzig de **gebruikers toewijzing vereist?** Schakel over naar Ja.
8. Klik op de knop **Opslaan** boven aan het scherm.

## <a name="assign-users"></a>Gebruikers toewijzen

Als u wilt toewijzen een of meer gebruikers rechtstreeks aan een toepassing, de volgende stappen uit te voeren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder of als eigenaar van een niet-beheerders toepassing.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** bovenin het navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory links.

5.  Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6.  Selecteer de toepassing die u wilt toewijzen van een gebruiker in de lijst.

7.  Zodra de toepassing is geladen, klikt u op **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

8.  Klik op de knop **toevoegen** boven aan de lijst **gebruikers en groepen** om het deel venster **toewijzing toevoegen** te openen.

9.  Klik op de selector **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

10. Typ de **volledige naam** of het **e-mail adres** van de gebruiker die u wilt toewijzen in het zoekvak **zoeken op naam of e-mail adres** .

11. Beweeg de muis aanwijzer over de **gebruiker** in de lijst om een **selectie vakje**weer te geven. Klik op het selectie vakje naast de foto of het logo van het profiel van de gebruiker om uw gebruiker toe te voegen aan de **geselecteerde** lijst.

12. **Optioneel:** Als u **meer dan één gebruiker wilt toevoegen**, typt u een andere **volledige naam** of een ander **e-mail adres** in het zoekvak **zoeken op naam of e-mail adres** en klikt u op het selectie vakje om deze gebruiker aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op de knop **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** Klik op het selectie vakje **rol** selecteren in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik op de knop **toewijzen** om de toepassing aan de geselecteerde gebruikers toe te wijzen.

Na een korte periode kunnen de gebruikers die u hebt geselecteerd deze toepassingen starten met behulp van de methoden die worden beschreven in de sectie Beschrijving van oplossing.

## <a name="assign-groups"></a>Groepen toewijzen

Als u wilt toewijzen een of meer groepen rechtstreeks aan een toepassing, de volgende stappen uit te voeren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of als eigenaar van een niet-beheerders toepassing waaraan een Azure AD Premium-licentie is toegewezen.

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** bovenin het navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory links.

5.  Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6.  Selecteer de toepassing die u wilt toewijzen van een gebruiker in de lijst.

7.  Zodra de toepassing is geladen, klikt u op **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

8.  Klik op de knop **toevoegen** boven aan de lijst **gebruikers en groepen** om het deel venster **toewijzing toevoegen** te openen.

9.  Klik op de selector **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

10. Typ de **volledige groeps naam** van de groep die u wilt toewijzen in het zoekvak **zoeken op naam of e-mail adres** .

11. Beweeg de muis aanwijzer over de **groep** in de lijst om een **selectie vakje**weer te geven. Klik op het selectie vakje naast het profiel foto of logo van de groep om uw gebruiker toe te voegen aan de **geselecteerde** lijst.

12. **Optioneel:** Als u **meer dan één groep wilt toevoegen**, typt u een andere **volledige groeps naam** in het zoekvak **zoeken op naam of e-mail adres** en klikt u op het selectie vakje om deze groep aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met het selecteren van groepen, klikt u op de knop **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** Klik op het selectie vakje **rol** selecteren in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de groepen die u hebt geselecteerd.

15. Klik op de knop **toewijzen** om de toepassing toe te wijzen aan de geselecteerde groepen.

Na een korte periode kunnen gebruikers in de groepen die u hebt geselecteerd deze toepassingen starten met behulp van de methoden die worden beschreven in de sectie Beschrijving van oplossing. Als dit dynamische groepen zijn, mogelijk zijn er enkele aanvullende verwerkingsvertraging in deze toewijzingen worden weergegeven voor gebruikers binnen deze groepen toegewezen.

## <a name="enable-self-service-application-access"></a>Toegang tot Self-servicetoepassingen inschakelen

Toegang tot Self-servicetoepassingen is een uitstekende manier om toe te staan gebruikers voor het zelf detecteren van toepassingen, toestaan de groep bedrijven voor het goedkeuren van toegang tot deze toepassingen. U kunt de bedrijfsgroep voor het beheren van de referenties die zijn toegewezen aan gebruikers voor eenmalige aanmelding op toepassingen wachtwoord rechts van hun toegangsvensters.

Om in te schakelen Self-servicetoepassingen toegang tot een toepassing, de volgende stappen uit te voeren:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** bovenin het navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory links.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing die u wilt inschakelen, selfservice toegang tot in de lijst.

7. Zodra de toepassing is geladen, klikt u op **self-service** in het navigatie menu aan de linkerkant van de toepassing.

8. Als u toegang voor selfservice toepassingen voor deze toepassing wilt inschakelen, schakelt u de optie **gebruikers toestaan om toegang te vragen tot deze toepassing?** in op **Ja.**

9. Vervolgens selecteert u de groep waartoe gebruikers die toegang tot deze toepassing vragen moeten worden toegevoegd, klikt u op de selector naast het label **waaraan gebruikers moeten worden toegevoegd** , en selecteert u een groep.

10. **Optioneel:** Als u een zakelijke goed keuring wilt vereisen voordat gebruikers toegang worden verleend, **stelt u de**optie **goed keuring vereisen alvorens toegang tot deze toepassing verlenen** in.

11. **Optioneel: voor toepassingen die alleen gebruikmaken van wacht woord voor eenmalige aanmelding,** als u wilt toestaan dat de goed keurders van deze gebruiker de wacht woorden opgeven die naar deze toepassing worden verzonden voor goedgekeurde gebruikers, stelt u de **goed keurders toestaan om gebruikers wachtwoorden in te stellen voor deze toepassing?** **in-of uitschakelen.**

12. **Optioneel:** Als u de zakelijke goed keurders wilt opgeven die de toegang tot deze toepassing mogen goed keuren, klikt u op de kiezer naast het label **dat de toegang tot deze toepassing mag goed keuren?** om Maxi maal 10 afzonderlijke goed keurders voor bedrijven te selecteren.

    >[!NOTE]
    >Groepen worden niet ondersteund.
    >
    >

13. **Optioneel:** **voor toepassingen die rollen**weer geven, kunt u, als u goedgekeurde selfservice gebruikers wilt toewijzen aan een rol, klikken op de selector naast de **rol waaraan gebruikers moeten worden toegewezen in deze toepassing?** om de rol te selecteren waaraan deze gebruikers moeten worden toegewezen.

14. Klik boven aan het deel venster op de knop **Opslaan** om de bewerking te volt ooien.

Zodra u de configuratie van de selfservice toepassing hebt voltooid, kunnen gebruikers naar hun [toepassings toegangs paneel](https://myapps.microsoft.com/) navigeren en op de knop **toevoegen** klikken om de apps te vinden waarvoor u selfservice toegang hebt ingeschakeld. Zakelijke goed keurders zien ook een melding in het [toegangs venster](https://myapps.microsoft.com/)van hun toepassing. U kunt een melding wanneer een gebruiker heeft toegang tot een toepassing waarvoor goedkeuring aangevraagd e-mail inschakelen. 

Deze goed keuringen ondersteunen alleen één goedkeurings werk stromen, wat betekent dat als u meerdere goed keurders opgeeft, één fiatteur de toegang tot de toepassing kan goed keuren.

## <a name="next-steps"></a>Volgende stappen
[Eenmalige aanmelding bieden voor uw apps met toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md)
