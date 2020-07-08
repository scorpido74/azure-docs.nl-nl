---
title: Veelgestelde vragen over Microsoft Azure Data Box | Microsoft Docs in gegevens
description: Bevat veelgestelde vragen en antwoorden voor Azure Data Box, een cloudoplossing waarmee u grote hoeveelheden gegevens naar Azure kunt overdragen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 09/03/2019
ms.author: alkohli
ms.openlocfilehash: d1455bc65f175970bb741dab4a56aa0aac58fc59
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83743000"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: veelgestelde vragen

Met de hybride oplossing Microsoft Azure Data Box kunt u terabytes aan gegevens op een snelle, goedkope en betrouwbare manier naar Azure verzenden met behulp van een overdrachtsapparaat. Hier vindt u antwoorden op vragen die u mogelijk hebt over het gebruik van Data Box in de Azure Portal. 

De vragen en antwoorden zijn in de volgende categorieën onderverdeeld:

- Over de service
- Apparaat bestellen
- Configureren en verbinding maken 
- Status bijhouden
- Gegevens kopiëren 
- Apparaat verzenden
- Gegevens verifiëren en uploaden 
- Ondersteuning voor de bewakingsketen

## <a name="about-the-service"></a>Over de service

### <a name="q-what-is-azure-data-box-service"></a>V. Wat is de Azure Data Box-service? 
A.  De service Azure Data Box is bedoeld voor offlinegegevensopname. Deze service beheert een matrix met producten met verschillende opslagcapaciteiten die allemaal zijn afgestemd voor gegevenstransport. 

### <a name="q-what-is-azure-data-box"></a>V. Wat is Azure Data Box?
A. De Azure Data Box biedt een snelle, goedkope en veilige overdracht van terabytes aan gegevens in Azure. U kunt het Data Box-apparaat bestellen via de Azure-portal. Microsoft verzendt een opslagapparaat met een bruikbare capaciteit van 80 TB via een regionale koerier. 

Nadat u het apparaat hebt ontvangen, kunt u het snel instellen via de lokale webinterface. Kopieer de gegevens van uw servers naar het apparaat en stuur het terug naar Azure. In het Azure-datacenter worden uw gegevens automatisch geüpload van de schijven naar Azure. Het volledige proces wordt gevolgd door de Data Box-service in de Azure-portal.

### <a name="q-when-should-i-use-data-box"></a>V. Wanneer moet ik Data Box gebruiken?
A. Als u 40-500 TB aan gegevens naar Azure wilt overdragen, hebt u baat bij het gebruik van Data Box. Voor gegevens grootte < 40 TB gebruikt u Data Box Disk en voor de grootte van gegevens > 500 TB, meldt u zich aan voor [Data Box Heavy](data-box-heavy-overview.md).

### <a name="q-what-is-the-price-of-data-box"></a>V. Wat kost Data Box?
A. Data Box is verkrijgbaar tegen een nominaal bedrag voor 10 dagen. Als u het productmodel selecteert terwijl u een bestelling in de Azure-portal maakt, zullen de kosten voor het apparaat worden weergegeven. De verzending is ook gratis, maar er zijn wel kosten verbonden aan de Azure-opslag. Ga voor meer informatie naar [Prijzen van Azure Data Box](https://azure.microsoft.com/pricing/details/storage/databox/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>V. Wat is de maximale hoeveelheid gegevens die ik met Data Box in één keer kan overdragen?
A. Data Box heeft een onbewerkte capaciteit van 100 TB en een bruikbare capaciteit van 80 TB. U kunt met Data Box maximaal 80 TB aan gegevens overdragen. Als u meer gegevens wilt overdragen, moet u meer apparaten bestellen.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>V. Hoe kan ik controleren of Data Box in mijn regio beschikbaar is? 
A.  Voor informatie over welke landen/regio's de Data Box beschikbaar is, gaat u naar [beschik bare regio's](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>V. In welke regio's kan ik gegevens opslaan met Data Box?
A. Data Box wordt ondersteund voor alle regio's in VS, Europa-west, Europa-noord, Frank rijk, UK, Japan, Australië en Canada. Ga naar [beschik bare regio](data-box-overview.md#region-availability)voor meer informatie.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>V. Met wie moet ik contact opnemen als ik een probleem krijg met Data Box?
A. Neem bij problemen met Data Box contact op met [Microsoft Ondersteuning](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-have-lost-my-data-box-is-there-a-lost-device-charge"></a>V. Ik heb geen verbinding meer met mijn Data Box. Zijn er kosten voor een verloren apparaat?
A. Ja. Er zijn kosten voor een verloren of beschadigd apparaat. Deze kosten wordt beschreven op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/storage/databox/) en in de [servicevoorwaarden bij het product](https://www.microsoft.com/licensing/product-licensing/products).


## <a name="order-device"></a>Apparaat bestellen

### <a name="q-how-do-i-get-data-box"></a>V. Hoe kan ik Data Box krijgen? 
A.  Meld u aan bij de Azure Portal en maak een bestelling voor Data Box om Azure Data Box te ontvangen. Geef uw contactgegevens en overige informatie op. Zodra u een bestelling hebt geplaatst, wordt Data Box op basis van beschikbaarheid binnen 10 dagen verzonden. Ga voor meer informatie naar [Een Data Box bestellen](data-box-deploy-ordered.md).

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>V. Ik kon geen bestelling voor een Data Box maken in de Azure Portal. Waarom is dit?
A. Als u geen bestelling voor een Data Box kunt maken, is er een probleem met uw abonnementstype of toegang. 

Raadpleeg uw abonnement. Data Box is alleen beschikbaar voor EA- en CSP-abonnementen (Enterprise Agreement en Cloud Solution Provider). Neem contact op met Microsoft Ondersteuning om uw abonnement te upgraden als uw abonnement niet onder de bovenstaande typen valt.

Controleer het toegangsniveau van uw abonnement als u een ondersteund type aanbieding voor het abonnement hebt. U moet inzender of eigenaar van een abonnement zijn om een bestelling te kunnen maken.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>V. Ik heb een aantal Data Box-apparaten besteld. Ik kan geen extra bestellingen maken. Waarom is dit?
A. We staan maximaal vijf actieve bestellingen per abonnement per handelsgrens (combinatie van geselecteerd land en geselecteerde regio) toe. Neem contact op met Microsoft Ondersteuning om de limiet voor uw abonnement te verhogen als u een extra apparaat moet bestellen.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>V. Als ik een bestelling probeer te maken, ontvang ik een melding dat de Data Box-service niet beschikbaar is. Wat betekent dit?
A. Dit betekent dat de Data Box-service niet beschikbaar is voor de combinatie van het land en de regio die u hebt geselecteerd. Waarschijnlijk is de Data Box-service wel beschikbaar als u deze combinatie wijzigt. Ga naar [Regionale beschikbaarheid voor Data Box](data-box-overview.md#region-availability) voor een overzicht van de regio’s waarin de service beschikbaar is.

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>V. Ik heb mijn Data Box-bestelling een paar dagen geleden geplaatst. Wanneer ontvang ik mijn Data Box?
A. Als u een bestelling plaatst, controleren we of er een apparaat beschikbaar is voor uw bestelling. Als het apparaat beschikbaar is, verzenden we het binnen 10 dagen. Mogelijk is er in bepaalde perioden een hogere vraag. Als dat het geval is, wordt uw bestelling in de wachtrij geplaatst en kunt u de statuswijziging volgen in de Azure-portal. De bestelling wordt automatisch geannuleerd als uw bestelling niet binnen 90 dagen wordt geleverd.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>V. Ik heb mijn Data Box gevuld met gegevens en moet er nog een bestellen. Kan ik dat op een snelle manier doen?
A. U kunt een kloon maken van uw vorige bestelling. Hierdoor maakt u dezelfde bestelling als eerst en kunt u de details van de bestelling bewerken zonder dat u opnieuw uw adres, contactgegevens en meldingsinformatie hoeft te typen.

## <a name="configure-and-connect"></a>Configureren en verbinding maken

### <a name="q-how-do-i-unlock-the-data-box"></a>V. Hoe kan ik de Data Box ontgrendelen? 
A.  Ga in Azure Portal naar uw Data Box-bestelling en ga vervolgens naar **Apparaatdetails**. Kopieer het ontgrendelingswachtwoord. Gebruik dit wachtwoord om u aan te melden bij de lokale webgebruikersinterface van uw Data Box. Ga voor meer informatie naar [Zelfstudie: uw Azure Data Box uitpakken, aansluiten en verbinden](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>V. Kan ik een Linux-hostcomputer gebruiken om verbinding te maken met de gegevens en deze naar de Data Box te kopiëren?
A.  Ja. U kunt de Data Box gebruiken om verbinding te maken met SMB- en NFS-clients. Ga naar de lijst met [ondersteunde besturingssystemen](data-box-system-requirements.md) voor uw hostcomputer voor meer informatie.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>V. Mijn Data Box is verzonden, maar ik wil de bestelling annuleren. Waarom is er geen knop Annuleren?
A.  U kunt de bestelling alleen annuleren nadat de Data Box is besteld en voordat de bestelling wordt verwerkt. U kunt de bestelling van de Data Box niet meer annuleren nadat deze is verwerkt. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>V. Kan ik een Data Box met meerdere hostcomputers tegelijk verbinden om gegevens over te dragen?
A. Ja. Er kunnen meerdere hostcomputers verbinding met een Data Box maken om gegevens over te dragen en er kunnen meerdere kopieertaken parallel worden uitgevoerd. Ga voor meer informatie naar [Zelfstudie: Gegevens naar Azure Data Box kopiëren](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>V. Kan ik verbinding maken met beide 10 GbE-interfaces op de Data Box om gegevens over te dragen ?
A. Ja. Beide 10 GbE-interfaces kunnen worden aangesloten op de Data Box om gegevens tegelijkertijd te kopiëren. Ga voor meer informatie over het kopiëren van gegevens naar de [zelf studie: gegevens kopiëren naar Azure data Box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>V. Het ledcontrolelampje voor systeemstoringen op het voorpaneel brandt. Wat moet ik doen?
A. Als het ledcontrolelampje voor systeemstoringen brandt, betekent dit dat uw systeem niet in orde is. [Neem contact op met Microsoft ondersteuning](data-box-disk-contact-microsoft-support.md) voor de volgende stappen.

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>V. Ik heb geen toegang tot het ontgrendelingswachtwoord voor de Data Box in de Azure Portal. Waarom is dit?
A. Controleer de machtigingen in uw abonnement en opslagaccount als u geen toegang tot het ontgrendelingswachtwoord in de Azure Portal hebt. Zorg dat u inzender- of eigenaarsmachtigingen op resourcegroepniveau hebt. Als dit niet het geval is, moet u minimaal machtigingen op het niveau van de rol Data Box Operator hebben om de toegangsreferenties te kunnen zien.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>V. Wordt poortkanaalconfiguratie ondersteund in Data Box? En MPIO?
A. We ondersteunen geen poortkanaalconfiguratie, Multipath I/O (MPIO) of vLAN-configuratie in Data Box.

## <a name="track-status"></a>Status bijhouden

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>V. Hoe kan ik de bestelstatus van de Data Box volgen vanaf het moment dat de bestelling is geplaatst als ik het apparaat terug wil sturen? 
A.  U kunt de status van de Data Box-bestelling volgen in Azure Portal. Als u de bestelling plaatst, wordt u ook gevraagd een e-mailadres voor meldingen op te geven. Als u dat hebt gedaan, dan krijgt u elke keer per e-mail een melding als er een statuswijziging heeft plaatsgevonden. Meer informatie over het [configureren van e-mailmeldingen](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>V. Hoe kan ik het apparaat terugsturen? 
A.  Microsoft geeft een verzendlabel weer op het E-ink-display. Als het verzendlabel niet wordt weergegeven op het E-ink-display, gaat u naar **Overzicht > Verzendlabel downloaden**. Download en print het label, plaats het label in het doorzichtige plastic hoesje op het apparaat en geef het apparaat mee aan de koerier die u gebruikt. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>V. Ik heb een e-mailbericht ontvangen dat mijn apparaat het Azure-datacenter heeft bereikt. Hoe kan ik weten of de gegevens worden geüpload?
A. U kunt in de Azure Portal naar uw Data Box-bestelling gaan en dan **Overzicht** kiezen. Als het uploaden van gegevens naar Azure is gestart, zult u de voortgang van het kopiëren in het rechterdeelvenster zien. 

## <a name="migrate-data"></a>Gegevens migreren

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>V. Wat is de maximale hoeveelheid gegevens die voor Data Box kan worden gebruikt?  
A.  Data Box heeft een bruikbare opslagcapaciteit van 80 TB. U kunt een Data Box-apparaat gebruiken voor gegevens met een grootte van 40 tot 80 TB. Voor grotere hoeveelheden gegevens tot 500 TB kunt u meerdere Data Box-apparaten bestellen. Meld u aan voor Data Box Heavy voor gegevens die groter zijn dan 500 TB.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>V. Wat zijn de maximale groottes voor blok-blob en pagina-blob die voor Data Box worden ondersteund? 
A.  De maximale groottes worden bepaald door Azure Storage-limieten. De maximale blok-blob is ongeveer 4,768 TiB en de maximale blobgrootte is 8 TiB. Zie [schaalbaarheids-en prestatie doelen voor Blob Storage](../storage/blobs/scalability-targets.md)voor meer informatie.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>V. Hoe weet ik of mijn gegevens veilig zijn tijdens de overdracht? 
A. Er zijn meerdere beveiligingsfuncties geïmplementeerd om te zorgen dat uw Data Box veilig is tijdens het verzenden. Sommige daarvan zijn verzegeling, detectie van hardware- en softwaremanipulatie en een wachtwoord om het apparaat te ontgrendelen. Ga naar [Azure Data Box-beveiliging en -gegevensbescherming](data-box-security.md) voor meer informatie.

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>V. Hoe kan ik de gegevens naar de Data Box kopiëren? 
A.  Als u een SMB-client gebruikt, kunt u een SMB-kopieerprogramma zoals Robocopy of Diskboss gebruiken als u gegevens naar schijven wilt kopiëren. U kunt zelfs gegevens naar het apparaat slepen vanuit Windows Verkenner. 

Als u een NFS-client gebruikt, kunt u [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) of [Ultracopier](https://ultracopier.first-world.info/) gebruiken. 

Ga voor meer informatie naar [Zelfstudie: Gegevens naar Azure Data Box kopiëren](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>V. Zijn er tips om het kopiëren van gegevens te versnellen?
A.  U kunt het kopieerproces als volgt versnellen:

- Gebruik meerdere gegevensstromen. In bijvoorbeeld Robocopy kunt u de optie voor meerdere threads gebruiken. Ga naar [Tutorial: Copy data to Azure Data Box and verify](data-box-deploy-copy-data.md) (Zelfstudie: Gegevens naar Azure Data Box kopiëren en verifiëren) voor meer informatie over de opdracht die u moet gebruiken.
- Gebruik meerdere sessies.
- In plaats van te kopiëren via een netwerkshare (waarbij de netwerksnelheid beperkend kan zijn), zorgt u ervoor dat de gegevens lokaal aanwezig zijn op de computer waarmee de Data Box is verbonden.
- Voer een benchmark-test uit van de prestaties van de computer die wordt gebruikt om de gegevens te kopiëren. Download en gebruik het hulpprogramma [Bluestop FIO](https://ci.appveyor.com/project/axboe/fio) om een benchmark-test uit te voeren van de prestaties van de serverhardware. Selecteer de nieuwste versie van x86 of x64, selecteer het tabblad **artefacten** en down load het MSI-bestand.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM's disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>V. Kan ik meerdere opslagaccounts gebruiken voor Data Box?
A.  Ja. Data Box ondersteunt maximaal 10 opslagaccounts, algemene opslag, klassieke opslag en blob-opslag. Zowel dynamische als statische blob wordt ondersteund. 


## <a name="ship-device"></a>Apparaat verzenden

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>V. Mijn apparaat werd geleverd, maar het apparaat lijkt te zijn beschadigd. Wat moet ik doen?
A. Gebruik het apparaat niet als het beschadigd is gearriveerd of als er bewijs van sabotage is. [Neem contact op met Microsoft Ondersteuning](data-box-disk-contact-microsoft-support.md) en stuur het apparaat zo snel mogelijk terug. U kunt ook een nieuwe Data Box-bestelling maken voor een vervangend apparaat. In dit geval worden er geen kosten voor het vervangende apparaat in rekening gebracht.

### <a name="q-can-i-pick-up-my-data-box-order-myself-can-i-return-the-data-box-via-a-carrier-that-i-choose"></a>V. Kan ik mijn Data Box bestelling zelf ophalen? Kan ik de Data Box retour neren via een door mij gekozen provider?
A. Ja. Micro soft biedt ook zelf-beheerde verzen ding in US Gov regio. Wanneer u de Data Box order plaatst, kunt u de optie voor zelf-beheerde verzen ding kiezen. Voer de volgende stappen uit om uw Data Box-apparaat op te halen:
    
1. Nadat u de order hebt geplaatst, wordt de order verwerkt en wordt de Data Box voor bereid. U wordt gewaarschuwd via een e-mail bericht dat uw bestelling gereed is voor ophalen. 
2. Zodra de bestelling gereed is voor ophalen, gaat u naar uw bestelling in het Azure Portal en gaat u naar de Blade **overzicht** . 
3. Er wordt een melding met een code in de Azure Portal weer geven. E-mail het [Azure data Box Operations-team](mailto:adbops@microsoft.com) en geef de code op. Het team geeft de locatie op en plant een datum en tijd voor het ophalen. U moet het team aanroepen binnen 5 werk dagen nadat u het e-mail bericht hebt ontvangen.

Wanneer het kopiëren van de gegevens is voltooid, voert u de volgende stappen uit om uw apparaat te retour neren:

1. Als het kopiëren van de gegevens is voltooid zonder fouten, voert u **voorbereiding voor verzending**uit. Nadat de voor bereiding is voltooid, ontvangt u een code in de lokale web-UI van het apparaat. Kopieer de code en sla deze op.
2. Schakel het apparaat uit en verwijder de verbindings kabels.
3. Rol het netsnoer op en plaats het veilig aan de achterzijde van het apparaat.
4. E-mail het [Azure data Box Operations-team](mailto:adbops@microsoft.com) en geef de code op die u eerder hebt opgeslagen. Ze geven u informatie over waar en wanneer u het apparaat moet neerzetten.

### <a name="q-will-my-data-box-devices-cross-countryregion-borders-during-shipping"></a>V. Worden er tijdens de verzen ding meerdere land-en regio grenzen door mijn Data Box apparaten?
A. Alle Data Box apparaten worden vanuit hetzelfde land/dezelfde regio als de bestemming verzonden en passeren geen internationale grenzen. De enige uitzonde ring hierop is voor orders in de Europese Unie (EU), waar apparaten kunnen worden verzonden naar en naar elk land/regio in de EU. Dit geldt voor zowel de Data Box als de Data Box Heavy-apparaten.

### <a name="q-i-ordered-a-data-box-in-us-east-but-i-received-a-device-that-was-shipped-from-a-location-in-us-west-where-should-i-return-the-device-to"></a>V. Ik heb een Data Box in VS Oost besteld, maar ik heb een apparaat ontvangen dat is verzonden vanaf een locatie in VS West. Waar moet ik het apparaat retour neren?
A. We proberen zo snel mogelijk een Data Box apparaat te verkrijgen. We geven een prioriteit aan de verzen ding van een Data Center dat het dichtst bij de locatie van uw opslag account ligt, maar verzendt een apparaat vanuit elk Azure-Data Center dat beschik bare inventaris. Uw Data Box moet worden teruggestuurd naar dezelfde locatie waar deze is verzonden, zoals wordt weer gegeven in het verzend label.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>V. Er wordt geen label voor retourzending weergegeven op het E-ink-display. Wat moet ik doen?
A. Voer de volgende stappen uit als er geen label voor retourverzending wordt weergegeven op het E-ink-display:
- Verwijder het oude verzendlabel en alle stickers van de vorige verzending.
- Ga naar uw bestelling in de Azure-portal. Ga naar **overzicht** en **down load het verzend label**. Ga voor meer informatie naar [Verzendlabel downloaden](data-box-portal-admin.md#download-shipping-label).
- Print het verzendlabel en steek het in het doorzichtige plastic hoesje op het apparaat. 
- Zorg dat het verzendlabel duidelijk zichtbaar is. 

### <a name="q-how-is-my-data-protected-during-transit"></a>V. Hoe worden mijn gegevens tijdens het verzenden beveiligd? 
A.  Tijdens het transport helpen de volgende functies van de Data Box de gegevens beschermen.
 - De Data Box-schijven zijn versleuteld met AES-256-versleuteling. 
 - Het apparaat is vergrendeld en er is een ontgrendelingswachtwoord nodig om toegang te krijgen tot gegevens.
Ga voor meer informatie naar [Data Box-beveiligingsfuncties](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>V. Ik heb Voorbereiding voor verzending afgerond en het apparaat uitgeschakeld. Kan ik nog steeds meer gegevens aan Data Box toevoegen?
A. Ja. U kunt het apparaat inschakelen en meer gegevens toevoegen. Als u het kopiëren van gegevens hebt voltooid, moet u **Voorbereiding voor verzending** opnieuw uitvoeren.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>V. Ik heb mijn apparaat ontvangen en ik kan het niet opstarten. Hoe stuur ik het apparaat terug?
A. Als u uw apparaat niet kunt opstarten, gaat u terug naar uw bestelling in de Azure-portal. Download een verzendlabel en bevestig dat op het apparaat. Ga voor meer informatie naar [Verzendlabel downloaden](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Verifiëren en uploaden

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>V. Hoe snel krijg ik toegang tot mijn gegevens in azure nadat ik de Data Box weer heb verzonden? 
A.  Zodra de status van de bestelling voor het **kopiëren van gegevens** wordt weer gegeven als **voltooid**, kunt u direct toegang krijgen tot uw gegevens.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>V. Waar bevinden zich mijn gegevens in Azure na het uploaden?
A.  Als u de gegevens naar Data Box kopieert, worden de gegevens naar een van deze locaties in uw Azure Storage-account geüpload, afhankelijk van het feit of de gegevens bestaan uit een blok-blob, een pagina-blob of Azure-bestanden.
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  U kunt ook naar uw Azure-opslagaccount in de Azure-portal gaan en van daaruit navigeren.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>V. Ik heb gemerkt dat ik de naamgevingsvereisten voor Azure niet voor mijn containers heb gevolgd. Worden mijn gegevens niet naar Azure geüpload?
A.  Als de containernamen hoofdletters bevatten, worden die namen automatisch naar kleine letters omgezet. Als de namen anderszins niet aan de vereisten voldoen (speciale tekens, andere taal, enzovoort), dan worden er geen gegevens geüpload. Ga voor meer informatie over beproefde methoden voor de naamgeving van shares, containers en bestanden naar:
- [Naamgeving van en verwijzing naar shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Conventies voor blok-blobs en pagina-blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>V. Hoe kan ik de gegevens controleren die naar Data Box zijn gekopieerd?
A.  Uw gegevens worden gevalideerd als u **Voorbereiding voor verzending** uitvoert nadat het kopiëren van gegevens is voltooid. Data Box genereert tijdens het validatieproces een lijst met bestanden en controlesommen voor de gegevens. U kunt de lijst met bestanden downloaden en de lijst verifiëren met de bestanden in de brongegevens. Ga voor meer informatie naar [Voorbereiding voor verzending](data-box-deploy-picked-up.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>V. Wat gebeurt er met de gegevens nadat ik de Data Box heb teruggestuurd?
A.  Als de gegevens naar Azure zijn gekopieerd, worden de gegevens van op de Data Box veilig gewist volgens de richtlijnen van NIST SP 800-88 Revision 1. Ga voor meer informatie naar [Gegevens verwijderen uit de Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Auditrapport

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Hoe helpt de Azure Data Box-service in de procedure voor de bewakingsketen van klanten?
A.  De Azure Data Box-service biedt systeemeigen rapporten die u kunt gebruiken voor de documentatie van uw bewakingsketen. De controle- en kopielogboeken zijn beschikbaar in uw opslagaccount in Azure. U kunt [de bestelgeschiedenis downloaden](data-box-portal-admin.md#download-order-history) via de Azure-portal nadat de bestelling is voltooid.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Wat voor soort rapporten is er beschikbaar ter ondersteuning van de bewakingsketen?
A.  De volgende soorten rapporten zijn beschikbaar ter ondersteuning van de bewakingsketen:

- Transport door UPS.
- Registratie van inschakeling en toegang tot de share van gebruikers.
- Manifestbestand met een cyclische redundantiecontrole van 64-bits (CRC-64) of controlesom voor elk bestand dat met succes in Data Box is opgenomen.
- Rapportage van bestanden die niet naar het Azure-opslagaccount konden worden geüpload.
- Opschoning van het Data Box-apparaat (volgens de norm NIST 800 88R1) nadat gegevens naar uw Azure-opslagaccount zijn gekopieerd.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Zijn de logboeken voor tracering van de koerier (van UPS) beschikbaar? 
A.  De logboeken voor tracering van de koerier worden vastgelegd in de bestelgeschiedenis van Data Box. Dit rapport is beschikbaar nadat het apparaat naar het Azure-datacenter is geretourneerd en de gegevens op de schijven van het apparaat zijn opgeschoond. Voor onmiddellijke behoefte kunt u ook rechtstreeks naar de website van de vervoerder gaan met het order tracking nummer en de tracerings gegevens ophalen.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Kan ik de Data Box naar het Azure-datacenter vervoeren? 
A.  Nee. Het Azure-datacenter accepteert momenteel geen levering van de Data Box door klanten of andere koeriers dan UPS.


## <a name="next-steps"></a>Volgende stappen

- De [Systeemvereisten voor Data Box](data-box-system-requirements.md) lezen.
- Informatie over de [Limieten voor Data Box](data-box-limits.md).
- [Azure Data Box](data-box-quickstart-portal.md) snel implementeren in de Azure-portal.
