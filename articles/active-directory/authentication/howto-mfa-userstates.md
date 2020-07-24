---
title: Multi-Factor Authentication per gebruiker inschakelen-Azure Active Directory
description: Meer informatie over het inschakelen van Azure Multi-Factor Authentication per gebruiker door het wijzigen van de gebruikers status
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 860616cbea598e40494155e250254b3c607c1173
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027492"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Microsoft Azure Multi-Factor Authentication per gebruiker inschakelen om aanmeldingsgebeurtenissen te beveiligen

Er zijn twee manieren om aanmeldings gebeurtenissen van gebruikers te beveiligen door multi-factor Authentication in azure AD te vereisen. De eerste en de voorkeurs optie is het instellen van een beleid voor voorwaardelijke toegang waarvoor multi-factor Authentication onder bepaalde voor waarden is vereist. De tweede optie is om elke gebruiker in te scha kelen voor Azure Multi-Factor Authentication. Wanneer gebruikers afzonderlijk worden ingeschakeld, voeren ze multi-factor Authentication elke keer dat ze zich aanmelden (met enkele uitzonde ringen, zoals wanneer ze zich aanmelden vanaf vertrouwde IP-adressen of wanneer de functie _onthouden apparaten_ is ingeschakeld).

> [!NOTE]
> Het inschakelen van Azure Multi-Factor Authentication met beleids regels voor voorwaardelijke toegang is de aanbevolen methode. Het wijzigen van de gebruikers status wordt niet meer aanbevolen, tenzij uw licenties geen voorwaardelijke toegang bevatten, omdat gebruikers elke keer dat ze zich aanmelden, MFA moeten uitvoeren. Zie [zelf studie: aanmeldings gebeurtenissen voor beveiligde gebruikers met Azure multi-factor Authentication](tutorial-enable-azure-mfa.md)om aan de slag te gaan met behulp van voorwaardelijke toegang.
>
> Voor Azure AD-gratis tenants zonder voorwaardelijke toegang kunt u de [standaard instellingen voor beveiliging gebruiken om gebruikers te beveiligen](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Gebruikers statussen van Azure Multi-Factor Authentication

Gebruikers accounts in azure Multi-Factor Authentication de volgende drie statussen hebben:

> [!IMPORTANT]
> Als Azure Multi-Factor Authentication wordt ingeschakeld via een beleid voor voorwaardelijke toegang, wordt de status van de gebruiker niet gewijzigd. Geen waarschuwing als gebruikers worden uitgeschakeld weer gegeven. De status wordt niet gewijzigd door voorwaardelijke toegang.
>
> **U mag geen gebruikers inschakelen of afdwingen als u beleid voor voorwaardelijke toegang gebruikt.**

| Status | Beschrijving | Betrokken niet-browser-apps | Browser-apps die worden beïnvloed | Betrokken moderne verificatie |
|:---:| --- |:---:|:--:|:--:|
| Uitgeschakeld | De standaard status voor een nieuwe gebruiker die niet is inge schreven in azure Multi-Factor Authentication. | Nee | Nee | Nee |
| Ingeschakeld | De gebruiker is inge schreven bij Azure Multi-Factor Authentication, maar heeft geen geregistreerde verificatie methoden. Er wordt een prompt weer gegeven om de volgende keer dat ze zich aanmelden te registreren. | Nee.  Ze blijven werken totdat het registratie proces is voltooid. | Ja. Nadat de sessie is verlopen, is de registratie van Azure Multi-Factor Authentication vereist.| Ja. Nadat het toegangs token is verlopen, is de registratie van Azure Multi-Factor Authentication vereist. |
| Afgedwongen | De gebruiker is inge schreven en heeft het registratie proces voor Azure Multi-Factor Authentication voltooid. | Ja. Apps vereisen app-wacht woorden. | Ja. Azure Multi-Factor Authentication is vereist bij de aanmelding. | Ja. Azure Multi-Factor Authentication is vereist bij de aanmelding. |

De status van een gebruiker geeft aan of een beheerder deze heeft inge schreven in azure Multi-Factor Authentication en of het registratie proces is voltooid.

Alle gebruikers worden *uitgeschakeld*. Wanneer u gebruikers inschrijft in azure Multi-Factor Authentication, verandert de status in *ingeschakeld*. Wanneer ingeschakelde gebruikers zich aanmelden en het registratie proces volt ooien, wordt de status ervan gewijzigd in *afgedwongen*.

> [!NOTE]
> Als MFA opnieuw wordt ingeschakeld voor een gebruikers object dat al registratie details, zoals een telefoon of e-mail adres, heeft, moeten beheerders die gebruiker MFA opnieuw registreren via Azure Portal of Power shell. Als de gebruiker zich niet opnieuw registreert, wordt de MFA-status niet overgezet van *ingeschakeld* om te worden *afgedwongen* in de gebruikers interface voor MFA-beheer.

## <a name="view-the-status-for-a-user"></a>De status van een gebruiker weer geven

Gebruik de volgende stappen om toegang te krijgen tot de Azure Portal pagina waar u de gebruikers status kunt weer geven en beheren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als beheerder.
1. Zoek en selecteer *Azure Active Directory*en selecteer vervolgens **gebruikers**  >  **alle gebruikers**.
1. Selecteer **Multi-Factor Authentication**. Mogelijk moet u naar rechts schuiven om deze menu optie weer te geven. Selecteer de onderstaande scherm afbeelding om het volledige Azure Portal venster en de menu locatie weer te geven:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Multi-Factor Authentication selecteren in het venster gebruikers in azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Er wordt een nieuwe pagina geopend waarin de gebruikers status wordt weer gegeven, zoals in het volgende voor beeld wordt weer gegeven.
   ![Scherm afbeelding met voor beeld van informatie over de gebruikers status voor Azure Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>De status voor een gebruiker wijzigen

Als u de status van Azure Multi-Factor Authentication voor een gebruiker wilt wijzigen, voert u de volgende stappen uit:

1. Gebruik de voor gaande stappen om naar de pagina Azure Multi-Factor Authentication- **gebruikers** te gaan.
1. Zoek de gebruiker die u wilt inschakelen voor Azure Multi-Factor Authentication. Mogelijk moet u de weer gave boven aan **gebruikers**wijzigen.
   ![Selecteer de gebruiker waarvan u de status wilt wijzigen van het tabblad gebruikers](./media/howto-mfa-userstates/enable1.png)
1. Schakel het selectie vakje in naast de naam (en) van de gebruiker (s) waarvan u de status wilt wijzigen.
1. Klik aan de rechter kant onder **snelle stappen**op **inschakelen** of **uitschakelen**. In het volgende voor beeld heeft de gebruiker *John Smith* een vinkje naast hun naam en wordt ingeschakeld voor gebruik: ![ Schakel geselecteerde gebruiker inschakelen door op inschakelen te klikken in het menu snelle stappen](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Ingeschakelde* gebruikers worden automatisch overgeschakeld naar *afgedwongen* wanneer ze zich registreren voor Azure multi-factor Authentication. Wijzig de gebruikers status niet hand matig in *afdwinging*.

1. Bevestig uw selectie in het pop-upvenster dat wordt geopend.

Nadat u gebruikers hebt ingeschakeld, stelt u deze via e-mail op de hoogte. Vertel de gebruikers dat een prompt wordt weer gegeven om te vragen de volgende keer dat ze zich aanmelden te registreren. Als uw organisatie niet-browser-apps gebruikt die geen ondersteuning bieden voor moderne verificatie, moeten ze ook app-wacht woorden maken. Raadpleeg voor meer informatie de [Azure multi-factor Authentication-eind gebruiker handleiding](../user-help/multi-factor-authentication-end-user.md) om hen te helpen aan de slag te gaan.

## <a name="change-state-using-powershell"></a>Status wijzigen met behulp van Power shell

Als u de gebruikers status wilt wijzigen met behulp van [Azure AD Power shell](/powershell/azure/), wijzigt u de `$st.State` para meter voor een gebruikers account. Er zijn drie mogelijke statussen voor een gebruikers account:

* *Ingeschakeld*
* *Afgedwongen*
* *Uitgeschakeld*  

Verplaats gebruikers niet rechtstreeks naar de status *afgedwongen* . Als u dit doet, werken niet-browser-apps niet meer omdat de gebruiker geen Azure-Multi-Factor Authentication registratie heeft door lopen en een [app-wacht woord](howto-mfa-app-passwords.md)heeft verkregen.

Als u aan de slag wilt gaan, installeert u de *MSOnline* -module met behulp van [install-module](/powershell/module/powershellget/install-module) als volgt:

```PowerShell
Install-Module MSOnline
```

Maak vervolgens verbinding via [Connect-MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

In het volgende voor beeld Power shell-script wordt MFA ingeschakeld voor een individuele gebruiker met de naam *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Het gebruik van Power shell is een goede optie wanneer u gebruikers bulksgewijs wilt kunnen inschakelen. Het volgende script herhaalt een lijst met gebruikers en schakelt MFA in voor hun accounts. Definieer de gebruikers accounts die op de eerste regel `$users` als volgt zijn ingesteld:

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

Als u MFA wilt uitschakelen, wordt in het volgende voor beeld een gebruiker met [Get-MsolUser](/powershell/module/msonline/get-msoluser)en vervolgens de *StrongAuthenticationRequirements* die voor de gedefinieerde gebruiker zijn ingesteld, verwijderd met [set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

U kunt MFA ook rechtstreeks uitschakelen voor een gebruiker met [set-MsolUser](/powershell/module/msonline/set-msoluser) als volgt:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gebruikers converteren van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang

De volgende Power shell kan u helpen bij het maken van de conversie naar Azure Multi-Factor Authentication op basis van voorwaardelijke toegang.

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
> Als MFA opnieuw wordt ingeschakeld voor een gebruikers object dat al registratie details, zoals een telefoon of e-mail adres, heeft, moeten beheerders die gebruiker MFA opnieuw registreren via Azure Portal of Power shell. Als de gebruiker zich niet opnieuw registreert, wordt de MFA-status niet overgezet van *ingeschakeld* om te worden *afgedwongen* in de gebruikers interface voor MFA-beheer.

## <a name="next-steps"></a>Volgende stappen

Zie [instellingen van Azure-multi-factor Authentication configureren](howto-mfa-mfasettings.md)als u Azure multi-factor Authentication-instellingen wilt configureren.

Zie [Manage User Settings with azure multi-factor Authentication](howto-mfa-userdevicesettings.md)om gebruikers instellingen voor Azure multi-factor Authentication te beheren.

Zie [Azure multi-factor Authentication-rapporten](howto-mfa-reporting.md)als u wilt weten waarom een gebruiker is gevraagd of niet wordt gevraagd MFA uit te voeren.
