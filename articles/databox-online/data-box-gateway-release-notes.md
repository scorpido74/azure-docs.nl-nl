---
title: Azure Data Box Gateway Release notes voor algemene beschikbaarheid| Microsoft Documenten
description: Beschrijft kritieke open problemen en oplossingen voor de Azure Data Box Gateway met de release van algemene beschikbaarheid.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265400"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure Data Box Edge/Azure Data Box Gateway Release notes General Availability

## <a name="overview"></a>Overzicht

In de volgende releasenotes worden de kritieke open problemen en de opgeloste problemen voor de release van De Algemene Beschikbaarheid (GA) voor Azure Data Box Edge en Azure Data Box Gateway geïdentificeerd.

De release notes worden voortdurend bijgewerkt en als kritieke problemen die een tijdelijke oplossing vereisen worden ontdekt, worden ze toegevoegd. Voordat u uw Data Box Edge/Data Box Gateway implementeert, controleert u zorgvuldig de informatie in de releasenotes.

De GA-release komt overeen met de softwareversies:

- **Data Box Gateway 1903 (1.5.814.447)**
- **Gegevensvakrand 1903 (1.5.814.447)**


## <a name="whats-new"></a>Nieuwe functies

- **Nieuwe virtuele schijfafbeeldingen** - Nieuwe VHDX en VMDK zijn nu beschikbaar in de Azure-portal. Download deze afbeeldingen om nieuwe Data Box Gateway GA-apparaten in te richten, configureren en implementeren. De Data Box Gateway-apparaten die in de eerdere preview-releases zijn gemaakt, kunnen niet naar deze versie worden bijgewerkt. Ga voor meer informatie naar [Voorbereiden om Azure Data Box Gateway te implementeren.](data-box-gateway-deploy-prep.md)
- **NFS-ondersteuning** - NFS-ondersteuning is momenteel in preview en beschikbaar voor v3.0- en v4.1-clients die toegang hebben tot de Data Box Edge- en Data Box Gateway-apparaten.
- **Opslagtolerantie** - Uw Data Box Edge-apparaat is bestand tegen het uitvallen van één gegevensschijf met de functie Opslagtolerantie. Deze functie is momenteel beschikbaar als preview-product. U opslagtolerantie inschakelen door de optie **Veerkrachtig te** selecteren in de **opslaginstellingen** in de lokale webgebruikersinterface.


## <a name="known-issues-in-ga-release"></a>Bekende problemen in GA release

In de volgende tabel vindt u een overzicht van bekende problemen voor de release van de Data Box Gateway-run.

| Nee. | Functie | Probleem | Tijdelijke oplossing/opmerkingen |
| --- | --- | --- | --- |
| **1.** |Bestandstypen | De volgende bestandstypen worden niet ondersteund: tekenbestanden, blokbestanden, sockets, leidingen, symbolische koppelingen.  |Als u deze bestanden kopieert, worden bestanden met 0-lengte gemaakt op het NFS-aandeel. Deze bestanden blijven in een foutstatus en worden ook gerapporteerd in *error.xml*. <br> Symbolische links naar mappen resulteren in mappen nooit steeds offline gemarkeerd. Als gevolg hiervan ziet u mogelijk niet het grijze kruis op de mappen dat aangeeft dat de mappen offline zijn en dat alle bijbehorende inhoud volledig is geüpload naar Azure. |
| **2.** |Verwijdering | Als gevolg van een bug in deze release, als een NFS-aandeel wordt verwijderd, kan het aandeel niet worden verwijderd. Op de sharestatus wordt *Verwijderen*weergegeven.  |Dit gebeurt alleen wanneer het aandeel een niet-ondersteunde bestandsnaam gebruikt. |
| **3.** |Exemplaar | Gegevenskopie mislukt met Fout: de gevraagde bewerking kan niet worden voltooid vanwege een beperking van het bestandssysteem.  |De alternatieve gegevensstroom (ADS) die is gekoppeld aan een bestandsgrootte groter dan 128 KB, wordt niet ondersteund.   |


## <a name="next-steps"></a>Volgende stappen

- [Bereid u voor op de implementatie van Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- [Bereid u voor op de implementatie van Azure Data Box Edge](data-box-edge-deploy-prep.md).
