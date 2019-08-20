---
title: Uitleg over beveiliging in Azure Australia
description: Informatie over de Australische regio's en voldoet aan de specifieke vereisten van het beleid voor de Australische overheid, de regelgeving en de wetgeving.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 79f5773cf6f4906f93a5b95ce5c042da09c789fc
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575462"
---
# <a name="azure-australia-security-explained"></a>Uitleg over beveiliging in Azure Australia

In dit artikel worden enkele veelgestelde vragen en gebieden van belang voor de Australische overheids instanties behandeld die onderzoeken met, ontwerpen en implementeren naar Microsoft Azure Australië.

## <a name="irap-and-certified-cloud-services-list-documents"></a>Documenten voor IRAP en gecertificeerde Cloud Services lijst

Het Australische Cyber Security Center (ACSC) biedt een verklaring van certificering, een certificerings rapport en een gebruikers handleiding voor de service wanneer deze wordt toegevoegd aan de lijst met gecertificeerde Cloud Services (CCSL).

Micro soft wordt momenteel vermeld op het CCSL voor Azure, Office 365 en Dynamics 365 CRM.

Micro soft maakt onze audit-, evaluatie-en ACSC-certificerings documenten beschikbaar voor klanten en partners op een Australia-specifieke pagina van de [micro soft service Trust-Portal](https://aka.ms/au-irap).

## <a name="dissemination-limiting-markers-and-protected-certification"></a>Beperkings markeringen voor verspreiding en beveiligde certificering

Het proces waarbij systemen, waaronder Cloud Services, worden goedgekeurd voor gebruik door overheids organisaties, wordt gedefinieerd in de [Information Security-hand leiding (ISM)](https://acsc.gov.au/infosec/ism/) die wordt geproduceerd en gepubliceerd door de ACSC. De ACSC is de entiteit binnen het Australische signalen Directoraat (ASD) dat verantwoordelijk is voor Cyber beveiliging en cloud certificering.

Er zijn twee stappen voor het goedkeurings proces:

1. **Beveiligings beoordeling (IRAP)** : Een proces waarbij geregistreerde professionals systemen, services en oplossingen beoordelen tegen de technische controles in de ISM en evalueren of de besturings elementen effectief zijn geïmplementeerd. De evaluatie identificeert ook eventuele specifieke Risico's voor de goedkeurings instantie om te overwegen voordat een goed keuring voor werk wordt verleend (ATO).

1. **Goed keuring voor de exploitatie**: Het proces waarbij een anciënniteit van een overheids instantie formeel het residuele risico van een systeem erkent en aanvaardt voor de informatie die IT verwerkt, opslaat en communiceert. Een invoer voor dit proces is de beveiligings beoordeling.

Bij de evaluatie van Azure-Services op het beveiligde niveau wordt vastgesteld dat de implementatie van de beveiligings controles die vereist zijn voor het opslaan en verwerken van beveiligde gegevens en onder meer, werd bevestigd en effectief werkt.

## <a name="australian-data-classification-changes"></a>Wijzigingen in Australische gegevens classificatie

Op 1 oktober 2018 is de afdeling van de algemene advocaat in het openbaar aangekondigde wijzigingen in het PSPF (Protect Security Policy Framework), met name een nieuw [vertrouwelijk en geclassificeerd informatie systeem](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx).

![Herziene PSPF-classificaties](media/pspf-classifications.png)

Alle Australische instanties en organisaties die onder de PSPF functioneren, worden beïnvloed door deze wijzigingen. De primaire wijziging die van invloed is op onze huidige IRAP/CCSL-certificeringen is dat de huidige verspreidings beperkings markeringen (DLMs) buiten gebruik zijn gesteld. De officiële: De gevoelige markering vervangt de verschillende DLMs die worden gebruikt voor de beveiliging van gevoelige informatie. De wijziging heeft ook drie gegevens beheer markeringen geïntroduceerd die kunnen worden toegepast op alle officiële informatie op alle niveaus van gevoeligheid en classificatie. De beveiligde classificatie blijft ongewijzigd.

De term ' niet-ingedeeld ' wordt verwijderd uit het nieuwe systeem en de term ' onofficieel ' wordt toegepast op niet-overheids informatie, hoewel er geen formele markering nodig is.

## <a name="choose-an-azure-region-for-official-sensitive-and-protected-workloads"></a>Kies een Azure-regio voor ambtenaar: Gevoelige en beveiligde werk belastingen

De Azure-ambtenaar: Gevoelige en beveiligde gecertificeerde services worden geïmplementeerd in alle vier de Australische Data Center regio's: Australië-oost, Australië-Zuid-Oost, Australië-centraal en Australië-centraal 2. Het certificerings rapport dat is uitgegeven door de ACSC, raadt aan dat beveiligde gegevens worden geïmplementeerd in de Azure Government regio's in Canberra als er een service beschikbaar is. Zie de [pagina Australië op de service Trust-Portal](https://aka.ms/au-irap)voor meer informatie over de beveiligde gecertificeerde Azure-Services.

>[!NOTE]
>Micro soft raadt aan dat de overheids gegevens van alle sensitivities en-classificaties moeten worden geïmplementeerd in de Australië-centraal en Australië-centraal 2 regio's, omdat ze speciaal zijn ontworpen en worden geëxploiteerd voor de behoeften van de overheid.

Zie [azure Australië-centraal-regio's](https://azure.microsoft.com/global-infrastructure/australia/)voor meer informatie over de speciale aard van de Azure Australische regio's.

## <a name="how-microsoft-separates-classified-and-official-data"></a>Hoe micro soft geclassificeerde en officiële gegevens scheidt

Micro soft werkt Azure en Office 365 in Australië alsof alle gegevens gevoelig of geclassificeerd zijn, waardoor onze beveiligings controles worden uitgevoerd op die hoge balk.

De infra structuur die Azure ondersteunt, levert mogelijk gegevens van meerdere classificaties. Omdat we ervan uitgaan dat de klant gegevens worden geclassificeerd, zijn de juiste besturings elementen aanwezig. Micro soft heeft een Baseline Security-postuur aangenomen voor onze services die voldoen aan de PSPF-vereisten voor het opslaan en verwerken van beveiligde geclassificeerde informatie.

Om ervoor te zorgen dat onze klanten een Tenant in azure geen risico lopen van andere tenants, implementeert micro soft uitgebreide, geavanceerde besturings elementen.

Naast de mogelijkheden die binnen het Microsoft Azure platform zijn geïmplementeerd, kunnen extra, door de klant geconfigureerde besturings elementen, zoals versleuteling met door de klant beheerde sleutels, geneste virtualisatie en just-in-time-beheer toegang, het risico verder verminderen. Binnen de Azure Government Australia-regio's in Canberra is een proces voor formele white list alleen van de Australische en Nieuw-Zeelandse overheid en de nationale organisaties voor kritieke infra structuur aanwezig. Deze community-Cloud biedt extra zekerheid voor organisaties die gevoelig zijn voor cotenant Risico's.

In het Microsoft Azure beveiligde certificerings rapport wordt bevestigd dat deze besturings elementen effectief zijn voor de opslag en verwerking van beveiligde geclassificeerde gegevens en hun isolatie.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Relevantie van IRAP/CCSL voor overheids-en kritieke infrastructuur providers

Veel overheid-en kritieke infrastructuur providers bevatten de vereisten van de federale overheid in hun beveiligings beleid en Assurance-Framework. Deze organisaties behandelen ook officiële, officiële: Gevoelig, en enige hoeveelheid beveiligde geclassificeerde gegevens, hetzij van hun interactie met de federale overheid als in hun eigen recht.

De Australische overheid richt zich steeds meer op het beleid en de wetgeving inzake de bescherming van niet-overheids gegevens die van invloed zijn op de veiligheid en economische welvaart van Australië. Als zodanig zijn de Azure Australia-regio's en de CCSL-certificering relevant voor al deze branches.

![Kritieke infrastructuur sectoren](media/nci-sectors.png)

In de micro soft-certificeringen wordt gedemonstreerd dat Azure-Services zijn onderworpen aan een grondige, strikte en formele beoordeling van de beveiligings beveiligingen en ze zijn goedgekeurd voor het afhandelen van dergelijke zeer gevoelige gegevens.

## <a name="location-and-control-of-microsoft-data-centres"></a>Locatie en controle van micro soft-data centers

Het is een verplichte vereiste van de overheid en kritieke infra structuur om expliciet de locatie en het eigendom van het Data Center te weten voor Cloud Services die hun gegevens verwerken. Micro soft is uniek als een grootschalige-Cloud provider in uitgebreide informatie over deze locaties en eigendom.

De Azure Australia-regio's (Australië-centraal en Australië-centraal 2) van micro soft kunnen worden gebruikt binnen de functies van CDC data centers. Het eigendom van CDC data centers is Australisch beheerd met 48% eigendom van het Gemenebest superannuation Corporation, 48% eigendom van Infratil (een Nieuw-Zeeland-, Dual-Australië-en Nieuw-Zeelandse infra structuur op lange termijn activa Fonds) en 4% Australische beheer. 

Het beheer van CDC data centers heeft contractuele garanties met de Australische overheid die de toekomstige overdracht van eigendom en controle beperken. Deze transparantie van de toeleverings keten en het eigendom via het partnerschap van micro soft met CDC data centers is in overeenstemming met de principes van de architectuur strategie van de [hele overheid](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) en de definitie van een gecertificeerd soeverein Data Center.

## <a name="azure-services-that-are-included-in-the-current-ccsl-certification"></a>Azure-Services die zijn opgenomen in de huidige CCSL-certificering

In juni 2017 is de ACSC Certified 41 Azure-Services voor de opslag en verwerking van gegevens op de niet-geclassificeerde: Niveau DLM. In april 2018 is 24 van deze services gecertificeerd voor beveiligde geclassificeerde gegevens.

De beschik baarheid van ACSC-gecertificeerde Azure-Services in onze Azure-regio's in Australië is als volgt (de services die vet worden weer gegeven, zijn gecertificeerd op het beveiligde niveau).

|Azure-Australië-centraal regio's|Niet-regionale Services en andere regio's|
|---|---|
|API Management, app-gateway, Toepassingsservices, **automatisering**, **Azure Portal**, **back-up**, **batch**, **Cloud Services**, Cosmos DB, Event hubs, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, logboek Analyse, **multi-factor Authentication**, redis cache, **Resource Manager**, **Service Bus**, **service Fabric**, **site Recovery**, **SQL database**, **opslag**, Traffic Manager, **virtueel Machines**, **Virtual Network**, **VPN gateway**|**Azure Active Directory**, CDN, Data Catalog, **importeren exporteren**, **Information Protection**, **IOT hub**, machine learning, Media Services, **Notification hubs**, Power bi, **scheduler**, **Security Center**, Zoeken, Stream Analytics|
|

Micro soft publiceert het [overzicht van Microsoft Azure naleving](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) waarin alle services binnen het bereik worden vermeld voor alle wereld wijde, overheids-, industrie-en regionale nalevings-en evaluatie processen die door Azure worden door gegeven, waaronder IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-needed"></a>De Azure-service wordt niet vermeld of beoordeeld op een lager niveau dan nodig is

Services die niet zijn gecertificeerd of die zijn gecertificeerd voor de officiële: Gevoelig, maar niet het beveiligde niveau, kan worden gebruikt naast of als onderdeel van een oplossing die beveiligde gegevens host, mits de services:

- Niet-versleutelde beveiligde gegevens worden niet opgeslagen of verwerkt
- U hebt een risico analyse voltooid en de service goedgekeurd om zelf beveiligde gegevens op te slaan.

U kunt een service gebruiken die niet is opgenomen in de CCSL voor het opslaan en verwerken van officiële gegevens, maar de ISM vereist dat u de ACSC schriftelijk op de hoogte stelt voordat u een contract met een Cloud serviceprovider invoert of verlengt.

Elke service die wordt gebruikt door een Bureau voor beveiligde werk belastingen moet worden beoordeeld en goedgekeurd op basis van de processen die worden beschreven in de ISM en het door de instantie beheerde IRAP-beoordelings proces in de [DTA Secure Cloud-strategie](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf).

![DTA veilig cloud strategie certificerings proces](media/certification.png)

Micro soft evalueert voortdurend onze services om ervoor te zorgen dat het platform veilig en passend is voor gebruik door de Australische overheid. Neem contact op met micro soft als u hulp nodig hebt bij een service die momenteel niet op het beveiligde niveau is CCSL.

Omdat micro soft een scala aan services heeft die zijn gecertificeerd voor de CCSL van zowel de niet-geclassificeerde DLM als beveiligde classificaties, vereist de ISM dat er ten minste om de twee jaar een IRAP-evaluatie van onze services wordt verricht. Micro soft onderneemt een jaarlijkse evaluatie, die ook de mogelijkheid biedt om extra services op te nemen voor overweging.

## <a name="certified-protected-gateway-in-azure"></a>Gecertificeerde beveiligde gateway in azure

Micro soft gebruikt geen door de overheid gecertificeerde, beveiligde Internet gateway (SIG) vanwege beperkingen op het aantal SIGs dat is toegestaan in het gateway consolidatie programma. Maar de verwachte en benodigde mogelijkheden van een SIG kunnen worden geconfigureerd in Microsoft Azure.

De ACSC heeft via de beveiligde certificering van Azure-Services specifieke aanbevelingen voor het maken van verbinding met Azure en bij het implementeren van netwerk segmentatie tussen beveiligings domeinen, bijvoorbeeld tussen beveiligde en Internet. Deze aanbevelingen omvatten het gebruik van netwerk beveiligings groepen, firewalls en virtuele particuliere netwerken. De ACSC raadt aan het gebruik van een virtuele gateway apparaat te gebruiken. Er zijn verschillende virtuele apparaten beschikbaar in azure die een fysiek equivalent hebben op de lijst met ASD-producten of die zijn geëvalueerd op basis van de gemeen schappelijke criteria voor beveiligings profielen en die worden vermeld op de common criteria Portal. Deze producten worden wederzijds door ASD erkend als een ondertekenende ondertekenaar van de algemene regeling voor criterium herkenning.

Micro soft heeft richt lijnen opgesteld voor het implementeren van op Azure gebaseerde mogelijkheden die de beveiligings functies bieden die vereist zijn voor het beveiligen van de grens tussen verschillende beveiligings domeinen, die in combi natie met een gecertificeerde SIG overeenkomen. Een aantal partners kan helpen bij het ontwerpen en implementeren van deze mogelijkheden en er zijn een aantal partner oplossingen beschikbaar die hetzelfde doen.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Beveiligings machtigingen en het burgerschap van mede werkers van micro soft-ondersteuning

Micro soft werkt onze services wereld wijd met geschermde en getraind beveiligings personeel. Mede werkers die fysieke toegang hebben tot faciliteiten in Sydney en Melbourne, hebben de beveiligings machtigingen voor de Australische overheid. Mede werkers binnen het Australië-centraal en Australië-centraal 2 regio's hebben mini maal negatieve hebben 1 (NV1) (afhankelijk van geheime gegevens). Deze vereisten voor de goed keuring bieden klanten extra zekerheid dat mede werkers binnen data centers van Azure zeer betrouwbaar zijn.

Micro soft heeft een permanent toegangs beleid met toegang verleend via een systeem van just-in-time en net genoeg beheer op basis van op rollen gebaseerd toegangs beheer. In de meeste gevallen hebben onze beheerders geen toegang of privileges voor klant gegevens nodig om de service te kunnen oplossen en te onderhouden. Dankzij de hoge mate van automatisering en het uitvoeren van scripts voor externe uitvoering wordt de nood zaak van rechtstreekse toegang tot klant gegevens door gegeven.

De afdeling van de algemene advocaat heeft bevestigd dat het beveiligings beleid en de procedures van het personeel van micro soft in azure consistent zijn met het doel van de PSPF toegang tot informatie voorzieningen in INFOSEC-9.

## <a name="store-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>Internationaal verkeer van wapens (ITAR) of gegevens over export voorschriften (OORMERK) opslaan

De technische controles van Azure die klanten helpen bij het voldoen aan hun verplichtingen voor het exporteren van gegevens zijn hetzelfde wereld wijd in Azure. Belang rijk: er is geen formeel beoordelings-en certificerings raamwerk voor gegevens die met exporteren worden beheerd.

Voor Azure Government en Office 365 US Government voor verdediging hebben we aanvullende contractuele en verwerkings maatregelen getroffen ter ondersteuning van klanten die onderworpen zijn aan export besturings elementen. Deze aanvullende contract bepalingen en de gegarandeerde nationale ondersteuning voor de Verenigde Staten en het beheer van de Azure-regio's zijn niet voor Australië van toepassing.

Dat betekent niet dat Azure in Australië niet kan worden gebruikt voor ITAR/oor, maar u moet de beperkingen die u via uw export licentie hebt opgelegd, duidelijk begrijpen. U moet ook aanvullende beveiligingen implementeren om te voldoen aan die verplichtingen voordat u Azure gebruikt om die gegevens op te slaan. U moet bijvoorbeeld het volgende doen:

- Bouw nationaliteit als een kenmerk in Azure Active Directory.
- Gebruik Azure Information Protection om versleutelings regels voor de gegevens af te dwingen en deze te beperken tot alleen de Verenigde Staten en andere nationaliteiten die zijn opgenomen in de export licentie.
- Versleutel alle gegevens on-premises voordat u deze opslaat in azure met behulp van een klant sleutel of houd uw eigen sleutel voor ITAR-gegevens.

Omdat ITAR geen formele certificering is, moet u weten wat de beperkingen en beperkingen zijn die aan de export licentie zijn gekoppeld. Vervolgens kunt u nagaan of er voldoende besturings elementen in azure zijn om aan deze vereisten te voldoen. In dit geval is een van de problemen met het nadenken van de toegang door onze technici die mogelijk geen nationaliteit hebben goedgekeurd op de export licentie.

## <a name="next-steps"></a>Volgende stappen

 Zie [Azure VPN gateway](vpn-gateway.md)voor de ISM-compatibele configuratie en implementatie van VPN-verbinding met Azure Australia.
