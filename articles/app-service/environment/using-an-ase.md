---
title: Een app-serviceomgeving gebruiken en beheren
description: Meer informatie over het maken, publiceren en schalen van apps in een App-serviceomgeving. Vind alle algemene taken in dit artikel.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 3/26/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4565580feeddc2df8f6ed3011302016bb39977b4
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586124"
---
# <a name="use-an-app-service-environment"></a>Een App Service-omgeving gebruiken

Een App Service Environment (ASE) is een implementatie van Azure App Service in een subnet in het Azure Virtual Network-exemplaar van een klant. Een ASE bestaat uit:

- **Front-ends**: Wanneer HTTP of HTTPS eindigt in een App Service-omgeving
- **Werknemers:** de bronnen die uw apps hosten
- **Database**: Bevat informatie die de omgeving definieert
- **Opslag:** wordt gebruikt om de door klanten gepubliceerde apps te hosten

U een ASE implementeren met een extern of intern virtueel IP (VIP) voor app-toegang. Een implementatie met een externe VIP wordt gewoonlijk een *externe ASE*genoemd. Een implementatie met een interne VIP wordt een *ILB ASE* genoemd omdat deze een interne load balancer (ILB) gebruikt. Zie [Een ILB ASE maken en gebruiken][MakeILBASE]voor meer informatie over de ILB ASE.

## <a name="create-an-app-in-an-ase"></a>Een app maken in een ASE

Als u een app in een ASE wilt maken, gebruikt u hetzelfde proces als wanneer u normaal gesproken een app maakt, maar met een paar kleine verschillen. Wanneer u een nieuw App Service-abonnement maakt:

- In plaats van een geografische locatie te kiezen waar u uw app wilt implementeren, kiest u een ASE als locatie.
- Alle App Service-abonnementen die in een ASE zijn gemaakt, kunnen alleen in een geïsoleerde prijslaag zijn.

Als u geen ASE hebt, u er een maken door de instructies te volgen in [Een app-serviceomgeving maken.][MakeExternalASE]

Een app maken in een ASE:

1. Selecteer **Een resource** > **Web + Mobile** > **Web App maken**.

1. voer een naam voor de app in. Als u al een App Service-abonnement in een ASE hebt geselecteerd, weerspiegelt de domeinnaam voor de app de domeinnaam van de ASE:

    ![App-naamselectie][1]

1. Selecteer een abonnement.

1. Voer een naam in voor een nieuwe resourcegroep of selecteer **Bestaand gebruiken** en selecteer er een in de vervolgkeuzelijst.

1. Selecteer uw besturingssysteem.

1. Selecteer een bestaand App Service-abonnement in uw ASE of maak een nieuw abonnement door de volgende stappen te volgen:

    a. Selecteer in het menu aan de linkerkant van azure-portal de optie **Een bron > Web App maken**.

    b. Selecteer het abonnement.

    c. Selecteer of maak de resourcegroep.

    d. Voer de naam van uw web-app in.

    e. Selecteer **Code** of **DockerContainer**.

    f. Selecteer een runtimestack.

    g. Selecteer **Linux** of **Windows**. 

    h. Selecteer uw ASE in de vervolgkeuzelijst **Regio.** 

    i. Selecteer of maak een nieuw App Service-abonnement. Als u een nieuw App Service-abonnement maakt, selecteert u de juiste **SKU-grootte.**

    ![Geïsoleerde prijsniveaus][2]

    > [!NOTE]
    > Linux-apps en Windows-apps kunnen niet in hetzelfde App Service-abonnement staan, maar ze kunnen wel in dezelfde App-serviceomgeving staan.
    >

1. Selecteer **Controleren + maken,** controleer of de informatie correct is en selecteer **Vervolgens Maken**.

## <a name="how-scale-works"></a>Hoe schaalgrootte werkt

Elke App Service-app wordt uitgevoerd in een App Service-abonnement. App-serviceomgevingen bevatten App-serviceplannen en App Service-abonnementen bevatten apps. Wanneer u een app schaalt, schaalt u ook het App Service-abonnement en alle apps in hetzelfde abonnement.

Wanneer u een App Service-abonnement schaalt, wordt de benodigde infrastructuur automatisch toegevoegd. Er is een vertraging om bewerkingen te schalen terwijl de infrastructuur wordt toegevoegd. Als u meerdere schaalbewerkingen achter elkaar uitvoert, wordt er op de eerste aanvraag voor infrastructuurschaal gereageerd en staan de andere in de wachtrij. Wanneer de eerste schaalbewerking is voltooid, werken de andere infrastructuuraanvragen allemaal samen. En wanneer de infrastructuur wordt toegevoegd, worden de appserviceplannen zo nodig toegewezen. Het maken van een nieuw App Service-abonnement is zelf een schaalbewerking omdat het om extra hardware vraagt.

In de multitenant App Service is schalen onmiddellijk omdat een groep resources direct beschikbaar is om deze te ondersteunen. In een ASE is er geen dergelijke buffer en worden resources toegewezen op basis van behoefte.

In een ASE u een App Service-abonnement tot 100 exemplaren schalen. Een ASE kan maximaal 201 totaal exemplaren hebben voor alle App Service-abonnementen in die ASE.

## <a name="ip-addresses"></a>IP-adressen

App Service kan een speciaal IP-adres toewijzen aan een app. Deze mogelijkheid is beschikbaar nadat u SSL op BASIS van IP hebt geconfigureerd, zoals beschreven in [Een bestaand aangepast TLS/SSL-certificaat binden aan Azure App Service.][ConfigureSSL] In een ILB ASE u niet meer IP-adressen toevoegen die kunnen worden gebruikt voor IP-gebaseerde SSL.

Met een externe ASE u IP-gebaseerde SSL voor uw app op dezelfde manier configureren als in de multitenant App Service. Er is altijd een reserveadres in de ASE, tot 30 IP-adressen. Elke keer dat u een gebruik maakt, wordt een ander toegevoegd, zodat een adres altijd direct beschikbaar is. Er is een vertraging nodig om een ander IP-adres toe te wijzen. Deze vertraging voorkomt dat er snel achter elkaar IP-adressen worden toegevoegd.

## <a name="front-end-scaling"></a>Front-end schalen

Wanneer u uw App Service-abonnementen uitschaalt, worden werknemers automatisch toegevoegd om ze te ondersteunen. Elke ASE is gemaakt met twee front-ends. The front ends automatically scale out at a rate of one front end for every set of 15 App Service plan instances. Als u bijvoorbeeld drie App Service-abonnementen hebt met elk vijf exemplaren, hebt u in totaal 15 exemplaren en drie front-ends. Als u schaalt tot een totaal van 30 exemplaren, hebt u vier front-ends. Dit patroon gaat verder als je uitschaalt.

Het aantal frontends dat standaard wordt toegewezen, is goed voor een matige belasting. U de verhouding verlagen tot slechts één front-end voor elke vijf instanties. U ook de grootte van de voorste uiteinden wijzigen. Standaard zijn ze single core. In de Azure-portal u de grootte ervan wijzigen in twee of vier kernen.

Er is een vergoeding voor het veranderen van de verhouding of de front-end maten. Zie [Azure App Service-prijzen][Pricing]voor meer informatie . Als u de laadcapaciteit van uw ASE wilt verbeteren, krijgt u meer verbetering door eerst te schalen naar twee kernfronten voordat u de schaalverhouding aanpast. Het wijzigen van de kerngrootte van uw vooreinden zal leiden tot een upgrade van uw ASE en moet worden gedaan buiten de reguliere kantooruren.

Front-end resources zijn het HTTP/HTTPS-eindpunt voor de ASE. Met de standaard front-end configuratie ligt het geheugengebruik per front-end consistent rond de 60 procent. De belangrijkste reden om uw front-ends te schalen is CPU-gebruik, dat voornamelijk wordt aangestuurd door HTTPS-verkeer.

## <a name="app-access"></a>App-toegang

In een externe ASE is het domeinachtervoegsel dat wordt gebruikt voor het maken van apps *.&lt; asename&gt;.p.azurewebsites.net*. Als uw ASE _extern_ wordt genoemd en u een app host die _contoso_ heet in die ASE, bereikt u deze op deze URL's:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Zie [Een app-serviceomgeving maken][MakeExternalASE]voor informatie over het maken van een externe ASE.

In een ILB ASE is het domeinachtervoegsel dat wordt gebruikt voor het maken van apps *.&lt; asename&gt;.appserviceenvironment.net*. Als uw ASE _ilb-ase_ wordt genoemd en u een app host die _contoso_ heet in die ASE, bereikt u deze op deze URL's:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Zie [Een ILB ASE maken en gebruiken][MakeILBASE]voor informatie over het maken van een ILB ASE.

De SCM-URL wordt gebruikt om toegang te krijgen tot de Kudu-console of om uw app te publiceren met Behulp van Web Deploy. Zie [Kudu-console voor Azure App Service voor][Kudu]meer informatie over de Kudu-console. De Kudu-console biedt u een web-gebruikersinterface voor foutopsporing, het uploaden van bestanden, het bewerken van bestanden en nog veel meer.

### <a name="dns-configuration"></a>DNS-configuratie 

Wanneer u een externe ASE gebruikt, worden apps die in uw ASE zijn gemaakt, geregistreerd bij Azure DNS. Met een ILB ASE moet u uw eigen DNS beheren. 

Ga als u DNS configureert met uw ILB ASE:

    create a zone for <ASE name>.appserviceenvironment.net
    create an A record in that zone that points * to the ILB IP address
    create an A record in that zone that points @ to the ILB IP address
    create a zone in <ASE name>.appserviceenvironment.net named scm
    create an A record in the scm zone that points * to the ILB IP address

De DNS-instellingen voor uw ASE-standaarddomeinachtervoegsel beperken uw apps niet om alleen toegankelijk te zijn voor die namen. U een aangepaste domeinnaam instellen zonder validatie op uw apps in een ILB ASE. Als u vervolgens een zone met de naam *contoso.net*wilt maken, u dit doen en deze naar het IP-adres van ILB richten. De aangepaste domeinnaam werkt voor app-aanvragen, maar niet voor de scm-site. De scm-site is alleen beschikbaar op * &lt;appname&gt;.scm.&lt; asename&gt;.appserviceenvironment.net*. 

De zone met de naam *.&lt; asename&gt;.appserviceenvironment.net* is wereldwijd uniek. Vóór mei 2019 konden klanten het domeinachtervoegsel van de ILB ASE opgeven. Als u *.contoso.com* voor het domeinachtervoegsel wilt gebruiken, kon u dit doen en dat zou de scm-site omvatten. Er waren uitdagingen met dat model, waaronder; het beheren van het standaard SSL-certificaat, het ontbreken van eenmalige aanmelding met de scm-site en de vereiste om een wildcardcertificaat te gebruiken. Het ILB ASE standaard certificaat upgradeproces was ook storend en zorgde ervoor dat de toepassing opnieuw werd opgestart. Om deze problemen op te lossen, werd het ilb ASE-gedrag gewijzigd om een domeinachtervoegsel te gebruiken op basis van de naam van de ASE en met een achtervoegsel van Microsoft. De wijziging in het ILB ASE-gedrag heeft alleen gevolgen voor ILB ASEs die na mei 2019 zijn gemaakt. Reeds bestaande ILB ASEs moeten nog steeds het standaardcertificaat van de ASE en hun DNS-configuratie beheren.

## <a name="publishing"></a>Publiceren

In een ASE, net als bij de multitenant App Service, u publiceren op basis van deze methoden:

- Webimplementatie
- FTP
- Continue integratie (CI)
- Slepen en neerzetten in de Kudu-console
- Een IDE, zoals Visual Studio, Eclipse of IntelliJ IDEA

Met een externe ASE werken deze publicatieopties allemaal op dezelfde manier. Zie [Implementatie in Azure App Service][AppDeploy]voor meer informatie.

Met een ILB ASE zijn de publicatieeindpunten alleen beschikbaar via de ILB. De ILB is op een prive-IP in het ASE subnet in het virtuele netwerk. Als u geen netwerktoegang hebt tot de ILB, u geen apps op die ASE publiceren. Zoals vermeld in [Maken en gebruik maken van een ILB ASE,][MakeILBASE]moet u DNS configureren voor de apps in het systeem. Deze eis omvat het SCM-eindpunt. Als de eindpunten niet goed zijn gedefinieerd, u niet publiceren. Uw IDC's moeten ook toegang hebben tot het ILB om deze rechtstreeks te kunnen publiceren.

Zonder extra wijzigingen werken op internet gebaseerde CI-systemen zoals GitHub en Azure DevOps niet met een ILB ASE omdat het publicatieeindpunt niet toegankelijk is via internet. U publiceren naar een ILB ASE van Azure DevOps inschakelen door een zelfgehoste releaseagent in het virtuele netwerk te installeren die de ILB ASE bevat. Je ook een CI-systeem gebruiken dat een pull-model gebruikt, zoals Dropbox.

De publicatie-eindpunten voor apps in een ILB AS-omgeving maken gebruik van het domein waarmee de ILB AS-omgeving is gemaakt. Je het zien in het publicatieprofiel van de app en in het portaalvenster van de app (in **Overzicht** > **Essentials** en ook in **Eigenschappen).**

## <a name="storage"></a>Storage

Een ASE heeft 1 TB opslagruimte voor alle apps in de ASE. Een App Service-abonnement in de SKU voor geïsoleerde prijzen heeft standaard een limiet van 250 GB. Als u vijf of meer App Service-abonnementen hebt, moet u ervoor zorgen dat u de limiet van 1 TB van de ASE niet overschrijdt. Als u meer dan de limiet van 250 GB in één App Service-abonnement nodig hebt, neemt u contact op met ondersteuning om de limiet voor appservice-apps aan te passen tot maximaal 1 TB. Wanneer de planlimiet wordt aangepast, is er nog steeds een limiet van 1 TB voor alle App Service-abonnementen in de ASE.

## <a name="logging"></a>Logboekregistratie

U uw ASE integreren met Azure Monitor om logboeken over de ASE te verzenden naar Azure Storage, Azure Event Hubs of Log Analytics. Deze items worden vandaag nog geregistreerd:

| Situatie | Bericht |
|---------|----------|
| ASE is ongezond | De opgegeven ASE is niet in orde vanwege een ongeldige virtuele netwerkconfiguratie. De ASE wordt opgeschort als de ongezonde toestand doorgaat. Zorg ervoor dat de hier https://docs.microsoft.com/azure/app-service/environment/network-infogedefinieerde richtlijnen worden gevolgd: . |
| ASE subnet is bijna uit de ruimte | De opgegeven ASE bevindt zich in een subnet dat bijna buiten de ruimte is. Er {0} zijn nog adressen. Zodra deze adressen zijn uitgeput, zal de ASE niet in staat zijn om te schalen.  |
| ASE nadert totale instantielimiet | De opgegeven ASE nadert de totale instantielimiet van de ASE. Het bevat {0} momenteel App Service Plan exemplaren van een maximum van 201 exemplaren. |
| ASE kan geen afhankelijkheid bereiken | De opgegeven ASE kan {0}niet worden bereikt .  Zorg ervoor dat de hier https://docs.microsoft.com/azure/app-service/environment/network-infogedefinieerde richtlijnen worden gevolgd: . |
| ASE is geschorst | De opgegeven ASE wordt opgeschort. De ASE-opschorting kan het gevolg zijn van een accounttekort of een ongeldige virtuele netwerkconfiguratie. Los de hoofdoorzaak op en hervat de ASE om verkeer te blijven bedienen. |
| ASE upgrade is gestart | Een platformupgrade naar de opgegeven ASE is begonnen. Verwacht vertragingen bij schaalbewerkingen. |
| ASE-upgrade is voltooid | Een platformupgrade naar de opgegeven ASE is voltooid. |
| Schaalbewerkingen zijn gestart | Een App Service-abonnement ({0}) is begonnen met schalen. Gewenste {1} staat:{2} Ik werknemers.
| Schaalbewerkingen zijn voltooid | Een App Service-abonnement ({0}) is voltooid schalen. Huidige {1} staat:{2} I werknemers. |
| Schaalbewerkingen zijn mislukt | Een App Service-abonnement ({0}) is niet op schaal. Huidige {1} staat:{2} I werknemers. |

Ga als u wilt inloggen op uw ASE:

1. Ga in de portal naar **Diagnostische instellingen**.
1. Selecteer **Diagnostische instelling toevoegen**.
1. Geef een naam op voor de logintegratie.
1. Selecteer en configureer de gewenste logboekbestemmingen.
1. Selecteer **AppServiceEnvironmentPlatformLogs**.

![ASE diagnostische logboekinstellingen][4]

Als u integreert met Log Analytics, u de logboeken bekijken door **logboeken** te selecteren in de ASE-portal en een query te maken tegen **AppServiceEnvironmentPlatformLogs.** Logboeken worden alleen uitgezonden wanneer uw ASE een gebeurtenis heeft die deze activeert. Als uw ASE niet over een dergelijke gebeurtenis beschikt, worden er geen logboeken weergegeven. Als u snel een voorbeeld van logboeken in uw Log Analytics-werkruimte wilt zien, voert u een schaalbewerking uit met een van de app-serviceplannen in uw ASE. U vervolgens een query uitvoeren tegen **AppServiceEnvironmentPlatformLogs** om deze logboeken te bekijken. 

**Een waarschuwing maken**

Als u een waarschuwing wilt maken voor uw logboeken, volgt u de instructies in [Logboekwaarschuwingen maken, weergeven en beheren met Azure Monitor][logalerts]. In het kort:

* De pagina Waarschuwingen openen in uw ASE-portal
* **Nieuwe waarschuwingsregel selecteren**
* Selecteer uw resource als uw Logboekanalysewerkruimte
* Stel uw voorwaarde in met een aangepaste logboekzoekopdracht om een query te gebruiken zoals :"AppServiceEnvironmentPlatformLogs | waar ResultDescription "is begonnen met schalen" of wat je maar wilt. Stel de drempelwaarde in. 
* Voeg desgewenst een actiegroep toe of maak deze. De actiegroep is waar u het antwoord op de waarschuwing definieert, zoals het verzenden van een e-mail of een sms-bericht
* Geef uw waarschuwing een naam en sla deze op.

## <a name="upgrade-preference"></a>Voorkeur voor upgrade

Als u meerdere AsEs hebt, wilt u misschien dat sommige AsEs worden geüpgraded voordat andere. Binnen het object ASE **HostingEnvironment Resource Manager** u een waarde instellen voor **upgradeVoorkeur**. De instelling **upgradeVoorkeur** kan worden geconfigureerd met behulp https://resources.azure.comvan een sjabloon, ARMClient of . De drie mogelijke waarden zijn:

- **Geen:** Azure zal uw ASE in willekeurige batch upgraden. Dit is de standaardwaarde.
- **Vroeg**: Uw ASE wordt in de eerste helft van de app-service-upgrades geüpgraded.
- **Laat**: Uw ASE wordt in de tweede helft van de app-service-upgrades geüpgraded.

Als u de https://resources.azure.comvolgende stappen gebruikt, voert u de volgende stappen uit om de waarde **voor upgradevoorkeuren** in te stellen:

1. Ga naar resources.azure.com en meld u aan met uw Azure-account.
1. Ga door de\/\[bronnen\]\/voor\/\[abonnementen abonnement\]\/\/naam resourceGroepen resource group name\/providers Microsoft.Web hostingEnvironments\/\[ASE naam\].
1. Selecteer **Bovenaan lezen/schrijven.**
1. Selecteer **Bewerken**.
1. **UpgradeInstellenVoorkeur** naar welke van de drie waarden u wilt.
1. Selecteer **Patch**.

![bronnen azure com-weergave][5]

De **functie upgradeVoorkeuren** is het meest logisch als je meerdere ASEs hebt, omdat je 'Vroege' AS's worden geüpgraded voor je 'Late' ASEs. Wanneer u meerdere ASEs hebt, moet u uw ontwikkeling instellen en ASEs als "Vroeg" en uw productie-ASEs als "laat" testen.

## <a name="pricing"></a>Prijzen

De prijsstelling SKU genaamd *Isolated* is alleen voor gebruik met ASEs. Alle App Service-abonnementen die in de ASE worden gehost, staan in de SKU voor geïsoleerde prijzen. Geïsoleerde tarieven voor App Service-abonnementen kunnen per regio verschillen.

Naast de prijs van uw App Service-abonnementen, is er een vast tarief voor de ASE zelf. Het vaste tarief verandert niet met de grootte van uw ASE. It pays for the ASE infrastructure at a default scale rate of one additional front end for every 15 App Service plan instances.

Als de standaardschaalsnelheid van één front-end voor elke 15 App Service-app-abonnementexemplaren niet snel genoeg is, u de verhouding aanpassen waarmee front-ends worden toegevoegd of de grootte van de fronteinden. Wanneer u de verhouding of grootte aanpast, betaalt u voor de front-endcores die standaard niet zouden worden toegevoegd.

Als u bijvoorbeeld de schaalverhouding aanpast aan 10, wordt voor elke 10 exemplaren in uw App Service-abonnementen een front-end toegevoegd. De vaste vergoeding dekt een schaaltarief van één front-end voor elke 15 exemplaren. With a scale ratio of 10, you pay a fee for the third front end that's added for the 10 App Service plan instances. U hoeft er niet voor te betalen wanneer u 15 exemplaren bereikt omdat het automatisch is toegevoegd.

Als u de grootte van de uiteinden aanpast aan twee kernen, maar de verhouding niet aanpast, betaalt u voor de extra cores. Een ASE is gemaakt met twee front-ends, dus zelfs onder de automatische schaaldrempel zou je betalen voor twee extra cores als je de grootte verhoogd tot twee-core front-ends.

Zie [Azure App Service-prijzen][Pricing]voor meer informatie .

## <a name="delete-an-ase"></a>Een ASE verwijderen

Een ASE verwijderen:

1. Selecteer **Verwijderen** boven aan het deelvenster **App-serviceomgeving.**

1. Voer de naam van uw ASE in om te bevestigen dat u deze wilt verwijderen. Wanneer u een ASE verwijdert, verwijdert u ook alle inhoud die erin zit.

    ![ASE-verwijdering][3]

1. Selecteer **OK**.

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
[logalerts]: ../../azure-monitor/platform/alerts-log.md
