---
title: Een Azure HPC-cache maken
description: Een Azure HPC-cache-exemplaar maken
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: befbe2435a518b82cf5a3ab12e6129aa3ce5c22b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537968"
---
# <a name="create-an-azure-hpc-cache"></a>Een Azure HPC-cache maken

Gebruik de Azure-portal om uw cache te maken.

![schermafbeelding van cacheoverzicht in Azure-portal, met knop maken onderaan](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Basisdetails definiëren

![schermafbeelding van de pagina projectdetails in Azure-portal](media/hpc-cache-create-basics.png)

Selecteer **in Projectdetails**de abonnements- en brongroep die de cache host.

Stel **in Servicegegevens**de cachenaam en deze andere kenmerken in:

* Locatie - Selecteer een van de [ondersteunde regio's](hpc-cache-overview.md#region-availability).
* Virtueel netwerk - U een bestaand netwerk selecteren of een nieuw virtueel netwerk maken.
* Subnet - Kies of maak een subnet met ten minste 64 IP-adressen (/24) die alleen worden gebruikt voor dit Azure HPC-cacheexemplaar.

## <a name="set-cache-capacity"></a>Cachecapaciteit instellen
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Op de pagina **Cache** moet u de capaciteit van uw cache instellen. De waarden die hier zijn ingesteld, bepalen hoeveel gegevens uw cache kan bevatten en hoe snel de clientaanvragen kunnen worden uitgevoerd.

Capaciteit heeft ook invloed op de kosten van de cache.

Kies de capaciteit door deze twee waarden in te stellen:

* De maximale gegevensoverdrachtssnelheid voor de cache (doorvoer), in GB/seconde
* De hoeveelheid opslagruimte die is toegewezen voor gegevens in de cache, in TB

Kies een van de beschikbare doorvoerwaarden en cacheopslagformaten.

Houd er rekening mee dat de werkelijke gegevensoverdrachtssnelheid afhankelijk is van de werkbelasting, netwerksnelheden en het type opslagdoelen. De waarden die u kiest, stellen de maximale doorvoer in voor het hele cachesysteem, maar een deel daarvan wordt gebruikt voor overheadtaken. Als een client bijvoorbeeld een bestand aanvraagt dat nog niet in de cache is opgeslagen of als het bestand als verouderd is gemarkeerd, gebruikt uw cache een deel van de doorvoer om het bestand uit backend-opslag te halen.

Azure HPC Cache beheert welke bestanden in de cache worden opgeslagen en vooraf zijn geladen om de hitsnelheden in de cache te maximaliseren. De inhoud van de cache wordt continu beoordeeld en bestanden worden verplaatst naar langdurige opslag wanneer ze minder vaak worden geopend. Kies een cacheopslaggrootte die de actieve set werkbestanden comfortabel kan vasthouden met extra ruimte voor metagegevens en andere overheadgegevens.

![schermafbeelding van de pagina cachegrootte](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Azure Key Vault-versleuteling inschakelen (optioneel)

Als uw cache zich in een regio bevindt die door de klant beheerde versleutelingssleutels ondersteunt, wordt de pagina **Schijfversleutelingssleutels** weergegeven tussen de tabbladen **Cache** en **Tags.** Vanaf publicatietijd wordt deze optie ondersteund in Oost-VS, South Central US en West US 2.

Als u de versleutelingssleutels wilt beheren die met uw cacheopslag worden gebruikt, geeft u uw Azure Key Vault-gegevens op de pagina **Schijfversleutelingssleutels.** De sleutelkluis moet zich in dezelfde regio en in hetzelfde abonnement als de cache bevinden.

U deze sectie overslaan als u geen door de klant beheerde sleutels nodig hebt. Azure versleutelt standaard gegevens met door Microsoft beheerde sleutels. Lees [Azure-opslagversleuteling](../storage/common/storage-service-encryption.md) voor meer informatie.

> [!NOTE]
>
> * U niet wijzigen tussen door Microsoft beheerde sleutels en door de klant beheerde sleutels nadat u de cache hebt gemaakt.
> * Nadat de cache is gemaakt, moet u deze autoriseren om toegang te krijgen tot de sleutelkluis. Klik **op** de knop Versleuteling inschakelen op de **pagina Overzicht** van de cache om versleuteling in te schakelen. Neem deze stap binnen 90 minuten na het maken van de cache.
> * Cacheschijven worden gemaakt na deze autorisatie. Dit betekent dat de initiële cachecreatietijd kort is, maar dat de cache tien minuten of langer niet klaar is voor gebruik nadat u de toegang hebt geautoriseerd.

Lees [Klantbeheersleutelsleutels gebruiken voor Azure HPC-cache voor](customer-keys.md)een volledige uitleg over het door de klant beheerde sleutelversleutelingsproces.

![schermafbeelding van de pagina versleutelingssleutels met 'door de klant beheerde' geselecteerde en sleutelkluisvelden met](media/create-encryption.png)

Selecteer **Klant is erin geslaagd** om door de klant beheerde sleutelversleuteling te kiezen. De specificatievelden voor belangrijke kluizen worden weergegeven. Selecteer de Azure Key Vault die u wilt gebruiken en selecteer vervolgens de sleutel en versie die u voor deze cache wilt gebruiken. De sleutel moet een 2048-bit RSA-toets zijn. U vanaf deze pagina een nieuwe sleutelkluis, sleutel of sleutelversie maken.

Nadat u de cache hebt gemaakt, moet u deze autoriseren om de key vault-service te gebruiken. Lees [Azure Key Vault-versleuteling uit de cache autoriseren](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) voor meer informatie.

## <a name="add-resource-tags-optional"></a>Resourcetags toevoegen (optioneel)

Op de pagina **Labels** u [brontags](https://go.microsoft.com/fwlink/?linkid=873112) toevoegen aan uw Azure HPC-cache-exemplaar.

## <a name="finish-creating-the-cache"></a>De cache maken

Nadat u de nieuwe cache hebt geconfigureerd, klikt u op het tabblad **Controleren + maken.** De portal valideert uw selecties en laat u uw keuzes bekijken. Als alles juist is, klikt u op **Maken**.

Het maken van caches duurt ongeveer 10 minuten. U de voortgang bijhouden in het meldingenpaneel van de Azure-portal.

![schermafbeelding van pagina's voor het maken van cache 'deployment underway' en 'notifications' in portal](media/hpc-cache-deploy-status.png)

Wanneer het maken is voltooid, wordt een melding weergegeven met een koppeling naar het nieuwe exemplaar Azure HPC-cache en wordt de cache weergegeven in de lijst **Resources** van uw abonnement.

![schermafbeelding van azure HPC-cache-instantie in Azure-portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Als uw cache door de klant beheerde versleutelingssleutels gebruikt, wordt de cache mogelijk weergegeven in de lijst met resources voordat de implementatiestatus is voltooid. Zodra de status van de cache wacht **op de sleutel,** u [deze autoriseren](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) om de sleutelkluis te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Nadat de cache in de lijst **Resources** is weergegeven, u naar de volgende stap gaan.

* [Definieer opslagdoelen](hpc-cache-add-storage.md) om uw cache toegang te geven tot uw gegevensbronnen.
* Als u door de klant beheerde versleutelingssleutels gebruikt, moet u [Azure Key Vault-versleuteling autoriseren](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) vanaf de overzichtspagina van de cache om de cache-instelling te voltooien. U moet deze stap doen voordat u opslag toevoegen. Lees [Gebruik door de klant beheerde versleutelingssleutels](customer-keys.md) voor meer informatie.
