---
title: Azure Marketplace
description: Hierin wordt beschreven hoe EA-klanten Azure Marketplace kunnen gebruiken
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: banders
ms.openlocfilehash: 83f88ffbd456e64a33d8ca6d8f0ab06098e8b861
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245822"
---
# <a name="azure-marketplace"></a>Azure Marketplace

In dit artikel wordt uitgelegd hoe EA-klanten en -partners de Marketplace-kosten kunnen bekijken en Azure Marketplace-aankopen kunnen inschakelen.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace voor EA-klanten

Voor directe klanten zijn Azure Marketplace-kosten zichtbaar in de Azure Enterprise-portal. Azure Marketplace-aankopen en -verbruik worden buiten de Azure-vooruitbetaling gefactureerd, en worden per kwartaal of per maand gefactureerd.

Indirecte klanten kunnen hun Azure Marketplace-abonnementen vinden op de pagina **Abonnementen beheren** van de Azure Enterprise-portal, maar de prijzen zijn verborgen. Klanten kunnen contact opnemen met hun LSP (Licensing Solutions Provider) voor informatie over de Azure Marketplace-kosten.

Nieuwe maandelijks of jaarlijks terugkerende Azure Marketplace-aankopen worden volledig gefactureerd tijdens de periode waarin Azure Marketplace-items zijn aangeschaft. Deze items worden in de volgende periode op dezelfde dag van de oorspronkelijke aankoop automatisch verlengd.

Bestaande, maandelijkse terugkerende kosten worden op de eerste dag van elke kalendermaand verlengd. Jaarlijkse kosten worden verlengd op de aankoopdatum.

Sommige services van externe resellers die beschikbaar zijn op Azure Marketplace, gebruiken nu het saldo van uw Azure-vooruitbetaling voor Enterprise-overeenkomsten (EA). Eerder werden deze services buiten de Azure-vooruitbetaling voor EA gehouden en werden ze afzonderlijk gefactureerd. De Azure-vooruitbetaling voor EA voor deze services in Azure Marketplace vereenvoudigt het aankoopproces voor klanten en de betalingsafhandeling. Raadpleeg de [update van 6 maart 2018, op de Azure-website](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/) voor een volledige lijst met services die nu uw Azure-vooruitbetaling gebruiken.

### <a name="partners"></a>Partners

LSP’s kunnen een Azure Marketplace-prijslijst downloaden op de pagina met het prijzenoverzicht in de Azure Enterprise-portal. Selecteer de koppeling **Marketplace-prijslijst** in de rechterbovenhoek. In de Azure Marketplace-prijslijst kunt u alle beschikbare services en de bijbehorende prijzen bekijken.

De prijslijst downloaden:

1. Ga in de Azure Enterprise-portal naar **Rapporten** > **Prijzenoverzicht**.
1. Zoek in de rechterbovenhoek de koppeling naar de Azure Marketplace-prijslijst onder uw gebruikersnaam.
1. Klik met de rechtermuisknop op de koppeling en selecteer **Document opslaan als**.
1. Wijzig in het venster **Opslaan** de titel van het document in `AzureMarketplacePricelist.zip`. Hierdoor wordt het bestand gewijzigd van een xlsx-bestand in een zip-bestand.
1. Nadat het downloaden is voltooid, hebt u een zip-bestand met landspecifieke prijslijsten.
1. LSP's moeten verwijzen naar het afzonderlijke landsbestand voor landspecifieke prijzen. LSP’s kunnen het tabblad **Meldingen** gebruiken voor informatie over SKU’s die net nieuw zijn of buiten gebruik zijn gesteld.
1. Prijzen worden niet vaak gewijzigd. LSP’s ontvangen 30 dagen vooraf een e-mailmelding over prijsverhogingen en wijzigingen in vreemde valuta (FX).
1. LSP's ontvangen per kwartaal één factuur per inschrijving en per ISV.

### <a name="enabling-azure-marketplace-purchases"></a>Azure Marketplace-aankopen inschakelen

Ondernemingsbeheerders kunnen Azure Marketplace-aankopen uit- of inschakelen voor alle Azure-abonnementen onder hun inschrijving. Als de ondernemingsbeheerder de aankopen uitschakelt en er Azure-abonnementen zijn die al Azure Marketplace-abonnementen hebben, worden deze Azure Marketplace-abonnementen niet geannuleerd of beïnvloed.

Hoewel klanten hun directe Azure-abonnementen naar Azure EA kunnen converteren door ze te koppelen aan hun inschrijving in de Azure Enterprise-portal, worden de onderliggende abonnementen met deze actie niet automatisch geconverteerd.

Azure Marketplace-aankopen inschakelen:

1. Meld u als ondernemingsbeheerder aan bij de Azure Enterprise-portal.
1. Ga naar **Beheren**.
1. Selecteer onder **Details van inschrijving** het potloodpictogram naast het regelitem **Azure Marketplace**.
1. Gebruik de wisselknop **Ingeschakeld/uitgeschakeld** of **Alleen gratis/BYOL-SKU's** indien van toepassing.
1. Selecteer **Opslaan**.

> [!NOTE]
> BYOL (Bring Your Own License) en met de optie Alleen gratis kunt u de aankoop en verwerving van Azure Marketplace-SKU's beperken tot BYOL en Alleen gratis SKU's.

### <a name="services-billed-hourly-for-azure-ea"></a>Services die per uur worden gefactureerd voor Azure EA

De volgende services worden per uur gefactureerd onder Enterprise Agreement, in plaats van het maandelijkse tarief in MOSP:

- Application Delivery Network
- Web Application Firewall

### <a name="azure-remoteapp"></a>Azure RemoteApp

Als u een Enterprise Agreement hebt, betaalt u voor Azure RemoteApp op basis van het Enterprise Agreement-prijsniveau. Er zijn geen extra kosten. De standaardprijs is inclusief de eerste 40 uur. De onbeperkte prijs omvat de eerste 80 uur. Het gebruik van een RemoteApp stopt na 80 uur.

## <a name="azure-marketplace-faq"></a>Veelgestelde vragen over Azure Marketplace

In deze sectie wordt uitgelegd hoe uw Azure-vooruitbetaling mogelijk van toepassing is op sommige services van externe resellers in Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-azure-ea-prepayment"></a>Wat is er veranderd in Azure Marketplace Services en de Azure-vooruitbetaling voor EA?

Vanaf 1 maart 2018 gebruiken sommige services van externe resellers de Azure-vooruitbetaling voor EA. Met uitzondering van Azure-VM-RI’s (gereserveerde instanties) werden deze services voorheen buiten de Azure-vooruitbetaling in rekening gebracht en afzonderlijk gefactureerd.

We hebben het gebruik van Azure-vooruitbetaling uitgebreid naar de gepubliceerde Azure Marketplace-services van derden die het vaakst worden aangeschaft. Azure-vooruitbetaling voor EA voor deze services in Azure Marketplace vereenvoudigt uw aankoopproces en betalingsafhandeling.

### <a name="why-did-we-make-this-change"></a>Waarom is deze wijziging aangebracht?

Klanten zoeken voortdurend naar extra manieren om de vooraf betaalde Azure-vooruitbetaling te kunnen gebruiken. Deze wijziging werd vaak aangevraagd door klanten en is van invloed op een groot deel van de Azure Marketplace-klanten.

### <a name="how-do-you-benefit"></a>Wat zijn de voordelen voor u?

Facturering is eenvoudiger, en u kunt uw Azure-vooruitbetaling voor EA beter besteden. Omdat deze services zijn opgenomen in uw vooraf betaalde Azure-vooruitbetaling, wordt uw Azure-vooruitbetaling voor EA waardevoller.

### <a name="what-azure-marketplace-services-use-azure-ea-prepayment-and-how-do-i-know"></a>Welke Azure Marketplace-Services maken gebruik van Azure-vooruitbetaling voor EA en hoe weet ik dat?

Wanneer u een service koopt die Azure-vooruitbetaling gebruikt, geeft Azure Marketplace een disclaimer weer. Bepaalde services die zijn gepubliceerd via Red Hat, SUSE, Autodesk en Oracle worden ondersteund. Momenteel worden services met vergelijkbare namen die zijn gepubliceerd door andere partijen, niet afgetrokken van uw Azure-vooruitbetaling. Aan het einde van deze pagina met veelgestelde vragen vindt u een volledige lijst.

### <a name="what-if-my-azure-ea-prepayment-runs-out"></a>Wat gebeurt er als mijn Azure-vooruitbetaling voor EA bijna op is?

Als u uw hele Azure-vooruitbetaling hebt verbruikt en u nu in overschrijding bevindt, worden de kosten voor deze services in de volgende overschrijdingsfactuur weergegeven, samen met andere verbruiksservices. Vóór de wijziging van 1 maart 2018 werden deze kosten gefactureerd met andere Azure Marketplace-services.

### <a name="why-dont-all-azure-marketplaces-consume-azure-ea-prepayment"></a>Waarom maken niet alle Azure Marketplaces gebruik van Azure-vooruitbetaling voor EA?

We proberen altijd de beste klantervaring te leveren met betrekking tot de Azure-vooruitbetaling voor EA. Deze wijziging is van belang voor een groot aantal klanten en een aanzienlijk deel van de totale uitgaven in Azure Marketplace. In de toekomst worden mogelijk andere services toegevoegd.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Hoe heeft dit invloed op indirecte inschrijvingen en partners?

Het heeft geen invloed op onze klanten voor indirecte inschrijving of partners. Deze services zijn onderhevig aan dezelfde partnermogelijkheden als andere verbruiksservices. De enige wijziging is dat de kosten worden weergegeven op een andere factuur, en dat de betaling van de kosten buiten de Azure-vooruitbetaling voor EA van de klant valt.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-azure-ea-prepayment"></a>Is er een lijst met Azure Marketplace-services die gebruikmaken van Azure-vooruitbetaling voor EA?

Voor specifieke Azure Marketplace-aanbiedingen kan Azure-vooruitbetaling worden gebruikt. Raadpleeg [Services van derden die gebruikmaken van Azure-vooruitbetaling](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) voor een volledige lijst met producten die zijn opgenomen in dit programma.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Prijzen](ea-pricing-overview.md).