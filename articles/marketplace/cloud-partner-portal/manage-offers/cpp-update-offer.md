---
title: Update Marketplace-aanbiedingen | Azure Marketplace
description: Aanbiedingen voor Azure en AppSource Marketplace bijwerken met behulp van de Cloud Partner-portal
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: b3f579dbdc943b2380c9de3dde6b2ebf4754d4d1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826714"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Azure Marketplace-en AppSource-aanbiedingen bijwerken

Er zijn verschillende soorten updates die u kunt Toep assen op uw aanbieding nadat deze is gepubliceerd.  De [Cloud Partner-Portal](https://cloudpartner.azure.com/) helpt u bij het goed wijzigen van kenmerken van een aanbieding, waaronder:

-  Nieuwe virtuele machine (VM)-installatie kopie of pakket versie toevoegen aan een bestaande SKU
-  Regio's wijzigen waarvoor een SKU beschikbaar is
-  Nieuwe Sku's toevoegen
-  Het bijwerken van de meta gegevens van Marketplace voor aanbiedingen of Sku's 
-  Prijzen bijwerken voor aanbiedingen met betalen naar gebruik

De portal heeft ook functies, zoals de mogelijkheid om functies te vergelijken en een geschiedenis van de functies van een aanbieding te bekijken, die u helpen bij het beheren van wijzigingen.  Nadat u een aanbieding of SKU hebt gewijzigd, moet deze opnieuw worden gepubliceerd voordat de wijzigingen ' live ' gaan.  Dit artikel begeleidt u door de verschillende aspecten van het bijwerken van uw Marketplace-aanbieding.

## <a name="unpermitted-changes-to-an-offersku"></a>Niet-toegestane wijzigingen in een aanbieding/SKU

Er zijn een aantal kenmerken van een aanbieding of SKU die niet meer kunnen worden gewijzigd nadat deze is gepubliceerd in de Marketplace.  De bijbehorende velden zijn uitgeschakeld op het tabblad **Editor** van de portal, bijvoorbeeld:  

- Aanbiedings-ID en uitgevers-ID
- SKU-ID 
- Aantal gegevens schijven van bestaande Sku's
- Wijzigingen in het facturerings-en licentie model van bestaande Sku's
- Versie Tags, bijvoorbeeld: `1.0.1`


## <a name="common-update-operations"></a>Algemene update bewerkingen

In de volgende secties wordt uitgelegd hoe u een aantal van de meest update bewerkingen kunt uitvoeren.  Deze bewerkingen zijn niet beschikbaar voor alle aanbiedings typen.  U moet zich aanmelden bij de Cloud Partner-portal om een van deze bewerkingen te starten.


### <a name="update-offer-contacts"></a>Contact personen van aanbieding bijwerken

Gebruik de volgende stappen om de ondersteunings contactpersonen voor uw aanbieding bij te werken.
1. Selecteer op de pagina **alle aanbiedingen** de aanbieding.
2. Selecteer het tabblad **contact personen** . werk uw contact personen bij.
3. Selecteer de knop **Opslaan**.
4. Selecteer **publiceren** om het publicatie proces te starten.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Regio's wijzigen een aanbieding of SKU is beschikbaar in

In de loop van de tijd wilt u uw aanbieding/SKU mogelijk beschikbaar maken in meer regio's.
U kunt er ook voor zorgen dat u de ondersteuning van de aanbieding/SKU in een bepaalde regio wilt beëindigen.
Voer de volgende stappen uit om deze wijzigingen te implementeren.

1. Zoek op de pagina **alle aanbiedingen** naar de aanbieding die u wilt bijwerken.

Voor Azure Marketplace kunt u het volgende doen:

1. Selecteer het tabblad **sku's** .  Selecteer de SKU die u wilt wijzigen.
1. Klik op de knop **landen selecteren** onder het veld **Beschik baarheid land/regio** .
1. Voeg in het dialoog venster Beschik baarheid van regio de regio's voor deze SKU toe of verwijder deze.

Voor AppSource-aanbiedingen:

1. Selecteer het tabblad Details van de **winkel** .
1. Klik naast het label **ondersteunde landen/regio's** op **ondersteunde landen/regio's**. 
1. Voeg in het dialoog venster ondersteunde landen/regio's de regio's voor deze aanbieding toe of verwijder deze.

Voor Marketplace:

1. Klik op **publiceren** om het publicatie proces te starten. 

Als een SKU beschikbaar wordt gemaakt in een nieuwe regio, hebt u de mogelijkheid om prijzen voor deze regio op te geven via de functionaliteit voor het **exporteren van prijs gegevens** . Als u een eerder beschik bare regio hebt toegevoegd, kunt u de prijs categorie niet bijwerken omdat de prijs wijzigingen niet zijn toegestaan.


### <a name="add-a-new-sku"></a>Een nieuwe SKU toevoegen 

Gebruik de volgende stappen om een nieuwe SKU beschikbaar te maken voor een bestaande aanbieding:

1. Zoek de aanbieding op de pagina **alle aanbiedingen** .
3. Klik onder het formulier **sku's** op **nieuwe SKU toevoegen** en geef een **SKU-id** op in het pop-upvenster.
4. Volg de overige stappen in de aanbieding voor het [publiceren van een virtuele machine](../virtual-machine/cpp-publish-offer.md).
5. Klik op **publiceren** om het publicatie proces te starten.


### <a name="update-offer-marketplace-assets"></a>Update aanbod Marketplace-assets

Mogelijk hebt u de mogelijkheid om de op tekst gebaseerde en afbeeldings activa van de Marketplace bij te werken, zoals uw bedrijfs logo's, een beschrijving van de aanbieding, enzovoort. Gebruik de volgende stappen om deze assets bij te werken.

1. Ga op de pagina **alle aanbiedingen** naar uw aanbieding. 
2. Selecteer het tabblad **Marketplace** en volg de instructies in het *tabblad Marketplace* van het aanbod.
3. Klik op **publiceren** om het publicatie proces te starten.


### <a name="update-pricing-on-published-offers"></a>Prijzen voor gepubliceerde aanbiedingen bijwerken

Zodra uw aanbieding voor betalen naar gebruik is gepubliceerd, kunt u de prijs van een bestaande SKU niet verhogen.  Maak in plaats daarvan een SKU onder dezelfde aanbieding, verwijder de oude SKU en publiceer uw aanbieding opnieuw. U kunt de prijs van eerder gepubliceerde aanbiedingen verlagen. Uw aanbiedings prijs verlagen:

1. Selecteer de SKU waarvoor u de prijzen wilt verlagen.
2. U moet de laagste prijs instellen volgens hetzelfde mechanisme dat u oorspronkelijk hebt gebruikt: rechtstreeks in de gebruikers interface van de portal of in het werk blad importeren/exporteren.
3. Klik op **Opslaan**.
4. Klik op **publiceren** om het publicatie proces te starten.

De prijzen zijn zichtbaar voor nieuwe klanten zodra deze live op Marketplace zijn, en alle nieuwe klanten betalen dan de nieuwe prijs.  Voor bestaande klanten wordt de prijs verlaging terugwerkt naar het begin van de facturerings cyclus, waardoor de prijs verlaging effectief werd.  Als ze al zijn gefactureerd voor de cyclus gedurende welke een prijs verlaging zich voordeed, ontvangt hij een terugbetaling tijdens de volgende facturerings cyclus om de verlaagde prijs te dekken.


## <a name="compare-feature"></a>Functie vergelijken

Wanneer u wijzigingen aanbrengt in een gepubliceerde aanbieding, kunt u de functie *compare* gebruiken om de wijzigingen te controleren. Gebruik deze functie als volgt:

1. U kunt op elk moment in het bewerkings proces op de knop **vergelijken** klikken in het tabblad **Editor** voor uw aanbieding.
2. In een vergelijkings venster worden naast de Marketplace-aanbieding side-by-side-versies van de opgeslagen wijzigingen voor deze aanbieding weer gegeven. 

![De knop aanbieding vergelijken op het tabblad Editor](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Geschiedenis van publicatie acties

Als u de historische publicatie activiteit wilt weer geven, selecteert u het tabblad **geschiedenis** in de verticale balk links van het Cloud Partner-Portal.  De geschiedenis pagina biedt flexibele filtering door diverse kenmerken en biedt ondersteuning voor de volg orde van kolommen.  Elke publicatie gebeurtenis bevindt zich in een tijds tempel.  Zie de [pagina controle geschiedenis](../portal-tour/cpp-history-page.md)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

U kunt ook de Cloud Partner-portal gebruiken om [een gepubliceerde SKU of aanbieding te verwijderen](./cpp-delete-offer.md).
