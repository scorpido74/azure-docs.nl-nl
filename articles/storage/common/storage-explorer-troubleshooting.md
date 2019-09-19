---
title: Gids voor het oplossen van problemen met Azure Storage Explorer | Microsoft Docs
description: Overzicht van technieken voor fout opsporing voor Azure Storage Explorer
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: ad73520c99dd5418fd4444b3f41d7d1e22f8d52f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090834"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Gids voor het oplossen van problemen Azure Storage Explorer

Microsoft Azure Storage Explorer is een zelfstandige app waarmee u eenvoudig kunt werken met Azure Storage gegevens in Windows, macOS en Linux. De app kan verbinding maken met opslag accounts die worden gehost op Azure, nationale Clouds en Azure Stack.

In deze hand leiding vindt u een overzicht van oplossingen voor problemen die vaak worden weer gegeven in Storage Explorer.

## <a name="rbac-permissions-issues"></a>Problemen met RBAC-machtigingen

Op rollen gebaseerd toegangs beheer is een zeer gedetailleerd toegangs beheer van Azure-resources [door het combi](https://docs.microsoft.com/azure/role-based-access-control/overview) neren van machtigingen sets in _rollen_. Hier volgen enkele strategieën om RBAC optimaal te laten werken in Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Heb ik toegang tot mijn resources in Storage Explorer Hoe kan ik?

Als u problemen ondervindt bij het openen van opslag bronnen via RBAC, hebt u mogelijk niet de juiste rollen toegewezen. In de volgende secties worden de machtigingen beschreven Storage Explorer momenteel vereist voor toegang tot uw opslag resources. Neem contact op met de beheerder van uw Azure-account als u niet zeker weet of u de juiste rollen of machtigingen hebt.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Wie Probleem met machtigingen voor lijst/ophalen van opslag account (s)

U moet gemachtigd zijn om opslag accounts weer te geven. Als u deze machtiging wilt ontvangen, moet u de rol van _lezer_ toewijzen.

#### <a name="list-storage-account-keys"></a>Sleutels van opslag account weer geven

Storage Explorer kunt ook account sleutels gebruiken om aanvragen te verifiëren. U kunt toegang krijgen tot account sleutels via krachtigere rollen, zoals de rol _Inzender_ .

> [!NOTE]
> Met toegangs sleutels worden onbeperkte machtigingen verleend aan iedereen die deze bevat. Daarom raden we u aan deze sleutels niet te leveren aan gebruikers. Als u toegangs sleutels wilt intrekken, kunt u ze opnieuw genereren op basis van de [Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Gegevens rollen

U moet ten minste één rol toewijzen die toegang verleent tot het lezen van gegevens uit resources. Als u bijvoorbeeld blobs wilt weer geven of downloaden, hebt u Mini maal de rol _Storage BLOB data Reader_ nodig.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Waarom heb ik een rol van beheer laag nodig om mijn resources in Storage Explorer te zien?

Azure Storage heeft twee toegangs niveaus: _beheer_ en _gegevens_. Abonnementen en opslag accounts zijn toegankelijk via de Management-laag. Containers, blobs en andere gegevens bronnen worden geopend via de gegevenslaag. Als u bijvoorbeeld een lijst wilt ophalen van uw opslag accounts van Azure, verzendt u een aanvraag naar het beheer eindpunt. Als u een lijst met Blob-containers in een account wilt, verzendt u een aanvraag naar het juiste service-eind punt.

RBAC-rollen kunnen machtigingen voor beheer-of gegevenslaag toegang bevatten. De rol van lezer geeft bijvoorbeeld alleen-lezen toegang tot management Layer-resources.

De rol van lezer biedt geen gegevenslaag machtigingen en is niet nodig om toegang te krijgen tot de gegevenslaag.

Met Storage Explorer kunt u eenvoudig toegang krijgen tot uw resources door de gegevens te verzamelen die nodig zijn om verbinding te maken met uw Azure-resources. Als u bijvoorbeeld uw BLOB-containers wilt weer geven, verzendt Storage Explorer een aanvraag ' lijst containers ' naar het eind punt van de BLOB-service. Storage Explorer zoekt in de lijst met abonnementen en opslag accounts waartoe u toegang hebt om dit eind punt op te halen. Als u uw abonnementen en opslag accounts wilt vinden, moet Storage Explorer ook toegang hebben tot de Management laag.

Als u geen rol hebt die machtigingen voor een beheer laag verleent, kan Storage Explorer de informatie die nodig is om verbinding te maken met de gegevenslaag niet ophalen.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Wat moet ik doen als ik de beheer laag machtigingen niet kan ophalen die ik nodig heb van mijn beheerder?

Er zijn momenteel geen RBAC-oplossing voor dit probleem. Als tijdelijke oplossing kunt u een SAS-URI aanvragen om [aan uw resource te koppelen](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fout: Zelfondertekend certificaat in de certificaat keten (en vergelijk bare fouten)

Certificaat fouten treden doorgaans op in een van de volgende situaties:

- De app is verbonden via een _transparante proxy_, wat betekent dat een server (zoals uw bedrijfs server) HTTPS-verkeer onderschept, ontsleutelt en vervolgens versleutelt met behulp van een zelfondertekend certificaat.
- U voert een toepassing uit die een zelfondertekend SSL-certificaat injecteert in de HTTPS-berichten die u ontvangt. Voor beelden van toepassingen die certificaten injecteren, zijn onder andere software voor de inspectie van virussen en netwerk verkeer.

Wanneer Storage Explorer een zelfondertekend of niet-vertrouwd certificaat ziet, weet het niet meer of het ontvangen HTTPS-bericht is gewijzigd. Als u een exemplaar van het zelfondertekende certificaat hebt, kunt u Storage Explorer om dit te vertrouwen door de volgende stappen uit te voeren:

1. Een met base 64 gecodeerde X. 509 (. CER)-kopie van het certificaat verkrijgen.
2. Ga naar **bewerken** > **SSL-certificaten** > **certificaten importeren**, en gebruik vervolgens de bestands kiezer om het. cer-bestand te zoeken, te selecteren en te openen.

Dit probleem kan ook optreden als er meerdere certificaten (root en tussenliggend) zijn. Om deze fout op te lossen, moeten beide certificaten worden toegevoegd.

Als u niet zeker weet waar het certificaat vandaan komt, volgt u deze stappen om het te vinden:

1. Installeer OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): Een van de lichte versies moet voldoende zijn.
    * Mac en Linux: Moet worden opgenomen in uw besturings systeem.
2. Voer OpenSSL uit.
    * Windows: Open de installatiemap, selecteer **/bin/** en dubbel klik vervolgens op **openssl. exe**.
    * Mac en Linux: Uitvoeren `openssl` vanaf een Terminal.
3. Voer `s_client -showcerts -connect microsoft.com:443` uit.
4. Zoek naar zelfondertekende certificaten. Als u niet zeker weet welke certificaten u zelf hebt ondertekend, noteert u de locatie van het `("s:")` onderwerp en de `("i:")` Uitgever.
5. Wanneer u zelfondertekende certificaten vindt, kopieert en plakt u alles uit (en inclusief) `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` in een nieuw CER-bestand.
6. Open Storage Explorer en ga naar**import certificaten**voor**SSL-certificaten** >  **bewerken** > . Gebruik vervolgens de bestands kiezer om de CER-bestanden die u hebt gemaakt, te zoeken, te selecteren en te openen.

Als u geen zelfondertekende certificaten kunt vinden door deze stappen te volgen, neemt u contact met ons op via het feedback programma. U kunt Storage Explorer ook openen vanaf de opdracht regel met behulp `--ignore-certificate-errors` van de vlag. Wanneer deze vlag wordt geopend, worden certificaat fouten Storage Explorer genegeerd.

## <a name="sign-in-issues"></a>Aanmeldingsproblemen

### <a name="blank-sign-in-dialog-box"></a>Het dialoog venster lege aanmelding

De dialoog vensters met een leeg aanmeld probleem worden meestal beschreven wanneer Active Directory Federation Services (AD FS) Storage Explorer om een omleiding uit te voeren, wat niet wordt ondersteund door elektron. U kunt dit probleem omzeilen door de apparaatcode stroom te gebruiken om u aan te melden. Voer hiertoe de volgende stappen uit:

1. Ga in het menu naar **voor beeld** > van**apparaatcode gebruiken aanmelden**.
2. Open het dialoog venster **verbinding maken** (via het pictogram met de plug-in de verticale balk aan de linkerkant of selecteer **account toevoegen** in het deel venster account).
3. Kies de omgeving waarin u zich wilt aanmelden.
4. Selecteer **Aanmelden**.
5. Volg de instructies op het volgende scherm.

Als u zich niet kunt aanmelden bij het account dat u wilt gebruiken omdat uw standaard browser al is aangemeld bij een ander account, voert u een van de volgende handelingen uit:

- Kopieer de koppeling en code hand matig naar een persoonlijke sessie van uw browser.
- Kopieer de koppeling en code hand matig naar een andere browser.

### <a name="reauthentication-loop-or-upn-change"></a>Herauthenticatie lus of UPN-wijziging

Voer de volgende stappen uit als u een lus voor opnieuw verifiëren hebt of als u de UPN van een van uw accounts hebt gewijzigd:

1. Verwijder alle accounts en sluit Storage Explorer.
2. Verwijder de. De map IdentityService van uw computer. In Windows bevindt de map zich `C:\users\<username>\AppData\Local`op. Voor Mac en Linux vindt u de map in de hoofdmap van de gebruikers lijst.
3. Als u Mac of Linux uitvoert, moet u ook de vermelding micro soft. developer. IdentityService verwijderen uit de opslag van uw besturings systeem. Op de Mac is het sleutel archief de *gnome-keten* toepassing. In Linux wordt de toepassing meestal _sleutel hanger_genoemd, maar de naam kan variëren, afhankelijk van uw distributie.

### <a name="conditional-access"></a>Voorwaardelijke toegang

Vanwege een beperking in de Azure AD-bibliotheek die wordt gebruikt door Storage Explorer, wordt voorwaardelijke toegang niet ondersteund wanneer Storage Explorer wordt gebruikt in Windows 10, Linux of macOS.

## <a name="mac-keychain-errors"></a>Mac-sleutel keten fouten

De macOS-sleutel hanger kan soms een status invoeren die problemen veroorzaakt voor de Storage Explorer-verificatie bibliotheek. Voer de volgende stappen uit om de sleutel hanger uit deze staat te halen:

1. Storage Explorer sluiten.
2. Open sleutel hanger (druk op Command + spatie balk, typ **sleutel keten**en druk op ENTER).
3. Selecteer de sleutel hanger aanmelden.
4. Selecteer het hang slot pictogram om de sleutel hanger te vergren delen. (Het hang slot wordt vergrendeld weer gegeven wanneer het proces is voltooid. Het kan een paar seconden duren, afhankelijk van de apps die u hebt geopend.

    ![Pictogram hang slot](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Open Storage Explorer.
6. U wordt gevraagd een bericht te ontvangen met de melding ' service hub heeft toegang tot de sleutel keten? ' Voer het wacht woord voor uw Mac-beheerders account in en selecteer **altijd toestaan** (of **toestaan** als de optie **altijd toestaan** niet beschikbaar is).
7. Probeer u aan te melden.

### <a name="general-sign-in-troubleshooting-steps"></a>Algemene stappen voor het oplossen van problemen met aanmelding

* Als u zich in macOS bevindt en het aanmeld venster nooit wordt weer gegeven in het dialoog venster **wachten op verificatie** , voert u de [volgende stappen uit](#mac-keychain-errors).
* Start Storage Explorer opnieuw.
* Als het verificatie venster leeg is, wacht u ten minste één minuut voordat u het dialoog venster verificatie sluit.
* Zorg ervoor dat uw proxy-en certificaat instellingen juist zijn geconfigureerd voor zowel uw computer als Storage Explorer.
* Als u Windows gebruikt en toegang hebt tot Visual Studio 2019 op dezelfde computer en de aanmeldings referenties, probeert u zich aan te melden bij Visual Studio 2019. Nadat u zich hebt aangemeld bij Visual Studio 2019, kunt u Storage Explorer openen en uw account bekijken in het deel venster account.

Als geen van deze methoden werkt, [opent u een probleem in github](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Ontbrekende abonnementen en verbroken tenants

Als u uw abonnementen niet kunt ophalen nadat u zich hebt aangemeld, kunt u de volgende probleemoplossings methoden proberen:

* Controleer of uw account toegang heeft tot de abonnementen die u verwacht. U kunt uw toegang controleren door u aan te melden bij de portal voor de Azure-omgeving die u wilt gebruiken.
* Zorg ervoor dat u zich hebt aangemeld via de juiste Azure-omgeving (Azure, Azure-China 21Vianet, Azure Duitsland, Azure Amerikaanse overheid of aangepaste omgeving).
* Als u zich achter een proxy server bevindt, moet u ervoor zorgen dat u de Storage Explorer proxy op de juiste wijze hebt geconfigureerd.
* Probeer het account te verwijderen en opnieuw toe te voegen.
* Als er een koppeling meer informatie is, controleert u welke fout berichten worden gerapporteerd voor de tenants die zijn mislukt. Als u niet zeker weet hoe u kunt reageren op de fout berichten, kunt u [een probleem openen in github](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Kan een gekoppeld account of opslag resource niet verwijderen

Als u een gekoppeld account of opslag resource niet kunt verwijderen via de gebruikers interface, kunt u alle gekoppelde resources hand matig verwijderen door de volgende mappen te verwijderen:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Sluit Storage Explorer voordat u deze mappen verwijdert.

> [!NOTE]
> Als u ooit SSL-certificaten hebt geïmporteerd, maakt u een back-up `certs` van de inhoud van de map. Later kunt u de back-up gebruiken om uw SSL-certificaten opnieuw te importeren.

## <a name="proxy-issues"></a>Proxy problemen

Controleer eerst of de volgende informatie die u hebt ingevoerd juist is:

* De proxy-URL en het poort nummer
* Gebruikers naam en wacht woord als de proxy deze vereist

> [!NOTE]
> Storage Explorer biedt geen ondersteuning voor automatische proxy-configuratie bestanden voor het configureren van proxy-instellingen.

### <a name="common-solutions"></a>Algemene oplossingen

Als u nog steeds problemen ondervindt, kunt u de volgende probleemoplossings methoden proberen:

* Als u verbinding kunt maken met internet zonder uw proxy te gebruiken, controleert u of Storage Explorer werkt zonder dat er proxy instellingen zijn ingeschakeld. Als dit het geval is, is er mogelijk een probleem met de proxy-instellingen. Werk samen met uw beheerder om de problemen te identificeren.
* Controleer of andere toepassingen die gebruikmaken van de proxy server naar verwachting werken.
* Controleer of u verbinding kunt maken met de portal voor de Azure-omgeving die u wilt gebruiken.
* Controleer of u antwoorden kunt ontvangen van uw service-eind punten. Voer een van uw eind punt-Url's in uw browser in. Als u verbinding kunt maken, ontvangt u InvalidQueryParameterValue of een soortgelijk XML-antwoord.
* Als iemand anders ook gebruikmaakt van Storage Explorer met uw proxy server, controleert u of ze verbinding kunnen maken. Als dat mogelijk is, moet u contact opnemen met de beheerder van de proxy server.

### <a name="tools-for-diagnosing-issues"></a>Hulpprogram ma's voor het oplossen van problemen

Als u hulpprogram ma's voor netwerken hebt, zoals Fiddler voor Windows, kunt u de problemen als volgt vaststellen:

* Als u uw proxy moet gebruiken, moet u mogelijk uw netwerk hulpprogramma configureren om verbinding te maken via de proxy.
* Controleer het poort nummer dat wordt gebruikt door uw netwerk programma.
* Voer de URL van de lokale host en het poort nummer van het netwerk hulpprogramma in als proxy-instellingen in Storage Explorer. Wanneer u dit op de juiste wijze uitvoert, start het netwerk hulpprogramma het registreren van netwerk aanvragen van Storage Explorer naar beheer-en service-eind punten. Voer `https://cawablobgrs.blob.core.windows.net/` bijvoorbeeld in voor uw BLOB-eind punt in een browser en ontvang een antwoord dat er ongeveer als volgt uitziet:

  ![Codevoorbeeld](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Dit antwoord geeft aan dat de resource bestaat, ook al hebt u er geen toegang toe.

### <a name="contact-proxy-server-admin"></a>Beheerder van de proxy server

Als uw proxy-instellingen juist zijn, moet u wellicht contact opnemen met de beheerder van uw proxy server voor het volgende:

* Zorg ervoor dat uw proxy geen verkeer naar Azure-beheer-of resource-eind punten blokkeert.
* Controleer het verificatie protocol dat door uw proxy server wordt gebruikt. Storage Explorer ondersteunt momenteel geen NTLM-proxy's.

## <a name="unable-to-retrieve-children-error-message"></a>Fout bericht ' kan geen onderliggende items ophalen '

Als u met Azure bent verbonden via een proxy, controleert u of de proxy-instellingen juist zijn. Als u toegang hebt tot een resource van de eigenaar van het abonnement of het account, controleert u of u lees-of lijst machtigingen voor die resource hebt.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>De verbindings reeks heeft geen volledige configuratie-instellingen

Als dit fout bericht wordt weer gegeven, is het mogelijk dat u niet over de benodigde machtigingen beschikt om de sleutels voor uw opslag account te verkrijgen. Om te bevestigen dat dit het geval is, gaat u naar de portal en zoekt u naar uw opslag account. U kunt dit doen door met de rechter muisknop op het knoop punt voor uw opslag account te klikken en **openen in portal**te selecteren. Ga vervolgens naar de Blade **toegangs sleutels** . Als u geen machtigingen hebt om sleutels weer te geven, ziet u het bericht ' u hebt geen toegang '. U kunt dit probleem omzeilen door de account sleutel van iemand anders te verkrijgen en de naam en sleutel te koppelen, of u kunt iemand voor een SAS vragen aan het opslag account en deze gebruiken om het opslag account te koppelen.

Als u de account sleutels ziet, moet u een probleem in GitHub oplossen zodat we u kunnen helpen het probleem op te lossen.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Er is een fout opgetreden tijdens het toevoegen van een nieuwe verbinding: TypeError: Kan de eigenschap Version niet lezen van niet gedefinieerd

Als dit fout bericht wordt weer gegeven wanneer u een aangepaste verbinding probeert toe te voegen, zijn de verbindings gegevens die zijn opgeslagen in de lokale referentie beheerder mogelijk beschadigd. U kunt dit probleem omzeilen door de beschadigde lokale verbindingen te verwijderen en vervolgens opnieuw toe te voegen:

1. Start Storage Explorer. Ga in het menu naar **Help** > **in-/uitschakelen Ontwikkelhulpprogramma's**.
2. Ga in het geopende venster op het tabblad **toepassing** naar **lokale opslag** (links) > **File://** .
3. Afhankelijk van het type verbinding waarmee u een probleem ondervindt, zoekt u de sleutel en kopieert u de waarde ervan naar een tekst editor. De waarde is een matrix met uw aangepaste verbindings namen, zoals de volgende:
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
4. Nadat u de huidige verbindings namen hebt opgeslagen, stelt u de waarde in `[]`Ontwikkelhulpprogramma's in op.

Als u de niet-beschadigde verbindingen wilt behouden, kunt u de volgende stappen gebruiken om de beschadigde verbindingen te vinden. Als u geen enkele bestaande verbinding verliest, kunt u deze stappen overs Laan en de specifieke instructies voor het verwijderen van uw verbindings gegevens volgen.

1. Voeg in een tekst editor elke verbindings naam opnieuw toe aan Ontwikkelhulpprogramma's en controleer vervolgens of de verbinding nog steeds werkt.
2. Als een verbinding correct werkt, is deze niet beschadigd en kunt u deze daar veilig laten staan. Als een verbinding niet werkt, verwijdert u de waarde uit Ontwikkelhulpprogramma's en registreert u deze zodat u deze later opnieuw kunt toevoegen.
3. Herhaal deze stap totdat u al uw verbindingen hebt gecontroleerd.

Nadat u alle verbindingen hebt door lopen, moet u voor alle verbindings namen die niet meer worden toegevoegd, de beschadigde gegevens wissen (indien aanwezig) en deze opnieuw toevoegen met behulp van de standaard stappen in Storage Explorer:

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

1. Zoek in het menu **Start** naar **referentie beheer** en open het.
2. Ga naar **Windows-referenties**.
3. Onder **algemene referenties**zoekt u naar vermeldingen die de `<connection_type_key>/<corrupted_connection_name>` sleutel hebben `StorageExplorer_CustomConnections_Accounts_v1/account1`(bijvoorbeeld).
4. Verwijder deze vermeldingen en voeg de verbindingen opnieuw toe.

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

1. Open Spotlight (Command + spatie balk) en zoek naar **sleutel hanger toegang**.
2. Zoek naar vermeldingen die de `<connection_type_key>/<corrupted_connection_name>` sleutel hebben ( `StorageExplorer_CustomConnections_Accounts_v1/account1`bijvoorbeeld).
3. Verwijder deze vermeldingen en voeg de verbindingen opnieuw toe.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

Het lokale referentie beheer varieert afhankelijk van de Linux-distributie. Als uw Linux-distributie geen ingebouwd GUI-hulp programma biedt voor lokaal referentie beheer, kunt u een hulp programma van derden installeren om uw lokale referenties te beheren. U kunt bijvoorbeeld [seahorse](https://wiki.gnome.org/Apps/Seahorse/)gebruiken, een open-source GUI-hulp programma voor het beheren van lokale referenties voor Linux.

1. Open het hulp programma lokaal referentie beheer en zoek uw opgeslagen referenties.
2. Zoek naar vermeldingen die de `<connection_type_key>/<corrupted_connection_name>` sleutel hebben ( `StorageExplorer_CustomConnections_Accounts_v1/account1`bijvoorbeeld).
3. Verwijder deze vermeldingen en voeg de verbindingen opnieuw toe.
---

Als deze fout blijft optreden nadat u deze stappen hebt uitgevoerd, of als u wilt delen wat er vermoedt dat de verbindingen zijn beschadigd, [opent u een probleem](https://github.com/microsoft/AzureStorageExplorer/issues) op onze github-pagina.

## <a name="issues-with-sas-url"></a>Problemen met SAS-URL

Als u verbinding maakt met een service via een SAS-URL en er zich een fout voordoet:

* Controleer of de URL de benodigde machtigingen heeft om resources te lezen of weer te geven.
* Controleer of de URL niet is verlopen.
* Als de SAS-URL is gebaseerd op een toegangs beleid, controleert u of het toegangs beleid niet is ingetrokken.

Als u per ongeluk een ongeldige SAS-URL hebt aangesloten en nu niet kunt ontkoppelen, voert u de volgende stappen uit:

1. Wanneer u Storage Explorer uitvoert, drukt u op F12 om het venster Ontwikkelhulpprogramma's te openen.
2. Selecteer op het tabblad **toepassing** de optie **lokale opslag** > **File://** in de structuur aan de linkerkant.
3. Zoek de sleutel die is gekoppeld aan het Service type van de problematische SAS-URI. Als de beschadigde SAS-URI bijvoorbeeld voor een BLOB-container is, zoekt u naar de sleutel `StorageExplorer_AddStorageServiceSAS_v1_blob`met de naam.
4. De waarde van de sleutel moet een JSON-matrix zijn. Zoek het object dat is gekoppeld aan de ongeldige URI en verwijder het.
5. Druk op CTRL + R om Storage Explorer opnieuw te laden.

## <a name="linux-dependencies"></a>Linux-afhankelijkheden

Storage Explorer 1.10.0 en hoger is beschikbaar als een module van de snap Store. Met de Storage Explorer-uitlijning worden alle afhankelijkheden automatisch geïnstalleerd en wordt deze bijgewerkt wanneer er een nieuwe versie van de magneet beschikbaar is. Het installeren van de Storage Explorer-module is de aanbevolen installatie methode.

Voor Storage Explorer is het gebruik van een wachtwoord beheerder vereist. u moet mogelijk hand matig verbinding maken voordat Storage Explorer goed werkt. U kunt Storage Explorer verbinding maken met het wachtwoord beheer van uw systeem door de volgende opdracht uit te voeren:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

U kunt de toepassing ook downloaden als een. tar. gz-bestand, maar u moet afhankelijkheden hand matig installeren.

> [!IMPORTANT]
> Storage Explorer zoals gegeven in de. tar. gz-down load wordt alleen ondersteund voor Ubuntu-distributies. Andere distributies zijn niet geverifieerd en kunnen alternatieve of extra pakketten vereisen.

Deze pakketten zijn de meest voorkomende vereisten voor Storage Explorer op Linux:

* [.NET Core 2,0-runtime](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` of `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Voor Storage Explorer versie 1.7.0 en eerder is .NET Core 2,0 vereist. Als u een nieuwere versie van .NET core hebt geïnstalleerd, moet u een [patch Storage Explorer](#patching-storage-explorer-for-newer-versions-of-net-core). Als u Storage Explorer 1.8.0 of hoger gebruikt, kunt u gebruikmaken van tot .NET Core 2,2. Versies van meer dan 2,2 zijn op dit moment niet geverifieerd.

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19.04](#tab/1904)

1. Down load Storage Explorer.
2. Installeer de [.net core-runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Voer de volgende opdracht uit:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18,04](#tab/1804)

1. Down load Storage Explorer.
2. Installeer de [.net core-runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Voer de volgende opdracht uit:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. Down load Storage Explorer.
2. Installeer de [.net core-runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Voer de volgende opdracht uit:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Down load Storage Explorer.
2. Installeer de [.net core-runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Voer de volgende opdracht uit:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Patches Storage Explorer voor nieuwere versies van .NET core

Voor Storage Explorer 1.7.0 of eerder moet u de versie van .NET core die door Storage Explorer wordt gebruikt, wellicht patchen:

1. Down load versie 1.5.43 van StreamJsonRpc van [NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Zoek naar de koppeling ' pakket downloaden ' aan de rechter kant van de pagina.
2. Nadat u het pakket hebt gedownload, wijzigt u de bestands `.nupkg` extensie `.zip`van in.
3. Pak het pakket uit.
4. Open de map `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopieer `StreamJsonRpc.dll` naar de volgende locaties in de map Storage Explorer:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>' Openen in Verkenner ' van de Azure Portal werkt niet

Als de knop **openen in Verkenner** op de Azure Portal niet werkt, moet u ervoor zorgen dat u een compatibele browser gebruikt. De volgende browsers zijn getest op compatibiliteit:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Volgende stappen

Als geen van deze oplossingen voor u werkt, [opent u een probleem in github](https://github.com/Microsoft/AzureStorageExplorer/issues). U kunt dit ook doen door in de linkerbenedenhoek op de knop **probleem melden met github te** klikken.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)
