---
title: Informatie over Azure Lab Services | Microsoft Docs
description: Lees hoe u met Lab Services gemakkelijk labs kunt maken, beheren en beveiligen met virtuele machines die kunnen worden gebruikt door ontwikkelaars, testers, docenten, studenten en anderen.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: 4258bec3ceacd52f9679f48d8357be558ee0e27f
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561634"
---
# <a name="an-introduction-to-azure-lab-services"></a>Een inleiding tot Azure Lab Services
Er zijn twee services in azure waarmee u test omgevingen in de cloud kunt instellen. 

- **Azure DevTest Labs** : met deze service kunt u snel een omgeving instellen voor uw team (bijvoorbeeld: ontwikkel omgeving of test omgeving in de Cloud). De eigenaar van een lab maakt een lab, richt virtuele machines met Windows of Linux in, installeert de benodigde software en hulpprogramma's en maakt deze beschikbaar voor gebruikers van het lab. Gebruikers met een lab maken verbinding met virtuele machines (Vm's) in het lab en gebruiken ze voor hun dagelijkse werk, projecten op korte termijn. Zodra gebruikers aan de slag gaan met resources in het lab, kan een beheerder van het lab kosten en gebruik voor meerdere labs analyseren en overkoepelend beleid instellen om de kosten van uw organisatie of het team te optimaliseren.
- **Azure Lab Services** : met deze service kunt u beheerde Lab-typen maken. Op dit moment is klassikale Labs het enige type beheerde Lab dat door Azure Lab Services wordt ondersteund. De service zelf is verantwoordelijk voor het volledige beheer van de infrastructuur voor een beheerd lab, van het inrichten van VM's tot de afhandeling van fouten, en het schalen van de infrastructuur. Nadat een IT-beheerder een Lab-account heeft gemaakt in Azure Lab Services, kan een docent snel een Lab instellen voor zijn klasse, het aantal en type virtuele machines opgeven die moeten worden verwerkt in de klasse en gebruikers toevoegen aan de klasse. Wanneer een gebruiker zich registreert voor de klasse, heeft de gebruiker toegang tot de virtuele machine voor het uitvoeren van oefeningen voor de klasse.  

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Deze services (Azure DevTest Labs en Azure Lab Services) ondersteunen de volgende belang rijke mogelijkheden/functies:

- **Snelle en flexibele installatie van een lab**. Met Azure Lab Services kunnen labeigenaren snel een lab instellen voor hun behoeften. De service biedt voor beheerde labs de mogelijkheid om alle beheertaken voor de Azure-infrastructuur uit te besteden. Eigenaren van een lab kunnen er echter ook voor kiezen om de infrastructuur zelf te beheren en aan te passen in het abonnement van de labeigenaar. De service biedt ingebouwde schaalbaarheid en tolerantie van de infrastructuur voor labs die de service voor u beheert.
- **Vereenvoudigde ervaring voor labgebruikers**. In een beheerd lab, zoals een lab voor het leslokaal, kunnen labgebruikers zich met een registratiecode registreren bij een lab en het lab vervolgens op elk gewenst moment openen om de resources van het lab te gebruiken. In een lab dat in DevTest Labs is gemaakt, kan de eigenaar van een lab machtigingen verlenen aan labgebruikers voor het maken en gebruiken van virtuele machines, het beheren en het gebruiken van gegevensschijven en het instellen van herbruikbare geheimen.  
- **Optimalisatie van kosten en analyse**. De eigenaar van een lab kan planningen voor het lab instellen om virtuele machines automatisch af te sluiten en op te starten. De eigenaar van het lab kan een planning instellen om de tijdstippen op te geven wanneer de virtuele machines van het lab toegankelijk zijn voor gebruikers. De eigenaar kan bovendien een gebruiksbeleid per gebruiker of per lab instellen om de kosten te optimaliseren, en om gebruiks- en activiteitstrends te analyseren. Voor beheerde labs zoals leslokaal-labs is momenteel een kleinere subset met opties voor kostenoptimalisatie en -analyse beschikbaar.
- **Ingesloten beveiliging**. De eigenaar van een lab kan particuliere virtuele netwerken en een subnet inrichten voor een lab, en een gedeeld openbaar IP-adres inschakelen. Labgebruikers hebben op een veilige manier toegang tot resources met behulp van een virtueel netwerk dat is geconfigureerd met ExpressRoute of site-naar-site-VPN. (momenteel alleen beschikbaar in DevTest Labs)
- **Integratie met uw werkstromen en hulpprogramma's**. Met behulp van Azure Lab Services kunt u de labs integreren met de website en beheersystemen van uw organisatie. U kunt vanuit uw hulpprogramma's voor continue integratie/continue implementatie (CI/CD) automatisch omgevingen inrichten. (momenteel alleen beschikbaar in DevTest Labs)

> [!NOTE]
> Azure Lab Services ondersteunt momenteel alleen VM's die zijn gemaakt op basis van Microsoft Azure Marketplace-installatiekopieën. Als u aangepaste installatiekopieën wilt gebruiken of andere PaaS-resources wilt maken in een testomgeving, gebruikt u DevTest Labs. Zie [een aangepaste installatie kopie maken in DevTest Labs](devtest-lab-create-custom-image-from-vm-using-portal.md) en [test omgevingen maken met Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md)voor meer informatie.

## <a name="scenarios"></a>Scenario's

Hier volgen enkele van de scenario's die Azure DevTest Labs en Azure Lab Services ondersteuning:

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Inrichten van een schaalbaar computerlab in de cloud voor uw leslokaal  

- Maak een beheerd lab voor het leslokaal. U geeft precies aan wat u nodig hebt en de service zorgt er vervolgens voor dat de infrastructuur van het lab wordt gemaakt en beheerd. Hierdoor kunt u zich volledig richten op uw leerlingen of studenten en hoeft u zich niet bezig te houden met de technische details van een lab.
- Bied leerlingen en studenten een lab met virtuele machines die zijn geconfigureerd met exact wat nodig is voor een les of college. Geef elke leerling of student een beperkt aantal uren voor het gebruik van de VM's voor het uitvoeren van hun taken.  
- Breng het fysieke computerlab van uw school over naar de cloud. U kunt het aantal VM's automatisch schalen tot de drempelwaarde voor gebruik en kosten die u instelt voor het lab.
- Verwijder het lab met één muisklik als u klaar bent.

### <a name="use-devtest-labs-for-development-environments"></a>DevTest Labs gebruiken voor ontwikkelomgevingen

Azure DevTest Labs kan worden gebruikt voor het implementeren van allerlei scenario's. Eén van de belangrijkste scenario's is het inzetten van DevTest Labs voor het hosten van ontwikkelmachines voor ontwikkelaars. In dit scenario biedt DevTest Labs deze voordelen:

- Ontwikkelaars kunnen hun ontwikkelmachines snel op aanvraag inrichten.
- U kunt Windows- en Linux-omgevingen inrichten met behulp van herbruikbare sjablonen en artefacten.
- Ontwikkelaars kunnen hun ontwikkelmachines eenvoudig aanpassen als dat nodig is.
- Beheerders kunnen de kosten beheersen door ervoor te zorgen dat ontwikkelaars niet meer VM's krijgen toegewezen dan dat ze nodig hebben voor ontwikkeling en dat VM's worden afgesloten wanneer deze niet in gebruik zijn.

Zie [Use Azure DevTest Labs for developers](devtest-lab-developer-lab.md) (Azure DevTest Labs gebruiken voor ontwikkelaars) voor meer informatie.

### <a name="use-devtest-labs-for-test-environments"></a>DevTest Labs gebruiken voor testomgevingen

U kunt Azure DevTest Labs gebruiken voor het implementeren van allerlei scenario's. Eén van de belangrijkste scenario's is het inzetten van DevTest Labs voor het hosten van machines voor testers. In dit scenario biedt DevTest Labs deze voordelen:

- Testers kunnen de nieuwste versie van hun toepassing testen door Windows- en Linux-omgevingen snel in te richten met behulp van herbruikbare sjablonen en artefacten.
- Testers kunnen hun belastingstests omhoog schalen door meerdere test-agents in te richten.
- Beheerders kunnen de kosten beheersen door ervoor te zorgen dat testers niet meer VM's krijgen toegewezen dan dat ze nodig hebben voor testen en dat VM's worden afgesloten wanneer deze niet in gebruik zijn.

Zie [Use Azure DevTest Labs for testing](devtest-lab-test-env.md) (Azure DevTest Labs gebruiken voor testen) voor meer informatie.

## <a name="types-of-labs"></a>Typen labs
U kunt twee soorten labs maken: **beheerde labs** met Azure Lab Services en **aangepaste labs** met Azure Lab Services. Als u alleen in een lab wilt invoeren wat u nodig hebt en de service de voor het lab vereiste infrastructuur wilt laten instellen, dient u een van de **beheerde labtypen** te kiezen. Op dit moment is het **leslokaallab** het enige type beheerde lab dat u met Azure Lab Services kunt maken. Als u uw eigen infrastructuur wilt beheren, maakt u een lab met behulp van **Azure DevTest Labs**.

De volgende secties bevatten meer informatie over deze labs. 

## <a name="managed-lab-types"></a>Beheerde labtypen
Met Azure Lab Services kunt u labs maken waarvan de infrastructuur wordt beheerd door Azure. In dit artikel worden deze beheerde labs genoemd. Beheerde labs bieden verschillende soorten labs die geschikt zijn voor uw specifieke behoeften. Op dit moment is **leslokaallab** het enige type beheerde lab dat wordt ondersteund. 

Met beheerde labs kunt u meteen aan de slag met een minimale installatie. De service zelf is verantwoordelijk voor het volledige beheer van de infrastructuur voor het lab, van het inrichten van de VM's tot de afhandeling van fouten, en het schalen van de infrastructuur. Als u een beheerd lab-type wilt maken, zoals een leslokaal Lab, moet u eerst een Lab-account maken voor uw organisatie. Het lab-account fungeert als het centrale account waarin alle labs in de organisatie worden beheerd. 

Wanneer u Azure-resources in deze beheerde labs maakt en gebruikt, worden deze door de service in interne abonnementen van Microsoft gemaakt en beheerd. Ze worden niet in uw eigen Azure-abonnement gemaakt. De service houdt het gebruik van deze resources bij in de interne Microsoft-abonnementen. Dit gebruik wordt weer in rekening gebracht op uw Azure-abonnement dat het lab-account bevat.   

Hier volgen enkele van de **gebruikscases voor beheerde labtypen**: 

- Bied leerlingen en studenten een lab met virtuele machines die zijn geconfigureerd met exact wat nodig is voor een les of college. Geef elke leerling of student een beperkt aantal uren voor het gebruik van de VM's voor hun huiswerk of persoonlijke projecten.
- Stel een pool in van hoogwaardige reken-VM’s om rekenintensief of grafisch-intensief onderzoek uit te voeren. Voer de virtuele machines uit, indien nodig, en schoon de machines op wanneer u klaar bent. 
- Breng het fysieke computerlab van uw school over naar de cloud. U kunt het aantal VM's automatisch schalen tot de drempelwaarde voor gebruik en kosten die u instelt voor het lab.  
- Richt snel een lab met virtuele machines in voor het hosten van een hackathon. Verwijder het lab met één muisklik als u klaar bent. 


## <a name="devtest-labs"></a>DevTest Labs
Mogelijk hebt u scenario's waarin u alle infrastructuur en configuratie zelf wilt beheren binnen uw eigen abonnement. Hiervoor kunt u in Azure Portal een lab maken met Azure DevTest Labs. Voor deze Labs hoeft u geen Lab-account te maken. Deze labs worden niet weergegeven in het lab-account (wat voor de beheerde labs bestaat).  

Hier volgen enkele **gebruikscases van DevTest Labs**: 

- Richt snel een lab met virtuele machines in voor het hosten van een hackathon of een praktische sessie op een conferentie. Verwijder het lab met één muisklik als u klaar bent. 
- Maak een pool met virtuele machines die zijn geconfigureerd met uw toepassing en laat uw team eenvoudig gebruikmaken van een virtuele machine voor bug-bashes.  
- Geef ontwikkelaars virtuele machines die zijn geconfigureerd met alle hulpprogramma's die ze nodig hebben. Maak een schema voor automatisch starten en afsluiten om kosten te minimaliseren. 
- Maak herhaaldelijk een lab met testmachines als onderdeel van uw implementatie. Test uw meest recente bits en ruim de testmachines op wanneer u klaar bent. 
- Stel tal van anders geconfigureerde virtuele machines en meerdere testagenten in voor het testen van de schaal en prestaties. 
- Bied uw klanten trainingssessies aan met een lab dat is geconfigureerd met de nieuwste versie van uw product. Geef elke klant een beperkt aantal uren voor het gebruik van het lab. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Beheerde Lab-typen versus DevTest Labs
In de volgende tabel worden twee soorten testlaboratoria vergeleken die worden ondersteund door Azure Lab Services: 

| Functies | Beheerde labtypen | DevTest Labs |
| -------- | ----------------- | ---------- |
| Beheer van Azure-infrastructuur in het lab. |  Automatisch beheerd door de service | U beheert zelf  |
| Ingebouwde tolerantie voor problemen in de infrastructuur | Automatisch afgehandeld door de service | U beheert zelf  |
| Abonnementsbeheer | Service verwerkt de toewijzing van resources binnen Microsoft-abonnementen die de service ondersteunen. Schalen wordt automatisch afgehandeld door de service. | U beheert zelf in uw eigen Azure-abonnement. Er is geen automatische schaling van abonnementen. |
| Implementatie van Azure Resource Manager in het lab | Niet beschikbaar | Beschikbaar |

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen: 

- [Over klassikale Labs](./classroom-labs/classroom-labs-overview.md)
- [Over DevTest Labs](devtest-lab-overview.md)
