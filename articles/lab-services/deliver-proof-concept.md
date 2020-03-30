---
title: Een proof of concept leveren - Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het leveren van een proof of concept, zodat Azure DevTest Labs met succes kan worden opgenomen in een bedrijfsomgeving.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: ca843213760cee60799568a6f33059c2bd91c835
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75643284"
---
# <a name="deliver-a-proof-of-concept"></a>Lever een proof of concept 

Een van de belangrijkste scenario's voor Azure DevTest Labs is het inschakelen van ontwikkel- en testomgevingen in de cloud. Voorbeelden zijn:

* Ontwikkelaarsdesktops maken in de cloud.
* Omgevingen configureren voor testen.
* Toegang tot virtuele machines en andere Azure-bronnen inschakelen.
* Het instellen van een sandbox-gebied voor ontwikkelaars om te leren en te experimenteren.

DevTest Labs biedt ook de beleidsvangrails en kostenbeheersing om de onderneming in staat te stellen "self-serve Azure" te bieden aan ontwikkelaars die zich houden aan het beleid inzake bedrijfsbeveiliging, regelgeving en naleving. 

Elke onderneming heeft andere vereisten voor hoe Azure DevTest Labs met succes in hun omgeving kan worden opgenomen. In dit artikel worden de meest voorkomende stappen beschreven die ondernemingen moeten voltooien om een succesvolle proof of concept te garanderen. Een proof of concept is de eerste stap naar een succesvolle end-to-end implementatie. 

## <a name="getting-started"></a>Aan de slag 

Om aan de slag te gaan met het leveren van een proof of concept. Het is belangrijk om wat tijd door te brengen om meer te weten te komen over Azure en DevTest Labs.  Hier volgen enkele startbronnen: 

* [De Azure-portal begrijpen](https://azure.microsoft.com/features/azure-portal/)
* [Basisprincipes van DevTest Labs](devtest-lab-overview.md)
* [DevTest Labs ondersteunde scenario's](devtest-lab-guidance-get-started.md)
* [Bedrijfsdocumentatie van DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Intro voor Azure-netwerken](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Vereisten 

Om een pilot of proof of concept met DevTest Labs succesvol te voltooien, zijn er een paar voorwaarden: 

* **Azure-abonnement**: Ondernemingen hebben vaak een bestaande [Enterprise-overeenkomst](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) die toegang tot Azure mogelijk maakt en ze kunnen een bestaand of nieuw abonnement gebruiken voor DevTest Labs. Als alternatief kunnen ondernemingen tijdens de pilot een [Visual Studio-abonnement](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) gebruiken (gebruikmakend van de gratis Azure-credits). Als geen van deze opties beschikbaar is, kan een onderneming een [gratis Azure-account](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)maken en gebruiken. Als er een Enterprise Agreement is gesloten, is het gebruik van een [Enterprise Dev/Test-abonnement](https://azure.microsoft.com/offers/ms-azr-0148p/) een geweldige optie om toegang te krijgen tot Windows 10/Windows 8.1-clientbesturingssystemen en gereduceerde tarieven voor het ontwikkelen en testen van workloads. 
* **Azure Active Directory-tenant**: Als u beheergebruikers wilt inschakelen (bijvoorbeeld gebruikers toevoegen of labeigenaren wilt toevoegen), moeten deze gebruikers deel uitmaken van de [Azure Active Directory-tenant](https://azure.microsoft.com/services/active-directory/) die wordt gebruikt in het Azure-abonnement voor de pilot. Vaak zullen ondernemingen [hybride identiteit](../active-directory/hybrid/whatis-hybrid-identity.md) instellen om gebruikers in staat te stellen hun on-premises identiteit in de cloud te gebruiken, maar dit is niet vereist voor de DevTest Labs-pilot. 

## <a name="scoping-of-the-pilot"></a>Scoping van de piloot 

Het is belangrijk om een pilot te plannen voordat u met de implementatie begint. Weten op voorhand dat de middelen niet zal blijven rond voor onbepaalde tijd stelt passende verwachtingen voor de gebruikers van de piloot. 

> [!IMPORTANT]
> We kunnen niet genoeg benadrukken het belang van scherp scoping de piloot en het instellen van verwachtingen op voorhand.

Beantwoord deze belangrijke vragen voordat u de pilot aftrapt: 

* Wat wil je leren en hoe ziet succes eruit voor de piloot? 
* Welke workloads of scenario's komen aan bod in de pilot? Het is belangrijk om slechts een kleine set te definiëren om ervoor te zorgen dat de pilot snel kan worden uitgevoerd en voltooid. 
* Welke middelen moeten beschikbaar zijn in het lab? Bijvoorbeeld: aangepaste afbeeldingen, marktplaatsafbeeldingen, beleid, netwerktopologie. 
* Wie zijn de gebruikers en teams die betrokken zullen zijn bij de pilot om de ervaring te verifiëren?  
* Wat is de duur van de pilot? Kies een tijdsbestek dat goed uitlijnt naar het geplande bereik, zoals twee weken of een maand. 
* Wat gebeurt er na de pilot met de toegewezen middelen die tijdens de pilot zijn gebruikt? Bent u van plan om de piloot middelen te verwijderen? Je zou kunnen denken:
   
   "Als we kunnen plannen op het weggooien van de virtuele machines en laboratoria aan het einde van de pilot, dan kunnen we het opzetten van een abonnement voor de piloot en al ons werk daar doen, terwijl we de schaal uitrol vragen op te lossen in parallel." 

Er is een algemene neiging om de piloot "perfect" te maken, zodat het identiek vertegenwoordigt wat de uiteindelijke staat zal zijn nadat de service is uitgerold bij het bedrijf. Dit is een verkeerde veronderstelling. Hoe dichter je bij "perfect" komt, hoe meer je moet voltooien *voordat* je aan de slag gaat met de piloot. Het doel van de pilot is om de juiste beslissingen te nemen over het opschalen en uitrollen van de uiteindelijke dienst. 

De focus van de pilot moet liggen op het kiezen van de minimaal noodzakelijke workloads en afhankelijkheden om antwoord te geven op de vraag of Azure DevTest Labs de juiste service voor uw onderneming is. We raden u aan de eenvoudigste workload met de minste afhankelijkheden te kiezen om een snel en schoon succes te garanderen. Als dat niet mogelijk is, kiest u een meest representatieve werkbelasting die potentiële complexiteiten blootlegt, zodat succes in de pilotfase kan worden gerepliceerd in de schaalfase. 

Het volgende voorbeeld toont een goed scoped proof of concept. 

## <a name="example-proof-of-concept-plan"></a>Voorbeeld: proof-of-concept plan 

Deze sectie toont een monster te gebruiken voor het scoping een proof of concept van de pilot voor DevTest Labs. 

> [!TIP]
> Om de mogelijkheid van het instellen van uw project voor mislukte problemen te minimaliseren, raden we u ten zeerste aan het voorbeeld dat in deze sectie wordt beschreven, niet over te slaan. 

### <a name="overview"></a>Overzicht 

We zijn van plan om een nieuwe omgeving in Azure te ontwikkelen op basis van DevTest Labs voor leveranciers om te gebruiken als een geïsoleerde omgeving van het bedrijfsnetwerk. Om te bepalen of de oplossing aan de eisen voldoet, ontwikkelen we een proof of concept om de end-to-end oplossing te valideren. We hebben verschillende leveranciers opgenomen om de ervaring uit te proberen en te verifiëren. 

### <a name="outcomes"></a>Resultaten 

Bij het bouwen van een proof of concept richten we ons eerst op de resultaten (wat proberen we te bereiken). Tegen het einde van de proof of concept verwachten we: 

* Een werkende end-to-end-oplossing voor leveranciers om gastaccounts in Azure Active Directory (Azure AD) te gebruiken om toegang te krijgen tot een geïsoleerde omgeving in Azure. Het milieu beschikt over de middelen die nodig zijn om productief te zijn. 
* Eventuele blokkeringsproblemen die van invloed zijn op het gebruik en de adoptie op een bredere schaal, worden opgesomd en begrepen.
* De personen die betrokken zijn bij de ontwikkeling van de proof of concept hebben een goed begrip van alle code. Ze begrijpen ook onderpand en hebben vertrouwen in een bredere adoptie.

### <a name="open-questions-and-prerequisites"></a>Open vragen en vereisten 

* Hebben we een abonnement gemaakt dat we kunnen gebruiken voor dit project? 
* Hebben we een Azure AD-tenant en een Azure AD-globale beheerder die kan helpen en begeleiden voor Azure AD-gerelateerde vragen? 
* Hebben we een plek om samen te werken voor de personen die aan het project werken? 

   * Broncode en scripts (zoals Azure Repos) 
   * Documenten (zoals Microsoft Teams of SharePoint)  
   * Gesprekken (zoals Microsoft Teams) 
   * Werkitems (zoals Azure Boards) 
* Wat zijn de vereiste resources voor leveranciers? Dit omvat toepassingen die beschikbaar zijn op het netwerk, zowel lokaal op de virtuele machines als op andere vereiste servers. 
* Worden de virtuele machines samengevoegd met een domein in Azure? Zo ja, wordt dit Azure Active Directory Domain Services (Azure AD DS) of iets anders? 
* Hebben we het team of leveranciers geïdentificeerd dat het doelwit van de proof of concept zal zijn? Wie zijn de klanten voor het milieu?
* Welke Azure-regio gebruiken we voor de proof of concept? 
* Hebben we een lijst van diensten die de leveranciers mogen gebruiken via DevTest Labs naast IaaS (VM's)? 
* Hoe zijn we van plan om verkopers / gebruikers te trainen op het gebruik van het lab? 

### <a name="components-of-the-proof-of-concept-solution"></a>Componenten van de proof-of-concept oplossing 

We verwachten dat de oplossing de volgende componenten zal hebben: 

* Verschillende leveranciersteams zullen een reeks labs in Azure gebruiken.
* De labs zijn verbonden met een netwerkinfrastructuur die de vereisten ondersteunt.
* De leveranciers hebben toegang tot de labs via Azure AD en roltoewijzingen.
* Leveranciers hebben een manier om succesvol verbinding te maken met hun resources. Met name een site-to-site VPN maakt het mogelijk om rechtstreeks toegang te krijgen tot virtuele machines zonder openbare IP-adressen.
* Een reeks artefacten dekt de vereiste software die de leveranciers nodig hebben op de virtuele machines.

## <a name="additional-planning-and-design-decisions"></a>Aanvullende plannings- en ontwerpbeslissingen 

Voordat u een volledige DevTest Labs-oplossing uitbrengt, moet u een aantal belangrijke plannings- en ontwerpbeslissingen nemen. De ervaring van het werken aan een proof of concept kan u helpen deze beslissingen te nemen. Verdere overweging omvat: 

* **Abonnementstopologie**: De vereisten op ondernemingsniveau voor resources in Azure kunnen verder reiken dan de [beschikbare quota binnen één abonnement](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Dit vereist meerdere Azure-abonnementen en/of serviceaanvragen om de initiële abonnementslimieten te verhogen. Het is belangrijk om vooraf te beslissen hoe resources over abonnementen kunnen worden verdeeld. Een waardevolle bron is de [abonnementsbeslissingshandleiding](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) omdat het moeilijk is om resources later naar een ander abonnement te verplaatsen. Een lab kan bijvoorbeeld niet worden verplaatst naar een ander abonnement nadat het is gemaakt.  
* **Netwerktopologie:** de [standaardnetwerkinfrastructuur](../app-service/networking-features.md) die DevTest Labs automatisch maakt, is mogelijk niet voldoende om te voldoen aan de vereisten en beperkingen voor de bedrijfsgebruikers. Het is gebruikelijk om te zien [Azure ExpressRoute verbonden virtuele netwerken,](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) [hub-and-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) voor connectiviteit tussen abonnementen, en zelfs [gedwongen routing](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) om on-premises connectiviteit alleen te garanderen. Met DevTest Labs kunnen bestaande virtuele netwerken worden aangesloten op het lab om het gebruik ervan mogelijk te maken wanneer u nieuwe virtuele machines in het lab maakt. 
* **Externe toegang tot virtuele machines**: Er zijn veel opties om op afstand toegang te krijgen tot de virtuele machines in DevTest Labs. Het gemakkelijkst is om openbare IP's of gedeelde openbare IP-ip's te gebruiken. Dit zijn [instellingen beschikbaar in het lab.](devtest-lab-shared-ip.md) Als deze opties niet voldoende zijn, is het gebruik van een externe toegangsgateway ook een optie. Deze optie wordt weergegeven op de [devTest Labs enterprise referentie architectuur](devtest-lab-reference-architecture.md) en verder beschreven in de [DevTest Labs remote desktop gateway documentatie.](configure-lab-remote-desktop-gateway.md) Bedrijven kunnen expressroute of een site-to-site VPN ook gebruiken om hun labs te verbinden met hun on-premises netwerk. Deze optie maakt directe remote desktop of SSH verbindingen met de virtuele machines op basis van hun prive-IP-adres zonder blootstelling aan het internet. 
* **Machtigingen voor het verwerken:** de twee belangrijkste machtigingen die vaak worden gebruikt in DevTest Labs zijn [eigenaar en labgebruiker.](devtest-lab-add-devtest-user.md) Het is belangrijk om te beslissen voordat de uitrol van DevTest Labs in grote lijnen wie zal worden toevertrouwd met elk niveau van toegang in het lab. Een gemeenschappelijk model is de budgeteigenaar (bijvoorbeeld teamlead) als de eigenaar van het lab en de teamleden als labgebruikers. Dit model stelt de persoon (teamleider) die verantwoordelijk is voor het budget in staat om de beleidsinstellingen aan te passen en het team binnen het budget te houden.  

## <a name="completing-the-proof-of-concept"></a>Het invullen van de proof of concept 

Nadat de verwachte lessen zijn behandeld, is het tijd om de pilot te voltooien. Dit is het moment om feedback van de gebruikers te verzamelen, te bepalen of de pilot succesvol was en te beslissen of de organisatie verder gaat met een schaalimplementatie van DevTest Labs in de onderneming. Het is ook een goed moment om te overwegen de implementatie van DevTest Labs en bijbehorende resources te automatiseren om consistentie te garanderen tijdens de schaalimplementatie. 

## <a name="next-steps"></a>Volgende stappen 

* [Bedrijfsdocumentatie van DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Referentiearchitectuur voor een onderneming](devtest-lab-reference-architecture.md)
* [Uw DevTest Labs-implementatie opschalen](devtest-lab-guidance-orchestrate-implementation.md)
* [De implementatie van Azure DevTest Labs orkestreren](devtest-lab-guidance-orchestrate-implementation.md)
