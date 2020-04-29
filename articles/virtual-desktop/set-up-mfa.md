---
title: Azure multi-factor Authentication instellen voor het virtuele bureau blad van Windows-Azure
description: Azure multi-factor Authentication instellen voor verbeterde beveiliging in Windows virtueel bureau blad.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80998478"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication instellen

De Windows-client voor Windows Virtual Desktop is een uitstekende optie voor het integreren van virtuele Windows-Bureau bladen met uw lokale computer. Wanneer u echter uw Windows virtueel-bureaublad account in de Windows-client configureert, zijn er bepaalde metingen die u moet uitvoeren om uzelf en uw gebruikers veilig te houden.

Wanneer u zich voor het eerst aanmeldt, vraagt de client om uw gebruikers naam, wacht woord en Azure MFA. Daarna herinnert de client de volgende keer dat u zich aanmeldt uw token van uw Azure Active Directory (AD) Enter prise-toepassing. Wanneer u **mij onthouden**selecteert, kunnen uw gebruikers zich aanmelden nadat de client opnieuw is opgestart zonder dat ze hun referenties opnieuw moeten invoeren.

Bij het onthouden van referenties is het handig om ook implementaties op bedrijfs scenario's of persoonlijke apparaten minder veilig te maken. Om uw gebruikers te beschermen, moet u ervoor zorgen dat de client vraagt om de referenties voor Azure Multi-Factor Authentication (MFA). In dit artikel wordt uitgelegd hoe u het beleid voor voorwaardelijke toegang configureert voor virtuele Windows-Bureau bladen om deze instelling in te scha kelen.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende informatie nodig om aan de slag te gaan:

- Wijs al uw gebruikers een van de volgende licenties toe:
  - Microsoft 365 E3 of E5
  - Azure Active Directory Premium P1 of P2
  - Enterprise Mobility + Security E3 of E5
- Een Azure Active Directory groep waaraan uw gebruikers zijn toegewezen als groeps leden.
- Schakel Azure MFA in voor al uw gebruikers. Zie [verificatie in twee stappen vereisen voor een gebruiker](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)voor meer informatie over hoe u dit doet.

>[!NOTE]
>De volgende instelling is ook van toepassing op de [Windows virtueel bureau blad-webclient](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="opt-in-to-the-conditional-access-policy"></a>Aanmelden bij het beleid voor voorwaardelijke toegang

1. Open **Azure Active Directory**.

2. Ga naar het tabblad **alle toepassingen** . Selecteer in de vervolg keuzelijst Type toepassing de optie **bedrijfs toepassingen**en zoek naar **Windows Virtual Desktop Client**.

    ![Een scherm afbeelding van het tabblad alle toepassingen. De gebruiker heeft ' Windows Virtual Desktop Client ' ingevoerd in de zoek balk en de app is weer gegeven in de zoek resultaten.](media/all-applications-search.png)

3. Selecteer **voorwaardelijke toegang**.

    ![Een scherm afbeelding waarin de gebruiker de muis aanwijzer op het tabblad voorwaardelijke toegang plaatst.](media/conditional-access-location.png)

4. Selecteer **+ Nieuw beleid**.

   ![Een scherm afbeelding van de pagina voor voorwaardelijke toegang. De gebruiker beweegt de muis aanwijzer over de knop Nieuw beleid.](media/new-policy-button.png)

5. Voer een **naam** in voor de **regel**en **Selecteer** vervolgens de * naam van de **groep** die u hebt gemaakt in de vereisten.

6. Selecteer **selecteren**en selecteer vervolgens **gereed**.

7. Open vervolgens **Cloud-apps of-acties**.

8. Selecteer in het deel venster **selecteren** de Windows-app voor **virtueel bureau blad** .

    ![Een scherm opname van de pagina Cloud-apps of-acties. De gebruiker heeft de Windows virtueel bureau blad-app geselecteerd door het selectie vakje ernaast in te scha kelen. De geselecteerde app is rood gemarkeerd.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >U moet ook de Windows-client-app voor virtueel bureau blad weer geven die aan de linkerkant van het scherm is geselecteerd, zoals wordt weer gegeven in de volgende afbeelding. U hebt zowel het virtuele bureau blad van Windows als de Enter prise-client van Windows virtueel bureau blad nodig om het beleid te kunnen gebruiken.
    >
    > ![Een scherm opname van de pagina Cloud-apps of-acties. De Windows Virtual Desktop-en Windows Virtual Desktop Client-apps worden rood gemarkeerd.](media/cloud-apps-enterprise-selected.png)

9. Selecteer **selecteren**

10. Open vervolgens **Grant** 

11. Selecteer **multi-factor Authentication vereisen**en selecteer vervolgens **een van de geselecteerde besturings elementen vereisen**.
   
    ![Een scherm opname van de pagina Grant. "Multi-factor Authentication vereisen" is geselecteerd.](media/grant-page.png)

    >[!NOTE]
    >Als u apparaten met MDM-inschrijving in uw organisatie hebt en u niet wilt dat ze de MFA-prompt weer geven, kunt u ook **vereisen dat apparaat is gemarkeerd als compatibel**.

12. Selecteer **sessie**.

13. Stel de **aanmeldings frequentie** in op **actief**en wijzig de waarde in **1 uur**.

    ![Een scherm afbeelding van de sessie pagina. In het menu sessie worden de vervolg keuzelijsten voor de aanmeldings frequentie gewijzigd in ' 1 ' en ' uur '.](media/sign-in-frequency.png)
   
    >[!NOTE]
    >Actieve sessies in uw Windows Virtual Desktop-omgeving blijven werken wanneer u het beleid wijzigt. Als u echter de verbinding verbreekt of afmeldt, moet u na 60 minuten uw referenties opnieuw opgeven. Wanneer u de instellingen wijzigt, kunt u de time-outperiode zo veel mogelijk verlengen (op voor waarde dat deze wordt uitgelijnd met het beveiligings beleid van uw organisatie).
    >
    >De standaard instelling is een doorlopende Window van 90 dagen, wat betekent dat de client gebruikers vraagt om zich opnieuw aan te melden wanneer ze proberen toegang te krijgen tot een bron nadat ze 90 dagen of langer op hun machine inactief zijn.

14. Schakel het beleid in.

15. Selecteer **maken** om het beleid te bevestigen.

U bent helemaal klaar! U kunt het beleid testen om er zeker van te zijn dat de lijst met toegestane onderdelen goed werkt.
