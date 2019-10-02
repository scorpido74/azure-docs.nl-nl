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
ms.openlocfilehash: bd66df381e11582b30ded5e3b529070357f35ad2
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720292"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Gebruikers en groepen toewijzen aan een toepassing in Azure Active Directory
Dit artikel leest u hoe gebruikers of groepen toewijzen aan een toepassing in Azure Active Directory (Azure AD). Gebruikers moeten eerst worden toegewezen aan een toepassing voordat u een beheerder kan hun toegang wordt verleend om het volgende doen:

-   Toegang tot een toepassing door **te navigeren naar de URL van de toepassing rechtstreeks** (ook wel bekend als Serviceprovider geïnitieerde eenmalige aanmelding).

-   Toegang tot een toepassing met behulp van de **URL van gebruikerstoegang** op van een toepassing **eigenschappen** pagina (ook wel bekend als id-provider geïnitieerde eenmalige aanmelding).

-   Zie een toepassing worden weergegeven op hun [Toegangsvenster](https://myapps.microsoft.com/) of mobiele toepassing.

-   Zie een toepassing worden weergegeven op hun [startprogramma voor toepassingen van Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

De beschik baarheid van een toewijzing op basis van een groep wordt bepaald door de licentie overeenkomst. Toewijzing op basis van een groep wordt alleen ondersteund voor beveiligings groepen. Geneste groepslid maatschappen en O365-groepen worden momenteel niet ondersteund.

## <a name="prerequisites"></a>Vereisten
Voordat u gebruikers en groepen aan een toepassing toewijzen kunt, moet u de Gebruikerstoewijzing vereisen. Gebruikerstoewijzing vereist:

1. Meld u aan bij Azure portal met een administrator-account.
2. Klik op de **alle services** item in het hoofdmenu.
3. Kies de map die u voor de toepassing gebruikt.
4. Klik op de **bedrijfstoepassingen** tabblad.
5. Selecteer de toepassing uit de lijst met toepassingen die zijn gekoppeld aan deze map.
6. Klik op de **eigenschappen** tabblad.
7. Wijzig de **Gebruikerstoewijzing vereist?** in-/ uitschakelen op Ja.
8. Klik op de **opslaan** knop aan de bovenkant van het scherm.

## <a name="assign-users"></a>Gebruikers toewijzen

Als u wilt toewijzen een of meer gebruikers rechtstreeks aan een toepassing, de volgende stappen uit te voeren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder of als eigenaar van een niet-beheerders toepassing.**

2.  Open de **Azure Active Directory-extensie** door te klikken op **alle services** boven aan het navigatiemenu links belangrijkste.

3.  Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4.  Klik op **bedrijfstoepassingen** in het menu van Azure Active Directory linker navigatie.

5.  Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6.  Selecteer de toepassing die u wilt toewijzen van een gebruiker in de lijst.

7.  Nadat de toepassing wordt geladen, klikt u op **gebruikers en groepen** vanuit navigatiemenu voor links van de toepassing.

8.  Klik op de **toevoegen** knop boven de **gebruikers en groepen** lijst om de **toevoegen toewijzing** deelvenster.

9.  Klik op de **gebruikers en groepen** selector vanaf de **toevoegen toewijzing** deelvenster.

10. Typ in het **volledige naam** of **e-mailadres** van de gebruiker die u geïnteresseerd bent in toewijzen in de **zoeken op naam of e-mailadres** zoekvak.

11. Beweeg de muisaanwijzer over de **gebruiker** in de lijst om weer te geven een **selectievakje**. Klik op het selectievakje naast de profielfoto van de gebruiker of het logo om toe te voegen van de gebruiker naar de **geselecteerde** lijst.

12. **Optioneel:** Als u **meer dan één gebruiker wilt toevoegen**, typt u een andere **volledige naam** of een ander **e-mail adres** in het zoekvak **zoeken op naam of e-mail adres** en klikt u op het selectie vakje om deze gebruiker aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met gebruikers te selecteren, klikt u op de **Selecteer** knop aan de lijst met gebruikers en groepen die moeten worden toegewezen aan de toepassing wilt toevoegen.

14. **Optioneel:** klikt u op de **rol selecteren** selector in de **toevoegen toewijzing** deelvenster te selecteren van een rol toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik op de **toewijzen** knop om de toepassing aan de geselecteerde gebruikers te wijzen.

Na een korte periode kunnen de gebruikers die u hebt geselecteerd deze toepassingen starten met behulp van de methoden die worden beschreven in de sectie Beschrijving van oplossing.

## <a name="assign-groups"></a>Groepen toewijzen

Als u wilt toewijzen een of meer groepen rechtstreeks aan een toepassing, de volgende stappen uit te voeren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of als eigenaar van een niet-beheerders toepassing waaraan een Azure AD Premium-licentie is toegewezen.

2.  Open de **Azure Active Directory-extensie** door te klikken op **alle services** boven aan het navigatiemenu links belangrijkste.

3.  Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4.  Klik op **bedrijfstoepassingen** in het menu van Azure Active Directory linker navigatie.

5.  Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6.  Selecteer de toepassing die u wilt toewijzen van een gebruiker in de lijst.

7.  Nadat de toepassing wordt geladen, klikt u op **gebruikers en groepen** vanuit navigatiemenu voor links van de toepassing.

8.  Klik op de **toevoegen** knop boven de **gebruikers en groepen** lijst om de **toevoegen toewijzing** deelvenster.

9.  Klik op de **gebruikers en groepen** selector vanaf de **toevoegen toewijzing** deelvenster.

10. Typ in het **volledige groepsnaam** van de groep die u geïnteresseerd bent in toewijzen in de **zoeken op naam of e-mailadres** zoekvak.

11. Beweeg de muisaanwijzer over de **groep** in de lijst om weer te geven een **selectievakje**. Klik op het selectievakje naast de profielfoto van de groep of het logo om toe te voegen van de gebruiker naar de **geselecteerde** lijst.

12. **Optioneel:** Als u **meer dan één groep wilt toevoegen**, typt u een andere **volledige groeps naam** in het zoekvak **zoeken op naam of e-mail adres** en klikt u op het selectie vakje om deze groep aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met groepen te selecteren, klikt u op de **Selecteer** knop aan de lijst met gebruikers en groepen die moeten worden toegewezen aan de toepassing wilt toevoegen.

14. **Optioneel:** klikt u op de **rol selecteren** selector in de **toevoegen toewijzing** deelvenster te selecteren van een rol toewijzen aan de groepen die u hebt geselecteerd.

15. Klik op de **toewijzen** om toe te wijzen van de toepassing aan de geselecteerde groepen.

Na een korte periode kunnen gebruikers in de groepen die u hebt geselecteerd deze toepassingen starten met behulp van de methoden die worden beschreven in de sectie Beschrijving van oplossing. Als dit dynamische groepen zijn, mogelijk zijn er enkele aanvullende verwerkingsvertraging in deze toewijzingen worden weergegeven voor gebruikers binnen deze groepen toegewezen.

## <a name="enable-self-service-application-access"></a>Toegang tot Self-servicetoepassingen inschakelen

Toegang tot Self-servicetoepassingen is een uitstekende manier om toe te staan gebruikers voor het zelf detecteren van toepassingen, toestaan de groep bedrijven voor het goedkeuren van toegang tot deze toepassingen. U kunt de bedrijfsgroep voor het beheren van de referenties die zijn toegewezen aan gebruikers voor eenmalige aanmelding op toepassingen wachtwoord rechts van hun toegangsvensters.

Om in te schakelen Self-servicetoepassingen toegang tot een toepassing, de volgende stappen uit te voeren:

1. Open de [ **Azure-portal** ](https://portal.azure.com/) en meld u aan als een **globale beheerder.**

2. Open de **Azure Active Directory-extensie** door te klikken op **alle services** boven aan het navigatiemenu links belangrijkste.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfstoepassingen** in het menu van Azure Active Directory linker navigatie.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6. Selecteer de toepassing die u wilt inschakelen, selfservice toegang tot in de lijst.

7. Nadat de toepassing wordt geladen, klikt u op **selfservice** vanuit navigatiemenu voor links van de toepassing.

8. Om in te schakelen toegang tot Self-servicetoepassingen voor deze toepassing, schakelt u de **toestaan dat gebruikers toegang tot deze toepassing aanvragen?** overzet naar **Ja.**

9. Klik vervolgens op de selector naast het label te selecteren in de groep waaraan de gebruikers die aanvragen toegang tot deze toepassing moet worden toegevoegd, **aan welke groep moeten toegewezen gebruikers worden toegevoegd?** en selecteert u een groep.

10. **Optioneel:** Als u een zakelijke goed keuring wilt vereisen voordat gebruikers toegang worden verleend, **stelt u de**optie **goed keuring vereisen alvorens toegang tot deze toepassing verlenen** in.

11. **Optioneel: Voor toepassingen die alleen gebruikmaken van een wacht woord voor eenmalige aanmelding,**  als u wilt toestaan dat deze goed keurders de wacht woorden opgeven die worden verzonden naar deze toepassing voor goedgekeurde gebruikers, stelt u de **goed keurders toestaan om gebruikers wachtwoorden in te stellen voor deze toepassing?** Schakel over naar **Ja**.

12. **Optioneel:** Als u de zakelijke goed keurders wilt opgeven die de toegang tot deze toepassing mogen goed keuren, klikt u op de kiezer naast het label **dat de toegang tot deze toepassing mag goed keuren?** om Maxi maal 10 afzonderlijke goed keurders voor bedrijven te selecteren.

    >[!NOTE]
    >Groepen worden niet ondersteund.
    >
    >

13. **Optioneel:** Als **u door selfservice**goedgekeurde gebruikers wilt toewijzen aan een rol, klikt u op de selector naast de **rol waaraan gebruikers moeten worden toegewezen in deze toepassing?** om de rol te selecteren waaraan deze gebruikers moeten worden toegewezen.

14. Klik op de **opslaan** knop aan de bovenkant van het deelvenster om te voltooien.

Nadat u Self-servicetoepassingen configuratie is voltooid, ernaartoe kunnen navigeren hun [Toegangsvenster](https://myapps.microsoft.com/) en klikt u op de **+ toevoegen** knop om de apps waarop u selfservice hebt ingeschakeld te vinden de toegang. Fiatteur ziet ook een melding in hun [Toegangsvenster](https://myapps.microsoft.com/). U kunt een melding wanneer een gebruiker heeft toegang tot een toepassing waarvoor goedkeuring aangevraagd e-mail inschakelen. 

Deze goed keuringen ondersteunen alleen één goedkeurings werk stromen, wat betekent dat als u meerdere goed keurders opgeeft, één fiatteur de toegang tot de toepassing kan goed keuren.

## <a name="next-steps"></a>Volgende stappen
[Geef single sign-on bij uw apps met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
