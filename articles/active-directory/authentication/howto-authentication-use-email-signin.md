---
title: Meld u aan met een e-mail adres als een alternatieve aanmeldings-ID voor Azure Active Directory
description: Informatie over het configureren en inschakelen van gebruikers om zich aan te melden bij Azure Active Directory met hun e-mail adres als een alternatieve aanmeldings-ID (preview-versie)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/22/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: scottsta
ms.openlocfilehash: ed317039e683ef36054d5ace612e09ca75dfa11e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837386"
---
# <a name="sign-in-to-azure-using-email-as-an-alternate-login-id-preview"></a>Meld u aan bij Azure via e-mail als een alternatieve aanmeldings-ID (preview-versie)

Veel organisaties willen dat gebruikers zich aanmelden bij Azure met dezelfde referenties als de on-premises Directory-omgeving. Bij deze benadering, ook wel hybride verificatie genoemd, hoeven gebruikers slechts één set referenties te onthouden.

Sommige organisaties zijn niet naar hybride verificatie verplaatst om de volgende redenen:

* De Azure Active Directory (Azure AD) user principal name (UPN) is standaard ingesteld op dezelfde UPN als de on-premises Directory.
* Als u de UPN van Azure AD wijzigt, wordt er een mis-overeenkomst tussen on-premises en Azure-omgevingen gemaakt die problemen met bepaalde toepassingen en services kunnen veroorzaken.
* Als gevolg van bedrijfs-of nalevings redenen wil de organisatie zich niet aanmelden bij Azure met de lokale UPN.

Om u te helpen bij het verplaatsen naar hybride verificatie kunt u Azure AD nu configureren zodat gebruikers zich met een e-mail adres in uw geverifieerde domein als een alternatieve aanmeldings-ID kunnen aanmelden bij Azure. Als *Contoso* bijvoorbeeld is gebrandt op *fabrikam*, in plaats van zich te blijven aanmelden met de verouderde `balas@contoso.com` UPN, kan e-mail als alternatieve aanmeldings-id nu worden gebruikt. Om toegang te krijgen tot een toepassing of services, melden gebruikers zich aan bij Azure met hun toegewezen e-mail adres, zoals `balas@fabrikam.com` .

|     |
| --- |
| Meld u aan bij Azure AD met een e-mail adres als alternatieve aanmeldings-ID. Dit is een open bare preview-functie van Azure Active Directory. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over Previews.|
|     |

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Overzicht van de benaderingen van Azure AD-aanmelding

Upn's (User Principal Names) zijn unieke id's voor een gebruikers account in uw on-premises Directory en in azure AD. Elk gebruikers account in een directory wordt vertegenwoordigd door een UPN, zoals `balas@contoso.com` . Wanneer u een on-premises Active Directory Domain Services-omgeving (AD DS) synchroniseert met Azure AD, wordt de UPN van Azure AD standaard ingesteld op overeenkomen met de lokale UPN.

In veel organisaties is het goed om de on-premises UPN en de UPN van Azure AD te laten overeenkomen. Wanneer gebruikers zich aanmelden bij Azure-toepassingen en-services, gebruiken ze hun Azure AD-UPN. Sommige organisaties kunnen echter geen overeenkomende Upn's gebruiken om zich aan te melden vanwege bedrijfs beleid of problemen met de gebruikers ervaring.

Organisaties die geen overeenkomende Upn's in azure AD kunnen gebruiken, hebben een aantal opties:

* Een manier is om de Azure AD-UPN in te stellen op iets anders op basis van de bedrijfs behoeften, zoals `balas@fabrikam.com` .
    * Niet alle toepassingen en services zijn echter compatibel met het gebruik van een andere waarde voor de on-premises UPN en de UPN van Azure AD.
* Een betere benadering is om ervoor te zorgen dat de Azure AD-en on-premises Upn's zijn ingesteld op dezelfde waarde, en Azure AD zo configureren dat gebruikers zich bij Azure aanmelden met hun e-mail adres als een alternatieve aanmeldings-ID.

Met e-mail als alternatieve aanmeldings-ID kunnen gebruikers zich nog steeds aanmelden bij Azure door hun UPN in te voeren, maar kunnen ze zich ook aanmelden met hun e-mail adres. Ter ondersteuning hiervan definieert u een e-mail adres in het kenmerk *proxyAddresses* van de gebruiker in de on-premises Directory. Dit *proxyAddress attribuut* -kenmerk ondersteunt een of meer e-mail adressen.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>E-mail adressen voor aanmelden synchroniseren met Azure AD

Traditionele verificatie van Active Directory Domain Services (AD DS) of Active Directory Federation Services (AD FS) gebeurt rechtstreeks op uw netwerk en wordt verwerkt door uw AD DS-infra structuur. Met hybride verificatie kunnen gebruikers zich in plaats daarvan rechtstreeks aanmelden bij Azure AD.

Ter ondersteuning van deze hybride verificatie benadering synchroniseert u uw on-premises AD DS omgeving met Azure AD met behulp van [Azure AD Connect][azure-ad-connect] en configureert u deze voor het gebruik van PHS (Password Hash Sync) of Pass Through-verificatie (PTA).

In beide configuratie opties verzendt de gebruiker de gebruikers naam en het wacht woord naar Azure AD, waarmee de referenties worden gevalideerd en een ticket wordt uitgegeven. Wanneer gebruikers zich aanmelden bij Azure AD, wordt de nood zaak van uw organisatie om een AD FS-infra structuur te hosten en te beheren, verwijderd.

![Diagram van hybride Azure AD-identiteit met wachtwoord hash-synchronisatie](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Diagram van hybride Azure AD-identiteit met Pass-Through-verificatie](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Een van de gebruikers kenmerken die automatisch worden gesynchroniseerd door Azure AD Connect is *proxyAddresses*. Als gebruikers een e-mail adres hebben gedefinieerd in de on-premises AD DS omgeving als onderdeel van het kenmerk *proxyAddresses* , wordt het automatisch gesynchroniseerd met Azure AD. Dit e-mail adres kan vervolgens rechtstreeks worden gebruikt in het aanmeldings proces van Azure AD als een alternatieve aanmeldings-ID.

> [!IMPORTANT]
> Alleen e-mails in geverifieerde domeinen voor de Tenant worden gesynchroniseerd met Azure AD. Elke Azure AD-Tenant heeft een of meer geverifieerde domeinen waarvoor u het eigendom hebt bewezen en die uniek zijn gebonden aan uw Tenant.
>
> Zie [een aangepaste domein naam toevoegen en verifiëren in azure AD][verify-domain]voor meer informatie.

Zie [de juiste verificatie methode kiezen voor uw Azure AD hybride identiteits oplossing][hybrid-auth-methods]voor meer informatie.

## <a name="enable-user-sign-in-with-an-email-address"></a>Aanmelding van gebruikers met een e-mail adres inschakelen

Zodra gebruikers met het kenmerk *proxyAddresses* zijn gesynchroniseerd met Azure AD met behulp van Azure AD Connect, moet u de functie inschakelen zodat gebruikers zich kunnen aanmelden met een e-mail adres als een alternatieve aanmeldings-id voor uw Tenant. Deze functie geeft aan dat de aanmeldings servers van Azure AD niet alleen de aanmeldings naam kunnen controleren aan de hand van UPN-waarden, maar ook op basis van *proxyAddresses* -waarden voor het e-mail adres.

Tijdens de preview-periode kunt u op dit moment alleen de optie aanmelden met e-mail inschakelen als een alternatieve aanmeldings-ID-functie met behulp van Power shell. U hebt *Tenant beheerders* machtigingen nodig om de volgende stappen uit te voeren:

1. Open een Power shell-sessie als beheerder en installeer de *AzureADPreview* -module met de cmdlet [install-module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Als u hierom wordt gevraagd, selecteert u **Y** om NuGet te installeren of om te installeren vanuit een niet-vertrouwde opslag plaats.

1. Meld u aan bij uw Azure AD-Tenant als een *Tenant beheerder* met de cmdlet [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    Connect-AzureAD
    ```

    De opdracht retourneert informatie over uw account, omgeving en Tenant-ID.

1. Controleer als volgt of het *HomeRealmDiscoveryPolicy* -beleid al bestaat in uw Tenant met behulp van de cmdlet [Get-AzureADPolicy][Get-AzureADPolicy] :

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. Als er op dit moment geen beleid is geconfigureerd, retourneert de opdracht niets. Als er een beleid wordt geretourneerd, slaat u deze stap over en gaat u verder met de volgende stap om een bestaand beleid bij te werken.

    Als u het *HomeRealmDiscoveryPolicy* -beleid aan de Tenant wilt toevoegen, gebruikt u de cmdlet [New-AzureADPolicy][New-AzureADPolicy] en stelt u het kenmerk *AlternateIdLogin* in op *' enabled ': True* , zoals wordt weer gegeven in het volgende voor beeld:

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Wanneer het beleid is gemaakt, retourneert de opdracht de beleids-ID, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Als er al een beleid is geconfigureerd, controleert u of het kenmerk *AlternateIdLogin*   is ingeschakeld, zoals wordt weer gegeven in het volgende voor beeld van het beleid uitvoer:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Als het beleid bestaat, maar het *AlternateIdLogin* -kenmerk dat niet aanwezig is of is ingeschakeld, of als er andere kenmerken bestaan in het beleid dat u wilt behouden, moet u het bestaande beleid bijwerken met de cmdlet [set-AzureADPolicy][Set-AzureADPolicy] .

    > [!IMPORTANT]
    > Wanneer u het beleid bijwerkt, moet u alle oude instellingen en het nieuwe *AlternateIdLogin* -kenmerk toevoegen.

    In het volgende voor beeld wordt het kenmerk *AlternateIdLogin* toegevoegd en wordt het *AllowCloudPasswordValidation* -kenmerk bewaard dat mogelijk al is ingesteld:

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Controleer of het bijgewerkte beleid uw wijzigingen weergeeft en of het kenmerk *AlternateIdLogin* nu is ingeschakeld:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="test-user-sign-in-with-email"></a>Gebruikers aanmelding testen met e-mail

Als u wilt testen of gebruikers zich kunnen aanmelden met e-mail, bladert u naar [https://myprofile.microsoft.com][my-profile] en meldt u zich aan met een gebruikers account op basis van hun e-mail adres, zoals `balas@fabrikam.com` , niet de UPN, zoals `balas@contoso.com` . De aanmeldings ervaring moet er hetzelfde uitzien als bij een aanmeld gebeurtenis op basis van een UPN.

## <a name="troubleshoot"></a>Problemen oplossen

Als gebruikers problemen ondervinden met aanmeldings gebeurtenissen met hun e-mail adres, raadpleegt u de volgende stappen voor probleem oplossing:

1. Zorg ervoor dat het e-mail adres van de gebruikers account is ingesteld voor het kenmerk *proxyAddresses* in de on-premises AD DS omgeving.
1. Controleer of Azure AD Connect is geconfigureerd en synchroniseert gebruikers accounts uit de on-premises AD DS omgeving in azure AD.
1. Controleer of het kenmerk *AlternateIdLogin* van het Azure AD *HomeRealmDiscoveryPolicy* -beleid is ingesteld op *' enabled ': True*:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hybride identiteiten, zoals Azure AD-app proxy of Azure AD Domain Services, raadpleegt u [Azure AD hybride identiteit voor toegang en beheer van on-premises workloads][hybrid-overview].

Zie How to Synchronizing [hash Sync][phs-overview] of [Pass-Through-verificatie][pta-overview] synchronisatie voor meer informatie over de bewerkingen voor Hybrid Identity.

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
