---
title: Aanmelden voor gebruikers op basis van sms voor Azure Active Directory
description: Meer informatie over het configureren en inschakelen van gebruikers om zich aan te melden bij Azure Active Directory met sms (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845948d9aec28ee79a11fb11aaef4cfbf1b263fa
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770557"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>Gebruikers configureren en inschakelen voor verificatie via sms met Azure Active Directory (voorbeeld)

Om de complexiteit en beveiligingsrisico's voor gebruikers om zich aan te melden bij toepassingen en services te verminderen, biedt Azure Active Directory (Azure AD) meerdere verificatieopties. Met verificatie via sms, die momenteel in preview is, kunnen gebruikers zich aanmelden zonder dat ze hun gebruikersnaam en wachtwoord hoeven op te geven of zelfs te weten. Nadat hun account is gemaakt door een identiteitsbeheerder, kunnen ze hun telefoonnummer invoeren bij de aanmeldingsprompt en een verificatiecode opgeven die via sms naar hen wordt verzonden. Deze verificatiemethode vereenvoudigt de toegang tot toepassingen en services, met name voor werknemers in de frontlinie.

In dit artikel ziet u hoe u verificatie via sms inschakelt voor bepaalde gebruikers of groepen in Azure AD.

|     |
| --- |
| Verificatie via sms voor gebruikers is een openbare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) meer informatie over voorvertoningen|
|     |

## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* U hebt *algemene beheerdersrechten* nodig in uw Azure AD-tenant om verificatie via sms in te schakelen.
* Elke gebruiker die is ingeschakeld in het beleid voor verificatiemethode voor sms'en moet een licentie hebben, zelfs als deze deze niet wordt gebruikt. Elke ingeschakelde gebruiker moet een van de volgende Azure AD- of Microsoft 365-licenties hebben:
    * [Azure AD Premium P1 of P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 of F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 of E5][ems-licensing] of [Microsoft 365 (M365) E3 of E5][m365-licensing]

## <a name="limitations"></a>Beperkingen

Tijdens de openbare preview van sms-verificatie zijn de volgende beperkingen van toepassing:

* Verificatie via sms is momenteel niet compatibel met Azure Multi-Factor Authentication.
* Met uitzondering van Teams is verificatie via sms momenteel niet compatibel met native Office-toepassingen.
* Verificatie via sms wordt niet aanbevolen voor B2B-accounts.
* Federatieve gebruikers zullen niet verifiëren in de woninghuurder. Ze verifiëren alleen in de cloud.

## <a name="enable-the-sms-based-authentication-method"></a>De verificatiemethode op basis van sms inschakelen

Er zijn drie belangrijke stappen om verificatie via sms in uw organisatie in te schakelen en te gebruiken:

* Schakel het verificatiemethodebeleid in.
* Selecteer gebruikers of groepen die de verificatiemethode op basis van sms kunnen gebruiken.
* Wijs een telefoonnummer toe voor elk gebruikersaccount.
    * Dit telefoonnummer kan worden toegewezen in de Azure-portal (die wordt weergegeven in dit artikel) en in *Mijn personeel* of *Mijn profiel*.

Laten we eerst verificatie via sms inschakelen voor uw Azure AD-tenant.

1. Meld u aan bij de [Azure-portal][azure-portal] als *globale beheerder.*
1. Zoek naar **Azure Active Directory** en selecteer deze optie.
1. Selecteer in het navigatiemenu aan de linkerkant van het Azure Active Directory-venster de optie **Beveiliging > verificatiemethoden > verificatiemethodebeleid (voorbeeld)**.

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Selecteer **Tekstbericht**in de lijst met beschikbare verificatiemethoden .
1. **Inschakelen instellen op** *Ja*.

    ![Tekstverificatie inschakelen in het beleidsvenster voor verificatiemethoden](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    U ervoor kiezen om verificatie via sms in te schakelen voor *alle gebruikers* of Gebruikers en groepen *selecteren.* In het volgende gedeelte schakelt u verificatie op basis van sms in voor een testgebruiker.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>De verificatiemethode toewijzen aan gebruikers en groepen

Als verificatie op basis van sms is ingeschakeld in uw Azure AD-tenant, selecteert u nu enkele gebruikers of groepen die deze verificatiemethode mogen gebruiken.

1. Stel in het beleidsvenster voor verificatie van sms'en **doel** in *op Gebruikers selecteren*.
1. Kies voor **Gebruikers of groepen toevoegen**en selecteer vervolgens een testgebruiker of -groep, zoals *Contoso-gebruiker* of *Contoso-sms-gebruikers.*

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Wanneer u uw gebruikers of groepen hebt geselecteerd, kiest u **Selecteren**en vervolgens het beleid voor de bijgewerkte verificatiemethode **opslaan.**

Elke gebruiker die is ingeschakeld in het beleid voor verificatiemethode voor sms'en moet een licentie hebben, zelfs als deze deze niet wordt gebruikt. Zorg ervoor dat u over de juiste licenties beschikt voor de gebruikers die u inschakelt in het verificatiemethodebeleid, vooral wanneer u de functie inschakelt voor grote groepen gebruikers.

## <a name="set-a-phone-number-for-user-accounts"></a>Een telefoonnummer instellen voor gebruikersaccounts

Gebruikers zijn nu ingeschakeld voor verificatie via sms, maar hun telefoonnummer moet zijn gekoppeld aan het gebruikersprofiel in Azure AD voordat ze zich kunnen aanmelden. De gebruiker kan [dit telefoonnummer zelf instellen](../user-help/sms-sign-in-explainer.md) in Mijn *profiel*of u het telefoonnummer toewijzen via de Azure-portal. Telefoonnummers kunnen worden ingesteld door *globale beheerders,* *verificatiebeheerders*of *bevoorrechte verificatiebeheerders*.

Wanneer een telefoonnummer is ingesteld voor SMS-sign, is het ook beschikbaar voor gebruik met [Azure Multi-Factor Authentication][tutorial-azure-mfa] en [self-service wachtwoordreset.][tutorial-sspr]

1. Zoek naar **Azure Active Directory** en selecteer deze optie.
1. Selecteer **Gebruikers**in het navigatiemenu aan de linkerkant van het Azure Active Directory-venster .
1. Selecteer de gebruiker die u hebt ingeschakeld voor verificatie via sms in de vorige sectie, zoals *Contoso-gebruiker,* en selecteer **vervolgens Verificatiemethoden**.
1. Voer het telefoonnummer van de gebruiker in, inclusief de landcode, zoals *+1 xxxxxxxxx*. De Azure-portal valideert het telefoonnummer in de juiste indeling.

    ![Een telefoonnummer instellen voor een gebruiker in de Azure-portal om te gebruiken met verificatie via sms](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    Het telefoonnummer moet uniek zijn in uw huurder. Als u hetzelfde telefoonnummer voor meerdere gebruikers probeert te gebruiken, wordt een foutbericht weergegeven.

1. Als u het telefoonnummer wilt toepassen op het account van een gebruiker, selecteert u **Opslaan**.

Wanneer met succes is ingericht, wordt een vinkje weergegeven voor *sms-aanmelding ingeschakeld*.

## <a name="test-sms-based-sign-in"></a>Aanmelden op sms'en

Voer de volgende stappen uit om het gebruikersaccount te testen dat nu is ingeschakeld voor aanmelding via sms:

1. Een nieuw InPrivate- of Incognito-browservenster openen om[https://www.office.com][office]
1. Selecteer **Aanmelden**in de rechterbovenhoek .
1. Voer bij de aanmeldingsprompt het telefoonnummer in dat in de vorige sectie aan de gebruiker is gekoppeld en selecteer **Volgende**.

    ![Voer een telefoonnummer in bij de aanmeldingsprompt voor de testgebruiker](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Er wordt een sms-bericht verzonden naar het opgegeven telefoonnummer. Als u het aanmeldingsproces wilt voltooien, voert u de 6-cijferige code in die bij de aanmeldingsprompt in het sms-bericht wordt weergegeven.

    ![Voer de bevestigingscode in die via sms naar het telefoonnummer van de gebruiker wordt verzonden](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. De gebruiker is nu aangemeld zonder dat hij een gebruikersnaam of wachtwoord hoeft op te geven.

## <a name="troubleshoot-sms-based-sign-in"></a>Problemen met aanmelding op basis van sms'en oplossen

De volgende scenario's en stappen voor het oplossen van problemen kunnen worden gebruikt als u problemen hebt met het inschakelen en gebruiken van aanmelden via sms.

### <a name="phone-number-already-set-for-a-user-account"></a>Telefoonnummer al ingesteld voor een gebruikersaccount

Als een gebruiker zich al heeft geregistreerd voor Azure Multi-Factor Authentication en/of self-service password reset (SSPR), heeft deze gebruiker al een telefoonnummer gekoppeld aan zijn account. Dit telefoonnummer is niet automatisch beschikbaar voor gebruik met sms-gebaseerde aanmelding.

Een gebruiker met een telefoonnummer dat al is ingesteld voor zijn account, krijgt een knop om in te *schakelen voor sms-aanmelding* op de pagina **Mijn profiel.** Selecteer deze knop en het account is ingeschakeld voor gebruik met sms-gebaseerde aanmelding en de vorige Azure Multi-Factor Authentication of SSPR-registratie.

Zie voor meer informatie over de gebruikerservaring de [gebruikerservaring voor sms'en voor telefoonnummer (preview).](../user-help/sms-sign-in-explainer.md)

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Fout bij het instellen van een telefoonnummer op het account van een gebruiker

Als u een fout ontvangt wanneer u een telefoonnummer voor een gebruikersaccount in de Azure-portal probeert in te stellen, controleert u de volgende stappen voor het oplossen van problemen:

1. Zorg ervoor dat u bent ingeschakeld voor de inlogvoorbeeld via sms.
1. Controleer of het gebruikersaccount is ingeschakeld in het beleid voor verificatiemethode *voor sms'en.*
1. Zorg ervoor dat u het telefoonnummer instelt met de juiste opmaak, zoals gevalideerd in de Azure-portal (zoals *+1 4251234567*).
1. Zorg ervoor dat het telefoonnummer niet elders in uw tenant wordt gebruikt.
1. Controleer of er geen stemnummer is ingesteld op het account. Als een spraaknummer is ingesteld, verwijdert u en probeert u het telefoonnummer opnieuw te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie [Wachtwoordloze verificatieopties voor Azure AD voor][concepts-passwordless]aanvullende manieren om u aan te melden bij Azure AD zonder wachtwoord, zoals de Microsoft Authenticator-app of FIDO2-beveiligingssleutels.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
