---
title: Handleiding voor probleemoplossing azure Storage Explorer | Microsoft Documenten
description: Overzicht van foutopsporingstechnieken voor Azure Storage Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: db36033ea524603416f16db27f40d5eefb8bf613
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437107"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Handleiding voor probleemoplossing azure Storage Explorer

Microsoft Azure Storage Explorer is een zelfstandige app waarmee u eenvoudig werken met Azure Storage-gegevens op Windows, macOS en Linux. De app kan verbinding maken met opslagaccounts die worden gehost op Azure, nationale clouds en Azure Stack.

Deze handleiding geeft een overzicht van oplossingen voor problemen die vaak worden gezien in Storage Explorer.

## <a name="rbac-permissions-issues"></a>Problemen met RBAC-machtigingen

Op rollen gebaseerd toegangsbeheer [RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) maakt zeer gedetailleerd toegangsbeheer van Azure-resources mogelijk door sets machtigingen in _rollen_te combineren. Hier volgen enkele strategieën om RBAC optimaal te laten werken in Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Hoe krijg ik toegang tot mijn bronnen in Storage Explorer?

Als u problemen ondervindt bij de toegang tot opslagbronnen via RBAC, is mogelijk niet de juiste rollen toegewezen. In de volgende secties worden de machtigingen beschreven die Storage Explorer momenteel nodig heeft voor toegang tot uw opslagbronnen. Neem contact op met uw Azure-accountbeheerder als u niet zeker weet of u over de juiste rollen of machtigingen beschikt.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Het probleem met de machtigingen van het 'Lees:'s-account(en)"

U moet toestemming hebben om opslagaccounts op te sommen. Als u deze toestemming wilt krijgen, moet u de rol _Reader_ toegewezen krijgen.

#### <a name="list-storage-account-keys"></a>Opslagaccountsleutels van de lijst

Storage Explorer kan ook accountsleutels gebruiken om aanvragen te verifiëren. U toegang krijgen tot accountsleutels via krachtigere rollen, zoals de rol _inzender._

> [!NOTE]
> Toegangssleutels verlenen onbeperkte machtigingen aan iedereen die ze in zijn bezit heeft. Daarom raden we u niet aan deze sleutels uit te delen aan accountgebruikers. Als u toegangssleutels moet intrekken, u deze opnieuw genereren vanuit de [Azure-portal.](https://portal.azure.com/)

#### <a name="data-roles"></a>Gegevensrollen

U moet ten minste één rol toegewezen krijgen die toegang geeft tot leesgegevens uit bronnen. Als u bijvoorbeeld blobs wilt aanbieden of downloaden, hebt u ten minste de rol _Opslagblob-gegevenslezer_ nodig.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Waarom heb ik een rol in de beheerlaag nodig om mijn resources in Storage Explorer te zien?

Azure Storage heeft twee toegangslagen: _beheer_ en _gegevens._ Abonnementen en opslagaccounts zijn toegankelijk via de beheerlaag. Containers, blobs en andere gegevensbronnen worden geopend via de gegevenslaag. Als u bijvoorbeeld een lijst met uw opslagaccounts uit Azure wilt ophalen, stuurt u een aanvraag naar het eindpunt van het beheer. Als u een lijst met blobcontainers in een account wilt, stuurt u een aanvraag naar het juiste serviceeindpunt.

RBAC-rollen kunnen machtigingen bevatten voor beheer of toegang tot gegevenslagen. De rol Reader geeft bijvoorbeeld alleen-lezen toegang tot beheerlaagbronnen.

Strikt genomen biedt de leesrol geen machtigingen voor gegevenslagen en is het niet nodig om toegang te krijgen tot de gegevenslaag.

Storage Explorer maakt het eenvoudig om toegang te krijgen tot uw bronnen door de benodigde informatie te verzamelen om verbinding te maken met uw Azure-bronnen. Als u bijvoorbeeld uw blobcontainers wilt weergeven, verzendt Storage Explorer een aanvraag voor 'lijstcontainers' naar het eindpunt van de blobservice. Om dat eindpunt te krijgen, doorzoekt Storage Explorer de lijst met abonnementen en opslagaccounts waartoe u toegang hebt. Om uw abonnementen en opslagaccounts te vinden, heeft Storage Explorer ook toegang nodig tot de beheerlaag.

Als u geen rol hebt waardoor beheerderslaagmachtigingen worden verleend, kan Storage Explorer niet de informatie krijgen die nodig is om verbinding te maken met de gegevenslaag.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Wat moet ik doen als ik de machtigingen voor beheerlagen die ik nodig heb, niet kan krijgen van mijn beheerder?

We hebben momenteel geen RBAC-gerelateerde oplossing voor dit probleem. Als tijdelijke oplossing u een SAS URI aanvragen om aan [uw resource te koppelen.](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri)

### <a name="recommended-built-in-rbac-roles"></a>Aanbevolen ingebouwde RBAC-rollen

Er zijn verschillende ingebouwde RBAC-rollen die de machtigingen kunnen bieden die nodig zijn om Storage Explorer te gebruiken. Sommige van die rollen zijn:
- [Eigenaar](/azure/role-based-access-control/built-in-roles#owner): Beheer alles, inclusief toegang tot bronnen. **Opmerking:** deze rol geeft u belangrijke toegang.
- [Bijdrager](/azure/role-based-access-control/built-in-roles#contributor): Beheer alles, exclusief toegang tot bronnen. **Opmerking:** deze rol geeft u belangrijke toegang.
- [Reader](/azure/role-based-access-control/built-in-roles#reader): Lees en lijst bronnen.
- [Opslagaccountbijdrager:](/azure/role-based-access-control/built-in-roles#storage-account-contributor)volledig beheer van opslagaccounts. **Opmerking:** deze rol geeft u belangrijke toegang.
- [Storage Blob Data Owner:](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)Volledige toegang tot Azure Storage blob containers en gegevens.
- [Opslagblobgegevenscontribuans:](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)Azure Storage-containers en blobs lezen, schrijven en verwijderen.
- [OpslagBlob-gegevenslezer:](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)lees en lijst Azure Storage-containers en blobs.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fout: Zelfondertekend certificaat in certificaatketen (en vergelijkbare fouten)

Certificaatfouten treden meestal op in een van de volgende situaties:

- De app is verbonden via een _transparante proxy,_ wat betekent dat een server (zoals uw bedrijfsserver) HTTPS-verkeer onderschept, decodeert en deze vervolgens versleutelt met behulp van een zelfondertekend certificaat.
- U voert een toepassing uit die een zelfondertekend TLS/SSL-certificaat injecteert in de HTTPS-berichten die u ontvangt. Voorbeelden van toepassingen die certificaten injecteren zijn antivirus- en netwerkverkeersinspectiesoftware.

Wanneer Storage Explorer een zelfondertekend of niet-vertrouwd certificaat ziet, weet het niet meer of het ontvangen HTTPS-bericht is gewijzigd. Als u een kopie van het zelfondertekende certificaat hebt, u Storage Explorer opdracht geven om het te vertrouwen door de volgende stappen te volgen:

1. Een Basis-64 gecodeerde X.509 (.cer) kopie van het certificaat verkrijgen.
2. Ga **naar** > **Ssl-certificaten** > **bewerken**en gebruik de bestandenkiezer om het .cer-bestand te zoeken, te selecteren en te openen.

Dit probleem kan ook optreden als er meerdere certificaten (root en intermediate) zijn. Om deze fout op te lossen, moeten beide certificaten worden toegevoegd.

Als u niet zeker weet waar het certificaat vandaan komt, voert u de volgende stappen uit om het te vinden:

1. Installeer OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): Elk van de lichte versies moet voldoende zijn.
    * Mac en Linux: Moet worden opgenomen met uw besturingssysteem.
2. OpenSSL uitvoeren.
    * Windows: Open de installatiemap, selecteer **/bin/** en dubbelklik op **openssl.exe**.
    * Mac en Linux: Run `openssl` from a terminal.
3. Voer `s_client -showcerts -connect microsoft.com:443` uit.
4. Zoek naar zelfondertekende certificaten. Als u niet zeker weet welke certificaten zelf zijn ondertekend, `("s:")` noteer dan overal waar het onderwerp en de uitgever `("i:")` hetzelfde zijn.
5. Wanneer u zelfondertekende certificaten vindt, kopieeren en plakt u `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` voor elk certificaat alles van (en inclusief) tot in een nieuw .cer-bestand.
6. Open Storage Explorer **Edit** > en ga naar**SSL-certificaten** > **bewerken**. Gebruik vervolgens de bestandenkiezer om de .cer-bestanden die u hebt gemaakt te zoeken, selecteren en openen.

Als u geen zelfondertekende certificaten vinden door deze stappen te volgen, neemt u contact met ons op via de feedbacktool. U Storage Explorer ook openen vanaf `--ignore-certificate-errors` de opdrachtregel met behulp van de vlag. Wanneer deze vlag wordt geopend, negeert Storage Explorer certificaatfouten.

## <a name="sign-in-issues"></a>Aanmeldingsproblemen

### <a name="blank-sign-in-dialog-box"></a>Dialoogvenster Leeg aanmelden

Lege aanmeldingsdialoogvensters komen het vaakst voor wanneer Active Directory Federation Services (AD FS) Storage Explorer vraagt om een omleiding uit te voeren, die niet wordt ondersteund door Electron. Als u dit probleem wilt oplossen, u proberen de stroom van apparaatcodes te gebruiken voor aanmelding. Hiervoor volgt u de volgende stappen:

1. Open Instellingen op de linker verticale **gereedschapsbalk**. Ga in het instellingenpaneel naar **Aanmelden bij toepassingen** > **.** **Aanmelden voor apparaatcodestroom**gebruiken inschakelen .
2. Open het dialoogvenster **Verbinden** (via het stekkerpictogram op de verticale balk aan de linkerkant of door **Account toevoegen** in het accountpaneel te selecteren).
3. Kies de omgeving waarop u zich wilt aanmelden.
4. Selecteer **Aanmelden**.
5. Volg de instructies op het volgende paneel.

Als u zich niet aanmelden bij het account dat u wilt gebruiken omdat uw standaardbrowser al is aangemeld bij een ander account, voert u een van de volgende handelingen uit:

- Kopieer de koppeling en code handmatig naar een privésessie van uw browser.
- Kopieer de koppeling en code handmatig naar een andere browser.

### <a name="reauthentication-loop-or-upn-change"></a>Verificatielus of UPN-wijziging

Als u zich in een herverificatielus bevindt of de UPN van een van uw accounts hebt gewijzigd, voert u de volgende stappen uit:

1. Verwijder alle accounts en sluit Storage Explorer.
2. Verwijder de . IdentityService-map van uw machine. In Windows bevindt de `C:\users\<username>\AppData\Local`map zich op . Voor Mac en Linux vindt u de map aan de basis van uw gebruikersmap.
3. Als u Mac of Linux draait, moet u ook de vermelding Microsoft.Developer.IdentityService uit de keystore van uw besturingssysteem verwijderen. Op de Mac is de keystore de *Gnome Keychain* applicatie. In Linux wordt de toepassing meestal _Keyring_genoemd, maar de naam kan verschillen afhankelijk van uw distributie.

### <a name="conditional-access"></a>Voorwaardelijke toegang

Vanwege een beperking in de Azure AD-bibliotheek die wordt gebruikt door Storage Explorer, wordt voorwaardelijke toegang niet ondersteund wanneer Storage Explorer wordt gebruikt op Windows 10, Linux of macOS.

## <a name="mac-keychain-errors"></a>Mac-sleutelhangerfouten

De macOS-sleutelhanger kan soms een status invoeren die problemen veroorzaakt voor de verificatiebibliotheek van Storage Explorer. Voer de volgende stappen uit om de sleutelhanger uit deze status te halen:

1. Sluit Storage Explorer.
2. Open Sleutelhanger (druk op Command+Spatiebalk, typ **sleutelhanger**en druk op Enter).
3. Selecteer de sleutelhanger 'inloggen'.
4. Selecteer het hangslotpictogram om de sleutelhanger te vergrendelen. (Het hangslot wordt vergrendeld wanneer het proces is voltooid. Het kan een paar seconden duren, afhankelijk van welke apps je hebt geopend).

    ![Pictogram Hangslot](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Open Storage Explorer.
6. U wordt gevraagd met een bericht als 'Servicehub wil toegang krijgen tot de sleutelhanger'. Voer het wachtwoord van uw Mac-beheerdersaccount in en selecteer **Altijd toestaan** (of **Toestaan** als **altijd toestaan** niet beschikbaar is).
7. Probeer je aan te melden.

### <a name="general-sign-in-troubleshooting-steps"></a>Stappen voor algemene aanmeldingsprobleemoppas

* Als u macOS bevindt en het aanmeldingsvenster nooit wordt weergegeven boven het dialoogvenster **Wachten op verificatie,** probeert u [deze stappen](#mac-keychain-errors).
* Start Storage Explorer opnieuw.
* Als het verificatievenster leeg is, wacht u ten minste één minuut voordat u het dialoogvenster verificatie sluit.
* Zorg ervoor dat uw proxy- en certificaatinstellingen correct zijn geconfigureerd voor zowel uw machine als Storage Explorer.
* Als u Windows gebruikt en toegang hebt tot Visual Studio 2019 op dezelfde machine en tot de aanmeldingsreferenties, probeert u zich aan te melden bij Visual Studio 2019. Na een succesvolle aanmelding bij Visual Studio 2019 kun je Storage Explorer openen en je account bekijken in het accountpaneel.

Als geen van deze methoden werkt, [opent u een probleem in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Ontbrekende abonnementen en verbroken huurders

Als u uw abonnementen niet ophalen nadat u zich hebt aangemeld, probeert u de volgende methoden voor probleemoplossing:

* Controleer of uw account toegang heeft tot de abonnementen die u verwacht. U uw toegang verifiëren door u aan te melden bij de portal voor de Azure-omgeving die u probeert te gebruiken.
* Zorg ervoor dat u zich hebt aangemeld via de juiste Azure-omgeving (Azure, Azure China 21Vianet, Azure Germany, Azure US Government of Custom Environment).
* Als u achter een proxyserver zit, controleert u of u de proxy van Storage Explorer correct hebt geconfigureerd.
* Probeer het account te verwijderen en opnieuw toe te voegen.
* Als er een koppeling 'Meer informatie' is, controleert u welke foutberichten worden gerapporteerd voor de tenants die het niet hebben. Als u niet zeker weet hoe u op de foutberichten moet reageren, u [een probleem openen in GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues)

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Kan een bijgevoegd account of opslagbron niet verwijderen

Als u een gekoppeld account of opslagbron niet verwijderen via de gebruikersinterface, u alle gekoppelde bronnen handmatig verwijderen door de volgende mappen te verwijderen:

* Windows:`%AppData%/StorageExplorer`
* Macos:`/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux:`~/.config/StorageExplorer`

> [!NOTE]
> Sluit Storage Explorer voordat u deze mappen verwijdert.

> [!NOTE]
> Als u ooit SSL-certificaten hebt geïmporteerd, maakt `certs` u een back-up van de inhoud van de map. Later u de back-up gebruiken om uw SSL-certificaten opnieuw te importeren.

## <a name="proxy-issues"></a>Proxyproblemen

Controleer eerst of de volgende gegevens die u hebt ingevoerd juist zijn:

* De proxy-URL en het poortnummer
* Gebruikersnaam en wachtwoord als de proxy dit vereist

> [!NOTE]
> Storage Explorer biedt geen ondersteuning voor automatische proxy-config-bestanden voor het configureren van proxy-instellingen.

### <a name="common-solutions"></a>Gemeenschappelijke oplossingen

Als er nog steeds problemen zijn, probeert u de volgende methoden voor het oplossen van problemen:

* Als u verbinding maken met internet zonder uw proxy te gebruiken, controleert u of Storage Explorer werkt zonder dat proxy-instellingen zijn ingeschakeld. Als dit het geval is, kan er een probleem zijn met uw proxy-instellingen. Werk samen met uw beheerder om de problemen te identificeren.
* Controleer of andere toepassingen die de proxyserver gebruiken, werken zoals verwacht.
* Controleer of u verbinding maken met de portal voor de Azure-omgeving die u probeert te gebruiken.
* Controleer of u reacties ontvangen van uw serviceeindpunten. Voer een van uw eindpunt-URL's in uw browser in. Als u verbinding maken, ontvangt u InvalidQueryParameterValue of een vergelijkbaar XML-antwoord.
* Als iemand anders ook Storage Explorer gebruikt met uw proxyserver, controleert u of deze persoon verbinding kan maken. Als dat mogelijk is, moet u mogelijk contact opnemen met uw proxyserverbeheerder.

### <a name="tools-for-diagnosing-issues"></a>Hulpmiddelen voor het diagnosticeren van problemen

Als u netwerkprogramma's hebt, zoals Fiddler voor Windows, u de problemen als volgt diagnosticeren:

* Als u via uw proxy moet werken, moet u mogelijk uw netwerkprogramma configureren om verbinding te maken via de proxy.
* Controleer het poortnummer dat wordt gebruikt door uw netwerktool.
* Voer de url van de lokale host en het poortnummer van het netwerkprogramma in als proxy-instellingen in Storage Explorer. Wanneer u dit correct doet, begint uw netwerktool netwerkaanvragen van Storage Explorer te registreren voor beheer- en serviceeindpunten. Voer bijvoorbeeld `https://cawablobgrs.blob.core.windows.net/` voor uw blob-eindpunt in een browser in en u ontvangt een antwoord dat lijkt op het volgende:

  ![Codevoorbeeld](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Dit antwoord suggereert dat de bron bestaat, ook al hebt u er geen toegang toe.

### <a name="contact-proxy-server-admin"></a>Contactpersoon voor proxyserverbeheerder

Als uw proxy-instellingen juist zijn, moet u mogelijk contact opnemen met de beheerder van de proxyserver om:

* Zorg ervoor dat uw proxy geen verkeer blokkeert naar Azure-beheer- of resourceeindpunten.
* Controleer het verificatieprotocol dat door uw proxyserver wordt gebruikt. Storage Explorer biedt momenteel geen ondersteuning voor NTLM-proxy's.

## <a name="unable-to-retrieve-children-error-message"></a>Foutbericht 'Kan kinderen niet ophalen'

Als u via een proxy verbonden bent met Azure, controleert u of uw proxy-instellingen correct zijn. Als u toegang krijgt tot een bron van de eigenaar van het abonnement of account, controleert u of u machtigingen voor die bron hebt gelezen of vermeldt.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Verbindingstekenreeks heeft geen volledige configuratie-instellingen

Als u dit foutbericht ontvangt, is het mogelijk dat u niet over de benodigde machtigingen beschikt om de sleutels voor uw opslagaccount te verkrijgen. Ga naar de portal en zoek uw opslagaccount om te bevestigen dat dit het geval is. U dit doen door met de rechtermuisknop op het knooppunt voor uw opslagaccount te klikken en **Openen in Portal**te selecteren. Ga vervolgens naar het **blade van Access Keys.** Als u geen machtigingen hebt om sleutels te bekijken, ziet u een bericht 'U hebt geen toegang'. Om dit probleem te omzeilen, u de accountsleutel van iemand anders verkrijgen en de naam en sleutel koppelen, of u iemand om een SAS vragen bij het opslagaccount en deze gebruiken om het opslagaccount te koppelen.

Als u de accountsleutels ziet, dient u een probleem op in GitHub, zodat we u kunnen helpen het probleem op te lossen.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Fout opgetreden tijdens het toevoegen van nieuwe verbinding: TypeError: Kan eigenschap 'versie' van niet-gedefinieerd niet lezen

Als u dit foutbericht ontvangt wanneer u een aangepaste verbinding probeert toe te voegen, kunnen de verbindingsgegevens die zijn opgeslagen in de lokale referentiemanager, beschadigd zijn. Als u dit probleem wilt oplossen, probeert u uw beschadigde lokale verbindingen te verwijderen en voegt u deze opnieuw toe:

1. Storage Explorer starten. Ga in het menu naar **Hulpmiddelen** > **voor ontwikkelaars helpen.**
2. Ga in het geopende venster op het tabblad **Toepassing** naar **Lokale opslag** (linkerkant) > **file://**.
3. Afhankelijk van het type verbinding waarmee u een probleem hebt, zoekt u naar de sleutel en kopieert u de waarde ervan naar een teksteditor. De waarde is een array van uw aangepaste verbindingsnamen, zoals:
    * Opslagaccounts
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Blobcontainers
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Bestandsshares
        * `StorageExplorer_CustomConnections_Files_v1`
    * Wachtrijen
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabellen
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Nadat u uw huidige verbindingsnamen hebt opgeslagen, `[]`stelt u de waarde in Hulpmiddelen voor ontwikkelaars in op .

Als u de verbindingen wilt behouden die niet beschadigd zijn, u de volgende stappen gebruiken om de beschadigde verbindingen te zoeken. Als u het niet erg vindt om alle bestaande verbindingen te verliezen, u deze stappen overslaan en de platformspecifieke instructies volgen om uw verbindingsgegevens te wissen.

1. Voeg vanuit een teksteditor elke verbindingsnaam opnieuw toe aan Hulpmiddelen voor ontwikkelaars en controleer vervolgens of de verbinding nog werkt.
2. Als een verbinding correct werkt, is deze niet beschadigd en u deze veilig laten staan. Als een verbinding niet werkt, verwijdert u de waarde ervan uit hulpprogramma's voor ontwikkelaars en neemt u deze op zodat u deze later toevoegen.
3. Herhaal dit totdat u al uw verbindingen hebt onderzocht.

Nadat u al uw verbindingen hebt doorlopen, moet u voor alle verbindingen namen die niet zijn toegevoegd, hun beschadigde gegevens wissen (als die er zijn) en deze weer toevoegen met behulp van de standaardstappen in Storage Explorer:

# <a name="windows"></a>[Windows](#tab/Windows)

1. Zoek in het menu **Start** naar **Referentiebeheer** en open deze.
2. Ga naar **Windows-referenties**.
3. Zoek **onder Algemene referenties**naar items `<connection_type_key>/<corrupted_connection_name>` met de sleutel `StorageExplorer_CustomConnections_Accounts_v1/account1`(bijvoorbeeld ).
4. Verwijder deze vermeldingen en voeg de verbindingen opnieuw toe.

# <a name="macos"></a>[Macos](#tab/macOS)

1. Open Spotlight (Command+Spacebar) en zoek **naar toegang tot sleutelhangers**.
2. Zoek naar items die `<connection_type_key>/<corrupted_connection_name>` de sleutel `StorageExplorer_CustomConnections_Accounts_v1/account1`hebben (bijvoorbeeld).
3. Verwijder deze vermeldingen en voeg de verbindingen opnieuw toe.

# <a name="linux"></a>[Linux](#tab/Linux)

Lokaal referentiebeheer is afhankelijk van de Linux-distributie. Als uw Linux-distributie geen ingebouwde GUI-tool biedt voor lokaal referentiebeheer, u een hulpprogramma van derden installeren om uw lokale referenties te beheren. U bijvoorbeeld [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), een open-source GUI-tool gebruiken voor het beheren van lokale Linux-referenties.

1. Open uw hulpmiddel voor het beheer van lokale referenties en vind uw opgeslagen referenties.
2. Zoek naar items die `<connection_type_key>/<corrupted_connection_name>` de sleutel `StorageExplorer_CustomConnections_Accounts_v1/account1`hebben (bijvoorbeeld).
3. Verwijder deze vermeldingen en voeg de verbindingen opnieuw toe.
---

Als u deze fout nog steeds tegenkomt nadat u deze stappen hebt uitgevoerd, of als u wilt delen wat u vermoedt dat de verbindingen heeft beschadigd, [opent u een probleem](https://github.com/microsoft/AzureStorageExplorer/issues) op onze GitHub-pagina.

## <a name="issues-with-sas-url"></a>Problemen met de SAS-URL

Als u verbinding maakt met een service via een SAS-URL en er een fout optreedt:

* Controleer of de URL de benodigde machtigingen biedt om bronnen te lezen of te vermelden.
* Controleer of de URL niet is verlopen.
* Als de SAS-URL is gebaseerd op een toegangsbeleid, controleert u of het toegangsbeleid niet is ingetrokken.

Als u per ongeluk een ongeldige SAS-URL hebt gebruikt en nu niet kan worden losgekoppeld, voert u de volgende stappen uit:

1. Wanneer u Storage Explorer uitvoert, drukt u op F12 om het venster Hulpmiddelen voor ontwikkelaars te openen.
2. Selecteer op het tabblad **Toepassing** de file:// **lokale opslag** > **in** de structuur aan de linkerkant.
3. Zoek de sleutel die is gekoppeld aan het servicetype van de problematische SAS URI. Als de slechte SAS URI bijvoorbeeld voor een blobcontainer `StorageExplorer_AddStorageServiceSAS_v1_blob`is, zoekt u naar de sleutel met de naam .
4. De waarde van de sleutel moet een JSON-array zijn. Zoek het object dat is gekoppeld aan de slechte URI en verwijder het.
5. Druk op Ctrl+R om Storage Explorer opnieuw te laden.

## <a name="linux-dependencies"></a>Linux-afhankelijkheden

Storage Explorer 1.10.0 en hoger is in een handomdraai verkrijgbaar in de Snap Store. De module Storage Explorer installeert alle afhankelijkheden automatisch en wordt bijgewerkt wanneer een nieuwe versie van de module beschikbaar is. Het installeren van de Storage Explorer-module is de aanbevolen installatiemethode.

Storage Explorer vereist het gebruik van een wachtwoordmanager, die u mogelijk handmatig moet verbinden voordat Storage Explorer correct werkt. U Storage Explorer verbinden met de wachtwoordmanager van uw systeem door de volgende opdracht uit te voeren:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Je de applicatie ook downloaden als een .tar.gz-bestand, maar je moet afhankelijkheden handmatig installeren.

> [!IMPORTANT]
> Storage Explorer zoals voorzien in de .tar.gz download wordt alleen ondersteund voor Ubuntu-distributies. Andere distributies zijn niet geverifieerd en vereisen mogelijk alternatieve of aanvullende pakketten.

Deze pakketten zijn de meest voorkomende vereisten voor Storage Explorer op Linux:

* [.NET Core 2.2 Runtime](/dotnet/core/install/dependencies?tabs=netcore22&pivots=os-linux)
* `libgconf-2-4`
* `libgnome-keyring0` of `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Voor Storage Explorer-versie 1.7.0 en eerder is .NET Core 2.0 vereist. Als u een nieuwere versie van .NET Core hebt geïnstalleerd, moet u [Storage Explorer patchen.](#patching-storage-explorer-for-newer-versions-of-net-core) Als u Storage Explorer 1.8.0 of hoger gebruikt, u maximaal .NET Core 2.2 gebruiken. Versies na 2.2 zijn op dit moment niet geverifieerd om te werken.

# <a name="ubuntu-1904"></a>[Ubuntu 19.04](#tab/1904)

1. Download Storage Explorer.
2. Installeer de [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Voer de volgende opdracht uit:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Download Storage Explorer.
2. Installeer de [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Voer de volgende opdracht uit:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Download Storage Explorer.
2. Installeer de [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Voer de volgende opdracht uit:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404"></a>[Ubuntu 14.04](#tab/1404)

1. Download Storage Explorer.
2. Installeer de [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Voer de volgende opdracht uit:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Patching Storage Explorer voor nieuwere versies van .NET Core

Voor Storage Explorer 1.7.0 of eerder moet u mogelijk de versie van .NET Core patchen die wordt gebruikt door Storage Explorer:

1. Download versie 1.5.43 van StreamJsonRpc [van NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Kijk voor de "Download pakket" link aan de rechterkant van de pagina.
2. Nadat u het pakket hebt gedownload, wijzigt u de bestandsextensie van `.nupkg` . `.zip`
3. Rits het pakket uit.
4. Open de map `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopiëren `StreamJsonRpc.dll` naar de volgende locaties in de map Storage Explorer:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>'Openen in Explorer' vanuit de Azure-portal werkt niet

Als de knop **Openen in Explorer** op de Azure-portal niet werkt, controleert u of u een compatibele browser gebruikt. De volgende browsers zijn getest op compatibiliteit:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Volgende stappen

Als geen van deze oplossingen voor u werkt, [opent u een probleem in GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues) U dit ook doen door de **knop Rapportnummer naar GitHub** in de linkerbenedenhoek te selecteren.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)
