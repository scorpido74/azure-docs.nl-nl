---
title: Voor beeld-UK-officiële en UK NHS-blauw drukken-controle toewijzing
description: De toewijzing van de voor beelden van het Verenigd Konink rijk-en UK-NHS blauw drukken. Elk besturings element wordt toegewezen aan een of meer Azure-beleids regels die helpen bij de evaluatie.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 9001e752c9b0f79d7c688e0b13fd7133004ff4ad
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248788"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Controle van de toewijzing van de voor beelden van het Verenigd Konink rijk en UK NHS blauw drukken

In het volgende artikel wordt uitgelegd hoe de voor beelden van het Verenigd Konink rijk en UK NHS blauw worden toegewezen aan de UK-officiële en UK NHS-controles. Zie [UK ambtenaar](https://www.gov.uk/government/publications/government-security-classifications)(Engelstalig) voor meer informatie over de besturings elementen.

De volgende toewijzingen zijn de besturings elementen voor de officiële en **UK-NHS** van **UK** . Gebruik de navigatie aan de rechter kant om rechtstreeks naar een specifieke besturings element koppeling te gaan. Veel van de toegewezen besturings elementen worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md) -initiatief. Als u het complete initiatief wilt bekijken, opent u **beleid** in het Azure Portal en selecteert u de pagina **definities** . Zoek en selecteer vervolgens de **\[Preview @ no__t-2 audit UK Official en UK NHS Controls en implementeer specifieke VM-extensies ter ondersteuning** van de ingebouwde beleids initiatieven voor controle vereisten.

> [!IMPORTANT]
> Elk besturings element hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md) definities. Met deze beleids regels kunt u de naleving van het besturings element [beoordelen](../../../policy/how-to/get-compliance-data.md) . Er is echter vaak geen 1:1-of volledige overeenkomst tussen een besturings element en een of meer beleids regels. Als zodanig is de **naleving** in azure Policy alleen bedoeld voor het beleid zelf. Dit garandeert niet dat u volledig compatibel bent met alle vereisten van een besturings element. Daarnaast bevat de nalevings standaard besturings elementen die niet worden behandeld door Azure Policy definities op dit moment. Daarom is naleving in Azure Policy slechts een gedeeltelijke weer gave van uw algemene nalevings status. De koppelingen tussen de besturings elementen en Azure Policy definities voor dit voor beeld van deze naleving blauw druk kunnen na verloop van tijd veranderen. Als u de wijzigings geschiedenis wilt weer geven, raadpleegt u de [github commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 gegevens in de doorvoer beveiliging

De blauw druk helpt u ervoor te zorgen dat de overdracht van gegevens met Azure-Services veilig is door [Azure Policy](../../../policy/overview.md) definities toe te wijzen waarmee Inveilige verbindingen met opslag accounts en redis cache worden gecontroleerd.

- Alleen beveiligde verbindingen met uw Redis Cache moeten worden ingeschakeld
- Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld

## <a name="23-data-at-rest-protection"></a>2,3 gegevens bij rust beveiliging

Deze blauw druk helpt u bij het afdwingen van uw beleid voor het gebruik van cryptograph-besturings elementen door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die specifieke cryptograph-besturings elementen afdwingen en het gebruik van zwakke cryptografische instellingen te controleren.
Als u wilt weten waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, kunt u corrigerende maat regelen nemen om ervoor te zorgen dat bronnen worden geconfigureerd in overeenstemming met uw informatie beveiligings beleid. Met name de beleids regels die door deze blauw drukken worden toegewezen, vereisen versleuteling voor data Lake Storage-accounts. transparante gegevens versleuteling vereisen voor SQL-data bases; ontbrekende versleuteling controleren voor opslag accounts, SQL-data bases, schijven van virtuele machines en Automation-account variabelen; Controleer onbeveiligde verbindingen met opslag accounts en Redis Cache; de zwakke versleuteling van het wacht woord van de virtuele machine controleren; en niet-versleutelde Service Fabric communicatie controleren.

- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld
- Schijf versleuteling moet worden toegepast op virtuele machines
- De variabelen van het Automation-account moeten worden versleuteld
- Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld
- Voor Service Fabric clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign
- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld
- Transparante gegevens versleuteling van SQL DB implementeren
- Versleuteling vereisen voor Data Lake Store accounts
- Toegestane locaties (is vastgelegd in "UK-Zuid" en "UK-WEST")
- Toegestane locaties voor resource groepen (is vastgelegd in "UK-Zuid" en "UK-WEST")

## <a name="52-vulnerability-management"></a>Beheer van beveiligings problemen 5,2

Deze blauw druk helpt u bij het beheren van beveiligings problemen met informatie systemen door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die ontbrekende eindpunt beveiliging controleren, ontbrekende systeem updates, kwets bare besturings systemen, SQL-beveiligings problemen en virtuele computer lekken. Deze inzichten bieden real-time informatie over de beveiligings status van uw geïmplementeerde resources en kunnen u helpen bij het bepalen van herstel acties.

- Ontbrekende Endpoint Protection in Azure Security Center controleren
- Systeem updates moeten worden geïnstalleerd op uw computers
- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld
- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken

## <a name="53-protective-monitoring"></a>5,3 beveiligings bewaking

Deze blauw druk helpt u bij het beveiligen van informatie systeem assets door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die beschermende bewaking bieden op onbeperkte toegang, white list-activiteit en bedreigingen.

- Onbeperkte netwerk toegang tot opslag accounts controleren
- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines
- Detectie van bedreigingen op SQL-servers implementeren
- Standaard micro soft IaaS anti-malware-extensie voor Windows Server implementeren

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 veilig gebruikers beheer/10 identiteit en verificatie

Azure implementeert op rollen gebaseerd toegangs beheer (RBAC) om u te helpen bij het beheren van de toegang tot resources in Azure. Met behulp van de Azure Portal kunt u controleren wie toegang heeft tot Azure-resources en de bijbehorende machtigingen. Deze blauw druk helpt u om toegangs rechten te beperken en te beheren door [Azure Policy](../../../policy/overview.md) definities toe te wijzen voor het controleren van externe accounts met de machtigingen eigenaar en/of lezen/schrijven met eigenaar, lees-en/of schrijf machtigingen die geen multi-factor verificatie ingeschakeld.

- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement
- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement

Deze blauw druk wijst Azure Policy definities toe om het gebruik van Azure Active Directory-verificatie voor SQL-servers en Service Fabric te controleren. Met behulp van Azure Active Directory-verificatie kunt u eenvoudig beheer van machtigingen en gecentraliseerd identiteits beheer van database gebruikers en andere micro soft-Services.

- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers
- Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor client verificatie

Deze blauw druk wijst ook Azure Policy definities toe aan controle-accounts waarvoor een prioriteit moet worden gegeven, waaronder afgeschreven accounts en externe accounts. Zo nodig kunnen accounts worden geblokkeerd om zich aan te melden (of verwijderd), waardoor de toegangs rechten voor Azure-bronnen onmiddellijk worden verwijderd. Deze blauw druk wijst twee Azure Policy definities toe aan het controleren van afgeschreven accounts die moeten worden beschouwd voor verwijdering.

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement

Deze blauw druk wijst ook een Azure Policy definitie toe waarmee de machtigingen van het Linux-VM-wacht woord worden gecontroleerd op waarschuwing als ze onjuist zijn ingesteld. Met dit ontwerp kunt u corrigerende maat regelen treffen om te voor komen dat verificators worden aangetast.

- \[Preview @ no__t-1: de machtigingen van de Linux VM/etc/passwd-bestand controleren zijn ingesteld op 0644

Deze blauw druk helpt u sterke wacht woorden af te dwingen door Azure Policy definities toe te wijzen waarmee Windows-Vm's worden gecontroleerd die geen minimale sterkte en andere wachtwoord vereisten afdwingen. Het bewustzijn van Vm's in strijd met het beleid voor wachtwoord sterkte helpt u bij het uitvoeren van corrigerende maat regelen om ervoor te zorgen dat wacht woorden voor alle VM-gebruikers accounts voldoen aan het beleid.

- \[Preview @ no__t-1: vereisten implementeren voor het controleren van Windows-Vm's waarvoor de instelling voor wachtwoord complexiteit niet is ingeschakeld
- \[Preview @ no__t-1: vereisten implementeren voor het controleren van Windows-Vm's die geen maximale wachtwoord duur van 70 dagen hebben
- \[Preview @ no__t-1: vereisten implementeren voor het controleren van virtuele Windows-machines zonder minimale wachtwoord duur van 1 dag
- \[Preview @ no__t-1: vereisten implementeren om Windows-Vm's te controleren die de minimale wachtwoord lengte niet beperken tot 14 tekens
- \[Preview @ no__t-1: vereisten implementeren voor het controleren van Windows-Vm's die het opnieuw gebruiken van de voor gaande 24 wacht woorden toestaan
- \[Preview @ no__t-1: Windows-Vm's controleren waarvoor de instelling voor wachtwoord complexiteit niet is ingeschakeld
- \[Preview @ no__t-1: Windows-Vm's controleren die geen maximale wachtwoord duur van 70 dagen hebben
- \[Preview @ no__t-1: Windows-Vm's met een minimale wachtwoord duur van 1 dag controleren
- \[Preview @ no__t-1: Windows-Vm's controleren die de minimale wachtwoord lengte niet beperken tot 14 tekens
- \[Preview @ no__t-1: Windows-Vm's controleren die het opnieuw gebruiken van de voor gaande 24 wacht woorden toestaan

Met deze blauw druk kunt u ook de toegang tot Azure-resources beheren door Azure Policy definities toe te wijzen. Met deze beleids regels wordt het gebruik van resource typen en configuraties gecontroleerd, waardoor de toegang tot bronnen kan worden uitgebreid. Informatie over resources die in strijd zijn met deze beleids regels, kan u helpen corrigerende maat regelen te nemen om ervoor te zorgen dat toegang tot Azure-resources wordt beperkt tot gemachtigde gebruikers.

- \[Preview @ no__t-1: vereisten implementeren voor het controleren van virtuele Linux-machines met accounts zonder wacht woorden
- \[Preview @ no__t-1: vereisten implementeren voor het controleren van virtuele Linux-machines die externe verbindingen toestaan van accounts zonder wacht woorden
- \[Preview @ no__t-1: virtuele Linux-machines met accounts zonder wacht woorden controleren
- \[Preview @ no__t-1: virtuele Linux-machines controleren die externe verbindingen toestaan van accounts zonder wacht woorden
- Opslag accounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Virtuele machines controleren die geen beheerde schijven gebruiken

## <a name="11-external-interface-protection"></a>11 externe interface beveiliging

Behalve met het gebruik van meer dan 25 beleids regels voor het juiste veilige gebruikers beheer, kunt u met deze blauw druk Service interfaces beveiligen tegen onbevoegde toegang door een [Azure Policy](../../../policy/overview.md) definitie toe te wijzen waarmee onbeperkte opslag accounts worden bewaakt. Opslag accounts met onbeperkte toegang kunnen onbedoelde toegang tot gegevens in het informatie systeem toestaan. Deze blauw druk wijst ook een beleid toe dat adaptieve toepassings besturings elementen op virtuele machines mogelijk maakt.

- Onbeperkte netwerk toegang tot opslag accounts controleren
- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines

## <a name="12-secure-service-administration"></a>12 beheer van beveiligde services

Azure implementeert op rollen gebaseerd toegangs beheer (RBAC) om u te helpen bij het beheren van de toegang tot resources in Azure. Met behulp van de Azure Portal kunt u controleren wie toegang heeft tot Azure-resources en de bijbehorende machtigingen. Deze blauw druk helpt u om privileged Access Rights te beperken en te beheren door vijf [Azure Policy](../../../policy/overview.md) definities toe te wijzen om externe accounts te controleren met eigenaar en/of schrijf machtigingen en accounts met eigenaar en/of schrijf machtigingen die geen multi-factor Authentication is ingeschakeld.

Systemen die worden gebruikt voor het beheer van een Cloud service hebben zeer bevoorrechte toegang tot de betreffende service. Hun inbreuk zou aanzienlijke gevolgen hebben, zoals de mogelijkheid om beveiligings controles over te slaan en grote hoeveel heden gegevens te stelen of te manipuleren. De methoden die door de beheerders van de service provider worden gebruikt om de operationele service te beheren, moeten worden ontworpen om het risico van misbruik dat de beveiliging van de service ondervindt, te beperken. Als dit principe niet is geïmplementeerd, kan een aanvaller de middelen gebruiken om beveiligings controles over te slaan en grote hoeveel heden gegevens te stelen of te manipuleren.

- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement
- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement

Deze blauw druk wijst Azure Policy definities toe om het gebruik van Azure Active Directory-verificatie voor SQL-servers en Service Fabric te controleren. Met behulp van Azure Active Directory-verificatie kunt u eenvoudig beheer van machtigingen en gecentraliseerd identiteits beheer van database gebruikers en andere micro soft-Services.

- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers
- Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor client verificatie

Deze blauw druk wijst ook Azure Policy definities toe aan controle-accounts waarvoor een prioriteit moet worden gegeven, waaronder afgeschreven accounts en externe accounts met verhoogde machtigingen. Zo nodig kunnen accounts worden geblokkeerd om zich aan te melden (of verwijderd), waardoor de toegangs rechten voor Azure-bronnen onmiddellijk worden verwijderd. Deze blauw druk wijst twee Azure Policy definities toe aan het controleren van afgeschreven accounts die moeten worden beschouwd voor verwijdering.

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement

Deze blauw druk wijst ook een Azure Policy definitie toe waarmee de machtigingen van het Linux-VM-wacht woord worden gecontroleerd op waarschuwing als ze onjuist zijn ingesteld. Met dit ontwerp kunt u corrigerende maat regelen treffen om te voor komen dat verificators worden aangetast.

- \[Preview @ no__t-1: de machtigingen van de Linux VM/etc/passwd-bestand controleren zijn ingesteld op 0644

## <a name="13-audit-information-for-users"></a>13 controle-informatie voor gebruikers

Deze blauw druk helpt u om ervoor te zorgen dat systeem gebeurtenissen worden vastgelegd door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die logboek instellingen op Azure-resources controleren. Een toegewezen beleid controleert ook of de virtuele machines geen logboeken verzenden naar een opgegeven log Analytics-werk ruimte.

- Controle moet worden ingeschakeld voor geavanceerde instellingen voor gegevens beveiliging op SQL Server
- Diagnostische instelling voor controleren
- Controle-instellingen op SQL server-niveau controleren
- \[Preview @ no__t-1: Log Analytics agent voor Linux-Vm's implementeren
- \[Preview @ no__t-1: Log Analytics-agent implementeren voor Windows-Vm's
- Network Watcher implementeren bij het maken van virtuele netwerken

## <a name="next-steps"></a>Volgende stappen

Nu u de controle toewijzing van het Verenigd Konink rijk-en UK-NHS blauw drukken hebt bekeken, gaat u naar de volgende artikelen voor meer informatie over het overzicht en hoe u dit voor beeld implementeert:

> [!div class="nextstepaction"]
> [UK-officiële en UK NHS blauw drukken-overzicht](./index.md)
> [UK-officiële en UK NHS blauw drukken-stappen implementeren](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).