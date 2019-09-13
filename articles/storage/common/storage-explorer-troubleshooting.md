---
title: Gids voor het oplossen van problemen met Azure Storage Explorer | Microsoft Docs
description: Overzicht van technieken voor fout opsporing voor Azure Storage Explorer
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 69631b39403dedab56ed75cb145d464c0e1f747c
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935347"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Gids voor het oplossen van problemen Azure Storage Explorer

Microsoft Azure Storage Explorer is een zelfstandige app waarmee u eenvoudig met Azure Storage gegevens kunt werken in Windows, macOS en Linux. De app kan verbinding maken met opslag accounts die worden gehost op Azure, nationale Clouds en Azure Stack.

Deze hand leiding bevat een overzicht van oplossingen voor veelvoorkomende problemen die worden weer gegeven in Storage Explorer.

## <a name="role-based-access-control-permission-issues"></a>Op rollen gebaseerde Access Control machtigings problemen

[Op rollen gebaseerd toegangs beheer (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) biedt een nauw keurig toegangs beheer van Azure-resources door het combi neren van machtigingen sets in _rollen_. Hieronder vindt u enkele suggesties die u kunt volgen om RBAC te laten werken in Storage Explorer.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Wat heb ik nodig om mijn resources te zien in Storage Explorer?

Als u problemen ondervindt bij het openen van opslag resources met RBAC, kan dat zijn omdat u niet de juiste rollen hebt toegewezen. In de volgende secties worden de machtigingen beschreven Storage Explorer momenteel vereist voor toegang tot uw opslag resources.

Neem contact op met de beheerder van uw Azure-account als u niet zeker weet of u de juiste rollen of machtigingen hebt.

#### <a name="read-listget-storage-accounts"></a>Wie Opslag account (s) weer geven/ophalen

U moet gemachtigd zijn om opslag accounts weer te geven. U kunt deze machtiging krijgen door de rol ' lezer ' toe te wijzen.

#### <a name="list-storage-account-keys"></a>Opslagsleutels voor account opnemen in een lijst

Storage Explorer kunt ook account sleutels gebruiken om aanvragen te verifiëren. U kunt toegang krijgen tot sleutels met krachtigere rollen, zoals de rol ' Inzender '.

> [!NOTE]
> Met toegangs sleutels worden onbeperkte machtigingen verleend aan iedereen die deze bevat. Daarom wordt het over het algemeen niet aanbevolen dat ze worden overgedragen aan gebruikers van het account. Als u toegangs sleutels wilt intrekken, kunt u ze opnieuw genereren via [Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Gegevens rollen

U moet ten minste één rol toewijzen die toegang verleent tot gegevens uit resources. Als u bijvoorbeeld blobs wilt weer geven of downloaden, hebt u ten minste de rol Storage BLOB data Reader nodig.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Waarom heb ik een rol van beheer laag nodig om mijn resources in Storage Explorer te zien?

Azure Storage heeft twee toegangs niveaus: _beheer_ en _gegevens_. Abonnementen en opslag accounts zijn toegankelijk via de Management-laag. Containers, blobs en andere gegevens bronnen worden geopend via de gegevenslaag. Als u bijvoorbeeld een lijst wilt ophalen van uw opslag accounts van Azure, verzendt u een aanvraag naar het beheer eindpunt. Als u een lijst met Blob-containers in een account wilt, verzendt u een aanvraag naar het juiste service-eind punt.

RBAC-rollen kunnen machtigingen bevatten voor toegang tot beheer of gegevenslaag. Met de rol ' lezer ' krijgt u bijvoorbeeld alleen-lezen toegangs beheer laag bronnen.

De rol ' lezer ' biedt geen gegevenslaag machtigingen en is niet nodig om toegang te krijgen tot de gegevenslaag.

Met Storage Explorer kunt u eenvoudig toegang krijgen tot uw resources door de gegevens te verzamelen die nodig zijn om verbinding te maken met uw Azure-resources. Als u bijvoorbeeld uw BLOB-containers wilt weer geven, verzendt Storage Explorer een aanvraag lijst containers naar het eind punt van de BLOB-service. Storage Explorer zoekt in de lijst met abonnementen en opslag accounts waartoe u toegang hebt om dit eind punt op te halen. Om uw abonnementen en opslag accounts te vinden, moet Storage Explorer ook toegang hebben tot de laag van het beheer.

Als u geen rol hebt die machtigingen voor beheer lagen verleent, kan Storage Explorer de informatie die nodig is om verbinding te maken met de gegevenslaag niet ophalen.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Wat moet ik doen als ik de beheer laag machtigingen niet kan ophalen die ik nodig heb van mijn beheerder?

Er is op dit moment nog geen oplossing op het gebied van RBAC. Als tijdelijke oplossing kunt u een SAS-URI aanvragen om [aan uw resource te koppelen](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fout: Zelfondertekend certificaat in de certificaat keten (en vergelijk bare fouten)

Certificaat fouten worden veroorzaakt door een van de volgende twee situaties:

1. De app is verbonden via een ' transparante proxy ', wat betekent dat een server (zoals uw bedrijfs server) HTTPS-verkeer onderschept, ontsleutelt en vervolgens versleutelt met een zelfondertekend certificaat.
2. U voert een toepassing uit die een zelfondertekend SSL-certificaat injecteert in de HTTPS-berichten die u ontvangt. Voor beelden van toepassingen die certificaten invoeren, omvatten anti virus-en netwerk verkeer inspectie software.

Wanneer Storage Explorer een zelfondertekend of niet-vertrouwd certificaat ziet, kan het niet meer weten of het ontvangen HTTPS-bericht is gewijzigd. Als u een exemplaar van het zelfondertekende certificaat hebt, kunt u dit als volgt door geven Storage Explorer vertrouwen:

1. Een met base 64 gecodeerde X. 509 (. CER)-kopie van het certificaat verkrijgen
2. Klik op **bewerken** → **SSL-certificaten** → **certificaten importeren**, en gebruik vervolgens de bestands kiezer om het. cer-bestand te zoeken, selecteren en openen

Dit probleem kan ook het gevolg zijn van meerdere certificaten (basis en tussenliggend). Beide certificaten moeten worden toegevoegd om de fout te verholpen.

Als u niet zeker weet waar het certificaat vandaan komt, kunt u deze stappen proberen om het te vinden:

1. Installeer Open SSL
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (alle licht versies moeten voldoende zijn)
    * Mac en Linux: moeten zijn opgenomen in uw besturings systeem
2. Voer Open SSL uit
    * Windows: Open de installatiemap, klik op **/bin/** en dubbel klik vervolgens op **openssl. exe**.
    * Mac en Linux: Voer **openssl** uit vanaf een Terminal.
3. Voer `s_client -showcerts -connect microsoft.com:443` uit
4. Zoek naar zelfondertekende certificaten. Als u niet zeker weet welke certificaten zelfondertekend zijn, zoekt u naar een wille keurige `("s:")` plaats waar het `("i:")` onderwerp en de uitgever hetzelfde zijn.
5. Wanneer u zelfondertekende certificaten hebt gevonden, kopieert en plakt u alles uit en voegt u **-----begin certificaat-----** toe **-----eind certificaat-----** naar een nieuw CER-bestand.
6. Open Storage Explorer, klik op **bewerken** → **SSL-certificaten** → **certificaten importeren**en gebruik vervolgens de bestands kiezer om de CER-bestanden die u hebt gemaakt, te zoeken, te selecteren en te openen.

Als u geen zelfondertekende certificaten kunt vinden met behulp van de voor gaande stappen, neemt u contact met ons op via het feedback programma voor meer informatie. U kunt er ook voor kiezen om Storage Explorer te starten vanaf de opdracht `--ignore-certificate-errors` regel met de vlag. Wanneer deze vlag wordt gestart, worden certificaat fouten door Storage Explorer genegeerd.

## <a name="sign-in-issues"></a>Aanmeldingsproblemen

### <a name="blank-sign-in-dialog"></a>Dialoog venster voor leeg aanmelden

Lege aanmeldings dialoogvensters worden meestal veroorzaakt door ADFS die Storage Explorer vraagt om een omleiding uit te voeren, wat niet wordt ondersteund door elektron. U kunt dit probleem omzeilen door de code stroom van het apparaat te gebruiken voor aanmelding. Voer hiervoor de volgende stappen uit:

1. Snelmenu's Preview-> ' de aanmeldings code van het apparaat gebruiken '.
2. Open het dialoog venster verbinding maken (via het pictogram met de plug-in links op de verticale balk of ' account toevoegen ' in het deel venster account).
3. Kies de omgeving waarin u zich wilt aanmelden.
4. Klik op de knop aanmelden.
5. Volg de instructies op het volgende scherm.

Als u problemen ondervindt bij het aanmelden van het account dat u wilt gebruiken omdat uw standaard browser al is aangemeld bij een ander account, kunt u het volgende doen:

1. Kopieer de koppeling en code hand matig naar een persoonlijke sessie van uw browser.
2. Kopieer de koppeling en code hand matig naar een andere browser.

### <a name="reauthentication-loop-or-upn-change"></a>Herauthenticatie lus of UPN-wijziging

Als u een lus voor opnieuw verifiëren hebt of als u de UPN van een van uw accounts hebt gewijzigd, voert u de volgende stappen uit:

1. Verwijder alle accounts en sluit Storage Explorer
2. Verwijder de. De map IdentityService van uw computer. In Windows bevindt de map zich `C:\users\<username>\AppData\Local`op. Voor Mac en Linux vindt u de map in de hoofdmap van de gebruikers lijst.
3. Als u een Mac-of Linux-versie gebruikt, moet u de vermelding micro soft. developer. IdentityService ook verwijderen uit de Store van het besturings systeem. Op Mac is het sleutel archief de toepassing ' gnome-keten '. Voor Linux wordt de toepassing meestal ' sleutel hanger ' genoemd, maar de naam kan verschillen, afhankelijk van uw distributie.

### <a name="conditional-access"></a>Voorwaardelijke toegang

Voorwaardelijke toegang wordt niet ondersteund wanneer Storage Explorer wordt gebruikt in Windows 10, Linux of macOS. Dit komt door een beperking in de AAD-bibliotheek die wordt gebruikt door Storage Explorer.

## <a name="mac-keychain-errors"></a>Mac-sleutel keten fouten

De macOS-sleutel hanger kan soms een status krijgen die problemen veroorzaakt voor de verificatie bibliotheek van de Storage Explorer. Voer de volgende stappen uit om de sleutel hanger uit deze staat te halen:

1. Storage Explorer sluiten.
2. Open sleutel hanger (**cmd + spatie**, type sleutel hanger, druk op ENTER).
3. Selecteer de sleutel hanger aanmelden.
4. Klik op het hang slot pictogram om de sleutel hanger te vergren delen (het hang slot wordt op een vergrendelde positie geanimeerd wanneer het proces is voltooid. het kan enkele seconden duren afhankelijk van de apps die u hebt geopend).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Start Storage Explorer.
6. Er wordt een pop-upvenster weer gegeven met de melding ' service hub wil toegang tot de sleutel keten? '. Wanneer dit het geval is, voert u het wacht woord voor uw Mac-beheerders account in en klikt u op **altijd toestaan** (of **toestaan** als **altijd toestaan** niet beschikbaar is).
7. Probeer u aan te melden.

### <a name="general-sign-in-troubleshooting-steps"></a>Algemene stappen voor het oplossen van problemen met aanmelding

* Als u zich in macOS bevindt, wordt het aanmeld venster nooit meer weer gegeven over het ' wachten op verificatie... ' en voer vervolgens [de volgende stappen uit](#mac-keychain-errors)
* Opnieuw opstarten Storage Explorer
* Als het verificatie venster leeg is, wacht u ten minste één minuut voordat u het dialoog venster verificatie sluit.
* Zorg ervoor dat uw proxy-en certificaat instellingen juist zijn geconfigureerd voor zowel uw computer als Storage Explorer.
* Als u werkt met Windows en toegang tot Visual Studio 2019 op dezelfde computer hebt en u zich hebt aangemeld, probeert u zich aan te melden bij Visual Studio 2019. Nadat u zich hebt aangemeld bij Visual Studio 2019, kunt u Storage Explorer openen en uw account bekijken in het deel venster account.

Als geen van deze methoden [een probleem opent op github](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Ontbrekende abonnementen en verbroken tenants

Als u uw abonnementen niet kunt ophalen nadat u zich hebt aangemeld, kunt u de volgende probleemoplossings methoden proberen:

* Controleer of uw account toegang heeft tot de abonnementen die u verwacht. U kunt uw toegang controleren door u aan te melden bij de portal voor de Azure-omgeving die u wilt gebruiken.
* Zorg ervoor dat u bent aangemeld met de juiste Azure-omgeving (Azure, Azure-China 21Vianet, Azure Duitsland, Azure Amerikaanse overheid of aangepaste omgeving).
* Als u zich achter een proxy bevindt, moet u ervoor zorgen dat u de Storage Explorer proxy op de juiste wijze hebt geconfigureerd.
* Probeer het account te verwijderen en opnieuw toe te voegen.
* Als er een koppeling meer informatie is, zoekt en ziet u welke fout berichten worden gerapporteerd voor de tenants die zijn mislukt. Als you'ren't weet wat u moet doen met de fout berichten die u ziet, kunt u [een probleem op github openen](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-attached-account-or-storage-resource"></a>Kan bijgevoegd account of opslag resource niet verwijderen

Als u een bijgevoegd account of opslag resource niet kunt verwijderen via de gebruikers interface, moet u alle gekoppelde resources hand matig verwijderen door de volgende mappen te verwijderen:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Sluit Storage Explorer voordat u de bovenstaande mappen verwijdert.

> [!NOTE]
> Als u ooit SSL-certificaten hebt geïmporteerd, maakt u een back- `certs` up van de inhoud van de map. Later kunt u de back-up gebruiken om uw SSL-certificaten opnieuw te importeren.

## <a name="proxy-issues"></a>Proxy problemen

Controleer eerst of de volgende gegevens die u hebt ingevoerd, juist zijn:

* De proxy-URL en het poort nummer
* Gebruikers naam en wacht woord indien vereist door de proxy

> [!NOTE]
> Storage Explorer biedt geen ondersteuning voor automatische proxy-configuratie bestanden voor het configureren van proxy-instellingen.

### <a name="common-solutions"></a>Algemene oplossingen

Als u nog steeds problemen ondervindt, kunt u de volgende probleemoplossings methoden proberen:

* Als u verbinding kunt maken met internet zonder uw proxy te gebruiken, controleert u of Storage Explorer werkt zonder dat er proxy instellingen zijn ingeschakeld. Als dit het geval is, is er mogelijk een probleem met de proxy-instellingen. Werk samen met uw proxy beheerder om de problemen te identificeren.
* Controleer of andere toepassingen die gebruikmaken van de proxy server naar verwachting werken.
* Controleer of u verbinding kunt maken met de portal voor de Azure-omgeving die u wilt gebruiken
* Controleer of u antwoorden kunt ontvangen van uw service-eind punten. Voer een van uw eind punt-Url's in uw browser in. Als u verbinding kunt maken, moet u een InvalidQueryParameterValue of soortgelijk XML-antwoord ontvangen.
* Als iemand anders ook gebruikmaakt van Storage Explorer met uw proxy server, controleert u of ze verbinding kunnen maken. Als ze verbinding kunnen maken, moet u mogelijk contact opnemen met de beheerder van de proxy server.

### <a name="tools-for-diagnosing-issues"></a>Hulpprogram ma's voor het oplossen van problemen

Als u hulpprogram ma's voor netwerken hebt, zoals Fiddler voor Windows, kunt u de problemen als volgt vaststellen:

* Als u uw proxy moet gebruiken, moet u mogelijk uw netwerk hulpprogramma configureren om verbinding te maken via de proxy.
* Controleer het poort nummer dat wordt gebruikt door uw netwerk programma.
* Voer de URL van de lokale host en het poort nummer van het netwerk hulpprogramma in als proxy-instellingen in Storage Explorer. Wanneer het netwerk hulpprogramma correct is uitgevoerd, wordt het registreren van netwerk aanvragen van Storage Explorer naar beheer-en service-eind punten gestart. Voer https://cawablobgrs.blob.core.windows.net/ bijvoorbeeld in voor uw BLOB-eind punt in een browser, en u ontvangt een antwoord dat lijkt op het volgende, waarmee wordt voorgesteld dat de resource bestaat, hoewel u er geen toegang tot hebt.

![Code voorbeeld](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Beheerder van de proxy server

Als uw proxy-instellingen juist zijn, moet u mogelijk contact opnemen met de beheerder van uw proxy server en

* Zorg ervoor dat uw proxy geen verkeer naar Azure-beheer-of resource-eind punten blokkeert.
* Controleer het verificatie protocol dat door uw proxy server wordt gebruikt. Storage Explorer ondersteunt momenteel geen NTLM-proxy's.

## <a name="unable-to-retrieve-children-error-message"></a>Fout bericht ' kan geen onderliggende items ophalen '

Als u met Azure bent verbonden via een proxy, controleert u of de proxy-instellingen juist zijn. Als u toegang hebt tot een resource van de eigenaar van het abonnement of het account, controleert u of u lees-of lijst machtigingen voor die resource hebt.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>De verbindings reeks heeft geen volledige configuratie-instellingen

Als dit fout bericht wordt weer gegeven, is het mogelijk dat u niet over de benodigde machtigingen beschikt om de sleutels voor uw opslag account te verkrijgen. Als u wilt controleren of dit het geval is, gaat u naar de portal en zoekt u naar uw opslag account. U kunt dit snel doen door met de rechter muisknop te klikken op het knoop punt voor uw opslag account en op ' openen in portal ' te klikken. Zodra u dit hebt gedaan, gaat u naar de Blade toegangs sleutels. Als u geen machtigingen hebt om sleutels weer te geven, ziet u een pagina met het bericht "u hebt geen toegang". U kunt dit probleem omzeilen door de account sleutel van iemand anders te verkrijgen en de naam en sleutel te koppelen. u kunt ook iemand vragen voor een SAS aan het opslag account en deze gebruiken om het opslag account te koppelen.

Als u de account sleutels ziet, kunt u een probleem in GitHub oplossen, zodat we u kunnen helpen het probleem op te lossen.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Er is een fout opgetreden tijdens het toevoegen van een nieuwe verbinding: TypeError: Kan de eigenschap Version niet lezen van niet gedefinieerd

Als dit fout bericht wordt weer gegeven wanneer u een aangepaste verbinding probeert toe te voegen, is het mogelijk dat de verbindings gegevens die zijn opgeslagen in de lokale referentie beheerder, beschadigd zijn.
U kunt dit probleem omzeilen door de beschadigde lokale verbindingen te verwijderen en vervolgens opnieuw toe te voegen.

1. Start Storage Explorer. Ga in het bovenste menu naar Help → wissel knop Ontwikkelhulpprogramma's.
2. Ga in het geopende venster naar het tabblad toepassing → lokale opslag (links) → file://
3. Afhankelijk van het type verbindingen waarmee u te maken hebt, zoekt u de sleutel en kopieert u de waarde ervan naar een tekst editor. De waarde is een matrix van uw aangepaste verbindings namen.
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
4. Nadat u de huidige verbindings namen hebt opgeslagen, stelt u de waarde in het `[]`Ontwikkelhulpprogramma's in op.

Als u de niet-beschadigde verbindingen wilt behouden, kunt u de volgende stappen uitvoeren om de beschadigde verbindingen te vinden. Als u niet alle bestaande verbindingen kwijtraakt, kunt u de volgende stappen overs Laan en de platformspecifieke instructies volgen om uw verbindings gegevens te wissen.

1. Voeg in de tekst editor elke verbindings naam weer toe aan de Ontwikkelhulpprogramma's en controleer of de verbinding nog steeds werkt.
2. Als een verbinding goed werkt, is deze niet beschadigd en kunt u deze daar veilig laten staan. Als een verbinding niet werkt, verwijdert u de waarde uit het Ontwikkelhulpprogramma's en legt u deze vast, zodat u deze later opnieuw kunt toevoegen.
3. Herhaal deze stap totdat u al uw verbindingen hebt gecontroleerd.

Nadat u alle verbindingen hebt door lopen, moet u voor alle verbindings namen die niet meer worden toegevoegd, de beschadigde gegevens wissen (indien aanwezig) en deze opnieuw toevoegen via de normale stappen met Storage Explorer.

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

1. Open referentie beheer door het menu Start te openen en zoek naar referentie beheer.
2. Ga in het geopende venster naar Windows-referenties.
3. Onder generieke referenties zoekt u naar vermeldingen met de `<connection_type_key>/<corrupted_connection_name>` sleutel ( `StorageExplorer_CustomConnections_Accounts_v1/account1`bijvoorbeeld).
4. Verwijder deze vermeldingen en voeg de verbindingen weer toe.

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

1. Open Spotlight (opdracht regel balk) en zoek naar ' sleutel hanger toegang '.
2. Zoek naar vermeldingen met de `<connection_type_key>/<corrupted_connection_name>` sleutel ( `StorageExplorer_CustomConnections_Accounts_v1/account1`bijvoorbeeld).
3. Verwijder deze vermeldingen en voeg de verbindingen weer toe.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

Het lokale referentie beheer varieert afhankelijk van de Linux-distributie. Als uw Linux-distributie geen ingebouwd GUI-hulp programma biedt voor lokaal referentie beheer, kunt u een hulp programma van derden installeren om uw lokale referenties te beheren. U kunt bijvoorbeeld [seahorse](https://wiki.gnome.org/Apps/Seahorse/)gebruiken, een open-source GUI-hulp programma voor het beheren van lokale referenties voor Linux.

1. Open uw lokale hulp programma voor referentie beheer, Zoek uw opgeslagen referenties.
2. Zoek naar vermeldingen met de `<connection_type_key>/<corrupted_connection_name>` sleutel ( `StorageExplorer_CustomConnections_Accounts_v1/account1`bijvoorbeeld).
3. Verwijder deze vermeldingen en voeg de verbindingen weer toe.

Als deze fout blijft optreden nadat u deze stappen hebt uitgevoerd, of als u wilt delen wat u denkt dat de verbindingen zijn beschadigd, [opent u een probleem](https://github.com/microsoft/AzureStorageExplorer/issues) op onze github-pagina.

## <a name="issues-with-sas-url"></a>Problemen met SAS-URL

Als u verbinding maakt met een service via een SAS-URL en deze fout ondervindt:

* Controleer of de URL de benodigde machtigingen heeft om resources te lezen of weer te geven.
* Controleer of de URL niet is verlopen.
* Als de SAS-URL is gebaseerd op een toegangs beleid, controleert u of het toegangs beleid niet is ingetrokken.

Als u per ongeluk hebt gekoppeld met een ongeldige SAS-URL en niet kunt ontkoppelen, voert u de volgende stappen uit:

1. Wanneer u Storage Explorer uitvoert, drukt u op F12 om het venster ontwikkel hulpprogramma's te openen.
2. Klik op het tabblad toepassing en klik vervolgens op lokale opslag > file://in de structuur aan de linkerkant.
3. Zoek de sleutel die is gekoppeld aan het Service type van de problematische SAS-URI. Als de beschadigde SAS-URI bijvoorbeeld voor een BLOB-container is, zoekt u naar de sleutel `StorageExplorer_AddStorageServiceSAS_v1_blob`met de naam.
4. De waarde van de sleutel moet een JSON-matrix zijn. Zoek het object dat is gekoppeld aan de ongeldige URI en verwijder het.
5. Druk op CTRL + R om Storage Explorer opnieuw te laden.

## <a name="linux-dependencies"></a>Linux-afhankelijkheden

Storage Explorer 1.10.0 en hoger is beschikbaar als een module van de snap Store. Met de module Storage Explorer worden alle afhankelijkheden automatisch geïnstalleerd en bijgewerkt wanneer er een nieuwe versie van de magneet beschikbaar is. Het installeren van de Storage Explorer-module is de aanbevolen installatie methode.

Voor Storage Explorer is het gebruik van een wachtwoord beheerder vereist, dat mogelijk hand matig moet worden aangesloten voordat Storage Explorer goed werkt. U kunt Storage Explorer verbinding maken met wachtwoord beheer van uw systeem met de volgende opdracht:

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
> Voor Storage Explorer versie 1.7.0 en eerder is .NET Core 2,0 vereist. Als u een nieuwere versie van .NET core hebt geïnstalleerd, moet u [Storage Explorer patches](#patching-storage-explorer-for-newer-versions-of-net-core). Als u Storage Explorer 1.8.0 of hoger gebruikt, kunt u gebruikmaken van tot .NET Core 2,2. Versies van meer dan 2,2 zijn op dit moment niet geverifieerd.

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

1. Storage Explorer downloaden
2. Installeer de [.net core-runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Voer de volgende opdracht uit:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Storage Explorer downloaden
2. Installeer de [.net core-runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Voer de volgende opdracht uit:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Patches Storage Explorer voor nieuwere versies van .NET core

Voor Storage Explorer 1.7.0 of ouder moet u mogelijk de versie van .NET core die door Storage Explorer wordt gebruikt, bijwerken.

1. Down load versie 1.5.43 van StreamJsonRpc van [nuget](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Zoek naar de koppeling pakket downloaden aan de rechter kant van de pagina.
2. Nadat u het pakket hebt gedownload, wijzigt u de `.nupkg` bestands `.zip`extensie van in.
3. Pak het pakket uit.
4. Open de map `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopieer `StreamJsonRpc.dll` naar de volgende locaties in de map Storage Explorer:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Openen in Verkenner vanuit Azure Portal werkt niet

Als de knop openen in Verkenner op de Azure Portal niet voor u werkt, moet u ervoor zorgen dat u een compatibele browser gebruikt. De volgende browsers zijn getest op compatibiliteit.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Volgende stappen

Als geen van de oplossingen voor u werkt, [opent u een probleem op github](https://github.com/Microsoft/AzureStorageExplorer/issues). U kunt ook snel aan de slag met GitHub met behulp van de knop ' probleem melden aan GitHub ' in de linkerbenedenhoek.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)
