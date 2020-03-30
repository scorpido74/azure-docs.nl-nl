---
title: Synchronisatie van wachtwoordhash implementeren met Azure AD Connect-synchronisatie | Microsoft Documenten
description: Hier vindt u informatie over hoe wachtwoordhashsynchronisatie werkt en hoe u deze instellen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: c41b11ab65f5710d338ce0041579e1eb4678ec42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331372"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Wachtwoord-hashsynchronisatie implementeren met Azure AD Connect-synchronisatie
In dit artikel vindt u informatie die u nodig hebt om uw gebruikerswachtwoorden te synchroniseren van een on-premises Active Directory-instantie naar een Azure AD-exemplaar (Azure AD) (Cloud.

## <a name="how-password-hash-synchronization-works"></a>Hoe synchronisatie van wachtwoord-hashes werkt
De Active Directory-domeinservice slaat wachtwoorden op in de vorm van een hashwaardeweergave van het werkelijke gebruikerswachtwoord. Een hashwaarde is het resultaat van een eenrichtingswiskundige functie (het *hashing-algoritme).* Er bestaat geen methode het resultaat van een eenzijdige functie terug te draaien naar de versie in tekst zonder opmaak van een wachtwoord. 

Als u uw wachtwoord wilt synchroniseren, haalt Azure AD Connect-synchronisatie uw wachtwoordhash uit het on-premises Active Directory-exemplaar. Er wordt extra beveiligingsverwerking toegepast op de wachtwoordhash voordat deze wordt gesynchroniseerd met de Azure Active Directory-verificatieservice. Wachtwoorden worden gesynchroniseerd per gebruiker en in chronologische volgorde.

De werkelijke gegevensstroom van het wachtwoordhashsynchronisatieproces is vergelijkbaar met de synchronisatie van gebruikersgegevens. Wachtwoorden worden echter vaker gesynchroniseerd dan het standaardsynchronisatievenster voor directorysynchronisatie voor andere kenmerken. Het wachtwoordhashsynchronisatieproces wordt elke 2 minuten uitgevoerd. U de frequentie van dit proces niet wijzigen. Wanneer u een wachtwoord synchroniseert, wordt het bestaande cloudwachtwoord overschrijft.

De eerste keer dat u de functie voor wachtwoordhashsynchronisatie inschakelt, voert het een eerste synchronisatie van de wachtwoorden van alle gebruikers in de scope uit. U niet expliciet een subset van gebruikerswachtwoorden definiëren die u wilt synchroniseren. Als er echter meerdere connectors zijn, is het mogelijk om wachtwoordhashsynchronisatie uit te schakelen voor sommige connectors, maar niet voor andere met behulp van de cmdlet [Set-ADSyncAADPasswordConfigurationConfiguration.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant)

Wanneer u een on-premises wachtwoord wijzigt, wordt het bijgewerkte wachtwoord gesynchroniseerd, meestal binnen enkele minuten.
De wachtwoordhashsynchronisatiefunctie probeert automatisch mislukte synchronisatiepogingen opnieuw in te voeren. Als er een fout optreedt tijdens een poging om een wachtwoord te synchroniseren, wordt er een fout in de gebeurtenisviewer geregistreerd.

De synchronisatie van een wachtwoord heeft geen invloed op de gebruiker die momenteel is aangemeld.
Uw huidige cloudservicesessie wordt niet onmiddellijk beïnvloed door een gesynchroniseerde wachtwoordwijziging die optreedt tijdens uw instemming bij een cloudservice. Wanneer u zich echter opnieuw moet verifiëren door de cloudservice, moet u uw nieuwe wachtwoord opgeven.

Een gebruiker moet zijn bedrijfsreferenties een tweede keer invoeren om te verifiëren bij Azure AD, ongeacht of deze is aangemeld bij zijn bedrijfsnetwerk. Dit patroon kan echter worden geminimaliseerd als de gebruiker het selectievakje Houd me aangemeld (KMSI) inschakelt bij aanmelding. Deze selectie stelt een sessiecookie in die verificatie gedurende 180 dagen omzeilt. HET KMSI-gedrag kan worden ingeschakeld of uitgeschakeld door de Azure AD-beheerder. Bovendien u wachtwoordprompts verminderen door [Seamless SSO](how-to-connect-sso.md)in te schakelen, die gebruikers automatisch inlogt wanneer ze zich op hun bedrijfsapparaten bevinden die zijn verbonden met uw bedrijfsnetwerk.

> [!NOTE]
> Wachtwoordsynchronisatie wordt alleen ondersteund voor de gebruiker van het objecttype in Active Directory. Het wordt niet ondersteund voor het objecttype iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Gedetailleerde beschrijving van hoe wachtwoordhashsynchronisatie werkt

In de volgende sectie wordt uitgebreid beschreven hoe wachtwoordhashsynchronisatie werkt tussen Active Directory en Azure AD.

![Gedetailleerde wachtwoordstroom](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Elke twee minuten vraagt de wachtwoordhashsynchronisatieagent op de AD Connect-server opgeslagen wachtwoordhashes (het unicodePwd-kenmerk) van een DC.  Deze aanvraag gebeurt via het standaard [MS-DRSR-replicatieprotocol](https://msdn.microsoft.com/library/cc228086.aspx) dat wordt gebruikt om gegevens tussen DC's te synchroniseren. Het serviceaccount moet replicadirectorywijzigingen en gerepliceerde adreswijzigingen hebben Alle AD-machtigingen (standaard verleend bij installatie) om de wachtwoordhashes te verkrijgen.
2. Voor het verzenden versleutelt de DC de MD4-wachtwoordhash met behulp van een sleutel die een [MD5-hash](https://www.rfc-editor.org/rfc/rfc1321.txt) van de RPC-sessiesleutel en een zout is. Het stuurt vervolgens het resultaat naar het wachtwoord hash synchronisatie agent via RPC. De DC geeft het zout ook door aan de synchronisatie-agent met behulp van het DC-replicatieprotocol, zodat de agent de envelop kan ontsleutelen.
3. Nadat de wachtwoordhashsynchronisatieagent de versleutelde envelop heeft, gebruikt het [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) en het zout om een sleutel te genereren om de ontvangen gegevens terug te decoderen naar de oorspronkelijke MD4-indeling. De wachtwoordhashsynchronisatieagent heeft nooit toegang tot het wachtwoord voor wissen tekst. Het gebruik van MD5 door de wachtwoordhash-synchronisatieagent is strikt voor de compatibiliteit van replicatieprotocol's met de DC en wordt alleen op locatie gebruikt tussen de DC- en de wachtwoordhashsynchronisatieagent.
4. De wachtwoordhashsynchronisatieagent breidt de 16-byte binaire wachtwoordhash uit tot 64 bytes door de hash eerst om te zetten naar een hexadecimale tekenreeks van 32 bytes en vervolgens deze tekenreeks opnieuw om te zetten in binaire met UTF-16-codering.
5. De wachtwoord hash synchronisatie agent voegt een per gebruiker zout, bestaande uit een 10-byte lengte zout, aan de 64-byte binaire om verder te beschermen de oorspronkelijke hash.
6. De wachtwoordhashsynchronisatieagent combineert vervolgens de MD4-hash plus het zout per gebruiker en voert deze in de [PBKDF2-functie.](https://www.ietf.org/rfc/rfc2898.txt) Er worden 1000 iteraties van het [HMAC-SHA256-keyed](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) hashing-algoritme gebruikt. 
7. De wachtwoordhashsynchronisatieagent neemt de resulterende hash van 32 byte, concateert zowel het zout per gebruiker als het aantal SHA256-iteraties (voor gebruik door Azure AD) en verzendt vervolgens de tekenreeks van Azure AD Connect naar Azure AD via TLS.</br> 
8. Wanneer een gebruiker zich probeert aan te melden bij Azure AD en zijn wachtwoord invoert, wordt het wachtwoord uitgevoerd via hetzelfde MD4+salt+PBKDF2+HMAC-SHA256-proces. Als de resulterende hash overeenkomt met de hash die is opgeslagen in Azure AD, heeft de gebruiker het juiste wachtwoord ingevoerd en is deze geverifieerd.

> [!NOTE]
> De oorspronkelijke MD4-hash wordt niet verzonden naar Azure AD. In plaats daarvan wordt de SHA256-hash van de originele MD4-hash verzonden. Als de hash die is opgeslagen in Azure AD wordt verkregen, kan deze niet worden gebruikt in een on-premises pass-the-hash-aanval.

### <a name="security-considerations"></a>Beveiligingsoverwegingen

Bij het synchroniseren van wachtwoorden wordt de plain-text-versie van uw wachtwoord niet blootgesteld aan de functie voor wachtwoordhashsynchronisatie, azure AD of een van de bijbehorende services.

Gebruikersverificatie vindt plaats tegen Azure AD in plaats van tegen de eigen Active Directory-instantie van de organisatie. De SHA256-wachtwoordgegevens die zijn opgeslagen in Azure AD- een hash van de oorspronkelijke MD4-hash, zijn veiliger dan wat is opgeslagen in Active Directory. Omdat deze SHA256-hash niet kan worden gedecodeerd, kan deze niet worden teruggebracht naar de Active Directory-omgeving van de organisatie en worden gepresenteerd als een geldig gebruikerswachtwoord in een pass-the-hash-aanval.

### <a name="password-policy-considerations"></a>Overwegingen wachtwoordbeleid

Er zijn twee typen wachtwoordbeleidsregels die worden beïnvloed door wachtwoordhashsynchronisatie in te schakelen:

* Complexiteitsbeleid voor wachtwoorden
* Verlopenvan wachtwoordbeleid

#### <a name="password-complexity-policy"></a>Complexiteitsbeleid voor wachtwoorden

Wanneer synchronisatie van wachtwoordhash is ingeschakeld, overschrijft het beleid voor wachtwoordcomplexiteit in uw on-premises Active Directory-instantie complexiteitsbeleid in de cloud voor gesynchroniseerde gebruikers. U alle geldige wachtwoorden van uw on-premises Active Directory-exemplaar gebruiken om toegang te krijgen tot Azure AD-services.

> [!NOTE]
> Wachtwoorden voor gebruikers die rechtstreeks in de cloud worden gemaakt, zijn nog steeds onderworpen aan wachtwoordbeleid zoals gedefinieerd in de cloud.

#### <a name="password-expiration-policy"></a>Verlopenvan wachtwoordbeleid

Als een gebruiker zich in het bereik van wachtwoordhashsynchronisatie bevindt, is het wachtwoord van het cloudaccount standaard ingesteld op *nooit verlopen.*

U zich blijven aanmelden bij uw cloudservices met een gesynchroniseerd wachtwoord dat is verlopen in uw on-premises omgeving. Uw cloudwachtwoord wordt bijgewerkt wanneer u het wachtwoord de volgende keer wijzigt in de on-premises omgeving.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Openbare preview van de functie *EnforceCloudPasswordPolicyForPasswordSyncedUsers*

Als er gesynchroniseerde gebruikers zijn die alleen interactie hebben met geïntegreerde Azure AD-services en ook moeten voldoen aan een beleid voor het verlopen van wachtwoorden, u hen dwingen om te voldoen aan uw Azure AD-wachtwoordvervaldatumbeleid door de functie *EnforceCloudPasswordPolicyForPasswordSyncedUsers in* te schakelen.

Wanneer *EnforceCloudPasswordPolicyForPasswordSyncedUsers* is uitgeschakeld (de standaardinstelling), stelt Azure AD Connect het kenmerk PasswordPolicies van gesynchroniseerde gebruikers in op 'PasswordPasswordExpiration'. Dit gebeurt elke keer dat het wachtwoord van een gebruiker wordt gesynchroniseerd en geeft Azure AD de opdracht om het vervaldatumsbeleid voor cloudwachtwoorden voor die gebruiker te negeren. U de waarde van het kenmerk controleren met de Azure AD PowerShell-module met de volgende opdracht:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


Voer de volgende opdracht uit met de MSOnline PowerShell-module zoals hieronder wordt weergegeven om de functie EnforceCloudPasswordPolicyPolicyForPasswordSyncedUsers in te schakelen. U zou ja moeten typen voor de parameter Inschakelen zoals hieronder weergegeven:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Eenmaal ingeschakeld, gaat Azure AD niet naar elke `DisablePasswordExpiration` gesynchroniseerde gebruiker om de waarde te verwijderen uit het kenmerk PasswordPolicies. In plaats daarvan wordt `None` de waarde ingesteld op tijdens de volgende wachtwoordsynchronisatie voor elke gebruiker wanneer ze vervolgens hun wachtwoord in on-premises AD wijzigen.  

Het wordt aanbevolen om EnforceCloudPasswordPolicyForPasswordSyncedUsers in te schakelen voordat wachtwoordhashwordt ingeschakeld, zodat de `DisablePasswordExpiration` eerste synchronisatie van wachtwoordhashes de waarde niet toevoegt aan het kenmerk PasswordPolicies voor de gebruikers.

Het standaard azure AD-wachtwoordbeleid vereist dat gebruikers hun wachtwoorden elke 90 dagen wijzigen. Als uw beleid in AD ook 90 dagen is, moeten de twee beleidsregels overeenkomen. Als het AD-beleid echter geen 90 dagen is, u het Azure AD-wachtwoordbeleid aanpassen aan de overeenkomst met de opdracht PowerShell set-MsolPasswordPolicy PowerShell.

Azure AD ondersteunt een afzonderlijk wachtwoordverloopbeleid per geregistreerd domein.

Waarschuwing: als er gesynchroniseerde accounts zijn die niet-verlopende wachtwoorden in Azure AD `DisablePasswordExpiration` moeten hebben, moet u de waarde expliciet toevoegen aan het kenmerk PasswordPolicies van het gebruikersobject in Azure AD.  U dit doen door de volgende opdracht uit te voeren.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Deze functie staat nu in Public Preview.
> De opdracht Set-MsolPasswordPolicy PowerShell werkt niet op federatieve domeinen. 

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Openbare voorbeeld van het synchroniseren van tijdelijke wachtwoorden en 'Wachtwoordwijziging forceren bij volgende aanmelding'

Het is typisch om een gebruiker te dwingen om zijn wachtwoord te wijzigen tijdens zijn eerste aanmelding, vooral nadat een wachtwoordreset van de beheerder optreedt.  Het is algemeen bekend als het instellen van een "tijdelijk" wachtwoord en wordt voltooid door het controleren van de "Gebruiker moet wachtwoord wijzigen bij volgende aanmelding" vlag op een gebruikersobject in Active Directory (AD).
  
De tijdelijke wachtwoordfunctionaliteit helpt ervoor te zorgen dat de overdracht van het eigendom van de referentie bij het eerste gebruik wordt voltooid, om de tijdsduur te minimaliseren waarin meer dan één persoon kennis van die referentie heeft.

Als u tijdelijke wachtwoorden in Azure AD voor gesynchroniseerde gebruikers wilt ondersteunen, u de functie *ForcePasswordChangeOnLogOn* inschakelen door de volgende opdracht uit te voeren op uw Azure AD Connect-server:

`Set-ADSyncAADCompanyFeature  -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Als u een gebruiker dwingt zijn wachtwoord bij de volgende aanmelding te wijzigen, is tegelijkertijd een wachtwoordwijziging vereist.  Azure AD Connect pikt de vlag voor het wijzigen van force-wachtwoord niet vanzelf op. het is een aanvulling op de gedetecteerde wachtwoordwijziging die optreedt tijdens het synchroniseren van wachtwoordhash.

> [!CAUTION]
> U mag deze functie alleen gebruiken wanneer SSPR en Password Writeback zijn ingeschakeld op de tenant.  Dit is zo dat als een gebruiker zijn wachtwoord wijzigt via SSPR, het wordt gesynchroniseerd met Active Directory.

> [!NOTE]
> Deze functie is in openbare preview op dit moment.

#### <a name="account-expiration"></a>Account verlopen

Als uw organisatie het kenmerk accountExpires gebruikt als onderdeel van gebruikersaccountbeheer, wordt dit kenmerk niet gesynchroniseerd met Azure AD. Als gevolg hiervan is een verlopen Active Directory-account in een omgeving die is geconfigureerd voor wachtwoordhashsynchronisatie, nog steeds actief in Azure AD. We raden u aan dat als het account is verlopen, een werkstroomactie een PowerShell-script activeert waarmee het Azure AD-account van de gebruiker wordt uitgeschakeld (gebruik de cmdlet [Set-AzureADUser).](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) Wanneer het account is ingeschakeld, moet de AD-instantie Azure worden ingeschakeld wanneer het account is ingeschakeld.

### <a name="overwrite-synchronized-passwords"></a>Gesynchroniseerde wachtwoorden overschrijven

Een beheerder kan uw wachtwoord handmatig opnieuw instellen met Windows PowerShell.

In dit geval overschrijft het nieuwe wachtwoord uw gesynchroniseerde wachtwoord en worden alle wachtwoordbeleidsregels die in de cloud zijn gedefinieerd, toegepast op het nieuwe wachtwoord.

Als u uw on-premises wachtwoord opnieuw wijzigt, wordt het nieuwe wachtwoord gesynchroniseerd met de cloud en wordt het handmatig bijgewerkte wachtwoord overschreven.

De synchronisatie van een wachtwoord heeft geen invloed op de Azure-gebruiker die is aangemeld. Uw huidige cloudservicesessie wordt niet onmiddellijk beïnvloed door een gesynchroniseerde wachtwoordwijziging die optreedt terwijl u bent aangemeld bij een cloudservice. KMSI verlengt de duur van dit verschil. Wanneer u door de cloudservice opnieuw moet verifiëren, moet u uw nieuwe wachtwoord opgeven.

### <a name="additional-advantages"></a>Extra voordelen

- Over het algemeen is wachtwoordhashsynchronisatie eenvoudiger te implementeren dan een federatieservice. Het vereist geen extra servers en elimineert de afhankelijkheid van een zeer beschikbare federatieservice om gebruikers te verifiëren.
- Wachtwoordhashsynchronisatie kan naast federatie ook worden ingeschakeld. Het kan worden gebruikt als een terugval als uw federatie service ervaart een storing.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Wachtwoordhashsynchronisatieproces voor Azure AD Domain Services

Als u Azure AD Domain Services gebruikt om verouderde verificatie te bieden voor toepassingen en services die Kerberos, LDAP of NTLM moeten gebruiken, maken sommige aanvullende processen deel uit van de synchronisatiestroom van wachtwoordhash. Azure AD Connect gebruikt het aanvullende volgende proces om wachtwoordhashes te synchroniseren met Azure AD voor gebruik in Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect mag alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS-omgevingen. Azure AD Connect wordt niet geïnstalleerd in een door Azure AD DS beheerd domein om objecten te synchroniseren met Azure AD.
>
> Azure AD Connect synchroniseert alleen verouderde wachtwoordhashes wanneer u Azure AD DS inschakelt voor uw Azure AD-tenant. De volgende stappen worden niet gebruikt als u alleen Azure AD Connect gebruikt om een on-premises AD DS-omgeving te synchroniseren met Azure AD.
>
> Als uw oudere toepassingen geen NTLM-verificatie of LDAP-eenvoudige bindingen gebruiken, raden we u aan ntlm-wachtwoordhashsynchronisatie voor Azure AD DS uit te schakelen. Zie [Zwakke cijfersuites en NTLM-hashsynchronisatie uitschakelen](../../active-directory-domain-services/secure-your-domain.md)voor meer informatie.

1. Azure AD Connect haalt de openbare sleutel op voor het exemplaar van Azure AD Domain Services van de tenant.
1. Wanneer een gebruiker zijn wachtwoord wijzigt, slaat de on-premises domeincontroller het resultaat van de wachtwoordwijziging (hashes) op in twee kenmerken:
    * *unicodePwd* voor de NTLM wachtwoord hash.
    * *aanvullende referenties* voor de Kerberos-wachtwoordhash.
1. Azure AD Connect detecteert wachtwoordwijzigingen via het mapreplicatiekanaal (kenmerkwijzigingen die moeten worden gerepliceerd naar andere domeincontrollers).
1. Voor elke gebruiker wiens wachtwoord is gewijzigd, voert Azure AD Connect de volgende stappen uit:
    * Genereert een willekeurige AES 256-bits symmetrische toets.
    * Genereert een willekeurige initialisatievector die nodig is voor de eerste ronde van versleuteling.
    * Haalt Kerberos wachtwoord hashes uit de *aanvullendeCredentials* attributen.
    * Hiermee controleert u de instelling *SynchronisatieNtlmPasswords* voor azure AD Domain Services.
        * Als deze instelling is uitgeschakeld, genereert u een willekeurige NTLM-hash met hoge entropie (anders dan het wachtwoord van de gebruiker). Deze hash wordt vervolgens gecombineerd met de veeleisende Kerberos wachtwoord hashes van de *aanvullendeCrendetials* attribuut in een gegevensstructuur.
        * Als ingeschakeld, combineert de waarde van de *unicodePwd* attribuut met de geëxtraheerd Kerberos wachtwoord hashes uit de *aanvullendeCredentials* attribuut in een gegevensstructuur.
    * Versleutelt de afzonderlijke gegevensstructuur met behulp van de AES-symmetrische toets.
    * Versleutelt de AES-symmetrische sleutel met behulp van de openbare sleutel azure AD Domain Services van de tenant.
1. Azure AD Connect verzendt de versleutelde AES-symmetrische sleutel, de versleutelde gegevensstructuur met de wachtwoordhashes en de initialisatievector naar Azure AD.
1. Azure AD slaat de versleutelde AES-symmetrische sleutel, de versleutelde gegevensstructuur en de initialisatievector voor de gebruiker op.
1. Azure AD pusht de versleutelde AES-symmetrische toets, de versleutelde gegevensstructuur en de initialisatievector met behulp van een intern synchronisatiemechanisme over een versleutelde HTTP-sessie naar Azure AD Domain Services.
1. Azure AD Domain Services haalt de privésleutel voor het exemplaar van de tenant op uit azure key vault.
1. Voor elke versleutelde set gegevens (die de wachtwoordwijziging van één gebruiker vertegenwoordigt), voert Azure AD Domain Services vervolgens de volgende stappen uit:
    * Gebruikt de privésleutel om de AES-symmetrische sleutel te decoderen.
    * Gebruikt de AES-symmetrische sleutel met de initialisatievector om de versleutelde gegevensstructuur te decoderen die de wachtwoordhashes bevat.
    * Hiermee schrijft u de Kerberos-wachtwoordhashes die het ontvangt aan de domeincontroller azure AD Domain Services. De hashes worden opgeslagen in het *aanvullende kenmerken van het* object dat is versleuteld met de openbare sleutel van de Azure AD Domain Services-domeincontroller.
    * Azure AD Domain Services schrijft de NTLM-wachtwoordhash die het heeft ontvangen aan de domeincontroller azure AD Domain Services. De hash wordt opgeslagen in het *unicodePwd-kenmerk* van het gebruikersobject dat is versleuteld naar de openbare sleutel van de Azure AD Domain Services-domeincontroller.

## <a name="enable-password-hash-synchronization"></a>Wachtwoord-hashsynchronisatie inschakelen

>[!IMPORTANT]
>Als u migreert van AD FS (of andere federatietechnologieën) naar Wachtwoordhashsynchronisatie, raden we u ten zeerste aan onze gedetailleerde implementatiehandleiding te volgen die [hier](https://aka.ms/adfstophsdpdownload)wordt gepubliceerd.

Wanneer u Azure AD Connect installeert met de optie **Expresinstellingen,** wordt wachtwoordhashsynchronisatie automatisch ingeschakeld. Zie [Aan de slag met Azure AD Connect met expresinstellingen](how-to-connect-install-express.md)voor meer informatie.

Als u aangepaste instellingen gebruikt wanneer u Azure AD Connect installeert, is wachtwoordhashsynchronisatie beschikbaar op de aanmeldingspagina van de gebruiker. Zie [Aangepaste installatie van Azure AD Connect](how-to-connect-install-custom.md)voor meer informatie.

![Wachtwoord-hashsynchronisatie inschakelen](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synchronisatie van wachtwoordhash en FIPS
Als uw server is vergrendeld volgens de Federal Information Processing Standard (FIPS), is MD5 uitgeschakeld.

**Voer de volgende stappen uit om MD5 in te schakelen voor wachtwoordhashsynchronisatie:**

1. Ga naar %programfiles%\Azure AD Sync\Bin.
2. Open miiserver.exe.config.
3. Ga naar het knooppunt configuratie/runtime aan het einde van het bestand.
4. Voeg het volgende knooppunt toe:`<enforceFIPSPolicy enabled="false"/>`
5. Sla uw wijzigingen op.

Ter referentie, dit fragment is hoe het eruit moet zien:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Zie [Azure AD-wachtwoordhashsynchronisatie, -versleuteling en FIPS-naleving voor](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)informatie over beveiliging en FIPS.

## <a name="troubleshoot-password-hash-synchronization"></a>Problemen met wachtwoordhashsynchronisatie oplossen
Zie Problemen met [wachtwoordhashsynchronisatie](tshoot-connect-password-hash-synchronization.md)oplossen als u problemen hebt met synchronisatie van wachtwoordhash.

## <a name="next-steps"></a>Volgende stappen
* [Synchronisatie van Azure AD Connect: synchronisatieopties aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
* [Ontvang een stapsgewijs implementatieplan voor het migreren van ADFS naar Wachtwoordhashsynchronisatie](https://aka.ms/authenticationDeploymentPlan)
