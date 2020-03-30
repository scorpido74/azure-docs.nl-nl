---
title: Veelgestelde vragen over Azure DevTest Labs | Microsoft Documenten
description: In dit artikel vindt u antwoorden op enkele veelgestelde vragen (FAQ) over Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: de99e9b1e4adceaf08beaf8ad3b5ea114b31a586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270782"
---
# <a name="azure-devtest-labs-faq"></a>Veelgestelde vragen over Azure DevTest Labs
Krijg antwoorden op enkele van de meest voorkomende vragen over Azure DevTest Labs.

## <a name="blog-post"></a>Blogbericht
Onze DevTest Labs Team blog is met pensioen vanaf 20 maart 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Waar kan ik vanaf nu functie-updates bijhouden?
Vanaf nu plaatsen we functie-updates en informatieve blogberichten op de Azure-blog en Azure-updates. Deze blogposts zullen ook naar onze documentatie linken waar nodig.

Abonneer u op de [Azure-updates van DevTest Labs](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) en [DevTest Labs](https://azure.microsoft.com/updates/?product=devtest-lab) om op de hoogte te blijven van nieuwe functies in DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>Wat gebeurt er met de bestaande blogposts?
We werken momenteel aan het migreren van bestaande blogberichten (met uitzondering van updates voor uitval) naar onze [Documentatie DevTest Labs.](devtest-lab-overview.md) Wanneer de MSDN-blog wordt afgeschaft, wordt deze doorgestuurd naar het documentatieoverzicht voor DevTest Labs. Zodra u bent omgeleid, u zoeken naar het artikel dat u zoekt in de titel 'Filteren op'. We hebben nog niet alle berichten gemigreerd, maar moet worden gedaan tegen het einde van deze maand. 


### <a name="where-do-i-see-outage-updates"></a>Waar zie ik updates voor storingen?
We zullen het plaatsen van updates van de storing met behulp van onze Twitter handvat vanaf nu. Volg ons op Twitter om de laatste updates over storingen en bekende bugs te krijgen.

### <a name="twitter"></a>Twitter
Onze Twitter handvat:[@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Algemeen
### <a name="what-if-my-question-isnt-answered-here"></a>Wat als mijn vraag hier niet wordt beantwoord?
Als uw vraag hier niet wordt vermeld, laat het ons weten, zodat we u kunnen helpen een antwoord te vinden.

- Plaats een vraag aan het einde van deze FAQ.
- Om een breder publiek te bereiken, plaatst u een vraag op het [Azure DevTest Labs MSDN-forum.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs) Neem contact op met het Azure DevTest Labs-team en andere leden van de community.
- Voor functieaanvragen dient u uw aanvragen en ideeën in bij [Azure DevTest Labs User Voice.](https://feedback.azure.com/forums/320373-azure-devtest-labs)

### <a name="what-is-a-microsoft-account"></a>Wat is een Microsoft-account?
Een Microsoft-account is een account dat u gebruikt voor bijna alles wat u met Microsoft-apparaten en -services doet. Het is een e-mailadres en wachtwoord dat u gebruikt om u aan te melden bij Skype, Outlook.com, OneDrive, Windows Phone, Azure en Xbox Live. Eén account betekent dat uw bestanden, foto's, contactpersonen en instellingen u op elk apparaat kunnen volgen.

> [!NOTE]
> Een Microsoft-account heette vroeger een Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Waarom zou ik Azure DevTest Labs gebruiken?
Azure DevTest Labs kan uw team tijd en geld besparen. Ontwikkelaars kunnen hun eigen omgevingen maken door verschillende bases te gebruiken. Ze kunnen ook artefacten gebruiken om toepassingen snel te implementeren en te configureren. Door aangepaste afbeeldingen en formules te gebruiken, u virtuele machines (VM's) opslaan als sjablonen en deze eenvoudig in het hele team reproduceren. DevTest Labs biedt ook verschillende configureerbare beleidsregels die labbeheerders kunnen gebruiken om verspilling te verminderen en de omgevingen van een team te beheren. Dit beleid omvat automatisch afsluiten, kostendrempel, maximale VM's per gebruiker en maximale VM-grootte. Voor een meer diepgaande uitleg van DevTest Labs, zie het [overzicht](devtest-lab-overview.md) of de [inleidende video](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Wat betekent 'zorgeloze selfservice'?
Zorgeloze selfservice betekent dat ontwikkelaars en testers hun eigen omgevingen creëren als dat nodig is. Beheerders hebben de zekerheid te weten dat DevTest Labs kan helpen bij het instellen van de juiste toegang, het minimaliseren van afval en het beheersen van de kosten. Beheerders kunnen opgeven welke VM-formaten zijn toegestaan, het maximum aantal VM's en wanneer VM's worden gestart en afgesloten. DevTest Labs maakt het ook gemakkelijk om kosten te controleren en waarschuwingen in te stellen, om u te helpen op de hoogte te blijven van hoe labresources worden gebruikt.

### <a name="how-can-i-use-devtest-labs"></a>Hoe kan ik DevTest Labs gebruiken?
DevTest Labs is handig wanneer u dev- of testomgevingen nodig hebt en deze snel wilt reproduceren of beheren met behulp van kostenbesparend beleid.
Hier volgen enkele scenario's waar onze klanten DevTest Labs voor gebruiken:

- Beheer dev- en testomgevingen op één plek. Gebruik beleid om de kosten te verlagen en aangepaste afbeeldingen te maken om builds in het hele team te delen.
- Ontwikkel een toepassing met behulp van aangepaste afbeeldingen om de schijfstatus op te slaan tijdens de ontwikkelingsfasen.
- Volg de kosten in relatie tot de voortgang.
- Maak massatestomgevingen voor kwaliteitsborgingstests.
- Gebruik artefacten en formules om een toepassing eenvoudig te configureren en te reproduceren in verschillende omgevingen.
- Distribueer VM's voor hackathons (collaboratief dev of testwerk) en deprovisioneer ze vervolgens eenvoudig wanneer het evenement eindigt.

### <a name="how-am-i-billed-for-devtest-labs"></a>Hoe word ik gefactureerd voor DevTest Labs?
DevTest Labs is een gratis service. Het maken van labs en het configureren van beleid, sjablonen en artefacten in DevTest Labs is gratis. U betaalt alleen voor de Azure-resources die in uw labs worden gebruikt, zoals VM's, opslagaccounts en virtuele netwerken. Zie [Azure DevTest Labs-prijzen](https://azure.microsoft.com/pricing/details/devtest-lab/)voor meer informatie over de kosten van labresources.

## <a name="security"></a>Beveiliging

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Wat zijn de verschillende beveiligingsniveaus in DevTest Labs?
Beveiligingstoegang wordt bepaald door RBAC (Role-Based Access Control). Om te leren hoe toegang werkt, helpt het om de verschillen tussen een machtiging, een rol en een bereik te leren, zoals gedefinieerd door RBAC.

- **Machtiging**: Een machtiging is een gedefinieerde toegang tot een specifieke actie. Een machtiging kan bijvoorbeeld worden gelezen toegang tot alle VM's.
- **Rol**: Een rol is een set machtigingen die kunnen worden gegroepeerd en toegewezen aan een gebruiker. Een gebruiker met een eigenaarrol voor een abonnement heeft bijvoorbeeld toegang tot alle bronnen binnen een abonnement.
- **Bereik:** een bereik is een niveau binnen de hiërarchie van een Azure-bron. Een scope kan bijvoorbeeld een resourcegroep, één lab of het hele abonnement zijn.

Binnen het bereik van DevTest Labs zijn er twee soorten rollen die gebruikersmachtigingen definiëren:

- **Lab eigenaar**: Een lab eigenaar heeft toegang tot alle middelen in het lab. Een labeigenaar kan beleid wijzigen, lezen en schrijven naar vm's, het virtuele netwerk wijzigen, enzovoort.
- **Labgebruiker**: een labgebruiker kan alle labbronnen bekijken, zoals VM's, beleidsregels en virtuele netwerken. Een labgebruiker kan echter geen beleid of VM's wijzigen die door andere gebruikers zijn gemaakt.

U ook aangepaste rollen maken in DevTest Labs. Zie [Gebruikersmachtigingen verlenen voor specifiek labbeleid voor](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)meer informatie over het maken van aangepaste rollen in DevTest Labs.

Omdat scopes hiërarchisch zijn, krijgt de gebruiker, wanneer een gebruiker machtigingen heeft op een bepaald bereik, automatisch toestemming voor die machtigingen op elk bereik op een lager niveau in het bereik. Als een gebruiker bijvoorbeeld de rol van abonnementseigenaar krijgt toegewezen, heeft de gebruiker toegang tot alle bronnen in een abonnement. Deze bronnen omvatten VM's, virtuele netwerken en labs. Een abonnementeigenaar neemt automatisch de rol van labeigenaar over. Het tegendeel is echter niet waar. Een labeigenaar heeft toegang tot een lab, wat een lager bereik is dan het abonnementsniveau. Een labeigenaar kan dus geen VM's, virtuele netwerken of andere bronnen zien die zich buiten het lab bevinden.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Hoe definieer ik op rollen gebaseerdtoegangscontrole voor mijn DevTest Labs-omgevingen om ervoor te zorgen dat IT kan bepalen terwijl ontwikkelaars/tests hun werk kunnen doen?
Er is een breed patroon, maar de details zijn afhankelijk van uw organisatie.

Central IT moet alleen bezitten wat nodig is en de project- en toepassingsteams in staat stellen het vereiste niveau van controle te hebben. Meestal betekent dit dat centrale IT eigenaar is van het abonnement en de belangrijkste IT-functies zoals netwerkconfiguraties afhandelt. De set **eigenaren** voor een abonnement moet klein zijn. Deze eigenaren kunnen extra eigenaren nomineren wanneer dat nodig is, of beleid op abonnementsniveau toepassen, bijvoorbeeld 'Geen openbaar IP'.

Er kan een subset van gebruikers zijn die toegang nodig hebben voor een abonnement, zoals Tier1- of Tier 2-ondersteuning. In dit geval raden we u aan deze gebruikers de toegang tot de **bijdrager** te geven, zodat ze de bronnen kunnen beheren, maar geen gebruikerstoegang kunnen bieden of beleid kunnen aanpassen.

De DevTest Labs-bron moet eigendom zijn van eigenaren die dicht bij het project/toepassingsteam staan. Het is omdat ze begrijpen hun eisen voor machines, en de vereiste software. In de meeste organisaties, de eigenaar van deze DevTest Labs bron is meestal het project / ontwikkeling leiden. Deze eigenaar kan gebruikers en beleid binnen de labomgeving beheren en kan alle VM's beheren in de DevTest Labs-omgeving.

De leden van het project/applicatieteam moeten worden toegevoegd aan de rol **DevTest Labs-gebruikers.** Deze gebruikers kunnen virtuele machines maken (in lijn met het beleid op lab- en abonnementsniveau). Ze kunnen ook hun eigen virtuele machines beheren. Ze kunnen geen virtuele machines beheren die van andere gebruikers zijn.

Zie Azure [enterprise scaffold - prescriptieve documentatie voor abonnementsbeheer](/azure/architecture/cloud-adoption/appendix/azure-scaffold)voor meer informatie.


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Hoe maak ik een rol waarmee gebruikers een specifieke taak kunnen uitvoeren?
Zie [Gebruikersmachtigingen verlenen aan specifiek labbeleid](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)voor een uitgebreid artikel over het maken van aangepaste rollen en het toewijzen van machtigingen aan een rol. Hier is een voorbeeld van een script dat de rol **DevTest Labs Advanced User**maakt, die toestemming heeft om alle VM's in het lab te starten en te stoppen:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Hoe kan een organisatie ervoor zorgen dat er bedrijfsbeveiligingsbeleid is ingevoerd?
Een organisatie kan dit bereiken door de volgende acties uit te voeren:

- Ontwikkelen en publiceren van een uitgebreid beveiligingsbeleid. Het beleid verwoordt de regels van aanvaardbaar gebruik in verband met het gebruik van software, cloud-assets. Het bepaalt ook wat duidelijk in strijd is met het beleid.
- Ontwikkel een aangepaste afbeelding, aangepaste artefacten en een implementatieproces dat orkestratie mogelijk maakt binnen de beveiligingsrealm die is gedefinieerd met active directory. Deze aanpak dwingt de bedrijfsgrens af en stelt een gemeenschappelijke set milieucontroles vast. Deze besturingselementen tegen de omgeving die een ontwikkelaar kan overwegen bij het ontwikkelen en volgen van een veilige ontwikkelingslevenscyclus als onderdeel van hun algehele proces. Het doel is ook om een omgeving te bieden die niet al te beperkend is en de ontwikkeling kan belemmeren, maar een redelijke reeks controles. Het groepsbeleid op de organisatie-eenheid (OU) die virtuele laboratoriummachines bevat, kan een subset zijn van het totale groepsbeleid dat in de productie wordt gevonden. Als alternatief kunnen ze een extra set zijn om geïdentificeerde risico's goed te beperken.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Hoe kan een organisatie de integriteit van gegevens waarborgen om ervoor te zorgen dat remoting-ontwikkelaars geen code kunnen verwijderen of malware of niet-goedgekeurde software kunnen introduceren?
Er zijn verschillende controlelagen om de dreiging van externe consultants, aannemers of werknemers die samenwerken in DevTest Labs te beperken.

Zoals eerder vermeld, moet de eerste stap een aanvaardbaar gebruiksbeleid hebben opgesteld en gedefinieerd dat duidelijk de gevolgen schetst wanneer iemand het beleid schendt.

De eerste laag van besturingselementen voor externe toegang is het toepassen van een beleid voor externe toegang via een VPN-verbinding die niet rechtstreeks is verbonden met het lab.

De tweede besturingselementenlaag is het toepassen van een set groepsbeleidsobjecten die kopiëren en plakken via extern bureaublad voorkomen. Er kan een netwerkbeleid worden geïmplementeerd om uitgaande services uit de omgeving, zoals FTP- en RDP-services, niet uit de omgeving toe te staan. Door de gebruiker gedefinieerde routering kan al het Azure-netwerkverkeer weer vrijmaken, maar de routering kan geen rekening houden met alle URL's waarmee het uploaden van gegevens mogelijk is, tenzij deze via een proxy wordt beheerd om inhoud en sessies te scannen. Openbare IP's kunnen worden beperkt binnen het virtuele netwerk ter ondersteuning van DevTest Labs om het overbruggen van een externe netwerkbron niet toe te staan.

Uiteindelijk moeten dezelfde soorten beperkingen worden toegepast in de hele organisatie, die rekening houden met alle mogelijke methoden van verwijderbare media of externe URL's die een bericht van inhoud kunnen accepteren. Overleg met uw beveiligingsprofessional om een beveiligingsbeleid te controleren en uit te voeren. Zie [Microsoft Cyber Security voor](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)meer aanbevelingen.

## <a name="lab-configuration"></a>Labconfiguratie

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Hoe maak ik een lab op basis van een Resource Manager-sjabloon?
We bieden een [GitHub-opslagplaats van lab Azure Resource Manager-sjablonen](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) die u implementeren of wijzigen om aangepaste sjablonen voor uw labs te maken. Elke sjabloon heeft een koppeling om het lab te implementeren zoals het is in uw eigen Azure-abonnement. U de sjabloon ook aanpassen en [implementeren met PowerShell of Azure CLI.](../azure-resource-manager/templates/deploy-powershell.md)


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Kan ik alle virtuele machines maken in een gemeenschappelijke resourcegroep in plaats daarvan met elke machine in zijn eigen resourcegroep?
Ja, als eigenaar van een lab u het lab de toewijzing van resourcegroepen voor u laten afhandelen of alle virtuele machines laten maken in een gemeenschappelijke resourcegroep die u opgeeft.

Afzonderlijk resourcegroepscenario:
-   DevTest Labs maakt een nieuwe brongroep voor elke openbare / private IP virtuele machine die u spin-up
-   DevTest Labs maakt een brongroep voor gedeelde IP-machines die tot dezelfde grootte behoren.

Scenario van de gemeenschappelijke resourcegroep:
-   Alle virtuele machines worden opgesponnen in de algemene resourcegroep die u opgeeft. Meer informatie over [de toewijzing van resourcegroepen voor het lab](https://aka.ms/RGControl).

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Hoe onderhoud ik een naamgevingsconventie in mijn DevTest Labs-omgeving?
U de huidige overeenkomsten voor bedrijfsnaamgeving uitbreiden naar Azure-bewerkingen en deze consistent maken in de Omgeving van DevTest Labs. Bij het implementeren van DevTest Labs raden we je aan een specifiek startbeleid te hebben. U implementeert dit beleid via een centraal script en JSON-sjablonen om consistentie af te dwingen. Naamgevingsbeleid kan worden geïmplementeerd via Azure-beleid dat op abonnementsniveau wordt toegepast. Zie Voorbeelden van [Azure-beleid](../governance/policy/samples/index.md)voor JSON-voorbeelden voor JSON-voorbeelden .

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Hoeveel labs kan ik maken onder hetzelfde abonnement?
Er is geen specifieke limiet voor het aantal labs dat per abonnement kan worden gemaakt. De hoeveelheid resources die per abonnement wordt gebruikt, is echter beperkt. U lezen over de [limieten en quota voor Azure-abonnementen](../azure-resource-manager/management/azure-subscription-service-limits.md) en hoe u deze limieten [verhogen.](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)


### <a name="how-many-vms-can-i-create-per-lab"></a>Hoeveel VM's kan ik per lab maken?
Er is geen specifieke limiet voor het aantal VM's dat per lab kan worden gemaakt. De resources (VM-cores, openbare IP-adressen, enzovoort) die worden gebruikt, zijn echter beperkt per abonnement. U lezen over de [limieten en quota voor Azure-abonnementen](../azure-resource-manager/management/azure-subscription-service-limits.md) en hoe u deze limieten [verhogen.](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Hoe bepaal ik de verhouding van gebruikers per lab en het totale aantal labs dat nodig is in een organisatie?
We raden aan dat business units en ontwikkelingsgroepen die zijn gekoppeld aan hetzelfde ontwikkelingsproject worden geassocieerd met hetzelfde lab. Hiermee kunnen dezelfde typen beleidsregels, afbeeldingen en afsluitbeleid op beide groepen worden toegepast.

Het kan ook nodig zijn om geografische grenzen te overwegen. Ontwikkelaars in het noordoosten van de Verenigde Staten (VS) kunnen bijvoorbeeld gebruik maken van een lab dat is ingericht in Oost-US2. En, ontwikkelaars in Dallas, Texas, en Denver, Colorado kan worden gericht op een bron te gebruiken in us South Central. Als er een samenwerkingsverband is met een externe derde partij, kunnen ze worden toegewezen aan een lab dat niet wordt gebruikt door interne ontwikkelaars.

U een lab ook gebruiken voor een specifiek project binnen Azure DevOps Projects. Vervolgens past u beveiliging toe via een opgegeven Azure Active Directory-groep, waarmee u toegang heeft tot beide sets resources. Het virtuele netwerk dat aan het lab is toegewezen, kan een andere grens zijn om gebruikers te consolideren.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Hoe kunnen we voorkomen dat de verwijdering van middelen binnen een lab?
We raden u aan de juiste machtigingen in te stellen op labniveau, zodat alleen geautoriseerde gebruikers resources kunnen verwijderen of het labbeleid kunnen wijzigen. Ontwikkelaars moeten worden geplaatst in de **DevTest Labs Gebruikers** groep. De leadontwikkelaar of de infrastructuurlead moet de **Eigenaar van DevTest Labs**zijn. We raden je aan om maar twee labeigenaren te hebben. Dit beleid strekt zich uit naar de code repository om corruptie te voorkomen. Labgebruikers hebben rechten om resources te gebruiken, maar kunnen het labbeleid niet bijwerken. Zie het volgende artikel met de rollen en rechten die elke ingebouwde groep in een lab heeft: [Eigenaren en gebruikers toevoegen in Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Hoe deel ik een directe link naar mijn lab?

1. Ga in de [Azure-portal](https://portal.azure.com)naar het lab.
2. Kopieer de URL van het **lab** vanuit uw browser en deel deze vervolgens met uw labgebruikers.

> [!NOTE]
> Als een labgebruiker een externe gebruiker is die een Microsoft-account heeft, maar geen lid is van het Active Directory-exemplaar van uw organisatie, ziet de gebruiker mogelijk een foutbericht wanneer hij toegang probeert te krijgen tot de gedeelde koppeling. Als een externe gebruiker een foutbericht ziet, vraagt u de gebruiker om eerst zijn naam te selecteren in de rechterbovenhoek van de Azure-portal. Vervolgens kan de gebruiker in de sectie Map van het menu de map selecteren waar het lab bestaat.

## <a name="virtual-machines"></a>Virtuele machines

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Waarom kan ik geen VM's zien op de pagina Virtuele machines die ik zie in DevTest Labs?
Wanneer u een vm maakt in DevTest Labs, krijgt u toestemming om toegang te krijgen tot die vm. U de VM zowel op de labspagina als op de pagina **Virtuele machines** bekijken. Gebruikers die zijn toegewezen aan de rol **DevTest Labs Owner,** kunnen alle VM's zien die in het lab zijn gemaakt op de pagina **Alle virtuele machines van** het lab. Gebruikers die de rol **DevTest Labs-gebruiker** hebben, krijgen echter niet automatisch leestoegang tot VM-bronnen die andere gebruikers hebben gemaakt. Deze VM's worden dus niet weergegeven op de pagina **Virtuele machines.**


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Hoe maak ik meerdere VM's tegelijk van dezelfde sjabloon?
U hebt twee opties om tegelijkertijd meerdere VM's van dezelfde sjabloon te maken:

- U de [azure devOps-takenextensie](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)gebruiken.
- U [een Resource Manager-sjabloon genereren](devtest-lab-add-vm.md#save-azure-resource-manager-template) terwijl u een vm maakt en de sjabloon [Resourcebeheer implementeren vanuit Windows PowerShell](../azure-resource-manager/templates/deploy-powershell.md).
- U ook meer dan één exemplaar opgeven van een machine die moet worden gemaakt tijdens het maken van virtuele machines. Zie het document over het maken van [een virtuele labmachine](devtest-lab-add-vm.md)voor meer informatie over het maken van meerdere exemplaren van virtuele machines.

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Hoe verplaats ik mijn bestaande Azure VM's naar mijn DevTest Labs-lab?
Ga als u uw bestaande VM's kopiëren naar DevTest Labs:

1.  Kopieer het VHD-bestand van uw bestaande VM met een [Windows PowerShell-script.](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1)
2.  Maak de [aangepaste afbeelding](devtest-lab-create-template.md) in uw DevTest Labs-lab.
3.  Maak een VM in het lab op basis van uw aangepaste afbeelding.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Kan ik meerdere schijven aan mijn VM's koppelen?

Ja, u meerdere schijven aan uw VM's koppelen.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Als ik een Windows OS-afbeelding wil gebruiken voor mijn testen, moet ik dan een MSDN-abonnement kopen?
Als u Windows-client-besturingssysteemafbeeldingen (Windows 7 of een latere versie) wilt gebruiken voor uw ontwikkeling of testen in Azure, neemt u een van de volgende stappen:

- [Koop een MSDN-abonnement.](https://www.visualstudio.com/products/how-to-buy-vs)
- Als u een Enterprise-overeenkomst hebt, maakt u een Azure-abonnement met de [Enterprise Dev/Test-aanbieding](https://azure.microsoft.com/offers/ms-azr-0148p).

Zie [Maandelijks Azure-tegoed voor Visual Studio-abonnees voor](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)meer informatie over de Azure-credits voor elke MSDN-aanbieding.


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Hoe automatiseer ik het proces van het verwijderen van alle VM's in mijn lab?
Als eigenaar van een lab u VM's uit uw lab verwijderen in de Azure-portal. U ook alle VM's in uw lab verwijderen met behulp van een PowerShell-script. Wijzig in het volgende voorbeeld onder de waarden om de opmerking **te wijzigen** de parameterwaarden. U de waarden subscriptionId, labResourceGroup en labName ophalen uit het labvenster in de Azure-portal.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Omgevingen

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Hoe kan ik Resource Manager-sjablonen gebruiken in mijn DevTest Labs-omgeving?
U implementeert uw Resource Manager-sjablonen in een DevTest Labs-omgeving met behulp van stappen die worden genoemd in de functie Omgevingen in het artikel [DevTest Labs.](devtest-lab-test-env.md) In principe controleert u uw Resource Manager-sjablonen in een Git-opslagplaats (Azure Repos of GitHub) en voegt u een [privéopslagplaats voor uw sjablonen](devtest-lab-test-env.md) toe aan het lab. Dit scenario is mogelijk niet handig als u DevTest Labs gebruikt om ontwikkelingsmachines te hosten, maar kan handig zijn als u een faseringsomgeving bouwt, die representatief is voor de productie.

Het is ook vermeldenswaard dat het aantal virtuele machines per lab of per gebruiker optie alleen beperkt het aantal machines native gemaakt in het lab zelf, en niet door een omgeving (Resource Manager sjablonen).

## <a name="custom-images"></a>Aangepaste installatiekopieën

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Hoe kan ik een eenvoudig herhaalbaar proces opzetten om mijn aangepaste organisatieafbeeldingen in een DevTest Labs-omgeving te brengen?
Zie deze [video op Image Factory patroon](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Dit scenario is een geavanceerd scenario en de meegeleverde scripts zijn alleen voorbeeldscripts. Als er wijzigingen nodig zijn, moet u de scripts beheren en onderhouden die in uw omgeving worden gebruikt.

Zie [Een aangepaste afbeeldingsfabriek maken in Azure DevTest Labs](image-factory-create.md)voor gedetailleerde informatie over het maken van een afbeeldingsfabriek.

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Wat is het verschil tussen een aangepaste afbeelding en een formule?
Een aangepaste afbeelding is een beheerde afbeelding. Een formule is een afbeelding die u configureren met extra instellingen en vervolgens opslaan en reproduceren. Een aangepaste afbeelding heeft de voorkeur als u snel meerdere omgevingen wilt maken met dezelfde basisafbeelding, onveranderlijke afbeelding. Een formule is misschien beter als u de configuratie van uw VM wilt reproduceren met de nieuwste bits, als onderdeel van een virtueel netwerk of subnet of als een VM van een specifieke grootte. Zie [Aangepaste afbeeldingen en formules vergelijken in DevTest Labs](devtest-lab-comparing-vm-base-image-types.md)voor een meer diepgaande uitleg.

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Wanneer moet ik een formule versus aangepaste afbeelding gebruiken?
De beslissende factor in dit scenario is doorgaans kosten en hergebruik. Als u een scenario hebt waarbij veel gebruikers/laboratoria een afbeelding met veel software bovenop de basisafbeelding nodig hebben, u de kosten verlagen door een aangepaste afbeelding te maken. Het betekent dat het beeld eenmaal wordt gemaakt. Het vermindert de insteltijd van de virtuele machine en de kosten die worden gemaakt als gevolg van de virtuele machine draait wanneer setup optreedt.

Echter, een extra factor om op te merken is de frequentie van wijzigingen in uw softwarepakket. Als u dagelijks bouwt en vereist dat deze software op de virtuele machines van uw gebruikers staat, u overwegen een formule te gebruiken in plaats van een aangepaste afbeelding.

Zie [Aangepaste afbeeldingen en formules vergelijken](devtest-lab-comparing-vm-base-image-types.md) in DevTest Labs voor een meer diepgaande uitleg.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Hoe automatiseer ik het proces van het uploaden van VHD-bestanden om aangepaste afbeeldingen te maken?

Als u het uploaden van VHD-bestanden wilt automatiseren om aangepaste afbeeldingen te maken, hebt u twee opties:

- Gebruik [AzCopy](../storage/common/storage-use-azcopy-v10.md) om VHD-bestanden te kopiëren of te uploaden naar het opslagaccount dat aan het lab is gekoppeld.
- Gebruik [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Storage Explorer is een zelfstandige app die draait op Windows, OS X en Linux.

Ga als verkenner op zoek naar het doelopslagaccount dat aan uw lab is gekoppeld:

1.  Meld u aan bij [Azure Portal](https://portal.azure.com).
2.  Selecteer **resourcegroepen**in het linkermenu .
3.  Zoek en selecteer de resourcegroep die aan uw lab is gekoppeld.
4.  Selecteer **onder Overzicht**een van de opslagaccounts.
5.  Selecteer **Blobs**.
6.  Zoek naar uploads in de lijst. Als er geen bestaat, gaat u terug naar stap 4 en probeert u een ander opslagaccount.
7.  Gebruik de **URL** als bestemming in de opdracht AzCopy.

Wanneer moet ik een Azure Marketplace-afbeelding gebruiken versus mijn eigen aangepaste organisatieafbeelding?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Wanneer moet ik een Azure Marketplace-afbeelding gebruiken versus mijn eigen aangepaste organisatieafbeelding?
Azure Marketplace moet standaard worden gebruikt, tenzij u specifieke problemen of organisatorische vereisten hebt. Enkele veelvoorkomende voorbeelden zijn;

- Complexe software-installatie waarvoor een toepassing moet worden opgenomen als onderdeel van de basisafbeelding.
- Het installeren en instellen van een toepassing kan vele uren duren, wat geen efficiënt gebruik is van de rekentijd die moet worden toegevoegd aan een Azure Marketplace-afbeelding.
- Ontwikkelaars en testers hebben snel toegang tot een virtuele machine nodig en willen de insteltijd van een nieuwe virtuele machine minimaliseren.
- Naleving of wettelijke voorwaarden (bijvoorbeeld beveiligingsbeleid) die voor alle machines moeten gelden.
- Het gebruik van aangepaste afbeeldingen moet niet lichtvaardig worden beschouwd. Ze introduceren extra complexiteit, omdat u nu VHD-bestanden moet beheren voor die onderliggende basisafbeeldingen. U moet ook routinematig patch die basisbeelden met software-updates. Deze updates omvatten nieuwe besturingssysteemupdates en eventuele updates of configuratiewijzigingen die nodig zijn voor het softwarepakket zelf.

## <a name="artifacts"></a>Artefacten

### <a name="what-are-artifacts"></a>Wat zijn artefacten?
Artefacten zijn aanpasbare elementen die u gebruiken om uw nieuwste bits te implementeren of uw ontwikkelaarstools te implementeren op een vm. Voeg artefacten toe aan uw VM wanneer u de VM maakt. Nadat de VM is ingericht, implementeren en configureren de artefacten uw VM. Verschillende reeds bestaande artefacten zijn beschikbaar in onze [openbare GitHub repository.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) U ook [uw eigen artefacten schrijven.](devtest-lab-artifact-author.md)

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Mijn artefact is mislukt tijdens het maken van VM's. Hoe los ik het op?
Zie Hoe u [artefact-fouten diagnosticeren in DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)voor meer informatie over het verzamelen van logboeken voor uw mislukte artefact.

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Wanneer moet een organisatie een openbare artefactrepository gebruiken versus private artefact-opslagplaats en in DevTest Labs?
De [openbare artefact repository](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) biedt een eerste set van software pakketten die het meest worden gebruikt. Het helpt bij een snelle implementatie zonder tijd te hoeven investeren om gangbare ontwikkeltools en add-ins te reproduceren. U ervoor kiezen om hun eigen private repository te implementeren. U een openbare en een privéopslagplaats parallel gebruiken. U er ook voor kiezen om de openbare opslagplaats uit te schakelen. De criteria voor het implementeren van een privé-opslagplaats moeten worden bepaald door de volgende vragen en overwegingen:

- Heeft de organisatie een vereiste om corporate gelicentieerde software als onderdeel van hun DevTest Labs aanbod hebben? Als het antwoord ja is, moet er een privé-opslagplaats worden gemaakt.
- Ontwikkelt de organisatie aangepaste software die een specifieke bewerking biedt, die nodig is als onderdeel van het algehele inrichtingsproces? Als het antwoord ja is, moet een privé-opslagplaats worden geïmplementeerd.
- Als het governancebeleid van de organisatie isolatie vereist en externe opslagplaatsen niet onder direct configuratiebeheer door de organisatie staan, moet een private artefact-opslagplaats worden geïmplementeerd. Als onderdeel van dit proces kan een eerste kopie van de openbare repository worden gekopieerd en geïntegreerd met de private repository. Vervolgens kan de openbare repository worden uitgeschakeld, zodat niemand binnen de organisatie er meer toegang toe heeft. Deze aanpak dwingt alle gebruikers binnen de organisatie om slechts één repository te hebben die is goedgekeurd door de organisatie en configuratiedrift te minimaliseren.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Moet een organisatie plannen voor een enkele repository of meerdere repositories toestaan?
Als onderdeel van de algemene governance- en configuratiebeheerstrategie van uw organisatie raden we u aan een gecentraliseerde repository te gebruiken. Wanneer u meerdere repositories gebruikt, kunnen ze in de loop van de tijd silo's van onbeheerde software worden. Met een centrale repository kunnen meerdere teams artefacten uit deze repository gebruiken voor hun projecten. Het dwingt standaardisatie, beveiliging, gemak van beheer, en elimineert de verdubbeling van de inspanningen. Als onderdeel van de centralisatie worden de volgende acties aanbevolen praktijken voor langetermijnbeheer en duurzaamheid:

- Koppel de Azure Repos aan dezelfde Azure Active Directory-tenant die het Azure-abonnement gebruikt voor verificatie en autorisatie.
- Maak een `All DevTest Labs Developers` groep met de naam In Azure Active Directory die centraal wordt beheerd. Elke ontwikkelaar die bijdraagt aan de ontwikkeling van artefacten moet in deze groep worden geplaatst.
- Dezelfde Azure Active Directory-groep kan worden gebruikt om toegang te bieden tot de Azure Repos-opslagplaats en tot het lab.
- In Azure Repos moet vertakking of forking worden gebruikt om een in-development repository te scheiden van de primaire productieopslagplaats. Inhoud wordt alleen toegevoegd aan de master branch met een pull-aanvraag na een goede codecontrole. Zodra de coderevisor de wijziging goedkeurt, voegt een leadontwikkelaar, die verantwoordelijk is voor het onderhoud van de hoofdbranch, de bijgewerkte code samen.

## <a name="cicd-integration"></a>CI/CD-integratie

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Integreert DevTest Labs met mijn CI/CD toolchain?
Als u Azure DevOps gebruikt, u een [devTest Labs-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) gebruiken om uw releasepijplijn in DevTest Labs te automatiseren. Enkele van de taken die u met deze extensie uitvoeren, zijn:

- Een VM automatisch maken en implementeren. U de vm ook configureren met de nieuwste build met Azure File Copy- of PowerShell Azure DevOps Services-taken.
- Leg automatisch de status van een VM vast na het testen om een bug op dezelfde VM te reproduceren voor verder onderzoek.
- Verwijder de VM aan het einde van de releasepijplijn wanneer deze niet meer nodig is.

De volgende blogberichten bieden richtlijnen en informatie over het gebruik van de Azure DevOps Services-extensie:

- [DevTest Labs en de Azure DevOps-extensie](integrate-environments-devops-pipeline.md)
- [Een nieuwe VM implementeren in een bestaand DevTest Labs-lab van Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Azure DevOps Services releasebeheer gebruiken voor continue implementaties naar DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Voor andere cd-toolchains (continuous integration)/continuous delivery (CONTINUOUS Delivery) u dezelfde scenario's realiseren door [Azure Resource Manager-sjablonen](https://azure.microsoft.com/resources/templates/) te implementeren met [Azure PowerShell-cmdlets](../azure-resource-manager/templates/deploy-powershell.md) en [.NET SDKs.](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/) U ook [REST API's voor DevTest Labs](https://aka.ms/dtlrestapis) gebruiken om te integreren met uw toolchain.

## <a name="networking"></a>Netwerken

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Wanneer moet ik een nieuw virtueel netwerk maken voor mijn DevTest Labs-omgeving versus het gebruik van een bestaand virtueel netwerk?
Als uw VM's moeten communiceren met bestaande infrastructuur, u overwegen een bestaand virtueel netwerk te gebruiken in uw DevTest Labs-omgeving. Als u ExpressRoute gebruikt, u de hoeveelheid VNets / Subnetten minimaliseren, zodat u uw IP-adresruimte niet fragmenteert die wordt toegewezen voor gebruik in de abonnementen.

Overweeg het gebruik van de VNet peering patroon hier ([Hub-Spoke model)](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)ook. Deze aanpak maakt vnet/subnetcommunicatie tussen abonnementen mogelijk. Anders kan elke DevTest Labs-omgeving zijn eigen virtuele netwerk hebben.

Er zijn [beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) op het aantal virtuele netwerken per abonnement. Het standaardbedrag is 50, maar deze limiet kan worden verhoogd tot 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Wanneer moet ik een gedeelde IP vs. openbare IP vs. private IP gebruiken?

Als u een site-to-site VPN of Express Route gebruikt, u overwegen om privé-IP's te gebruiken, zodat uw machines toegankelijk zijn via uw interne netwerk en ontoegankelijk zijn via openbaar internet.

> [!NOTE]
> Labeigenaren kunnen dit subnetbeleid wijzigen om ervoor te zorgen dat niemand per ongeluk openbare IP-adressen voor hun VM's maakt. De eigenaar van het abonnement moet een abonnementsbeleid maken dat voorkomt dat openbare IP's worden gemaakt.

Bij het gebruik van gedeelde openbare IP's delen de virtuele machines in een lab een openbaar IP-adres. Deze aanpak kan handig zijn wanneer u moet voorkomen dat de limieten voor openbare IP-adressen voor een bepaald abonnement worden overschreden.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Hoe zorg ik ervoor dat de ontwikkeling en test virtuele machines niet in staat zijn om het openbare internet te bereiken? Zijn er aanbevolen patronen om netwerkconfiguratie in te stellen?

Ja. Er zijn twee aspecten te overwegen - inkomend en uitgaand verkeer.

- **Binnenkomend verkeer** - Als de virtuele machine geen openbaar IP-adres heeft, kan het niet worden bereikt door het internet. Een gemeenschappelijke aanpak is ervoor te zorgen dat een beleid op abonnementsniveau wordt ingesteld, zodat geen enkele gebruiker een openbaar IP-adres kan maken.
- **Uitgaand verkeer** - Als u wilt voorkomen dat virtuele machines rechtstreeks toegang krijgen tot openbaar internet en verkeer via een bedrijfsfirewall afdwingen, u verkeer on-premises via een snelroute of VPN routeren, door gebruik te maken van gedwongen routering.

> [!NOTE]
> Als u een proxyserver hebt die verkeer blokkeert zonder proxy-instellingen, vergeet dan niet uitzonderingen toe te voegen aan het artefact-opslagaccount van het lab.

U ook netwerkbeveiligingsgroepen gebruiken voor virtuele machines of subnetten. Deze stap voegt een extra beschermingslaag toe om verkeer toe te staan / te blokkeren.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Waarom wordt mijn bestaande virtuele netwerk niet goed opgeslagen?
Een mogelijkheid is dat uw virtuele netwerknaam perioden bevat. Als dat het zo is, probeer dan het verwijderen van de perioden of ze te vervangen door koppeltekens. Probeer vervolgens opnieuw het virtuele netwerk op te slaan.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Waarom krijg ik een foutmelding 'Bovenliggende bron niet gevonden' wanneer ik een VM van PowerShell ind?
Wanneer een resource een bovenliggende bron is, moet de bovenliggende resource bestaan voordat u de onderliggende bron maakt. Als de bovenliggende bron niet bestaat, ziet u een **ParentResourceNotFound-bericht.** Als u geen afhankelijkheid van de bovenliggende resource opgeeft, kan de onderliggende bron worden geïmplementeerd vóór de bovenliggende bron.

VM's zijn onderliggende bronnen onder een lab in een resourcegroep. Wanneer u Resourcemanager-sjablonen gebruikt om VM's te implementeren met PowerShell, moet de naam van de resourcegroep in het PowerShell-script de naam van de brongroep van het lab zijn. Zie [Veelvoorkomende Azure-implementatiefouten oplossen voor](../azure-resource-manager/templates/common-deployment-errors.md)meer informatie.

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Waar kan ik meer foutgegevens vinden als een VM-implementatie mislukt?
VM-implementatiefouten worden vastgelegd in activiteitslogboeken. U labVM-activiteitslogboeken vinden onder **Controlelogboeken** of **Virtuele machinediagnostiek** in het resourcemenu op de VM-pagina van het lab (de pagina wordt weergegeven nadat u de VM hebt geselecteerd in de lijst Mijn virtuele machines).

Soms treedt de implementatiefout op voordat de VM-implementatie begint. Een voorbeeld hiervan is wanneer de abonnementslimiet voor een resource die met de VM is gemaakt, wordt overschreden. In dit geval worden de foutgegevens vastgelegd in de activiteitslogboeken op labniveau. Activiteitslogboeken bevinden zich onder aan de **instellingen voor configuratie en beleid.** Zie [Activiteitslogboeken weergeven om acties op resources te controleren](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over het gebruik van activiteitslogboeken in Azure.

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Waarom krijg ik fout 'locatie is niet beschikbaar voor resourcetype' wanneer ik een lab probeer te maken?
Mogelijk ziet u een foutbericht dat vergelijkbaar is met het volgende bericht wanneer u een lab probeert te maken:

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

U deze fout oplossen door een van de volgende stappen uit te voeren:

#### <a name="option-1"></a>Mogelijkheid 1
Controleer de beschikbaarheid van het resourcetype in Azure-gebieden op de pagina [Producten die beschikbaar zijn op regio.](https://azure.microsoft.com/global-infrastructure/services/) Als het resourcetype niet beschikbaar is in een bepaalde regio, ondersteunt DevTest Labs geen creatie van een lab in die regio. Selecteer een ander gebied bij het maken van uw lab.

#### <a name="option-2"></a>Optie 2
Als het resourcetype beschikbaar is in uw regio, controleert u of het is geregistreerd bij uw abonnement. Het kan worden gedaan op het niveau van de eigenaar van het abonnement, zoals weergegeven in [dit artikel](../azure-resource-manager/management/resource-providers-and-types.md).
