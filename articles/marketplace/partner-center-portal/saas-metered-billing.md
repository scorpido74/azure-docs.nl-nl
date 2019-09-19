---
title: Facturering met data limieten met Marketplace meter service | Azure Marketplace
description: Deze documentatie is een hand leiding voor Isv's die SaaS-aanbiedingen publiceren met flexibele facturerings modellen.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a244e4f7ac406fecd4c053b39a5e9a9cb6ecab2c
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105502"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Facturering met data limieten met behulp van Marketplace-meet service

Met de Marketplace-meet service kunt u SaaS-aanbiedingen (Software-as-a-Service) maken in het commerciële Marketplace-programma dat wordt gefactureerd op basis van niet-standaard eenheden.  Voordat u deze aanbieding publiceert, definieert u de facturerings dimensies, zoals band breedte, tickets of e-mail berichten die zijn verwerkt.  Klanten betalen vervolgens op basis van het verbruik van deze dimensies, waarbij uw systeem micro soft informeert via de Marketplace meter Service API van factureer bare gebeurtenissen wanneer deze zich voordoen.  

## <a name="prerequisites-for-metered-billing"></a>Vereisten voor facturering met data limiet

Om een SaaS-aanbieding te kunnen gebruiken, moet deze:

* Voldoen aan de vereisten van de aanbieding voor een [verkoop via micro soft-aanbieding](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) , zoals beschreven in [een SaaS-aanbieding maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer).
* Integreer met de [SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) -fulfillment-api's voor klanten om uw aanbieding in te richten en er verbinding mee te maken.  
* Worden geconfigureerd voor het prijs model voor **vaste kosten** voor het opladen van klanten voor uw service.  Dimensies zijn een optionele uitbrei ding voor het prijs model voor vaste tarieven. 
* Integreer met de [api's van de Marketplace-meet service](./marketplace-metering-service-apis.md) om micro soft te informeren over factureer bare gebeurtenissen.

>[!Note]
>Marketplace-meet service is alleen beschikbaar voor het facturerings model vast tarief en is niet van toepassing op het facturerings model per gebruiker.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Hoe gefactureerde facturering past bij de prijzen

Wanneer het gaat om het definiëren van de aanbieding samen met de prijs modellen, is het belang rijk om inzicht te krijgen in de aanbiedings hiërarchie.

* Elke SaaS-aanbieding is zo geconfigureerd dat deze wordt verkocht via micro soft of niet.  Deze instelling kan niet worden gewijzigd nadat een aanbieding is gepubliceerd.
* Elk SaaS-aanbod dat is geconfigureerd om te worden verkocht via micro soft, kan een of meer abonnementen hebben. Een gebruiker meldt zich aan bij de SaaS-aanbieding, maar wordt aangeschaft via micro soft binnen de context van een abonnement.
* Aan elk abonnement is een prijs model gekoppeld: **vast tarief** of **per gebruiker**. Alle abonnementen in een aanbieding moeten aan hetzelfde prijs model zijn gekoppeld. Een voor beeld: er kan geen aanbieding zijn waarin een van de abonnementen een prijs model voor vaste tarieven is en een andere prijs model per gebruiker.
* Binnen elk schema dat is geconfigureerd voor een facturerings model met een vast tarief, is ten minste één terugkerend tarief (dat kan $0), opgenomen:
    * Terugkerende **maandelijkse** kosten: vaste maandelijkse kosten die vooraf zijn betaald op een maandelijks terugkeer patroon wanneer de gebruiker het abonnement koopt.
    * Terugkerende **jaarlijkse** kosten: forfaitaire jaarlijkse kosten die vooraf worden betaald bij een jaarlijks terugkeer patroon wanneer de gebruiker het abonnement koopt.
* Naast de terugkerende kosten kan het plan ook optionele dimensies bevatten die worden gebruikt om klanten in rekening te brengen voor gebruik dat niet in het vast tarief is opgenomen.   Elke dimensie vertegenwoordigt een factureer bare eenheid die uw service communiceert met micro soft via de [Marketplace meter Service-API](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Voor beeld aanbieding

Contoso is bijvoorbeeld een uitgever met een SaaS-service met de naam contoso Notification Services (CNS). Met CNS kunnen klanten meldingen verzenden via e-mail of tekst. Contoso is geregistreerd als een uitgever in het partner centrum voor het programma voor commerciële Marketplace voor het publiceren van aanbiedingen aan Azure-klanten.  Er zijn twee plannen gekoppeld aan CNS, die hieronder worden beschreven:

* Basis plan
    * 10000 e-mails en 1000 teksten verzenden voor $0/maand
    * Na de 10000 e-mails betaalt u $1 voor elke 100 e-mail berichten
    * Na de 1000 teksten betaalt u voor elke tekst een bedrag van $0,02
* Premium-abonnement
    * 50000 e-mails en 10000 teksten verzenden voor $350/maand
    * Na de 50000 e-mails betaalt u $0,5 voor elke 100 e-mail berichten
    * Na de 10000 teksten betaalt u voor elke tekst een bedrag van $0,01

Een Azure-klant die zich abonneert op de CNS-service, kan het inbegrepen aantal tekst en e-mails per maand verzenden op basis van het geselecteerde plan.  Contoso meet het gebruik tot het inbegrepen aantal zonder gebruik van gebeurtenissen naar micro soft te verzenden.  Wanneer klanten meer dan het inbegrepen aantal verbruiken, hoeven ze geen plannen te wijzigen of iets anders te doen.  Contoso meet de overschrijding buiten het inbegrepen aantal en start het verzenden van gebruiks gebeurtenissen naar micro soft voor extra gebruik met de [Marketplace meter Service-API](./marketplace-metering-service-apis.md).  Micro soft brengt de klant in rekening voor het aanvullende gebruik zoals opgegeven door de uitgever.

## <a name="billing-dimensions"></a>Facturerings dimensies

Facturerings dimensies worden gebruikt om aan de klant te communiceren over hoe ze worden gefactureerd voor het gebruik van de software en om gebruiks gebeurtenissen te communiceren met micro soft. Ze worden als volgt gedefinieerd:

* **Dimensie-id**: de onveranderbare id waarnaar wordt verwezen tijdens het verzenden van gebruiks gebeurtenissen.
* **Dimensie naam**: de weergave naam die aan de dimensie is gekoppeld, bijvoorbeeld ' verzonden tekst berichten '.
* **Maat eenheid**: de beschrijving van de facturerings eenheid, bijvoorbeeld ' per SMS-bericht ' of ' per 100 e-mail berichten '.
* **Prijs per eenheid**: de prijs voor één eenheid van de dimensie.  
* **Inbegrepen hoeveelheid voor maandelijkse periode**: de hoeveelheid dimensie die per maand wordt opgenomen voor klanten die de terugkerende maandelijkse kosten betalen, moet een geheel getal zijn.
* **Inbegrepen hoeveelheid voor jaarlijkse periode**: de hoeveelheid dimensie die per maand wordt opgenomen voor klanten die de terugkerende jaarlijkse kosten betalen, moet een geheel getal zijn.

Facturerings dimensies worden gedeeld in alle abonnementen voor een aanbieding.  Sommige kenmerken zijn van toepassing op de dimensie over alle plannen en andere kenmerken zijn specifiek voor een plan.

De kenmerken die de dimensie zelf definiëren, worden verdeeld over alle abonnementen voor een aanbieding.  Voordat u de aanbieding publiceert, is een wijziging in deze kenmerken van de context van een plan van invloed op de dimensie definitie voor alle plannen.  Zodra u de aanbieding hebt gepubliceerd, kunnen deze kenmerken niet meer worden bewerkt.  Deze kenmerken zijn:

* ID
* Name
* Maateenheid

De andere kenmerken van een dimensie zijn specifiek voor elk plan en kunnen verschillende waarden hebben van plan tot plan.  Voordat u het abonnement publiceert, kunt u deze waarden bewerken. Dit geldt alleen voor dit abonnement.  Zodra u het abonnement hebt gepubliceerd, kunnen deze kenmerken niet meer worden bewerkt.  Deze kenmerken zijn:

* Prijs per eenheid
* Inbegrepen hoeveelheid voor maandelijkse klanten 
* Inbegrepen hoeveelheid voor jaarlijkse klanten 

Dimensies hebben ook twee speciale concepten: ' enabled ' en ' infinite '.

* **Ingeschakeld** geeft aan dat dit plan deelneemt aan deze dimensie.  U kunt dit selectie vakje uitschakelen als u een nieuw plan maakt dat geen gebruiks gebeurtenissen op basis van deze dimensie verzendt.  Daarnaast worden nieuwe dimensies die zijn toegevoegd nadat een plan voor het eerst werd gepubliceerd, weer gegeven als ' niet ingeschakeld ' in het al gepubliceerde abonnement.  Een uitgeschakelde dimensie wordt niet weer gegeven in een lijst met dimensies voor een plan dat door klanten wordt gezien.
* **Oneindig**, vertegenwoordigd door het oneindigheids teken "∞", geeft aan dat dit plan deelneemt aan deze dimensie, maar geen gebruik te maken van deze dimensie.  Als u aan uw klanten wilt aangeven dat de functionaliteit die wordt vertegenwoordigd door deze dimensie, in het plan is opgenomen, maar zonder limiet voor gebruik.  Een dimensie met oneindig gebruik wordt weer gegeven in een lijst met dimensies voor een plan dat door klanten wordt gezien, met een indicatie dat er nooit kosten in rekening worden gebracht voor dit abonnement.

>[!Note] 
>De volgende scenario's worden expliciet ondersteund: <br> -U kunt een nieuwe dimensie toevoegen aan een nieuw plan.  De nieuwe dimensie wordt niet ingeschakeld voor al gepubliceerde plannen. <br> -U kunt een schema voor **vaste kosten** publiceren zonder dimensies, vervolgens een nieuw plan toevoegen en een nieuwe dimensie voor dat plan configureren. De nieuwe dimensie wordt niet ingeschakeld voor al gepubliceerde plannen.

## <a name="constraints"></a>Beperkingen

### <a name="trial-behavior"></a>Proef gedrag

Facturering met data limieten met Marketplace-meet service is niet compatibel met een gratis proef versie.  Het is niet mogelijk om een abonnement te configureren voor het gebruik van zowel een gefactureerde facturering als een gratis proef versie.

### <a name="locking-behavior"></a>Vergrendelings gedrag

Omdat een dimensie die wordt gebruikt met de Marketplace-meet service, een goed beeld vormt van de manier waarop een klant betaalt voor de service, kunnen alle Details voor een dimensie niet meer worden gewijzigd nadat u deze hebt gepubliceerd.  Het is belang rijk dat u uw dimensies volledig hebt gedefinieerd voor een plan voordat u publiceert.
  
Zodra een aanbieding is gepubliceerd met een dimensie, kunnen de details van het aanbod niveau voor die dimensie niet meer worden gewijzigd:

* ID
* Name
* Maateenheid

Zodra een plan is gepubliceerd, kunnen de details op plan niveau niet meer worden gewijzigd:

* Prijs per eenheid
* Inbegrepen hoeveelheid voor maandelijkse periode
* Inbegrepen hoeveelheid voor jaar periode
* Hiermee wordt aangegeven of de dimensie is ingeschakeld voor het plan

### <a name="upper-limits"></a>Bovengrens

Het maximum aantal dimensies dat kan worden geconfigureerd voor een enkele aanbieding is 18 unieke dimensies.

## <a name="get-support"></a>Ondersteuning krijgen

Als u een van de volgende hebt, kunt u een ondersteunings ticket openen.

* Technische problemen met Marketplace meter Service-API.
* Een probleem dat moet worden geëscaleerd vanwege een fout of bug aan de zijkant (bijvoorbeeld verkeerde gebruiks gebeurtenis).
* Alle andere problemen met betrekking tot facturering met data limiet. 

Volg de onderstaande stappen om uw ondersteunings ticket in te dienen:

1. Ga naar de [ondersteunings pagina](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). De eerste paar vervolg keuzemenu's worden automatisch ingevuld. Voor Marketplace-ondersteuning identificeert u de product familie als **Cloud en Online Services**, het product als **Marketplace-Uitgever**.  Wijzig de vooraf gevulde vervolg menu selecties niet.
2. Selecteer **Live offer Management**onder ' Selecteer de product versie '.
3. Kies onder Selecteer een categorie die het probleem het beste beschrijft " **SaaS-apps**.
4. Selecteer in het gedeelte Selecteer een probleem dat het beste het probleem beschrijft de optie gefactureerd **Factureren**.
5. Als u de knop **volgende** selecteert, wordt u omgeleid naar de pagina met details van het **probleem** , waar u meer informatie kunt invoeren over uw probleem.

Zie [ondersteuning voor het programma voor commerciële Marketplace in het partner centrum](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support) voor meer ondersteunings opties voor Publisher.

## <a name="next-steps"></a>Volgende stappen

- Zie [api's voor Marketplace metering service](./marketplace-metering-service-apis.md) voor meer informatie.
