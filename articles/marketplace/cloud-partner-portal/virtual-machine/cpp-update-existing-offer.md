---
title: Een bestaande VM-aanbieding bijwerken in azure Marketplace
description: Hierin wordt uitgelegd hoe u een bestaande VM-aanbieding op Azure Marketplace bijwerkt.
services: Azure, Marketplace, Cloud Partner Portal,
author: MaggiePucciEvans
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/27/2018
ms.author: evansma
ms.openlocfilehash: 1ba2abb3fbeb1d08ed780669fb94a2ef83cbfb1b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934251"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Een bestaande VM-aanbieding op Azure Marketplace bijwerken

Dit artikel begeleidt u door de verschillende aspecten van het bijwerken van de aanbieding van uw virtuele machine (VM) in de [Cloud Partner-Portal](https://cloudpartner.azure.com/) en vervolgens de aanbieding opnieuw te publiceren. 

Er zijn een aantal gebruikelijke-redenen waarom u uw aanbieding kunt bijwerken, met inbegrip van:

-  Een nieuwe VM-installatie kopie versie toevoegen aan bestaande Sku's
-  Regio's wijzigen waarvoor een SKU beschikbaar is
-  Nieuwe Sku's toevoegen
-  De meta gegevens van de Marketplace voor de aanbieding of afzonderlijke Sku's bijwerken
-  Prijs bijwerken voor aanbiedingen met betalen naar gebruik

Om u te helpen bij deze wijzigingen, biedt de Portal de functies **compare** en **history** .  

>[!Note]
>Opt-in voor Cloud Solution Providers (CSP)-partner kanaal is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](../../cloud-solution-providers.md) voor meer informatie over het marketing gebruik van uw aanbieding via de micro soft CSP-partner kanalen.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Niet-toegestane wijzigingen in de VM-aanbieding of SKU

Er zijn enkele kenmerken van een VM-aanbieding of-SKU die niet meer kan worden gewijzigd nadat de aanbieding in azure Marketplace is opgenomen, voornamelijk:

-  **Aanbiedings-id** en **uitgevers-id** van de aanbieding
-  **SKU-id** van bestaande sku's
-  Aantal gegevens schijven van bestaande Sku's
-  Wijzigingen in het facturerings-en licentie model voor bestaande Sku's
-  Prijs verhogingen naar een gepubliceerde SKU


## <a name="common-update-operations"></a>Algemene update bewerkingen

Hoewel er een breed scala aan kenmerken is die u op een VM-aanbieding kunt wijzigen, zijn de volgende bewerkingen gebruikelijk.

### <a name="update-the-vm-image-version-for-a-sku"></a>De versie van de VM-installatie kopie bijwerken voor een SKU

Het is gebruikelijk dat een VM-installatie kopie regel matig wordt bijgewerkt met beveiligings patches, extra functies, enzovoort.  Onder dergelijke scenario's wilt u de VM-installatie kopie die uw SKU verwijst, bijwerken met behulp van de volgende stappen:

1.  Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).

2.  Zoek onder **alle aanbiedingen**de aanbieding die u wilt bijwerken.

3.  Klik op het tabblad **sku's** op de SKU die is gekoppeld aan de VM-installatie kopie die moet worden bijgewerkt.

4.  Klik onder **schijf versie**op **+ nieuwe schijf versie** om een nieuwe VM-installatie kopie toe te voegen.

5.  Geef de **schijf versie**van de nieuwe VM-installatie kopieën op. De schijf versie moet de indeling van de [semantische versie](https://semver.org/) volgen. Versies moeten de indeling X. Y. Z hebben, waarbij X, Y en Z gehele getallen zijn. Controleer of de nieuwe versie die u opgeeft, groter is dan alle vorige versies. Als u de nieuwe versie opnieuw publiceert, worden deze niet weer gegeven in de portal of in de Azure Marketplace.

6.  Voor de **VHD-URL**van het besturings systeem voert u de [SAS-URI (Shared Access Signature)](./cpp-get-sas-uri.md) in die is gemaakt voor de virtuele harde schijf. 

    > [!WARNING] 
    > Het aantal gegevens schijven kan niet worden gewijzigd tussen verschillende versies van de SKU. Als voor eerdere versies gegevens schijven waren geconfigureerd, moet deze nieuwe versie ook hetzelfde aantal gegevens schijven hebben.

7.  Klik op **publiceren** om de werk stroom te starten om uw nieuwe VM-versie te publiceren op de Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Beschik baarheid van regio's van een SKU wijzigen

In de loop van de tijd wilt u uw aanbieding/SKU mogelijk beschikbaar maken in meer regio's.  U kunt er ook voor zorgen dat u de ondersteuning van de aanbieding/SKU in een bepaalde regio wilt beëindigen.
Gebruik de volgende stappen om de beschik baarheid te wijzigen:

1.  Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).

2.  Zoek onder **alle aanbiedingen** de aanbieding die u wilt bijwerken.

3.  Klik op het tabblad **sku's** op de SKU waarvan u de beschik baarheid wilt wijzigen.

4.  Klik op de knop **landen selecteren** onder het veld **Beschik baarheid land/regio** .

5.  Voeg in de pop-up Beschik baarheid regio de regio's voor deze SKU toe of verwijder deze.

6.  Klik op **publiceren** om de werk stroom publiceren te starten om de beschik baarheid van uw sku's-regio bij te werken.

Als een SKU beschikbaar wordt gemaakt in een nieuwe regio, hebt u de mogelijkheid om prijzen voor deze regio op te geven via de functionaliteit voor het **exporteren van prijs gegevens** . Als u een regio opnieuw toevoegt die eenmaal beschikbaar was, kunt u de prijzen niet bijwerken omdat de prijs wijzigingen niet zijn toegestaan.


### <a name="add-a-new-sku"></a>Een nieuwe SKU toevoegen

Gebruik de volgende stappen om een nieuwe SKU beschikbaar te maken voor uw bestaande aanbieding: 

1.  Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).

2.  Zoek onder **alle aanbiedingen** de aanbieding die u wilt bijwerken.

3.  Klik op het tabblad **sku's** op **nieuwe SKU toevoegen** en geef in het pop-upvenster een **SKU-id** op.

4.  Publiceer de VM opnieuw zoals beschreven in het artikel [een virtuele machine naar Azure Marketplace publiceren](./cpp-publish-offer.md).

5.  Klik op **publiceren** om de werk stroom te starten om uw nieuwe SKU te publiceren.


### <a name="update-offer-marketplace-metadata"></a>Update aanbieding Marketplace-meta gegevens

Voer de volgende stappen uit om de meta gegevens van de Marketplace bij te werken: Bedrijfs naam, logo's, enzovoort, die zijn gekoppeld aan uw aanbieding. 

1.  Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).

2.  Zoek onder **alle aanbiedingen** de aanbieding die u wilt bijwerken.

3.  Ga naar het tabblad **Marketplace** en volg de instructies in het artikel [een virtuele machine naar Azure Marketplace publiceren](./cpp-publish-offer.md) om meta gegevens wijzigingen aan te brengen.

4.  Klik op **publiceren** om de werk stroom te starten om uw wijzigingen te publiceren.


### <a name="update-pricing-on-published-offers"></a>Prijzen voor gepubliceerde aanbiedingen bijwerken

Zodra uw aanbieding voor betalen per gebruik is gepubliceerd, kunt u de prijzen voor de SKU niet direct verhogen.  (U kunt echter een nieuwe SKU maken onder dezelfde aanbieding, de oude SKU verwijderen en vervolgens uw aanbieding opnieuw publiceren voor nieuwe klanten.)  U kunt de prijs van een gepubliceerde aanbieding daarentegen verlagen door de volgende stappen uit te voeren:

1.  Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).

2.  Zoek onder **alle aanbiedingen**de aanbieding die u wilt bijwerken.

3.  Klik op de SKU waarvoor u de prijzen wilt verlagen.

4.  Als u de prijzen hebt ingesteld in de 1x1-gebruikers interface, kunt u de prijs rechtstreeks wijzigen in de gebruikers interface. Als u prijzen hebt ingesteld via het werk blad importeren/exporteren, kunt u de prijzen alleen verlagen via de functie importeren/exporteren.

3.  Klik op **Opslaan**.

4.  Klik op **publiceren** om de werk stroom te starten om uw wijzigingen te publiceren.

De nieuwe klanten die de prijs afnemen, worden weer gegeven als ze live op de website zijn.  Deze nieuwe prijs is van invloed op uw klanten op de volgende manieren:

- Nieuwe klanten worden dit nieuwe tarief in rekening gebracht. 
- Voor bestaande klanten wordt de prijs verlaging terugwerkt naar het begin van de facturerings cyclus, waardoor de prijs verlaging effectief werd.
Als ze al zijn gefactureerd voor de cyclus gedurende welke een prijs verlaging zich voordeed, ontvangt hij een terugbetaling tijdens de volgende facturerings cyclus om de verlaagde prijs te dekken.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Prijzen voor vereenvoudigd valuta

Vanaf september 1 2018 wordt een nieuwe sectie met de naam **vereenvoudigde valuta prijs** toegevoegd aan de portal. Micro soft stroomt de Azure Marketplace-onderneming door meer voorspel bare prijzen en verzamelingen van uw klanten in de hele wereld in te scha kelen. Deze stroom lijn omvat het verminderen van het aantal valuta's waarin we uw klanten factureren.

In de nieuwe sectie worden de prijzen in deze nieuwe valuta's in rekening gehouden. Zodra alle klanten zijn gemigreerd naar deze nieuwe vereffenings valuta's, wordt de oorspronkelijke prijs sectie buiten gebruik gesteld en wordt alleen de prijs categorie voor vereenvoudigde prijzen bewaard.

U moet tot 1 november 2018 een nieuwe prijs voor de regio's instellen waarbij de vereffenings valuta wordt gewijzigd. Het is niet mogelijk om de prijs voor regio's te verhogen, waarbij de vereffenings valuta niet verandert.

> [!NOTE] 
> Als u Api's gebruikt om uw aanbieding te publiceren, ziet u mogelijk een nieuwe sectie in de JSON van de aanbieding. Dit wordt als `virtualMachinePricingV2` of `monthlyPricingV2`, afhankelijk van het type van de aanbieding. 

Als u vragen hebt over deze wijziging, neemt u contact op met de [ondersteuning voor Azure Marketplace](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Functie vergelijken

Wanneer u wijzigingen aanbrengt in een al gepubliceerde aanbieding, kunt u gebruikmaken van de functie **compare** om de wijzigingen te controleren die zijn aangebracht. Om deze functie te gebruiken, moet u ook het volgende doen:

1.  Op elk moment in het bewerkings proces, klikt u op de knop **vergelijken** voor uw aanbieding.

    ![Functie knop vergelijken](./media/publishvm_037.png)


2.  Side-by-side versies van marketing assets en meta gegevens weer geven.


## <a name="history-of-publishing-actions"></a>Geschiedenis van publicatie acties

Als u een historische publicatie activiteit wilt weer geven, klikt u op het **geschiedenis** item in de linker navigatie balk van Cloud Partner-Portal. Hier kunt u de tijds tempels weer geven die zijn gemaakt tijdens de levens duur van uw aanbiedingen in azure Marketplace.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

