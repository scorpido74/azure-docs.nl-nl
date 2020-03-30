---
title: Een bestaande VM-aanbieding bijwerken in de Azure Marketplace
description: Hier wordt uitgelegd hoe u een bestaande VM-aanbieding op Azure Marketplace bijwerkt.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: fe1397d7ad00a2eb6e9fe04d8bfa50e49839fe34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288781"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Een bestaande VM-aanbieding bijwerken op Azure Marketplace

In dit artikel vindt u de verschillende aspecten van het bijwerken van uw virtuele machine (VM) in de [Cloud Partner Portal](https://cloudpartner.azure.com/) en publiceert u het aanbod opnieuw. 

Er zijn een aantal redenen waarom u uw aanbieding bijwerken, waaronder:

-  Een nieuwe VM-afbeeldingsversie toevoegen aan bestaande SKU's
-  Regio's wijzigen Een SKU is beschikbaar
-  Nieuwe SKU's toevoegen
-  De metagegevens van de marktplaats voor de aanbieding of individuele SKU's bijwerken
-  Beprijzing voor betalen per gebruik-aanbiedingen bijwerken

Om u te helpen bij deze wijzigingen, biedt de portal de functies **Vergelijken** en **Geschiedenis.**  

>[!Note]
>Cloud Solution Providers (CSP) partnerkanaal opt-in is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](../../cloud-solution-providers.md) voor meer informatie over het op de markt brengen van uw aanbieding via de Microsoft CSP-partnerkanalen.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Niet-toegestane wijzigingen in vm-aanbieding of SKU

Er zijn enkele kenmerken van een VM-aanbieding of SKU die niet kunnen worden gewijzigd zodra de aanbieding live is in de Azure Marketplace, voornamelijk:

-  **Id van aanbieding** en **publisher ID** van de aanbieding
-  **SKU-id** van bestaande SKU's
-  Aantal gegevensschijf van bestaande SKU's
-  Wijzigingen in facturering/licentiemodel in bestaande SKU's
-  Prijsverhogingen naar een gepubliceerde SKU


## <a name="common-update-operations"></a>Algemene updatebewerkingen

Hoewel er een breed scala aan kenmerken is die u wijzigen op een VM-aanbieding, zijn de volgende bewerkingen gebruikelijk.

### <a name="update-the-vm-image-version-for-a-sku"></a>De VM-afbeeldingsversie voor een SKU bijwerken

Het is gebruikelijk dat een VM-afbeelding periodiek wordt bijgewerkt met beveiligingspatches, extra functies, enzovoort.  Onder dergelijke scenario's wilt u de VM-afbeelding bijwerken waarnaar uw SKU verwijst met de volgende stappen:

1.  Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Onder **Alle aanbiedingen**vindt u de aanbieding om bij te werken.

3.  Klik op het tabblad **SKU's** op de SKU die is gekoppeld aan de VM-afbeelding om bij te werken.

4.  Klik **onder Schijfversie**op **+Nieuwe schijfversie** om een nieuwe VM-afbeelding toe te voegen.

5.  Geef de nieuwe versie van de VM Images **Disk**op . De schijfversie moet de [semantische versieindeling](https://semver.org/) volgen. Versies moeten van het formulier X.Y.Z zijn, waarbij X, Y en Z gehele getallen zijn. Controleer of de nieuwe versie die u aanbiedt groter is dan alle vorige versies. anders wordt de nieuwe versie na het opnieuw publiceren niet weergegeven in de portal of de Azure Marketplace.

6.  Voer voor **OS VHD URL**de [sas-uri (shared access signature) in](./cpp-get-sas-uri.md) die is gemaakt voor het besturingssysteem VHD. 

    > [!WARNING] 
    > Het aantal gegevensschijven kan niet veranderen tussen verschillende versies van de SKU. Als in eerdere versies gegevensschijven zijn geconfigureerd, moet deze nieuwe versie ook hetzelfde aantal gegevensschijven bevatten.

7.  Klik op **Publiceren** om de werkstroom te starten om uw nieuwe VM-versie te publiceren in de Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>De beschikbaarheid van een SKU in regio's wijzigen

Na verloop van tijd u uw aanbieding/SKU beschikbaar maken in meer regio's.  U ook stoppen met het ondersteunen van de aanbieding/SKU in een bepaalde regio.
Als u de beschikbaarheid wilt wijzigen, gebruikt u de volgende stappen:

1.  Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Onder **Alle aanbiedingen** vind je de aanbieding die je wilt updaten.

3.  Klik op het tabblad **SKU's** op de SKU die u de beschikbaarheid ervan wilt wijzigen.

4.  Klik op de knop **Landen selecteren** onder het veld **Beschikbaarheid land/regio.**

5.  Voeg in de pop-up regiobeschikbaarheid de regio's voor deze SKU toe of verwijder deze.

6.  Klik op **Publiceren** om de publicatieworkflow te starten om de beschikbaarheid van uw SKU's-regio bij te werken.

Als een SKU beschikbaar wordt gesteld in een nieuwe regio, u prijzen voor die specifieke regio opgeven via de functionaliteit **Exportpricing Data.** Als u een regio toevoegt die ooit beschikbaar was, u de prijzen niet bijwerken omdat prijswijzigingen niet zijn toegestaan.


### <a name="add-a-new-sku"></a>Een nieuwe SKU toevoegen

Gebruik de volgende stappen om een nieuwe SKU beschikbaar te maken voor uw bestaande aanbieding: 

1.  Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Onder **Alle aanbiedingen** vind je de aanbieding die je wilt updaten.

3.  Klik onder het tabblad **SKU's** op **Nieuwe SKU toevoegen** en geef een **SKU-id** op in de pop-up.

4.  Publiceer de VM opnieuw zoals beschreven in het artikel [Een virtuele machine publiceren naar Azure Marketplace](./cpp-publish-offer.md).

5.  Klik op **Publiceren** om de werkstroom te starten om uw nieuwe SKU te publiceren.


### <a name="update-offer-marketplace-metadata"></a>Update bieden marktplaats metadata

Gebruik de volgende stappen om de metagegevens van de marktplaats bij te werken— bedrijfsnaam, logo's, enz.— die aan uw aanbieding zijn gekoppeld: 

1.  Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Onder **Alle aanbiedingen** vind je de aanbieding die je wilt updaten.

3.  Ga naar het tabblad **Marketplace** en volg de instructies in het artikel [Een virtuele machine publiceren naar Azure Marketplace](./cpp-publish-offer.md) om metagegevens wijzigingen aan te brengen.

4.  Klik op **Publiceren** om de werkstroom te starten om uw wijzigingen te publiceren.


### <a name="update-pricing-on-published-offers"></a>Prijzen bijwerken op gepubliceerde aanbiedingen

Zodra uw pay-as-you-go-aanbieding is gepubliceerd, u de SKU-prijzen niet rechtstreeks verhogen.  (U echter een nieuwe SKU maken onder dezelfde aanbieding, de oude SKU verwijderen en vervolgens uw aanbieding voor nieuwe klanten opnieuw publiceren.)  U de prijs van een gepubliceerde aanbieding daarentegen verlagen met de volgende stappen:

1.  Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Onder **Alle aanbiedingen**vindt u de aanbieding om bij te werken.

3.  Klik op de SKU waarvoor u de prijzen wilt verlagen.

4.  Als u de prijzen in de 1x1 GUI hebt ingesteld, u de prijs rechtstreeks wijzigen in de gebruikersinterface. Als u prijzen instelt via import/export spreadsheet, u de prijzen alleen verlagen via de import/export functie.

3.  Klik op **Opslaan**.

4.  Klik op **Publiceren** om de werkstroom te starten om uw wijzigingen te publiceren.

De nieuwe verlaagde prijzen zullen zichtbaar zijn voor nieuwe klanten zodra deze live is op de website.  Deze nieuwe prijs heeft invloed op uw klanten op de volgende manieren:

- Nieuwe klanten betalen dit nieuwe tarief. 
- Voor bestaande klanten wordt de prijsdaling met terugwerkende kracht weerspiegeld tot het begin van de factureringscyclus waarin de prijsdaling van kracht werd.
Als ze al zijn gefactureerd voor de cyclus waarin een prijsdaling heeft plaatsgevonden, ontvangen ze een terugbetaling tijdens hun volgende factureringscyclus om de verlaagde prijs te dekken.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Vereenvoudigde valutaprijzen

Vanaf 1 september 2018 wordt een nieuwe sectie met de naam **Simplified Currency Pricing** toegevoegd aan het portaal. Microsoft stroomlijnt het Azure Marketplace-bedrijf door meer voorspelbare prijzen en verzamelingen van uw klanten over de hele wereld mogelijk te maken. Deze stroomlijning omvat het verminderen van het aantal valuta's waarin we uw klanten factureren.

De nieuwe sectie zal prijzen in deze nieuwe valuta's.Zodra alle klanten zijn gemigreerd naar deze nieuwe afwikkelingsvaluta's, wordt het oorspronkelijke prijsgedeelte buiten gebruik gesteld en blijft alleen de sectie Vereenvoudigde valutaprijzen over.

U hebt tot 1 november 2018 de tijd om een nieuwe prijs vast te stellen voor de regio's waarin de afwikkelingsvaluta verandert. U zult niet in staat zijn om de prijs te verhogen voor regio's waarin de afwikkeling valuta niet verandert.

> [!NOTE] 
> Als u API's gebruikt om uw aanbieding te publiceren, ziet u mogelijk een nieuwe sectie in de JSON Aanbieding. Dit zou worden geannoteerd als `virtualMachinePricingV2` of `monthlyPricingV2`, afhankelijk van het type aanbod. 

Als u vragen hebt over deze wijziging, neemt u contact op met [Azure Marketplace Support](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Functie vergelijken

Wanneer u wijzigingen aanbrengt in een reeds gepubliceerde aanbieding, u de functie **Vergelijken** gebruiken om de wijzigingen die zijn aangebracht te controleren. Om deze functie te gebruiken, moet u ook het volgende doen:

1.  Klik op elk moment in het bewerkingsproces op de knop **Vergelijken** voor uw aanbieding.

    ![Knop Functie vergelijken](./media/publishvm_037.png)


2.  Bekijk side-by-side versies van marketingassets en metadata.


## <a name="history-of-publishing-actions"></a>Geschiedenis van publicatieacties

Als u een historische publicatieactiviteit wilt weergeven, klikt u op het item **Geschiedenis** in de linkernavigatiemenubalk van Cloud Partner Portal. Hier u de acties met tijdgestempeldbekijken die zijn uitgevoerd tijdens de levensduur van uw Azure Marketplace-aanbiedingen.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

