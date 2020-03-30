---
title: Facturering met gemeten facturering met behulp van de marktplaatsmeetservice | Azure Marketplace
description: Deze documentatie is een handleiding voor ISV's die SaaS-aanbiedingen publiceren met flexibele factureringsmodellen.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e5a4813301cbab16d1cffabaaa60688f6e826ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281320"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Facturering met gemeten gemeten met behulp van de marktplaatsmetingservice

Met de Marketplace-meetservice u software-as-a-service -aanbiedingen (SaaS) maken in het commerciële marktplaatsprogramma die in rekening worden gebracht volgens niet-standaardeenheden.  Voordat u deze aanbieding publiceert, definieert u de factureringsdimensies, zoals bandbreedte, tickets of e-mails die zijn verwerkt.  Klanten betalen vervolgens op basis van hun verbruik van deze dimensies, waarbij uw systeem Microsoft informeert via de Marketplace-meetservice-API van factureerbare gebeurtenissen wanneer deze zich voordoen.  

## <a name="prerequisites-for-metered-billing"></a>Voorwaarden voor facturering met datameter

Om een SaaS-aanbieding te laten gebruiken voor facturering met datameter, moet het:

* Voldoe aan alle aanbiedingsvereisten voor een [verkoop via Microsoft-aanbieding](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) zoals beschreven in [Een SaaS-aanbieding maken.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer)
* Integreer met de [SaaS Fulfillment API's](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) voor klanten om uw aanbod in te richten en aan te sluiten.  
* Wees geconfigureerd voor het **forfaitaire** prijsmodel voor het opladen van klanten voor uw service.  Afmetingen zijn een optionele uitbreiding van het forfaitaire prijsmodel. 
* Integreer met de [API's van de Marketplace-meetservice](./marketplace-metering-service-apis.md) om Microsoft op de hoogte te stellen van factureerbare gebeurtenissen.

>[!Note]
>De Marketplace-meetservice is alleen beschikbaar voor het factureringsmodel met vaste tariefen en is niet van toepassing op het factureringsmodel per gebruiker.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Hoe facturering met datameter past bij de prijsstelling

Als het gaat om het definiëren van het aanbod samen met de prijsmodellen, is het belangrijk om de aanbiedingshiërarchie te begrijpen.

* Elke SaaS-aanbieding is geconfigureerd om via Microsoft te verkopen of niet.  Deze instelling kan niet worden gewijzigd nadat een aanbieding is gepubliceerd.
* Elke SaaS-aanbieding, geconfigureerd om te verkopen via Microsoft, kan een of meer abonnementen hebben. Een gebruiker abonneert zich op de SaaS-aanbieding, maar wordt gekocht via Microsoft in het kader van een abonnement.
* Aan elk plan is een prijsmodel gekoppeld: **vast tarief** of **per gebruiker.** Alle abonnementen in een aanbieding moeten aan hetzelfde prijsmodel worden gekoppeld. Er kan bijvoorbeeld geen aanbieding zijn waarbij een van de plannen een vast prijsmodel is en een ander is per prijsmodel per gebruiker.
* Binnen elk abonnement dat is geconfigureerd voor een factureringsmodel met een vast tarief, zijn ten minste één terugkerende vergoeding (die $ 0 kan zijn) inbegrepen:
    * Terugkerende **maandelijkse** kosten: vaste maandelijkse kosten die vooraf worden betaald bij een maandelijkse herhaling wanneer de gebruiker het abonnement koopt.
    * Terugkerende **jaarlijkse** kosten: vaste jaarlijkse vergoeding die vooraf wordt betaald bij een jaarlijkse herhaling wanneer de gebruiker het plan koopt.
* Naast de terugkerende kosten kan het plan ook optionele afmetingen bevatten die worden gebruikt om klanten in rekening te brengen voor gebruik dat niet in het vaste tarief is inbegrepen.   Elke dimensie vertegenwoordigt een factureerbare eenheid die uw service met Microsoft communiceert via de [API voor marketplace-meetservice.](./marketplace-metering-service-apis.md)

## <a name="sample-offer"></a>Voorbeeldaanbieding

Contoso is bijvoorbeeld een uitgever met een SaaS-service genaamd Contoso Notification Services (CNS). CNS stelt klanten in staat om meldingen te verzenden via e-mail of sms. Contoso is geregistreerd als uitgever in Partner Center voor het commerciële marktplaatsprogramma om aanbiedingen aan Azure-klanten te publiceren.  Er zijn twee plannen gekoppeld aan CNS, hieronder beschreven:

* Basisplan
    * Stuur 10000 e-mails en 1000 sms'jes voor $ 0 per maand
    * Buiten de 10000 e-mails, betalen $ 1 voor elke 100 e-mails
    * Naast de 1000 teksten, betaal $0.02 voor elke tekst
* Premium-abonnement
    * Stuur 50000 e-mails en 10000 sms'jes voor $ 350/maand
    * Buiten de 50000 e-mails, betalen 0,5 dollar voor elke 100 e-mails
    * Naast de 10000 teksten, betaal $0.01 voor elke tekst

Een Azure-klant die zich abonneert op de CNS-service, kan de hoeveelheid tekst en e-mails per maand verzenden op basis van het geselecteerde abonnement.  Contoso meet het gebruik tot de meegeleverde hoeveelheid zonder gebruiksgebeurtenissen naar Microsoft te verzenden.  Wanneer klanten meer verbruiken dan de meegeleverde hoeveelheid, hoeven ze geen plannen te wijzigen of iets anders te doen.  Contoso meet de overschrijding buiten de meegeleverde hoeveelheid en start met het uitzenden van gebruiksgebeurtenissen naar Microsoft voor extra gebruik met behulp van de [Marketplace-meetservice-API.](./marketplace-metering-service-apis.md)  Microsoft brengt op zijn beurt kosten in rekening voor het extra gebruik zoals opgegeven door de uitgever.

## <a name="billing-dimensions"></a>Factureringsafmetingen

Factureringsafmetingen worden gebruikt om met de klant te communiceren over hoe deze worden gefactureerd voor het gebruik van de software, en ook om gebruiksgebeurtenissen aan Microsoft te communiceren. Zij worden als volgt gedefinieerd:

* **Dimensie-id:** de onveranderlijke id waarnaar wordt verwezen tijdens het uitzenden van gebruiksgebeurtenissen.
* **Dimensienaam:** de weergavenaam die aan de dimensie is gekoppeld, bijvoorbeeld "verzonden tekstberichten".
* **Maateenheid**: de beschrijving van de factureringseenheid, bijvoorbeeld "per sms" of "per 100 e-mails".
* **Prijs per eenheid**: de prijs voor één eenheid van de dimensie.  
* **Inbegrepen hoeveelheid voor de maandelijkse termijn**: hoeveelheid dimensie die per maand is opgenomen voor klanten die de terugkerende maandelijkse kosten betalen, moet een geheel getal zijn.
* **Inbegrepen hoeveelheid voor de jaarlijkse looptijd**: hoeveelheid dimensie die per maand is opgenomen voor klanten die de terugkerende jaarlijkse vergoeding betalen, moet een geheel getal zijn.

Factureringsdimensies worden gedeeld in alle plannen voor een aanbieding.  Sommige kenmerken zijn van toepassing op de dimensie voor alle plannen en andere kenmerken zijn planspecifiek.

De kenmerken die de dimensie zelf definiëren, worden gedeeld in alle plannen voor een aanbieding.  Voordat u de aanbieding publiceert, is een wijziging in deze kenmerken vanuit de context van een plan van invloed op de dimensiedefinitie in alle plannen.  Zodra u de aanbieding publiceert, zijn deze kenmerken niet meer bewerkbaar.  Deze kenmerken zijn:

* Id
* Name
* Meeteenheid

De andere kenmerken van een dimensie zijn specifiek voor elk plan en kunnen verschillende waarden hebben van plan tot plan.  Voordat u het plan publiceert, u deze waarden bewerken en alleen dit plan wordt beïnvloed.  Zodra u het plan publiceert, zijn deze kenmerken niet meer bewerkbaar.  Deze kenmerken zijn:

* Prijs per eenheid
* Inbegrepen hoeveelheid voor maandelijkse klanten 
* Inbegrepen hoeveelheid voor jaarlijkse klanten 

Dimensions hebben ook twee speciale concepten, "ingeschakeld" en "oneindig":

* **Ingeschakeld** geeft aan dat dit plan deelneemt aan deze dimensie.  U dit niet-gecontroleerd laten controleren als u een nieuw plan maakt dat geen gebruiksgebeurtenissen verzendt op basis van deze dimensie.  Ook worden eventuele nieuwe dimensies die zijn toegevoegd nadat een plan voor het eerst is gepubliceerd, weergegeven als "niet ingeschakeld" op het reeds gepubliceerde plan.  Een uitgeschakelde dimensie wordt niet weergegeven in lijsten met dimensies voor een abonnement dat door klanten wordt gezien.
* **Infinite**, vertegenwoordigd door het oneindige symbool "",, geeft aan dat dit plan deelneemt aan deze dimensie, maar niet meter gebruik tegen deze dimensie.  Als u uw klanten wilt aangeven dat de functionaliteit die door deze dimensie wordt vertegenwoordigd, in het plan is opgenomen, maar zonder beperking van het gebruik.  Een dimensie met oneindig gebruik wordt weergegeven in lijsten met dimensies voor een plan dat door klanten wordt gezien, met een indicatie dat dit plan nooit in rekening wordt gebracht.

>[!Note] 
>De volgende scenario's worden expliciet ondersteund: <br> - U een nieuwe dimensie toevoegen aan een nieuw plan.  De nieuwe dimensie is niet ingeschakeld voor reeds gepubliceerde plannen. <br> - U een **vast plan** zonder afmetingen publiceren, vervolgens een nieuw plan toevoegen en een nieuwe dimensie voor dat plan configureren. De nieuwe dimensie zal niet worden ingeschakeld voor reeds gepubliceerde plannen.

## <a name="constraints"></a>Beperkingen

### <a name="trial-behavior"></a>Proefgedrag

Facturering met een gemeten beperking via de marketplace-meetservice is niet compatibel met het aanbieden van een gratis proefperiode.  Het is niet mogelijk om een plan te configureren voor zowel facturering met datameter als een gratis proefperiode.

### <a name="locking-behavior"></a>Vergrendelingsgedrag

Omdat een dimensie die wordt gebruikt met de Marketplace-meetservice inzicht geeft in hoe een klant voor de service betaalt, zijn alle details voor een dimensie niet meer bewerkbaar zodra u deze publiceert.  Het is belangrijk dat u uw afmetingen volledig hebt gedefinieerd voor een plan voordat u publiceert.
  
Zodra een aanbieding met een dimensie is gepubliceerd, kunnen de details op aanbiedingsniveau voor die dimensie niet langer worden gewijzigd:

* Id
* Name
* Meeteenheid

Zodra een plan is gepubliceerd, kunnen de details op planniveau niet meer worden gewijzigd:

* Prijs per eenheid
* Inbegrepen hoeveelheid voor de maandelijkse termijn
* Opgenomen hoeveelheid voor de jaarlijkse looptijd
* Of de dimensie is ingeschakeld voor het plan

### <a name="upper-limits"></a>Bovengrenzen

Het maximum aantal dimensies dat kan worden geconfigureerd voor één aanbieding is 18 unieke afmetingen.

## <a name="get-support"></a>Ondersteuning krijgen

Als je een van de volgende opties hebt, kun je een ondersteuningsticket openen.

* Technische problemen met marketplace meeting service API.
* Een probleem dat moet worden geëscaleerd als gevolg van een fout of bug aan uw kant (bijvoorbeeld. verkeerde gebruiksgebeurtenis).
* Alle andere problemen met betrekking tot facturering met datameter. 

Volg de onderstaande stappen om je supportticket in te dienen:

1. Ga naar de [ondersteuningspagina.](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff) De eerste paar vervolgkeuzemenu's worden automatisch voor u ingevuld. Voor Marketplace-ondersteuning u de productfamilie identificeren als **Cloud- en Online Services,** het product als **Marketplace-uitgever.**  Wijzig de vooraf ingevulde vervolgkeuzemenuselecties niet.
2. Selecteer onder 'Selecteer de productversie' het **beheer van de live-aanbieding**.
3. Kies **SaaS-apps**onder 'Selecteer een categorie die het probleem het beste beschrijft.
4. Selecteer onder 'Selecteer een probleem dat het probleem het beste beschrijft' facturering **met datameter**.
5. Door de knop **Volgende te** selecteren, wordt u doorverwezen naar de pagina Details van **het probleem,** waar u meer details over uw probleem invoeren.

Zie [Ondersteuning voor het commerciële marktplaatsprogramma in partnercentrum](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support) voor meer opties voor ondersteuning van uitgevers.

## <a name="next-steps"></a>Volgende stappen

- Zie [API's voor marketplace-meetservice voor](./marketplace-metering-service-apis.md) meer informatie.
