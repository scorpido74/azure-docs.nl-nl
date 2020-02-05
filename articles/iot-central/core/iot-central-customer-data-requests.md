---
title: Functies voor klant gegevens aanvragen in azure IoT Central | Microsoft Docs
description: In dit artikel wordt beschreven hoe u klant gegevens kunt identificeren, verwijderen en exporteren in azure IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3cf88b4d3c4c74493235d2997db4d464bb055b81
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023749"
---
# <a name="summary-of-customer-data-request-features"></a>Overzicht van de klant functies voor aanvraag

Azure IoT Central is een software-as-a-service-oplossing met volledige beheerde Internet of Things (IoT) waarmee u eenvoudig uw IoT-assets op schaal kunt aansluiten, bewaken en beheren, een grondige inzichten kunt creëren op basis van uw IoT-gegevens en een gefundeerde actie kunt ondernemen.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Klant gegevens identificeren

Azure Active Directory-object-Id's worden gebruikt om gebruikers te identificeren en rollen toe te wijzen. De Azure IoT Central-portal geeft gebruikers-e-mail adressen weer voor roltoewijzingen, maar alleen de Azure Active Directory object-ID wordt opgeslagen, het e-mail adres wordt dynamisch opgehaald uit Azure Active Directory. Azure IoT Central-beheerders kunnen toepassings gebruikers weer geven, exporteren en verwijderen in het gedeelte gebruikers beheer van een Azure IoT Central-toepassing.

Binnen de toepassing kunnen e-mail adressen worden geconfigureerd voor het ontvangen van waarschuwingen. In dit geval worden e-mail adressen opgeslagen in IoT Central en moeten ze worden beheerd via de beheer pagina voor de account van de app.

Met betrekking tot apparaten onderhoudt micro soft geen informatie en heeft deze geen toegang tot gegevens waardoor het apparaat aan de gebruikers correlatie kan worden door gegeven. Veel van de apparaten die worden beheerd in azure IoT Central zijn geen persoonlijke apparaten, bijvoorbeeld een automaten machine of een koffie maker. Klanten kunnen er echter voor zorgen dat sommige apparaten persoonlijk worden geïdentificeerd en kunnen hun eigen Asset-of inventaris tracking-systemen onderhouden die apparaten aan individuen koppelen. Met Azure IoT Central worden alle gegevens die zijn gekoppeld aan apparaten, beheerd en opgeslagen alsof deze persoons gegevens zijn.

Wanneer u micro soft Enter prise Services gebruikt, genereert micro soft bepaalde informatie, ook wel door het systeem gegenereerde logboeken. Deze logboeken vormen feitelijke acties die worden uitgevoerd in de service en diagnostische gegevens met betrekking tot afzonderlijke apparaten, en zijn niet gerelateerd aan de gebruikers activiteit. Door Azure IoT Central door het systeem gegenereerde logboeken zijn niet toegankelijk of exporteerbaar door toepassings beheerders.

## <a name="deleting-customer-data"></a>Verwijderen van klantgegevens

De mogelijkheid om gebruikers gegevens te verwijderen wordt alleen geboden via de pagina IoT Central beheer. Toepassings beheerders kunnen de te verwijderen gebruiker selecteren en **verwijderen** selecteren in de rechter bovenhoek van de toepassing om de record te verwijderen. Toepassings beheerders kunnen ook afzonderlijke accounts verwijderen die niet meer aan de betreffende toepassing zijn gekoppeld.

Nadat een gebruiker is verwijderd, worden er geen waarschuwingen meer naar hen verzonden. Het e-mail adres moet echter afzonderlijk worden verwijderd uit elke geconfigureerde waarschuwing.

## <a name="exporting-customer-data"></a>Exporteren van gegevens van de klant

De mogelijkheid om gegevens te exporteren, wordt alleen geboden via de pagina IoT Central beheer. Klant gegevens, met inbegrip van toegewezen rollen, kunnen worden geselecteerd, gekopieerd en geplakt door een toepassings beheerder.

## <a name="links-to-additional-documentation"></a>Koppelingen naar aanvullende documentatie

Zie [uw toepassing beheren](howto-administer.md)voor meer informatie over account beheer, waaronder roldefinities.
