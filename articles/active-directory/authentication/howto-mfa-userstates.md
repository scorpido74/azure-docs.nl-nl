---
title: Multi-Factor-verificatie per gebruiker - Azure Active Directory
description: Schakel MFA in door gebruikersstatussen te wijzigen in Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e955a3d10eea0a23154396a44fe6e1fc552a9ccb
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653480"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Verificatie in twee stappen voor een gebruiker vereisen

U een van de twee benaderingen gebruiken voor het vereisen van verificatie in twee stappen, waarvoor beide een algemeen beheerdersaccount moeten worden gebruikt. De eerste optie is om elke gebruiker in te schakelen voor Azure Multi-Factor Authentication (MFA). Wanneer gebruikers afzonderlijk zijn ingeschakeld, voeren ze elke keer dat ze zich aanmelden in twee stappen uit (op enkele uitzonderingen na, bijvoorbeeld wanneer ze zich aanmelden vanaf vertrouwde IP-adressen of wanneer de functie _onthouden apparaten_ is ingeschakeld). De tweede optie is het instellen van een beleid voor voorwaardelijke toegang dat verificatie in twee stappen vereist onder bepaalde voorwaarden.

> [!TIP]
> Azure Multi-Factor Authentication inschakelen met behulp van beleid voor voorwaardelijke toegang is de aanbevolen aanpak. Het wijzigen van gebruikersstatussen wordt niet langer aanbevolen, tenzij uw licenties voorwaardelijke toegang niet bevatten, omdat gebruikers mfa moeten uitvoeren elke keer dat ze zich aanmelden.

## <a name="choose-how-to-enable"></a>Kies hoe u inschakelt

**Ingeschakeld door de gebruikersstatus te wijzigen** - Dit is de traditionele methode voor het vereisen van verificatie in twee stappen en wordt besproken in dit artikel. Het werkt met zowel Azure MFA in de cloud als Azure MFA Server. Met deze methode moeten gebruikers tweestapsverificatie uitvoeren **elke keer dat** ze zich aanmelden en beleid voor voorwaardelijke toegang overschrijven.

**Ingeschakeld door beleid voor voorwaardelijke toegang** - Dit is het meest flexibele middel om verificatie in twee stappen voor uw gebruikers mogelijk te maken. Het inschakelen van het beleid voorwaardelijke toegang werkt alleen voor Azure MFA in de cloud en is een premium functie van Azure AD. Meer informatie over deze methode is te vinden in [Deploy cloud-based Azure Multi-Factor Authentication](howto-mfa-getstarted.md).

**Ingeschakeld door Azure AD-identiteitsbeveiliging** - Deze methode gebruikt het risicobeleid voor Azure AD-identiteitsbeveiliging om verificatie in twee stappen te vereisen die alleen is gebaseerd op aanmeldingsrisico's voor alle cloudtoepassingen. Voor deze methode is Azure Active Directory P2-licenties vereist. Meer informatie over deze methode vindt u in [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Meer informatie over licenties en prijzen vindt u op de prijspagina's [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) en [Multi-Factor Authentication.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

## <a name="enable-azure-mfa-by-changing-user-state"></a>Azure MFA inschakelen door gebruikersstatus te wijzigen

Gebruikersaccounts in Azure Multi-Factor Authentication hebben de volgende drie verschillende statussen:

> [!IMPORTANT]
> Als u Azure MFA inschakelt via een beleid voor voorwaardelijke toegang, wordt de status van de gebruiker niet gewijzigd. Wees niet gealarmeerd gebruikers verschijnen uitgeschakeld. Voorwaardelijke toegang verandert de status niet. **Organisaties mogen gebruikers niet in- of afdwingen als ze gebruikmaken van beleid voor voorwaardelijke toegang.**

| Status | Beschrijving | Niet-browser apps getroffen | Browser-apps getroffen | Moderne verificatie beïnvloed |
|:---:| --- |:---:|:--:|:--:|
| Uitgeschakeld | De standaardstatus voor een nieuwe gebruiker die niet is ingeschreven voor Azure MFA. | Nee | Nee | Nee |
| Ingeschakeld | De gebruiker is ingeschreven in Azure MFA, maar is niet geregistreerd. Ze ontvangen een prompt om zich te registreren de volgende keer dat ze zich aanmelden. | Nee.  Ze blijven werken totdat het registratieproces is voltooid. | Ja. Nadat de sessie is verlopen, is Azure MFA-registratie vereist.| Ja. Nadat het toegangstoken is verlopen, is Azure MFA-registratie vereist. |
| Afgedwongen | De gebruiker is ingeschreven en heeft het registratieproces voor Azure MFA voltooid. | Ja. Apps vereisen app-wachtwoorden. | Ja. Azure MFA is vereist bij het inloggen. | Ja. Azure MFA is vereist bij het inloggen. |

De status van een gebruiker geeft aan of een beheerder deze heeft ingeschreven in Azure MFA en of deze het registratieproces heeft voltooid.

Alle gebruikers beginnen *uitgeschakeld .* Wanneer u gebruikers inschrijft in Azure MFA, wordt de status *gewijzigd in Ingeschakeld*. Wanneer gebruikers die zijn ingeschakeld zich aanmelden en het registratieproces voltooien, worden de statuswijzigingen *in Afgedwongen*.

> [!NOTE]
> Als MFA opnieuw is ingeschakeld op een gebruikersobject dat al registratiegegevens heeft, zoals telefoon of e-mail, moeten beheerders die gebruiker MFA opnieuw laten registreren via Azure portal of PowerShell. Als de gebruiker zich niet opnieuw registreert, wordt de MFA-status niet *overgezet* van Ingeschakeld naar *Afgedwongen* in de gebruikersinterface van MFA-beheer.

### <a name="view-the-status-for-a-user"></a>De status van een gebruiker weergeven

Gebruik de volgende stappen om toegang te krijgen tot de pagina waar u gebruikersstatussen bekijken en beheren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als beheerder.
2. Zoek naar *Azure Active Directory* en selecteer deze optie. Selecteer **Gebruikers** > **Alle gebruikers**.
3. Selecteer **Meervoudige verificatie**. Mogelijk moet u naar rechts scrollen om deze menuoptie te zien. Selecteer de onderstaande voorbeeldschermafbeelding om het volledige Azure-portalvenster en de locatie van het menu te bekijken:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Multifactorverificatie selecteren in het venster Gebruikers in Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
4. Een nieuwe pagina die de gebruikersstatussen weergeeft, wordt geopend.
   ![gebruikersstatus van meervoudige verificatie - schermafbeelding](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>De status voor een gebruiker wijzigen

1. Gebruik de voorgaande stappen om naar de pagina Azure Multi-Factor **Authentication-gebruikers** te gaan.
2. Zoek de gebruiker die u wilt inschakelen voor Azure MFA. Mogelijk moet u de weergave bovenaan wijzigen.
   ![Selecteer de gebruiker waarvoor u de status wilt wijzigen op het tabblad Gebruikers](./media/howto-mfa-userstates/enable1.png)
3. Schakel het selectievakje naast hun naam in.
4. Kies rechts onder **snelle stappen**de optie **In-** of **uitschakelen**.
   ![Geselecteerde gebruiker inschakelen door op Inschakelen te klikken in het menu Snelle stappen](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Ingeschakelde* gebruikers worden automatisch overgeschakeld naar *Afgedwongen* wanneer ze zich registreren voor Azure MFA. Wijzig de gebruikersstatus niet handmatig in *Afgedwongen*.

5. Bevestig uw selectie in het pop-upvenster dat wordt geopend.

Nadat u gebruikers hebt ingeschakeld, stelt u ze hiervan per e-mail op de hoogte. Zeg dat ze worden gevraagd zich te registreren wanneer ze zich de volgende keer aanmelden. Als uw organisatie ook niet-browser-apps gebruikt die geen moderne verificatie ondersteunen, moeten ze app-wachtwoorden maken. U ook een koppeling naar de [Azure MFA-handleiding voor eindgebruikers](../user-help/multi-factor-authentication-end-user.md) opnemen om hen op weg te helpen.

### <a name="use-powershell"></a>PowerShell gebruiken

Als u de gebruikersstatus wilt wijzigen `$st.State`met Azure AD [PowerShell](/powershell/azure/overview), wijzigt u . Er zijn drie mogelijke staten:

* Ingeschakeld
* Afgedwongen
* Uitgeschakeld  

Verplaats gebruikers niet rechtstreeks naar de *afgedwongen* status. Als u dit doet, werken apps op basis van niet-browser niet meer omdat de gebruiker de Azure MFA-registratie niet heeft doorlopen en een [app-wachtwoord](howto-mfa-mfasettings.md#app-passwords)heeft verkregen.

Installeer de module eerst met behulp van:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Vergeet niet eerst verbinding te maken via **Connect-MsolService**

   ```PowerShell
   Connect-MsolService
   ```

In dit voorbeeld maakt PowerShell-script MFA voor een individuele gebruiker mogelijk:

   ```PowerShell
   Import-Module MSOnline
   Connect-MsolService
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

Het gebruik van PowerShell is een goede optie wanneer u gebruikers in bulk moet inschakelen. Als voorbeeld, de volgende script lussen door middel van een lijst van gebruikers en maakt MFA op hun rekeningen:

   ```PowerShell
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Als u MFA wilt uitschakelen, gebruikt u dit script:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
   ```

die ook kan worden ingekort tot:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gebruikers converteren van MFA per gebruiker naar op voorwaardelijke toegang gebaseerde MFA

De volgende PowerShell kan u helpen bij het maken van de conversie naar voorwaardelijke toegang op basis van Azure Multi-Factor Authentication.

Voer deze PowerShell uit in een ISE-venster of sla op als een . PS1-bestand om lokaal uit te voeren.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> We hebben onlangs het gedrag en PowerShell script hierboven dienovereenkomstig veranderd. Voorheen heeft het script de MFA-methoden opgeslagen, MFA uitgeschakeld en de methoden hersteld. Dit is niet langer nodig nu het standaardgedrag voor uitschakelen de methoden niet meer wist.
>
> Als MFA opnieuw is ingeschakeld op een gebruikersobject dat al registratiegegevens heeft, zoals telefoon of e-mail, moeten beheerders die gebruiker MFA opnieuw laten registreren via Azure portal of PowerShell. Als de gebruiker zich niet opnieuw registreert, wordt de MFA-status niet *overgezet* van Ingeschakeld naar *Afgedwongen* in de gebruikersinterface van MFA-beheer.

## <a name="next-steps"></a>Volgende stappen

* Waarom is een gebruiker gevraagd of niet gevraagd om MFA uit te voeren? Zie de sectie [Azure AD-aanmeldingsrapport in het document Rapporten in Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Zie het artikel [Azure Multi-Factor Authentication settings](howto-mfa-mfasettings.md) configureren voor het configureren van aanvullende instellingen zoals vertrouwde IP's, aangepaste spraakberichten en fraudemeldingen.
* Informatie over het beheren van gebruikersinstellingen voor Azure Multi-Factor Authentication vindt u in het artikel [Gebruikersinstellingen beheren met Azure Multi-Factor Authentication in de cloud](howto-mfa-userdevicesettings.md)
