---
title: Azure-multifactorverificatie instellen voor Windows Virtual Desktop - Azure
description: Azure-multifactorverificatie instellen voor meer beveiliging in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998478"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication instellen

De Windows-client voor Windows Virtual Desktop is een uitstekende optie voor het integreren van Windows Virtual Desktop met uw lokale machine. Wanneer u uw Windows Virtual Desktop-account echter configureert in de Windows-client, zijn er bepaalde maatregelen die u moet nemen om uzelf en uw gebruikers veilig te houden.

Wanneer u zich voor het eerst aanmeldt, vraagt de client om uw gebruikersnaam, wachtwoord en Azure MFA. Daarna onthoudt de client uw token de volgende keer dat u zich aanmeldt vanuit uw Azure Active Directory (AD) Enterprise-toepassing. Wanneer u **Remember Me**selecteert, kunnen uw gebruikers zich aanmelden nadat ze de client opnieuw hebben opgestart zonder dat ze hun referenties opnieuw hoeven in te voeren.

Hoewel het onthouden van referenties handig is, kan het ook implementaties op Enterprise-scenario's of persoonlijke apparaten minder veilig maken. Om uw gebruikers te beschermen, moet u ervoor zorgen dat de client blijft vragen om MFA-referenties (Azure Multi-Factor Authentication). In dit artikel ziet u hoe u het beleid voor voorwaardelijke toegang voor Windows Virtual Desktop configureert om deze instelling in te schakelen.

## <a name="prerequisites"></a>Vereisten

Dit is wat je nodig hebt om aan de slag te gaan:

- Wijs al uw gebruikers een van de volgende licenties toe:
  - Microsoft 365 E3 of E5
  - Azure Active Directory Premium P1 of P2
  - Enterprise Mobility + Beveiliging E3 of E5
- Een Azure Active Directory-groep met uw gebruikers toegewezen als groepsleden.
- Azure MFA inschakelen voor al uw gebruikers. Zie Hoe u verificatie in [twee stappen voor een gebruiker nodig hebt,](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)voor meer informatie over hoe u dat doen.

>[!NOTE]
>De volgende instelling is ook van toepassing op de [Windows Virtual Desktop-webclient.](https://rdweb.wvd.microsoft.com/webclient/index.html)

## <a name="opt-in-to-the-conditional-access-policy"></a>Aanmelden voor het beleid voor voorwaardelijke toegang

1. **Open Azure Active Directory**.

2. Ga naar het tabblad **Alle toepassingen.** Selecteer in de vervolgkeuzelijst 'Toepassingstype' de optie **Enterprise-toepassingen**en zoek vervolgens naar **Windows Virtual Desktop Client**.

    ![Een screenshot van het tabblad Alle toepassingen. De gebruiker heeft 'windows virtual desktop client' ingevoerd in de zoekbalk en de app is weergegeven in de zoekresultaten.](media/all-applications-search.png)

3. Selecteer **Voorwaardelijke toegang**.

    ![Een schermafbeelding waarop de gebruiker de muiscursor boven het tabblad Voorwaardelijke toegang beweegt.](media/conditional-access-location.png)

4. Selecteer **+ Nieuw beleid**.

   ![Een schermafbeelding van de pagina Voorwaardelijke toegang. De gebruiker zweeft met de muiscursor over de nieuwe beleidsknop.](media/new-policy-button.png)

5. Voer een **naam** in voor de **regel**en **selecteer** vervolgens de *naam van de **groep** die u in de vereisten hebt gemaakt.

6. Selecteer **Selecteren**en selecteer **Vervolgens Gereed**.

7. Open vervolgens **Cloud Apps of acties**.

8. Selecteer **in** het deelvenster Selecteren de **Windows Virtual Desktop** Enterprise-app.

    ![Een schermafbeelding van de pagina Cloud-apps of -acties. De gebruiker heeft de Windows Virtual Desktop-app geselecteerd door het vinkje ernaast te selecteren. De geselecteerde app wordt rood gemarkeerd.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >U moet ook de Windows Virtual Desktop Client-app aan de linkerkant van het scherm zien, zoals in de volgende afbeelding wordt weergegeven. U hebt zowel de Windows Virtual Desktop- als de Windows Virtual Desktop Client Enterprise-apps nodig om het beleid te laten werken.
    >
    > ![Een schermafbeelding van de pagina Cloud-apps of -acties. De apps Windows Virtual Desktop en Windows Virtual Desktop Client zijn rood gemarkeerd.](media/cloud-apps-enterprise-selected.png)

9. Selecteer **Selecteren**

10. Vervolgens opent u **Grant** 

11. Selecteer **Meervoudige verificatie vereisen**en selecteer Vervolgens Een van de geselecteerde **besturingselementen vereisen**.
   
    ![Een screenshot van de Grant-pagina. Er is gekozen voor "Multi-factor authentication".](media/grant-page.png)

    >[!NOTE]
    >Als u mdm-ingeschreven apparaten in uw organisatie hebt en niet wilt dat ze de MFA-prompt weergeven, u ook apparaat vereisen selecteren **dat is gemarkeerd als compatibel**.

12. Selecteer **Sessie**.

13. Stel de **aanmeldingsfrequentie in op** **Actief**en wijzig de waarde vervolgens in **1 uur**.

    ![Een screenshot van de sessiepagina. Het sessiemenu toont dat de vervolgkeuzemenu's voor aanmeldingsfrequentie zijn gewijzigd in '1' en 'Uren'.](media/sign-in-frequency.png)
   
    >[!NOTE]
    >Actieve sessies in uw Windows Virtual Desktop-omgeving blijven werken terwijl u het beleid wijzigt. Als u echter de verbinding verbreekt of aftekent, moet u uw referenties na 60 minuten opnieuw verstrekken. Als u de instellingen wijzigt, u de time-outperiode zoveel verlengen als u wilt (zolang deze in lijn is met het beveiligingsbeleid van uw organisatie).
    >
    >De standaardinstelling is een rollend venster van 90 dagen, wat betekent dat de client gebruikers zal vragen om zich opnieuw aan te melden wanneer ze proberen om toegang te krijgen tot een resource nadat ze gedurende 90 dagen of langer inactief zijn op hun machine.

14. Schakel het beleid in.

15. Selecteer **Maken** om het beleid te bevestigen.

U bent helemaal klaar! Voel je vrij om het beleid te testen om ervoor te zorgen dat uw lijst toestaan werkt zoals bedoeld.
