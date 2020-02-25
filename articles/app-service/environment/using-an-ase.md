---
title: Een App Service Environment gebruiken en beheren
description: Apps in een Azure App Service omgeving maken, publiceren en schalen. Zoek de algemene taken in één document.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7be1676c8949cd30d5e1fe93a73afd75a5a9b67f
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565640"
---
# <a name="use-an-app-service-environment"></a>Een App Service omgeving gebruiken #

De App Service Environment (ASE) is een implementatie van de Azure App Service in een subnet in het virtuele Azure-netwerk van een klant. Een ASE bestaat uit:

- **Front-** ends: de front-ends zijn waar http/https eindigt in een app service omgeving.
- **Werk nemers**: de werk nemers zijn de resources die uw apps hosten.
- **Data Base**: de Data Base bevat informatie die de omgeving definieert.
- **Opslag**: de opslag wordt gebruikt voor het hosten van de door de klant gepubliceerde apps.

U kunt een ASE implementeren met een externe of interne VIP voor toegang tot apps. De implementatie met een extern VIP wordt meestal een externe ASE genoemd. De interne versie wordt de ILB-ASE genoemd, omdat deze gebruikmaakt van een interne load balancer (ILB). Zie [een ILB ASE maken en gebruiken][MakeILBASE]voor meer informatie over de ILB-ASE.

## <a name="create-an-app-in-an-ase"></a>Een app maken in een ASE ##

Als u een app in een ASE wilt maken, gebruikt u hetzelfde proces als wanneer u het normaal maakt, maar met enkele kleine verschillen. Wanneer u een nieuw App Service plan (ASP) maakt:

- In plaats van een geografische locatie te kiezen voor het implementeren van uw app, kiest u een ASE als uw locatie.
- Alle App Service plannen die in een ASE zijn gemaakt, kunnen zich alleen in een geïsoleerde prijs categorie bevindt.

Als u geen ASE hebt, kunt u er een maken door de instructies in [een app service omgeving maken][MakeExternalASE]te volgen.

Een app maken in een ASE:

1. Selecteer **een resource maken** > **Web en mobiel** > **Web-app**.

2. Voer een naam in voor de app. Als u al een App Service plan in een ASE hebt geselecteerd, weerspiegelt de domein naam voor de app de domein naam van de ASE.

    ![App-naam selecteren][1]

1. Selecteer een abonnement.

1. Voer een naam in voor een nieuwe resource groep of selecteer **bestaande gebruiken** en selecteer er een in de vervolg keuzelijst.

1. Selecteer uw besturingssysteem. 

1. Selecteer een bestaand App Service plan in uw ASE of maak een nieuw abonnement door de volgende stappen uit te voeren:

    a. Selecteer **een resource maken > web-app**in het Azure Portal menu aan de linkerkant.

    b. Selecteer het abonnement.
    
    c. Selecteer of maak de resource groep.
    
    d. Geef de naam van uw web-app op.
    
    e. Selecteer code of DockerContainer.
    
    f. Selecteer runtime stack.
    
    g. Selecteer Linux of Windows. 
    
    h. Selecteer uw ASE in de vervolg keuzelijst **regio** . 
    
    i. Selecteer of maak een nieuw App Service plan. Als u een nieuw App Service plan maakt, selecteert u de juiste **geïsoleerde** SKU-grootte.
    
    ![Geïsoleerde prijs Categorieën][2]

    > [!NOTE]
    > Linux-apps en Windows-apps kunnen zich niet in hetzelfde App Service plan bevindt, maar kunnen zich in dezelfde App Service Environment. 
    >

2. Selecteer **controleren + maken** en selecteer vervolgens **maken** als de gegevens correct zijn.

## <a name="how-scale-works"></a>Hoe schalen werkt ##

Elke App Service-app wordt uitgevoerd in een App Service plan. App Service omgevingen houden App Service plannen en App Service abonnementen op apps. Wanneer u een app schaalt, schaalt u het App Service plan en schaalt u alle apps in hetzelfde abonnement.

Wanneer u een App Service plan schaalt, wordt de benodigde infra structuur automatisch toegevoegd. Er is een vertraging om bewerkingen te schalen terwijl de infra structuur is toegevoegd. Als u verschillende schaal bewerkingen op volg orde uitvoert, wordt de eerste aanvraag voor de infrastructuur schaal uitgevoerd op en de andere wachtrij. Wanneer de eerste schaal bewerking is voltooid, worden alle andere infrastructuur aanvragen samen gebruikt. Wanneer de infra structuur wordt toegevoegd, worden de App Service plannen als toepasselijk toegewezen. Het maken van een nieuw App Service plan is zelf een schaal bewerking omdat er extra hardware wordt aangevraagd. 

In de multi tenant-App Service is het schalen direct omdat een groep resources eenvoudig beschikbaar is om het te ondersteunen. In een ASE is er geen buffer, en worden de bronnen na behoefte toegewezen.

In een ASE kunt u een App Service plan tot 100 exemplaren schalen. Een ASE kan tot 201 totaal aantal exemplaren hebben in alle App Service-abonnementen die zich in de ASE bevinden. 

## <a name="ip-addresses"></a>IP-adressen ##

App Service beschikt over de mogelijkheid om een toegewezen IP-adres toe te wijzen aan een app. De mogelijkheid om een specifiek IP-adres toe te wijzen aan een app is beschikbaar nadat u een SSL op basis van IP hebt geconfigureerd, zoals beschreven in [een bestaand aangepast SSL-certificaat binden aan Azure app service][ConfigureSSL]. In een ILB-ASE kunt u geen extra IP-adressen toevoegen die moeten worden gebruikt voor een SSL op basis van IP.

Met een externe ASE kunt u op IP gebaseerde SSL voor uw app op dezelfde manier configureren als in de multi tenant-App Service. Er bevindt zich altijd één reserve adres in de ASE met Maxi maal 30 IP-adressen. Telkens wanneer u er een gebruikt, wordt er een andere toegevoegd, zodat een adres altijd beschikbaar is voor gebruik. Er is een vertraging vereist om een ander IP-adres toe te wijzen, waardoor het snel achter elkaar toevoegen van IP-adressen wordt voor komen.

## <a name="front-end-scaling"></a>Front-end schalen ##

Wanneer u uw App Service-abonnementen uitbreidt, worden werk nemers automatisch toegevoegd ter ondersteuning van de plannen. Elke ASE wordt gemaakt met twee front-ends. De front-ends worden automatisch uitgeschaald met een snelheid van één front-ends voor elk totaal van 15 exemplaren van een App Service-abonnement. Als u drie App Service plannen hebt met elk vijf instanties, hebt u een totaal van 15 exemplaren en drie front-ends. Als u naar een totaal van 30 exemplaren schaalt, hebt u vier front-ends, enzovoort. 

Het aantal front-ends dat standaard wordt toegewezen, is geschikt voor een gemiddelde belasting. U kunt de verhouding wijzigen in zo weinig als één front-ends voor elke vijf exemplaren. U kunt ook de grootte van de front-ends wijzigen. Standaard zijn deze enkelvoudige kernen. In plaats daarvan kunt u de grootte van de front-ends in de portal wijzigen in twee of vier kern groottes. Er worden kosten in rekening gebracht voor het wijzigen van de verhouding of de grootte van de front-ends. Zie [Azure app service prijzen][Pricing]voor meer informatie. Als u de capaciteit van de belasting van uw ASE wilt verbeteren, krijgt u meer verbetering door eerst te schalen naar twee kern front-ends voordat u de schaal ratio aanpast. Als u de kern grootte van uw front-ends wijzigt, treedt er een upgrade van uw ASE op en moet deze buiten kantoor uren worden uitgevoerd.

Front-ends bronnen zijn het HTTP/HTTPS-eind punt voor de ASE. Met de standaard front-end-configuratie is het geheugen gebruik per front-end consistent rond 60 procent. De belangrijkste reden voor het schalen van uw front-ends is CPU, dat voornamelijk wordt aangedreven door HTTPS-verkeer.

## <a name="app-access"></a>App-toegang ##

In een externe ASE is het domein achtervoegsel dat wordt gebruikt voor het maken van de app *.&lt;asename&gt;. p.azurewebsites.net*. Als uw ASE de naam _External-ASE_ heeft en u een app met de naam _CONTOSO_ in die ASE host, bereikt u deze op de volgende url's:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Zie [een app service omgeving maken][MakeExternalASE] voor meer informatie over het maken van een externe ASE

In een ILB-ASE is het domein achtervoegsel dat wordt gebruikt voor het maken van de app *.&lt;asename&gt;. appserviceenvironment.net*. Als uw ASE de naam _ILB-ASE_ heeft en u een app met de naam _CONTOSO_ in die ASE host, bereikt u deze op de volgende url's:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Zie [een ILB ASE maken en gebruiken][MakeILBASE]voor meer informatie over het maken van een ILB-ASE. 

De SCM-URL wordt gebruikt voor toegang tot de kudu-console of voor het publiceren van uw app met behulp van Web Deploy. Zie [kudu-console voor Azure app service][Kudu]voor meer informatie over de kudu-console. De kudu-console biedt u een webinterface voor het opsporen van fouten, het uploaden van bestanden, het bewerken van bestanden en nog veel meer.

## <a name="publishing"></a>Publiceren ##

Net als bij de multi tenant App Service, in een ASE kunt u het volgende publiceren:

- Webimplementatie.
- FTP.
- Continue integratie.
- Sleep en zet neer in de kudu-console.
- Een IDE, zoals Visual Studio, eclips of IntelliJ.

Met een externe ASE werken deze publicatie opties allemaal hetzelfde. Zie [implementatie in azure app service][AppDeploy]voor meer informatie. 

Het belangrijkste verschil met publiceren is ten opzichte van een ILB-ASE. Met een ILB-ASE zijn de publicatie-eind punten alleen beschikbaar via de ILB. De ILB bevindt zich op een privé-IP in het subnet ASE in het virtuele netwerk. Als u geen netwerk toegang tot de ILB hebt, kunt u geen apps publiceren op die ASE. Zoals vermeld in [Create and use a ILB ASE][MakeILBASE], moet u DNS configureren voor de apps in het systeem. Die het SCM-eind punt bevat. Als ze niet juist zijn gedefinieerd, kunt u niet publiceren. Uw Ide's moet ook netwerk toegang hebben tot de ILB om er rechtstreeks naar te kunnen publiceren.

Op internet gebaseerde CI-systemen, zoals GitHub en Azure DevOps, werken niet met een ILB ASE omdat het publicatie-eind punt niet toegankelijk is via internet. U kunt publiceren naar een ILB ASE inschakelen vanuit Azure DevOps door een zelf-hostende release agent te installeren in het VNet dat de ILB ASE bevat. U kunt ook een CI-systeem gebruiken dat gebruikmaakt van een pull-model, zoals Dropbox.

De publicatie-eindpunten voor apps in een ILB AS-omgeving maken gebruik van het domein waarmee de ILB AS-omgeving is gemaakt. U kunt dit zien in het publicatie Profiel van de app en in de portal-Blade van de app (in **overzicht** > **Essentials** en ook in **Eigenschappen**). 

## <a name="storage"></a>Opslag

Een ASE heeft 1 TB opslag ruimte voor alle apps in de ASE. Een geïsoleerd SKU App Service-abonnement heeft standaard een limiet van 250 GB. Als u vijf of meer App Service abonnementen hebt, moet u erop letten dat u de limiet van 1 TB van de ASE niet overschrijdt. Als u meer dan de limiet van 250 GB per App Service abonnement nodig hebt, neemt u contact op met de ondersteuning om de limiet voor het App Service plan aan te passen aan een maximum van 1 TB. Wanneer de limiet voor het abonnement is aangepast, is er nog steeds een limiet van 1 TB voor alle App Service plannen in het ASE. 

## <a name="logging"></a>Logboekregistratie ##

U kunt uw ASE integreren met Azure Monitor voor het verzenden van logboeken over de ASE naar Storage, Event hub of Log Analytics. De items die vandaag zijn geregistreerd zijn:

| Hiervan | Bericht |
|---------|----------|
| ASE is beschadigd | De opgegeven ASE is beschadigd vanwege een ongeldige configuratie van het virtuele netwerk. De ASE wordt onderbroken als de status slecht wordt voortgezet. Zorg ervoor dat de hier gedefinieerde richt lijnen worden gevolgd: https://docs.microsoft.com/azure/app-service/environment/network-info |
| Het ASE-subnet heeft bijna geen ruimte meer | De opgegeven ASE bevindt zich in een subnet dat bijna geen ruimte meer heeft. Er zijn {0} resterende adressen. Zodra deze adressen zijn uitgeput, kan de ASE niet worden geschaald  |
| De limiet voor het aantal exemplaren van de ASE is bijna bereikt | De opgegeven ASE is bijna de limiet voor het aantal exemplaren van de ASE. Het bevat momenteel {0} App Service abonnement van een maximum aantal exemplaren van 201. |
| ASE kan geen afhankelijkheid bereiken | De opgegeven ASE kan niet worden bereikt {0}.  Zorg ervoor dat de hier gedefinieerde richt lijnen worden gevolgd: https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE is onderbroken | De opgegeven ASE is onderbroken. De ASE-suspensie kan worden veroorzaakt door een account tekort of een ongeldige configuratie van het virtuele netwerk. Los de hoofd oorzaak op en hervat de ASE om verkeer door te gaan |
| Upgrade van ASE is gestart | Een platform upgrade naar de opgegeven ASE is gestart. Verwachte vertragingen bij het schalen van bewerkingen |
| De ASE-upgrade is voltooid | Een platform upgrade naar de opgegeven ASE is voltooid |
| Schaal bewerkingen zijn gestart | Een App Service plan ({0}) is begonnen met schalen. Gewenste status: {1} ik{2} werk nemers 
| Schaal bewerkingen zijn voltooid | Het schalen van een App Service plan ({0}) is voltooid. Huidige status: {1} ik{2} werk nemers |
| Schaal bewerkingen zijn mislukt | Een App Service plan ({0}) is niet geschaald. Huidige status: {1} ik{2} werk nemers |

Logboek registratie inschakelen voor uw ASE: 

1. Ga naar Diagnostische instellingen in de portal.  
1. Selecteer diagnostische instelling toevoegen.
1. Geef een naam op voor de logboek integratie
1. Controleer de gewenste logboek doelen en configureer deze. 
1. AppServiceEnvironmentPlatformLogs controleren

![Instellingen voor Diagnostische logboeken van ASE][4]

Als u integreert met Log Analytics, kunt u de logboeken bekijken door Logboeken te selecteren in de ASE-Portal en een query te maken op basis van de AppServiceEnvironmentPlatformLogs. 

## <a name="upgrade-preference"></a>Upgrade voorkeur ##

Als u meerdere as hebt, is het mogelijk dat u een upgrade van sommige as wilt uitvoeren voor anderen. In het object ASE hosting Environment Resource Manager kunt u een waarde instellen voor UpgradePreference. De instelling upgradePreference kan worden geconfigureerd via sjabloon, ARMClient of https://resources.azure.com.  De drie mogelijke waarden zijn:

* Geen: de standaard instelling is dat Azure uw ASE in geen enkele batch bijwerkt.
* Early-Early betekent dat uw ASE wordt bijgewerkt in de eerste helft van de App Service upgrades
* Late-Late houdt in dat uw ASE wordt bijgewerkt in de tweede helft van de App Service upgrades

Als u https://resources.azure.comgebruikt, kunt u de waarde van upgradePreferences instellen op:

1. Ga naar resources.azure.com en meld u aan met uw Azure-account
1. Navigeer door abonnementen\/naam van \[abonnement\]\/resourceGroups\/\[resource groep naam\]\/providers\/micro soft. Web\/hostingEnvironments\/\[ASE naam\]
1. Boven selecteren voor lezen/schrijven
1. Selecteer Bewerken
1. Wijzig de waarde voor upgradePreference in wat u nodig hebt uit de drie opties.
1. Patch selecteren

![bronnen Azure com-weer gave][5]

De upgradePreferences-functie is echt het meest zinnig wanneer u meerdere as hebt, omdat uw ' early ' geüpgradede as wordt bijgewerkt vóór uw ' late ' as. Wanneer u meerdere as hebt, moet uw dev/test as zijn ingesteld op ' early ' en moet uw productie as worden ingesteld op ' late '.

## <a name="pricing"></a>Prijzen ##

De prijs-SKU met de naam **geïsoleerd** is alleen voor gebruik met ASE. Alle App Service plannen die worden gehost in de ASE, bevinden zich in de afzonderlijke prijs-SKU. De afzonderlijke App Service plan tarieven kunnen per regio verschillen. 

Naast de prijs voor uw App Service plannen, is er een vast tarief voor ASE zelf. De vaste snelheid verandert niet met de grootte van uw ASE en betaalt voor de ASE-infra structuur een standaard schaal factor van één extra front-end voor elke 15 App Service-abonnement.  

Als de standaard schaal snelheid van één front-ends voor elke 15 App Service-plan exemplaren niet snel genoeg is, kunt u de verhouding aanpassen waarmee de front-ends worden toegevoegd of de grootte van de front-ends.  Wanneer u de verhouding of grootte aanpast, betaalt u voor de front-ends kernen die niet standaard zouden worden toegevoegd.  

Als u bijvoorbeeld de schaal verhouding wijzigt in 10, wordt een front-end toegevoegd voor elke 10 instanties in uw App Service-abonnementen. De vaste kosten omvatten een schaal tarief van een front-end voor elke 15 exemplaren. Met een schaal ratio van 10 betaalt u een vergoeding voor de derde front-end die is toegevoegd voor de tien App Service-plan exemplaren. U hoeft er niet voor te betalen wanneer u 15 exemplaren bereikt, omdat deze automatisch is toegevoegd.

Als u de grootte van de front-ends hebt aangepast aan twee kernen maar de verhouding niet aanpast, betaalt u voor de extra kernen.  Een ASE wordt gemaakt met twee front-ends, dus zelfs onder de drempel waarde voor automatisch schalen kunt u twee extra kernen betalen als u de grootte hebt verhoogd naar twee kern front-ends.

Zie [Azure app service prijzen][Pricing]voor meer informatie.

## <a name="delete-an-ase"></a>Een ASE verwijderen ##

Een ASE verwijderen: 

1. Gebruik **verwijderen** boven aan de blade **app service Environment** . 

1. Voer de naam van uw ASE in om te bevestigen dat u deze wilt verwijderen. Wanneer u een ASE verwijdert, verwijdert u ook alle inhoud erin. 

    ![ASE verwijderen][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
