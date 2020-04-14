---
title: Azure Lab Services - Beheerdershandleiding | Microsoft Documenten
description: Deze handleiding helpt beheerders die labaccounts maken en beheren met Azure Lab Services.
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
ms.openlocfilehash: 55e319ba8aecb9205c00dda4a400e37f7c010649
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257773"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services - Beheerdershandleiding
It-beheerders (Information Technology) die de cloudresources van een universiteit beheren, zijn doorgaans verantwoordelijk voor het opzetten van het labaccount voor hun school. Zodra een labaccount is ingesteld, maken beheerders of docenten klaslokalen die zich in het labaccount bevinden. Dit artikel biedt een overzicht op hoog niveau van de betrokken Azure-resources en de richtlijnen voor het maken ervan.

![Weergave op hoog niveau van Azure-resources in een labaccount](../media/administrator-guide/high-level-view.png)

- Classroom labs worden gehost binnen een Azure-abonnement dat eigendom is van Azure Lab Services.
- Labaccounts, gedeelde afbeeldingsgalerie en afbeeldingsversies worden gehost in uw abonnement.
- U uw labaccount en de gedeelde afbeeldingsgalerie in dezelfde resourcegroep hebben. In dit diagram bevinden ze zich in verschillende resourcegroepen. 

## <a name="subscription"></a>Abonnement
Uw universiteit heeft een of meer Azure-abonnementen. Een abonnement wordt gebruikt voor het beheren van facturering en beveiliging voor alle Azure-resources\services die erin worden gebruikt, inclusief labaccounts.

De relatie tussen een labaccount en het abonnement is belangrijk omdat:

- Facturering wordt gerapporteerd via het abonnement dat het labaccount bevat.
- U gebruikers in de Azure Active Directory (AD)-tenant van het abonnement toegang geven tot Azure Lab Services. U een gebruiker toevoegen als eigenaar van een labaccount\bijdrager, maker van een klaslokaallab of eigenaar van een klaslokaallab.

Classroom labs en hun virtuele machines (VM's) worden beheerd en gehost voor u binnen een abonnement dat eigendom is van Azure Lab Services.

## <a name="resource-group"></a>Resourcegroep
Een abonnement bevat een of meer brongroepen. Resourcegroepen worden gebruikt om logische groeperingen van Azure-resources te maken die samen binnen dezelfde oplossing worden gebruikt.  

Wanneer u een labaccount maakt, moet u de brongroep configureren die het labaccount bevat. 

Er is ook een resourcegroep vereist bij het maken van een [gedeelde afbeeldingsgalerie](#shared-image-gallery). U ervoor kiezen om uw labaccount en gedeelde afbeeldingsgalerie in twee afzonderlijke brongroepen te plaatsen, wat typisch is als u van plan bent de afbeeldingsgalerie over verschillende oplossingen te delen. U er ook voor kiezen om ze in dezelfde resourcegroep te plaatsen.

Wanneer u een labaccount maakt, u automatisch tegelijkertijd een gedeelde afbeeldingsgalerie maken en koppelen.  Deze optie resulteert in het labaccount en de gedeelde afbeeldingsgalerie die wordt gemaakt in afzonderlijke brongroepen. U ziet dit gedrag wanneer u de stappen gebruikt die in deze zelfstudie worden beschreven: [Gedeelde afbeeldingsgalerie configureren op het moment van het maken van labaccounten.](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) De afbeelding bovenaan dit artikel maakt ook gebruik van deze configuratie. 

We raden u aan vooraf tijd te investeren om de structuur van uw resourcegroepen te plannen, omdat het *niet* mogelijk is om de brongroep van een labaccount of gedeelde afbeeldingsgalerie te wijzigen zodra deze is gemaakt. Als u de brongroep voor deze bronnen wilt wijzigen, moet u uw labaccount en de gedeelde afbeeldingsgalerie verwijderen en opnieuw maken.

## <a name="lab-account"></a>Lab-account
Een labaccount dient als container voor een of meer klaslokalen. Wanneer u aan de slag gaat met Azure Lab Services, is het gebruikelijk om slechts één labaccount te hebben. Naarmate uw laboratoriumgebruik wordt geschaald, u er later voor kiezen om meer labaccounts aan te maken.

In de volgende lijst worden scenario's weergegeven waarin meer dan één labaccount nuttig kan zijn:

- **Verschillende beleidsvereisten beheren in klaslokalen** 
    
    Wanneer u een labaccount instelt, stelt u beleid in dat van toepassing is op *alle* klaslokalen onder het labaccount, zoals:
    - Het virtuele Azure-netwerk met gedeelde bronnen waartoe het klaslab toegang heeft. U bijvoorbeeld een reeks klaslokalen hebben die toegang nodig hebben tot een gedeelde gegevensset binnen een virtueel netwerk.
    - De virtuele machine (VM) beelden die de klas labs kunnen gebruiken om VM's te maken. U bijvoorbeeld een reeks klaslokalen hebben die toegang nodig hebben tot de [afbeelding van de Data Science VM voor Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Marketplace. 
    
    Als u klaslokalen hebt met unieke beleidsvereisten van elkaar, kan het nuttig zijn om afzonderlijke labaccounts te maken om deze klaslokalen afzonderlijk te beheren.

- **Afzonderlijk budget per labaccount**
  
    In plaats van alle klassikale labkosten te rapporteren via één labaccount, hebt u mogelijk een duidelijker gescheiden budget nodig. U bijvoorbeeld labaccounts maken voor de afdeling Wiskunde van uw universiteit, de afdeling Informatica, enzovoort, om het budget te scheiden tussen afdelingen.  Vervolgens u de kosten voor elk afzonderlijk labaccount bekijken met [Azure Cost Management.](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)
    
- **Proeflabs isoleren van actieve\productielaboratoria**
  
    U gevallen hebben waarin u beleidswijzigingen voor een labaccount wilt doorvoeren zonder mogelijk invloed te hebben op actieve\productielaboratoria. In dit type scenario u met het maken van een afzonderlijk labaccount voor pilotdoeleinden wijzigingen isoleren. 

## <a name="classroom-lab"></a>Klaslokaal lab
Een klaslokaal lab bevat virtuele machines (VM's) die elk zijn toegewezen aan een enkele student. In het algemeen u verwachten:

- Hebben een klaslokaal lab voor elke klas.
- Maak elk semester een nieuwe set klaslokalen (of voor elk tijdsbestek dat je klas wordt aangeboden). Meestal voor klassen die dezelfde afbeeldingbehoeften hebben, moet u een [gedeelde afbeeldingsgalerie](#shared-image-gallery) gebruiken om afbeeldingen in laboratoria en semesters opnieuw te gebruiken.

Houd rekening met de volgende punten bij het bepalen hoe u uw klassikale labs structureren:

- **Alle VM's in een klaslokaallab worden geïmplementeerd met dezelfde afbeelding die is gepubliceerd**

    Als u een klasse hebt waarvoor verschillende laboratoriumafbeeldingen tegelijkertijd moeten worden gepubliceerd, moeten voor elk van deze klassen afzonderlijke klaslokalen worden gemaakt.
  
- **Gebruiksquotum is ingesteld op labniveau en is van toepassing op alle gebruikers in het lab**
    
    Als u verschillende quota voor gebruikers wilt instellen, moet u afzonderlijke klaslokalen maken. Het is echter mogelijk om meer uren toe te voegen aan een specifieke gebruiker nadat u het quotum hebt ingesteld.
  
- **Het opstart- of afsluitschema is ingesteld op labniveau en is van toepassing op alle VM's in het lab**

    Net als bij het vorige punt moet u, als u verschillende schema's voor gebruikers moet instellen, afzonderlijke klaslokalen maken. 

## <a name="shared-image-gallery"></a>Gedeelde afbeeldingsgalerie
Een gedeelde afbeeldingsgalerij is gekoppeld aan een labaccount en dient als een centrale opslagplaats voor het opslaan van afbeeldingen. Een afbeelding wordt opgeslagen in de galerie wanneer een docent ervoor kiest om te exporteren vanuit de virtuele machine (VM) van een klaslokaallab. Elke keer dat een docent wijzigingen aanbrengt in de sjabloon-VM en exporteert, worden nieuwe versies van de afbeelding opgeslagen met behoud van eerdere versies.

Cursusleiders kunnen een afbeeldingsversie uit de gedeelde afbeeldingsgalerie publiceren wanneer ze een nieuw klaslokaallab maken. Hoewel de galerie meerdere versies van een afbeelding kan opslaan, kunnen docenten alleen de nieuwste versie selecteren tijdens het maken van het lab.

Gedeelde afbeeldingsgalerie is een optionele bron die u mogelijk niet onmiddellijk nodig hebt wanneer u begint met slechts een paar klaslokalen. Het gebruik van gedeelde afbeeldingsgalerie heeft echter veel voordelen die nuttig zijn als u schaalt naar het hebben van meer klaslokalen:

- **Hiermee u versies van een vm-afbeelding van een sjabloon opslaan en beheren**

    Het is handig om een aangepaste afbeelding te maken of wijzigingen aan te brengen (software, configuratie, enzovoort) in een afbeelding uit de openbare Marketplace-galerie.  Het is bijvoorbeeld gebruikelijk dat docenten verschillende software\tooling vereisen. In plaats van studenten te verplichten deze vereisten handmatig zelf te installeren, kunnen verschillende versies van de vm-afbeelding van de sjabloon worden geëxporteerd naar een gedeelde afbeeldingsgalerie. Deze afbeeldingsversies kunnen vervolgens worden gebruikt bij het maken van nieuwe klaslokalen.
- **Hiermee u het delen van sjabloon-VM-afbeeldingen in klaslokalen gebruiken**

    U een afbeelding opslaan en opnieuw gebruiken, zodat u de afbeelding niet helemaal opnieuw hoeft te configureren telkens wanneer u een nieuw klaslokaallab maakt. Als er bijvoorbeeld meerdere klassen worden aangeboden die dezelfde afbeelding nodig hebben, hoeft deze afbeelding slechts één keer te worden gemaakt en geëxporteerd naar de gedeelde afbeeldingsgalerie, zodat deze kan worden gedeeld in klaslokalen.
- **Zorgt voor beschikbaarheid van afbeeldingen door replicatie**

    Wanneer u vanuit een klaslokaal in de gedeelde afbeeldingsgalerie opslaat, wordt uw afbeelding automatisch gerepliceerd naar andere [regio's binnen dezelfde geografie.](https://azure.microsoft.com/global-infrastructure/regions/) In het geval dat er een storing is voor een regio, wordt het publiceren van de afbeelding in uw klaslokaallab niet beïnvloed omdat een afbeeldingsreplica uit een andere regio kan worden gebruikt.  Het publiceren van VM's uit meerdere replica's kan ook helpen bij de prestaties.

Als u gedeelde afbeeldingen logisch wilt groeperen, hebt u een aantal opties:

- Meerdere gedeelde afbeeldingsgalerieën maken. Elk labaccount kan slechts verbinding maken met één gedeelde afbeeldingsgalerie, dus voor deze optie moet u ook meerdere labaccounts maken.
- U ook één gedeelde afbeeldingsgalerie gebruiken die wordt gedeeld door meerdere labaccounts. In dit geval kan elk labaccount alleen afbeeldingen inschakelen die van toepassing zijn op de klaslabs die het bevat.

## <a name="naming"></a>Naamgeving
Terwijl u aan de slag gaat met Azure Lab Services, raden we u aan naamgevingsconventies op te stellen voor resourcegroepen, labaccounts, klaslokalen en de gedeelde afbeeldingsgalerie. Hoewel de naamgevingsconventies die u vaststelt, uniek zijn voor de behoeften van uw organisatie, worden in de volgende tabel algemene richtlijnen beschreven.

| Resourcetype | Rol | Voorgesteld patroon | Voorbeelden |
| ------------- | ---- | ----------------- | -------- | 
| Resourcegroep | Bevat een of meer labaccounts en een of meer gedeelde afbeeldingsgalerieën | \<organisatie korte\>-\<\>naam omgeving -rg<ul><li>**De korte naam** van de organisatie identificeert de naam van de organisatie die de resourcegroep ondersteunt</li><li>**Omgeving** identificeert de omgeving voor de bron, zoals Pilot of Production</li><li>**Rg** staat voor het resourcetype: resourcegroep.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Lab-account | Bevat een of meer labs | \<organisatie korte\>-\<\>naam omgeving -la<ul><li>**De korte naam** van de organisatie identificeert de naam van de organisatie die de resourcegroep ondersteunt</li><li>**Omgeving** identificeert de omgeving voor de bron, zoals Pilot of Production</li><li>**La** staat voor het type resource: lab account.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Klaslokaal lab | Bevat een of meer VM's |\<classificatie\>-\<van\>-\<klassenaam timeframe educator\><ul><li>**Klassenaam** identificeert de naam van de klasse die het lab ondersteunt.</li><li>**Het tijdsbestek** identificeert het tijdsbestek waarin de klasse wordt aangeboden.</li>**Onderwijs-id** identificeert de opvoeder die eigenaar is van het lab.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-lente2019-johndoe | 
| Gedeelde afbeeldingsgalerie | Bevat een of meer VM-afbeeldingsversies | \<organisatie korte\>naam galerie | contosouniversitylabsgallery |

Zie [Conventies voor Azure-bronnen benoemen](/azure/architecture/best-practices/naming-conventions)voor meer informatie over het benoemen van andere Azure-bronnen.

## <a name="regionslocations"></a>Regio's\locaties

Wanneer u de resources van uw Azure Lab Services instelt, moet u een regio (of locatie) van het datacenter opgeven dat de bron host. Hier vindt u meer informatie over de invloed van de regio op elk van de bronnen die betrokken zijn bij het opzetten van een lab.

### <a name="resource-group"></a>Resourcegroep

Het gebied geeft het datacenter op waar informatie over de resourcegroep wordt opgeslagen. Azure-resources in de resourcegroep kunnen zich in verschillende regio's bevinden dan hun bovenliggende bronnen.

### <a name="lab-account"></a>Lab-account

De locatie van een labaccount geeft aan in welke regio deze bron bestaat.  

### <a name="classroom-lab"></a>Klaslokaal lab
    
De locatie waarin een klassikale lab bestaat, is afhankelijk van de volgende factoren:

  - **Lab-account wordt gekeken naar een virtueel netwerk (VNet)**
  
    Een labaccount kan worden [gekeken met een VNet](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) wanneer deze zich in dezelfde regio bevindt.  Wanneer een labaccount wordt gekeken met een VNet, worden klaslokalen automatisch gemaakt in dezelfde regio als zowel het labaccount als VNet.

    > [!NOTE]
    > Wanneer een labaccount wordt gekeken met een VNet, is de instelling voor **de locatie van** het lab uitgeschakeld. Aanvullende informatie is te vinden over deze instelling in het artikel: [Laat de maker van het lab locatie voor het lab kiezen.](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)
    
  - **Geen VNet is peered ***en*** lab makers zijn niet toegestaan om het lab locatie te kiezen**
  
    Wanneer er **geen** VNet-peered is met het labaccount *en* [labmakers de lablocatie **niet** mogen kiezen,](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)worden klaslokalen automatisch gemaakt in een regio met beschikbare VM-capaciteit.  Azure Lab Services zoekt specifiek naar beschikbaarheid in [regio's die zich binnen dezelfde geografie bevinden als het labaccount.](https://azure.microsoft.com/global-infrastructure/regions)

  - **Geen VNet is peered ***en*** lab makers zijn toegestaan om het lab locatie te kiezen**
       
    Wanneer er **geen** VNet peered is en [labmakers de lablocatie mogen kiezen,](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)zijn de locaties die door de maker van het lab kunnen worden geselecteerd, gebaseerd op de beschikbare capaciteit.

> [!NOTE]
> Om ervoor te zorgen dat er voldoende VM-capaciteit is voor een regio, is het belangrijk dat u eerst capaciteit opvraagt via het labaccount of bij het maken van het lab.

Een algemene regel is om de regio van een resource in te stellen op een regio die het dichtst bij de gebruikers staat. Voor klaslokalen betekent dit het creëren van het klaslokaallab dat het dichtst bij uw leerlingen staat. Voor online cursussen waar studenten zich over de hele wereld bevinden, moet u uw beste oordeel gebruiken om een klaslokaallab te maken dat centraal is gelegen. Of splits een klas op in meerdere klaslokalen op basis van de regio van uw leerlingen.

### <a name="shared-image-gallery"></a>Gedeelde afbeeldingsgalerie

Het gebied geeft het brongebied aan waar de eerste afbeeldingsversie wordt opgeslagen voordat deze automatisch wordt gerepliceerd naar doelregio's.

## <a name="vm-sizing"></a>VM-formaat
Wanneer beheerders of labmakers een klaslokaallab maken, kunnen ze kiezen uit de volgende VM-formaten op basis van de behoeften van hun klaslokaal. Houd er rekening mee dat de beschikbare rekengroottes afhankelijk zijn van de regio waarin uw labaccount zich bevindt:

| Grootte | Specificaties | Reeks | Voorgesteld gebruik |
| ---- | ----- | ------ | ------------- |
| Klein| <ul><li>2 kernen</li><li>3,5 GB RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Deze grootte is het meest geschikt voor opdrachtregel, het openen van webbrowser, low traffic webservers, kleine tot middelgrote databases. |
| Middelgroot | <ul><li>4 Kernen</li><li>7 GB RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Deze grootte is het meest geschikt voor relationele databases, in-memory caching en analytics. |
| Medium (Geneste virtualisatie) | <ul><li>4 Kernen</li><li>16 GB RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Deze grootte is het meest geschikt voor relationele databases, in-memory caching en analytics.  Deze grootte ondersteunt ook geneste virtualisatie. |
| Groot | <ul><li>8 kernen</li><li>32 GB RAM</li></ul>  | [Standard_DC8_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Dit formaat is het meest geschikt voor toepassingen die snellere CPU's nodig hebben, betere lokale schijfprestaties, grote databases, grote geheugencaches.  Deze grootte ondersteunt ook geneste virtualisatie. |
| Kleine GPU (visualisatie) | <ul><li>6 Kernen</li><li>56 GB RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Dit formaat is het meest geschikt voor externe visualisatie, streaming, gaming, codering met behulp van frameworks zoals OpenGL en DirectX. |
| Kleine GPU (Berekenen) | <ul><li>6 Kernen</li><li>56 GB RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Dit formaat is het meest geschikt voor computerintensieve toepassingen zoals Kunstmatige Intelligentie en Deep Learning. |
| Middelgrote GPU (visualisatie) | <ul><li>12 kernen</li><li>112 GB RAM</li></ul>  | [Standard_NC12](https://docs.microsoft.com/azure/virtual-machines/nc-series) | Dit formaat is het meest geschikt voor externe visualisatie, streaming, gaming, codering met behulp van frameworks zoals OpenGL en DirectX. |

## <a name="manage-identity"></a>Identiteit beheren
Met behulp van [het op rollen gebaseerde toegangscontrolebeheer van Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)kunnen de volgende rollen worden toegewezen om toegang te geven tot labaccounts en klaslokalen:

- **Labeigenaar**

    De beheerder die het labaccount maakt, wordt automatisch toegevoegd aan de **rol Eigenaar** van het labaccount.  Een beheerder die de rol **Eigenaar** heeft toegewezen, kan:
     - Wijzig de instellingen van het labaccount.
     - Geef andere beheerders toegang tot het labaccount als eigenaren of bijdragers. 
     - Geef docenten toegang tot klaslokalen als makers, eigenaren of bijdragers.
     - Maak en beheer alle klaslokalen binnen het labaccount.

- **Bijdrage aan labaccount**

    Een beheerder die de rol **Inzender** heeft toegewezen, kan:
    - Wijzig de instellingen van het labaccount.
    - Maak en beheer alle klaslokalen binnen het labaccount.
    
    Ze kunnen andere gebruikers echter *geen* toegang geven tot labaccounts of klaslokalen.

- **De laboratoriumschepper van het klaslokaal**

    Als u klaslokalen wilt maken binnen een labaccount, moet een docent lid zijn van de rol **Lab Creator.**  Wanneer een docent een klaslokaallab maakt, worden ze automatisch toegevoegd als eigenaar van het lab.  Raadpleeg de zelfstudie over het [toevoegen van een gebruiker aan de lab **creator-rol.** ](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role) 

- **Classroom lab owner\contributor**
  
    Een docent kan de instellingen van een klaslokaallab bekijken en wijzigen wanneer hij lid is van de **rol Eigenaar** of **Bijdrager van** een lab. ze moeten ook lid zijn van de **Reader-rol** van het labaccount.

    Een belangrijk verschil tussen de **rol van eigenaar** van een lab en de **inzender is** dat een bijdrager andere gebruikers geen toegang *kan* geven tot het beheer van het lab - alleen eigenaren kunnen andere gebruikers toegang geven om het lab te beheren.
    
    Bovendien kan een docent *geen* nieuwe klaslabs maken, tenzij ze ook lid zijn van de **Lab Creator-rol.**

- **Gedeelde afbeeldingsgalerie**
    
    Wanneer u een gedeelde afbeeldingsgalerie koppelt aan een labaccount, worden eigenaren van labaccounten\bijdragers en labmakers\eigenaren\bijdragers, automatisch toegang tot het bekijken en opslaan van afbeeldingen in de galerie. 

Hier volgen enkele tips om rollen toe te wijzen:
   - Doorgaans moeten alleen beheerders lid zijn van de **eigenaar-** of **inzenderrollen** van een labaccount. u meer dan één eigenaar hebben\bijdrager leveren.

   - Om een docent de mogelijkheid te geven om nieuwe klaslabs te maken en de labs te beheren die ze maken; u hoeft alleen toegang toe te wijzen tot de lab **creator-rol.**
   
   - Om een docent de mogelijkheid te geven om specifieke klaslabs te beheren, maar *niet* de mogelijkheid om nieuwe labs te maken; u moet toegang toewijzen tot de rol **Eigenaar** of **inzender** voor elk van de klaslokalen die ze zullen beheren.  U bijvoorbeeld toestaan dat zowel een professor als een onderwijsassistent mede-eigenaar zijn van een klaslokaallab.  Raadpleeg de handleiding voor het [toevoegen van een gebruiker als eigenaar aan een klaslokaallab.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner)

## <a name="pricing"></a>Prijzen

### <a name="azure-lab-services"></a>Azure Lab-Services
De prijzen voor Azure Lab Services worden beschreven in het volgende artikel: [Azure Lab Services-prijzen](https://azure.microsoft.com/pricing/details/lab-services/).

U moet ook rekening houden met de prijzen voor de gedeelde afbeeldingsgalerie als u van plan bent deze te gebruiken voor het opslaan en beheren van afbeeldingsversies. 

### <a name="shared-image-gallery"></a>Gedeelde afbeeldingsgalerie
Het maken van een gedeelde afbeeldingsgalerie en het koppelen aan uw labaccount is gratis. Er worden geen kosten gemaakt totdat u een afbeeldingsversie in de galerie hebt opgeslagen. Doorgaans zijn de prijzen voor het gebruik van een gedeelde afbeeldingsgalerie vrij verwaarloosbaar, maar het is belangrijk om te begrijpen hoe deze wordt berekend, omdat deze niet is opgenomen in de prijzen voor Azure Lab Services.  

#### <a name="storage-charges"></a>Opslagkosten
Als u afbeeldingsversies wilt opslaan, maakt een gedeelde afbeeldingsgalerie gebruik van standaard schijven die door hdd's worden beheerd. De grootte van de hdd-beheerde schijf die wordt gebruikt, is afhankelijk van de grootte van de afbeeldingsversie die wordt opgeslagen. Zie het volgende artikel om de prijzen te bekijken: [Prijzen voor beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/).


#### <a name="replication-and-network-egress-charges"></a>Replicatie- en netwerkuitgangskosten
Wanneer u een afbeeldingsversie opslaat met de virtuele machine (VM) van een classroom lab, slaat Azure Lab Services deze eerst op in een brongebied en repliceert u de bronafbeeldingsversie automatisch naar een of meer doelregio's. Het is belangrijk op te merken dat Azure Lab Services automatisch de bronafbeeldingsversie repliceert naar alle [doelgebieden binnen de geografie](https://azure.microsoft.com/global-infrastructure/regions/) waar het klaslokaallab zich bevindt. Als uw klaslab zich bijvoorbeeld in de Amerikaanse geografie bevindt, wordt een afbeeldingsversie gerepliceerd naar elk van de acht regio's die binnen de VS bestaan.

Er wordt een netwerkuitgangslading in rekening gebracht wanneer een afbeeldingsversie wordt gerepliceerd vanuit het brongebied naar extra doelregio's. Het in rekening gebrachte bedrag is gebaseerd op de grootte van de afbeeldingsversie wanneer de gegevens van de afbeelding in eerste instantie uitgaand worden overgedragen vanuit het brongebied.  Voor prijsdetails verwijzen wij u naar het volgende artikel: [Details over bandbreedteprijzen](https://azure.microsoft.com/pricing/details/bandwidth/).

Klanten van [onderwijsoplossingen](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) kunnen worden kwijtgescholden van het betalen van uitgaande kosten. Neem contact op met uw accountmanager voor meer informatie.  Zie voor meer informatie **de faq-sectie** in het gekoppelde document, met name de vraag "Welke programma's voor gegevensoverdracht bestaan voor academische klanten en hoe kom ik in aanmerking?".

#### <a name="pricing-example"></a>Prijsvoorbeeld
Als u de hierboven beschreven prijzen wilt samenvatten, bekijken we een voorbeeld van het opslaan van onze sjabloon-VM-afbeelding in gedeelde afbeeldingsgalerie. Ga uit van de volgende scenario's:

- U hebt één aangepaste VM-afbeelding.
- Je slaat twee versies van de afbeelding op.
- Je lab is in de VS, die in totaal acht regio's heeft.
- Elke afbeeldingsversie is 32 GB groot; als gevolg daarvan, de HDD-beheerde schijf prijs is $ 1,54 per maand.

De totale kosten worden geschat als:

Aantal afbeeldingen × aantal versies × aantal replica's × beheerde schijfprijs

In dit voorbeeld zijn de kosten:

1 aangepaste afbeelding (32 GB) x 2-versies x 8 Amerikaanse regio's x $ 1,54 = $ 24,64 per maand

#### <a name="cost-management"></a>Kostenbeheer
Het is belangrijk dat de beheerder van het labaccount de kosten beheert door routinematig overbodige afbeeldingsversies uit de galerie te verwijderen. 

U moet replicatie naar specifieke regio's niet verwijderen als een manier om de kosten te verlagen (deze optie bestaat in de gedeelde afbeeldingsgalerie). Replicatiewijzigingen kunnen nadelige gevolgen hebben voor de mogelijkheid van Azure Lab Service om VM's te publiceren van afbeeldingen die zijn opgeslagen in een gedeelde afbeeldingsgalerie.

## <a name="next-steps"></a>Volgende stappen
Bekijk de zelfstudie voor stapsgewijze instructies om een labaccount en een lab te maken: [Handleiding instellen](tutorial-setup-lab-account.md)
