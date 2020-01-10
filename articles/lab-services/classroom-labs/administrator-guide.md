---
title: Azure Lab Services-beheerders handleiding | Microsoft Docs
description: Deze hand leiding helpt beheerders die Lab-accounts maken en beheren met behulp van Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 638a90615d248b3c2829770432dd6a08eb4bb2fb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771731"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services-beheerders handleiding
IT-beheerders die de cloud resources van een organisatie beheren, zijn doorgaans verantwoordelijk voor het instellen van het lab-account voor hun organisatie. Beheerders of docenten maken klassikale Labs in het lab-account. Dit artikel bevat een overzicht op hoog niveau van de betrokken Azure-resources en de richt lijnen voor het maken van deze.

![Weer gave op hoog niveau van Azure-resources in een Lab-account](../media/administrator-guide/high-level-view.png)

- Klassikale Labs worden gehost in een Azure-abonnement dat eigendom is van Azure Lab Services
- Lab-accounts, Galerie met gedeelde afbeeldingen en installatie kopie versies worden gehost in uw abonnement
- U kunt een Lab-account en de Shard-installatie kopie galerie hebben in dezelfde resource groep. In dit diagram bevinden ze zich in verschillende resource groepen. 

## <a name="subscription"></a>Abonnement
Uw organisatie heeft een of meer Azure-abonnementen. Een abonnement wordt gebruikt voor het beheren van facturering en beveiliging voor alle Azure-resources\services die erin worden gebruikt, met inbegrip van Lab-accounts.

De relatie tussen een Lab-account en het abonnement is belang rijk omdat:

- Facturering wordt gerapporteerd via het abonnement dat het lab-account bevat.
- U kunt gebruikers in de Azure Active Directory (AD)-Tenant die is gekoppeld aan het abonnement, de toegang geven tot Azure Lab Services. U kunt de gebruiker toevoegen als een Lab-account owner\contributor of als maker van een leslokaal.

Klassikale Labs en hun virtuele machines (Vm's) worden volledig voor u beheerd. Ze worden specifiek gehost in een speciaal abonnement dat eigendom is van Azure Lab Services.

## <a name="resource-group"></a>Resourcegroep
Een abonnement bevat een of meer resource groepen. Resource groepen worden gebruikt voor het maken van logische groeperingen van Azure-resources die samen worden gebruikt binnen dezelfde oplossing.  

Wanneer u een Lab-account maakt, moet u de resource groep configureren die het lab-account bevat. 

Een resource groep is ook vereist bij het maken van een [Galerie met gedeelde installatie kopieën](#shared-image-gallery). U kunt ervoor kiezen om uw Lab-account en de galerie met gedeelde afbeeldingen in twee afzonderlijke resource groepen te plaatsen, wat gebruikelijk is als u van plan bent de galerie met installatie kopieën te delen in verschillende oplossingen. U kunt er ook voor kiezen om deze in dezelfde resource groep te plaatsen.

Wanneer u een Lab-account maakt en tegelijkertijd automatisch een gedeelde installatie kopie galerie maakt en koppelt, worden het lab-account en de galerie met gedeelde afbeeldingen standaard in afzonderlijke resource groepen gemaakt. U ziet dit gedrag wanneer u de stappen in deze zelf studie gebruikt: de [Galerie gedeelde afbeeldingen configureren op het moment dat het account wordt gemaakt](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). In de afbeelding boven aan dit artikel wordt deze configuratie ook gebruikt. 

U wordt aangeraden tijd vooraf in te zetten om de structuur van uw resource groepen te plannen, omdat het niet mogelijk is om de resource groep van een Lab-account of de gedeelde installatie kopie galerie te wijzigen nadat deze is gemaakt. Als u de resource groep voor deze resources moet wijzigen, moet u de en/of-galerie met gedeelde afbeeldingen van uw Lab-account verwijderen en opnieuw maken.

## <a name="lab-account"></a>Lab-account
Een Lab-account fungeert als een container voor een of meer klassikale Labs. Wanneer u aan de slag gaat met Azure Lab Services, is het gebruikelijk dat u slechts één Lab-account hebt. U kunt later meer Lab-accounts maken naarmate uw test gebruik wordt geschaald.

De volgende lijst geeft een overzicht van scenario's waarin meer dan één Lab-account nuttig kan zijn:

- **Verschillende beleids vereisten in klassikale Labs beheren** 

    Wanneer u een Lab-account instelt, stelt u beleids regels in die van toepassing zijn op alle klassikale Labs onder het lab-account, zoals:
    - Het virtuele Azure-netwerk met gedeelde bronnen waartoe het leslokaal Lab toegang heeft. Zo kunt u bijvoorbeeld een set klassikale Labs hebben die toegang nodig heeft tot een gedeelde gegevensset in een virtueel netwerk.
    - De virtuele machine (VM)-installatie kopieën die de klassikale Labs kunnen gebruiken om Vm's te maken. U hebt bijvoorbeeld een set klassikale Labs die toegang nodig heeft tot de [Data Science VM voor Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) Marketplace-installatie kopie.  

    Als u klassikale Labs hebt die unieke beleids vereisten van elkaar hebben, kan het nuttig zijn om afzonderlijke Lab-accounts te maken om deze klassikale Labs afzonderlijk te beheren.
- **De toegang van Lab-makers beperken tot specifieke klassikale Labs**  

    Wanneer een gebruiker wordt toegevoegd als een Lab-Maker, krijgen ze toegang tot alle klassikale Labs binnen het lab-account, met inbegrip van de laboratoria die zijn gemaakt door andere Lab-makers. U kunt Lab-makers voor het beheren van specifieke Labs beperken door afzonderlijke Lab-accounts te maken om het bereik van hun toegang te beperken. U kunt bijvoorbeeld een afzonderlijke Lab-account maken voor elke afdeling in een universiteit. Bijvoorbeeld: een Lab-account voor de weten schappelijke afdeling en een andere voor de reken kundige afdeling, enzovoort.   
- **Afzonderlijk budget per Lab-account**

    In plaats van dat alle klassikale Lab-kosten voor één Lab-account moeten worden gerapporteerd, moet u mogelijk een beter, gescheiden budget hebben. Als u doorgaat met het voor beeld in het bovenstaande opsommings teken, kunt u voor elke universiteit een Lab-account maken om het budget dienovereenkomstig te scheiden. Met Azure Cost Management kunt u de kosten voor elk afzonderlijke Lab-account weer geven.
- **Prototype Labs isoleren van actieve Labs**

    Het kan voor komen dat u beleids wijzigingen wilt door lopen in een Lab-account zonder dat dit invloed heeft op actieve Labs. In dit type scenario kunt u met het maken van een afzonderlijke Lab-account voor test doeleinden wijzigingen isoleren. 

## <a name="classroom-lab"></a>Klas Lab
Een leslokaal Lab bevat een of meer virtuele machines (Vm's) die elk zijn toegewezen aan een specifieke student. Over het algemeen kunt u het volgende verwachten:

- U hebt één leslokaal Lab voor elke klasse.
- Maak elk semester een nieuwe set klassikale Labs (of voor elke periode waarin uw klasse wordt aangeboden). Voor klassen met dezelfde installatie kopie moet u meestal een [Galerie met gedeelde afbeeldingen](#shared-image-gallery) gebruiken om afbeeldingen te delen in Labs en semesters.

Houd rekening met de volgende punten wanneer u bepaalt hoe u uw klassikale Labs structureert:

- **Alle vm's in een klas Lab worden geïmplementeerd met dezelfde installatie kopie die wordt gepubliceerd**. Als u een klasse hebt waarvoor verschillende Lab-installatie kopieën zijn vereist, moet u als gevolg hiervan afzonderlijke klassikale Labs voor elk abonnement maken.
- **Gebruiks quotum is ingesteld op het lab-niveau en is van toepassing op alle gebruikers in het lab**. U kunt bijvoorbeeld een set docenten hebben die toegang nodig hebben tot de Vm's van een klasse om de lessen voor te bereiden, maar de docenten vereisen alleen een quotum van tien uur wanneer studenten die zijn Inge schreven in de klasse een quotum van 40 uur vereisen. Als u verschillende quota wilt instellen voor gebruikers, moet u afzonderlijke klassikale Labs maken. Het is echter mogelijk om meer uren aan een specifieke gebruiker toe te voegen nadat u het quotum hebt ingesteld.
- **Het opstart-of afsluit schema wordt ingesteld op het lab-niveau en is van toepassing op alle virtuele machines in het lab**. Net als bij het vorige punt, moet u, als u verschillende planningen wilt instellen voor gebruikers, afzonderlijke klassikale Labs maken. 

## <a name="shared-image-gallery"></a>Galerie met gedeelde afbeeldingen
Een galerie met gedeelde installatie kopieën is gekoppeld aan een Lab-account en fungeert als een centrale opslag plaats voor het opslaan van installatie kopieën. Een afbeelding wordt in de galerie opgeslagen wanneer een docent ervoor kiest om deze op te slaan vanuit de sjabloon virtuele machine (VM) van een klas Lab. Telkens wanneer een docent wijzigingen aanbrengt in de sjabloon-VM en opslaat, worden nieuwe versies van de installatie kopie opgeslagen tijdens het behoud van eerdere versies.

Docenten kunnen een installatie kopie versie publiceren vanuit de galerie met gedeelde afbeeldingen wanneer ze een nieuw leslokaal Lab maken. Hoewel in de galerie meerdere versies van een afbeelding kunnen worden opgeslagen, kunnen docenten alleen de nieuwste versie selecteren tijdens het maken van het lab.

De galerie met gedeelde afbeeldingen is een optionele resource die u mogelijk niet direct nodig hebt bij het starten met slechts enkele klassikale lessen. Het gebruik van de galerie met gedeelde afbeeldingen heeft echter veel voor delen die handig zijn wanneer u schaalt naar meer klassikale Labs:

- **Hiermee kunt u versies van een sjabloon-VM-installatie kopie opslaan en beheren**.

    Het is handig bij het maken van een aangepaste installatie kopie of het aanbrengen van wijzigingen (software, configuratie, enzovoort) in een installatie kopie van de Public Marketplace-galerie.  Het is bijvoorbeeld gebruikelijk dat docenten verschillende software\tooling moeten installeren. In plaats van te vereisen dat cursisten hun eigen vereisten hand matig installeren, kunnen verschillende versies van de sjabloon-VM-installatie kopie worden opgeslagen in een galerie met gedeelde installatie kopieën. Deze installatie kopie versies kunnen vervolgens worden gebruikt bij het maken van nieuwe klassikale lessen.
- **Hiermee wordt sharing\reuse van sjabloon-VM-installatie kopieën in klassikale Labs ingeschakeld**.

    Zo voor komt u dat u elke keer dat u een nieuw leslokaal Lab maakt een nieuwe installatie kopie moet configureren. Als er bijvoorbeeld meerdere klassen worden aangeboden die dezelfde installatie kopie nodig hebben, hoeft deze installatie kopie slechts één keer te worden gemaakt en opgeslagen in de galerie met gedeelde afbeeldingen, zodat deze kan worden gedeeld in klassikale Labs.
- Hiermee wordt de **Beschik baarheid van afbeeldingen via replicatie gegarandeerd**.

    Wanneer u opslaat in de galerie met gedeelde afbeeldingen van een leslokaal Lab, wordt uw installatie kopie automatisch gerepliceerd naar andere regio's binnen dezelfde geografie. Als er een storing optreedt in een regio, wordt de publicatie van de sjabloon-VM in uw klas Lab niet beïnvloed door het gebruik van een afbeeldings replica in andere regio's. Daarnaast helpt het met de prestaties in scenario's voor het publiceren van meerdere VM'S door te verspreiden voor het gebruik van verschillende replica's.

U hebt een aantal opties om gedeelde installatie kopieën logisch te groeperen:

- Meerdere gemeen schappelijke afbeeldings galerieën maken. Elk lab-account kan alleen verbinding maken met één gedeelde installatie kopie galerie. Daarom moet u voor deze optie ook meerdere Lab-accounts maken.
- U kunt ook één gedeelde installatie kopie galerie gebruiken die wordt gedeeld door meerdere Lab-accounts. In dit geval kan elk lab-account alleen de installatie kopieën inschakelen die van toepassing zijn op de klassikale Labs die het bevat.

## <a name="naming"></a>Naamgeving
Wanneer u aan de slag gaat met Azure Lab Services, raden we u aan om naam conventies voor resource groepen, Lab-accounts, klassikale Labs en de galerie met gedeelde afbeeldingen te bepalen. Hoewel de naamgevings regels die u instelt, uniek zijn voor de behoeften van uw organisatie, wordt in de volgende tabel een overzicht gegeven van de algemene richt lijnen.

| Resourcetype | Rol | Voorgesteld patroon | Voorbeelden |
| ------------- | ---- | ----------------- | -------- | 
| Resourcegroep | Bevat een of meer Lab-accounts en een of meer galerie met gedeelde afbeeldingen | \<organisatie korte naam\>-\<omgeving\>-RG<ul><li>**Korte naam** van de organisatie identificeert de naam van de organisatie die de resource groep ondersteunt</li><li>**Omgeving** identificeert de omgeving voor de resource, zoals test of productie</li><li>**RG** staat voor het resource type: resource groep.</li></ul> | contosouniversitylabs-RG<br/>contosouniversitylabs-test-RG<br/>contosouniversitylabs-Prod-RG |
| Lab-account | Bevat een of meer Labs | \<organisatie korte naam\>-\<omgeving\>-La<ul><li>**Korte naam** van de organisatie identificeert de naam van de organisatie die de resource groep ondersteunt</li><li>**Omgeving** identificeert de omgeving voor de resource, zoals test of productie</li><li>**La** staat voor het resource type: Lab-account.</li></ul> | contosouniversitylabs-La<br/>mathdeptlabs-La<br/>sciencedeptlabs-test-La<br/>sciencedeptlabs-Prod-La |
| Klas Lab | Bevat een of meer virtuele machines |\<class name\>-\<tijds bestek\>-\<docent-id\><ul><li>**Klassenaam** identificeert de naam van de klasse die door het lab wordt ondersteund.</li><li>**Tijds bestek** duidt de periode aan waarin de klasse wordt aangeboden.</li>**Onderwijs-id** identificeert de docenten die eigenaar zijn van het lab.</li></ul> | CS1234-fall2019-JANJANSEN<br/>CS1234-spring2019-JANJANSEN | 
| Galerie met gedeelde afbeeldingen | Bevat een of meer versies van VM-installatie kopieën | korte naam\>galerie \<organisatie | contosouniversitylabsgallery |

Zie [naamgevings conventies voor Azure-resources](/azure/architecture/best-practices/naming-conventions)voor meer informatie over het benoemen van andere Azure-resources.

## <a name="regions-or-locations"></a>Regio's of locaties
Wanneer u de resources van uw Azure Lab Services instelt, moet u een regio of locatie opgeven van het Data Center dat als host fungeert voor de resource. Hier vindt u meer informatie over hoe region\location van invloed is op elk van de volgende resources die worden gebruikt in de implementatie van de test omgeving:

- **Resourcegroep**

    In de regio wordt het data centrum opgegeven waarin informatie over de resource groep wordt opgeslagen. Azure-resources die deel uitmaken van de resource groep, kunnen zich in verschillende regio's van hun bovenliggende regio bevinden.
- **Lab-account of school lab**

    De locatie van het lab-account geeft de regio voor deze resource aan. Klassikale Labs die in het lab-account is gemaakt, kan worden geïmplementeerd in elke regio binnen hetzelfde geografische gebied. De specifieke regio waar de virtuele machines van de Lab worden geïmplementeerd, worden automatisch geselecteerd op basis van de capaciteit die op dat moment beschikbaar is in de regio.  
    Als een beheerder de locatie van het leslokaal van de klas Lab kan kiezen, zijn de locaties die beschikbaar zijn voor selectie, gebaseerd op de beschik bare regionale capaciteit bij het maken van het lab.

    De locatie van het leslokaal Lab bepaalt ook welke VM-reken grootten er kunnen worden geselecteerd. Bepaalde reken grootten zijn alleen beschikbaar op specifieke locaties.
- **Galerie met gedeelde afbeeldingen**

    De regio geeft de bron regio aan waar de eerste versie van de installatie kopie wordt opgeslagen voordat deze automatisch wordt gerepliceerd naar doel regio's.
    
Een algemene regel is om de region\location van een resource in te stellen op een die het dichtst bij de gebruikers ligt. Voor klassikale Labs betekent dit dat u het leslokaal Lab maakt dat het dichtst bij uw studenten ligt. Voor online cursussen waar studenten zich overal ter wereld bevinden, moet u de beste beslissing nemen om een leslokaal Lab te maken dat zich centraal bevindt. Of Splits een klasse in meerdere klassikale Labs op basis van de regio van uw studenten.

## <a name="vm-sizing"></a>VM-grootte
Wanneer beheerders of Lab-makers een leslokaal Lab maken, kunnen ze kiezen uit de volgende VM-grootten op basis van de behoeften van hun klas lokaal. Houd er rekening mee dat de beschik bare reken grootten afhankelijk zijn van de regio waarin uw Lab-account zich bevindt:

| Grootte | Specificaties | Voorgesteld gebruik |
| ---- | ----- | ------------- |
| Klein| <ul><li>2 kernen</li><li>3,5 GB RAM</li></ul> | Deze grootte is het meest geschikt voor de opdracht regel, de webbrowser openen, webservers met weinig verkeer, kleine tot middel grote data bases. |
| Middelgroot | <ul><li>4 kernen</li><li>7 GB RAM</li></ul> | Deze grootte is het meest geschikt voor relationele data bases, caching in het geheugen en analyse. |
| Gemiddeld (geneste virtualisatie) | <ul><li>4 kernen</li><li>16 GB RAM-GEHEUGEN</li></ul> | Deze grootte is het meest geschikt voor relationele data bases, caching in het geheugen en analyse.  Deze grootte biedt ook ondersteuning voor geneste virtualisatie. |
| Groot | <ul><li>8 kernen</li><li>32 GB RAM-GEHEUGEN</li></ul> | Deze grootte is het meest geschikt voor toepassingen die snellere Cpu's nodig hebben, betere prestaties van de lokale schijf, grote data bases, grote geheugen caches.  Deze grootte biedt ook ondersteuning voor geneste virtualisatie. |
| Kleine GPU (visualisatie) | <ul><li>6 kernen</li><li>56 GB RAM-GEHEUGEN</li> | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games, code ring met behulp van frameworks zoals OpenGL en DirectX. |
| Kleine GPU (Compute) | <ul><li>6 kernen</li><li>56 GB RAM-GEHEUGEN</li></ul> |Deze grootte is het meest geschikt voor computer-intensieve toepassingen, zoals kunst matige intelligentie en diep gaande lessen. |
| Gemiddelde GPU (visualisatie) | <ul><li>12 kernen</li><li>112 GB RAM-GEHEUGEN</li></ul> | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games, code ring met behulp van frameworks zoals OpenGL en DirectX. |

## <a name="manage-identity"></a>Identiteit beheren
Er zijn twee soorten rollen die een Lab-account beheerder kan hebben:

- **Eigenaar**

    Een beheerder die de rol **eigenaar** heeft toegewezen, heeft volledige toegang tot het lab-account, met inbegrip van het recht om andere gebruikers toegang te verlenen tot het lab-account en Lab-makers toe te voegen. De beheerder die het lab-account maakt, wordt standaard toegevoegd als de eigenaar.
- **Inzender**

    Een beheerder die de rol Inzender heeft toegewezen, kan de instellingen van het lab-account wijzigen, maar kan geen toegang verlenen aan andere gebruikers. ook kunnen ze Lab-makers toevoegen.

Wanneer u een galerie met gedeelde afbeeldingen koppelt aan een Lab-account, wordt de toegang automatisch aan de beheerder en de Lab-makers gegeven, zodat deze afbeeldingen in de galerie kunnen bekijken en opslaan. 

## <a name="pricing"></a>Prijzen

### <a name="azure-lab-services"></a>Azure Lab Services
De prijzen voor Azure Lab Services worden beschreven in het volgende artikel: [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/).

U moet ook rekening houden met de prijzen voor de galerie met gedeelde afbeeldingen als u deze wilt gebruiken voor het opslaan en beheren van installatie kopieën. 

### <a name="shared-image-gallery"></a>Galerie met gedeelde afbeeldingen
Het is gratis om een galerie met gedeelde afbeeldingen te maken en deze toe te voegen aan uw Lab-account. Er worden geen kosten in rekening gebracht totdat u een afbeeldings versie in de galerie opslaat. Normaal gesp roken is de prijs voor het gebruik van een galerie met gedeelde afbeeldingen redelijk te verwaarlozen, maar het is belang rijk om te begrijpen hoe deze wordt berekend omdat deze niet is opgenomen in de prijzen voor Azure Lab Services.  

### <a name="storage-charges"></a>Opslag kosten
Voor het opslaan van afbeeldings versies gebruikt een galerie met gedeelde installatie kopieën standaard schijven die met schijven worden beheerd. De grootte van de schijf die wordt gebruikt voor de HDD, is afhankelijk van de grootte van de installatie kopie versie die wordt opgeslagen. Raadpleeg het volgende artikel voor het weer geven van de prijzen: [prijs informatie Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).


### <a name="replication-and-network-egress-charges"></a>Kosten voor replicatie en netwerk uitgaand verkeer
Wanneer u een installatie kopie versie met behulp van de virtuele machine (VM) van een leslokaal Lab opslaat, worden deze door Lab-services eerst opgeslagen in een bron regio en wordt de versie van de bron installatie kopie automatisch gerepliceerd naar een of meer doel regio's. Het is belang rijk te weten dat Azure Lab Services de versie van de bron installatie kopie automatisch repliceert naar alle doel regio's binnen de geolocatie waar het leslokaal Lab zich bevindt. Als uw klas Lab bijvoorbeeld zich in de Amerikaanse geolocatie bevindt, wordt er een afbeeldings versie gerepliceerd naar elk van de acht regio's in de VS

Wanneer een versie van een installatie kopie wordt gerepliceerd van de bron regio naar extra doel regio's, treedt er een uitvulling van het netwerk op. De kosten worden berekend op basis van de grootte van de versie van de installatie kopie wanneer de gegevens van de afbeelding in eerste instantie uitgaande van de bron regio worden verzonden.  Raadpleeg het volgende artikel voor prijs informatie: [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/).

[Educatie-oplossingen](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) klanten kunnen worden kwijtgescholden van de kosten voor het betalen van uitgaand verkeer. Neem contact op met uw account manager voor meer informatie.  Zie voor meer informatie **Raadpleeg de sectie Veelgestelde vragen** in het gekoppelde document, met name de vraag "welke Program ma's voor gegevens overdracht bestaan voor academische klanten en hoe kom ik in aanmerking?").

### <a name="pricing-example"></a>Prijsvoorbeeld
Voor het samen vatting van de prijzen die hierboven worden beschreven, bekijken we een voor beeld van het opslaan van de VM-installatie kopie van de sjabloon naar de galerie met gedeelde installatie kopieën. Stel dat de volgende scenario's zijn:

- U hebt één aangepaste VM-installatie kopie.
- U slaat twee versies van de installatie kopie op.
- Uw lab bevindt zich in de Verenigde Staten, met in totaal acht regio's.
- Elke afbeeldings versie is 32 GB groot. Als gevolg hiervan is de prijs voor de schijf met harde schijven $1,54 per maand.

De totale kosten worden geschat als:

Aantal installatie kopieën × het aantal versies × het aantal replica's × de beheerde schijf prijs

In dit voor beeld zijn de kosten:

1 aangepaste installatie kopie (32 GB) x 2 versies x 8 Amerikaanse regio's x $1,54 = $24,64 per maand

### <a name="cost-management"></a>Kostenbeheer
Het is belang rijk dat een Lab-account beheerder kosten kan beheren door de niet-vereiste installatie kopieën in de galerie regel matig te verwijderen. 

Het is niet mogelijk om replicatie naar specifieke regio's te verwijderen als een manier om de kosten te verlagen (deze optie komt voor in de galerie met gedeelde afbeeldingen). Wijzigingen in de replicatie kunnen nadelige gevolgen hebben voor de mogelijkheid van de Azure Lab-service om Vm's te publiceren vanuit afbeeldingen die zijn opgeslagen in een galerie met gedeelde installatie kopieën.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de zelf studie voor stapsgewijze instructies voor het maken van een Lab-account en een Lab: [zelf studie: een Lab-account instellen](tutorial-setup-lab-account.md)
