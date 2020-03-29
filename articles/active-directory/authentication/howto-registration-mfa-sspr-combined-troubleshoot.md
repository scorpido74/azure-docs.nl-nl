---
title: Problemen met gecombineerde registratie oplossen - Azure Active Directory
description: Problemen met Azure AD Multi-Factor Authentication en selfservice wachtwoord reset gecombineerde registratie oplossen (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab7c38d23cb1f05e07488810640aeb791ded3d4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847385"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Problemen met gecombineerde beveiligingsgegevensregistratie oplossen (voorbeeld)

De informatie in dit artikel is bedoeld om beheerders te begeleiden die problemen oplossen die door gebruikers van de gecombineerde registratie-ervaring zijn gemeld.

|     |
| --- |
| Gecombineerde beveiligingsgegevensregistratie voor Azure Multi-Factor Authentication en Azure Active Directory (Azure AD) selfservice wachtwoord reset is een openbare preview-functie van Azure AD. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over voorvertoningen.|
|     |

## <a name="audit-logs"></a>Auditlogboeken

De gebeurtenissen die zijn aangemeld voor gecombineerde registratie, bevinden zich in de categorie Verificatiemethoden in de Azure AD-controlelogboeken.

![Azure AD-controlelogboekeninterface met registratiegebeurtenissen](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

In de volgende tabel worden alle auditgebeurtenissen vermeld die door gecombineerde registratie worden gegenereerd:

| Activiteit | Status | Reden | Beschrijving |
| --- | --- | --- | --- |
| Gebruiker heeft alle vereiste beveiligingsgegevens geregistreerd | Geslaagd | De gebruiker registreerde alle vereiste beveiligingsgegevens. | Deze gebeurtenis treedt op wanneer een gebruiker de registratie heeft voltooid.|
| Gebruiker heeft alle vereiste beveiligingsgegevens geregistreerd | Fout | Gebruiker heeft de registratie van beveiligingsgegevens geannuleerd. | Deze gebeurtenis vindt plaats wanneer een gebruiker de registratie annuleert vanuit de interrupt-modus.|
| Door de gebruiker geregistreerde beveiligingsgegevens | Geslaagd | Door de gebruiker geregistreerde *methode*. | Deze gebeurtenis treedt op wanneer een gebruiker een afzonderlijke methode registreert. *Methode* kan authenticator app, telefoon, e-mail, beveiligingsvragen, app-wachtwoord, alternatieve telefoon, enzovoort.| 
| Door de gebruiker beoordeelde beveiligingsgegevens | Geslaagd | De gebruiker heeft beveiligingsgegevens met succes gecontroleerd. | Deze gebeurtenis vindt plaats wanneer een gebruiker **Er goed uitziet** op de pagina beveiligingsgegevens.|
| Door de gebruiker beoordeelde beveiligingsgegevens | Fout | De gebruiker kan de beveiligingsgegevens niet controleren. | Deze gebeurtenis vindt plaats wanneer een gebruiker **Er goed uitziet** op de pagina beveiligingsgegevens, maar er mislukt iets op de backend.|
| Door de gebruiker verwijderde beveiligingsgegevens | Geslaagd | Verwijderde *methode*van gebruiker . | Deze gebeurtenis treedt op wanneer een gebruiker een afzonderlijke methode verwijdert. *Methode* kan authenticator app, telefoon, e-mail, beveiligingsvragen, app-wachtwoord, alternatieve telefoon, enzovoort.|
| Door de gebruiker verwijderde beveiligingsgegevens | Fout | De gebruiker kan *de methode*niet verwijderen . | Deze gebeurtenis vindt plaats wanneer een gebruiker een methode probeert te verwijderen, maar de poging mislukt om een of andere reden. *Methode* kan authenticator app, telefoon, e-mail, beveiligingsvragen, app-wachtwoord, alternatieve telefoon, enzovoort.|
| Standaardbeveiligingsgegevens van de gebruiker gewijzigd | Geslaagd | De gebruiker heeft de standaardbeveiligingsgegevens voor *methode*gewijzigd. | Deze gebeurtenis treedt op wanneer een gebruiker de standaardmethode wijzigt. *Methode* kan authenticator app melding, een code van mijn authenticator app of token, Call +X XXXXXXXXXX, Tekst een code naar +X XXXXXXXXX, en ga zo maar door.|
| Standaardbeveiligingsgegevens van de gebruiker gewijzigd | Fout | De gebruiker kan de standaardbeveiligingsgegevens voor *methode*niet wijzigen. | Deze gebeurtenis treedt op wanneer een gebruiker probeert de standaardmethode te wijzigen, maar de poging mislukt om een of andere reden. *Methode* kan authenticator app melding, een code van mijn authenticator app of token, Call +X XXXXXXXXXX, Tekst een code naar +X XXXXXXXXX, en ga zo maar door.|

## <a name="troubleshooting-interrupt-mode"></a>Interrupt-modus oplossen

| Symptoom | Stappen voor probleemoplossing |
| --- | --- |
| Ik zie niet de methoden die ik verwacht te zien. | 1. Controleer of de gebruiker een Azure AD-beheerrol heeft. Zo ja, bekijk de verschillen in het SSPR-beheerbeleid. <br> 2. Bepaal of de gebruiker wordt onderbroken vanwege de handhaving van de registratie van multi-factorauthenticatie of handhaving van de SSPR-registratie. Zie het [stroomdiagram](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) onder "Gecombineerde registratiemodi" om te bepalen welke methoden moeten worden weergegeven. <br> 3. Bepaal hoe recent het Multi-Factor Authentication- of SSPR-beleid is gewijzigd. Als de wijziging recent is, kan het enige tijd duren voordat het bijgewerkte beleid wordt doorgegeven.|

## <a name="troubleshooting-manage-mode"></a>Modus Probleemoplossing beheren

| Symptoom | Stappen voor probleemoplossing |
| --- | --- |
| Ik heb niet de mogelijkheid om een bepaalde methode toe te voegen. | 1. Bepaal of de methode is ingeschakeld voor multifactorauthenticatie of voor SSPR. <br> 2. Als de methode is ingeschakeld, slaat u het beleid opnieuw op en wacht u 1-2 uur voordat u opnieuw test. <br> 3. Als de methode is ingeschakeld, moet u ervoor zorgen dat de gebruiker het maximumaantal van die methode dat hij mag instellen, nog niet heeft ingesteld.|

## <a name="disable-combined-registration"></a>Gecombineerde registratie uitschakelen

Wanneer een gebruiker een telefoonnummer en/of mobiele app registreert in de nieuwe gecombineerde ervaring, stempelt onze service een set vlaggen (StrongAuthenticationMethods) voor die methoden op die gebruiker. Met deze functionaliteit kan de gebruiker multifactorauthenticatie met deze methoden uitvoeren wanneer multi-factorauthenticatie vereist is.

Als een beheerder de preview inschakelt, registreren gebruikers zich via de nieuwe ervaring en schakelt de beheerder de preview uit, gebruikers kunnen ook onbewust worden geregistreerd voor Multi-Factor Authentication.

Als een gebruiker die de gecombineerde registratie heeft voltooid, naar de huidige [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)zelfbedieningspagina voor het opnieuw instellen van wachtwoorden (SSPR) gaat, wordt de gebruiker gevraagd om multifactorverificatie uit te voeren voordat hij toegang heeft tot die pagina. Deze stap wordt verwacht vanuit een technisch oogpunt, maar het is nieuw voor gebruikers die eerder werden geregistreerd voor SSPR alleen. Hoewel deze extra stap de beveiligingshouding van de gebruiker verbetert door een ander beveiligingsniveau te bieden, willen beheerders hun gebruikers misschien terugdraaien, zodat ze geen multifactorauthenticatie meer kunnen uitvoeren.  

### <a name="how-to-roll-back-users"></a>Gebruikers terugdraaien

Als u als beheerder de multifactorauthenticatie-instellingen van een gebruiker opnieuw wilt instellen, u het PowerShell-script in de volgende sectie gebruiken. Het script zal de eigenschap StrongAuthenticationMethods wissen voor de mobiele app en/of het telefoonnummer van een gebruiker. Als u dit script voor uw gebruikers uitvoert, moeten ze zich opnieuw registreren voor multi-factorauthenticatie als ze het nodig hebben. We raden u aan terugdraaien te testen met een of twee gebruikers voordat alle getroffen gebruikers worden teruggedraaid.

Met de volgende stappen u een gebruiker of groep gebruikers terugdraaien.

#### <a name="prerequisites"></a>Vereisten

1. Installeer de juiste Azure AD PowerShell-modules. Voer in een PowerShell-venster de volgende opdrachten uit om de modules te installeren:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Sla de lijst met aangedane gebruikersobject-id's op uw computer op als een tekstbestand met één ID per regel. Noteer de locatie van het bestand.
1. Sla het volgende script op uw computer op en noteer de locatie van het script:

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>Terugdraaien

Voer in een PowerShell-venster de volgende opdracht uit, waarbij de locatie van het script en het gebruikersbestand wordt weergegeven. Voer globale beheerdersreferenties in wanneer daarom wordt gevraagd. Het script zal de uitkomst van elke bewerking van de gebruikersupdate uitvoeren.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>De voorbeeldervaring uitschakelen

Voer de volgende stappen uit om de voorbeeldervaring voor uw gebruikers uit te schakelen:

1. Meld u aan bij de Azure-portal als gebruikersbeheerder.
2. Ga naar Azure Active > **Directory-gebruikersinstellingen** > **Beheer instellingen voor previewfuncties voor toegangsdeelvensters**. **Azure Active Directory**
3. Onder **Gebruikers kunnen voorbeeldfuncties gebruiken voor het registreren en beheren van beveiligingsgegevens,** de kiezer instellen op **Geen**en vervolgens **Opslaan selecteren**.

Gebruikers wordt niet langer gevraagd zich te registreren met behulp van de preview-ervaring.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de openbare preview van gecombineerde registratie voor selfservicewachtwoordreset en Azure Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
