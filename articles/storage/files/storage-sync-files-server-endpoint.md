---
title: Een eindpunt van een Azure File Sync-server toevoegen/verwijderen | Microsoft Documenten
description: Lees waar u rekening mee moet houden bij het plannen van een Azure-bestandenimplementatie.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 684b30a24e049722cb531cbc84e3a2cd90912ec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255104"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Een eindpunt van een Azure File Sync-server toevoegen/verwijderen
Met Azure File Sync kunt u bestandsshares van uw organisatie in Azure Files centraliseren zonder in te leveren op de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Dit gebeurt door uw Windows-servers om te zetten in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is in Windows Server gebruiken voor lokale toegang tot uw gegevens (inclusief SMB, NFS en FTPS) en u kunt zoveel caches hebben als waar ook ter wereld u nodig hebt.

Een *servereindpunt* vertegenwoordigt een specifieke locatie op een *geregistreerde server,* zoals een map op een servervolume of de hoofdmap van het volume. Er kunnen meerdere servereindpunten op hetzelfde volume bestaan als hun naamruimten elkaar niet overlappen (bijvoorbeeld F:\sync1 en F:\sync2). U voor elk servereindpunt afzonderlijk beleid voor cloudtiering configureren. Als u een serverlocatie met een bestaande set bestanden als servereindpunt toevoegt aan een synchronisatiegroep, worden deze bestanden samengevoegd met andere bestanden die al op andere eindpunten in de synchronisatiegroep zijn.

Zie [Azure File Sync implementeren](storage-sync-files-deployment-guide.md) voor informatie over het end-to-end implementeren van Azure File Sync.

## <a name="prerequisites"></a>Vereisten
Als u een servereindpunt wilt maken, moet u er eerst voor zorgen dat aan de volgende criteria wordt voldaan: 
- De server heeft de Azure File Sync-agent geïnstalleerd en is geregistreerd. Instructies voor het installeren van de Azure File Sync Agent zijn te vinden in het [artikel Een server registreren/uitschrijven met Azure File Sync.](storage-sync-files-server-registration.md) 
- Controleer of er een Storage Sync-service is geïmplementeerd. Zie [Azure File Sync implementeren](storage-sync-files-deployment-guide.md) voor meer informatie over het implementeren van een Opslagsynchronisatieservice. 
- Controleer of een synchronisatiegroep is geïmplementeerd. Meer informatie over het [maken van een synchronisatiegroep](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Controleer of de server is verbonden met internet en dat Azure toegankelijk is. We gebruiken poort 443 voor alle communicatie tussen de server en onze service.

## <a name="add-a-server-endpoint"></a>Servereindpunt toevoegen
Als u een servereindpunt wilt toevoegen, navigeert u naar de gewenste synchronisatiegroep en selecteert u 'Servereindpunt toevoegen'.

![Nieuw servereindpunt toevoegen in het deelvenster met de synchronisatiegroep](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

De volgende informatie is vereist onder **Servereindpunt toevoegen:**

- **Geregistreerde server:** de naam van de server of het cluster om het servereindpunt op te maken.
- **Pad:** het pad op de Windows Server dat moet worden gesynchroniseerd als onderdeel van de synchronisatiegroep.
- **Cloudtiering:** een switch om cloudtiering in of uit te schakelen. Wanneer ingeschakeld, worden cloudlagenbestanden *gelaagd* naar uw Azure-bestandsshares. Hiermee worden on-premises bestandsshares omgezet in een cache, in plaats van een volledige kopie van de gegevensset, om u te helpen de ruimteefficiëntie op uw server te beheren.
- **Volumevrije ruimte:** de hoeveelheid vrije ruimte om te reserveren op het volume dat het servereindpunt bevindt. Als de volumevrije ruimte bijvoorbeeld is ingesteld op 50% op een volume met één servereindpunt, wordt ongeveer de helft van de hoeveelheid gegevens gelaagd naar Azure-bestanden. Ongeacht of cloudtiering is ingeschakeld, heeft uw Azure-bestandsshare altijd een volledige kopie van de gegevens in de synchronisatiegroep.

Selecteer **Maken** om het servereindpunt toe te voegen. De bestanden binnen een naamruimte van een synchronisatiegroep worden nu gesynchroniseerd. 

## <a name="remove-a-server-endpoint"></a>Een servereindpunt verwijderen
Als u azure bestandssynchronisatie voor een bepaald servereindpunt wilt stopzetten, u het servereindpunt verwijderen. 

> [!Warning]  
> Probeer geen problemen met synchronisatie, cloudtiering of enig ander aspect van Azure File Sync op te lossen door het eindpunt van de server te verwijderen en opnieuw te maken, tenzij dit expliciet is inopdracht gegeven door een Microsoft-technicus. Het verwijderen van een servereindpunt is een destructieve bewerking en gelaagde bestanden binnen het servereindpunt worden niet 'opnieuw' verbonden met hun locaties in het Azure-bestandsshare nadat het servereindpunt opnieuw is gemaakt, wat resulteert in synchronisatiefouten. Houd er ook rekening mee dat gelaagde bestanden die buiten de naamruimte van het servereindpunt bestaan, permanent verloren kunnen gaan. Er kunnen gelaagde bestanden bestaan binnen het eindpunt van uw server, zelfs als cloudtiering nooit is ingeschakeld.

Als u ervoor wilt zorgen dat alle gelaagde bestanden worden teruggeroepen voordat u het servereindpunt verwijdert, schakelt u cloudtiering op het servereindpunt uit en voert u de volgende PowerShell-cmdlet uit om alle gelaagde bestanden in de naamruimte van uw servereindpunt op te roepen:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
Als `-Order CloudTieringPolicy` u opgeeft, worden eerst de meest recent gewijzigde bestanden in herinnering geroepen.
Andere optionele maar nuttige parameters om rekening mee te houden zijn:
* `-ThreadCount`bepaalt het aantal bestanden parallel kan worden teruggeroepen.
* `-PerFileRetryCount`hiermee wordt bepaald hoe vaak een terugroepactie wordt geprobeerd van een bestand dat momenteel is geblokkeerd.
* `-PerFileRetryDelaySeconds`bepaalt de tijd in seconden tussen het opnieuw inroepen van pogingen en moet altijd worden gebruikt in combinatie met de vorige parameter.

> [!Note]  
> Als het lokale volume dat de server host niet genoeg vrije `Invoke-StorageSyncFileRecall` ruimte heeft om alle gelaagde gegevens terug te roepen, mislukt de cmdlet.  

Ga als een voorbeeld voor het verwijderen van het eindpunt van de server:

1. Navigeer naar de opslagsynchronisatieservice waar uw server is geregistreerd.
2. Navigeer naar de gewenste synchronisatiegroep.
3. Verwijder het gewenste servereindpunt in de synchronisatiegroep in de opslagsynchronisatieservice. Dit kan worden bereikt door met de rechtermuisknop op het desbetreffende servereindpunt in het deelvenster synchronisatiegroep te klikken.

    ![Een servereindpunt uit een synchronisatiegroep verwijderen](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Volgende stappen
- [Een server registreren/uitschrijven met Azure File Sync](storage-sync-files-server-registration.md)
- [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
- [Azure File Sync bewaken](storage-sync-files-monitoring.md)
