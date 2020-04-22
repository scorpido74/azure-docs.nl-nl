---
title: Migreren naar Azure-bestandsshares
description: Meer informatie over migraties naar Azure-bestandsshares en vind uw migratiegids.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6141d48d67dd44c348961c6e09acf4e2531a61e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685995"
---
# <a name="migrate-to-azure-file-shares"></a>Migreren naar Azure-bestandsshares

Dit artikel behandelt de basisaspecten van een migratie naar Azure-bestandsshares.

Dit artikel bevat migratiebasisbeginselen en een tabel met migratiegidsen. Met deze hulplijnen u uw bestanden verplaatsen naar Azure-bestandsshares. De hulplijnen worden georganiseerd op basis van waar uw gegevens zich bevinden en naar welk implementatiemodel (alleen in de cloud of hybride) u naartoe gaat.

## <a name="migration-basics"></a>Basisbeginselen van migratie

Azure heeft meerdere beschikbare typen cloudopslag. Een fundamenteel aspect van bestandsmigraties naar Azure is bepalen welke Azure-opslagoptie geschikt is voor uw gegevens.

[Azure-bestandsshares](storage-files-introduction.md) zijn geschikt voor bestandsgegevens voor algemene doeleinden. Deze gegevens bevatten alles waarvoor u een on-premises SMB- of NFS-share gebruikt. Met [Azure File Sync](storage-sync-files-planning.md)u de inhoud van verschillende Azure-bestandsshares opslaan op servers waarop Windows Server on-premises wordt uitgevoerd.

Voor een app die momenteel op een on-premises server wordt uitgevoerd, is het opslaan van bestanden in een Azure-bestandsshare mogelijk een goede keuze. U de app verplaatsen naar Azure en Azure-bestandsshares gebruiken als gedeelde opslag. U [Azure Disks](../../virtual-machines/windows/managed-disks-overview.md) ook voor dit scenario overwegen.

Sommige cloud-apps zijn niet afhankelijk van kmo's of van computerlokale gegevenstoegang of gedeelde toegang. Voor die apps is objectopslag zoals [Azure blobs](../blobs/storage-blobs-overview.md) vaak de beste keuze.

De sleutel in elke migratie is het vastleggen van alle toepasselijke bestandsgetrouwheid bij het verplaatsen van uw bestanden van hun huidige opslaglocatie naar Azure. Hoeveel getrouwheid de Azure-opslagoptie ondersteunt en hoeveel uw scenario vereist, helpt u ook bij het kiezen van de juiste Azure-opslag. Bestandsgegevens voor algemene doeleinden zijn traditioneel afhankelijk van bestandsmetagegevens. App-gegevens mogelijk niet.

Hier zijn de twee basiscomponenten van een bestand:

- **Gegevensstroom:** de gegevensstroom van een bestand slaat de bestandsinhoud op.
- **Bestandsmetagegevens**: De bestandsmetagegevens hebben deze subcomponenten:
   * Bestandskenmerken zoals alleen-lezen
   * Bestandsmachtigingen, die *ntfs-machtigingen of acl.ntfs-machtigingen of acl.n.s.-bestanden* *en map-ACL's* kunnen worden genoemd
   * Tijdstempels, met name de creatie en laatste gewijzigde tijdstempels
   * Een alternatieve gegevensstroom, die een ruimte is om grotere hoeveelheden niet-standaardeigenschappen op te slaan

Bestandstrouw in een migratie kan worden gedefinieerd als de mogelijkheid om:

- Sla alle toepasselijke bestandsinformatie op de bron op.
- Bestanden overbrengen met het migratiehulpprogramma.
- Bestanden opslaan in de doelopslag van de migratie.

Om ervoor te zorgen dat uw migratie soepel verloopt, identificeert u [de beste kopieertool voor uw behoeften](#migration-toolbox) en matcht u een opslagdoel met uw bron.

Rekening houdend met de vorige informatie, u zien dat de doelopslag voor algemene bestanden in Azure [Azure-bestandsshares is.](storage-files-introduction.md)

In tegenstelling tot objectopslag in Azure blobs kan een Azure-bestandsshare bestandsmetagegevens native opslaan. Azure-bestandsshares behouden ook de bestands- en maphiërarchie, kenmerken en machtigingen. NTFS-machtigingen kunnen worden opgeslagen in bestanden en mappen omdat ze on-premises zijn.

Een gebruiker van Active Directory, de on-premises domeincontroller, heeft native toegang tot een Azure-bestandsshare. Dat geldt ook voor een gebruiker van Azure Active Directory Domain Services (Azure AD DS). Elk gebruikt zijn huidige identiteit om toegang te krijgen op basis van machtigingen voor delen en op acl.a.s. Dit gedrag is vergelijkbaar met een gebruiker die verbinding maakt met een on-premises bestandsshare.

De alternatieve gegevensstroom is het primaire aspect van bestandstrouw dat momenteel niet kan worden opgeslagen in een bestand in een Azure-bestandsshare. Het blijft on-premises behouden wanneer Azure File Sync wordt gebruikt.

Meer informatie over [Azure AD-verificatie](storage-files-identity-auth-active-directory-enable.md) en [Azure AD DS-verificatie](storage-files-identity-auth-active-directory-domain-service-enable.md) voor Azure-bestandsshares.

## <a name="migration-guides"></a>Migratiehandleidingen

In de volgende tabel vindt u gedetailleerde migratiegidsen.

De tabel gebruiken:

1. Zoek de rij voor het bronsysteem waarop uw bestanden momenteel zijn opgeslagen.

1. Kies een van deze doelen:

   - Een hybride implementatie met Azure File Sync om de inhoud van Azure-bestandsshares on-premises in de cache te opslaan
   - Azure-bestandsshares in de cloud

   Selecteer de doelkolom die overeenkomt met uw keuze.

1. Binnen het snijpunt van bron en doel geeft een tabelcel beschikbare migratiescenario's weer. Selecteer er een om rechtstreeks een koppeling te maken naar de gedetailleerde migratiegids.

Een scenario zonder koppeling heeft nog geen gepubliceerde migratiegids. Controleer deze tabel af en toe voor updates. Nieuwe gidsen worden gepubliceerd wanneer ze beschikbaar zijn.

| Bron | Doel: </br>Hybride implementatie | Doel: </br>Implementatie in de cloud alleen |
|:---|:--|:--|
| | Gereedschapscombinatie:| Gereedschapscombinatie: |
| Windows Server 2012 R2 en hoger | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Azure-bestandssynchronisatie en Azure-gegevensvak](storage-sync-offline-data-transfer.md)</li><li>Azure-service voor bestandssynchronisatie en -opslag</li></ul> | <ul><li>Azure File Sync</li><li>Azure-bestandssynchronisatie en gegevensvak</li><li>Azure-service voor bestandssynchronisatie en -opslag</li><li>Robocopy</li></ul> |
| Windows Server 2012 en eerder | <ul><li>Azure-bestandssynchronisatie en gegevensvak</li><li>Azure-service voor bestandssynchronisatie en -opslag</li></ul> | <ul><li>Azure-service voor bestandssynchronisatie en -opslag</li><li>Robocopy</li></ul> |
| Netwerkopslag (NAS) | <ul><li>[Azure File Sync en RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux of Samba | <ul><li>[Azure File Sync en RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 of StorSimple Cloud Appliance 8600 | <ul><li>[Azure File Sync en StorSimple Cloud Appliance 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Migratiegereedschapsset

### <a name="file-copy-tools"></a>Hulpmiddelen voor bestandskopiëren

Er zijn verschillende tools voor bestandskopie beschikbaar bij Microsoft en anderen. Als u het juiste hulpprogramma voor uw migratiescenario wilt selecteren, moet u rekening houden met deze fundamentele vragen:

* Ondersteunt de tool de bron- en doellocaties voor uw bestandskopie?

* Ondersteunt de tool uw netwerkpad of beschikbare protocollen (zoals REST, SMB of NFS) tussen de bron- en doelopslaglocaties?

* Behoudt de tool de benodigde bestandstrouw die wordt ondersteund door uw bron- en doellocaties?

    In sommige gevallen ondersteunt uw doelopslag niet dezelfde getrouwheid als uw bron. Als de doelopslag voldoende is voor uw behoeften, moet de tool alleen overeenkomen met de bestandsgetrouwheidsmogelijkheden van het doelwit.

* Heeft de tool functies die het laten passen in uw migratiestrategie?

    Bedenk bijvoorbeeld of u met de tool uw downtime minimaliseren.
    
    Wanneer een tool een optie ondersteunt om een bron te spiegelen aan een doel, u deze vaak meerdere keren op dezelfde bron en doel uitvoeren terwijl de bron toegankelijk blijft.

    De eerste keer dat u het gereedschap uitvoert, kopieert het het grootste deel van de gegevens. Deze eerste run kan een tijdje duren. Het duurt vaak langer dan u wilt voor het offline halen van de gegevensbron voor uw bedrijfsprocessen.

    Door een bron te spiegelen aan een doel (zoals bij **robocopy /MIR),** u het gereedschap opnieuw uitvoeren op dezelfde bron en doelstelling. De run is veel sneller omdat het alleen bronwijzigingen moet transporteren die optreden na de vorige run. Het opnieuw uitvoeren van een kopieertool op deze manier kan downtime aanzienlijk verminderen.

In de volgende tabel worden Microsoft-hulpprogramma's en hun huidige geschiktheid voor Azure-bestandsshares geklasseerd:

| Aanbevolen | Hulpprogramma | Ondersteuning voor Azure-bestandsshares | Behoud van bestandstrouw |
| :-: | :-- | :---- | :---- |
|![Ja, aanbevolen](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Ondersteund. Azure-bestandsshares kunnen worden gemonteerd als netwerkstations. | Volledige trouw.* |
|![Ja, aanbevolen](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Native geïntegreerd in Azure-bestandsshares. | Volledige trouw.* |
|![Ja, aanbevolen](media/storage-files-migration-overview/circle-green-checkmark.png)| Service voor opslagmigratie | Indirect ondersteund. Azure-bestandsshares kunnen worden gemonteerd als netwerkstations op sms-doelservers. | Volledige trouw.* |
|![Niet volledig aanbevolen](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Data Box | Ondersteund. | Kopieert geen metadata. [Gegevensvak kan worden gebruikt met Azure File Sync](storage-sync-offline-data-transfer.md). |
|![Niet aanbevolen](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Ondersteund. | Kopieert geen metadata. |
|![Niet aanbevolen](media/storage-files-migration-overview/circle-red-x.png)| Azure Opslagverkenner | Ondersteund. | Kopieert geen metadata. |
|![Niet aanbevolen](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Ondersteund. | Kopieert geen metadata. |
|||||

*\*Volledige getrouwheid: voldoet aan of overtreft azure-mogelijkheden voor bestandsshare.*

### <a name="migration-helper-tools"></a>Hulpprogramma's voor migratiehelper

In deze sectie worden hulpprogramma's beschreven waarmee u migraties plannen en uitvoeren.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy van Microsoft Corporation

RoboCopy is een van de tools die het meest van toepassing zijn op bestandsmigraties. Het komt als onderdeel van Windows. De belangrijkste [RoboCopy-documentatie](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) is een nuttige bron voor de vele opties van deze tool.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize van JAM Software GmbH

Azure File Sync schaalt voornamelijk met het aantal items (bestanden en mappen) en niet met het totale opslagbedrag. Met het gereedschap TreeSize u het aantal items op uw Windows Server-volumes bepalen.

U het hulpprogramma gebruiken om een perspectief te maken voordat een [Azure File Sync-implementatie wordt geïmplementeerd.](storage-sync-files-deployment-guide.md) U het ook gebruiken wanneer cloudtiering is ingeschakeld na implementatie. In dat scenario ziet u het aantal items en welke mappen uw servercache het meest gebruiken.

De geteste versie van de tool is versie 4.4.1. Het is compatibel met cloud-tiered bestanden. De tool zorgt er niet voor dat er tijdens de normale werking geen trapsgewijze bestanden worden teruggeroepen.

## <a name="next-steps"></a>Volgende stappen

1. Maak een plan voor welke implementatie van Azure-bestandsshares (alleen in de cloud of hybride) u wilt.
1. Bekijk de lijst met beschikbare migratiegidsen om de gedetailleerde handleiding te vinden die overeenkomt met uw bron en implementatie van Azure-bestandsshares.

Hier vindt u meer informatie over de Azure Files-technologieën die in dit artikel worden genoemd:

* [Overzicht van Azure-bestandsshare](storage-files-introduction.md)
* [Planning voor de implementatie van Azure Files Sync](storage-sync-files-planning.md)
* [Azure-bestandssynchronisatie: cloudlaag](storage-sync-cloud-tiering.md)
