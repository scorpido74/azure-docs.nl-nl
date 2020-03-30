---
title: Veelgestelde vragen over internetanalyse | Microsoft Documenten
description: De veelgestelde vragen voor Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184257"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Veelgestelde vragen over Azure Internet Analyzer (voorbeeld)

Dit is de veelgestelde vragen voor Azure Internet Analyzer: als u aanvullende vragen hebt, gaat u naar het [feedbackforum](https://aka.ms/internetAnalyzerFeedbackForum) en plaatst u uw vraag. Wanneer een vraag vaak wordt gesteld, voegen we deze toe aan dit artikel, zodat deze snel en gemakkelijk kan worden gevonden.

## <a name="how-do-i-participate-in-the-preview"></a>Hoe neem ik deel aan de preview?

De preview is beschikbaar voor bepaalde klanten. Als je geïnteresseerd bent om deel te nemen aan de preview, ga je als volgt te werk:

1. Meld u aan bij [Azure Portal](https://ms.portal.azure.com).
2. Navigeer naar de pagina **Abonnementen.**
3. Klik op het Azure-abonnement waarmee u Internet Analyzer wilt gebruiken.
4. Ga naar de instellingen **resourceproviders** voor het abonnement.
5. Zoek naar **Microsoft.Network** en klik op de knop **Registreren** (of **opnieuw registreren).**
![toegangsverzoek](./media/ia-faq/request-preview-access.png)

6. [Vraag goedkeuring aan](https://aka.ms/internetAnalyzerContact) door ons uw e-mailadres en de Azure-abonnements-ID te verstrekken die is gebruikt om het toegangsverzoek in te dienen.
7. Zodra uw aanvraag is goedgekeurd, ontvangt u een bevestiging per e-mail en u internetanalysebronnen maken/bijwerken/wijzigen vanuit het nieuw toegestane Azure-abonnement.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Moet ik de client insluiten om een test uit te voeren?

Ja, de Internet Analyzer-client moet in uw toepassing worden geïnstalleerd om statistieken te verzamelen die specifiek zijn voor uw gebruikers. [Meer informatie over het installeren van de client.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Krijg ik een factuur voor deelname aan de preview?
Nee, Azure Internet Analyzer is gratis te gebruiken in preview. Er is geen service-level overeenkomst tijdens preview.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Welke scenario's is internet analyzer ontworpen om aan te pakken?

Internet Analyzer is ontworpen om u inzichten in netwerkprestaties te bieden op basis van uw gebruikerspopulatie. Om uw gebruikers de beste prestatiebeslissingen te kunnen nemen, vergelijkt Internet Analyzer de prestaties van twee interneteindpunten met behulp van uw afzonderlijke gebruikerspopulatie. Hoewel Internet Analyzer een veelheid aan vragen kan beantwoorden, zijn enkele van de meest voorkomende:

* Wat is de impact van de prestaties van migratie naar de cloud? 
    * *Aanbevolen test: aangepast (uw huidige on-premises infrastructuur) versus Azure (elk vooraf geconfigureerd eindpunt)*
* Wat is de waarde van mijn gegevens op de rand ten opzichte van een datacenter? 
    *  *Aanbevolen test: Azure vs. Azure Front Door, Azure vs. Azure CDN van Microsoft*
* Wat is het prestatievoordeel van Azure Front Door?
    *  *Aanbevolen test: Aangepaste/ Azure/ CDN vs. Azure Front Door*
* Wat is het prestatievoordeel van Azure CDN van Microsoft? 
    *  *Aanbevolen test: Aangepaste/ Azure/ AFD vs. Azure CDN van Microsoft*
* Hoe stapelt Azure CDN van Microsoft zich op? 
    *  *Aanbevolen test: aangepast (ander CDN-eindpunt) versus Azure CDN van Microsoft*
* Wat is de beste cloud voor uw eindgebruikerspopulatie in elke regio? 
    *  *Aanbevolen test: aangepaste (andere cloudservice) versus Azure (elk vooraf geconfigureerd eindpunt)*

## <a name="which-tests-can-i-run-in-preview"></a>Welke tests kan ik uitvoeren in preview?

Elke test die u maakt in Internet Analyzer bestaat uit twee eindpunten: Eindpunt A en Eindpunt B. Een van de volgende combinaties kan als test worden uitgevoerd:  
* Twee vooraf geconfigureerde eindpunten,
* Eén aangepast en één vooraf geconfigureerd eindpunt, of
* Twee [aangepaste eindpunten](internet-analyzer-custom-endpoint.md) (één aangepast eindpunt moet zich in Azure bevinden).

De volgende vooraf geconfigureerde eindpunten zijn beschikbaar tijdens de preview:
* **Azure-regio's**
    * Brazilië - zuid
    * India - centraal
    * VS - centraal
    * Azië - oost
    * VS - oost
    * Japan - west
    * Europa - noord
    * Zuid-Afrika Noord
    * Azië - zuidoost
    * VAE Noord
    * Verenigd Koninkrijk West  
    * Europa -west
    * VS - west
    * VS - west 2
* **Meerdere Azure-regiocombinaties**
    * Oost-VS, Brazilië Zuid
    * Oost-VS, Oost-Azië
    * West-Europa, Brazilië Zuid
    * West-Europa, Zuidoost-Azië
    * West-Europa, VAE Noord
    * West US, Oost-VS
    * West-VS, West-Europa
    * West US, VAE Noord
    * West-Europa, VAE Noord, Zuidoost-Azië
    * West-VS, West-Europa, Oost-Azië
    * West-VS, Noord-Europa, Zuidoost-Azië, VAE Noord, Zuid-Afrika Noord 
* **Azure + Azure Front Door** - geïmplementeerd op een enkele of meerdere Azure-regiocombinaties hierboven
* **Azure + Azure CDN van Microsoft** - geïmplementeerd op een enkele Azure-regiocombinatie die hierboven wordt vermeld
* **Azure + Azure Traffic Manager** - geïmplementeerd op een combinatie met meerdere Azure-regio's hierboven vermeld

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Waarin verschilt Internet Analyzer van andere bewakingsservices die door Azure worden geleverd?

Internet Analyzer helpt u de prestaties van uw eindgebruikers te begrijpen en helpt bij het nemen van beslissingen om hun prestaties te verbeteren. Terwijl andere Azure-bewakingstools inzicht bieden in uw Azure-services, richt Internet Analyzer zich op het meten van end-to-end internetprestaties voor uw gebruikers.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Hoe worden meetgegevens verwerkt door Internet Analyzer?

Azure heeft [sterke beveiligingsprocessen en voldoet aan een breed scala aan nalevingsnormen.](https://azure.microsoft.com/support/trust-center/) Alleen u en uw aangewezen team hebben toegang tot uw gegevens. Microsoft-medewerkers kunnen er alleen onder specifieke beperkte omstandigheden toegang toe hebben onder specifieke beperkte omstandigheden met uw medeweten. Het is versleuteld tijdens het transport en in rust.

## <a name="next-steps"></a>Volgende stappen

Zie ons Internet [Analyzer Overview](internet-analyzer-overview.md)voor meer informatie.
