---
title: Internetpeering - veelgestelde vragen
titleSuffix: Azure
description: Internetpeering - veelgestelde vragen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775496"
---
# <a name="internet-peering---faqs"></a>Internetpeering - veelgestelde vragen

U onderstaande informatie bekijken voor algemene vragen.

**Wat is het verschil tussen internetpeering en Peering Service?**

Peering Service is een service die microsoft zakelijke IP-connectiviteit wil bieden voor haar zakelijke klanten. Enterprise grade Internet omvat connectiviteit via ISP's met een hoge doorvoerverbinding met Microsoft en redundantie voor een HA-connectiviteit. Bovendien is gebruikersverkeer geoptimaliseerd voor latentie naar de dichtstbijzijnde Microsoft Edge. Peering Service bouwt voort op peering-connectiviteit met partnerprovider. De peering-connectiviteit met de partner moet Direct peering zijn in tegenstelling tot Exchange-peering. Direct peering moet lokale en geo-redundantie hebben.

**Wat is legacy peering?**

Peering-verbinding die is ingesteld met Azure PowerShell, wordt beheerd als een Azure-bron. Peering-verbindingen die in het verleden zijn ingesteld, worden in ons systeem opgeslagen als legacy-peering die u kiezen om te converteren naar beheer als Azure-bron.

**Wanneer New-AzPeeringDirectConnectionObject wordt aangeroepen, welke IP-adressen worden gegeven aan Microsoft- en Peer-apparaten?**

Bij het aanroepen van de cmdlet Nieuw-AzPeeringDirectConnectionObject wordt een /31-adres (a.b.c.d/31) of een /30-adres (a.b.c.d/30) ingevoerd. Het eerste IP-adres (a.b.c.d+0) wordt gegeven aan het apparaat van Peer en het tweede IP-adres (a.b.c.d+1) wordt aan microsoft-apparaat gegeven.

**Wat is MaxPrefixesAdvertisedIPv4 en MaxPrefixesAdvertisedIPv6 parameters in New-AzPeeringDirectConnectionObject cmdlet?**

MaxPrefixesAdvertisedIPv4 en MaxPrefixesAdvertisedIPv6 parameters vertegenwoordigen het maximum aantal IPv4 en IPv6 voorvoegsels een Peer wil Dat Microsoft accepteert. Deze parameters kunnen op elk gewenst moment worden gewijzigd.