---
title: Azure Multi-Factor Authentication instellen voor virtueel bureau blad van Windows-Azure
description: Azure Multi-Factor Authentication instellen voor verbeterde beveiliging in Windows virtueel bureau blad.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a769b5584abbd6da89ccb6032e5f0c5ac8ea1cb1
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930519"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Azure Multi-Factor Authentication voor virtueel bureau blad van Windows inschakelen

De Windows-client voor Windows Virtual Desktop is een uitstekende optie voor het integreren van virtuele Windows-Bureau bladen met uw lokale computer. Wanneer u echter uw Windows virtueel-bureaublad account in de Windows-client configureert, zijn er bepaalde metingen die u moet uitvoeren om uzelf en uw gebruikers veilig te houden.

Wanneer u zich voor het eerst aanmeldt, vraagt de client om uw gebruikers naam, wacht woord en Azure MFA. Daarna herinnert de client de volgende keer dat u zich aanmeldt uw token van uw Azure Active Directory (AD) Enter prise-toepassing. Wanneer u **mij onthouden**selecteert, kunnen uw gebruikers zich aanmelden nadat de client opnieuw is opgestart zonder dat ze hun referenties opnieuw moeten invoeren.

Bij het onthouden van referenties is het handig om ook implementaties op bedrijfs scenario's of persoonlijke apparaten minder veilig te maken. Om uw gebruikers te beschermen, moet u ervoor zorgen dat de client vraagt om de referenties voor Azure Multi-Factor Authentication (MFA). In dit artikel wordt uitgelegd hoe u het beleid voor voorwaardelijke toegang configureert voor virtuele Windows-Bureau bladen om deze instelling in te scha kelen.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende informatie nodig om aan de slag te gaan:

- Wijs gebruikers toe aan een licentie die Azure Active Directory Premium P1 of P2 bevat.
- Een Azure Active Directory groep waaraan uw gebruikers zijn toegewezen als groeps leden.
- Schakel Azure MFA in voor al uw gebruikers. Zie [verificatie in twee stappen vereisen voor een gebruiker](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)voor meer informatie over hoe u dit doet.

> [!NOTE]
> De volgende instelling is ook van toepassing op de [Windows virtueel bureau blad-webclient](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

In deze sectie wordt uitgelegd hoe u een beleid voor voorwaardelijke toegang maakt waarvoor multi-factor Authentication is vereist wanneer verbinding wordt gemaakt met een virtueel Windows-bureau blad.

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
2. Blader naar **Azure Active Directory** > **beveiligings** > **voorwaardelijke toegang**.
3. Selecteer **Nieuw beleid**.
4. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
5. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**.
   - Onder **insluiten**selecteert u **gebruikers en groepen** > **gebruikers en groepen** selecteren > kiest u de groep die in de vereisten fase is gemaakt.
   - Selecteer **Done**.
6. Onder **Cloud-apps of acties** > **, selecteert**u **apps selecteren**.
   - Kies **Windows virtueel bureau blad** (app-id 9cdead84-a844-4324-93f2-b2e6bb768d07), **Selecteer**en vervolgens **gereed**.
   
     ![Een scherm opname van de pagina Cloud-apps of-acties. De Windows Virtual Desktop-en Windows Virtual Desktop Client-apps worden rood gemarkeerd.](media/cloud-apps-enterprise.png)

     >[!NOTE]
     >Als u de App-ID wilt vinden van de app die u wilt selecteren, gaat u naar **bedrijfs toepassingen** en selecteert u **micro soft-toepassingen** in de vervolg keuzelijst toepassings type.

7. Onder **toegangs beheer** > **toekennen**selecteert u **toegang verlenen**, **multi-factor Authentication vereisen**en **selecteert**u vervolgens.
8. Onder **toegangs beheer** > **sessie**selecteert u **aanmeldings frequentie**, stelt u de waarde in op **1** en de eenheid op **uren**en selecteert u **selecteren**.
9. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
10. Selecteer **maken** om uw beleid in te scha kelen.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over beleid voor voorwaardelijke toegang](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Meer informatie over de aanmeldings frequentie van gebruikers](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
