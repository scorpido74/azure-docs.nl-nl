---
title: Virtuele machine-aanbieding maken in Azure Marketplace
description: Bevat de stappen die nodig zijn om een nieuwe vm-aanbieding (virtual machine) te maken voor de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: bc1cf7a839307e65bd91eb29531663141e521472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278038"
---
# <a name="create-virtual-machine-offer"></a>Virtuele machine-aanbieding maken

In deze sectie worden de stappen weergegeven die nodig zijn om een nieuwe vm-aanbiedingsaanvraag voor virtuele machines (voor de Azure Marketplace) te maken.  Elke aanbieding wordt weergegeven als een eigen entiteit in Azure Marketplace en is gekoppeld aan een of meer SKU's.  Een VM-aanbieding bestaat uit de volgende groeperingen van activa en ondersteunende diensten: 

![Activa voor een VM-aanbieding](./media/publishvm_002.png)

Hierbij

|  **Activagroep**   |  **Beschrijving**  |
|  ---------------   |  ---------------  |
|    SKU's            |  De kleinste purchasable eenheid van een aanbieding. Aan één aanbieding (productklasse) kunnen meerdere SKU's zijn gekoppeld om onderscheid te maken tussen ondersteunde functies, VM-afbeeldingstypen en factureringsmodellen. |
|  Marketplace       | Bevat marketing-, juridische en leadmanagementactiva en -specificaties.  <ul><li> Marketingmiddelen omvatten aanbiedingsnaam, beschrijving en logo's</li> <li> Juridische middelen omvatten een privacybeleid, gebruiksvoorwaarden en andere juridische documentatie</li>  <li> Met het beheerbeleid voor leads u opgeven hoe u leads verwerken vanuit de Azure Marketplace-portal voor eindgebruikers.</li> </ul> |
| Ondersteuning            | Bevat ondersteuningscontact- en beleidsinformatie |
| Test Drive         | Definieert assets waarmee eindgebruikers uw aanbod kunnen testen voordat ze het kopen |
|  |  |


## <a name="new-offer-form"></a>Nieuw aanbiedingsformulier

Zodra u zich aanmeldt bij de [Cloud Partner Portal,](https://cloudpartner.azure.com/)klikt u op het item **+ Nieuw voorstel** op de linkermenubalk. Klik in het resulterende menu op **Virtuele machines** om het formulier **Nieuwe aanbieding** weer te geven en het proces van het definiëren van assets voor een nieuwe VM-aanbieding te starten. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Nieuwe virtuele machine bieden gebruikersinterface selectie](./media/publishvm_003.png)

> [!WARNING]
> Als de optie **Virtuele machines** niet wordt weergegeven of niet is ingeschakeld, heeft uw account geen toestemming om dit aanbiedingstype te maken.  Controleer of je aan alle voorwaarden voor dit [aanbiedingstype](./cpp-prerequisites.md) hebt voldaan, inclusief het registreren voor een ontwikkelaarsaccount.


## <a name="next-steps"></a>Volgende stappen

De volgende onderwerpen in deze sectie weerspiegelen de tabbladen op de pagina **Nieuwe aanbieding** (voor een vm-aanbiedingstype).  In elk artikel wordt uitgelegd hoe u het bijbehorende tabblad gebruiken om de activagroepen en ondersteunende services voor uw nieuwe vm-aanbieding te definiëren.

- [Tabblad voor aanbiedingsinstellingen](./cpp-offer-settings-tab.md)
- [Tabblad voor SKU's](./cpp-skus-tab.md)
- [Tabblad voor Test Drive](./cpp-test-drive-tab.md)
- [Tabblad voor Marketplace](./cpp-marketplace-tab.md)
- [Tabblad voor ondersteuning](./cpp-support-tab.md)
