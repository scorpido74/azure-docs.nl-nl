---
title: Uitnodigingen toestaan of blokkeren voor specifieke organisaties - Azure AD
description: Laat zien hoe een beheerder de Azure-portal of PowerShell kan gebruiken om een lijst met toegang of weigering in te stellen om B2B-gebruikers van bepaalde domeinen toe te staan of te blokkeren.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/15/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5100c4406cfd4a8395dfa177dc3cd5e911decb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273422"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Uitnodigingen voor B2B-gebruikers van bepaalde organisaties toestaan of blokkeren

U een lijst met toestaan of een lijst met weigeren gebruiken om uitnodigingen voor B2B-gebruikers van specifieke organisaties toe te staan of te blokkeren. Als u bijvoorbeeld persoonlijke e-mailadresdomeinen wilt blokkeren, u een weigeringslijst instellen met domeinen als Gmail.com en Outlook.com. Of als uw bedrijf een partnerschap heeft met andere bedrijven, zoals Contoso.com, Fabrikam.com en Litware.com, en u uitnodigingen wilt beperken tot alleen deze organisaties, u Contoso.com, Fabrikam.com en Litware.com toevoegen aan uw lijst met toegestane personen.
  
## <a name="important-considerations"></a>Belangrijke overwegingen

- U een lijst met toestaan of een lijst met weigeren maken. U niet beide typen lijsten instellen. Standaard staan alle domeinen niet in de lijst met toegestane domeinen op de lijst met weigeringen en vice versa. 
- U slechts één beleid per organisatie maken. U het beleid bijwerken om meer domeinen op te nemen, of u het beleid verwijderen om een nieuw beleid te maken. 
- Het aantal domeinen dat u toevoegen aan een lijst met toegestane of weigeringslijst wordt alleen beperkt door de grootte van het beleid. De maximale grootte van het hele beleid is 25 KB (25.000 tekens), inclusief de lijst met toegestane of weigeringslijst en alle andere parameters die zijn geconfigureerd voor andere functies.
- Deze lijst werkt onafhankelijk van OneDrive voor Bedrijven en SharePoint Online-allow/block-lijsten. Als u het delen van afzonderlijke bestanden in SharePoint Online wilt beperken, moet u een lijst voor toestaan of weigeren instellen voor OneDrive voor Bedrijven en SharePoint Online. Zie [Beperkt domeinen delen in SharePoint Online en OneDrive voor Bedrijven voor](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9)meer informatie.
- De lijst is niet van toepassing op externe gebruikers die de uitnodiging al hebben ingewisseld. De lijst wordt afgedwongen nadat de lijst is ingesteld. Als een uitnodiging voor een gebruiker in behandeling is en u een beleid instelt dat zijn domein blokkeert, mislukt de poging van de gebruiker om de uitnodiging in te wisselen.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Het lijstbeleid toestaan of weigeren instellen in de portal

Standaard is de **instelling Uitnodigingen toestaan voor een domein (meest inclusief)** ingeschakeld. In dit geval u B2B-gebruikers van elke organisatie uitnodigen.

### <a name="add-a-deny-list"></a>Een lijst met weigeren toevoegen

Dit is het meest typische scenario, waarbij uw organisatie met bijna elke organisatie wil werken, maar wil voorkomen dat gebruikers van specifieke domeinen worden uitgenodigd als B2B-gebruikers.

Een lijst met weigeren toevoegen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **gebruikersinstellingen van Azure Active Directory** > **Users** > **User settings**.
3. Selecteer **onder Externe gebruikers**de optie Instellingen voor externe samenwerking **beheren**.
4. Selecteer **Uitnodigingen voor de opgegeven domeinen weigeren**onder **samenwerkingsbeperkingen**.
5. Voer **onder DOELDOMEINEN**de naam in van een van de domeinen die u wilt blokkeren. Voer voor meerdere domeinen elk domein in op een nieuwe regel. Bijvoorbeeld:

   ![Toont de optie weigeren met toegevoegde domeinen](./media/allow-deny-list/DenyListSettings.png)
 
6. Klik op **Opslaan**als u klaar bent.

Nadat u het beleid hebt ingesteld, ontvangt u een bericht dat het domein van de gebruiker momenteel wordt geblokkeerd door uw uitnodigingsbeleid als u een gebruiker uit een geblokkeerd domein probeert uit te nodigen.
 
### <a name="add-an-allow-list"></a>Een lijst met toegestane toevoegen

Dit is een meer beperkende configuratie, waarbij u specifieke domeinen in de lijst toestaan instellen en uitnodigingen beperken tot andere organisaties of domeinen die niet worden genoemd. 

Als u een lijst met toegestane gegevens wilt gebruiken, moet u ervoor zorgen dat u tijd besteedt aan het volledig evalueren van wat uw bedrijfsbehoeften zijn. Als u dit beleid te beperkend maakt, kunnen uw gebruikers ervoor kiezen om documenten via e-mail te verzenden of andere niet-IT-gesanctioneerde manieren van samenwerken te zoeken.


Ga als u een lijst met toegestane plaatsen toevoeg:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **gebruikersinstellingen van Azure Active Directory** > **Users** > **User settings**.
3. Selecteer **onder Externe gebruikers**de optie Instellingen voor externe samenwerking **beheren**.
4. Selecteer **Uitnodigingen alleen toestaan voor de opgegeven domeinen (meest beperkend)** onder **Samenwerkingsbeperkingen.**
5. Voer **onder DOELDOMEINEN**de naam in van een van de domeinen die u wilt toestaan. Voer voor meerdere domeinen elk domein in op een nieuwe regel. Bijvoorbeeld:

   ![Toont de optie Toestaan met toegevoegde domeinen](./media/allow-deny-list/AllowListSettings.png)
 
6. Klik op **Opslaan**als u klaar bent.

Nadat u het beleid hebt ingesteld, als u een gebruiker probeert uit te nodigen uit een domein dat niet op de lijst toestaan staat, ontvangt u een bericht waarin staat dat het domein van de gebruiker momenteel wordt geblokkeerd door uw uitnodigingsbeleid.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Overschakelen van toestaan naar lijst weigeren en vice versa 

Als u van het ene beleid naar het andere overschakelt, wordt de bestaande beleidsconfiguratie verwijderd. Zorg ervoor dat u een back-up maakt van de gegevens van uw configuratie voordat u de switch uitvoert. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Het lijstbeleid voor toestaan of weigeren instellen met PowerShell

### <a name="prerequisite"></a>Vereiste

> [!Note]
> De AzureADPreview-module is geen volledig ondersteunde module zoals in preview. 

Als u de lijst toestaan of weigeren wilt instellen met PowerShell, moet u de voorbeeldversie van de Azure Active Directory Module voor Windows PowerShell installeren. Installeer specifiek de AzureADPreview-moduleversie 2.0.0.98 of hoger.

Om de versie van de module te controleren (en te kijken of deze is geïnstalleerd):
 
1. Open Windows PowerShell als een verhoogde gebruiker (Als administrator uitvoeren). 
2. Voer de volgende opdracht uit om te zien of er versies van de Azure Active Directory Module voor Windows PowerShell op uw computer zijn geïnstalleerd:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Als de module niet is geïnstalleerd of als u geen vereiste versie hebt, voert u een van de volgende handelingen uit:

- Als er geen resultaten worden geretourneerd, voert u de volgende opdracht uit om de nieuwste versie van de AzureADPreview-module te installeren:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Als alleen de AzureAD-module in de resultaten wordt weergegeven, voert u de volgende opdrachten uit om de AzureADPreview-module te installeren: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Als alleen de AzureADPreview-module wordt weergegeven in de resultaten, maar de versie minder dan 2.0.0.98 is, voert u de volgende opdrachten uit om deze bij te werken: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Als zowel de AzureAD- als AzureADPreview-modules in de resultaten worden weergegeven, maar de versie van de AzureADPreview-module minder dan 2.0.0.98 is, voert u de volgende opdrachten uit om deze bij te werken: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>De cmdlets AzureADPolicy gebruiken om het beleid te configureren

Als u een lijst voor toestaan of weigeren wilt maken, gebruikt u de cmdlet [Nieuw-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) In het volgende voorbeeld ziet u hoe u een weigeringslijst instelt die het domein 'live.com' blokkeert.

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Het volgende geeft hetzelfde voorbeeld weer, maar met de beleidsdefinitie inline.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Als u het lijstbeleid voor toestaan of weigeren wilt instellen, gebruikt u de cmdlet [Set-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) Bijvoorbeeld:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Als u het beleid wilt opvragen, gebruikt u de cmdlet [Get-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) Bijvoorbeeld:

```powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Als u het beleid wilt verwijderen, gebruikt u de cmdlet [Remove-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) Bijvoorbeeld:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Volgende stappen

- Zie Wat is Azure AD B2B-samenwerking voor een overzicht van Azure AD [B2B-samenwerking?](what-is-b2b.md)
- Zie [Voorwaardelijke toegang voor B2B-samenwerkingsgebruikers voor](conditional-access.md)informatie over voorwaardelijke toegang en B2B-samenwerking.



