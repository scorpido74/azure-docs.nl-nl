---
title: Onbedoeld verwijderen voor komen-Azure-bestands shares
description: Meer informatie over zacht verwijderen voor Azure-bestands shares en hoe u deze kunt gebruiken voor het herstellen van gegevens en het voor komen van onopzettelijke verwijderingen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 96e3d5001d11455337ae092776a1a4c5c3738012
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83883070"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Onopzettelijke verwijdering van Azure-bestands shares voor komen

Azure Storage biedt nu een tijdelijke verwijdering voor bestands shares. Met zacht verwijderen kunt u uw gegevens herstellen wanneer deze per ongeluk worden verwijderd door een toepassing of een ander opslag account.

## <a name="how-soft-delete-works"></a>Hoe zacht verwijderen werkt

Wanneer deze optie is ingeschakeld, kunt u met zacht verwijderen uw bestands shares opslaan en herstellen wanneer ze worden verwijderd. Wanneer gegevens worden verwijderd, wordt deze overgezet naar een voorlopig verwijderde status in plaats van dat ze permanent worden gewist. U kunt de hoeveelheid tijd waarvoor tijdelijke verwijderde gegevens worden hersteld, configureren voordat deze permanent wordt verwijderd.

Zacht verwijderen kan worden ingeschakeld op nieuwe of bestaande bestands shares. Voorlopig verwijderen is ook achterwaarts compatibel, zodat u geen wijzigingen hoeft aan te brengen in uw toepassingen om te kunnen profiteren van de beveiliging van de tijdelijke verwijdering. 

Voor soft-verwijderde Premium-bestands shares wordt het quotum voor bestands shares (de ingerichte grootte van een bestands share) gebruikt in de berekening van het totale opslag account tot de tijdelijke datum waarop de share verloopt, wanneer deze volledig is verwijderd.

### <a name="availability"></a>Beschikbaarheid

Zacht verwijderen voor Azure-bestands shares is beschikbaar op alle opslag lagen, alle typen opslag accounts en in elke regio die Azure Files beschikbaar is in.

## <a name="configuration-settings"></a>Configuratie-instellingen

Voorlopig verwijderen voor bestands shares is ingeschakeld op het niveau van het opslag account. de instellingen voor voorlopig verwijderen zijn van toepassing op alle bestands shares in een opslag account. Wanneer u een nieuw opslag account maakt, is standaard uitgeschakeld. Voorlopig verwijderen is standaard uitgeschakeld voor bestaande opslag accounts. U kunt op elk gewenst moment zacht verwijderen in-en uitschakelen.

Als u de optie voor het tijdelijk verwijderen van bestands shares inschakelt, verwijdert u enkele bestands shares en schakelt u de optie voorlopig verwijderen uit. als de shares zijn opgeslagen in die periode, kunt u deze bestands shares nog steeds openen en herstellen. Wanneer u voorlopig verwijderen inschakelt, moet u ook de Bewaar periode configureren.

De retentie periode geeft de hoeveelheid tijd aan dat de tijdelijke verwijderde bestands shares worden opgeslagen en beschikbaar zijn voor herstel. Voor bestands shares die expliciet worden verwijderd, wordt de klok van de Bewaar periode gestart wanneer de gegevens worden verwijderd. Momenteel kunt u zachte verwijderde shares gedurende 1 tot 365 dagen blijven gebruiken.

U kunt de tijdelijke Bewaar periode voor verwijderen op elk gewenst moment wijzigen. Een bijgewerkte Bewaar periode is alleen van toepassing op shares die zijn verwijderd nadat de Bewaar periode is bijgewerkt. Shares die zijn verwijderd voordat de retentie periode is bijgewerkt, verloopt op basis van de retentie periode die is geconfigureerd tijdens het verwijderen van de gegevens.

Als u een bestands share permanent wilt verwijderen voordat de verloop tijd is verstreken, moet u het verwijderen van de share ongedaan maken, de optie voor het verwijderen van de software uitschakelen en vervolgens de share opnieuw verwijderen. Vervolgens moet u voorlopig verwijderen opnieuw inschakelen, omdat andere bestands shares in dat opslag account kwetsbaar zijn voor onbedoeld verwijderen, terwijl zacht verwijderen is uitgeschakeld.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Standaard-en Premium-bestands shares worden gefactureerd op de gebruikte capaciteit wanneer deze worden verwijderd, in plaats van de ingerichte capaciteit. Daarnaast worden Premium-bestands shares gefactureerd op basis van de moment opname met de status voorlopig verwijderen. Standaard bestands shares worden gefactureerd op basis van het normale aantal in de status voorlopig verwijderen. Er worden geen kosten in rekening gebracht voor gegevens die permanent worden verwijderd na de geconfigureerde Bewaar periode.

Zie de [pagina met prijzen voor azure File Storage](https://azure.microsoft.com/pricing/details/storage/files/)voor meer informatie over de prijzen voor Azure-file storage in het algemeen.

Wanneer u voorlopig verwijderen voor het eerst inschakelt, kunt u het beste een kleine Bewaar periode gebruiken om beter te begrijpen hoe de functie van invloed is op uw factuur.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het inschakelen en gebruiken van voorlopig verwijderen gaat u naar [voorlopig verwijderen inschakelen](storage-files-enable-soft-delete.md)