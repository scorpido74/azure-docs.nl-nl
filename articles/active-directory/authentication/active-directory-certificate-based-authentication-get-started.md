---
title: Verificatie op basis van certificaten - Azure Active Directory
description: Meer informatie over het configureren van verificatie op basis van certificaten in uw omgeving
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b57c4f474b0b9def08005f32f48225d36ea8cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848830"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Aan de slag met verificatie op basis van certificaten in Azure Active Directory

Met verificatie op basis van certificaten u worden geverifieerd door Azure Active Directory met een clientcertificaat op een Windows-, Android- of iOS-apparaat wanneer u uw Exchange online-account verbindt met:

- Mobiele toepassingen van Microsoft, zoals Microsoft Outlook en Microsoft Word
- Exchange ActiveSync (EAS) clients

Als u deze functie configureert, hoeft u geen combinatie van gebruikersnaam en wachtwoord in bepaalde e-mail- en Microsoft Office-toepassingen op uw mobiele apparaat in te voeren.

Dit onderwerp:

- Biedt u de stappen voor het configureren en gebruiken van verificatie op basis van certificaten voor gebruikers van tenants in office 365-abonnementen voor Bedrijven, Onderwijs en de Amerikaanse overheid. Deze functie is beschikbaar in preview in Office 365 China, Us Government Defense en Federal Plans van de Amerikaanse regering.
- Gaat ervan uit dat u al een [openbare sleutelinfrastructuur (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) en [AD FS](../hybrid/how-to-connect-fed-whatis.md) hebt geconfigureerd.

## <a name="requirements"></a>Vereisten

Als u verificatie op basis van certificaten wilt configureren, moeten de volgende instructies waar zijn:

- Certificaatgebaseerde verificatie (CBA) wordt alleen ondersteund voor Federated-omgevingen voor browsertoepassingen, native clients met behulp van moderne verificatie (ADAL) of MSAL-bibliotheken. De enige uitzondering is Exchange Active Sync (EAS) voor Exchange Online (EXO), die kan worden gebruikt voor federatieve en beheerde accounts.
- De basiscertificaatautoriteit en de tussentijdse certificaatautoriteiten moeten zijn geconfigureerd in Azure Active Directory.
- Elke certificaatautoriteit moet beschikken over een certificaatintrekkingslijst (CRL) waarnaar kan worden verwezen via een url die naar internet is gericht.
- U moet ten minste één certificaatautoriteit hebben geconfigureerd in Azure Active Directory. U gerelateerde stappen vinden in de sectie [Certificaatautoriteiten configureren.](#step-2-configure-the-certificate-authorities)
- Voor Exchange ActiveSync-clients moet het clientcertificaat het routeerbare e-mailadres van de gebruiker in Exchange online hebben in de hoofdnaam of de naamwaarde RFC822 van het veld Alternatieve naam onderwerp. Azure Active Directory brengt de RFC822-waarde in de map in.
- Uw clientapparaat moet toegang hebben tot ten minste één certificaatinstantie die clientcertificaten uitgeeft.
- Er moet een clientcertificaat voor clientverificatie zijn afgegeven aan uw client.

>[!IMPORTANT]
>De maximale grootte van een CRL voor Azure Active Directory om de download en cache met succes te downloaden en in de cache te downloaden, is 20 MB en de tijd die nodig is om de CRL te downloaden mag niet langer zijn dan 10 seconden.  Als Azure Active Directory geen CRL kan downloaden, mislukken verificaties op basis van certificaten met certificaten die zijn uitgegeven door de overeenkomstige CA. Aanbevolen procedures om ervoor te zorgen dat CRL-bestanden binnen de grootte beperkingen zijn, zijn om de levensduur van certificaten binnen redelijke grenzen te houden en verlopen certificaten op te schonen. 

## <a name="step-1-select-your-device-platform"></a>Stap 1: Selecteer uw apparaatplatform

Als eerste stap moet u voor het apparaatplatform waar u om geeft het volgende bekijken:

- Ondersteuning voor mobiele office-toepassingen
- De specifieke uitvoeringsvereisten

De gerelateerde informatie bestaat voor de volgende apparaatplatforms:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Stap 2: Certificaatautoriteiten configureren

Upload het volgende als u uw certificaatautoriteiten wilt configureren in Azure Active Directory voor elke certificaatautoriteit:

* Het openbare gedeelte van het certificaat, in *.cer-indeling*
* De naar internet gerichte URL's waar de certificaatintrekkingslijsten (CRL's) zich bevinden

Het schema voor een certificaatautoriteit ziet er als volgt uit:

    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }

Voor de configuratie u de [Azure Active Directory PowerShell-versie 2](/powershell/azure/install-adv2?view=azureadps-2.0)gebruiken:

1. Start Windows PowerShell met beheerdersbevoegdheden.
2. Installeer de Azure AD-module versie [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) of hoger.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Als eerste configuratiestap moet u een verbinding met uw tenant tot stand brengen. Zodra er een verbinding met uw tenant bestaat, u de vertrouwde certificaatautoriteiten controleren, toevoegen, verwijderen en wijzigen die in uw directory zijn gedefinieerd.

### <a name="connect"></a>Verbinding maken

Als u een verbinding met uw tenant wilt maken, gebruikt u de cmdlet [Connect-AzureAD:](/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

    Connect-AzureAD

### <a name="retrieve"></a>Ophalen

Als u de vertrouwde certificaatautoriteiten wilt ophalen die in uw map zijn gedefinieerd, gebruikt u de cmdlet [Get-AzureADTrustedCertificateAuthority.](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0)

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Toevoegen

Als u een vertrouwde certificaatautoriteit wilt maken, gebruikt u de cmdlet [Nieuw-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) en stelt u het **crlDistributionPoint-kenmerk** in op een juiste waarde:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Verwijderen

Als u een vertrouwde certificaatautoriteit wilt verwijderen, gebruikt u de cmdlet [Remove-AzureADTrustedCertificateAuthority:](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0)

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Wijzigen

Als u een vertrouwde certificaatautoriteit wilt wijzigen, gebruikt u de cmdlet [Set-AzureADTrustedCertificateAuthority:](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0)

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>Stap 3: Intrekking configureren

Als u een clientcertificaat wilt intrekken, haalt Azure Active Directory de certificaatintrekkingslijst (CRL) op uit de URL's die zijn geüpload als onderdeel van de gegevens van de certificaatautoriteit en deze caches. De laatste publicatietijdstempel **(eigenschap Effectieve datum)** in de CRL wordt gebruikt om ervoor te zorgen dat de CRL nog steeds geldig is. Er wordt periodiek naar verwezen om de toegang tot certificaten die deel uitmaken van de lijst in te trekken.

Als een meer onmiddellijke intrekking vereist is (bijvoorbeeld als een gebruiker een apparaat verliest), kan het autorisatietoken van de gebruiker ongeldig worden gemaakt. Als u het autorisatietoken wilt ongeldig maken, stelt u het veld **StsRefreshTokenValidFrom** in voor deze specifieke gebruiker met Windows PowerShell. U moet het veld **StsRefreshTokenValidFrom** bijwerken voor elke gebruiker waarvoor u de toegang wilt intrekken.

Om ervoor te zorgen dat de intrekking blijft bestaan, moet u de **ingangsdatum** van de CRL instellen op een datum na de waarde die is ingesteld door **StsRefreshTokenValidFrom** en ervoor zorgen dat het certificaat in kwestie in de CRL staat.

In de volgende stappen wordt het proces beschreven voor het bijwerken en ongeldig maken van het autorisatietoken door het veld **StsRefreshTokenValidFrom in te** stellen.

**Intrekking configureren:**

1. Maak verbinding met beheerdersreferenties met de MSOL-service:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Haal de huidige StsRefreshTokensValidFrom-waarde voor een gebruiker op:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Configureer een nieuwe StsRefreshTokensValidFrom-waarde voor de gebruiker die gelijk is aan de huidige tijdstempel:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

De datum die u instelt, moet in de toekomst zijn. Als de datum niet in de toekomst is, is de eigenschap **StsRefreshTokensValidFrom** niet ingesteld. Als de datum in de toekomst is, is **StsRefreshTokensValidFrom** ingesteld op de huidige tijd (niet de datum die wordt aangegeven door de opdracht Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Stap 4: Uw configuratie testen

### <a name="testing-your-certificate"></a>Uw certificaat testen

Als eerste configuratietest moet u proberen u aan te melden bij [Outlook Web Access](https://outlook.office365.com) of [SharePoint Online](https://microsoft.sharepoint.com) met uw browser op het **apparaat.**

Als uw aanmelding succesvol is, weet u dat:

- Het gebruikerscertificaat is ingericht op uw testapparaat
- AD FS is correct geconfigureerd

### <a name="testing-office-mobile-applications"></a>Mobiele Office-toepassingen testen

**Ga als het gaat om verificatie op basis van certificaten op uw mobiele Office-toepassing:**

1. Installeer op uw testapparaat een mobiele Office-toepassing (bijvoorbeeld OneDrive).
3. Start de toepassing.
4. Voer uw gebruikersnaam in en selecteer vervolgens het gebruikerscertificaat dat u wilt gebruiken.

Je moet ingelogd zijn om voor deze deel te zijn ingelogd.

### <a name="testing-exchange-activesync-client-applications"></a>Exchange ActiveSync-clienttoepassingen testen

Om toegang te krijgen tot Exchange ActiveSync (EAS) via verificatie op basis van certificaten, moet een EAS-profiel met het clientcertificaat beschikbaar zijn voor de toepassing.

Het EAS-profiel moet de volgende gegevens bevatten:

- Het gebruikerscertificaat dat moet worden gebruikt voor verificatie

- Het EAS-eindpunt (bijvoorbeeld outlook.office365.com)

Een EAS-profiel kan worden geconfigureerd en op het apparaat worden geplaatst door het gebruik van Mobile device management (MDM), zoals Intune of door het certificaat handmatig in het EAS-profiel op het apparaat te plaatsen.

### <a name="testing-eas-client-applications-on-android"></a>EAS-clienttoepassingen testen op Android

**Certificaatverificatie testen:**

1. Configureer een EAS-profiel in de toepassing dat voldoet aan de vereisten in de vorige sectie.
2. Open de toepassing en controleer of de e-mail wordt gesynchroniseerd.

## <a name="next-steps"></a>Volgende stappen

[Aanvullende informatie over verificatie op basis van certificaten op Android-apparaten.](active-directory-certificate-based-authentication-android.md)

[Aanvullende informatie over verificatie op basis van certificaten op iOS-apparaten.](active-directory-certificate-based-authentication-ios.md)
