---
title: Aanbiedingen van de updatemarktplaats | Azure Marketplace
description: Aanbiedingen op de Azure- en AppSource-marketplaces bijwerken met behulp van de Cloud Partner Portal
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 101369a050770be3acd9534cef6229037fe1c366
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288509"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Azure Marketplace- en AppSource-aanbiedingen bijwerken

Er zijn verschillende soorten updates die u toepassen op uw aanbieding nadat deze is gepubliceerd.  De [Cloud Partner Portal](https://cloudpartner.azure.com/) helpt u bij het correct wijzigen van kenmerken van een aanbieding, waaronder:

-  Nieuwe virtuele machine (VM)-afbeelding of pakketversie toevoegen aan een bestaande SKU
-  Regio's wijzigen waarin een SKU beschikbaar is
-  Nieuwe SKU's toevoegen
-  Metagegevens van marktplaatsen bijwerken voor aanbiedingen of SKU's 
-  Prijzen bijwerken voor betalen per gebruik-aanbiedingen

De portal heeft ook functies, zoals de mogelijkheid om functies te vergelijken en een geschiedenis van functies voor een aanbieding te bekijken, die u helpen bij het beheren van wijzigingen.  Nadat u een aanbieding of SKU hebt gewijzigd, moet deze opnieuw worden gepubliceerd voordat de wijzigingen "live" gaan.  Dit artikel leidt u door de verschillende aspecten van het bijwerken van uw marktplaats aanbieding.

## <a name="unpermitted-changes-to-an-offersku"></a>Niet-toegestane wijzigingen in een aanbieding/SKU

Er zijn enkele kenmerken van een aanbieding of SKU die niet kunnen worden gewijzigd nadat deze op de markt is gepubliceerd.  De bijbehorende velden zijn uitgeschakeld op het tabblad **Editor** van de portal, bijvoorbeeld:  

- Aanbiedings-id en Publisher-id
- SKU-id 
- Aantal gegevensschijf van bestaande SKU's
- Wijzigingen in facturering/licentiemodel van bestaande SKU's
- Versietags, bijvoorbeeld:`1.0.1`


## <a name="common-update-operations"></a>Algemene updatebewerkingen

In de volgende secties wordt uitgelegd hoe u enkele van de meest updatebewerkingen uitvoeren.  Deze bewerkingen zijn niet beschikbaar voor alle aanbiedingstypen.  U moet zich aanmelden bij de Cloud Partner Portal om een van deze bewerkingen te starten.


### <a name="update-offer-contacts"></a>Contactpersonen voor aanbieding bijwerken

Gebruik de volgende stappen om de ondersteuningscontactpersonen voor uw aanbieding bij te werken.
1. Selecteer op de pagina **Alle aanbiedingen** de aanbieding.
2. Selecteer het tabblad **Contactpersonen.** Werk uw contactpersonen bij.
3. Selecteer de knop **Opslaan**.
4. Selecteer **Publiceren** om het publicatieproces te starten.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Regio's wijzigen een aanbieding of SKU is beschikbaar in

Na verloop van tijd u uw aanbieding/SKU beschikbaar maken in meer regio's.
U ook stoppen met het ondersteunen van de aanbieding/SKU in een bepaalde regio.
Volg de volgende stappen om deze wijzigingen te implementeren.

1. Zoek op de pagina **Alle aanbiedingen** de aanbieding die u wilt bijwerken.

Voor Azure Marketplace-aanbiedingen:

1. Selecteer het tabblad **SKU's.**  Selecteer de SKU die u wilt wijzigen.
1. Klik **op** de knop Landen selecteren onder het **veld Beschikbaarheid land/regio.**
1. Voeg in het dialoogvenster regiobeschikbaarheid de regio's voor deze SKU toe of verwijder deze.

Voor AppSource-aanbiedingen:

1. Selecteer het tabblad **StorefrontDetails.**
1. Klik naast het label **Ondersteunde landen/regio's** op **Ondersteunde landen/regio's**. 
1. Voeg in het dialoogvenster Ondersteunde landen/regio's de regio's voor deze aanbieding toe of verwijder deze.

Voor beide marktplaatsen:

1. Klik **op Publiceren** om het publicatieproces te starten. 

Als een SKU beschikbaar wordt gesteld in een nieuwe regio, u prijzen voor die specifieke regio opgeven via de functionaliteit **Exportprijzen.** Als u een regio toevoegt die voorheen beschikbaar was, u de prijzen niet bijwerken omdat prijswijzigingen niet zijn toegestaan.


### <a name="add-a-new-sku"></a>Een nieuwe SKU toevoegen 

Als u een nieuwe SKU beschikbaar wilt maken voor een bestaande aanbieding, gebruikt u de volgende stappen:

1. Zoek op de pagina **Alle aanbiedingen** het aanbod.
3. Klik onder het **SKU-formulier** op **Nieuwe SKU toevoegen** en geef een **SKU-id** op in de pop-up.
4. Volg de rest van de stappen die worden beschreven in [Het publiceren van een virtuele machine aanbieding](../virtual-machine/cpp-publish-offer.md).
5. Klik **op Publiceren** om het publicatieproces te starten.


### <a name="update-offer-marketplace-assets"></a>Update bieden marktplaats activa

U scenario's hebben waarin u de tekstgebaseerde en beeldactiva van de marktplaats, zoals uw bedrijfslogo's, aanbiedingsbeschrijving, enz. Gebruik de volgende stappen om deze elementen bij te werken.

1. Zoek op de pagina **Alle aanbiedingen** uw aanbieding. 
2. Selecteer het tabblad **Marketplace** en volg de instructies in het *tabblad Marktplaats-onderwerp* van je aanbieding.
3. Klik **op Publiceren** om het publicatieproces te starten.


### <a name="update-pricing-on-published-offers"></a>Prijzen bijwerken op gepubliceerde aanbiedingen

Zodra uw pay-as-you-go-aanbieding is gepubliceerd, u de prijs van een bestaande SKU niet verhogen.  Maak in plaats daarvan een SKU onder dezelfde aanbieding, verwijder de oude SKU en publiceer vervolgens uw aanbieding opnieuw. U de prijs verlagen voor eerder gepubliceerde aanbiedingen. Ga als beste de andere prijs voor uw aanbieding verlagen:

1. Selecteer de SKU waarvoor u de prijzen wilt verlagen.
2. U moet de lagere prijs instellen volgens hetzelfde mechanisme dat u oorspronkelijk hebt gebruikt: rechtstreeks in de gebruikersinterface van de portal of met de spreadsheet importeren/exporteren.
3. Klik op **Opslaan**.
4. Klik **op Publiceren** om het publicatieproces te starten.

De prijzen zijn zichtbaar voor nieuwe klanten zodra het live is op de markt, en alle nieuwe klanten betalen dan de nieuwe verlaagde prijs.  Voor bestaande klanten wordt de prijsdaling met terugwerkende kracht weerspiegeld tot het begin van de factureringscyclus waarin de prijsdaling van kracht werd.  Als ze al zijn gefactureerd voor de cyclus waarin een prijsdaling heeft plaatsgevonden, ontvangen ze een terugbetaling tijdens hun volgende factureringscyclus om de verlaagde prijs te dekken.


## <a name="compare-feature"></a>Functie vergelijken

Wanneer u wijzigingen aanbrengt in een gepubliceerde aanbieding, u de functie *Vergelijken* gebruiken om de wijzigingen te controleren. Ga als volgt te werk om deze functie te gebruiken:

1. Op elk moment in het bewerkingsproces u op de knop **Vergelijken** klikken op het tabblad **Editor** voor uw aanbieding.
2. In een vergelijkingsvenster worden naast elkaar versies van de opgeslagen wijzigingen in deze aanbieding weergegeven in vergelijking met het marketplace-aanbod. 

![Knop Aanbieding vergelijken op het tabblad Editor](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Geschiedenis van publicatieacties

Als u historische publicatieactiviteiten wilt weergeven, selecteert u het tabblad **Geschiedenis** in de linker verticale menubalk van de Cloud Partner Portal.  De pagina Geschiedenis biedt flexibele filtering op verschillende kenmerken en ondersteunt het bestellen van kolommen.  Elke publicatiegebeurtenis is voorzien van een tijdstempel.  Zie [Pagina Auditgeschiedenis](../portal-tour/cpp-history-page.md)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

U de Cloud Partner Portal ook gebruiken om [een gepubliceerde SKU of aanbieding](./cpp-delete-offer.md)te verwijderen.
