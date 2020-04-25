---
title: Aanbieding voor virtuele machines maken in azure Marketplace
description: Een lijst met de stappen die nodig zijn voor het maken van een nieuwe virtuele machine (VM)-aanbieding voor Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: a25f6877f1fb4940fb1de127b81d83975c8e835c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142711"
---
# <a name="create-virtual-machine-offer"></a>De aanbieding voor de virtuele machine maken

> [!IMPORTANT]
> Vanaf 13 april 2020 gaan we het beheer van uw Azure Virtual Machine-aanbiedingen naar het partner centrum verplaatsen. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in [een Azure virtual machine-aanbieding maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) om uw gemigreerde aanbiedingen te beheren.

In deze sectie vindt u de stappen die nodig zijn voor het maken van een nieuwe aanvraag voor een virtuele machine (VM) voor Azure Marketplace.  Elk aanbod wordt als eigen entiteit weer gegeven in azure Marketplace en is gekoppeld aan een of meer Sku's.  Een VM-aanbieding bestaat uit de volgende groeperingen van assets en ondersteunende services: 

![Activa voor een VM-aanbieding](./media/publishvm_002.png)

Hierbij

|  **Activa groep**   |  **Beschrijving**  |
|  ---------------   |  ---------------  |
|    SKU's            |  De kleinste tevens-eenheid van een aanbieding. Aan één aanbieding (product klasse) kunnen meerdere Sku's worden gekoppeld om onderscheid te maken tussen ondersteunde functies, VM-installatie kopieën en facturerings modellen. |
|  Marketplace       | Bevat marketing-, juridische-en Lead beheer activa en-specificaties.  <ul><li> Marketing assets bevatten naam, beschrijving en logo's van de aanbieding</li> <li> Juridische activa omvatten een privacybeleid, gebruiks voorwaarden en andere juridische documentatie</li>  <li> Met het beleid voor het beheer van leads kunt u opgeven hoe leads van de eind gebruikers portal van Azure Marketplace moeten worden afgehandeld.</li> </ul> |
| Ondersteuning            | Bevat ondersteunings contactpersoon en beleids gegevens |
| Test Drive         | Hiermee worden assets gedefinieerd waarmee eind gebruikers uw aanbieding kunnen testen voordat ze deze kopen |
|  |  |


## <a name="new-offer-form"></a>Formulier voor nieuwe aanbieding

Zodra u zich aanmeldt bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/), klikt u op de menu balk links op **+ Nieuw** item. Klik in het menu dat wordt weer gegeven op **virtual machines** om het formulier voor de **nieuwe aanbieding** te openen en het proces van het definiëren van assets voor een nieuwe VM-aanbieding te starten. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Selectie van de gebruikers interface voor de aanbieding van nieuwe virtuele machines](./media/publishvm_003.png)

> [!WARNING]
> Als de optie **virtual machines** niet wordt weer gegeven of niet is ingeschakeld, is uw account niet gemachtigd om dit aanbiedings type te maken.  Controleer of u aan alle [vereisten](./cpp-prerequisites.md) voor dit aanbiedings type hebt voldaan, inclusief registratie voor een ontwikkelaars account.


## <a name="next-steps"></a>Volgende stappen

In de volgende onderwerpen in deze sectie wordt de tabbladen in de **nieuwe aanbiedings** pagina (voor een type VM-aanbieding) gespiegeld.  In elk artikel wordt uitgelegd hoe u het bijbehorende tabblad gebruikt voor het definiëren van de activa groepen en ondersteunende services voor uw nieuwe VM-aanbieding.

- [Tabblad voor aanbiedingsinstellingen](./cpp-offer-settings-tab.md)
- [Tabblad voor SKU's](./cpp-skus-tab.md)
- [Tabblad voor Test Drive](./cpp-test-drive-tab.md)
- [Tabblad voor Marketplace](./cpp-marketplace-tab.md)
- [Tabblad voor ondersteuning](./cpp-support-tab.md)
