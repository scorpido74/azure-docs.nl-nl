---
title: Met HSM beveiligde sleutels genereren en overdragen voor Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Gebruik dit artikel om u te helpen bij het plannen, genereren en overdragen van uw eigen met HSM beveiligde sleutels voor gebruik met Azure Key Vault. Ook wel bekend als BYOK of uw eigen sleutel nemen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 0d51e1aaf1d9b0472245b9a7f29148517a0a7b93
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429304"
---
# <a name="import-hsm-protected-keys-for-key-vault-legacy"></a>Met HSM beveiligde sleutels voor Key Vault importeren (verouderd)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor extra zekerheid kunt u, wanneer u Azure Key Vault gebruikt, sleutels importeren of genereren in Hardware Security modules (Hsm's) die de HSM-grens nooit verlaten. Dit scenario wordt vaak *uw eigen sleutel*of BYOK genoemd. Azure Key Vault maakt gebruik van nCipher nShield-familie van Hsm's (FIPS 140-2 level 2 gevalideerd) om uw sleutels te beveiligen.

Gebruik de informatie in dit onderwerp om u te helpen bij het plannen, genereren en overdragen van uw eigen met HSM beveiligde sleutels voor gebruik met Azure Key Vault.

Deze functionaliteit is niet beschikbaar voor Azure China.

> [!NOTE]
> Zie [Wat is Azure Key Vault?](key-vault-overview.md) voor meer informatie over Azure Key Vault.  
> Zie [Wat is Azure Key Vault?](key-vault-overview.md)voor een zelf studie waarmee u aan de slag kunt gaan met het maken van een sleutel kluis voor met HSM beschermde sleutels.

Meer informatie over het genereren en overdragen van een met HSM beschermde sleutel via internet:

* U genereert de sleutel van een offline werk station, waardoor de kwets baarheid wordt verminderd.
* De sleutel is versleuteld met een sleutel uitwisselings sleutel (KEK), die versleuteld blijft totdat deze wordt overgedragen naar de Azure Key Vault Hsm's. Alleen de versleutelde versie van uw sleutel laat het oorspronkelijke werk station ongewijzigd.
* De toolset stelt eigenschappen in voor uw Tenant sleutel die uw sleutel verbindt met de Azure Key Vault beveiligings wereld. Nadat de Azure Key Vault Hsm's de sleutel heeft ontvangen en ontsleuteld, kan alleen deze Hsm's deze gebruiken. De sleutel kan niet worden geëxporteerd. Deze binding wordt afgedwongen door de nCipher Hsm's.
* De sleutel uitwisselings sleutel (KEK) die wordt gebruikt om uw sleutel te versleutelen, wordt gegenereerd in de Azure Key Vault Hsm's en kan niet worden geëxporteerd. De Hsm's dwingt af dat er geen duidelijke versie van de KEK buiten de Hsm's kan worden uitgevoerd. Daarnaast bevat de toolset Attestation van nCipher dat de KEK niet exporteerbaar is en is gegenereerd in een echte HSM die is gefabriceerd door nCipher.
* De toolset bevat Attestation van nCipher dat de Azure Key Vault Security-wereld ook is gegenereerd op basis van een echte HSM die is gefabriceerd door nCipher. Deze verklaring bewijst dat micro soft legitieme hardware gebruikt.
* Micro soft maakt gebruik van afzonderlijke KEKs en afzonderlijke beveiligings werelden in elke geografische regio. Deze schei ding zorgt ervoor dat uw sleutel alleen kan worden gebruikt in data centers in de regio waarin u deze hebt versleuteld. Een sleutel van een Europese klant kan bijvoorbeeld niet worden gebruikt in data centers in Noord-Amerika of Azië.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Meer informatie over nCipher Hsm's en micro soft-Services

nCipher-beveiliging, een Entrust Datacard bedrijf, is een leider op de HSM-markt voor algemeen gebruik, die wereld wijde organisaties biedt door vertrouwen, integriteit en controle te leveren aan hun bedrijfs kritieke informatie en toepassingen. de cryptografische oplossingen van nCipher beveiligen opkomende technologieën – Cloud, IoT, Block Chain, digitale betalingen, en helpen om te voldoen aan nieuwe nalevings mandaten, met behulp van dezelfde bewezen beproefde technologie waar wereld wijde organisaties hun gevoelige gegevens, netwerk communicatie en bedrijfs infrastructuur. nCipher zorgt voor een vertrouwen voor bedrijfskritische toepassingen, waardoor de integriteit van gegevens en klanten te allen tijde volledig kunnen worden beheerd.

Micro soft heeft samengewerkt met nCipher-beveiliging om de status van Art voor Hsm's te verbeteren. Deze verbeteringen bieden u de mogelijkheid om de typische voor delen van gehoste services te krijgen zonder dat u de verliest controle over uw sleutels hoeft te nemen. Met deze verbeteringen kan micro soft de Hsm's beheren, zodat u dit niet hoeft te doen. Als Cloud service Azure Key Vault zo snel omhoog geschaald om te voldoen aan de gebruiks pieken van uw organisatie. Op hetzelfde moment wordt uw sleutel beschermd in de Hsm's van micro soft: u behoudt de controle over de levens cyclus van de sleutel, omdat u de sleutel genereert en deze overdraagt naar de Hsm's van micro soft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementatie van uw eigen sleutel (BYOK) voor Azure Key Vault

Gebruik de volgende informatie en procedures als u uw eigen met HSM beveiligde sleutel wilt genereren en deze vervolgens wilt overzetten naar Azure Key Vault: het BYOK-scenario (uw eigen sleutel).

## <a name="prerequisites-for-byok"></a>Vereisten voor BYOK

Zie de volgende tabel voor een lijst met vereisten voor het nemen van uw eigen sleutel (BYOK) voor Azure Key Vault.

| Vereiste | Meer informatie |
| --- | --- |
| Een abonnement op Azure |Als u een Azure Key Vault wilt maken, hebt u een Azure-abonnement nodig: Meld u aan [voor een gratis proef versie](https://azure.microsoft.com/pricing/free-trial/) |
| De Azure Key Vault Premium-servicelaag ter ondersteuning van met HSM beveiligde sleutels |Zie de [Azure Key Vault-prijs](https://azure.microsoft.com/pricing/details/key-vault/) website voor meer informatie over de service lagen en mogelijkheden voor Azure Key Vault. |
| nCipher nShield Hsm's, Smart Cards en ondersteunings software |U moet toegang hebben tot een nCipher Hardware Security module en basis kennis van nCipher nShield Hsm's. Zie [NCipher NShield Hardware Security module](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) voor de lijst met compatibele modellen of als u een HSM wilt kopen als u deze niet hebt. |
| De volgende hardware en software:<ol><li>Een offline x64-werk station met een mini maal Windows-besturings systeem van Windows 7 en nCipher nShield-software van ten minste versie 11,50.<br/><br/>Als op dit werk station Windows 7 wordt uitgevoerd, moet u [Microsoft .NET Framework 4,5 installeren](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Een werk station dat is verbonden met internet en een mini maal Windows-besturings systeem van Windows 7 en [Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) **Mini maal geïnstalleerde versie 1.1.0** geïnstalleerd.</li><li>Een USB-station of ander draagbaar opslag apparaat met ten minste 16 MB beschik bare ruimte.</li></ol> |Uit veiligheids overwegingen raden we u aan het eerste werk station niet met een netwerk te verbinden. Deze aanbeveling wordt echter niet programmatisch afgedwongen.<br/><br/>In de volgende instructies wordt dit werk station aangeduid als het niet-verbonden werk station.</p></blockquote><br/>Als uw Tenant sleutel voor een productie netwerk is, raden we u aan een tweede, afzonderlijk werk station te gebruiken om de hulpprogram ma's te downloaden en de Tenant sleutel te uploaden. Maar voor test doeleinden kunt u hetzelfde werk station gebruiken als het eerste.<br/><br/>In de volgende instructies wordt dit tweede werk station aangeduid als het met internet verbonden werk station.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Uw sleutel genereren en overdragen naar Azure Key Vault HSM

U gebruikt de volgende vijf stappen voor het genereren en overdragen van uw sleutel naar een Azure Key Vault HSM:

* [Stap 1: uw werk station met Internet verbinding voorbereiden](#step-1-prepare-your-internet-connected-workstation)
* [Stap 2: het niet-verbonden werk station voorbereiden](#step-2-prepare-your-disconnected-workstation)
* [Stap 3: uw sleutel genereren](#step-3-generate-your-key)
* [Stap 4: uw sleutel voorbereiden voor overdracht](#step-4-prepare-your-key-for-transfer)
* [Stap 5: uw sleutel overdragen naar Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Stap 1: uw werk station met Internet verbinding voorbereiden

Voer voor deze eerste stap de volgende procedures uit op uw werk station dat is verbonden met internet.

### <a name="step-11-install-azure-powershell"></a>Stap 1,1: Installeer Azure PowerShell

Op het met internet verbonden werk station downloadt en installeert u de Azure PowerShell module met de-cmdlets voor het beheren van Azure Key Vault. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview)voor installatie-instructies.

### <a name="step-12-get-your-azure-subscription-id"></a>Stap 1,2: uw Azure-abonnements-ID ophalen

Start een Azure PowerShell-sessie en meld u aan bij uw Azure-account met behulp van de volgende opdracht:

```Powershell
   Connect-AzAccount
```
Voer in het pop-upvenster in de browser uw gebruikersnaam en wachtwoord voor uw Azure-account in. Gebruik vervolgens de opdracht [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) :

```powershell
   Get-AzSubscription
```
Ga vanuit de uitvoer naar de ID van het abonnement dat u wilt gebruiken voor Azure Key Vault. U hebt deze abonnements-ID later nodig.

Sluit het Azure PowerShell-venster niet.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Stap 1,3: down load de BYOK-toolset voor Azure Key Vault

Ga naar het micro soft Download centrum en [down load de Azure Key Vault BYOK-toolset](https://www.microsoft.com/download/details.aspx?id=45345) voor uw geografische regio of Azure-exemplaar. Gebruik de volgende informatie om de pakket naam te identificeren die u wilt downloaden en de bijbehorende SHA-256-pakket-hash:

---
**Verenigde Staten:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Europa**

KeyVault-BYOK-Tools-Europe. zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Asia**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Latijns-Amerika:**

KeyVault-BYOK-Tools-LatinAmerica. zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Japan:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Korea:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**Zuid-Afrika:**

KeyVault-BYOK-Tools-SouthAfrica. zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**VAE**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Australië:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure Government:** ](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**Amerikaanse overheid DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Canada:**

KeyVault-BYOK-Tools-Canada. zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Duitsland:**

KeyVault-BYOK-Tools-Germany. zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Open bare Duitsland:**

KeyVault-BYOK-Tools-Germany-Public. zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**India:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Frankrijk:**

KeyVault-BYOK-Tools-France. zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Verenigd Koninkrijk:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Zwitserland:**

KeyVault-BYOK-Tools-Switzerland. zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


Gebruik de cmdlet [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) om de integriteit van uw gedownloade BYOK-toolset te valideren vanuit uw Azure PowerShell-sessie.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

De toolset bevat:

* Een KEK-pakket (Key Exchange Key) met een naam die begint met **BYOK-Kek-pak-.**
* Een beveiligings wereld pakket met een naam die begint met **BYOK-SecurityWorld-pak-.**
* Een python-script met de naam **verifykeypackage.py.**
* Een uitvoerbaar opdracht regel bestand met de naam **KeyTransferRemote. exe** en de bijbehorende DLL-bestanden.
* Een Visual C++ Redistributable-pakket met de naam **vcredist_x64. exe.**

Kopieer het pakket naar een USB-station of andere draag bare opslag.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Stap 2: het niet-verbonden werk station voorbereiden

Voer voor deze tweede stap de volgende procedures uit op het werk station dat niet is verbonden met een netwerk (Internet of uw interne netwerk).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Stap 2,1: de niet-verbonden werk station voorbereiden met nCipher nShield HSM

Installeer de nCipher-ondersteunings software op een Windows-computer en koppel vervolgens een nCipher nShield HSM aan die computer.

Zorg ervoor dat de nCipher-hulpprogram ma's zich in het pad bevinden ( **% nfast_home% \ bin**). Typ bijvoorbeeld het volgende:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Zie de gebruikers handleiding die is opgenomen in de nShield HSM voor meer informatie.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Stap 2,2: Installeer de BYOK-toolset op het niet-verbonden werk station

Kopieer het pakket met de BYOK-hulpprogram ma's van het USB-station of andere draag bare opslag en Ga als volgt te werk:

1. Pak de bestanden uit het gedownloade pakket naar een wille keurige map.
2. Voer vcredist_x64. exe uit in de map.
3. Volg de instructies voor het installeren van de C++ Visual runtime-onderdelen voor visual studio 2013.

## <a name="step-3-generate-your-key"></a>Stap 3: uw sleutel genereren

Voer voor deze derde stap de volgende procedures uit op het niet-verbonden werk station. Om deze stap te volt ooien moet uw HSM zich in de initialisatie modus bevindt. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Stap 3,1: Wijzig de HSM-modus in ' I '

Als u nCipher nShield Edge gebruikt, wijzigt u de modus: 1. Gebruik de knop modus om de vereiste modus te markeren. 2. Binnen een paar seconden drukt u op de knop wissen en houdt u deze gedurende een paar seconden ingedrukt. Als de modus wordt gewijzigd, wordt de LED van de nieuwe modus niet meer knippert en blijft deze branden. De LED van de status kan een paar seconden onregelmatig knip peren en vervolgens regel matig knippert wanneer het apparaat klaar is. Als dat niet het geval is, blijft het apparaat in de huidige modus, met de juiste modus LED voor branden.

### <a name="step-32-create-a-security-world"></a>Stap 3,2: een beveiligings wereld maken

Start een opdracht prompt en voer het nCipher New-World-programma uit.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Dit programma maakt een **beveiligings wereld** bestand op% NFAST_KMDATA% \ local\world, dat overeenkomt met de map C:\ProgramData\nCipher\Key Management Data\local. U kunt verschillende waarden voor het quorum gebruiken, maar in het volgende voor beeld wordt u gevraagd om drie lege kaarten en pincodes voor elke waarde in te voeren. Vervolgens geven alle twee kaarten volledige toegang tot de beveiligings wereld. Deze kaarten worden de **beheerders kaart** die is ingesteld voor de nieuwe beveiligings wereld.

> [!NOTE]
> Als uw HSM de nieuwere coderings Suite-DLf3072s256mRijndael niet ondersteunt, kunt u--cipher-Suite = DLf3072s256mRijndael vervangen door--cipher-Suite = DLf1024s160mRijndael
> 
> De beveiligings wereld die is gemaakt met New-World. exe, die wordt geleverd met nCipher-software versie 12,50 is niet compatibel met deze BYOK-procedure. Er zijn twee opties beschikbaar:
> 1) Downgrade nCipher-software versie naar 12.40.2 om een nieuwe beveiligings wereld te maken.
> 2) Neem contact op met nCipher-ondersteuning en vraag hen om een hotfix te bieden voor 12,50-software versie, waarmee u 12.40.2-versie van New-World. exe kunt gebruiken die compatibel is met deze BYOK procedure.

Ga daarna als volgt te werk:

* Maak een back-up van het wereld bestand. Beveilig en Beveilig het wereld bestand, de beheerders kaarten en hun pincodes, en zorg ervoor dat er geen enkele persoon toegang tot meer dan één kaart heeft.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Stap 3,3: Wijzig de HSM-modus in ' O '

Als u nCipher nShield Edge gebruikt, wijzigt u de modus: 1. Gebruik de knop modus om de vereiste modus te markeren. 2. Binnen een paar seconden drukt u op de knop wissen en houdt u deze gedurende een paar seconden ingedrukt. Als de modus wordt gewijzigd, wordt de LED van de nieuwe modus niet meer knippert en blijft deze branden. De LED van de status kan een paar seconden onregelmatig knip peren en vervolgens regel matig knippert wanneer het apparaat klaar is. Als dat niet het geval is, blijft het apparaat in de huidige modus, met de juiste modus LED voor branden.

### <a name="step-34-validate-the-downloaded-package"></a>Stap 3,4: het gedownloade pakket valideren

Deze stap is optioneel, maar wordt aanbevolen, zodat u het volgende kunt valideren:

* De sleutel uitwisselings sleutel die is opgenomen in de toolset is gegenereerd op basis van een legitieme nCipher nShield HSM.
* De hash van de beveiligings wereld die is opgenomen in de toolset is gegenereerd in een legitieme nCipher nShield HSM.
* De sleutel uitwisselings sleutel kan niet worden geëxporteerd.

> [!NOTE]
> Voor het valideren van het gedownloade pakket moet de HSM zijn verbonden, ingeschakeld en moet deze een beveiligings wereld hebben (zoals de versie die u zojuist hebt gemaakt).

Het gedownloade pakket valideren:

1. Voer het script verifykeypackage.py uit door een van de volgende opties te typen, afhankelijk van uw geografische regio of Azure-exemplaar:

   * Voor Noord-Amerika:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Voor Europa:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Voor Azië:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Voor Latijns-Amerika:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Voor Japan:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Voor Korea:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Voor Zuid-Afrika:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * Voor VAE:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * Voor Australië:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Voor [Azure Government](https://azure.microsoft.com/features/gov/), waarbij gebruik wordt gemaakt van het Amerikaanse overheids exemplaar van Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Voor Amerikaanse overheid DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Voor Canada:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Voor Duitsland:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Voor open bare Duitsland:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Voor India:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Voor Frank rijk:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Voor het Verenigd Konink rijk:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
   * Voor Zwitser land:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1

     > [!TIP]
     > De nCipher nShield-software bevat python op% NFAST_HOME% \ python\bin
     >
     >
2. Controleer of het volgende wordt weer gegeven. Dit geeft aan dat de validatie is geslaagd: **resultaat: geslaagd**

Met dit script wordt de ondertekenaar gecontroleerd op basis van de nShield-hoofd sleutel. De hash van deze hoofd sleutel is inge sloten in het script en de waarde moet **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**zijn. U kunt deze waarde ook afzonderlijk bevestigen door de [nCipher-website](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation)te bezoeken.

U bent nu klaar om een nieuwe sleutel te maken.

### <a name="step-35-create-a-new-key"></a>Stap 3,5: een nieuwe sleutel maken

Genereer een sleutel met behulp van het nCipher nShield **generatekey** -programma.

Voer de volgende opdracht uit om de sleutel te genereren:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Wanneer u deze opdracht uitvoert, volgt u deze instructies:

* De para meter *Protect* moet worden ingesteld op de waarde- **module**, zoals wordt weer gegeven. Hiermee maakt u een door een module beveiligde sleutel. De BYOK-toolset biedt geen ondersteuning voor met OCS beveiligde sleutels.
* Vervang de waarde van *contosokey* voor de **ident** en de eigenschap **Plain** door een wille keurige teken reeks waarde. Voor het minimaliseren van administratieve overhead en het verminderen van het risico van fouten, raden we u aan om dezelfde waarde voor beide te gebruiken. De **ident** -waarde mag alleen cijfers, streepjes en kleine letters bevatten.
* De pubexp wordt in dit voor beeld leeg gelaten (standaard), maar u kunt specifieke waarden opgeven. Zie de [nCipher-documentatie](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based) voor meer informatie.

Met deze opdracht maakt u een sleutel bestand met sleutels in uw% NFAST_KMDATA% \ lokale map met een naam die begint met **key_simple_** , gevolgd door de **ident** die is opgegeven in de opdracht. Bijvoorbeeld: **key_simple_contosokey**. Dit bestand bevat een versleutelde sleutel.

Maak een back-up van dit token sleutel bestand op een veilige locatie.

> [!IMPORTANT]
> Wanneer u de sleutel later overdraagt aan Azure Key Vault, kan micro soft deze sleutel niet meer naar u exporteren, zodat het erg belang rijk is dat u een veilige back-up maakt van uw sleutel en beveiligings wereld. Neem contact op met [nCipher](https://www.ncipher.com/about-us/contact-us) voor hulp en aanbevolen procedures voor het maken van een back-up van uw sleutel.
>


U bent nu klaar om uw sleutel over te dragen naar Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Stap 4: uw sleutel voorbereiden voor overdracht

Voer voor deze vierde stap de volgende procedures uit op het niet-verbonden werk station.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Stap 4,1: een kopie van uw sleutel met beperkte machtigingen maken

Open een nieuwe opdracht prompt en wijzig de huidige map in de locatie waar u het BYOK zip-bestand hebt uitgepakt. Als u de machtigingen voor uw sleutel wilt beperken, voert u een van de volgende handelingen uit vanaf een opdracht prompt, afhankelijk van de geografische regio of het exemplaar van Azure:

* Voor Noord-Amerika:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Voor Europa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Voor Azië:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Voor Latijns-Amerika:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Voor Japan:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Voor Korea:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Voor Zuid-Afrika:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* Voor VAE:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* Voor Australië:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Voor [Azure Government](https://azure.microsoft.com/features/gov/), waarbij gebruik wordt gemaakt van het Amerikaanse overheids exemplaar van Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Voor Amerikaanse overheid DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Voor Canada:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Voor Duitsland:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Voor open bare Duitsland:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Voor India:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Voor Frank rijk:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Voor het Verenigd Konink rijk:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* Voor Zwitser land:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

Wanneer u deze opdracht uitvoert, vervangt u *contosokey* door dezelfde waarde die u hebt opgegeven in **stap 3,5: Maak een nieuwe sleutel** van de stap [uw sleutel genereren](#step-3-generate-your-key) .

U wordt gevraagd om uw beveiligings wereld-beheer kaarten te koppelen.

Wanneer de opdracht is voltooid, ziet u **resultaat: geslaagd** en de kopie van uw sleutel met beperkte machtigingen bevindt zich in het bestand met de naam key_xferacId_\<contosokey >.

U kunt de ACL'S controleren met behulp van de volgende opdrachten met behulp van de nCipher nShield-hulpprogram ma's:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump. exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Wanneer u deze opdrachten uitvoert, vervangt u contosokey door dezelfde waarde die u hebt opgegeven in **stap 3,5: Maak een nieuwe sleutel** van de stap [uw sleutel genereren](#step-3-generate-your-key) .

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Stap 4,2: uw sleutel versleutelen met de sleutel uitwisselings sleutel van micro soft

Voer een van de volgende opdrachten uit, afhankelijk van uw geografische regio of het exemplaar van Azure:

* Voor Noord-Amerika:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Europa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Azië:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Latijns-Amerika:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Japan:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Korea:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Zuid-Afrika:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor VAE:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Australië:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor [Azure Government](https://azure.microsoft.com/features/gov/), waarbij gebruik wordt gemaakt van het Amerikaanse overheids exemplaar van Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Amerikaanse overheid DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Canada:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Duitsland:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor open bare Duitsland:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor India:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Frank rijk:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor het Verenigd Konink rijk:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Zwitser land:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Wanneer u deze opdracht uitvoert, volgt u deze instructies:

* Vervang *contosokey* door de id die u hebt gebruikt om de sleutel te genereren in **stap 3,5: Maak een nieuwe sleutel** van de stap [uw sleutel genereren](#step-3-generate-your-key) .
* Vervang *SubscriptionID* door de id van het Azure-abonnement dat uw sleutel kluis bevat. U hebt deze waarde eerder opgehaald in **stap 1,2: uw Azure-abonnements-id ophalen** uit de stap [uw werk station voorbereiden op Internet verbinding](#step-1-prepare-your-internet-connected-workstation) .
* Vervang *ContosoFirstHSMKey* door een label dat wordt gebruikt voor de naam van het uitvoer bestand.

Wanneer dit is voltooid, wordt het resultaat weer gegeven **: geslaagd** en er is een nieuw bestand in de huidige map met de volgende naam: KeyTransferPackage-*ContosoFirstHSMkey*. byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Stap 4,3: uw sleutel overdrachts pakket kopiëren naar het met internet verbonden werk station

Gebruik een USB-station of andere draag bare opslag om het uitvoer bestand van de vorige stap (KeyTransferPackage-ContosoFirstHSMkey. byok) te kopiëren naar uw werk station met Internet verbinding.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Stap 5: uw sleutel overdragen naar Azure Key Vault

Voor deze laatste stap gebruikt u op het met internet verbonden werk station de cmdlet [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) om het sleutel overdrachts pakket dat u van het niet-verbonden werk station hebt gekopieerd, te uploaden naar de Azure Key Vault HSM:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Als het uploaden is gelukt, ziet u de eigenschappen van de sleutel die u zojuist hebt toegevoegd.

## <a name="next-steps"></a>Volgende stappen

U kunt deze met HSM beschermde sleutel nu gebruiken in uw sleutel kluis. Zie voor meer informatie deze [vergelijking](https://azure.microsoft.com/pricing/details/key-vault/)van prijzen en functies.
