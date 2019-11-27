---
title: Multi-Factor Authentication per gebruiker-Azure Active Directory
description: Schakel MFA in door de gebruikers status te wijzigen in azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 258675a343387eb6930cd3511bf885bf510050c6
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404216"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Verificatie in twee stappen vereisen voor een gebruiker

U kunt een van twee benaderingen nemen voor het vereisen van verificatie in twee stappen, beide waarvoor een algemeen beheerders account nodig is. De eerste mogelijkheid is het inschakelen van MFA voor een gebruiker. Wanneer gebruikers afzonderlijk worden ingeschakeld, voeren ze een verificatie in twee stappen uit telkens wanneer ze zich aanmelden (met enkele uitzonde ringen, bijvoorbeeld wanneer ze zich aanmelden bij vertrouwde IP-adressen of wanneer de functie _onthouden apparaten_ is ingeschakeld). De tweede optie is om een beleid voor voorwaardelijke toegang in te stellen waarvoor een verificatie in twee stappen onder bepaalde voor waarden vereist is.

> [!TIP]
> Het inschakelen van Azure Multi-Factor Authentication met beleids regels voor voorwaardelijke toegang is de aanbevolen methode. Het wijzigen van de gebruikers status wordt niet meer aanbevolen, tenzij uw licenties de voorwaardelijke toegang niet bevatten, omdat gebruikers elke keer dat ze zich aanmelden verplichten om MFA uit te voeren.

## <a name="choose-how-to-enable"></a>Kiezen hoe u kunt inschakelen

**Ingeschakeld door het wijzigen van de gebruikers status** : dit is de traditionele methode voor het vereisen van verificatie in twee stappen en wordt beschreven in dit artikel. Het werkt met Azure MFA in de Cloud en Azure MFA-server. Wanneer u deze methode gebruikt, moeten gebruikers **elke keer** dat ze zich aanmelden een verificatie in twee stappen uitvoeren en het beleid voor voorwaardelijke toegang negeren.

Ingeschakeld door het beleid voor voorwaardelijke toegang: dit is de meest flexibele manier om verificatie in twee stappen voor uw gebruikers in te scha kelen. Het gebruik van beleid voor voorwaardelijke toegang kan alleen worden ingeschakeld voor Azure MFA in de Cloud en is een Premium-functie van Azure AD. Meer informatie over deze methode vindt u in [Azure multi-factor Authentication op basis van de Cloud implementeren](howto-mfa-getstarted.md).

Ingeschakeld door Azure AD Identity Protection: deze methode maakt gebruik van het Azure AD Identity Protection risico beleid om verificatie in twee stappen alleen op basis van de aanmeldings Risico's voor alle Cloud toepassingen te vereisen. Voor deze methode is Azure Active Directory P2-licentie vereist. Meer informatie over deze methode vindt u in [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Meer informatie over licenties en prijzen kunt u vinden op de pagina met [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) -en [multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) prijzen.

## <a name="enable-azure-mfa-by-changing-user-state"></a>Azure MFA inschakelen door de gebruikers status te wijzigen

Gebruikers accounts in azure Multi-Factor Authentication de volgende drie statussen hebben:

| Status | Beschrijving | Betrokken niet-browser-apps | Browser-apps die worden beïnvloed | Betrokken moderne verificatie |
|:---:|:---:|:---:|:--:|:--:|
| Uitgeschakeld |De standaard status voor een nieuwe gebruiker die niet is inge schreven bij Azure MFA. |Nee |Nee |Nee |
| Ingeschakeld |De gebruiker is inge schreven bij Azure MFA, maar is niet geregistreerd. Er wordt een prompt weer gegeven om de volgende keer dat ze zich aanmelden te registreren. |Nee.  Ze blijven werken totdat het registratie proces is voltooid. | Ja. Nadat de sessie is verlopen, is de Azure MFA-registratie vereist.| Ja. Nadat het toegangs token is verlopen, is de Azure MFA-registratie vereist. |
| Afgedwongen |De gebruiker is inge schreven en heeft het registratie proces voor Azure MFA voltooid. |Ja. Apps vereisen app-wacht woorden. |Ja. Azure MFA is vereist bij de aanmelding. | Ja. Azure MFA is vereist bij de aanmelding. |

De status van een gebruiker geeft aan of een beheerder deze heeft inge schreven in azure MFA en of het registratie proces is voltooid.

Alle gebruikers worden *uitgeschakeld*. Wanneer u gebruikers inschrijft in azure MFA, verandert de status in *ingeschakeld*. Wanneer ingeschakelde gebruikers zich aanmelden en het registratie proces volt ooien, wordt de status ervan gewijzigd in *afgedwongen*.  

### <a name="view-the-status-for-a-user"></a>De status van een gebruiker weer geven

Gebruik de volgende stappen om de pagina te openen waar u de status van een gebruiker kunt bekijken en beheren:

1. Meld u als beheerder aan bij [Azure Portal](https://portal.azure.com).
2. Zoek en selecteer *Azure Active Directory*. Selecteer **gebruikers** > **alle gebruikers**.
3. Selecteer **multi-factor Authentication**.
   ![Multi-Factor Authentication selecteren](./media/howto-mfa-userstates/selectmfa.png)
4. Er wordt een nieuwe pagina geopend waarin de gebruikers statussen worden weer gegeven.
   ![gebruikers status van multi-factor Authentication-scherm afbeelding](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>De status voor een gebruiker wijzigen

1. Gebruik de voor gaande stappen om naar de pagina Azure Multi-Factor Authentication- **gebruikers** te gaan.
2. Zoek de gebruiker die u wilt inschakelen voor Azure MFA. Mogelijk moet u de weer gave bovenaan wijzigen.
   ![Selecteer de gebruiker waarvan u de status wilt wijzigen van het tabblad gebruikers](./media/howto-mfa-userstates/enable1.png)
3. Schakel het selectievakje naast de naam van de gebruiker in.
4. Klik aan de rechter kant onder **snelle stappen**op **inschakelen** of **uitschakelen**.
   ![geselecteerde gebruiker inschakelen door op inschakelen te klikken in het menu snelle stappen](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Ingeschakelde* gebruikers worden automatisch overgeschakeld naar *afgedwongen* wanneer ze zich registreren voor Azure MFA. Wijzig de gebruikers status niet hand matig in *afdwinging*.

5. Bevestig uw selectie in het pop-upvenster dat wordt geopend.

Nadat u gebruikers hebt ingeschakeld, kunt u gebruikers informeren via e-mail Laat ze weten dat ze gevraagd worden om zich te registreren de volgende keer dat ze zich aanmelden. Als uw organisatie gebruikmaakt van niet-browser-apps die moderne verificatie niet ondersteunen, moeten zij ook app-wachtwoorden maken. U kunt ook een koppeling naar de [Azure MFA-hand leiding voor eind gebruikers](../user-help/multi-factor-authentication-end-user.md) toevoegen om hen te helpen aan de slag te gaan.

### <a name="use-powershell"></a>PowerShell gebruiken

Als u de gebruikers status wilt wijzigen met behulp van [Azure AD Power shell](/powershell/azure/overview), wijzigt u `$st.State`. Er zijn drie mogelijke statussen:

* Ingeschakeld
* Afgedwongen
* Uitgeschakeld  

Verplaats gebruikers niet rechtstreeks naar de status *afgedwongen* . Als u dat wel doet, werken niet-browser-apps niet meer omdat de gebruiker de Azure MFA-registratie niet heeft door lopen en een [app-wacht woord](howto-mfa-mfasettings.md#app-passwords)heeft verkregen.

Installeer de module eerst met behulp van:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Vergeet niet eerst verbinding te maken met **Connect-MsolService**

In dit voor beeld Power shell-script wordt MFA ingeschakeld voor een afzonderlijke gebruiker:

   ```PowerShell
   Import-Module MSOnline
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

Het gebruik van Power shell is een goede optie wanneer u gebruikers bulksgewijs wilt kunnen inschakelen. Als voor beeld wordt met het volgende script een lijst met gebruikers door lopen en wordt MFA op hun accounts ingeschakeld:

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
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationMethods @()
   ```

Dit kan ook worden inge kort tot:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gebruikers converteren van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang

De volgende Power shell kan u helpen bij het maken van de conversie naar Azure Multi-Factor Authentication op basis van voorwaardelijke toegang.

Voer deze Power shell uit in een ISE-venster of sla het bestand op als een. Het PS1-bestand dat lokaal moet worden uitgevoerd.

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
> Het gedrag en het Power shell-script hierboven zijn onlangs gewijzigd. Voorheen werd het script bespaard op de MFA-methoden, uitgeschakeld MFA en de methoden hersteld. Dit is nu niet langer nodig om de methoden niet te wissen met het standaard gedrag voor uitschakelen.

## <a name="next-steps"></a>Volgende stappen

* Waarom wordt een gebruiker gevraagd of niet gevraagd MFA uit te voeren? Zie de sectie [Azure AD-aanmeld rapport in de rapporten in Azure multi-factor Authentication document](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Zie het artikel [Azure multi-factor Authentication-instellingen configureren](howto-mfa-mfasettings.md) voor meer informatie over het configureren van aanvullende instellingen, zoals vertrouwde IP-adressen, aangepaste spraak berichten en fraude waarschuwingen.
* Informatie over het beheren van gebruikers instellingen voor Azure Multi-Factor Authentication vindt u in het artikel [gebruikers instellingen beheren met Azure multi-factor Authentication in de Cloud](howto-mfa-userdevicesettings.md)
