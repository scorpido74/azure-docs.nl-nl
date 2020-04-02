---
title: Veelgestelde vragen voor azure import/exportservice | Microsoft Documenten
description: Lees antwoorden op veelgestelde vragen over azure import exportservice.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 904f93aa705a4e327f29bbec109bdf3b937f6c70
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519486"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Azure Import/Export-service: veelgestelde vragen

Hieronder volgen vragen en antwoorden die u hebben wanneer u uw Azure Import/Export-service gebruikt om gegevens over te dragen naar Azure-opslag. De vragen en antwoorden zijn in de volgende categorieën onderverdeeld:

- Over import/exportservice
- De schijven voorbereiden op import/export
- Taken importeren/exporteren
- Verzendschijven
- Diversen

## <a name="about-importexport-service"></a>Over import/exportservice

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Kan ik Azure File-opslag kopiëren met de Azure Import/Export-service?

Ja. De Azure Import/Export-service ondersteunt import in Azure File Storage. Het ondersteunt op dit moment geen export van Azure Files.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Is de Azure Import/Export-service beschikbaar voor CSP-abonnementen?

Ja. Azure Import/Export-service ondersteunt CSP-abonnementen (Cloud Solution Providers).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Kan ik de Azure Import/Export-service gebruiken om PST-postvakken en SharePoint-gegevens naar O365 te kopiëren?

Ja. Ga voor meer informatie naar [PST-bestanden of SharePoint-gegevens importeren in Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Kan ik de Azure Import/Export-service gebruiken om mijn back-ups offline te kopiëren naar de Azure Backup Service?

Ja. Ga voor meer informatie naar [de offline back-upwerkstroom in Azure Backup.](../../backup/backup-azure-backup-import-export.md)

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Kan ik schijven kopen voor import-/exporttaken van Microsoft?

Nee. U moet uw eigen schijven verzenden voor import- en exporttaken.

## <a name="preparing-disks-for-importexport"></a>Schijven voorbereiden op import/export

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Kan ik de stap voor het voorbereiden van een schijf voor een importtaak overslaan? Kan ik een station voorbereiden zonder te kopiëren?

Nee. Elk station dat wordt gebruikt om gegevens te importeren, moet worden voorbereid met het azure WAImportExport-hulpprogramma. Gebruik de tool om ook gegevens naar het station te kopiëren.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Moet ik schijfvoorbereiding uitvoeren bij het maken van een exporttaak?

Nee. Sommige voorcontroles worden aanbevolen. Als u het aantal vereiste schijven wilt controleren, gebruikt u de opdracht Voorbeeldexport van het gereedschap WAImportExport. Zie Voor meer informatie [het gebruik van stations voor een exporttaak bekijken](https://msdn.microsoft.com/library/azure/dn722414.aspx). Met de opdracht u een voorbeeld bekijken van het gebruik van de stations voor de geselecteerde blobs, op basis van de grootte van de stations die u gaat gebruiken. Controleer ook of u lezen van en schrijven naar de harde schijf die wordt verzonden voor de export taak.

## <a name="importexport-jobs"></a>Taken importeren/exporteren

### <a name="can-i-cancel-my-job"></a>Kan ik mijn baan opzeggen?

Ja. U een taak annuleren wanneer de status **Maken** of **verzenden is.** Buiten deze fasen kan de taak niet worden geannuleerd en gaat deze door tot de laatste fase.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Hoe lang kan ik de status van voltooide taken in de Azure-portal bekijken?

U de status voor voltooide taken maximaal 90 dagen bekijken. Voltooide taken worden na 90 dagen verwijderd.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Wat moet ik doen als ik meer dan 10 schijven wil importeren of exporteren?

Een import- of exporttaak kan slechts 10 stations in één taak verwijzen. Als u meer dan 10 schijven wilt verzenden, moet u meerdere taken maken. Schijven die aan dezelfde taak zijn gekoppeld, moeten samen in hetzelfde pakket worden verzonden.
Neem contact op met Microsoft Support voor meer informatie en richtlijnen wanneer de gegevenscapaciteit meerdere schijfimporttaken omvat.

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>De geüploade blob geeft de status weer als 'Lease verlopen'. Wat moet ik doen?

U het veld 'Verlopen lease' negeren. Import/Export neemt lease op de blob tijdens het uploaden om ervoor te zorgen dat geen enkel ander proces de blob parallel kan bijwerken. Lease verlopen houdt in dat Import/export niet meer uploadt en de blob beschikbaar is voor gebruik.

## <a name="shipping-disks"></a>Verzendschijven

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Wat is het maximum aantal HDD voor in een zending?

Er is geen limiet aan het aantal HDD's in één zending. Als de schijven tot meerdere taken behoren, raden we u aan:

- de schijven met bijbehorende taaknamen labelen.
- update de taken met een tracking nummer achterblijver met -1, -2 etc.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Moet ik iets anders dan de HDD in mijn pakket?

Verzend alleen uw harde schijven in het verzendpakket. Neem geen items zoals voedingskabels of USB-kabels.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Moet ik mijn schijven verzenden via FedEx of DHL?

U schijven verzenden naar het Azure-datacenter via een bekende vervoerder zoals FedEx, DHL, UPS of US Postal Service. Voor het retourneren van retourzendingen van schijven vanuit het datacenter moet u echter het als belangrijkste opgeven:

- Een FedEx-rekeningnummer in de VS en de EU, of
- Een DHL-rekeningnummer in de regio's Azië en Australië.

> [!NOTE]
> De datacenters in India vereisen een verklaring brief op uw briefhoofd (levering challan) om de stations terug te keren. Om de vereiste toegangspas te regelen, moet u ook de pick-up boeken bij de geselecteerde provider en de details delen met het datacenter.

### <a name="are-there-any-restrictions-with-shipping-and-returning-my-drive-internationally"></a>Zijn er beperkingen met verzending en het retourneren van mijn rit internationaal?

Houd er rekening mee dat de fysieke media die u verzendt mogelijk internationale grenzen moeten overschrijden. U bent verantwoordelijk voor het importeren en/of exporteren van uw fysieke media en gegevens in overeenstemming met de geldende wetgeving. Neem contact op met uw adviseurs voordat u de fysieke media verzendt om te controleren of uw media en gegevens legaal naar het geïdentificeerde datacenter kunnen worden verzonden. Dit zal helpen om ervoor te zorgen dat het Microsoft bereikt in een tijdige wijze.

Nadat de upload is voltooid, kan het proces om drive(s) terug te sturen naar een internationaal adres langer duren dan de typische 2-3 dagen die nodig zijn voor lokale verzending. Tijdens de fase die in de Azure-portal als Verpakking wordt vermeld, zorgt het Data Box-team ervoor dat de juiste documentatie wordt verstrekt om ervoor te zorgen dat de zending voldoet aan de verschillende internationale import- en exportvereisten.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Zijn er speciale vereisten voor het leveren van mijn schijven aan een datacenter?

De vereisten zijn afhankelijk van de specifieke Azure-datacenterbeperkingen.

- Er zijn een paar sites, zoals Australië, Duitsland en het Verenigd Koninkrijk Zuid, die vereisen dat een Microsoft datacenter Inbound ID-nummer worden geschreven op het pakket om veiligheidsredenen. Voordat u uw schijven of schijven naar het datacenteradbops@microsoft.comverzendt, neemt u contact op met Azure DataBox Operations ( ) om dit nummer op te halen. Zonder dit nummer wordt het pakket afgewezen.
- De datacenters in India vereisen de persoonlijke gegevens van de bestuurder, zoals de Government ID Card of Proof No. (bijvoorbeeld PAN, AADHAR, DL), naam, contact en het kenteken van de auto om een toegangspas voor de poort te krijgen. Om vertragingen bij de levering te voorkomen, moet u uw provider informeren over deze vereisten.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Bij het maken van een taak is het verzendadres een locatie die verschilt van mijn opslagaccountlocatie. Wat moet ik doen?

Sommige opslagaccountlocaties zijn toegewezen aan alternatieve verzendlocaties. Eerder beschikbare verzendlocaties kunnen ook tijdelijk worden toegewezen aan alternatieve locaties. Controleer altijd het verzendadres dat wordt opgegeven tijdens het maken van een taak voordat u uw schijven verzendt.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Bij het verzenden van mijn schijf, de vervoerder vraagt om het datacenter contactadres en telefoonnummer. Wat moet ik bieden?

Het telefoonnummer en het DC-adres worden opgegeven als onderdeel van het scheppen van banen.

## <a name="miscellaneous"></a>Diversen

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Wat gebeurt er als ik per ongeluk een HDD stuur die niet voldoet aan de ondersteunde vereisten?

Het Azure-datacenter retourneert het station dat niet aan de ondersteunde vereisten voldoet. Als slechts een deel van de schijven in het pakket voldoet aan de ondersteuningsvereisten, worden deze schijven verwerkt en worden de schijven die niet aan de vereisten voldoen, aan u geretourneerd.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Maakt de service de stations voordat u ze retourt?

Nee. Alle schijven zijn versleuteld met BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Hoe kan ik toegang krijgen tot gegevens die door deze service worden geïmporteerd?

Gebruik de Azure-portal of [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) om toegang te krijgen tot de gegevens onder uw Azure-opslagaccount.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Hoe zien mijn gegevens eruit nadat de import is voltooid in het opslagaccount? Is mijn directoryhiërarchie behouden?

Wanneer u een harde schijf voorbereidt op een importtaak, wordt de bestemming opgegeven door het veld DstBlobPathOrPrefix in de csv-gegevensset. Dit is de doelcontainer in het opslagaccount waarnaar gegevens van de harde schijf worden gekopieerd. Binnen deze doelcontainer worden virtuele mappen gemaakt voor mappen van de harde schijf en blobs voor bestanden.

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Als een station bestanden heeft die al bestaan in mijn opslagaccount, overschrijft de service dan bestaande blobs of bestanden?

Hangt. Bij het voorbereiden van het station u opgeven of de doelbestanden moeten worden overschreven of genegeerd met behulp van het veld in het CSV-bestand met de naam Disposition:<herschrijven|niet-overschrijven|overschrijven>. Standaard hernoemt de service de nieuwe bestanden in plaats van bestaande blobs of bestanden te overschrijven.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Is de WAImportExport tool compatibel met 32-bits besturingssystemen?

Nee. De WAImportExport tool is alleen compatibel met 64-bits Windows-besturingssystemen. Ga voor een volledige lijst met ondersteunde besturingssysteem naar [ondersteunde besturingssystemen](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Wat is de maximale blokblob- en paginablobgrootte die wordt ondersteund door Azure Import/Export?

- De maximale blokblobgrootte is ongeveer 4.768 TB of 5.000.000 MB.
- Max Page Blob grootte is 8TB.

### <a name="does-azure-importexport-support-aes-256-encryption"></a>Ondersteunt Azure Import/Export AES-256-versleuteling?

Nee. Azure Import/Export-service maakt gebruik van AES-128 BitLocker-versleuteling.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure Import/Export?](storage-import-export-service.md)
