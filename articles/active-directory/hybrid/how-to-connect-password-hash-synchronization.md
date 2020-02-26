---
title: Wachtwoord-hashsynchronisatie met Azure AD Connect-synchronisatie implementeren | Microsoft Docs
description: Bevat informatie over de werking van wachtwoord-hashsynchronisatie en hoe u kunt instellen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2019
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 405b2fb9d9b8ef3bce17a9370ac87592a3437026
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585948"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Wachtwoord-hashsynchronisatie met Azure AD Connect sync implementeren
Dit artikel bevat gegevens die u nodig hebt om te synchroniseren van uw wachtwoorden van gebruikers uit een on-premises Active Directory-exemplaar naar een cloud-gebaseerde Azure Active Directory (Azure AD)-exemplaar.

## <a name="how-password-hash-synchronization-works"></a>Hoe synchronisatie van wachtwoord-hashes werkt
Wachtwoorden worden opgeslagen in de vorm van een weergave van de hash-waarde van de werkelijke gebruikerswachtwoord van de Active Directory domain Services. Een hash-waarde is een resultaat van een reken kundige functie ( *hash-algoritme*). Er bestaat geen methode het resultaat van een eenzijdige functie terug te draaien naar de versie in tekst zonder opmaak van een wachtwoord. 

Als u wilt synchroniseren van uw wachtwoord, extraheert Azure AD Connect-synchronisatie de wachtwoord-hash van de on-premises Active Directory-exemplaar. Verwerking van de extra beveiliging wordt toegepast op de wachtwoord-hash, voordat deze wordt gesynchroniseerd met de Azure Active Directory authentication-service. Wachtwoorden worden gesynchroniseerd op basis van per gebruiker en in chronologische volgorde.

De werkelijke gegevensstroom van het proces van synchronisatie van wachtwoord-hash is vergelijkbaar met de synchronisatie van gebruikersgegevens. Wachtwoorden worden echter vaker dan de standaard directory-synchronisatie-venster voor andere kenmerken gesynchroniseerd. Het proces van synchronisatie van wachtwoord-hash wordt elke twee minuten uitgevoerd. U kunt de frequentie van dit proces niet wijzigen. Wanneer u een wachtwoord wordt gesynchroniseerd, overschrijft het bestaande wachtwoord van de cloud.

De eerste keer dat u de wachtwoordfunctie-hash-synchronisatie inschakelen wordt uitgevoerd een initiële synchronisatie van de wachtwoorden van gebruikers binnen de regeling vallen. Kunt u definiëren niet expliciet een subset van de wachtwoorden van gebruikers die u wilt synchroniseren. Als er echter meerdere connectors zijn, is het mogelijk om wachtwoord hash-synchronisatie uit te scha kelen voor sommige connectors, maar niet voor andere met de cmdlet [set-ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant) .

Wanneer u een on-premises wachtwoord wijzigt, wordt het bijgewerkte wachtwoord gesynchroniseerd, meestal binnen een paar minuten.
Van mislukte synchronisatiepogingen wordt automatisch opnieuw geprobeerd de wachtwoordfunctie-hash-synchronisatie. Als er een fout optreedt tijdens een poging om het synchroniseren van een wachtwoord, wordt een fout wordt geregistreerd in uw Logboeken.

De synchronisatie van een wachtwoord heeft geen invloed op de gebruiker die momenteel is aangemeld.
Uw huidige sessie van de cloud-service wordt niet direct beïnvloed door een gesynchroniseerde wachtwoordwijziging dat zich voordoet, terwijl u bent aangemeld, in een cloud-service. Wanneer de service in de cloud vereist dat u opnieuw te verifiëren, moet u uw nieuwe wachtwoord op te geven.

Een gebruiker moet hun zakelijke referenties een tweede keer invoeren om te verifiëren met Azure AD, ongeacht of ze zijn aangemeld bij het bedrijfsnetwerk. Dit patroon kan echter worden geminimaliseerd als de gebruiker de aangemeld blijven selecteert in het selectievakje (KMSI) bij het aanmelden. Deze selectie stelt een sessiecookie die verificatie voor 180 dagen omzeilt. KMSI gedrag kan worden ingeschakeld of uitgeschakeld door de Azure AD-beheerder. Daarnaast kunt u wacht woorden verminderen door [naadloze SSO](how-to-connect-sso.md)in te scha kelen, waarbij gebruikers automatisch worden ondertekend wanneer ze zich op hun bedrijfs apparaten bevinden die zijn verbonden met uw bedrijfs netwerk.

> [!NOTE]
> Wachtwoordsynchronisatie wordt alleen ondersteund voor de gebruiker van het type object in Active Directory. Het wordt niet ondersteund voor het type iNetOrgPerson-object.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Gedetailleerde beschrijving van de werking van wachtwoord-hashsynchronisatie

De volgende sectie wordt beschreven, uitgebreide, de werking van wachtwoord-hashsynchronisatie tussen Active Directory en Azure AD.

![Gedetailleerde wachtwoord stroom](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Elke twee minuten de wachtwoord-hash synchronisatie-agent op de AD Connect-serveraanvragen opgeslagen wachtwoord-hashes (het kenmerk unicodePwd) vanaf een domeincontroller.  Deze aanvraag is via het standaard [MS-DRSR-](https://msdn.microsoft.com/library/cc228086.aspx) replicatie protocol dat wordt gebruikt voor het synchroniseren van gegevens tussen dc's. Het serviceaccount moet directorywijzigingen en repliceren Directory wijzigingen alle AD machtigingen (standaard op installatie) hebben tot de wachtwoord-hashes ophalen.
2. Voordat de domein controller wordt verzonden, wordt de MD4-wachtwoord-hash versleuteld met behulp van een sleutel die een [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) -hash van de RPC-sessie sleutel en een Salt is. Het verzendt vervolgens het resultaat naar de wachtwoord-hash-synchronisatieagent via RPC. De domeincontroller geeft ook de salt aan de synchronisatieagent via het protocol van de replicatie DC, zodat de agent is mogelijk voor het ontsleutelen van de envelop.
3. Nadat de synchronisatie agent voor wacht woord-hash de versleutelde envelop heeft, gebruikt [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) en het zout om een sleutel te genereren voor het ontsleutelen van de ontvangen gegevens naar de oorspronkelijke MD4-indeling. De synchronisatieagent van wachtwoord-hash heeft nooit toegang tot het wachtwoord niet-versleutelde tekst. De wachtwoord-hash-synchronisatieagent van gebruik van MD5 geldt uitsluitend voor replicatie protocol compatibiliteit met de domeincontroller en deze alleen on-premises tussen de domeincontroller en de wachtwoord-hash-synchronisatieagent is gebruikt.
4. De wachtwoord-hash-synchronisatieagent breidt de 16-bytes binary-wachtwoord-hash naar 64 bytes door eerst te converteren back-the-hash op een 32-bytes hexadecimale tekenreeks, vervolgens deze tekenreeks te converteren naar binair met UTF-16-codering.
5. De synchronisatieagent van de wachtwoord-hash-voegt een per gebruiker salt, die bestaan uit een lengte van 10-byte-salt, naar de 64-bytes binary verder beschermen van de oorspronkelijke hash.
6. De synchronisatie agent voor wacht woord-hash combineert vervolgens de MD4-hash plus het per gebruiker zout en voert deze in de functie [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) . 1000 iteraties van de hash-algoritme voor het [HMAC-sha256-](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) versleutelen worden gebruikt. 
7. De synchronisatieagent van wachtwoord-hash wordt de resulterende 32-byte-hash, worden samengevoegd in zowel de per gebruiker salt en het aantal SHA256 iteraties toe (voor gebruik door Azure AD), klikt u vervolgens verzendt de tekenreeks van Azure AD Connect naar Azure AD via SSL.</br> 
8. Wanneer een gebruiker wil zich aanmelden bij Azure AD en hun wachtwoord invoert, wordt het wachtwoord door de dezelfde MD4 + salt + PBKDF2 + HMAC-SHA256 proces uitgevoerd. Als de resulterende hash overeenkomt met de hash die zijn opgeslagen in Azure AD, wordt de gebruiker het juiste wachtwoord is ingevoerd en is geverifieerd.

> [!NOTE]
> De oorspronkelijke MD4-hash is niet verzonden naar Azure AD. In plaats daarvan wordt de SHA256-hash van de oorspronkelijke MD4-hash verzonden. Als gevolg hiervan als de hash die zijn opgeslagen in Azure AD wordt verkregen, worden deze niet gebruikt in een on-premises pass-the-hash-aanval.

### <a name="security-considerations"></a>Beveiligingsoverwegingen

Bij het synchroniseren van wachtwoorden, wordt de versie van de tekst zonder opmaak van uw wachtwoord niet blootgesteld aan de wachtwoordfunctie-hash-synchronisatie, naar Azure AD, of een van de gekoppelde services.

Verificatie van de gebruiker vindt plaats op basis van Azure AD in plaats van op basis van de Active Directory-exemplaar van het bedrijf. De SHA256-wachtwoord worden opgeslagen in Azure AD--een hash van de oorspronkelijke MD4-hash--is veiliger dan wat wordt opgeslagen in Active Directory. Worden verder, omdat deze SHA256-hash kan niet worden ontsleuteld, deze niet kan teruggebracht naar Active Directory-omgeving van de organisatie en weergegeven als het wachtwoord van een geldige gebruiker in een pass-the-hash-aanval.

### <a name="password-policy-considerations"></a>Overwegingen met betrekking tot het beleid van het wachtwoord

Er zijn twee soorten wachtwoordbeleid dat wordt beïnvloed door de wachtwoord-hashsynchronisatie inschakelen:

* Complexiteit wachtwoordbeleid
* Beleid vervaldatum gebruikerswachtwoord

#### <a name="password-complexity-policy"></a>Complexiteit wachtwoordbeleid

Wanneer de wachtwoord-hashsynchronisatie is ingeschakeld, overschrijven de complexiteit wachtwoordbeleid in uw on-premises Active Directory-exemplaar complexiteit beleid in de cloud voor gesynchroniseerde gebruikers. U kunt alle geldige wachtwoorden van uw on-premises Active Directory-exemplaar gebruiken voor toegang tot Azure AD-services.

> [!NOTE]
> Wachtwoorden voor gebruikers die rechtstreeks in de cloud gemaakt zijn nog steeds onderworpen aan beleid voor wachtwoorden zoals gedefinieerd in de cloud.

#### <a name="password-expiration-policy"></a>Beleid vervaldatum gebruikerswachtwoord

Als een gebruiker zich in het bereik van de wachtwoord-hash-synchronisatie bevindt, wordt het wacht woord voor het Cloud account standaard ingesteld op *nooit verlopen*.

U kunt zich aanmelden met uw cloud-services met behulp van een gesynchroniseerde wachtwoord is verlopen in uw on-premises omgeving. Uw cloud-wachtwoord wordt bijgewerkt zodra die u het wachtwoord in de on-premises omgeving wijzigen.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Open bare preview-versie van de *EnforceCloudPasswordPolicyForPasswordSyncedUsers* -functie

Als er gesynchroniseerde gebruikers zijn die alleen communiceren met Azure AD Integrated Services en moeten voldoen aan het beleid voor het verlopen van wacht woorden, kunt u ervoor zorgen dat ze voldoen aan het verloop beleid van uw Azure AD-wacht woord door de functie *EnforceCloudPasswordPolicyForPasswordSyncedUsers* in te scha kelen.

Wanneer *EnforceCloudPasswordPolicyForPasswordSyncedUsers* is uitgeschakeld (dit is de standaard instelling), stelt Azure AD Connect het kenmerk PasswordPolicies van gesynchroniseerde gebruikers in op "DisablePasswordExpiration". Dit gebeurt telkens wanneer het wacht woord van een gebruiker wordt gesynchroniseerd en geeft Azure AD het verloop beleid voor Cloud wachtwoord voor die gebruiker te negeren. U kunt de waarde van het kenmerk controleren met behulp van de Azure AD Power shell-module met de volgende opdracht:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


Als u de functie EnforceCloudPasswordPolicyForPasswordSyncedUsers wilt inschakelen, voert u de volgende opdracht uit met behulp van de MSOnline Power shell-module, zoals hieronder wordt weer gegeven. U moet ja typen voor de para meter Enable zoals hieronder wordt weer gegeven:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Als deze functie is ingeschakeld, gaat Azure AD niet naar elke gesynchroniseerde gebruiker om de `DisablePasswordExpiration` waarde uit het kenmerk PasswordPolicies te verwijderen. In plaats daarvan wordt de waarde ingesteld op `None` tijdens de volgende wachtwoord synchronisatie voor elke gebruiker wanneer ze hun wacht woord vervolgens wijzigen in on-premises AD.  

Het is raadzaam om EnforceCloudPasswordPolicyForPasswordSyncedUsers in te scha kelen voordat u wachtwoord-hash-synchronisatie inschakelt, zodat de eerste synchronisatie van wacht woord-hashes de `DisablePasswordExpiration` waarde niet toevoegt aan het kenmerk PasswordPolicies voor de gebruikers.

Het standaard wachtwoord beleid voor Azure AD vereist dat gebruikers elke 90 dagen hun wacht woord wijzigen. Als uw beleid in AD ook 90 dagen is, moeten de twee beleids regels overeenkomen. Als het AD-beleid echter niet 90 dagen is, kunt u het Azure AD-wachtwoord beleid bijwerken zodat dit overeenkomt met behulp van de set-MsolPasswordPolicy Power shell-opdracht.

Azure AD biedt ondersteuning voor een afzonderlijk beleid voor wachtwoord verloop per geregistreerd domein.

Voor behoud: als er gesynchroniseerde accounts zijn die niet-verlopende wacht woorden moeten hebben in azure AD, moet u de `DisablePasswordExpiration` waarde expliciet toevoegen aan het kenmerk PasswordPolicies van het gebruikers object in azure AD.  U kunt dit doen door de volgende opdracht uit te voeren.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Deze functie is nu beschikbaar in de open bare preview.

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Open bare preview van het synchroniseren van tijdelijke wacht woorden en ' wacht woord wijzigen bij de volgende aanmelding afdwingen '

Het is gebruikelijk dat een gebruiker het wacht woord tijdens de eerste aanmelding wijzigt, met name nadat een beheerders wachtwoord opnieuw is ingesteld.  Het is vaak bekend als het instellen van een ' tijdelijk ' wacht woord en wordt uitgevoerd door de vlag ' gebruiker moet wacht woord bij volgende aanmelding wijzigen ' op een gebruikers object in Active Directory (AD) in te scha kelen.
  
De functionaliteit van het tijdelijke wacht woord helpt ervoor te zorgen dat de overdracht van eigendom van de referentie wordt voltooid bij het eerste gebruik, om zo de tijd te beperken dat meer dan één persoon kennis van die referentie heeft.

Ter ondersteuning van tijdelijke wacht woorden in azure AD voor gesynchroniseerde gebruikers, kunt u de functie *ForcePasswordChangeOnLogOn* inschakelen door de volgende opdracht uit te voeren op uw Azure AD Connect-server:

`Set-ADSyncAADCompanyFeature  -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Als u het wacht woord voor de volgende aanmelding wilt wijzigen, moet u het wacht woord op hetzelfde moment wijzigen.  AD Connect neemt de vlag voor het wijzigen van het wacht woord niet op zichzelf op. het is een aanvulling op de gedetecteerde wachtwoord wijziging die optreedt tijdens de wachtwoord-hash-synchronisatie.

> [!CAUTION]
> Als u self-service voor wachtwoord herstel (SSPR) niet inschakelt in azure AD-gebruikers, heeft dit een verwarrende ervaring wanneer ze hun wacht woord opnieuw instellen in azure AD en zich vervolgens proberen aan te melden Active Directory met het nieuwe wacht woord, omdat het nieuwe wacht woord niet geldig is in Active Directory . U moet deze functie alleen gebruiken wanneer SSPR en wacht woord terugschrijven is ingeschakeld op de Tenant.

> [!NOTE]
> Deze functie is nu beschikbaar in de open bare preview.

#### <a name="account-expiration"></a>Account verloopt

Als uw organisatie gebruikmaakt van het kenmerk accountExpires als onderdeel van het beheer van gebruikersaccounts, wordt dit kenmerk is niet gesynchroniseerd naar Azure AD. Als gevolg hiervan wordt verlopen Active Directory-account in een omgeving die is geconfigureerd voor wachtwoord-hashsynchronisatie nog steeds actief zijn in Azure AD. Het is raadzaam dat als het account is verlopen, een werk stroom actie een Power shell-script moet activeren waarmee het Azure AD-account van de gebruiker wordt uitgeschakeld (gebruik de cmdlet [set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) ). Daarentegen, wanneer het account is ingeschakeld, de Azure AD-exemplaar moet zijn ingeschakeld.

### <a name="overwrite-synchronized-passwords"></a>Gesynchroniseerde wachtwoorden overschrijven

Een beheerder kan handmatig uw wachtwoord opnieuw instellen met behulp van Windows PowerShell.

In dit geval wordt het nieuwe wachtwoord vervangt je gesynchroniseerde wachtwoord, en alle wachtwoordbeleid gedefinieerd in de cloud worden toegepast op het nieuwe wachtwoord.

Als u uw on-premises wachtwoord wijzigt ook het nieuwe wachtwoord wordt gesynchroniseerd met de cloud, en dit vervangt de handmatig bijgewerkte wachtwoord.

De synchronisatie van een wachtwoord heeft geen invloed op de Azure-gebruiker die is aangemeld. Uw huidige sessie van de cloud-service is niet direct beïnvloed door een gesynchroniseerde wachtwoordwijziging die optreedt terwijl u bent aangemeld bij een service in de cloud. KMSI breidt de duur van dit verschil. Wanneer de service in de cloud vereist dat u opnieuw te verifiëren, moet u uw nieuwe wachtwoord op te geven.

### <a name="additional-advantages"></a>Extra voordelen

- Wachtwoord-hashsynchronisatie is over het algemeen eenvoudiger te implementeren dan een federation-service. Het vereist geen extra servers en elimineert de afhankelijkheid van een maximaal beschikbare federation-service om gebruikers te verifiëren.
- Wachtwoord-hashsynchronisatie kan ook worden ingeschakeld naast federation. Het kan worden gebruikt als alternatieve methode als er een storing optreedt in de federation-service.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Wacht woord-hash-synchronisatie proces voor Azure AD Domain Services

Als u Azure AD Domain Services gebruikt om verouderde verificatie te bieden voor toepassingen en services waarvoor Kerberos, LDAP of NTLM moet worden gebruikt, maken sommige extra processen deel uit van de synchronisatie stroom voor wacht woord-hashes. Azure AD Connect gebruikt het volgende proces om wacht woord-hashes te synchroniseren met Azure AD voor gebruik in Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect mag alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS omgevingen. Het is niet mogelijk om Azure AD Connect te installeren in een beheerd domein van Azure AD DS om objecten terug te synchroniseren naar Azure AD.
>
> Azure AD Connect synchroniseert alleen verouderde wachtwoord-hashes wanneer u Azure AD DS voor uw Azure AD-Tenant inschakelt. De volgende stappen worden niet gebruikt als u alleen Azure AD Connect gebruikt om een on-premises AD DS omgeving te synchroniseren met Azure AD.
>
> Als uw verouderde toepassingen geen gebruikmaken van NTLM-verificatie of LDAP-eenvoudige bindingen, raden we u aan NTLM-wachtwoord hash-synchronisatie voor Azure AD DS uit te scha kelen. Zie voor meer informatie [zwak coderings suites en hash-synchronisatie van NTLM-referenties uitschakelen](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect haalt de open bare sleutel op voor het exemplaar van de Tenant van Azure AD Domain Services.
1. Wanneer een gebruiker het wacht woord wijzigt, slaat de on-premises domein controller het resultaat van het wijzigen van het wacht woord (hashes) op in twee kenmerken:
    * *unicodePwd* voor de NTLM-wachtwoord-hash.
    * *supplementalCredentials* voor de Kerberos-wachtwoord-hash.
1. Azure AD Connect detecteert wachtwoord wijzigingen via het Directory replicatie kanaal (kenmerk wijzigingen die moeten worden gerepliceerd naar andere domein controllers).
1. Azure AD Connect voert de volgende stappen uit voor elke gebruiker waarvan het wacht woord is gewijzigd:
    * Hiermee wordt een wille keurige AES 256-bits symmetrische sleutel gegenereerd.
    * Hiermee wordt een wille keurige initialisatie vector gegenereerd die nodig is voor de eerste afronding van versleuteling.
    * Extraheert Kerberos-wachtwoord-hashes uit de *supplementalCredentials* -kenmerken.
    * Hiermee wordt de Azure AD Domain Services instelling beveiligings configuratie *SyncNtlmPasswords* gecontroleerd.
        * Als deze instelling is uitgeschakeld, wordt een wille keurige NTLM-hash met een hoge entropie gegenereerd (anders dan het wacht woord van de gebruiker). Deze hash wordt vervolgens gecombineerd met de exacte Kerberos-wachtwoord-hashes van het kenmerk *supplementalCrendetials* in één gegevens structuur.
        * Bij inschakeling wordt de waarde van het kenmerk *unicodePwd* gecombineerd met het geëxtraheerde Kerberos-wacht woord hashes van het kenmerk *supplementalCredentials* in één gegevens structuur.
    * Hiermee versleutelt u de afzonderlijke gegevens structuur met de AES symmetrische sleutel.
    * Hiermee versleutelt u de AES symmetrische sleutel met de open bare sleutel van de Tenant Azure AD Domain Services.
1. Azure AD Connect verzendt de versleutelde AES symmetrische sleutel, de versleutelde gegevens structuur met de wacht woord-hashes en de initialisatie vector naar Azure AD.
1. In azure AD worden de versleutelde AES symmetrische sleutel, de versleutelde gegevens structuur en de initialisatie vector voor de gebruiker opgeslagen.
1. Azure AD duwt de versleutelde AES symmetrische sleutel, de versleutelde gegevens structuur en de initialisatie vector met behulp van een intern synchronisatie mechanisme via een versleutelde HTTP-sessie tot Azure AD Domain Services.
1. Azure AD Domain Services haalt de persoonlijke sleutel voor het exemplaar van de Tenant op uit de Azure-sleutel kluis.
1. Azure AD Domain Services voert de volgende stappen uit voor elke versleutelde set gegevens (die de wachtwoord wijziging van één gebruiker vertegenwoordigen):
    * Maakt gebruik van de persoonlijke sleutel voor het ontsleutelen van de AES symmetrische sleutel.
    * Maakt gebruik van de AES symmetrische sleutel met de initialisatie vector voor het ontsleutelen van de versleutelde gegevens structuur die de wacht woord-hashes bevat.
    * Schrijft de Kerberos-wachtwoord hashes die worden ontvangen naar de Azure AD Domain Services domein controller. De hashes worden opgeslagen in het kenmerk *supplementalCredentials* van het gebruikers object dat is versleuteld met de open bare sleutel van de Azure AD Domain Services domein controller.
    * Azure AD Domain Services schrijft de NTLM-wachtwoord-hash die is ontvangen naar de Azure AD Domain Services-domein controller. De hash wordt opgeslagen in het kenmerk *unicodePwd* van het gebruikers object dat is versleuteld met de open bare sleutel van de Azure AD Domain Services domein controller.

## <a name="enable-password-hash-synchronization"></a>Wachtwoord-hashsynchronisatie inschakelen

>[!IMPORTANT]
>Als u migreert van AD FS (of andere Federatie technologieën) naar de synchronisatie van wacht woord-hashes, wordt u ten zeerste aangeraden onze gedetailleerde implementatie handleiding te volgen die u [hier](https://aka.ms/adfstophsdpdownload)hebt gepubliceerd.

Wanneer u Azure AD Connect installeert met de optie **snelle instellingen** , wordt de synchronisatie van wacht woord-hash automatisch ingeschakeld. Zie [aan de slag met Azure AD Connect met behulp van snelle instellingen](how-to-connect-install-express.md)voor meer informatie.

Als u aangepaste instellingen wanneer u Azure AD Connect installeert, is wachtwoord-hashsynchronisatie beschikbaar op de aanmeldingspagina van gebruiker. Zie [aangepaste installatie van Azure AD Connect](how-to-connect-install-custom.md)voor meer informatie.

![Wachtwoord-hashsynchronisatie inschakelen](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Wachtwoord-hashsynchronisatie en FIPS
Als uw server is vergrendeld op basis van Federal Information Processing Standard (FIPS), wordt MD5 uitgeschakeld.

**Voer de volgende stappen uit om MD5 in te scha kelen voor wachtwoord hash-synchronisatie:**

1. Ga naar %programfiles%\Azure AD Sync\Bin.
2. Open miiserver.exe.config.
3. Ga naar de configuratie/runtime-knooppunt aan het einde van het bestand.
4. Voeg het volgende knoop punt toe: `<enforceFIPSPolicy enabled="false"/>`
5. Sla uw wijzigingen op.

Ter informatie: dit fragment is hoe het eruit moet zien:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Zie [Azure AD Password Hash Sync, Encryption en FIPS compliance](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)(Engelstalig) voor meer informatie over beveiliging en FIPS.

## <a name="troubleshoot-password-hash-synchronization"></a>Problemen met wachtwoord-hashsynchronisatie oplossen
Zie [problemen met wachtwoord synchronisatie oplossen](tshoot-connect-password-hash-synchronization.md)als u problemen ondervindt met het synchroniseren van wachtwoord-hashes.

## <a name="next-steps"></a>Volgende stappen
* [Azure AD Connect synchronisatie: synchronisatie opties aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
* [Een stap-voor-stap implementatie plan voor het migreren van ADFS naar wachtwoord-hash-synchronisatie](https://aka.ms/authenticationDeploymentPlan)
