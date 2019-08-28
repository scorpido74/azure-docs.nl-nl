---
title: Veelgestelde vragen over Microsoft Azure Data Box Disk | Microsoft Docs in gegevens
description: Bevat veelgestelde vragen en antwoorden voor Azure Data Box Disk, een cloudoplossing waarmee u grote hoeveelheden gegevens naar Azure kunt overdragen
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 08/15/2019
ms.author: alkohli
ms.openlocfilehash: 1ac91853c78a33645c0ccb234437e3cabc188f88
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076901"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk: Veelgestelde vragen

Met de cloudoplossing Microsoft Azure Data Box Disk kunt u terabytes aan gegevens op een snelle, goedkope en betrouwbare manier naar Azure verzenden. Hier vindt u antwoorden op vragen die u mogelijk hebt over het gebruik van Data Box-schijven in de Azure-portal. 

De vragen en antwoorden zijn in de volgende categorieën onderverdeeld:

- Over de service
- Configureren en verbinding maken 
- Status bijhouden
- Gegevens migreren 
- Gegevens verifiëren en uploaden 


## <a name="about-the-service"></a>Over de service

### <a name="q-what-is-azure-data-box-service"></a>V. Wat is de Azure Data Box-service? 
A.  De service Azure Data Box is bedoeld voor offlinegegevensopname. Met deze service wordt een reeks producten beheerd die geschikt zijn voor gegevenstransport bij verschillende opslagcapaciteiten. 

### <a name="q-what-are-azure-data-box-disks"></a>V. Wat zijn Azure Data Box-schijven?
A. MetAzure Data Box-schijven kunt u terabytes aan gegevens snel, goedkoop en veilig naar en vanuit Azure overdragen. U ontvangt maximaal vijf schijven, met een maximale opslagcapaciteit van 35 TB. U kunt deze schijven makkelijk configureren, verbinden en ontgrendelen via de Data Box-service in Azure Portal.  

Schijven worden versleuteld met Microsoft BitLocker-stationsversleuteling en uw encryptiesleutels worden beheerd in Azure Portal. Vervolgens kopieert u de gegevens vanuit de servers van de klant. In het datacenter worden uw gegevens naar de cloud gemigreerd met behulp van een snelle uploadkoppeling via een privénetwerk en naar Azure geüpload.

### <a name="q-when-should-i-use-data-box-disks"></a>V. Wanneer moet ik Data Box-schijven gebruiken?
A. Als u 40 TB (of minder) aan gegevens naar Azure wilt overdragen, hebt u baat bij het gebruik van Data Box Disks.

### <a name="q-what-is-the-price-of-data-box-disks"></a>V. Wat kosten Data Box-schijven?
A. Ga naar de [pagina Prijzen](https://azure.microsoft.com/pricing/details/databox/disk/) voor informatie over de prijs van Data Box-schijven.

### <a name="q-how-do-i-get-data-box-disks"></a>V. Hoe verkrijg ik Data Box-schijven? 
A.  Als u Azure Data Box-schijven wilt aanschaffen, meldt u zich aan bij Azure Portal en plaatst u een Data Box-bestelling voor schijven. Geef uw contactgegevens en overige informatie op. Zodra u een bestelling hebt geplaatst, worden de schijven op basis van beschikbaarheid binnen tien dagen verzonden.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>V. Wat is de maximale hoeveelheid gegevens die ik met Data Box-schijven in één keer kan overdragen?
A. Bij vijf schijven van 8 TB (7 TB aan bruikbare capaciteit), bedraagt de maximaal bruikbare capaciteit 35 TB. U kunt dus 35 TB aan gegevens in één keer overdragen. Als u meer gegevens wilt overdragen, moet u meer schijven bestellen.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>V. Hoe kan ik controleren of Data Box Disk in mijn regio beschikbaar is? 
A.  Als u wilt zien waar de Data Box schijven op dit moment beschikbaar zijn, gaat u naar de [Beschik baarheid](data-box-disk-overview.md#region-availability)van de regio.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>V. In welke regio's kan ik gegevens opslaan met Data Box Disk?
A. Data Box Disk wordt ondersteund voor alle regio's binnen de Verenigde Staten, Canada, Australië, Europa-west en Europa-noord, Korea en Japan. Alleen de Azure-regio's met een openbare cloud worden ondersteund. Azure Government of andere onafhankelijke clouds worden niet ondersteund.

### <a name="q-will-my-data-box-disk-cross-country-borders-during-shipping"></a>V. Worden de land grenzen van mijn Data Box Disk tijdens de verzen ding?
A. Data Box Disk worden verzonden vanuit hetzelfde land als hun bestemming en er worden geen internationale grenzen in rekening gebracht. De enige uitzonde ring hierop is voor orders in de Europese Unie (EU), waarbij schijven kunnen worden verzonden naar en naar elk land van de EU.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>V. Met wie moet ik contact opnemen als ik een probleem krijg met Data Box-schijven?
A. Neem bij problemen met Data Box-schijven contact op met [Microsoft Ondersteuning](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support).

## <a name="configure-and-connect"></a>Configureren en verbinding maken
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>V. Kan ik het aantal Data Box-schijven in de bestelling opgeven?
A.  Nee. Het aantal schijven van 8 TB (maximaal vijf) dat u ontvangt, is afhankelijk van uw gegevensgrootte en de beschikbaarheid van de schijven.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>V. Hoe ontgrendel ik de Data Box-schijven? 
A.  Ga in Azure Portal naar uw Data Box Disk-bestelling en vervolgens naar **Apparaatdetails**. Kopieer de wachtwoordsleutel. Download in de Azure-portal het ontgrendelingsprogramma van Data Box Disk voor uw besturingssysteem en pak het programma uit. Voer het hulpprogramma uit op de computer waarop de gegevens staan die u wilt kopiëren naar de schijven. Geef de wachtwoordsleutel op om de schijven te ontgrendelen. U kunt alle schijven met dezelfde wachtwoordsleutel ontgrendelen. 

Voor stapsgewijze instructies gaat u naar [Schijven ontgrendelen op een Windows-client](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) of [Schijven ontgrendelen op een Linux-client](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>V. Kan ik een Linux-hostcomputer gebruiken om verbinding te maken met de gegevens en deze naar de Data Box-schijven te kopiëren?
A.  Ja. Zowel de Linux- als de Windows-client kan worden gebruikt om verbinding te maken en gegevens te kopiëren naar de Data Box-schijven. Ga naar de lijst met [Ondersteunde besturingssystemen](data-box-disk-system-requirements.md) voor uw hostcomputer voor meer informatie.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>V. Mijn schijven zijn verzonden, maar ik wil de bestelling annuleren. Waarom is er geen knop Annuleren?
A.  U kunt de bestelling alleen annuleren nadat de schijven zijn besteld en voordat ze worden verzonden. Zodra de schijven zijn verzonden, kunt u de bestelling niet meer annuleren. U kunt uw schijven tegen extra kosten echter wel retourneren. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>V. Kan de hostcomputer verbinding maken met meerdere Data Box-schijven tegelijk om gegevens over te dragen?
A. Ja. Er kunnen meerdere Data Box-schijven verbinding maken met dezelfde hostcomputer om gegevens over te dragen en er kunnen meerdere kopieertaken parallel worden uitgevoerd.

## <a name="track-status"></a>Status bijhouden

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>V. Hoe kan ik de bestelstatus van de schijven volgen vanaf het moment dat de bestelling is geplaatst als ik de schijven terug wil sturen? 
A.  U kunt de status van de Data Box Disk-bestelling volgen in Azure Portal. Als u de bestelling plaatst, wordt u ook gevraagd een e-mailadres voor meldingen op te geven. Als u dat hebt gedaan, dan krijgt u elke keer per e-mail een melding als er een statuswijziging heeft plaatsgevonden. Meer informatie over het [configureren van e-mailmeldingen](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>V. Hoe kan ik de schijven terugsturen? 
A.  In de verpakking van de Data Box-schijven zit een verzendlabel. Bevestig het label aan de doos met de schijven en geef de verzegelde verpakking af bij de verzender. Als het label beschadigd is of verloren is gegaan, gaat u naar **Overzicht > Verzendlabel downloaden** en downloadt u een nieuw verzendlabel.

## <a name="migrate-data"></a>Gegevens migreren

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>V. Wat is de maximale hoeveelheid gegevens die voor Data Box-schijven kan worden gebruikt?  
A.  Een Data Box Disk-oplossing kan uit maximaal vijf schijven bestaan met een maximale gebruikscapaciteit van 35 TB. De schijven zelf zijn elk 8 TB groot (waarvan 7 TB voor gebruik).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>V. Wat zijn de maximale groottes voor blok-blob en pagina-blob die voor Data Box-schijven worden ondersteund? 
A.  De maximale groottes worden bepaald door Azure Storage-limieten. De maximale blok-blob is ongeveer 4,768 TiB en de maximale blobgrootte is 8 TiB. Ga naar [Schaalbaarheids- en prestatiedoelen in Azure Storage](../storage/common/storage-scalability-targets.md) voor meer informatie.

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>V. Wat is de snelheid van gegevensoverdracht voor Data Box-schijven?
A. Bij een test met schijven die verbonden waren via USB 3.0 was de prestatie van de schijven maximaal 430 MB/s. De werkelijke waarden variëren, afhankelijk van de gebruikte bestandsgrootte. Voor kleinere bestanden zijn de prestaties lager.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>V. Hoe weet ik of mijn gegevens veilig zijn tijdens de overdracht? 
A.  Data Box-schijven zijn versleuteld met BitLocker AES-128-bitsversleuteling; de wachtwoordsleutel is alleen beschikbaar via Azure Portal. Meld u aan bij Azure Portal met uw accountreferenties om de wachtwoordsleutel op te halen. Geef deze wachtwoordsleutel op als u het Data Box Disk-ontgrendelingsprogramma wilt uitvoeren.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>V. Hoe kan ik de gegevens naar de Data Box-schijven kopiëren? 
A.  Gebruik een SMB-kopieerprogramma als Robocopy of Diskboss als u gegevens naar schijven wilt kopiëren. U kunt ook slepen vanuit Windows Verkenner.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>V. Zijn er tips om het kopiëren van gegevens te versnellen?
A.  U kunt het kopieerproces als volgt versnellen:

- Gebruik meerdere gegevensstromen. In bijvoorbeeld Robocopy kunt u de optie voor meerdere threads gebruiken. Meer informatie over de opdracht die u moet gebruiken is te vinden in [Zelfstudie: Gegevens kopiëren naar de Azure Data Box-schijf en deze gegevens controleren](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Gebruik meerdere sessies.
- In plaats van te kopiëren via een netwerkshare (waarbij de netwerksnelheid beperkend kan zijn), zorgt u ervoor dat de gegevens lokaal aanwezig zijn op de computer waarmee de schijven zijn verbonden.
- Zorg ervoor dat u USB 3.0 of hoger gebruikt gedurende het hele kopieerproces. Download en gebruik het hulpprogramma [USBView](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview) om de USB-controllers en de USB-apparaten te identificeren die met de computer zijn verbonden.
- Voer een benchmark-test uit van de prestaties van de computer die wordt gebruikt om de gegevens te kopiëren. Download en gebruik het hulpprogramma [Bluestop FIO](https://ci.appveyor.com/project/axboe/fio) om een benchmark-test uit te voeren van de prestaties van de serverhardware. Selecteer de nieuwste versie van x86 of x64, selecteer het tabblad artefacten en down load het MSI-bestand.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>V. Hoe kan ik het kopiëren van de gegevens versnellen als de brongegevens uit kleine bestanden (kB's of enkele MB's) bestaan?
A.  U kunt het kopieerproces als volgt versnellen:

- Maak een lokale VHDx in snelle opslag of maak een lege VHD op de HDD/SSD (langzamer).
- Koppel deze aan een VM.
- Kopieer de bestanden naar de schijf van de VM.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>V. Kan ik meerdere opslagaccounts gebruiken voor Data Box Disk?
A.  Nee. Er wordt momenteel slechts één (algemeen of klassiek) opslagaccount ondersteund voor Data Box Disk. Zowel dynamische als statische blob wordt ondersteund. Op dit moment worden alleen de opslagaccounts in de V.S. en in West- en Noord-Europa in de openbare Azure-cloud ondersteund.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>V. Welke hulpprogramma's zijn er beschikbaar voor mijn gegevens op Data Box-schijven?
A. Er zijn drie hulpprogramma's beschikbaar voor Data Box Disk:
 - Het **ontgrendelingsprogramma voor Data Box Disk**: Gebruik dit hulpprogramma om de versleutelde schijven van Microsoft te ontgrendelen. Wanneer u schijven ontgrendelt met het hulpprogramma moet u een wachtwoordsleutel opgeven. Deze vindt u in de gegevens van de Data Box Disk-bestelling in de Azure-portal. 
 - **Het validatieprogramma van Data Box Disk**: Gebruik dit hulpprogramma voor het controleren van de grootte, de indeling en de blob-namen op basis van de Azure-naamconventies. Hiermee worden ook controlesommen voor de gekopieerde gegevens gemaakt. Deze worden vervolgens gebruikt voor het verifiëren van de gegevens die naar Azure worden geüpload.
 - **Het splits- en kopieerprogramma voor Data Box Disk**: U dit hulpprogramma gebruiken als u meerdere schijven gebruikt en u over een grote gegevensset beschikt die moet worden gesplitst en gekopieerd naar alle schijven. Dit hulpprogramma is momenteel beschikbaar voor Windows. Dit hulp programma wordt niet ondersteund met Managed disks. Dit hulp programma valideert ook bij het kopiëren van de gegevens. Daarom kunt u de validatie stap overs Laan wanneer u dit hulp programma gebruikt.

De set hulpprogramma's is beschikbaar voor zowel Windows als Linux. U kunt de set hulpprogramma's hier downloaden:
- [Data Box Disk-toolset voor Windows downloaden](https://aka.ms/databoxdisktoolswin) 
- [Data Box Disk-toolset voor Linux downloaden](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>V. Kan ik Data Box Disk gebruiken om gegevens over te brengen naar Azure Files en vervolgens de gegevens gebruiken met Azure File Sync? 
A. Azure Files worden ondersteund met Data Box Disk, maar werken niet goed met Azure File Sync. Meta gegevens blijven niet behouden als de bestands gegevens worden gebruikt met Azure File Sync.


## <a name="verify-and-upload"></a>Verifiëren en uploaden

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>V. Wanneer heb ik weer toegang tot mijn gegevens in Azure als ik de schijven terug heb gestuurd? 
A.  Zodra de bestellingsstatus voor Gegevens kopiëren als voltooid is aangemerkt, hebt u weer toegang tot uw gegevens.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>V. Waar bevinden zich mijn gegevens in Azure na het uploaden?
A.  Als u de gegevens onder de mappen *BlockBlob* en *PageBlob* op uw schijf kopieert, wordt er voor elke submap onder de mappen *BlockBlob* en *PageBlob* een container gemaakt in het Azure Storage-account. Als u de bestanden onder de mappen *BlockBlob* en *PageBlob* rechtstreeks hebt gekopieerd, bevinden deze zich in de standaardcontainer *$root* onder het Azure Storage-account. Wanneer u de gegevens naar een map onder *AzureFile* map kopieert, wordt een file share gemaakt.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>V. Ik heb gemerkt dat ik de naamgevingsvereisten voor Azure niet voor mijn containers heb gevolgd. Worden mijn gegevens niet naar Azure geüpload?
A. Als de containernamen hoofdletters bevatten, worden deze automatisch naar kleine letters omgezet. Als de namen anderszins niet aan de vereisten voldoen (speciale tekens, andere taal, enzovoort), dan worden er geen gegevens geüpload. Ga voor meer informatie naar [Naamconventies](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>V. Hoe controleer ik de gegevens die ik naar meerdere Data Box-schijven heb gekopieerd?
A.  Als het kopiëren van de gegevens is voltooid, kunt u `DataBoxDiskValidation.cmd` uitvoeren in de map *DataBoxDiskImport* om controlesommen te genereren voor validatie. Als u meerdere schijven hebt, moet u per schijf een opdrachtvenster openen en deze opdracht uitvoeren. Houd er rekening mee dat deze bewerking lang kan duren (uren), afhankelijk van de grootte van de gegevens.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>V. Wat gebeurt er met de gegevens nadat ik de schijven heb teruggestuurd?
A.  Als de gegevens naar Azure zijn gekopieerd, worden de gegevens van de schijven veilig gewist volgens de richtlijnen van NIST SP 800-88 Revision 1.  

### <a name="q-how-is-my-data-protected-during-transit"></a>V. Hoe worden mijn gegevens tijdens het verzenden beveiligd? 
A.  De Data Box-schijven zijn versleuteld met AES-128 Microsoft BitLocker-stationsversleuteling. Hiervoor is één wachtwoordsleutel vereist om alle schijven en toegangsgegevens te ontgrendelen.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>V. Moet ik de controlesom opnieuw valideren als ik meer gegevens aan de Data Box-schijven toevoeg?
A. Ja. Als u besluit uw gegevens te valideren (aanbevolen), moet u de validatie opnieuw uit te voeren als u meer gegevens aan de schijven hebt toegevoegd.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>V. Ik heb al mijn schijven gebruikt om gegevens over te dragen. Ik wil meer schijven bestellen. Kan ik dat op een snelle manier doen?
A. U kunt een kloon maken van uw vorige bestelling. Hierdoor maakt u dezelfde bestelling als eerst en kunt u de details van de bestelling bewerken zonder dat u opnieuw uw adres, contactgegevens en meldingsinformatie hoeft te typen.

### <a name="q-i-copied-data-to-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-and-how-can-i-locate-it"></a>V. Ik heb gegevens gekopieerd naar de map ManagedDisk. Ik zie geen Managed disks met de resource groep die is opgegeven voor beheerde schijven. Zijn mijn gegevens geüpload naar Azure en hoe kan ik deze vinden?
A. Ja. Uw gegevens zijn geüpload naar Azure, maar als u geen beheerde schijven met de opgegeven resource groepen ziet, is het waarschijnlijk dat de gegevens ongeldig zijn. Als de pagina-blobs, blok-blobs, Azure Files en Managed disks niet geldig zijn, gaan ze naar de volgende mappen:
 - Pagina-blobs gaan naar een blok-BLOB-container die begint met *databoxdisk-ongeldig-PB*-.
 - Azure Files gaat naar een blok-BLOB-container die begint met *databoxdisk-ongeldig-af-* .
 - Beheerde schijven gaan naar een blok-BLOB-container, te beginnen met *databoxdisk-ongeldig-MD-* .

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [Data Box disk systeem vereisten](data-box-disk-system-requirements.md).
- De [Limieten voor Data Box Disk](data-box-disk-limits.md) begrijpen.
- Snel [Azure Data Box Disk](data-box-disk-quickstart-portal.md) in Azure Portal implementeren.
