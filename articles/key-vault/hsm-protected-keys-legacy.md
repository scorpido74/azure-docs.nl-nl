---
title: HSM-beveiligde sleutels genereren en overdragen voor Azure Key Vault - Azure Key Vault | Microsoft Documenten
description: Gebruik dit artikel om u te helpen bij het plannen, genereren en vervolgens overdragen van uw eigen HSM-beveiligde sleutels voor gebruik met Azure Key Vault. Ook wel bekend als BYOK of breng je eigen sleutel.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 0d51e1aaf1d9b0472245b9a7f29148517a0a7b93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77429304"
---
# <a name="import-hsm-protected-keys-for-key-vault-legacy"></a>HSM-beveiligde sleutels importeren voor Key Vault (legacy)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor extra zekerheid u bij het gebruik van Azure Key Vault sleutels importeren of genereren in hardwarebeveiligingsmodules (HSM's) die nooit de HSM-grens verlaten. Dit scenario wordt vaak *Bring Your Own Key* of BYOK genoemd. Azure Key Vault maakt gebruik van nCipher nShield-familie hsm's (FIPS 140-2 Level 2 gevalideerd) om uw sleutels te beschermen.

Gebruik de informatie in dit onderwerp om u te helpen bij het plannen, genereren en vervolgens overdragen van uw eigen HSM-beveiligde sleutels om te gebruiken met Azure Key Vault.

Deze functionaliteit is niet beschikbaar voor Azure China.

> [!NOTE]
> Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](key-vault-overview.md)  
> Zie [Wat is Azure Key Vault?](key-vault-overview.md)

Meer informatie over het genereren en overdragen van een HSM-beveiligde sleutel via internet:

* U genereert de sleutel vanuit een offline werkstation, waardoor het aanvalsoppervlak wordt verminderd.
* De sleutel wordt versleuteld met een Key Exchange Key (KEK), die versleuteld blijft totdat deze wordt overgebracht naar de Azure Key Vault HSM's. Alleen de versleutelde versie van uw sleutel verlaat het oorspronkelijke werkstation.
* De toolset stelt eigenschappen in op uw tenantsleutel die uw sleutel bindt aan de beveiligingswereld van Azure Key Vault. Dus nadat de Azure Key Vault HSM's uw sleutel hebben ontvangen en ontsleutelen, kunnen alleen deze HSM's deze gebruiken. Uw sleutel kan niet worden geëxporteerd. Deze binding wordt afgedwongen door de nCipher HSM's.
* De Key Exchange Key (KEK) die wordt gebruikt om uw sleutel te versleutelen, wordt gegenereerd in de Azure Key Vault HSM's en is niet exporteerbaar. De HSM's dwingen af dat er geen versie van de KEK buiten de HSM's kan bestaan. Bovendien bevat de toolset attest van nCipher dat de KEK niet exporteerbaar is en werd gegenereerd in een echte HSM die door nCipher is vervaardigd.
* De toolset bevat attestation van nCipher dat de Azure Key Vault-beveiligingswereld ook is gegenereerd op een echte HSM die door nCipher is vervaardigd. Deze verklaring bewijst aan u dat Microsoft echte hardware gebruikt.
* Microsoft maakt gebruik van afzonderlijke KEKs en afzonderlijke beveiligingswerelden in elke geografische regio. Deze scheiding zorgt ervoor dat uw sleutel alleen kan worden gebruikt in datacenters in de regio waar u deze hebt versleuteld. Een sleutel van een Europese klant kan bijvoorbeeld niet worden gebruikt in datacenters in Noord-Amerika of Azië.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Meer informatie over nCipher HSM's en Microsoft-services

nCipher Security, een Bedrijf van Entrust Datacard, is een leider in de hsm-markt voor algemeen gebruik en stelt toonaangevende organisaties in staat om vertrouwen, integriteit en controle te leveren aan hun bedrijfskritieke informatie en toepassingen. nCipher's cryptografische oplossingen beveiligen opkomende technologieën – cloud, IoT, blockchain, digitale betalingen – en helpen voldoen aan nieuwe nalevingsmandaten, met behulp van dezelfde bewezen technologie waar wereldwijde organisaties vandaag de dag van afhankelijk zijn om te beschermen tegen bedreigingen voor hun gevoelige gegevens, netwerkcommunicatie en bedrijfsinfrastructuur. nCipher biedt vertrouwen voor bedrijfskritische toepassingen, zorgt voor de integriteit van gegevens en geeft klanten volledige controle – vandaag, morgen, te allen tijde.

Microsoft heeft samengewerkt met nCipher Security om de stand van zaken voor HSM's te verbeteren. Dankzij deze verbeteringen kunt u profiteren van de gebruikelijke voordelen van gehoste services, zonder dat u de controle over uw sleutels verliest. Dankzij deze uitbreidingen kan Microsoft de HSM's beheren en hoeft u dat niet te doen. Als cloudservice schaalt Azure Key Vault op korte termijn op om aan de gebruikspieken van uw organisatie te voldoen. Tegelijkertijd wordt uw sleutel beschermd binnen de HSM's van Microsoft: u behoudt de controle over de levenscyclus van de sleutel omdat u de sleutel zelf genereert en overdraagt naar de HSM's van Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Bring Your Own Key (BYOK) implementeren voor Azure Key Vault

Gebruik de volgende informatie en procedures als u uw eigen door HSM beveiligde sleutel genereert en deze vervolgens overzet naar Azure Key Vault, het BYOK-scenario (Bring Your Own Key).

## <a name="prerequisites-for-byok"></a>Vereisten voor BYOK

Zie de volgende tabel voor een lijst met vereisten voor het meenemen van uw eigen sleutel (BYOK) voor Azure Key Vault.

| Vereiste | Meer informatie |
| --- | --- |
| Een abonnement op Azure |Als u een Azure Key Vault wilt maken, hebt u een Azure-abonnement nodig: [Aanmelden voor gratis proefversie](https://azure.microsoft.com/pricing/free-trial/) |
| De Azure Key Vault Premium-servicelaag ter ondersteuning van hsm-beveiligde sleutels |Zie de website [Azure Key Vault Pricing](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie over de servicelagen en -mogelijkheden voor Azure Key Vault. |
| nCipher nShield HSM's, smartcards en ondersteuningssoftware |U moet toegang hebben tot een nCipher Hardware Security Module en basisoperationele kennis van nCipher nShield HSM's. Zie [nCipher nShield Hardware Security Module](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) voor de lijst met compatibele modellen, of om een HSM aan te schaffen als u er geen hebt. |
| De volgende hardware en software:<ol><li>Een offline x64-workstation met een minimaal Windows-besturingssysteem van Windows 7- en nCipher nShield-software dat ten minste versie 11.50 is.<br/><br/>Als op dit werkstation Windows 7 wordt uitgevoerd, moet u [Microsoft .NET Framework 4.5 installeren.](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe)</li><li>Een werkstation dat is verbonden met internet en een minimaal Windows-besturingssysteem van Windows 7 en [Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) **minimumversie 1.1.0** heeft geïnstalleerd.</li><li>Een USB-station of een ander draagbaar opslagapparaat met ten minste 16 MB vrije ruimte.</li></ol> |Uit veiligheidsoverwegingen raden we u aan het eerste werkstation niet aan te sluiten op een netwerk. Deze aanbeveling wordt echter niet programmatisch uitgevoerd.<br/><br/>In de instructies die volgen, wordt dit werkstation aangeduid als het losgekoppelde werkstation.</p></blockquote><br/>Als uw tenantsleutel voor een productienetwerk is, raden we u bovendien aan een tweede, apart werkstation te gebruiken om de toolset te downloaden en de tenantsleutel te uploaden. Voor testdoeleinden kunt u echter hetzelfde werkstation gebruiken als het eerste.<br/><br/>In de instructies die volgen, wordt dit tweede werkstation aangeduid als het werkstation met internetverbinding.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Uw sleutel genereren en overdragen naar Azure Key Vault HSM

U gebruikt de volgende vijf stappen om uw sleutel te genereren en over te dragen aan een Azure Key Vault HSM:

* [Stap 1: Uw werkstation met internetverbinding voorbereiden](#step-1-prepare-your-internet-connected-workstation)
* [Stap 2: Uw losgekoppelde werkstation voorbereiden](#step-2-prepare-your-disconnected-workstation)
* [Stap 3: Uw sleutel genereren](#step-3-generate-your-key)
* [Stap 4: uw sleutel voorbereiden voor overdracht](#step-4-prepare-your-key-for-transfer)
* [Stap 5: uw sleutel overdragen naar Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Stap 1: Uw werkstation met internetverbinding voorbereiden

Doe voor deze eerste stap de volgende procedures op uw werkstation dat is verbonden met internet.

### <a name="step-11-install-azure-powershell"></a>Stap 1.1: Azure PowerShell installeren

Download en installeer de Azure PowerShell-module met de cmdlets om Azure Key Vault te beheren vanaf het werkstation met internetverbinding. Zie [Azure PowerShell installeren en configureren voor](/powershell/azure/overview)installatie-instructies.

### <a name="step-12-get-your-azure-subscription-id"></a>Stap 1.2: Uw Azure-abonnements-ID aanschaffen

Start een Azure PowerShell-sessie en meld u aan bij uw Azure-account met de volgende opdracht:

```Powershell
   Connect-AzAccount
```
Voer in het pop-upvenster in de browser uw gebruikersnaam en wachtwoord voor uw Azure-account in. Gebruik vervolgens de opdracht [Get-AzSubscription:](/powershell/module/az.accounts/get-azsubscription)

```powershell
   Get-AzSubscription
```
Zoek in de uitvoer de id voor het abonnement dat u voor Azure Key Vault gebruikt. Je hebt deze abonnements-ID later nodig.

Sluit het Azure PowerShell-venster niet.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Stap 1.3: De BYOK-toolset voor Azure Key Vault downloaden

Ga naar het Microsoft Downloadcentrum en [download de Azure Key Vault BYOK-toolset](https://www.microsoft.com/download/details.aspx?id=45345) voor uw geografische regio of instantie van Azure. Gebruik de volgende informatie om de te downloaden pakketnaam en de bijbehorende SHA-256-pakkethash te identificeren:

---
**Verenigde Staten:**

KeyVault-BYOK-Tools-Verenigde Staten.zip

2E8C0032040043010636A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Europa:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB624858754203721D2F8D300910634A32DFA1FB19E4A

---
**Azië:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A136F44C97388EC7121EF6B5

---
**Latijns-Amerika:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Japan:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC0651295ADC482B027AF923A7FF1F6BF98B4D4B8E94632DEC7DF

---
**Korea:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**Zuid-Afrika:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C6776F46D98620784A

---
**Uae:**

KeyVault-BYOK-Tools-VAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Australië:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3EEE2452A025223EEB166901C40A

---
[**Azure-overheid:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**Amerikaanse regering DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205444442B3DF861B79B8B991BB555C35263

---
**Canada:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E2719363088799717C7B

---
**Duitsland:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AFC02AFD9841F7BADD025D7EE819894A29ED3C71C3F844C45D6

---
**Duitsland Publiek:**

KeyVault-BYOK-Tools-Germany-Public.zip

54534936D0A0C9C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**India:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Frankrijk:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09F60897C9ae3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Verenigd Koninkrijk:**

KeyVault-BYOK-Tools-VerenigdKoninkrijk.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB2956489D3782B3B80849

---
**Zwitserland:**

KeyVault-BYOK-Tools-Switzerland.zip

88CF8D39899E26D456D4E0BC57E5C9494913ABF1D73A89013FCE3BBD9599AD2FE9

---


Als u de integriteit van uw gedownloade BYOK-toolset wilt valideren, gebruikt u vanuit uw Azure PowerShell-sessie de cmdlet [Get-FileHash.](https://technet.microsoft.com/library/dn520872.aspx)

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

De toolset bevat:

* Een KEK-pakket (Key Exchange Key) met een naam die begint met **BYOK-KEK-pkg-.**
* Een Security World pakket dat een naam heeft die begint met **BYOK-SecurityWorld-pkg-.**
* Een python script genaamd **verifykeypackage.py.**
* Een uitvoerbaar bestand voor opdrachtregel met de naam **KeyTransferRemote.exe** en bijbehorende DLL's.
* Een Visual C++ Redistributable Package met de naam **vcredist_x64.exe.**

Kopieer het pakket naar een USB-station of ander draagbaar opslagmedium.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Stap 2: Uw losgekoppelde werkstation voorbereiden

Doe voor deze tweede stap de volgende procedures op het werkstation dat niet is verbonden met een netwerk (internet of intern netwerk).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Stap 2.1: Bereid het losgekoppelde werkstation voor met nCipher nShield HSM

Installeer de nCipher-ondersteuningssoftware op een Windows-computer en bevestig vervolgens een nCipher nShield HSM op die computer.

Controleer of de nCipher-gereedschappen zich op uw pad bevinden **(%nfast_home%\bin).** Typ bijvoorbeeld het volgende:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Zie voor meer informatie de gebruikershandleiding bij de nShield HSM.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Stap 2.2: De BYOK-toolset installeren op het losgekoppelde werkstation

Kopieer het pakket met de BYOK-hulpmiddelenset van het USB-station of het andere draagbare opslagmedium en doe daarna het volgende:

1. Pak de bestanden in het gedownloade pakket uit naar een map.
2. Voer vcredist_x64.exe uit in die map.
3. Volg de instructies voor het installeren van de Visual C++ runtime componenten voor Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Stap 3: Uw sleutel genereren

Doe voor deze derde stap de volgende procedures op het losgekoppelde werkstation. Om deze stap te voltooien moet uw HSM in de initialisatiemodus staan. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Stap 3.1: De HSM-modus wijzigen in 'I'

Als u nCipher nShield Edge gebruikt, wijzigt u de modus: 1. Gebruik de knop Modus om de vereiste modus te markeren. 2. Houd binnen enkele seconden de knop Wissen ingedrukt voor een paar seconden. Als de modus verandert, knippert de LED van de nieuwe modus niet meer en blijft deze branden. De status-LED kan enkele seconden onregelmatig knipperen en vervolgens regelmatig knipperen wanneer het apparaat klaar is. Anders blijft het apparaat in de huidige modus, met de juiste modus LED verlicht.

### <a name="step-32-create-a-security-world"></a>Stap 3.2: Een beveiligingswereld creëren

Start een opdrachtprompt en voer het nCipher new-world-programma uit.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Dit programma maakt een **Beveiligingswereld**-bestand op %NFAST_KMDATA%\local\world, wat overeenkomt met de map C:\ProgramData\nCipher\Key Management Data\local. U kunt andere waarden gebruiken voor het quorum, maar in ons voorbeeld wordt u gevraagd drie lege kaarten en pincodes voor elk adres in te voeren. Dan geven alle twee kaarten volledige toegang tot de beveiligingswereld. Deze kaarten worden de **Beheerderskaartenset** voor de nieuwe beveiligingswereld.

> [!NOTE]
> Als uw HSM de nieuwere cypher suite DLf3072s256mRijndael niet ondersteunt, u --cipher-suite= DLf3072s256mRijndael vervangen door --cipher-suite=DLf1024s160mRijndael
> 
> Security wereld gemaakt met new-world.exe dat schepen met nCipher software versie 12.50 is niet compatibel met deze BYOK procedure. Er zijn twee opties beschikbaar:
> 1) Downgrade nCipher software versie naar 12.40.2 om een nieuwe security wereld te creëren.
> 2) Neem contact op met nCipher-ondersteuning en vraag hen om een hotfix te leveren voor 12.50 softwareversie, waarmee u 12.40.2-versie van new-world.exe gebruiken die compatibel is met deze BYOK-procedure.

Ga daarna als volgt te werk:

* Maak een back-up van het wereldbestand. Beveilig en bescherm het wereldbestand, de beheerderskaarten en de bijbehorende pincodes en zorg ervoor dat niemand toegang heeft tot meer dan één kaart.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Stap 3.3: De HSM-modus wijzigen in 'O'

Als u nCipher nShield Edge gebruikt, wijzigt u de modus: 1. Gebruik de knop Modus om de vereiste modus te markeren. 2. Houd binnen enkele seconden de knop Wissen ingedrukt voor een paar seconden. Als de modus verandert, knippert de LED van de nieuwe modus niet meer en blijft deze branden. De status-LED kan enkele seconden onregelmatig knipperen en vervolgens regelmatig knipperen wanneer het apparaat klaar is. Anders blijft het apparaat in de huidige modus, met de juiste modus LED verlicht.

### <a name="step-34-validate-the-downloaded-package"></a>Stap 3.4: Het gedownloade pakket valideren

Deze stap is optioneel maar wordt aanbevolen, zodat u het volgende kunt valideren:

* De Key Exchange-sleutel die is opgenomen in de toolset is gegenereerd uit een echte nCipher nShield HSM.
* De hash van de Security World die is opgenomen in de toolset is gegenereerd in een echte nCipher nShield HSM.
* De sleutel voor sleuteluitwisseling (KEK-sleutel) is niet exporteerbaar.

> [!NOTE]
> Als u het gedownloade pakket wilt valideren, moet de HSM zijn verbonden, zijn ingeschakeld en zijn voorzien van een beveiligingswereld (zoals u net hebt gemaakt).

Ga als het gaat om het gedownloade pakket te valideren:

1. Voer het verifykeypackage.py script uit door een van de volgende typen te typen, afhankelijk van uw geografische regio of instantie van Azure:

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
   * Voor de VAE:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * Voor Australië:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Voor [Azure Government](https://azure.microsoft.com/features/gov/), dat gebruik maakt van het amerikaanse overheidsexemplaar van Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Voor Dod van de Amerikaanse regering:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Voor Canada:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Voor Duitsland:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Voor Duitsland Publiek:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Voor India:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Voor Frankrijk:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Voor Het Verenigd Koninkrijk:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
   * Voor Zwitserland:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1

     > [!TIP]
     > De nCipher nShield software bevat python bij %NFAST_HOME%\python\bin
     >
     >
2. Controleer of u het volgende ziet, wat duidt op succesvolle validatie: **Resultaat: SUCCES**

Dit script valideert de aantekenaarsketen tot aan de nShield-hoofdsleutel. De hash van deze hoofdsleutel is ingesloten in het script en de waarde moet gelijk zijn aan **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. U deze waarde ook afzonderlijk bevestigen door de [nCipher-website te](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation)bezoeken.

Je bent nu klaar om een nieuwe sleutel te maken.

### <a name="step-35-create-a-new-key"></a>Stap 3.5: Een nieuwe sleutel maken

Genereer een sleutel met behulp van het nCipher nShield **generatekey-programma.**

Voer de volgende opdracht uit om de sleutel te genereren:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Volg de volgende instructies om deze opdracht uit te voeren:

* De parameter *protect* moet worden ingesteld op de waarde **module**, zoals weergegeven. Hiermee maakt u een modulair beveiligde sleutel. De BYOK-toolset biedt geen ondersteuning voor met OCS beveiligde sleutels.
* Vervang de waarde van *contosokey* voor de **ident** en **plainname** door een willekeurige tekenreekswaarde. Om administratieve overheadkosten te minimaliseren en het risico op fouten te verminderen, raden we u aan voor beide dezelfde waarde te gebruiken. De **ident-waarde** mag alleen getallen, streepjes en kleine letters bevatten.
* De pubexp is leeg (standaard) in dit voorbeeld, maar u kunt specifieke waarden opgeven. Zie voor meer informatie de [nCipher-documentatie.](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

Met deze opdracht wordt een tokenized key-bestand gemaakt in uw map %NFAST_KMDATA%\met een naam die begint met **key_simple_,** gevolgd door de **ident** die in de opdracht is opgegeven. Bijvoorbeeld: **key_simple_contosokey**. Dit bestand bevat een versleutelde sleutel.

Maak van deze Tokenized sleutel een back-up op een veilige locatie.

> [!IMPORTANT]
> Wanneer u later uw sleutel overdraagt naar Azure Key Vault, kan Microsoft deze sleutel niet naar u exporteren, zodat het uiterst belangrijk wordt dat u veilig een back-up maakt van uw sleutel- en beveiligingswereld. Neem contact op met [nCipher](https://www.ncipher.com/about-us/contact-us) voor richtlijnen en aanbevolen procedures voor het maken van een back-up van uw sleutel.
>


U bent nu klaar om uw sleutel over te zetten naar Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Stap 4: uw sleutel voorbereiden voor overdracht

Ga voor deze vierde stap de volgende procedures uit op het losgekoppelde werkstation.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Stap 4.1: Een kopie van uw sleutel maken met beperkte machtigingen

Open een nieuwe opdrachtprompt en wijzig de huidige map in de locatie waar u het BYOK-zip-bestand hebt uitgepakt. Als u de machtigingen voor uw sleutel wilt verminderen, voert u een van de volgende machtigingen uit, afhankelijk van uw geografische regio of instantie van Azure:

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
* Voor de VAE:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* Voor Australië:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Voor [Azure Government](https://azure.microsoft.com/features/gov/), dat gebruik maakt van het amerikaanse overheidsexemplaar van Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Voor Dod van de Amerikaanse regering:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Voor Canada:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Voor Duitsland:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Voor Duitsland Publiek:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Voor India:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Voor Frankrijk:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Voor Het Verenigd Koninkrijk:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* Voor Zwitserland:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

Wanneer u deze opdracht uitvoert, vervangt u *contosokey* door dezelfde waarde die u hebt opgegeven in **stap 3.5: Een nieuwe sleutel maken** vanuit de [toetsstap Genereren.](#step-3-generate-your-key)

U wordt gevraagd om uw beveiligingswereld admin kaarten aan te sluiten.

Wanneer de opdracht is voltooid, ziet u **Resultaat: SUCCES** en de kopie van\<uw sleutel met beperkte machtigingen staan in het bestand met de naam key_xferacId_ contosokey>.

U de ACLS inspecteren met behulp van volgende opdrachten met behulp van de nCipher nShield-hulpprogramma's:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Wanneer u deze opdrachten uitvoert, vervangt u contosokey door dezelfde waarde die u hebt opgegeven in **stap 3.5: Maak een nieuwe sleutel** uit de [toetsstap Genereren.](#step-3-generate-your-key)

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Stap 4.2: Uw sleutel versleutelen met de sleutelsleutel van Microsoft

Voer een van de volgende opdrachten uit, afhankelijk van uw geografische regio of instantie van Azure:

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
* Voor de VAE:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Australië:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor [Azure Government](https://azure.microsoft.com/features/gov/), dat gebruik maakt van het amerikaanse overheidsexemplaar van Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Dod van de Amerikaanse regering:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Canada:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Duitsland:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Duitsland Publiek:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor India:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Frankrijk:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Het Verenigd Koninkrijk:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Voor Zwitserland:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Volg de volgende instructies om deze opdracht uit te voeren:

* Vervang *contosokey* door de id die u hebt gebruikt om de sleutel te genereren in **stap 3.5: Maak een nieuwe sleutel** uit de [toetsstap Genereren.](#step-3-generate-your-key)
* *Vervang SubscriptionID* door de id van het Azure-abonnement dat uw sleutelkluis bevat. U hebt deze waarde eerder opgehaald in **stap 1.2: Haal uw Azure-abonnements-id** op van de stap [Werkstation voorbereiden](#step-1-prepare-your-internet-connected-workstation) met internet.
* Vervang *ContosoFirstHSMKey* door een label dat wordt gebruikt voor de naam van uw uitvoerbestand.

Wanneer dit is voltooid, wordt **resultaat: SUCCES** weergegeven en is er een nieuw bestand in de huidige map met de volgende naam: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Stap 4.3: Kopieer uw sleuteloverdrachtspakket naar het werkstation dat is verbonden met internet

Gebruik een USB-station of andere draagbare opslag om het uitvoerbestand van de vorige stap (KeyTransferPackage-ContosoFirstHSMkey.byok) naar uw met internet verbonden werkstation te kopiëren.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Stap 5: uw sleutel overdragen naar Azure Key Vault

Gebruik voor deze laatste stap op het werkstation met internetverbinding de cmdlet [Add-AzKeyVaultKey om](/powershell/module/az.keyvault/add-azkeyvaultkey) het sleuteloverdrachtpakket dat u hebt gekopieerd van het losgekoppelde werkstation te uploaden naar het Azure Key Vault HSM:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Als de upload succesvol is, ziet u de eigenschappen van de sleutel die u zojuist hebt toegevoegd.

## <a name="next-steps"></a>Volgende stappen

U deze HSM-beveiligde sleutel nu gebruiken in uw sleutelkluis. Zie voor meer informatie deze prijs- en [functievergelijking.](https://azure.microsoft.com/pricing/details/key-vault/)
