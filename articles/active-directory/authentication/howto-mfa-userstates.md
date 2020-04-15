---
title: Multifactorverificatie per gebruiker inschakelen - Azure Active Directory
description: Meer informatie over het inschakelen van Azure Multi-Factor Authentication per gebruiker door de gebruikersstatus te wijzigen
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8ceaf13324864c7ec3df731c3e710815b0eba9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309788"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Azure multifactorverificatie per gebruiker inschakelen om aanmeldingsgebeurtenissen te beveiligen

Er zijn twee manieren om aanmeldingsgebeurtenissen van gebruikers te beveiligen door meervoudige verificatie in Azure AD te vereisen. De eerste en de voorkeursoptie is het instellen van een beleid voor voorwaardelijke toegang dat multifactorauthenticatie vereist onder bepaalde voorwaarden. De tweede optie is om elke gebruiker in te schakelen voor Azure Multi-Factor Authentication. Wanneer gebruikers afzonderlijk zijn ingeschakeld, voeren ze multifactorauthenticatie uit telkens wanneer ze zich aanmelden (op enkele uitzonderingen na, bijvoorbeeld wanneer ze zich aanmelden vanaf vertrouwde IP-adressen of wanneer de functie _onthouden apparaten_ is ingeschakeld).

> [!NOTE]
> Azure Multi-Factor Authentication inschakelen met behulp van beleid voor voorwaardelijke toegang is de aanbevolen aanpak. Het wijzigen van gebruikersstatussen wordt niet langer aanbevolen, tenzij uw licenties voorwaardelijke toegang niet bevatten, omdat gebruikers mfa moeten uitvoeren elke keer dat ze zich aanmelden.
>
> Zie [Aanmeldingsgebeurtenissen voor gebruikers beveiligen met Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)om aan de slag te gaan met Voorwaardelijke toegang.

## <a name="azure-multi-factor-authentication-user-states"></a>Azure Multi-Factor Authentication-gebruikersstatussen

Gebruikersaccounts in Azure Multi-Factor Authentication hebben de volgende drie verschillende statussen:

> [!IMPORTANT]
> Als u Azure Multi-Factor Authentication inschakelt via een beleid voor voorwaardelijke toegang, wordt de status van de gebruiker niet gewijzigd. Wees niet gealarmeerd als gebruikers uitgeschakeld lijken. Voorwaardelijke toegang verandert de status niet.
>
> **U moet gebruikers niet in- of afdwingen als u beleid voor voorwaardelijke toegang gebruikt.**

| Status | Beschrijving | Niet-browser apps getroffen | Browser-apps getroffen | Moderne verificatie beïnvloed |
|:---:| --- |:---:|:--:|:--:|
| Uitgeschakeld | De standaardstatus voor een nieuwe gebruiker die niet is ingeschreven voor Azure Multi-Factor Authentication. | Nee | Nee | Nee |
| Ingeschakeld | De gebruiker is ingeschreven voor Azure Multi-Factor Authentication, maar is niet geregistreerd. Ze ontvangen een prompt om zich te registreren de volgende keer dat ze zich aanmelden. | Nee.  Ze blijven werken totdat het registratieproces is voltooid. | Ja. Nadat de sessie is verlopen, is Azure Multi-Factor Authentication-registratie vereist.| Ja. Nadat het toegangstoken is verlopen, is Azure Multi-Factor Authentication-registratie vereist. |
| Afgedwongen | De gebruiker is ingeschreven en heeft het registratieproces voor Azure Multi-Factor Authentication voltooid. | Ja. Apps vereisen app-wachtwoorden. | Ja. Azure Multi-Factor Authentication is vereist bij het inloggen. | Ja. Azure Multi-Factor Authentication is vereist bij het inloggen. |

De status van een gebruiker geeft aan of een beheerder deze heeft ingeschreven in Azure Multi-Factor Authentication en of deze het registratieproces heeft voltooid.

Alle gebruikers beginnen *uitgeschakeld .* Wanneer u gebruikers inschrijft voor Azure Multi-Factor Authentication, wordt de status *gewijzigd in Ingeschakeld.* Wanneer gebruikers die zijn ingeschakeld zich aanmelden en het registratieproces voltooien, worden de statuswijzigingen *in Afgedwongen*.

> [!NOTE]
> Als MFA opnieuw is ingeschakeld op een gebruikersobject dat al registratiegegevens heeft, zoals telefoon of e-mail, moeten beheerders die gebruiker MFA opnieuw laten registreren via Azure portal of PowerShell. Als de gebruiker zich niet opnieuw registreert, wordt de MFA-status niet *overgezet* van Ingeschakeld naar *Afgedwongen* in de gebruikersinterface van MFA-beheer.

## <a name="view-the-status-for-a-user"></a>De status van een gebruiker weergeven

Gebruik de volgende stappen om toegang te krijgen tot de Azure-portalpagina waar u gebruikersstatussen bekijken en beheren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als beheerder.
1. Zoeken naar en selecteer *Azure Active Directory*en selecteer vervolgens **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Meervoudige verificatie**. Mogelijk moet u naar rechts scrollen om deze menuoptie te zien. Selecteer de onderstaande voorbeeldschermafbeelding om het volledige Azure-portalvenster en de locatie van het menu te bekijken:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Multifactorverificatie selecteren in het venster Gebruikers in Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Er wordt een nieuwe pagina geopend die de gebruikersstatus weergeeft, zoals in het volgende voorbeeld wordt weergegeven.
   ![Schermafbeelding van voorbeeldgegevens van gebruikersstatus voor Azure Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>De status voor een gebruiker wijzigen

Voer de volgende stappen uit om de azure multi-factor verificatiestatus voor een gebruiker te wijzigen:

1. Gebruik de voorgaande stappen om naar de pagina Azure Multi-Factor **Authentication-gebruikers** te gaan.
1. Zoek de gebruiker die u wilt inschakelen voor Azure Multi-Factor Authentication. Mogelijk moet u de weergave bovenaan wijzigen in **gebruikers.**
   ![Selecteer de gebruiker waarvoor u de status wilt wijzigen op het tabblad Gebruikers](./media/howto-mfa-userstates/enable1.png)
1. Schakel het selectievakje in naast de naam(en) van de gebruiker(en) om de status te wijzigen.
1. Kies aan de rechterkant, onder **snelle stappen,** **inschakelen** of **Uitschakelen**. In het volgende voorbeeld heeft de gebruiker *John Smith* een controle naast ![zijn naam en wordt deze ingeschakeld voor gebruik: Geselecteerde gebruiker inschakelen door op Inschakelen te klikken in het menu Snelle stappen](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Ingeschakelde* gebruikers worden automatisch overgeschakeld naar *Afteformeer wanneer* ze zich registreren voor Azure Multi-Factor Authentication. Wijzig de gebruikersstatus niet handmatig in *Afgedwongen*.

1. Bevestig uw selectie in het pop-upvenster dat wordt geopend.

Nadat u gebruikers hebt ingeschakeld, stelt u ze hiervan per e-mail op de hoogte. Vertel de gebruikers dat er een prompt wordt weergegeven om hen te vragen zich te registreren wanneer ze zich de volgende keer aanmelden. Als uw organisatie ook niet-browser-apps gebruikt die geen moderne verificatie ondersteunen, moeten ze app-wachtwoorden maken. Zie de [gebruikershandleiding azure multi-factor authenticatie](../user-help/multi-factor-authentication-end-user.md) om hen op weg te helpen voor meer informatie.

## <a name="change-state-using-powershell"></a>Status wijzigen met PowerShell

Als u de gebruikersstatus wilt wijzigen met `$st.State` Azure AD [PowerShell,](/powershell/azure/overview)wijzigt u de parameter voor een gebruikersaccount. Er zijn drie mogelijke statussen voor een gebruikersaccount:

* *Ingeschakeld*
* *Afgedwongen*
* *Handicap*  

Verplaats gebruikers niet rechtstreeks naar de *afgedwongen* status. Als u dit doet, werken apps op basis van niet-browser niet meer omdat de gebruiker de registratie van Azure Multi-Factor Authentication niet heeft doorlopen en een [app-wachtwoord](howto-mfa-mfasettings.md#app-passwords)heeft verkregen.

Installeer de *MSOnline-module* met [Install-Module](/powershell/module/powershellget/install-module) als volgt om aan de slag te gaan:

```PowerShell
Install-Module MSOnline
```

Maak vervolgens verbinding via [Connect-MsolService:](/powershell/module/msonline/connect-msolservice)

```PowerShell
Connect-MsolService
```

In het volgende voorbeeld powershell-script *bsimon@contoso.com*wordt MFA instaat voor een individuele gebruiker met de naam:

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Het gebruik van PowerShell is een goede optie wanneer u gebruikers in bulk moet inschakelen. Het volgende script lussen door middel van een lijst van gebruikers en maakt MFA op hun accounts. Definieer de gebruikersaccounts die deze `$users` in de eerste regel als volgt instellen:

   ```PowerShell
   # Define your list of users to update state in bulk
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

Als u MFA wilt uitschakelen, wordt in het volgende voorbeeld een gebruiker met [Get-MsolUser](/powershell/module/msonline/get-msoluser)verwijderd en worden alle *StrongAuthenticationRequirements-vereisten* voor de gedefinieerde gebruiker verwijderd met [Set-MsolUser:](/powershell/module/msonline/set-msoluser)

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

U MFA ook rechtstreeks uitschakelen voor een gebruiker met [Set-MsolUser](/powershell/module/msonline/set-msoluser) als volgt:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gebruikers converteren van MFA per gebruiker naar op voorwaardelijke toegang gebaseerde MFA

De volgende PowerShell kan u helpen bij het maken van de conversie naar voorwaardelijke toegang op basis van Azure Multi-Factor Authentication.

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
> We hebben onlangs het gedrag en dit PowerShell-script gewijzigd. Voorheen heeft het script de MFA-methoden opgeslagen, MFA uitgeschakeld en de methoden hersteld. Dit is niet langer nodig nu het standaardgedrag voor uitschakelen de methoden niet meer wist.
>
> Als MFA opnieuw is ingeschakeld op een gebruikersobject dat al registratiegegevens heeft, zoals telefoon of e-mail, moeten beheerders die gebruiker MFA opnieuw laten registreren via Azure portal of PowerShell. Als de gebruiker zich niet opnieuw registreert, wordt de MFA-status niet *overgezet* van Ingeschakeld naar *Afgedwongen* in de gebruikersinterface van MFA-beheer.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Multi-Factor Authentication-instellingen configureren](howto-mfa-mfasettings.md)als vertrouwde IP's, aangepaste spraakberichten en fraudemeldingen. Zie [Gebruikersinstellingen beheren met Azure Multi-Factor Authentication](howto-mfa-userdevicesettings.md)als u gebruikersinstellingen voor Azure Multi-Factor Authentication wilt beheren.

Zie [Azure Multi-Factor Authentication-rapporten](howto-mfa-reporting.md#azure-ad-sign-ins-report)voor het uitvoeren van MFA om te begrijpen waarom een gebruiker is gevraagd of niet is gevraagd om MFA uit te voeren.
