---
title: Beveiligde toepassingen ontwerpen op Microsoft Azure
description: In dit artikel worden best practices besproken om rekening mee te houden tijdens de vereisten- en ontwerpfasen van uw webtoepassingsproject.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 889897cfd4dc8714ae3aea556f0924c9dbcd7825
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299411"
---
# <a name="design-secure-applications-on-azure"></a>Beveiligde toepassingen ontwerpen op Azure
In dit artikel presenteren we beveiligingsactiviteiten en besturingselementen om rekening mee te houden wanneer u toepassingen voor de cloud ontwerpt. Trainingsbronnen en beveiligingsvragen en concepten waarmee u rekening moet houden tijdens de vereisten en ontwerpfasen van de Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) komen aan bod. Het doel is om u te helpen bij het definiëren van activiteiten en Azure-services die u gebruiken om een veiligere toepassing te ontwerpen.

De volgende SDL-fasen komen aan bod in dit artikel:

- Training
- Vereisten
- Ontwerp

## <a name="training"></a>Training
Voordat u begint met het ontwikkelen van uw cloudtoepassing, neemt u de tijd om de beveiliging en privacy op Azure te begrijpen. Door deze stap te nemen, u het aantal en de ernst van misbruikbare kwetsbaarheden in uw toepassing verminderen. U zult meer bereid zijn om adequaat te reageren op de steeds veranderende dreiging landschap.

Gebruik de volgende bronnen tijdens de trainingsfase om vertrouwd te raken met de Azure-services die beschikbaar zijn voor ontwikkelaars en met aanbevolen procedures voor beveiliging op Azure:

  - [De handleiding van de ontwikkelaar voor Azure](https://azure.microsoft.com/campaigns/developer-guide/) laat zien hoe u aan de slag met Azure. De gids laat u zien welke services u gebruiken om uw toepassingen uit te voeren, uw gegevens op te slaan, informatie op te nemen, IoT-apps te bouwen en uw oplossingen op een efficiëntere en veiligere manier te implementeren.

  - [Aan de slag gids voor Azure-ontwikkelaars](../../guides/developer/azure-developer-guide.md) biedt essentiële informatie voor ontwikkelaars die op zoek zijn om aan de slag met behulp van het Azure-platform voor hun ontwikkelingsbehoeften.

  - [SDK's en hulpprogramma's](https://docs.microsoft.com/azure/index?pivot=sdkstools) beschrijven de hulpprogramma's die beschikbaar zijn op Azure.

  - [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) biedt hulpprogramma's voor ontwikkelingssamenwerking. De tools omvatten krachtige pijplijnen, gratis Git-repositories, configureerbare Kanban-borden en uitgebreide geautomatiseerde en cloudgebaseerde belastingstests.
    Het [DevOps Resource Center](https://docs.microsoft.com/azure/devops/learn/) combineert onze resources voor het leren van DevOps-praktijken, Git-versiebeheer, agile methoden, hoe we werken met DevOps bij Microsoft en hoe u uw eigen DevOps-progressie beoordelen.

  - [Top 5 beveiligingsitems om rekening mee te houden voordat](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) u naar de productie gaat, laten zien hoe u uw webtoepassingen op Azure beveiligen en uw apps beschermen tegen de meest voorkomende en gevaarlijke aanvallen op webtoepassingen.

  - [Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) is een verzameling scripts, hulpprogramma's, extensies en automatiseringen die tegemoetkomt aan de uitgebreide Azure-abonnements- en resourcebeveiligingsbehoeften van DevOps-teams die uitgebreide automatisering gebruiken. De Secure DevOps Kit voor Azure kan u laten zien hoe u beveiliging probleemloos integreren in uw native DevOps-workflows. De kit richt zich op tools zoals beveiligingsverificatietests (SVT's), die ontwikkelaars kunnen helpen bij het schrijven van veilige code en het testen van de veilige configuratie van hun cloudtoepassingen in de coderings- en vroege ontwikkelingsfase.

  - [Aanbevolen procedures voor beveiliging voor Azure-oplossingen](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) bieden een verzameling aanbevolen procedures voor beveiliging om te gebruiken bij het ontwerpen, implementeren en beheren van uw cloudoplossingen met azure.

## <a name="requirements"></a>Vereisten
De definitiefase van vereisten is een cruciale stap in het definiëren van wat uw toepassing is en wat het zal doen wanneer deze wordt vrijgegeven. De vereistenfase is ook een tijd om na te denken over de beveiligingscontroles die u in uw toepassing inbouwt. Tijdens deze fase begint u ook met de stappen die u in de hele SDL zult nemen om ervoor te zorgen dat u een beveiligde toepassing vrijgeeft en implementeert.

### <a name="consider-security-and-privacy-issues"></a>Houd rekening met beveiligings- en privacykwesties
Deze fase is de beste tijd om fundamentele veiligheid en privacy kwesties te overwegen. Het definiëren van aanvaardbare niveaus van veiligheid en privacy aan het begin van een project helpt een team:

- Inzicht in risico's in verband met beveiligingsproblemen.
- Identificeer en los beveiligingsbugs op tijdens de ontwikkeling.
- Pas gevestigde niveaus van beveiliging en privacy toe gedurende het hele project.

Wanneer u de vereisten voor uw toepassing schrijft, moet u rekening houden met beveiligingsbesturingselementen die uw toepassing en gegevens veilig kunnen houden.

### <a name="ask-security-questions"></a>Stel beveiligingsvragen
Stel beveiligingsvragen zoals:

  - Bevat mijn aanvraag gevoelige gegevens?

  - Verzamelt of bewaart mijn toepassing gegevens die mij verplichten om te voldoen aan industrienormen en nalevingsprogramma's zoals de [Federal Financial Institution Examination Council (FFIEC)](../blueprints/ffiec-analytics-overview.md) of de Payment Card Industry Data Security Standards [(PCI DSS)](../blueprints/pcidss-analytics-overview.md)?

  - Verzamelt of bevat mijn toepassing gevoelige persoonlijke of klantgegevens die alleen of met andere informatie kunnen worden gebruikt om één persoon te identificeren, contact op te nemen of te lokaliseren?

  - Verzamelt of bevat mijn aanvraag gegevens die kunnen worden gebruikt om toegang te krijgen tot de medische, educatieve, financiële of werkgelegenheidsinformatie van een individu? Het identificeren van de gevoeligheid van uw gegevens tijdens de vereistenfase helpt u uw gegevens te classificeren en de gegevensbeschermingsmethode te identificeren die u voor uw toepassing zult gebruiken.

  - Waar en hoe worden mijn gegevens opgeslagen? Bedenk hoe u de opslagservices controleert die uw toepassing gebruikt voor onverwachte wijzigingen (zoals tragere responstijden). Zult u in staat zijn om logging te beïnvloeden om meer gedetailleerde gegevens te verzamelen en een probleem in de diepte te analyseren?

  - Zal mijn aanvraag beschikbaar zijn voor het publiek (alleen op het internet) of intern? Als uw toepassing beschikbaar is voor het publiek, hoe beschermt u de gegevens die kunnen worden verzameld tegen het op de verkeerde manier worden gebruikt? Als uw toepassing alleen intern beschikbaar is, moet u overwegen wie in uw organisatie toegang moet hebben tot de toepassing en hoe lang ze toegang moeten hebben.

  - Begrijpt u uw identiteitsmodel voordat u begint met het ontwerpen van uw toepassing? Hoe gaat u bepalen dat gebruikers zijn wie ze zeggen dat ze zijn en wat een gebruiker is gemachtigd om te doen?

  - Voert mijn applicatie gevoelige of belangrijke taken uit (zoals het overmaken van geld, het ontgrendelen van deuren of het leveren van medicijnen)?
    Bedenk hoe u valideert dat de gebruiker die een gevoelige taak uitvoert, gemachtigd is om de taak uit te voeren en hoe u zich zult verifiëren dat de persoon is wie hij zegt dat hij of zij is. Autorisatie (AuthZ) is de handeling van het verlenen van een geverifieerde security principal toestemming om iets te doen. Authenticatie (AuthN) is de handeling van het uitdagen van een partij voor legitieme referenties.

  - Voert mijn applicatie riskante softwareactiviteiten uit, zoals het toestaan van gebruikers om bestanden of andere gegevens te uploaden of te downloaden? Als uw toepassing riskante activiteiten uitvoert, moet u overwegen hoe uw toepassing gebruikers beschermt tegen het verwerken van schadelijke bestanden of gegevens.

### <a name="review-owasp-top-10"></a>Review OWASP top 10
Overweeg de [<span class="underline">OWASP Top 10 Application Security Risks</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)te bekijken.
De OWASP Top 10 richt zich op kritieke beveiligingsrisico's voor webapplicaties.
Bewustzijn van deze beveiligingsrisico's kan u helpen bij het nemen van vereisten en het ontwerpen van beslissingen die deze risico's in uw toepassing minimaliseren.

Nadenken over beveiligingscontroles om inbreuken te voorkomen is belangrijk.
U wilt er echter ook [van uitgaan dat er een inbreuk](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) optreedt. Ervan uitgaande dat een inbreuk helpt bij het beantwoorden van een aantal belangrijke vragen over de veiligheid op voorhand, zodat ze niet hoeven te worden beantwoord in een noodsituatie:

  - Hoe bespeur ik een aanval?

  - Wat moet ik doen als er een aanval of overtreding is?

  - Hoe ga ik herstellen van de aanval, zoals datalekken of knoeien?

## <a name="design"></a>Ontwerp

De ontwerpfase is van cruciaal belang voor het vaststellen van best practices voor ontwerp en functionele specificaties. Het is ook van cruciaal belang voor het uitvoeren van risicoanalyse die helpt bij het beperken van beveiligings- en privacyproblemen tijdens een project.

Wanneer u beveiligingsvereisten hebt en veilige ontwerpconcepten gebruikt, u mogelijkheden voor een beveiligingslek vermijden of minimaliseren. Een beveiligingslek is een vergissing in het ontwerp van de toepassing waarmee een gebruiker kwaadaardige of onverwachte acties kan uitvoeren nadat uw toepassing is vrijgegeven.

Denk tijdens de ontwerpfase ook na over hoe je beveiliging in lagen toepassen; een niveau van verdediging is niet per se genoeg. Wat gebeurt er als een aanvaller voorbij uw webapplicatie firewall (WAF) komt? Je wilt een andere veiligheidscontrole om je te verdedigen tegen die aanval.

Met dit in gedachten bespreken we de volgende veilige ontwerpconcepten en de beveiligingscontroles die u moet aanpakken wanneer u beveiligde toepassingen ontwerpt:

- Gebruik een beveiligde coderingsbibliotheek en een softwareframework.
- Scan naar kwetsbare componenten.
- Gebruik bedreigingsmodellering tijdens het ontwerp van toepassingen.
- Verminder je aanvalsoppervlak.
- Adopteer een identiteitsbeleid als primaire beveiligingsperimeter.
- Opnieuw verificatie vereisen voor belangrijke transacties.
- Gebruik een key management oplossing om sleutels, referenties en andere geheimen te beveiligen.
- Bescherm gevoelige gegevens.
- Implementeer fail-safe maatregelen.
- Profiteer van fout- en uitzonderingsafhandeling.
- Gebruik logboekregistratie en waarschuwingen.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Een beveiligde coderingsbibliotheek en een softwareframework gebruiken

Gebruik voor ontwikkeling een beveiligde coderingsbibliotheek en een softwareframework dat beveiliging heeft ingesloten. Ontwikkelaars kunnen bestaande, bewezen functies (encryptie, invoersanitaire voorzieningen, uitvoercodering, sleutels of verbindingstekenreeksen, en iets anders dat als een beveiligingscontrole wordt beschouwd) gebruiken in plaats van beveiligingscontroles vanaf nul te ontwikkelen. Dit helpt beschermen tegen beveiligingsgerelateerde ontwerp- en implementatiefouten.

Zorg ervoor dat u de nieuwste versie van uw framework en alle beveiligingsfuncties gebruikt die beschikbaar zijn in het framework. Microsoft biedt een uitgebreide [reeks ontwikkeltools](https://azure.microsoft.com/product-categories/developer-tools/) voor alle ontwikkelaars, die op elk platform of taal werken, om cloudtoepassingen te leveren. U coderen met de taal van uw keuze door te kiezen uit verschillende [SDKs.](https://azure.microsoft.com/downloads/)
U profiteren van volledig uitgeruste geïntegreerde ontwikkelomgevingen (IdEs) en editors met geavanceerde foutopsporingsmogelijkheden en ingebouwde Azure-ondersteuning.

Microsoft biedt verschillende [talen, frameworks en hulpprogramma's](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all) die u gebruiken om toepassingen op Azure te ontwikkelen. Een voorbeeld is [Azure voor .NET- en .NET Core-ontwikkelaars](https://docs.microsoft.com/dotnet/azure/). Voor elke taal en framework die we aanbieden, vindt u quickstarts, tutorials en API-verwijzingen om u te helpen snel aan de slag te gaan.

Azure biedt een verscheidenheid aan services die u gebruiken om websites en webtoepassingen te hosten. Met deze services u zich ontwikkelen in uw favoriete taal, of dat nu .NET, .NET Core, Java, Ruby, Node.js, PHP of Python is.
[Azure App Service Web Apps](../../app-service/overview.md) (Web Apps) is een van deze services.

Web Apps voegt de kracht van Microsoft Azure toe aan uw toepassing. Het omvat beveiliging, taakverdeling, automatisch schalen en geautomatiseerd beheer. U ook profiteren van DevOps-mogelijkheden in Web Apps, zoals pakketbeheer, faseringsomgevingen, aangepaste domeinen, SSL/TLS-certificaten en continue implementatie van Azure DevOps, GitHub, Docker Hub en andere bronnen.

Azure biedt andere services die u gebruiken om websites en webtoepassingen te hosten. Voor de meeste scenario's is Web Apps de beste keuze. Voor een microservicearchitectuur u [Azure Service Fabric overwegen.](https://azure.microsoft.com/documentation/services/service-fabric)
Als u meer controle wilt over de virtuele machines waarop uw code wordt uitgevoerd, kunt u [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) overwegen.
Zie een [vergelijking van Azure App Service, Virtual Machines, Service Fabric en Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree)voor meer informatie over hoe u kiezen tussen deze Azure-services.

### <a name="apply-updates-to-components"></a>Updates toepassen op onderdelen

Om kwetsbaarheden te voorkomen, moet u continu zowel uw client- als servercomponenten (bijvoorbeeld frameworks en bibliotheken) en hun afhankelijkheden voor updates inventariseren. Nieuwe kwetsbaarheden en bijgewerkte softwareversies worden continu vrijgegeven. Zorg ervoor dat u een doorlopend plan hebt om updates of configuratiewijzigingen toe te passen op de bibliotheken en onderdelen die u gebruikt.

Zie de Pagina [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) over het gebruik van componenten met bekende [kwetsbaarheden](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) voor toolsuggesties. U zich ook abonneren op e-mailwaarschuwingen voor beveiligingsproblemen die verband houden met onderdelen die u gebruikt.

### <a name="use-threat-modeling-during-application-design"></a>Threat modeling gebruiken tijdens het ontwerpen van toepassingen

Bedreigingsmodellering is het proces van het identificeren van potentiële beveiligingsbedreigingen voor uw bedrijf en toepassing en er vervolgens voor zorgen dat de juiste oplossingen aanwezig zijn. De SDL geeft aan dat teams zich moeten bezighouden met bedreigingsmodellering tijdens de ontwerpfase, wanneer het oplossen van potentiële problemen relatief eenvoudig en kosteneffectief is. Het gebruik van bedreigingsmodellering in de ontwerpfase kan uw totale ontwikkelingskosten sterk verlagen.

Om het proces voor bedreigingsmodellering te vergemakkelijken, hebben we de [SDL Threat Modeling Tool](threat-modeling-tool.md) ontworpen met niet-beveiligingsexperts in het achterhoofd. Deze tool maakt bedreigingsmodellering eenvoudiger voor alle ontwikkelaars door duidelijke richtlijnen te geven over het maken en analyseren van bedreigingsmodellen.

Het modelleren van het ontwerp van de toepassing en het opsommen van [STRIDE-bedreigingen](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) — Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service en Elevation of Privilege — over alle vertrouwensgrenzen heen, heeft bewezen dat het een effectieve manier is om ontwerpfouten al vroeg te vangen. In de volgende tabel worden de STRIDE-bedreigingen weergegeven en worden enkele voorbeelden gegeven van de functies die door Azure worden geleverd. Deze oplossingen werken niet in elke situatie.

| Bedreiging | Beveiligingseigenschap | Potentiële Azure-platformbeperking |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Adresvervalsing (spoofing)               | Authentication        | [HTTPS-verbindingen vereisen](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Knoeien              | Gegevensintegriteit             | SSL/TLS-certificaten valideren. Toepassingen die SSL/TLS gebruiken, moeten de X.509-certificaten van de entiteiten waarmee ze verbinding maken volledig verifiëren. Gebruik Azure Key Vault-certificaten om [uw x509-certificaten](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates)te beheren. |
| Verwerping            | Niet-verwerping       | [Azure-bewaking en -diagnose inschakelen](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).|
| Openbaarmaking van informatie | Vertrouwelijkheid       | Versleutel gevoelige gegevens [in rust](../fundamentals/encryption-atrest.md) en [onderweg.](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) |
| Denial of Service      | Beschikbaarheid          | Monitor prestatiestatistieken voor mogelijke denial of service-omstandigheden. Verbindingsfilters implementeren. [Azure DDoS-beveiliging](../../virtual-network/ddos-protection-overview.md#next-steps), in combinatie met best practices voor het ontwerpen van toepassingen, biedt bescherming tegen DDoS-aanvallen.|
| Verhoging van privilege | Autorisatie         | Gebruik Azure <span class="underline"> </span> Active Directory [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md).|

### <a name="reduce-your-attack-surface"></a>Verminder uw aanvalsoppervlak

Een aanvalsoppervlak is de totale som van waar potentiële kwetsbaarheden kunnen optreden. In deze paper richten we ons op het aanvalsoppervlak van een toepassing.
De focus ligt op het beschermen van een toepassing tegen aanvallen. Een eenvoudige en snelle manier om uw aanvalsoppervlak te minimaliseren, is door ongebruikte bronnen en code uit uw toepassing te verwijderen. Hoe kleiner uw toepassing, hoe kleiner uw aanvaloppervlak. Verwijder bijvoorbeeld:

- Code voor functies die u nog niet hebt vrijgegeven.
- Debugging-ondersteuningscode.
- Netwerkinterfaces en protocollen die niet worden gebruikt of die zijn afgeschaft.
- Virtuele machines en andere resources die u niet gebruikt.

Het regelmatig opruimen van uw resources en ervoor zorgen dat u ongebruikte code verwijdert, zijn geweldige manieren om ervoor te zorgen dat er minder mogelijkheden zijn voor kwaadwillende actoren om aan te vallen.

Een meer gedetailleerde en diepgaande manier om uw aanvaloppervlak te verminderen is het voltooien van een aanval oppervlak analyse. Een aanvalsoppervlakteanalyse helpt u de delen van een systeem in kaart te brengen die moeten worden gecontroleerd en getest op beveiligingsproblemen.

Het doel van een aanval oppervlak analyse is om de risicogebieden in een toepassing te begrijpen, zodat ontwikkelaars en security specialisten zich bewust zijn van welke delen van de toepassing zijn open voor aanvallen. Vervolgens u manieren vinden om dit potentieel te minimaliseren, bijhouden wanneer en hoe het aanvalsoppervlak verandert en wat dit betekent vanuit een risicoperspectief.

Een aanvalsoppervlakteanalyse helpt u bij het identificeren van:

- Functies en onderdelen van het systeem die u nodig hebt om te controleren en te testen op beveiligingsproblemen.
- Gebieden met een hoog risico van code die bescherming-diepgaande bescherming vereisen (delen van het systeem dat u moet verdedigen).
- Wanneer u het aanvalsoppervlak wijzigt en een dreigingsbeoordeling moet vernieuwen.

Voor het verminderen van de mogelijkheden voor aanvallers om misbruik te maken van een potentiële zwakke plek of kwetsbaarheid, moet u het totale aanvalsoppervlak van uw toepassing grondig analyseren. Het omvat ook het uitschakelen of beperken van de toegang tot systeemservices, het toepassen van het principe van de minste bevoegdheden, en het gebruik van gelaagde verdediging waar mogelijk.

We bespreken [het uitvoeren van een aanval oppervlak review](secure-develop.md#conduct-attack-surface-review) tijdens de verificatiefase van de SDL.

> [!NOTE]
> **Wat is het verschil tussen threat modeling en attack surface analyse?**
Bedreigingsmodellering is het proces van het identificeren van potentiële beveiligingsbedreigingen voor uw toepassing en ervoor te zorgen dat de juiste oplossingen tegen de bedreigingen zijn op zijn plaats. Aanvaloppervlakteanalyse identificeert gebieden met een hoog risico van code die open staan voor aanvallen. Het gaat om het vinden van manieren om gebieden met een hoog risico van uw toepassing te verdedigen en die gebieden van code te controleren en te testen voordat u de toepassing implementeert.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Een identiteitsbeleid aannemen als primaire beveiligingsperimeter

Wanneer u cloudtoepassingen ontwerpt, is het belangrijk om uw focus op de beveiligingsperimeter uit te breiden van een netwerkgerichte benadering naar een identiteitsgerichte benadering. Historisch gezien was de primaire on-premises beveiligingsperimeter het netwerk van een organisatie. De meeste on-premises beveiligingsontwerpen gebruiken het netwerk als de primaire beveiligingsspil. Voor cloudtoepassingen u beter worden bediend door identiteit als de primaire beveiligingsperimeter te beschouwen.

Dingen die u doen om een identiteitsgerichte benadering te ontwikkelen voor het ontwikkelen van webapplicaties:

- Multifactorauthenticatie afdwingen voor gebruikers.
- Gebruik krachtige verificatie- en autorisatieplatforms.
- Pas het principe van de minste bevoegdheden toe.
- Implementeer just-in-time toegang.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Meervoudige verificatie afdwingen voor gebruikers

Gebruik tweestapsverificatie. Tweestapsverificatie is de huidige standaard voor verificatie en autorisatie omdat hiermee de beveiligingsproblemen worden vermeden die inherent zijn aan gebruikersnamen- en wachtwoordtypen van verificatie. Toegang tot de Azure-beheerinterfaces (Azure portal/remote PowerShell) en tot klantgerichte services moet worden ontworpen en geconfigureerd om [Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)te gebruiken.

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Sterke verificatie- en autorisatieplatforms gebruiken

Gebruik door platforms geleverde verificatie- en autorisatiemechanismen in plaats van aangepaste code. Dit komt omdat het ontwikkelen van aangepaste verificatiecode foutgevoelig kan zijn. Commerciële code (bijvoorbeeld van Microsoft) wordt vaak uitgebreid gecontroleerd op beveiliging. [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) is de Azure-oplossing voor identiteits- en toegangsbeheer. Deze Azure AD-hulpprogramma's en -services helpen bij veilige ontwikkeling:

- [Azure AD-identiteitsplatform (Azure AD voor ontwikkelaars)](../../active-directory/develop/about-microsoft-identity-platform.md) is een cloudidentiteitsservice die ontwikkelaars gebruiken om apps te bouwen die gebruikers veilig aanmelden. Azure AD helpt ontwikkelaars die lob-apps (single-tenant, line-of-business) en ontwikkelaars bouwen die multi-tenant-apps willen ontwikkelen. Naast de basisaanmelding kunnen apps die zijn gebouwd met Azure AD Microsoft API's en aangepaste API's aanroepen die zijn gebouwd op het Azure AD-platform. Het Azure AD-identiteitsplatform ondersteunt industriestandaardprotocollen zoals OAuth 2.0 en OpenID Connect.

- [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) is een identiteitsbeheerservice die u gebruiken om aan te passen en te bepalen hoe klanten zich aanmelden, zich aanmelden en hun profielen beheren wanneer ze uw toepassingen gebruiken. Dit omvat applicaties die zijn ontwikkeld voor onder andere iOS, Android en .NET. Azure AD B2C maakt deze acties mogelijk en beschermt tegelijkertijd de identiteit van klanten.

#### <a name="apply-the-principle-of-least-privilege"></a>Het principe van de minste bevoegdheid toepassen

Het concept van [de minste privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege) betekent dat gebruikers het precieze niveau van toegang en controle die ze nodig hebben om hun werk te doen en niets meer.

Heeft een softwareontwikkelaar domeinbeheerdersrechten nodig? Heeft een administratief medewerker toegang nodig tot administratieve controles op zijn personal computer? Het evalueren van de toegang tot software is niet anders. Als u [op rollen gebaseerde toegangscontrole (RBAC)](../../role-based-access-control/overview.md) gebruikt om gebruikers verschillende vaardigheden en autoriteit in uw toepassing te geven, zou u niet iedereen toegang geven tot alles. Door de toegang tot wat voor elke rol nodig is te beperken, beperkt u het risico dat zich een beveiligingsprobleem voordoet.

Zorg ervoor dat uw toepassing [de minste bevoegdheden](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) afdwingt in de toegangspatronen.

> [!NOTE]
>  De regels van de minste bevoegdheden moeten van toepassing zijn op de software en op de mensen die de software maken. Softwareontwikkelaars kunnen een enorm risico vormen voor de IT-beveiliging als ze te veel toegang krijgen. De gevolgen kunnen ernstig zijn als een ontwikkelaar kwade bedoelingen heeft of te veel toegang krijgt. We raden aan om de regels van de minste bevoegdheden toe te passen op ontwikkelaars gedurende de hele ontwikkelingslevenscyclus.

#### <a name="implement-just-in-time-access"></a>Just-in-time toegang implementeren

Implementeer *just-in-time* (JIT) toegang om de belichtingstijd van privileges verder te verlagen. Gebruik [Azure AD Privileged Identity Management](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-build-visibility-and-take-full-control-of-admin-activity) om:

- Geef gebruikers de machtigingen die ze alleen JIT nodig hebben.
- Rolrollen toewijzen voor een verkorte duur met het vertrouwen dat de bevoegdheden automatisch worden ingetrokken.

### <a name="require-re-authentication-for-important-transactions"></a>Opnieuw verificatie vereisen voor belangrijke transacties

[Cross-site request vervalsing](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (ook bekend als *XSRF* of *CSRF)* is een aanval op web-hosted apps waarbij een kwaadaardige web-app de interactie tussen een clientbrowser en een web-app die die browser vertrouwt beïnvloedt. Cross-site request vervalsingsaanvallen zijn mogelijk omdat webbrowsers bij elk verzoek automatisch bepaalde soorten authenticatietokens naar een website verzenden.
Deze vorm van uitbuiting wordt ook wel een aanval met *één klik* of *sessierijden* genoemd, omdat de aanval gebruikmaakt van de eerder geverifieerde sessie van de gebruiker.

De beste manier om te verdedigen tegen dit soort aanvallen is om de gebruiker te vragen om iets dat alleen de gebruiker kan bieden voor elke belangrijke transactie, zoals een aankoop, account deactivering, of een wachtwoord te wijzigen. U de gebruiker vragen om zijn wachtwoord opnieuw in te voeren, een captcha in te vullen of een geheim token in te dienen dat alleen de gebruiker zou hebben. De meest voorkomende aanpak is het geheime token.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Gebruik een oplossing voor belangrijk beheer om sleutels, referenties en andere geheimen te beveiligen

Het verliezen van sleutels en referenties is een veel voorkomend probleem. Het enige wat erger is dan het verliezen van uw sleutels en referenties is het hebben van een onbevoegde partij toegang krijgen tot hen. Aanvallers kunnen profiteren van geautomatiseerde en handmatige technieken om sleutels en geheimen te vinden die zijn opgeslagen in coderepositories zoals GitHub. Plaats geen sleutels en geheimen in deze openbare code repositories of op een andere server.

Plaats uw sleutels, certificaten, geheimen en verbindingstekenreeksen altijd in een belangrijke beheeroplossing. U een gecentraliseerde oplossing gebruiken waarin sleutels en geheimen worden opgeslagen in hardwarebeveiligingsmodules (HSM's). Azure biedt u een HSM in de cloud met [Azure Key Vault.](../../key-vault/key-vault-overview.md)

Key Vault is een *geheime winkel:* het is een gecentraliseerde cloudservice voor het opslaan van toepassingsgeheimen. Key Vault houdt uw vertrouwelijke gegevens veilig door toepassingsgeheimen op één, centrale locatie te bewaren en veilige toegang, machtigingencontrole en toegangslogboekregistratie te bieden.

Geheimen worden opgeslagen in individuele *kluizen.* Elke kluis heeft zijn eigen configuratie- en beveiligingsbeleid om de toegang te beheren. U krijgt uw gegevens via een REST API of via een client SDK die beschikbaar is voor de meeste programmeertalen.

> [!IMPORTANT]
> Azure Key Vault is ontworpen om configuratiegeheimen voor servertoepassingen op te slaan. Het is niet bedoeld voor het opslaan van gegevens die eigendom zijn van app-gebruikers. Dit wordt weerspiegeld in de prestatiekenmerken, de API en het kostenmodel.
>
> Gebruikersgegevens moeten elders worden opgeslagen, zoals in een Azure SQL Database-exemplaar met Transparante gegevensversleuteling (TDE) of in een opslagaccount dat Azure Storage Service Encryption gebruikt. Geheimen die door uw toepassing worden gebruikt om toegang te krijgen tot deze gegevensarchieven, kunnen worden bewaard in Azure Key Vault.

### <a name="protect-sensitive-data"></a>Gevoelige gegevens beveiligen

Het beschermen van gegevens is een essentieel onderdeel van uw beveiligingsstrategie.
Door uw gegevens te classificeren en uw gegevensbeschermingsbehoeften te identificeren, u uw app ontwerpen met het oog op gegevensbeveiliging. Door opgeslagen gegevens te classificeren (categoriseren) op gevoeligheid en bedrijfsimpact kunnen ontwikkelaars de risico's bepalen die aan gegevens zijn gekoppeld.

Label alle toepasselijke gegevens als gevoelig wanneer u uw gegevensindelingen ontwerpt. Zorg ervoor dat de toepassing de toepasselijke gegevens als gevoelig behandelt. Deze praktijken kunnen u helpen uw gevoelige gegevens te beschermen:

- Gebruik encryptie.
- Vermijd harde codering geheimen zoals sleutels en wachtwoorden.
- Zorg ervoor dat toegangsbesturingselementen en controle aanwezig zijn.

#### <a name="use-encryption"></a>Versleuteling gebruiken

Het beschermen van gegevens moet een essentieel onderdeel zijn van uw beveiligingsstrategie.
Als uw gegevens worden opgeslagen in een database of als ze heen en weer bewegen tussen locaties, gebruikt u versleuteling van [gegevens in rust](../fundamentals/encryption-atrest.md) (terwijl u zich in de database bevindt) en versleuteling van gegevens [onderweg](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) (op weg naar en van de gebruiker, de database, een API of serviceeindpunt). We raden u aan altijd SSL/TLS-protocollen te gebruiken om gegevens uit te wisselen. Zorg ervoor dat u de nieuwste versie van TLS gebruikt voor versleuteling (momenteel is dit versie 1.2).

#### <a name="avoid-hard-coding"></a>Vermijd harde codering

Sommige dingen moeten nooit hard-gecodeerd in uw software. Enkele voorbeelden zijn hostnamen of IP-adressen, URL's, e-mailadressen, gebruikersnamen, wachtwoorden, opslagaccountsleutels en andere cryptografische sleutels. Overweeg vereisten te implementeren rond wat wel of niet hard gecodeerd kan worden in uw code, ook in de commentaarsecties van uw code.

Wanneer u opmerkingen in uw code plaatst, moet u ervoor zorgen dat u geen gevoelige informatie opslaat. Dit omvat uw e-mailadres, wachtwoorden, verbindingstekenreeksen, informatie over uw toepassing die alleen bekend zou zijn bij iemand in uw organisatie, en al het andere dat een aanvaller een voordeel kan geven bij het aanvallen van uw toepassing of organisatie .

In principe, ga ervan uit dat alles in uw ontwikkelingsproject zal worden openbare kennis wanneer het wordt ingezet. Vermijd het opnemen van gevoelige gegevens van welke aard dan ook in het project.

Eerder bespraken we [Azure Key Vault.](../../key-vault/key-vault-overview.md) U Key Vault gebruiken om geheimen zoals sleutels en wachtwoorden op te slaan in plaats van ze hard te coderen. Wanneer u Key Vault gebruikt in combinatie met beheerde identiteiten voor Azure-bronnen, heeft uw Azure-webapp eenvoudig en veilig toegang tot geheime configuratiewaarden zonder geheimen op te slaan in uw bronbeheer of -configuratie. Zie [Geheimen beheren in uw server-apps met Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/)voor meer informatie.

### <a name="implement-fail-safe-measures"></a>Implementeer fail-safe maatregelen

Uw toepassing moet [fouten](https://docs.microsoft.com/dotnet/standard/exceptions/) die optreden tijdens de uitvoering op een consistente manier kunnen verwerken. De toepassing moet vangen alle fouten en ofwel niet veilig of gesloten.

U moet er ook voor zorgen dat fouten worden geregistreerd met voldoende gebruikerscontext om verdachte of schadelijke activiteiten te identificeren. Logboeken moeten voldoende lang worden bewaard om vertraagde forensische analyse mogelijk te maken. Logboeken moeten in een formaat zijn dat gemakkelijk kan worden verbruikt door een oplossing voor logboekbeheer. Zorg ervoor dat waarschuwingen voor fouten die gerelateerd zijn aan beveiliging worden geactiveerd. Onvoldoende logging en monitoring stelt aanvallers in staat om systemen verder aan te vallen en persistentie te behouden.

### <a name="take-advantage-of-error-and-exception-handling"></a>Profiteer van fout- en uitzonderingsafhandeling

Het implementeren van de juiste fout- en [uitzonderingsafhandeling](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) is een belangrijk onderdeel van defensieve codering. Fout- en uitzonderingsafhandeling zijn essentieel om een systeem betrouwbaar en veilig te maken. Fouten in foutafhandeling kunnen leiden tot verschillende soorten beveiligingsproblemen, zoals het lekken van informatie naar aanvallers en het helpen van aanvallers om meer inzicht te krijgen in uw platform en ontwerp.

Zorg ervoor dat:

- U verwerkt uitzonderingen op een gecentraliseerde manier om dubbele [try/catch-blokken](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) in de code te voorkomen.

- Alle onverwachte gedragingen worden behandeld in de toepassing.

- Berichten die aan gebruikers worden weergegeven, lekken geen kritieke gegevens, maar bieden wel voldoende informatie om het probleem uit te leggen.

- Uitzonderingen worden geregistreerd en dat ze voldoende informatie bieden voor forensische of incidentresponseteams om te onderzoeken.

[Azure Logic Apps](../../logic-apps/logic-apps-overview.md) biedt een eersteklas ervaring voor [het afhandelen van fouten en uitzonderingen](../../logic-apps/logic-apps-exception-handling.md) die worden veroorzaakt door afhankelijke systemen. U Logic Apps gebruiken om werkstromen te maken om taken en processen te automatiseren die apps, gegevens, systemen en services integreren in ondernemingen en organisaties.

### <a name="use-logging-and-alerting"></a>Logboekregistratie en waarschuwing en waarschuwing gebruiken

[Meld](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) uw beveiligingsproblemen voor beveiligingsonderzoeken en activeer waarschuwingen over problemen om ervoor te zorgen dat mensen tijdig op de hoogte zijn van problemen. Schakel controle en logboekregistratie op alle onderdelen in. Auditlogs moeten de context van de gebruiker vastleggen en alle belangrijke gebeurtenissen identificeren.

Controleer of u geen gevoelige gegevens registreert die een gebruiker naar uw site verzendt. Voorbeelden van gevoelige gegevens zijn:

- Gebruikersreferenties
- Burgerservicenummers of andere identificerende informatie
- Creditcardnummers of andere financiële informatie
- Gezondheidsinformatie
- Privésleutels of andere gegevens die kunnen worden gebruikt om versleutelde informatie te decoderen
- Systeem- of toepassingsinformatie die kan worden gebruikt om de toepassing effectiever aan te vallen

Controleer of de toepassing gebruikersbeheergebeurtenissen controleert, zoals geslaagde en mislukte gebruikersaanmeldingen, wachtwoordresets, wachtwoordwijzigingen, accountuitsluiting en gebruikersregistratie. Logboekregistratie voor deze gebeurtenissen helpt u bij het detecteren en reageren op mogelijk verdacht gedrag. Het stelt u ook in staat om bedrijfsgegevens te verzamelen, zoals wie toegang heeft tot de toepassing.

## <a name="next-steps"></a>Volgende stappen
In de volgende artikelen raden we beveiligingscontroles en -activiteiten aan die u kunnen helpen bij het ontwikkelen en implementeren van beveiligde toepassingen.

- [Veilige toepassingen ontwikkelen](secure-develop.md)
- [Veilige toepassingen implementeren](secure-deploy.md)
