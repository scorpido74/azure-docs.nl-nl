---
title: Functies voor klantgegevensaanvragen in Azure IoT Central | Microsoft Documenten
description: In dit artikel worden klantgegevens in Azure IoT Central-toepassing geïdentificeerd, verwijderd en geëxporteerd.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3cf88b4d3c4c74493235d2997db4d464bb055b81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023749"
---
# <a name="summary-of-customer-data-request-features"></a>Overzicht van de functies voor het aanvragen van klantgegevens

Azure IoT Central is een volledig beheerde IoT-software-as-a-service-oplossing (Internet of Things) die het eenvoudig maakt om uw IoT-assets op schaal te verbinden, te monitoren en te beheren, diepgaande inzichten te creëren uit uw IoT-gegevens en geïnformeerde actie te ondernemen.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Klantgegevens identificeren

Azure Active Directory Object-ID's worden gebruikt om gebruikers te identificeren en rollen toe te wijzen. De Azure IoT Central-portal geeft e-mailadressen van gebruikers weer voor roltoewijzingen, maar alleen de Azure Active Directory-object-id wordt opgeslagen, het e-mailadres wordt dynamisch opgevraagd vanuit Azure Active Directory. Azure IoT Central-beheerders kunnen gebruikers van toepassingen weergeven, exporteren en verwijderen in de sectie gebruikersbeheer van een Azure IoT Central-toepassing.

Binnen de toepassing kunnen e-mailadressen worden geconfigureerd om waarschuwingen te ontvangen. In dit geval worden e-mailadressen opgeslagen in IoT Central en moeten ze worden beheerd vanaf de in-app accountbeheerpagina.

Wat apparaten betreft, heeft Microsoft geen informatie en heeft het geen toegang tot gegevens die de correlatie van het apparaat mogelijk maken. Veel van de apparaten die worden beheerd in Azure IoT Central zijn geen persoonlijke apparaten, bijvoorbeeld een automaat of koffiezetapparaat. Klanten kunnen sommige apparaten echter persoonlijk identificeerbaar beschouwen en naar eigen inzicht hun eigen activa- of voorraadvolgsystemen onderhouden die apparaten aan individuen koppelen. Azure IoT Central beheert en bewaart alle gegevens die aan apparaten zijn gekoppeld alsof het persoonlijke gegevens zijn.

Wanneer u Microsoft enterprise services gebruikt, genereert Microsoft bepaalde informatie, bekend als systeemgegenereerde logboeken. Deze logboeken vormen feitelijke acties die worden uitgevoerd binnen de service en diagnostische gegevens met betrekking tot individuele apparaten, en zijn niet gerelateerd aan gebruikersactiviteit. Azure IoT Central-systeemlogboeken zijn niet toegankelijk of exporteerbaar door toepassingsbeheerders.

## <a name="deleting-customer-data"></a>Klantgegevens verwijderen

De mogelijkheid om gebruikersgegevens te verwijderen wordt alleen verstrekt via de pagina IoT Central-beheer. Toepassingsbeheerders kunnen de gebruiker selecteren die moet worden verwijderd en **verwijderen** in de rechterbovenhoek van de toepassing selecteren om de record te verwijderen. Toepassingsbeheerders kunnen ook afzonderlijke accounts verwijderen die niet langer aan de betreffende toepassing zijn gekoppeld.

Nadat een gebruiker is verwijderd, worden er geen verdere waarschuwingen naar hen gemaild. Hun e-mailadres moet echter afzonderlijk worden verwijderd uit elke geconfigureerde waarschuwing.

## <a name="exporting-customer-data"></a>Klantgegevens exporteren

De mogelijkheid om gegevens te exporteren wordt alleen geleverd via de pagina IoT Central-beheer. Klantgegevens, waaronder toegewezen rollen, kunnen worden geselecteerd, gekopieerd en geplakt door een toepassingsbeheerder.

## <a name="links-to-additional-documentation"></a>Koppelingen naar aanvullende documentatie

Zie [Hoe u uw toepassing beheert](howto-administer.md)voor meer informatie over accountbeheer, inclusief roldefinities.
