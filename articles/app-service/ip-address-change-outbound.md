---
title: Voorbereiden op wijziging van het uitgaande IP-adres
description: Als uw uitgaande IP-adres wordt gewijzigd, leest u wat u moet doen zodat uw app na de wijziging blijft werken.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671668"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Voorbereiden op een uitgaande IP-adreswijziging

Als u een melding hebt ontvangen dat de uitgaande IP-adressen van uw Azure App Service-app worden gewijzigd, volgt u de instructies in dit artikel.

## <a name="determine-if-you-have-to-do-anything"></a>Bepaal of je iets moet doen

* Optie 1: Als uw App Service-app geen IP-filtering, een expliciete opnamelijst of speciale afhandeling van uitgaand verkeer zoals routering of firewall gebruikt, is er geen actie vereist.

* Optie 2: Als uw app wel een speciale afhandeling heeft voor de uitgaande IP-adressen (zie voorbeelden hieronder), voegt u de nieuwe uitgaande IP-adressen toe, waar de bestaande adressen ook worden weergegeven. Vervang de bestaande IP-adressen niet. U de nieuwe uitgaande IP-adressen vinden door de instructies in de volgende sectie te volgen.

  Een uitgaand IP-adres kan bijvoorbeeld expliciet worden opgenomen in een firewall buiten uw app of een externe betalingsservice heeft mogelijk een toegestane lijst met het uitgaande IP-adres voor uw app. Als uw uitgaande adres is geconfigureerd in een lijst buiten uw app, moet dat worden gewijzigd.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>De uitgaande IP-adressen zoeken in de Azure-portal

De nieuwe uitgaande IP-adressen worden weergegeven in de portal voordat ze van kracht worden. Wanneer Azure de nieuwe begint te gebruiken, worden de oude niet meer gebruikt. Er wordt slechts één set tegelijk gebruikt, dus vermeldingen in inclusielijsten moeten zowel oude als nieuwe IP-adressen hebben om een storing te voorkomen wanneer de switch plaatsvindt. 

1.  Open de [Azure Portal](https://portal.azure.com).

2.  Selecteer **App Services**in het navigatiemenu aan de linkerkant .

3.  Selecteer uw App Service-app in de lijst.

1.  Zie [Uitgaande IP-adressen van de functie-app](../azure-functions/ip-addresses.md#find-outbound-ip-addresses)als de app een functie-app is.

4.  Klik **onder** de kop Instellingen op **Eigenschappen** in de linkernavigatie en zoek de sectie met het label **Uitgaande IP-adressen**.

5. Kopieer de IP-adressen en voeg ze toe aan uw speciale afhandeling van uitgaand verkeer, zoals een filter of een toegestane lijst. Verwijder de bestaande IP-adressen in de lijst niet.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u zich voorbereiden op een IP-adreswijziging die is geïnitieerd door Azure. Zie [Binnenkomende en uitgaande IP-adressen in Azure App Service](overview-inbound-outbound-ips.md)voor meer informatie over IP-adressen in Azure App Service.
