---
title: Aanbevolen procedures voor beveiligde PaaS-implementaties - Microsoft Azure
description: Lees best practices voor het ontwerpen, bouwen en beheren van beveiligde cloudtoepassingen op Azure en verkrijg de beveiligingsvoordelen van PaaS ten opzichte van andere cloudservicemodellen.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 8fd5a063683d09cb94b45205426871d880119cc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138023"
---
# <a name="securing-paas-deployments"></a>PaaS-implementaties beveiligen

In dit artikel vindt u informatie die u helpt:

- Inzicht in de beveiligingsvoordelen van hostingtoepassingen in de cloud
- De beveiligingsvoordelen van platform as a service (PaaS) ten opzichte van andere cloudservicemodellen evalueren
- Verander uw beveiligingsfocus van een netwerkgerichte naar een identiteitsgerichte perimeterbeveiligingsaanpak
- Algemene aanbevolen procedures voor PaaS-beveiliging implementeren

[Het ontwikkelen van veilige toepassingen op Azure](abstract-develop-secure-apps.md) is een algemene handleiding voor de beveiligingsvragen en -besturingselementen die u in elke fase van de levenscyclus van softwareontwikkeling moet overwegen bij het ontwikkelen van toepassingen voor de cloud.

## <a name="cloud-security-advantages"></a>Voordelen voor cloudbeveiliging
Het is belangrijk om de verdeling van de [verantwoordelijkheid](shared-responsibility.md) tussen u en Microsoft te begrijpen. On-premises bent u eigenaar van de hele stack, maar als u naar de cloud gaat, worden sommige verantwoordelijkheden overgedragen aan Microsoft.

Er zijn [beveiligingsvoordelen aan het zijn in de cloud.](shared-responsibility.md#cloud-security-advantages) In een on-premises omgeving hebben organisaties waarschijnlijk onvervulde verantwoordelijkheden en beperkte middelen beschikbaar om te investeren in beveiliging, waardoor een omgeving ontstaat waarin aanvallers kwetsbaarheden in alle lagen kunnen misbruiken.

Organisaties zijn in staat om hun detectie- en reactietijden voor bedreigingen te verbeteren door gebruik te maken van de cloudgebaseerde beveiligingsmogelijkheden en cloudintelligence van een provider.  Door verantwoordelijkheden te verschuiven naar de cloudprovider, kunnen organisaties meer beveiligingsdekking krijgen, waardoor ze beveiligingsresources en budget kunnen herverdelen naar andere zakelijke prioriteiten.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Beveiligingsvoordelen van een PaaS-cloudservicemodel
Laten we eens kijken naar de beveiligingsvoordelen van een Azure PaaS-implementatie versus on-premises.

![Beveiligingsvoordelen van PaaS](./media/paas-deployments/advantages-of-paas.png)

Beginnend aan de onderkant van de stack, de fysieke infrastructuur, beperkt Microsoft veelvoorkomende risico's en verantwoordelijkheden. Omdat de Microsoft-cloud voortdurend wordt gecontroleerd door Microsoft, is het moeilijk aan te vallen. Het heeft geen zin voor een aanvaller om de Microsoft-cloud als doelwit na te streven. Tenzij de aanvaller heeft veel geld en middelen, de aanvaller is waarschijnlijk om door te gaan naar een ander doel.  

In het midden van de stack is er geen verschil tussen een PaaS-implementatie en on-premises. Op de toepassingslaag en de account- en toegangsbeheerlaag heb je vergelijkbare risico's. In de volgende stappen sectie van dit artikel, zullen we u begeleiden naar best practices voor het elimineren of minimaliseren van deze risico's.

Aan de bovenkant van de stack, data governance en rights management, neemt u een risico dat kan worden beperkt door key management. (Sleutelbeheer wordt behandeld in best practices.) Hoewel sleutelbeheer een extra verantwoordelijkheid is, hebt u gebieden in een PaaS-implementatie die u niet meer hoeft te beheren, zodat u resources verschuiven naar sleutelbeheer.

Het Azure-platform biedt u ook sterke DDoS-bescherming door gebruik te maken van verschillende netwerkgebaseerde technologieën. Alle soorten netwerkgebaseerde DDoS-beveiligingsmethoden hebben echter hun grenzen op basis van per link en per datacenter. Om de impact van grote DDoS-aanvallen te voorkomen, u profiteren van de kerncloudmogelijkheden van Azure om u in staat te stellen snel en automatisch uit te schalen om zich te verdedigen tegen DDoS-aanvallen. We gaan meer in detail in op hoe u dit doen in de aanbevolen praktijken artikelen.

## <a name="modernizing-the-defenders-mindset"></a>Modernisering van de mentaliteit van de verdediger
Met PaaS-implementaties komt een verschuiving in uw algemene benadering van beveiliging. U verschuift van het nodig hebben om alles zelf te controleren naar het delen van verantwoordelijkheid met Microsoft.

Een ander belangrijk verschil tussen PaaS en traditionele on-premises implementaties, is een nieuwe weergave van wat de primaire beveiligingsperimeter definieert. Historisch gezien was de primaire on-premises beveiligingsperimeter uw netwerk en de meeste on-premises beveiligingsontwerpen gebruiken het netwerk als primaire beveiligingsspil. Voor PaaS-implementaties u beter worden bediend door als identiteit de primaire beveiligingsperimeter te beschouwen.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Een identiteitsbeleid aannemen als primaire beveiligingsperimeter
Een van de vijf essentiële kenmerken van cloud computing is brede netwerktoegang, waardoor netwerkgericht denken minder relevant is. Het doel van veel van cloud computing is om gebruikers toegang te geven tot bronnen, ongeacht de locatie. Voor de meeste gebruikers, hun locatie gaat ergens op het internet.

De volgende afbeelding laat zien hoe de beveiligingsperimeter is geëvolueerd van een netwerkperimeter naar een identiteitsperimeter. Beveiliging wordt minder over het verdedigen van uw netwerk en meer over het verdedigen van uw gegevens, evenals het beheren van de veiligheid van uw apps en gebruikers. Het belangrijkste verschil is dat u de beveiliging dichter bij uw bedrijf wilt brengen.

![Identiteit als nieuwe beveiligingsperimeter](./media/paas-deployments/identity-perimeter.png)

In eerste instantie leverden Azure PaaS-services (bijvoorbeeld webrollen en Azure SQL) weinig of geen traditionele netwerkperimeterverdediging. Het was duidelijk dat het doel van het element was om te worden blootgesteld aan het internet (webrol) en dat verificatie de nieuwe perimeter biedt (bijvoorbeeld BLOB of Azure SQL).

Moderne beveiligingspraktijken gaan ervan uit dat de tegenstander de netwerkperimeter heeft geschonden. Daarom zijn moderne verdedigingspraktijken verplaatst naar identiteit. Organisaties moeten een identiteitsgebaseerde beveiligingsperimeter instellen met sterke verificatie- en autorisatiehygiëne (best practices).

Principes en patronen voor de netwerkperimeter zijn al tientallen jaren beschikbaar. In tegenstelling, de industrie heeft relatief minder ervaring met het gebruik van identiteit als de primaire beveiligingsperimeter. Met dat gezegd hebben we genoeg ervaring opgebouwd om een aantal algemene aanbevelingen te doen die in het veld zijn bewezen en van toepassing zijn op bijna alle PaaS-services.

De volgende zijn aanbevolen procedures voor het beheren van de identiteitsperimeter.

**Aanbevolen procedures**: beveilig uw sleutels en referenties om uw PaaS-implementatie te beveiligen.   
**Detail:** Het verliezen van sleutels en referenties is een veel voorkomend probleem. U een gecentraliseerde oplossing gebruiken waar sleutels en geheimen kunnen worden opgeslagen in hardwarebeveiligingsmodules (HSM's). [Azure Key Vault](../../key-vault/key-vault-overview.md) beschermt uw sleutels en geheimen door verificatiesleutels, opslagaccountsleutels, gegevensversleutelingssleutels, .pfx-bestanden en wachtwoorden te versleutelen met sleutels die door HSM's worden beschermd.

**Aanbevolen procedures**: Plaats geen referenties en andere geheimen in de broncode of GitHub.   
**Detail:** Het enige wat erger is dan het verliezen van uw sleutels en referenties is het hebben van een onbevoegde partij toegang krijgen tot hen. Aanvallers kunnen profiteren van bottechnologieën om sleutels en geheimen te vinden die zijn opgeslagen in coderepositories zoals GitHub. Plaats geen sleutel en geheimen in deze openbare code repositories.

**Aanbevolen procedures**: Bescherm uw VM-beheerinterfaces op hybride PaaS- en IaaS-services met behulp van een beheerinterface waarmee u deze VM's rechtstreeks op afstand beheren.   
**Detail**: Remote management protocollen zoals [SSH,](https://en.wikipedia.org/wiki/Secure_Shell) [RDP](https://support.microsoft.com/kb/186607)en [PowerShell remoting](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) kunnen worden gebruikt. In het algemeen raden we u aan geen directe toegang op afstand tot VM's vanaf het internet in te schakelen.

Gebruik indien mogelijk alternatieve benaderingen zoals het gebruik van virtuele privénetwerken in een virtueel Azure-netwerk. Als er geen alternatieve benaderingen beschikbaar zijn, moet u ervoor zorgen dat u complexe wachtwoordzinnen en tweestapsverificatie gebruikt (zoals [Azure Multi-Factor Authentication).](/azure/active-directory/authentication/multi-factor-authentication)

**Aanbevolen procedures**: Gebruik sterke verificatie- en autorisatieplatforms.   
**Detail:** Gebruik federatieve identiteiten in Azure AD in plaats van aangepaste gebruikersarchieven. Wanneer u federatieve identiteiten gebruikt, maakt u gebruik van een platformgebaseerde aanpak en delegert u het beheer van geautoriseerde identiteiten aan uw partners. Een federatieve identiteitsbenadering is vooral belangrijk wanneer werknemers worden beëindigd en die informatie moet worden weerspiegeld door middel van meerdere identiteits- en autorisatiesystemen.

Gebruik door platforms geleverde verificatie- en autorisatiemechanismen in plaats van aangepaste code. De reden hiervoor is dat het ontwikkelen van aangepaste verificatiecode foutgevoelig kan zijn. De meeste van uw ontwikkelaars zijn geen beveiligingsexperts en zijn waarschijnlijk niet op de hoogte van de subtiliteiten en de nieuwste ontwikkelingen op het gebied van authenticatie en autorisatie. Commerciële code (bijvoorbeeld van Microsoft) wordt vaak uitgebreid gecontroleerd op beveiliging.

Gebruik tweestapsverificatie. Tweestapsverificatie is de huidige standaard voor verificatie en autorisatie omdat hiermee de beveiligingsproblemen die inherent zijn aan gebruikersnamen- en wachtwoordtypen van verificatie worden vermeden. Toegang tot zowel de Azure-beheerinterfaces (portal/remote PowerShell) als klantgerichte services moeten worden ontworpen en geconfigureerd om [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)te gebruiken.

Gebruik standaardverificatieprotocollen, zoals OAuth2 en Kerberos. Deze protocollen zijn uitgebreid peer reviewed en zijn waarschijnlijk geïmplementeerd als onderdeel van uw platform bibliotheken voor authenticatie en autorisatie.

## <a name="use-threat-modeling-during-application-design"></a>Threat modeling gebruiken tijdens het ontwerpen van toepassingen
De [Levenscyclus van](https://www.microsoft.com/en-us/sdl) Microsoft Security Development bepaalt dat teams tijdens de ontwerpfase een proces moeten uitvoeren dat threat modeling wordt genoemd. Om dit proces te vergemakkelijken, heeft Microsoft de [SDL Threat Modeling Tool](/azure/security/azure-security-threat-modeling-tool)gemaakt. Het modelleren van het ontwerp van de toepassing en het opsommen van [STRIDE-bedreigingen](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) over alle vertrouwensgrenzen heen kan ontwerpfouten al vroeg opvangen.

In de volgende tabel worden de STRIDE-bedreigingen weergegeven en worden enkele voorbeelden gegeven van oplossingen die Azure-functies gebruiken. Deze oplossingen werken niet in elke situatie.

| Bedreiging | Beveiligingseigenschap | Mogelijke azure-platformmitigaties |
| --- | --- | --- |
| Adresvervalsing (spoofing) | Authentication | Https-verbindingen vereisen. |
| Knoeien | Gegevensintegriteit | SSL-certificaten valideren. |
| Verwerping | Niet-verwerping | [Azure-bewaking en -diagnose inschakelen](/azure/architecture/best-practices/monitoring). |
| Openbaarmaking van informatie | Vertrouwelijkheid | Versleutel gevoelige gegevens in rust met behulp van [servicecertificaten.](/rest/api/appservice/certificates) |
| Denial of Service | Beschikbaarheid | Monitor prestatiestatistieken voor mogelijke denial-of-service-omstandigheden. Verbindingsfilters implementeren. |
| Verhoging van het voorrecht | Autorisatie | [Gebruik Privileged Identity Management](/azure/active-directory/privileged-identity-management/subscription-requirements). |

## <a name="develop-on-azure-app-service"></a>Ontwikkelen op Azure App Service
[Azure App Service](/azure/app-service/overview) is een PaaS-aanbod waarmee u web- en mobiele apps maken voor elk platform of apparaat en overal, in de cloud of on-premises verbinding maken met gegevens. App Service omvat de web- en mobiele mogelijkheden die voorheen afzonderlijk werden geleverd als Azure Websites en Azure Mobile Services. Deze service bevat ook nieuwe mogelijkheden voor het automatiseren van bedrijfsprocessen en het hosten van cloud-API's. Als één geïntegreerde service biedt App Service een uitgebreide reeks mogelijkheden voor web-, mobiele en integratiescenario's.

Hieronder volgen de aanbevolen procedures voor het gebruik van App Service.

**Aanbevolen procedures**: [Verifiëren via Azure Active Directory](/azure/app-service/overview-authentication-authorization).   
**Detail:** App Service biedt een OAuth 2.0-service voor uw identiteitsprovider. OAuth 2.0 richt zich op de eenvoud van clientontwikkelaars en biedt specifieke autorisatiestromen voor webtoepassingen, desktoptoepassingen en mobiele telefoons. Azure AD gebruikt OAuth 2.0 om u in staat te stellen toegang tot mobiele en webtoepassingen te autoriseren.

**Aanbevolen procedures**: Beperk de toegang op basis van de noodzaak om beveiligingsprincipes te kennen en de minste bevoegdheden te kennen.   
**Detail:** Het beperken van de toegang is noodzakelijk voor organisaties die beveiligingsbeleid voor gegevenstoegang willen afdwingen. U RBAC gebruiken om machtigingen toe te wijzen aan gebruikers, groepen en toepassingen op een bepaald bereik. Zie [Aan de slag met toegangsbeheer](/azure/role-based-access-control/overview)voor meer informatie over het verlenen van gebruikers toegang tot toepassingen.

**Aanbevolen :** Bescherm uw sleutels.   
**Detail:** Azure Key Vault helpt cryptografische sleutels en geheimen te beveiligen die cloudtoepassingen en -services gebruiken. Met Key Vault u sleutels en geheimen versleutelen (zoals verificatiesleutels, opslagaccountsleutels, gegevensversleutelingssleutels,. PFX-bestanden en wachtwoorden met behulp van sleutels die worden beschermd door hardwarebeveiligingsmodules (HSM's). Voor extra zekerheid kunt u de sleutels importeren of genereren in HSM's. Zie [Azure Key Vault](/azure/key-vault/key-vault-overview) voor meer informatie. U Key Vault ook gebruiken om uw TLS-certificaten te beheren met automatische verlenging.

**Aanbevolen procedures**: Ip-adressen van binnenkomende bronnen beperken.   
**Detail:** [App Service Environment](/azure/app-service/environment/intro) heeft een virtuele netwerkintegratiefunctie waarmee u inkomende bron-IP-adressen beperken via netwerkbeveiligingsgroepen. Met virtuele netwerken u Azure-bronnen plaatsen in een niet-internet, routeerbaar netwerk waarop u de toegang tot beheert. Zie Uw [app integreren met een virtueel Azure-netwerk](/azure/app-service/web-sites-integrate-with-vnet)voor meer informatie.

**Aanbevolen procedures:** controleer de beveiligingsstatus van uw App-serviceomgevingen.   
**Detail:** Gebruik Azure Security Center om uw App Service-omgevingen te bewaken. Wanneer Security Center potentiële beveiligingsproblemen identificeert, worden [er aanbevelingen](../../security-center/security-center-virtual-machine-protection.md) gemaakt die u begeleiden bij het configureren van de benodigde besturingselementen.

> [!NOTE]
> Monitoring App Service is in preview en alleen beschikbaar op de [standaardlaag](/azure/security-center/security-center-pricing) van Security Center.
>
>

## <a name="install-a-web-application-firewall"></a>Een firewall voor webtoepassingen installeren
Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van veelvoorkomende bekende beveiligingsproblemen. Veelvoorkomende aanvallen zijn hierbij onder andere aanvallen met SQL-injecties en aanvallen via scripting op meerdere sites. Het kan een hele uitdaging zijn om dergelijke aanvallen in toepassingscode te voorkomen en dit kan tevens veel onderhoud, patching en controle vereisen op meerdere lagen van de toepassingstopologie. Een gecentraliseerde firewall voor webtoepassingen maakt het beveiligingsbeheer veel eenvoudiger en biedt toepassingsbeheerders meer veiligheid tegen bedreigingen of aanvallen. Een WAF-oplossing kan ook sneller reageren op een beveiligingsrisico door een patch voor een bekend beveiligingsprobleem toe te passen op een centrale locatie in plaats van elke afzonderlijke webtoepassing te beveiligen. Bestaande toepassingsgateways kunnen eenvoudig worden geconverteerd naar een toepassingsgateway met Web Application Firewall.

[Web application firewall (WAF)](/azure/frontdoor/waf-overview) is een functie van Application Gateway die gecentraliseerde bescherming van uw webapplicaties biedt tegen veelvoorkomende exploits en kwetsbaarheden. WAF is gebaseerd op regels uit de Kernregel 3.0 of 2.2.9 van het [Open Web Application Security Project (OWASP).](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)

## <a name="monitor-the-performance-of-your-applications"></a>De prestaties van uw toepassingen bewaken
Monitoring is de handeling van het verzamelen en analyseren van gegevens om de prestaties, status en beschikbaarheid van uw toepassing te bepalen. Een effectieve bewakingsstrategie helpt u de gedetailleerde werking van de onderdelen van uw toepassing te begrijpen. Het helpt u uw uptime te verhogen door u op de hoogte te stellen van kritieke problemen, zodat u ze oplossen voordat ze problemen worden. Het helpt u ook bij het detecteren van afwijkingen die mogelijk verband houden met de beveiliging.

Gebruik [Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) om de beschikbaarheid, prestaties en het gebruik van uw toepassing te controleren, of deze nu wordt gehost in de cloud of on-premises. Door Application Insights te gebruiken, u snel fouten in uw toepassing identificeren en diagnosticeren zonder te wachten tot een gebruiker deze rapporteert. Met de informatie die u verzamelt, kunt u weloverwogen keuzes maken over het onderhoud en de verbeteringen van uw toepassing.

Application Insights heeft uitgebreide hulpmiddelen voor interactie met de gegevens die worden verzameld. Application Insights slaat zijn gegevens op in een gemeenschappelijke opslagplaats. Het kan profiteren van gedeelde functionaliteit, zoals waarschuwingen, dashboards en diepgaande analyse met de Kusto-querytaal.

## <a name="perform-security-penetration-testing"></a>Beveiligingspenetratietesten uitvoeren
Het valideren van beveiligingsbeveiligingen is net zo belangrijk als het testen van andere functionaliteit. Maak [penetratietesten](pen-testing.md) een standaardonderdeel van uw build- en implementatieproces. Plan regelmatig beveiligingstests en het scannen van kwetsbaarheden op geïmplementeerde toepassingen en controleer op open poorten, eindpunten en aanvallen.

Fuzz testen is een methode voor het vinden van programmafouten (codefouten) door het leveren van misvormde invoergegevens aan programma-interfaces (entry points) die deze gegevens ontleden en consumeren. [Microsoft Security Risk Detection](https://www.microsoft.com/en-us/security-risk-detection/) is een cloudtool die u gebruiken om te zoeken naar bugs en andere beveiligingsproblemen in uw software voordat u deze implementeert in Azure. De tool is ontworpen om kwetsbaarheden te vangen voordat u software implementeert, zodat u geen bug hoeft te patchen, crashes hoeft aan te pakken of op een aanval hoeft te reageren nadat de software is vrijgegeven.


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebben we ons gericht op beveiligingsvoordelen van een azure PaaS-implementatie en aanbevolen procedures voor beveiliging voor cloudtoepassingen. Lees vervolgens aanbevolen procedures voor het beveiligen van uw PaaS-web- en mobiele oplossingen met specifieke Azure-services. We beginnen met Azure App Service, Azure SQL Database en Azure SQL Data Warehouse en Azure Storage. Als artikelen over aanbevolen praktijken voor andere Azure-services beschikbaar komen, worden koppelingen weergegeven in de volgende lijst:

- [Azure App Service](paas-applications-using-app-services.md)
- [Azure SQL Database en Azure SQL Data Warehouse](paas-applications-using-sql.md)
- [Azure Storage](paas-applications-using-storage.md)
- Azure Cache voor Redis
- Azure Service Bus
- Firewalls voor webtoepassingen

Zie [Het ontwikkelen van veilige toepassingen op Azure](abstract-develop-secure-apps.md) voor beveiligingsvragen en -besturingselementen die u in elke fase van de levenscyclus van softwareontwikkeling moet overwegen bij het ontwikkelen van toepassingen voor de cloud.

Zie [aanbevolen procedures en patronen voor Azure-beveiliging](best-practices-and-patterns.md) voor meer aanbevolen procedures voor beveiliging die u gebruiken bij het ontwerpen, implementeren en beheren van uw cloudoplossingen met Azure.

De volgende bronnen zijn beschikbaar om meer algemene informatie te verstrekken over Azure-beveiliging en gerelateerde Microsoft-services:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) - voor up-to-date informatie over de nieuwste azure security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - waar beveiligingsproblemen van Microsoft, waaronder problemen met Azure, kunnen worden gemeld of via e-mail naarsecure@microsoft.com
