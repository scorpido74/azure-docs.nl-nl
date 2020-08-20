---
title: Uw publicatie optie bepalen-micro soft Commercial Marketplace
description: In dit artikel worden de criteria en vereisten voor het publiceren van aanbiedingen naar Microsoft AppSource en Azure Marketplace beschreven.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 1b0846c68f6860b5c7dac9e93808088dac4f6a05
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607441"
---
# <a name="determine-your-publishing-option"></a>Uw publicatieoptie bepalen

De publicatie optie die u kiest voor uw aanbieding is rechtstreeks gerelateerd aan de vereisten voor de geschiktheid en commerciële Marketplace GTM-voor delen. Belang rijker is dat de selectie van de publicatie optie en het type aanbieding bepaalt hoe gebruikers met uw commerciële Marketplace-aanbieding zullen werken.

Als u uw aanbieding wilt configureren, moet u de volgende belang rijke commerciële Marketplace-concepten kennen: de publicatie opties, aanbiedings typen en configuratie, en aanroepen naar actie die bepalen hoe en waar uw aanbieding wordt weer gegeven in de online winkels voor commerciële Marketplace.

In dit artikel leert u het volgende:

- Bepaal de juiste online winkel voor uw oplossing.
- Welke publicatie opties en aanroepen-to-actie-bewerkingen zijn beschikbaar in elke online winkel.
- Welke aanbiedings typen beschikbaar zijn voor elke publicatie optie.

## <a name="commercial-marketplace-publishing-options"></a>Publicatie opties voor commerciële Marketplace

In de volgende tabel ziet u de publicatie opties voor aanbiedings typen in Microsoft AppSource en Azure Marketplace.

|   | **Lijst (contact)**  | **Lijst (proef versie)**  | **Gratis** | **BYOL** | **Transact**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Virtuele machine** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Azure-apps (meerdere VM'S)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Container installatie kopie** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Module IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Beheerde services** |  |  |  | Azure Marketplace |   |
| **Adviesservices** | Beide online winkels |  |  |  |   |
| **SaaS-app** | Beide online winkels | Beide online winkels | Beide online winkels |  | Beide online winkels * |
| **App Microsoft 365** | AppSource | AppSource |  |  | AppSource * *  |
| **Dynamics 365-invoeg toepassing** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; SaaS app Transact-aanbiedingen in Microsoft AppSource zijn momenteel alleen een credit card.

&#42;&#42; Microsoft 365 aanbiedingen zijn gratis te installeren en kunnen worden te gelde via SaaS-aanbieding als licentie service. Zie [geld verdienen Your Office 365-invoeg toepassing via micro soft Commercial Marketplace](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)(Engelstalig) voor meer informatie.

## <a name="choose-a-call-to-action"></a>Kies een actie oproep

De beschik bare publicatie opties bieden een gedifferentieerde klant betrokkenheid, waarbij u toegang krijgt tot het delen van leads en [commerciële Marketplace-voor delen](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Let op de aanroepen-naar-actie die overeenkomt met de publicatie optie:

| **Publicatie optie**    | **Beschrijving**  |
| :------------------- | :-------------------|
| **List** | Eenvoudige vermelding van uw toepassing of service waarmee een commerciële Marketplace-gebruiker u kan vragen om verbinding te maken met de klant via de **contact persoon die mij** aanroept. |
| **Proefversie** | Gebruik de commerciële Marketplace voor het verbeteren van de detectie en het automatiseren van het inrichten van de proef ervaring van uw oplossing, waardoor potentiële gebruikers uw SaaS, IaaS of micro soft in-app-ervaring gratis kunnen gebruiken voor een beperkte periode voordat ze worden gekocht. De aanroep-to-action die wordt gebruikt voor de optie voor het publiceren van de proef versie, is een **gratis proef versie** of een **test station**. |
| **BYOL** | Gebruik de commerciële Marketplace om de detectie en het automatiseren van de inrichting van uw oplossing te verbeteren en de financiële trans actie afzonderlijk te volt ooien. BYOL-aanbiedings typen zijn ideaal voor on-premises naar Cloud migraties. De aanroep-to-Action wordt **nu ontvangen**.
| **Transact** | Aanbiedingen voor de Transact worden verkocht via de commerciële Marketplace. Micro soft is verantwoordelijk voor facturering en verzamelingen. De aanroep-to-Action wordt **nu ontvangen**.|

> [!Note]
> Wanneer u de optie voor het publiceren van Transact gebruikt, is het belang rijk om inzicht te krijgen in de overwegingen met betrekking tot prijzen, facturering, facturering en betaling voordat u een aanbiedings type selecteert en uw aanbieding maakt. Raadpleeg het artikel [Commercial Marketplace Transact-mogelijkheden](./marketplace-commercial-transaction-capabilities-and-considerations.md)voor meer informatie.

## <a name="selecting-an-online-store"></a>Een online winkel selecteren

Elke online winkel fungeert als unieke klant vereisten en streeft naar specifieke doel groepen. Uw aanbiedings type, de Transact-mogelijkheden en de categorie bepalen waar uw aanbieding wordt gepubliceerd. Categorieën en subcategorieën worden toegewezen aan elke online winkel op basis van de doel groep:

**Microsoft AppSource** streeft naar zakelijke gebruikers die op zoek zijn naar line-of-Business-of Industry-oplossingen en advies Services voor Dynamics 365, Microsoft 365 en Power platform.

**Azure Marketplace** streeft ernaar IT-professionals en ontwikkel aars die op zoek zijn naar oplossingen die zijn gebouwd voor of op Azure, evenals advies services die hun gebruik van Azure versnellen.

Selecteer de categorie en subcategorie die het beste aansluiten bij uw doel groep. Een Web Application Firewall moet bijvoorbeeld worden gepubliceerd op Azure Marketplace, onder de categorie beveiliging, omdat IT-professionals het beoogde publiek zijn. Een contract beheer-app moet in plaats daarvan worden gepubliceerd naar AppSource onder de categorie verkoop, aangezien de beoogde doel groep zakelijke gebruikers is. Als u de onjuiste categorie of subcategorie selecteert, wordt uw aanbieding naar de verkeerde online winkel gepubliceerd.

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>Publiceren naar beide online winkels (alleen voor SaaS-aanbiedingen)

SaaS-aanbiedingen kunnen worden gepubliceerd in azure Marketplace of AppSource. Als uw SaaS-aanbieding is bedoeld *voor een* technisch publiek (Azure Marketplace) en een bedrijfs doel (AppSource), selecteert u een categorie en/of een subcategorie die van toepassing is op elke online winkel. Aanbiedingen die naar beide online winkels worden gepubliceerd, moeten een toegevoegde waarde hebben die uitbreidt naar IT-professionals *en* zakelijke gebruikers.

> [!IMPORTANT]
> SaaS-aanbiedingen met facturering via data limiet zijn beschikbaar via Azure Marketplace en de Azure Portal. SaaS-aanbiedingen met alleen persoonlijke abonnementen zijn beschikbaar via de Azure Portal.

| Factuur met data limiet | Openbaar abonnement | Persoonlijk abonnement | Beschikbaar in: |
|---|---|---|---|
| Ja             | Ja         | Nee           | Azure Marketplace en Azure Portal |
| Ja             | Ja         | Ja          | Azure Marketplace en Azure Portal * |
| Ja             | Nee          | Ja          | Alleen Azure Portal |
| Nee              | Nee          | Ja          | Alleen Azure Portal |

&#42; het privé-abonnement van de aanbieding is alleen beschikbaar via de Azure Portal

Zo wordt een aanbieding met een facturerings regeling en een privé-abonnement alleen (geen openbaar abonnement) gekocht door klanten in de Azure Portal. Meer informatie over [privé aanbiedingen in micro soft Commercial Marketplace](private-offers.md).

### <a name="categories"></a>Categorieën

Categorieën en subcategorieën worden toegewezen aan elke online winkel op basis van de doel groep. Selecteer de categorieën en subcategorieën die het beste aansluiten bij uw aanbieding en de beoogde doel groep. U kunt het volgende selecteren:

- Ten minste één en Maxi maal twee categorieën. U hebt de optie om een primaire en een secundaire categorie te kiezen.
- Maxi maal twee subcategorieën voor elke primaire en/of secundaire categorie. Als u geen subcategorie selecteert, kan uw aanbieding nog steeds worden gedetecteerd onder de geselecteerde categorie.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Volgende stappen

- Zodra u een publicatie optie hebt geselecteerd, kunt u [het aanbiedings type selecteren](./publisher-guide-by-offer-type.md) dat wordt gebruikt om uw aanbieding te presen teren.
- Bekijk de geschiktheids vereisten in de sectie publicatie opties per aanbiedings type om de selectie en configuratie van uw aanbieding te volt ooien.
- Bekijk de publicatie patronen in de online winkel voor beelden van hoe uw oplossing is gekoppeld aan een type en configuratie van een aanbieding.
