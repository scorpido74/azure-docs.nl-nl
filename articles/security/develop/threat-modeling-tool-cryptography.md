---
title: Cryptografie - Microsoft Threat Modeling Tool - Azure | Microsoft Documenten
description: oplossingen voor bedreigingen die worden blootgesteld in de Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: c9116472af5b400ded0fea24f98b07bad9d9039b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728195"
---
# <a name="security-frame-cryptography--mitigations"></a>Beveiligingsframe: cryptografie | Mitigaties 

| Product/service | Artikel |
| --------------- | ------- |
| **Webtoepassing** | <ul><li>[Gebruik alleen goedgekeurde symmetrische blokcijfers en sleutellengtes](#cipher-length)</li><li>[Goedgekeurde blokversleutelingsmodi en initialisatievectoren gebruiken voor symmetrische cijfers](#vector-ciphers)</li><li>[Gebruik goedgekeurde asymmetrische algoritmen, sleutellengtes en opvulling](#padding)</li><li>[Gebruik goedgekeurde random number generators](#numgen)</li><li>[Gebruik geen symmetrische stroomcijfers](#stream-ciphers)</li><li>[Goedgekeurde MAC/HMAC/ingetoetste hash-algoritmen gebruiken](#mac-hash)</li><li>[Alleen goedgekeurde cryptografische hashfuncties gebruiken](#hash-functions)</li></ul> |
| **Database** | <ul><li>[Sterke versleutelingsalgoritmen gebruiken om gegevens in de database te versleutelen](#strong-db)</li><li>[SSIS-pakketten moeten worden versleuteld en digitaal ondertekend](#ssis-signed)</li><li>[Digitale handtekening toevoegen aan kritieke databasesecurables](#securables-db)</li><li>[SQL server EKM gebruiken om versleutelingssleutels te beveiligen](#ekm-keys)</li><li>[AlwaysEncrypted-functie gebruiken als versleutelingssleutels niet aan de databaseengine mogen worden onthuld](#keys-engine)</li></ul> |
| **IoT-apparaat** | <ul><li>[Cryptografische sleutels veilig opslaan op IoT-apparaat](#keys-iot)</li></ul> | 
| **IoT Cloud Gateway** | <ul><li>[Een willekeurige symmetrische sleutel van voldoende lengte genereren voor verificatie naar IoT Hub](#random-hub)</li></ul> | 
| **Dynamics CRM Mobile-client** | <ul><li>[Zorg ervoor dat er een apparaatbeheerbeleid is dat een pincode voor gebruik vereist en het op afstand afvegen mogelijk maakt](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook-client** | <ul><li>[Zorg ervoor dat er een apparaatbeheerbeleid is ingevoerd waarvoor een pincode/wachtwoord/automatisch slot vereist is en dat alle gegevens worden versleuteld (bijvoorbeeld BitLocker)](#bitlocker)</li></ul> | 
| **Identiteitsserver** | <ul><li>[Controleren of tekensleutels worden omgerold bij het gebruik van Identity Server](#rolled-server)</li><li>[Zorg ervoor dat cryptografisch sterke client-ID, clientgeheim worden gebruikt in Identity Server](#client-server)</li></ul> | 

## <a name="use-only-approved-symmetric-block-ciphers-and-key-lengths"></a><a id="cipher-length"></a>Gebruik alleen goedgekeurde symmetrische blokcijfers en sleutellengtes

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Producten mogen alleen gebruik maken van symmetrische blokcijfers en bijbehorende sleutellengtes die expliciet zijn goedgekeurd door de Crypto-adviseur in uw organisatie. Goedgekeurde symmetrische algoritmen bij Microsoft bevatten de volgende blokcijfers:</p><ul><li>Voor de nieuwe code AES-128 zijn AES-192 en AES-256 aanvaardbaar</li><li>Voor achterwaartse compatibiliteit met bestaande code is 3DES met drie toetsen aanvaardbaar</li><li>Voor producten die symmetrische blokcijfers gebruiken:<ul><li>Advanced Encryption Standard (AES) is vereist voor nieuwe code</li><li>Drie-toets triple Data Encryption Standard (3DES) is toegestaan in bestaande code voor achterwaartse compatibiliteit</li><li>Alle andere blokcijfers, waaronder RC2, DES, 2 Key 3DES, DESX en Skipjack, mogen alleen worden gebruikt voor het decoderen van oude gegevens en moeten worden vervangen als ze worden gebruikt voor versleuteling</li></ul></li><li>Voor symmetrische blokversleutelingsalgoritmen is een minimale sleutellengte van 128 bits vereist. Het enige blok encryptie algoritme aanbevolen voor nieuwe code is AES (AES-128, AES-192 en AES-256 zijn allemaal aanvaardbaar)</li><li>Drie-toets 3DES is momenteel aanvaardbaar als al in gebruik in bestaande code; overgang naar AES wordt aanbevolen. DES, DESX, RC2 en Skipjack worden niet langer als veilig beschouwd. Deze algoritmen mogen alleen worden gebruikt voor het decoderen van bestaande gegevens omwille van achterwaartse compatibiliteit, en gegevens moeten opnieuw worden versleuteld met behulp van een aanbevolen blok cipher</li></ul><p>Houd er rekening mee dat alle symmetrische blokcijfers moeten worden gebruikt met een goedgekeurde versleutelingsmodus, waarvoor gebruik moet worden gemaakt van een geschikte initialisatievector (IV). Een geschikte IV, is meestal een willekeurig getal en nooit een constante waarde</p><p>Het gebruik van verouderde of anderszins niet-goedgekeurde crypto-algoritmen en kleinere sleutellengtes voor het lezen van bestaande gegevens (in tegenstelling tot het schrijven van nieuwe gegevens) kan worden toegestaan na de crypto-beoordeling van uw organisatie. U moet echter een uitzondering indienen voor deze vereiste. Bovendien moeten producten bij bedrijfsimplementaties rekening houden met waarschuwingsbeheerders wanneer zwakke crypto wordt gebruikt om gegevens te lezen. Dergelijke waarschuwingen moeten verklarend en uitvoerbaar zijn. In sommige gevallen kan het passend zijn om groepsbeleid het gebruik van zwakke crypto</p><p>Toegestane .NET-algoritmen voor beheerde crypto-flexibiliteit (in volgorde van voorkeur)</p><ul><li>AesCng (FIPS compliant)</li><li>AuthenticdAesCng (FIPS-compatibel)</li><li>AESCryptoServiceProvider (FIPS compliant)</li><li>AESManaged (niet-FIPS-compatibel)</li></ul><p>Houd er rekening mee dat geen van `SymmetricAlgorithm.Create` `CryptoConfig.CreateFromName` deze algoritmen kan worden opgegeven via de of methoden zonder wijzigingen aan te brengen in het bestand machine.config. Houd er ook rekening mee dat AES in versies van `RijndaelManaged`.NET voorafgaand aan .NET 3.5 de naam heeft en `AesCng` >beschikbaar `AuthenticatedAesCng` zijn via CodePlex en CNG in het onderliggende besturingssysteem vereist</p>

## <a name="use-approved-block-cipher-modes-and-initialization-vectors-for-symmetric-ciphers"></a><a id="vector-ciphers"></a>Goedgekeurde blokversleutelingsmodi en initialisatievectoren gebruiken voor symmetrische cijfers

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Alle symmetrische blokcijfers moeten worden gebruikt met een goedgekeurde symmetrische cijfermodus. De enige goedgekeurde modi zijn CBC en CTS. In het bijzonder moet de wijze van werking van het elektronische codeboek (ECB) worden vermeden; het gebruik van ecb vereist de Crypto Board review van uw organisatie. Al het gebruik van OFB, CFB, CTR, CCM en GCM of een andere versleutelingsmodus moet worden beoordeeld door het Crypto-bord van uw organisatie. Hergebruik van dezelfde initialisatievector (IV) met blokcijfers in 'streaming ciphers modes', zoals CTR, kan ervoor zorgen dat versleutelde gegevens worden onthuld. Alle symmetrische blokcijfers moeten ook worden gebruikt met een geschikte initialisatievector (IV). Een geschikte IV is een cryptografisch sterk, willekeurig getal en nooit een constante waarde. |

## <a name="use-approved-asymmetric-algorithms-key-lengths-and-padding"></a><a id="padding"></a>Gebruik goedgekeurde asymmetrische algoritmen, sleutellengtes en opvulling

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Het gebruik van verboden cryptografische algoritmen brengt een aanzienlijk risico voor de productveiligheid met zich mee en moet worden vermeden. Producten mogen alleen gebruik maken van die cryptografische algoritmen en bijbehorende sleutellengtes en opvullingen die expliciet zijn goedgekeurd door het Crypto-bord van uw organisatie.</p><ul><li>**RSA-** kan worden gebruikt voor encryptie, sleuteluitwisseling en handtekening. RSA-versleuteling mag alleen de opvullingsmodi OAEP of RSA-KEM gebruiken. Bestaande code kan PKCS #1 v1.5-opvullingsmodus alleen gebruiken voor compatibiliteit. Het gebruik van null padding is expliciet verboden. Sleutels >= 2048 bits is vereist voor nieuwe code. Bestaande code kan sleutels < 2048 bits alleen ondersteunen voor achterwaartse compatibiliteit na een beoordeling door het Crypto-bord van uw organisatie. Sleutels < 1024 bits mogen alleen worden gebruikt voor het decoderen/ verifiëren van oude gegevens en moeten worden vervangen als ze worden gebruikt voor versleuteling of ondertekeningsbewerkingen</li><li>**ECDSA-** mag alleen worden gebruikt voor handtekening. ECDSA met >=256-bits toetsen is vereist voor nieuwe code. Handtekeningen op basis van eCDSA moeten een van de drie door de NIST goedgekeurde curven gebruiken (P-256, P-384 of P521). Curven die grondig zijn geanalyseerd, kunnen alleen worden gebruikt na een beoordeling met het Crypto-bord van uw organisatie.</li><li>**ECDH-** mag alleen worden gebruikt voor sleuteluitwisseling. ECDH met >=256-bits toetsen is vereist voor nieuwe code. Ecdh-gebaseerde sleuteluitwisseling moet gebruik maken van een van de drie NIST goedgekeurde curven (P-256, P-384, of P521). Curven die grondig zijn geanalyseerd, kunnen alleen worden gebruikt na een beoordeling met het Crypto-bord van uw organisatie.</li><li>**DSA-** kan aanvaardbaar zijn na beoordeling en goedkeuring van het Crypto-bord van uw organisatie. Neem contact op met uw beveiligingsadviseur om de Crypto Board review van uw organisatie te plannen. Als uw gebruik van DSA is goedgekeurd, moet u het gebruik van sleutels met een lengte van minder dan 2048 bits verbieden. CNG ondersteunt 2048-bits en grotere sleutellengtes vanaf Windows 8.</li><li>**Diffie-Hellman-** kan alleen worden gebruikt voor sessiesleutelbeheer. Sleutellengte >= 2048 bits is vereist voor nieuwe code. Bestaande code kan belangrijke lengtes < 2048 bits alleen ondersteunen voor achterwaartse compatibiliteit na een beoordeling door het Crypto-bord van uw organisatie. Toetsen < 1024 bits mogen niet worden gebruikt.</li><ul>

## <a name="use-approved-random-number-generators"></a><a id="numgen"></a>Gebruik goedgekeurde random number generators

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Producten moeten gebruik maken van goedgekeurde random number generators. Pseudorandom functies zoals de C runtime functie rand, de .NET Framework class System.Random, of systeemfuncties zoals GetTickCount mag daarom nooit worden gebruikt in een dergelijke code. Gebruik van de dubbele elliptische curve random number generator (DUAL_EC_DRBG) algoritme is verboden</p><ul><li>**CNG-** BCryptGenRandom (gebruik van de aanbevolen BCRYPT_USE_SYSTEM_PREFERRED_RNG vlag, tenzij de beller kan worden uitgevoerd op een IRQL groter dan 0 [dat wil zeggen, PASSIVE_LEVEL])</li><li>**CAPI-** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (nieuwe implementaties moeten BCryptGenRandom of CryptGenRandom gebruiken) * rand_s * SystemPrng (voor kernelmodus)</li><li>**. NET-** RNGCryptoServiceProvider of RNGCng</li><li>**Windows Store-apps-** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom of . GenerateRandomNumber</li><li>**Apple OS X (10,7+)/iOS(2.0+)-** int SecRandomCopyBytes (SecRandomRef \*random, size_t count, uint8_t bytes)</li><li>**Apple OS X (<10,7)-** Gebruik /dev/random om willekeurige getallen op te halen</li><li>**Java(inclusief Google Android Java code)-** java.security.SecureRandom klasse. Merk op dat voor Android 4.3 (Jelly Bean), ontwikkelaars moeten volgen de Android aanbevolen tijdelijke oplossing en hun toepassingen bijwerken om expliciet te initialiseren de PRNG met entropie van / dev / urandom of / dev / random</li></ul>|

## <a name="do-not-use-symmetric-stream-ciphers"></a><a id="stream-ciphers"></a>Gebruik geen symmetrische stroomcijfers

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Symmetrische stroomcijfers, zoals RC4, mogen niet worden gebruikt. In plaats van symmetrische stroomcijfers, moeten producten een blokcijfer gebruiken, met name AES met een sleutellengte van ten minste 128 bits. |

## <a name="use-approved-machmackeyed-hash-algorithms"></a><a id="mac-hash"></a>Goedgekeurde MAC/HMAC/ingetoetste hash-algoritmen gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Producten mogen alleen mac-algoritmen (approved message authentication code) of hash-based message authentication code (HMAC) gebruiken.</p><p>Een berichtverificatiecode (MAC) is een stukje informatie dat is gekoppeld aan een bericht waarmee de ontvanger zowel de authenticiteit van de afzender als de integriteit van het bericht kan verifiëren met behulp van een geheime sleutel. Het gebruik van een op hash gebaseerde MAC[(HMAC)](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)of [op block-cipher gebaseerde MAC](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) is toegestaan zolang alle onderliggende hash- of symmetrische versleutelingsalgoritmen ook zijn goedgekeurd voor gebruik; momenteel omvat dit de HMAC-SHA2 functies (HMAC-SHA256, HMAC-SHA384 en HMAC-SHA512) en de CMAC/OMAC1 en OMAC2 block cipher-based MACs (deze zijn gebaseerd op AES).</p><p>Het gebruik van HMAC-SHA1 is mogelijk toegestaan voor platformcompatibiliteit, maar u moet een uitzondering op deze procedure indienen en de Crypto-beoordeling van uw organisatie ondergaan. Truncation van HMACs tot minder dan 128 bits is niet toegestaan. Het gebruik van klantmethoden om een sleutel te hasjen en gegevens worden niet goedgekeurd en moeten de Crypto-boardbeoordeling van uw organisatie ondergaan voordat deze worden gebruikt.</p>|

## <a name="use-only-approved-cryptographic-hash-functions"></a><a id="hash-functions"></a>Alleen goedgekeurde cryptografische hashfuncties gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Producten moeten gebruik maken van de SHA-2-familie van hash-algoritmen (SHA256, SHA384 en SHA512). Als een kortere hash nodig is, zoals een 128-bits uitvoerlengte om te passen bij een gegevensstructuur die is ontworpen met de kortere MD5-hash in het achterhoofd, kunnen productteams een van de SHA2-hashes (meestal SHA256) afgekapt. Houd er rekening mee dat SHA384 een afgekapte versie van SHA512 is. Het afkappen van cryptografische hashes voor beveiligingsdoeleinden tot minder dan 128 bits is niet toegestaan. Nieuwe code mag geen gebruik maken van de MD2-, MD4-, MD5-, SHA-0-, SHA-1- of RIPEMD-hashalgoritmen. Hash-botsingen zijn computationeel haalbaar voor deze algoritmen, waardoor ze effectief worden gebroken.</p><p>Toegestane .NET hash-algoritmen voor beheerde crypto-flexibiliteit (in volgorde van voorkeur):</p><ul><li>SHA512Cng (FIPS compliant)</li><li>SHA384Cng (FIPS compliant)</li><li>SHA256Cng (FIPS compliant)</li><li>SHA512Managed (niet-FIPS-compatibel) (gebruik SHA512 als algoritmenaam in aanroepen naar HashAlgorithm.Create of CryptoConfig.CreateFromName)</li><li>SHA384Managed (niet-FIPS-compatibel) (gebruik SHA384 als algoritmenaam in aanroepen naar HashAlgorithm.Create of CryptoConfig.CreateFromName)</li><li>SHA256Managed (niet-FIPS-compatibel) (gebruik SHA256 als algoritmenaam in aanroepen naar HashAlgorithm.Create of CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (FIPS compliant)</li><li>SHA256CryptoServiceProvider (FIPS compliant)</li><li>SHA384CryptoServiceProvider (FIPS compliant)</li></ul>| 

## <a name="use-strong-encryption-algorithms-to-encrypt-data-in-the-database"></a><a id="strong-db"></a>Sterke versleutelingsalgoritmen gebruiken om gegevens in de database te versleutelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Een versleutelingsalgoritme kiezen](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Stappen** | Versleutelingsalgoritmen definiëren gegevenstransformaties die niet gemakkelijk kunnen worden teruggedraaid door onbevoegde gebruikers. SQL Server stelt beheerders en ontwikkelaars in staat om uit verschillende algoritmen te kiezen, waaronder DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bits RC4, DESX, 128-bits AES, 192-bits AES en 256-bits AES |

## <a name="ssis-packages-should-be-encrypted-and-digitally-signed"></a><a id="ssis-signed"></a>SSIS-pakketten moeten worden versleuteld en digitaal ondertekend

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [De bron van pakketten identificeren met digitale handtekeningen,](https://msdn.microsoft.com/library/ms141174.aspx) [bedreigings- en kwetsbaarheidsbeperking (integratieservices)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Stappen** | De bron van een pakket is de persoon of organisatie die het pakket heeft gemaakt. Het uitvoeren van een pakket van een onbekende of niet-vertrouwde bron kan riskant zijn. Om ongeoorloofde manipulatie van SSIS-pakketten te voorkomen, moeten digitale handtekeningen worden gebruikt. Om de vertrouwelijkheid van de pakketten tijdens opslag/doorvoer te waarborgen, moeten SSIS-pakketten ook worden versleuteld |

## <a name="add-digital-signature-to-critical-database-securables"></a><a id="securables-db"></a>Digitale handtekening toevoegen aan kritieke databasesecurables

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [HANDTEKENING TOEVOEGEN (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Stappen** | In gevallen waarin de integriteit van een kritieke database moet worden geverifieerd, moeten digitale handtekeningen worden gebruikt. Databasesecurables zoals een opgeslagen procedure, functie, assemblage of trigger kunnen digitaal worden ondertekend. Hieronder vindt u een voorbeeld van wanneer dit nuttig kan zijn: Laten we zeggen dat een ISV (Independent Software Vendor) ondersteuning heeft geboden aan een software die aan een van hun klanten wordt geleverd. Alvorens ondersteuning te bieden, zou de ISV ervoor willen zorgen dat een database securable in de software niet per ongeluk of door een kwaadaardige poging is geknoeid. Als de securable digitaal is ondertekend, kan de ISV zijn digitale handtekening verifiëren en de integriteit ervan valideren.| 

## <a name="use-sql-server-ekm-to-protect-encryption-keys"></a><a id="ekm-keys"></a>SQL server EKM gebruiken om versleutelingssleutels te beveiligen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [SQL Server Extensible Key Management (EKM),](https://msdn.microsoft.com/library/bb895340) [Extensible Key Management Using Azure Key Vault (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Stappen** | SQL Server Extensible Key Management maakt het mogelijk de versleutelingssleutels die de databasebestanden beschermen, op te slaan in een off-box apparaat, zoals een smartcard, USB-apparaat of EKM/HSM-module. Dit maakt ook gegevensbescherming mogelijk tegen databasebeheerders (behalve leden van de sysadmin-groep). Gegevens kunnen worden versleuteld met behulp van encryptiesleutels waartoe alleen de databasegebruiker toegang heeft op de externe EKM/HSM-module. |

## <a name="use-alwaysencrypted-feature-if-encryption-keys-should-not-be-revealed-to-database-engine"></a><a id="keys-engine"></a>AlwaysEncrypted-functie gebruiken als versleutelingssleutels niet aan de databaseengine mogen worden onthuld

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | SQL Azure, OnPrem |
| **Kenmerken**              | SQL-versie - V12, MsSQL2016 |
| **Verwijzingen**              | [Altijd versleuteld (databaseengine)](https://msdn.microsoft.com/library/mt163865) |
| **Stappen** | Always Encrypted is een functie die is ontworpen om gevoelige gegevens te beschermen, zoals creditcardnummers of nationale identificatienummers (bijvoorbeeld Amerikaanse burgerservicenummers), opgeslagen in Azure SQL Database- of SQL Server-databases. Always Encrypted stelt clients in staat om gevoelige gegevens in clienttoepassingen te versleutelen en nooit de versleutelingssleutels aan de Database Engine (SQL Database of SQL Server) te onthullen. Als gevolg hiervan biedt Always Encrypted een scheiding tussen degenen die eigenaar zijn van de gegevens (en deze kunnen bekijken) en degenen die de gegevens beheren (maar geen toegang moeten hebben) |

## <a name="store-cryptographic-keys-securely-on-iot-device"></a><a id="keys-iot"></a>Cryptografische sleutels veilig opslaan op IoT-apparaat

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-apparaat | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Apparaatbesturingssysteem - Windows IoT Core, apparaatconnectiviteit - Azure IoT-apparaat SDKs |
| **Verwijzingen**              | [TPM op Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [TPM instellen op Windows IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), [Azure IoT Device SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Stappen** | Symmetrische of Certificaat Privésleutels veilig in een hardwarebeveiligde opslag zoals TPM- of Smart Card-chips. Windows 10 IoT Core ondersteunt de gebruiker van een TPM en er https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpmzijn verschillende compatibele TPM's die kunnen worden gebruikt: . Het wordt aanbevolen om een firmware of discrete TPM te gebruiken. Een Software TPM mag alleen worden gebruikt voor ontwikkelings- en testdoeleinden. Zodra een TPM beschikbaar is en de sleutels erin zijn ingericht, moet de code die het token genereert worden geschreven zonder dat er gevoelige informatie in staat. | 

### <a name="example"></a>Voorbeeld
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Zoals kan worden gezien, is de primaire sleutel van het apparaat niet aanwezig in de code. In plaats daarvan wordt het opgeslagen in de TPM op sleuf 0. TPM-apparaat genereert een kortlevend SAS-token dat vervolgens wordt gebruikt om verbinding te maken met de IoT-hub. 

## <a name="generate-a-random-symmetric-key-of-sufficient-length-for-authentication-to-iot-hub"></a><a id="random-hub"></a>Een willekeurige symmetrische sleutel van voldoende lengte genereren voor verificatie naar IoT Hub

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT Cloud Gateway | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Keuze voor gateway - Azure IoT Hub |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | IoT Hub bevat een apparaat identiteitsregister en tijdens het inrichten van een apparaat, genereert automatisch een willekeurige symmetrische sleutel. Het wordt aanbevolen om deze functie van het Azure IoT Hub Identity Registry te gebruiken om de sleutel te genereren die wordt gebruikt voor verificatie. IoT Hub maakt het ook mogelijk om een sleutel te specificeren tijdens het maken van het apparaat. Als een sleutel buiten IoT Hub wordt gegenereerd tijdens het inrichten van het apparaat, wordt aanbevolen om een willekeurige symmetrische toets of ten minste 256 bits te maken. |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-use-pin-and-allows-remote-wiping"></a><a id="pin-remote"></a>Zorg ervoor dat er een apparaatbeheerbeleid is dat een pincode voor gebruik vereist en het op afstand afvegen mogelijk maakt

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Dynamics CRM Mobile-client | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Zorg ervoor dat er een apparaatbeheerbeleid is dat een pincode voor gebruik vereist en het op afstand afvegen mogelijk maakt |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-pinpasswordauto-lock-and-encrypts-all-data-eg-bitlocker"></a><a id="bitlocker"></a>Zorg ervoor dat er een apparaatbeheerbeleid is ingevoerd waarvoor een pincode/wachtwoord/automatisch slot vereist is en dat alle gegevens worden versleuteld (bijvoorbeeld BitLocker)

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Dynamics CRM Outlook-client | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Zorg ervoor dat er een apparaatbeheerbeleid is ingevoerd waarvoor een pincode/wachtwoord/automatisch slot vereist is en dat alle gegevens worden versleuteld (bijvoorbeeld BitLocker) |

## <a name="ensure-that-signing-keys-are-rolled-over-when-using-identity-server"></a><a id="rolled-server"></a>Controleren of tekensleutels worden omgerold bij het gebruik van Identity Server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Identiteitsserver | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Identity Server - Sleutels, handtekeningen en cryptografie](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Stappen** | Controleer of tekensleutels worden omgerold wanneer u Identity Server gebruikt. In de koppeling in de sectie Verwijzingen wordt uitgelegd hoe dit moet worden gepland zonder dat er onderbrekingen optreden voor toepassingen die afhankelijk zijn van Identity Server. |

## <a name="ensure-that-cryptographically-strong-client-id-client-secret-are-used-in-identity-server"></a><a id="client-server"></a>Zorg ervoor dat cryptografisch sterke client-ID, clientgeheim worden gebruikt in Identity Server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Identiteitsserver | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Zorg ervoor dat cryptografisch sterke client-ID, clientgeheim worden gebruikt in Identity Server. De volgende richtlijnen moeten worden gebruikt tijdens het genereren van een client-ID en geheim:</p><ul><li>Een willekeurige GUID genereren als client-id</li><li>Genereer een cryptografisch willekeurige 256-bits sleutel als het geheim</li></ul>|
