---
title: Voor beeld van Engelse, officiële & UK NHS blauw drukken-besturings elementen
description: De toewijzing van de voor beelden van het Verenigd Konink rijk-en UK-NHS blauw drukken. Elk besturings element wordt toegewezen aan een of meer Azure-beleids regels die helpen bij de evaluatie.
ms.date: 12/04/2019
ms.topic: sample
ms.openlocfilehash: 5bef590013a9ef06b791e58dc6c82e74dffe1a17
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851363"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Controle van de toewijzing van de voor beelden van het Verenigd Konink rijk en UK NHS blauw drukken

In het volgende artikel wordt uitgelegd hoe de voor beelden van het Verenigd Konink rijk en UK NHS blauw worden toegewezen aan de UK-officiële en UK NHS-controles. Zie [UK ambtenaar](https://www.gov.uk/government/publications/government-security-classifications)(Engelstalig) voor meer informatie over de besturings elementen.

De volgende toewijzingen zijn de besturings elementen voor de officiële en **UK-NHS** van **UK** . Gebruik de navigatie aan de rechter kant om rechtstreeks naar een specifieke besturings element koppeling te gaan. Veel van de toegewezen besturings elementen worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md) -initiatief. Als u het complete initiatief wilt bekijken, opent u **beleid** in het Azure Portal en selecteert u de pagina **definities** . Zoek en selecteer vervolgens de **\[preview-\] audit UK officiële en UK NHS Controls en implementeer specifieke VM-extensies ter ondersteuning** van de ingebouwde beleids initiatieven voor controle vereisten.

> [!IMPORTANT]
> Elk besturings element hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md) definities. Met deze beleids regels kunt u de naleving van het besturings element [beoordelen](../../../policy/how-to/get-compliance-data.md) . Er is echter vaak geen 1:1-of volledige overeenkomst tussen een besturings element en een of meer beleids regels. Als zodanig is de **naleving** in azure Policy alleen bedoeld voor het beleid zelf. Dit garandeert niet dat u volledig compatibel bent met alle vereisten van een besturings element. Daarnaast bevat de nalevings standaard besturings elementen die niet worden behandeld door Azure Policy definities op dit moment. Daarom is naleving in Azure Policy slechts een gedeeltelijke weer gave van uw algemene nalevings status. De koppelingen tussen de besturings elementen en Azure Policy definities voor dit voor beeld van deze naleving blauw druk kunnen na verloop van tijd veranderen. Als u de wijzigings geschiedenis wilt weer geven, raadpleegt u de [github commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 gegevens in de doorvoer beveiliging

De blauw druk helpt u ervoor te zorgen dat de overdracht van gegevens met Azure-Services veilig is door [Azure Policy](../../../policy/overview.md) definities toe te wijzen waarmee Inveilige verbindingen met opslag accounts en redis cache worden gecontroleerd.

- Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Controle resultaten weer geven van Windows-webservers die geen beveiligde communicatie protocollen gebruiken
- Vereisten implementeren voor het controleren van Windows-webservers die geen beveiligde communicatie protocollen gebruiken
- De laatste TLS-versie moet worden gebruikt in uw API-app
- De laatste TLS-versie moet worden gebruikt in uw web-app
- De meest recente versie van TLS moet worden gebruikt in uw functie-app

## <a name="23-data-at-rest-protection"></a>2,3 gegevens bij rust beveiliging

Deze blauw druk helpt u bij het afdwingen van uw beleid voor het gebruik van cryptograph-besturings elementen door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die specifieke cryptograph-besturings elementen afdwingen en het gebruik van zwakke cryptografische instellingen te controleren.
Als u wilt weten waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, kunt u corrigerende maat regelen nemen om ervoor te zorgen dat bronnen worden geconfigureerd in overeenstemming met uw informatie beveiligings beleid. Met name de beleids regels die door deze blauw drukken worden toegewezen, vereisen versleuteling voor data Lake Storage-accounts. transparante gegevens versleuteling vereisen voor SQL-data bases; ontbrekende versleuteling controleren voor opslag accounts, SQL-data bases, schijven van virtuele machines en Automation-account variabelen; Controleer onbeveiligde verbindingen met opslag accounts en Redis Cache; de zwakke versleuteling van het wacht woord van de virtuele machine controleren; en niet-versleutelde Service Fabric communicatie controleren.

- Schijfversleuteling moet worden toegepast op virtuele machines
- De variabelen van het Automation-account moeten worden versleuteld
- Voor Service Fabric clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign
- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld
- Transparante gegevens versleuteling van SQL DB implementeren
- Versleuteling vereisen voor Data Lake Store accounts
- Toegestane locaties (is vastgelegd in "UK-Zuid" en "UK-WEST")
- Toegestane locaties voor resource groepen (is vastgelegd in "UK-Zuid" en "UK-WEST")

## <a name="52-vulnerability-management"></a>Beheer van beveiligings problemen 5,2

Deze blauw druk helpt u bij het beheren van beveiligings lekken met informatie door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die ontbrekende eindpunt beveiliging controleren, ontbrekende systeem updates, kwets baarheid van het besturings systeem, SQL-beveiligings problemen en beveiligings problemen met virtuele machines. Deze inzichten bieden real-time informatie over de beveiligings status van uw geïmplementeerde resources en kunnen u helpen bij het bepalen van herstel acties.

- Ontbrekende Endpoint Protection bewaken in Azure Security Center
- Er moeten systeemupdates op uw computers zijn geïnstalleerd
- Systeemupdates op virtuele-machineschaalsets moeten worden geïnstalleerd
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken
- Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op uw SQL-servers
- De evaluatie van beveiligings problemen moet worden ingeschakeld voor uw door SQL beheerde instanties
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw door SQL beheerde instanties
- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers

## <a name="53-protective-monitoring"></a>5,3 beveiligings bewaking

Deze blauw druk helpt u bij het beveiligen van informatie systeem assets door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die beschermende bewaking bieden op onbeperkte toegang, een lijst met toegestane activiteiten en bedreigingen.

- Onbeperkte netwerk toegang tot opslag accounts controleren
- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines
- Virtuele machines controleren zonder nood herstel zijn geconfigureerd
- De DDoS Protection-standaard moet zijn ingeschakeld
- De geavanceerde beveiligings typen voor bedreigingen moeten worden ingesteld op ' all ' in de instellingen voor geavanceerde gegevens beveiliging van SQL Managed instance
- De geavanceerde beveiligings typen voor bedreigingen moeten worden ingesteld op ' all ' in de instellingen voor geavanceerde gegevens beveiliging van SQL Server
- Detectie van bedreigingen op SQL-servers implementeren
- Standaard micro soft IaaSAntimalware-extensie voor Windows Server implementeren

## <a name="9-secure-user-management"></a>9 beveiligd gebruikers beheer 

Azure implementeert op rollen gebaseerd toegangs beheer (RBAC) om u te helpen bij het beheren van de toegang tot resources in Azure. Met behulp van de Azure Portal kunt u controleren wie toegang heeft tot Azure-resources en de bijbehorende machtigingen. Deze blauw druk helpt u om toegangs rechten te beperken en te beheren door [Azure Policy](../../../policy/overview.md) definities toe te wijzen voor het controleren van externe accounts met de machtigingen eigenaar en/of lezen/schrijven, en voor accounts met de machtigingen eigenaar, lezen en/of schrijven waarvoor multi-factor Authentication niet is ingeschakeld.

- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement
- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement

## <a name="10-identity-and-authentication"></a>10 identiteit en verificatie

Deze blauw druk helpt u om toegangs rechten te beperken en te beheren door [Azure Policy](../../../policy/overview.md) definities toe te wijzen voor het controleren van externe accounts met de machtigingen eigenaar en/of lezen/schrijven, en voor accounts met de machtigingen eigenaar, lezen en/of schrijven waarvoor multi-factor Authentication niet is ingeschakeld.

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

- \[preview-\]: controle resultaten weer geven van Linux-Vm's waarvoor de machtigingen voor het passwd-bestand niet zijn ingesteld op 0644

Deze blauw druk helpt u sterke wacht woorden af te dwingen door Azure Policy definities toe te wijzen waarmee Windows-Vm's worden gecontroleerd die geen minimale sterkte en andere wachtwoord vereisten afdwingen. Het bewustzijn van Vm's in strijd met het beleid voor wachtwoord sterkte helpt u bij het uitvoeren van corrigerende maat regelen om ervoor te zorgen dat wacht woorden voor alle VM-gebruikers accounts voldoen aan het beleid.

- \[preview\]: vereisten implementeren voor het controleren van Windows-Vm's waarvoor de instelling voor wachtwoord complexiteit niet is ingeschakeld
- \[preview\]: vereisten implementeren voor het controleren van Windows-Vm's die geen maximale wachtwoord duur van 70 dagen hebben
- \[preview\]: vereisten implementeren voor het controleren van Windows-Vm's die geen minimale wachtwoord duur van 1 dag hebben
- \[preview\]: vereisten implementeren om Windows-Vm's te controleren die de minimale wachtwoord lengte niet beperken tot 14 tekens
- \[preview\]: vereisten implementeren voor het controleren van Windows-Vm's die het opnieuw gebruiken van de voor gaande 24 wacht woorden toestaan
- \[preview\]: controle resultaten weer geven van Windows-Vm's waarvoor de instelling voor wachtwoord complexiteit niet is ingeschakeld
- \[preview\]: controle resultaten weer geven van Windows-Vm's die geen maximale wachtwoord duur van 70 dagen hebben
- \[preview\]: controle resultaten weer geven van Windows-Vm's waarvoor geen minimale wachtwoord leeftijd van 1 dag is
- \[preview\]: controle resultaten van Windows-Vm's weer geven die de minimale wachtwoord lengte niet beperken tot 14 tekens
- \[preview\]: controle resultaten van virtuele Windows-machines weer geven die het opnieuw gebruiken van de voor gaande 24 wacht woorden toestaan

Met deze blauw druk kunt u ook de toegang tot Azure-resources beheren door Azure Policy definities toe te wijzen. Met deze beleids regels wordt het gebruik van resource typen en configuraties gecontroleerd, waardoor de toegang tot bronnen kan worden uitgebreid. Informatie over resources die in strijd zijn met deze beleids regels, kan u helpen corrigerende maat regelen te nemen om ervoor te zorgen dat toegang tot Azure-resources wordt beperkt tot gemachtigde gebruikers.

- \[preview\]: vereisten implementeren voor het controleren van virtuele Linux-machines met accounts zonder wacht woorden
- \[preview\]: vereisten implementeren voor het controleren van virtuele Linux-machines die externe verbindingen toestaan van accounts zonder wacht woorden
- \[preview-\]: Linux-Vm's met accounts zonder wacht woorden controleren
- \[preview-\]: Linux-Vm's controleren die externe verbindingen toestaan van accounts zonder wacht woorden
- Opslag accounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Virtuele machines controleren die geen beheerde schijven gebruiken

## <a name="11-external-interface-protection"></a>11 externe interface beveiliging

Behalve met het gebruik van meer dan 25 beleids regels voor het juiste veilige gebruikers beheer, kunt u met deze blauw druk Service interfaces beveiligen tegen onbevoegde toegang door een [Azure Policy](../../../policy/overview.md) definitie toe te wijzen waarmee onbeperkte opslag accounts worden bewaakt. Opslag accounts met onbeperkte toegang kunnen onbedoelde toegang tot gegevens in het informatie systeem toestaan. Deze blauw druk wijst ook een beleid toe dat adaptieve toepassings besturings elementen op virtuele machines mogelijk maakt.

- Onbeperkte netwerk toegang tot opslag accounts controleren
- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines
- De Nsg's-regels voor webtoepassingen op IaaS moeten een harde verbinding hebben
- Toegang via Internet gericht eind punt moet worden beperkt
- De regels voor de netwerk beveiligings groep voor virtuele machines die zijn gericht op internet, moeten worden gehard
- De Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machineschaalsets
- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines
- Onbeperkte netwerk toegang tot opslag accounts controleren
- Fout opsporing op afstand moet worden uitgeschakeld voor functie-app
- Foutopsporing op afstand moet worden uitgeschakeld voor Web-App
- Fout opsporing op afstand moet worden uitgeschakeld voor de API-app
- Web-App moet alleen toegankelijk zijn via HTTPS
- Functie-App moet alleen toegankelijk zijn via HTTPS
- De API-app mag alleen toegankelijk zijn via HTTPS

## <a name="12-secure-service-administration"></a>12 beheer van beveiligde services

Azure implementeert op rollen gebaseerd toegangs beheer (RBAC) om u te helpen bij het beheren van de toegang tot resources in Azure. Met behulp van de Azure Portal kunt u controleren wie toegang heeft tot Azure-resources en de bijbehorende machtigingen. Deze blauw druk helpt u om privileged Access Rights te beperken en te beheren door vijf [Azure Policy](../../../policy/overview.md) definities toe te wijzen om externe accounts te controleren met eigenaar en/of schrijf machtigingen en accounts met de eigenaar en/of schrijf machtigingen waarvoor multi-factor Authentication niet is ingeschakeld.

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

- \[preview\]: de machtigingen van de Linux VM/etc/passwd-bestand controleren zijn ingesteld op 0644

## <a name="13-audit-information-for-users"></a>13 controle-informatie voor gebruikers

Deze blauw druk helpt u om ervoor te zorgen dat systeem gebeurtenissen worden vastgelegd door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die logboek instellingen op Azure-resources controleren. Een toegewezen beleid controleert ook of de virtuele machines geen logboeken verzenden naar een opgegeven log Analytics-werk ruimte.

- Controle moet worden ingeschakeld voor geavanceerde instellingen voor gegevens beveiliging op SQL Server
- Diagnostische instelling voor controleren
- \[Preview-versie\]: Log Analytics-Agent voor Linux-VM's implementeren
- \[Preview-versie\]: Log Analytics-Agent voor Windows-VM's implementeren
- Network Watcher implementeren bij het maken van virtuele netwerken

## <a name="next-steps"></a>Volgende stappen

Nu u de controle toewijzing van het Verenigd Konink rijk-en UK-NHS blauw drukken hebt bekeken, gaat u naar de volgende artikelen voor meer informatie over het overzicht en hoe u dit voor beeld implementeert:

> [!div class="nextstepaction"]
> [UK-officiële en UK NHS-blauw drukken-overzicht](./index.md)
> [UK-officiële en UK NHS blauw drukken-stappen implementeren](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
