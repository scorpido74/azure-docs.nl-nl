---
title: Voor beeld-ISO 27001 blauw druk-besturings element toewijzing
description: Bepaal de toewijzing van het ISO 27001-blauw druk-voor beeld. Elk besturings element wordt toegewezen aan een of meer Azure-beleids regels die helpen bij de evaluatie.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/22/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: f381cb2beff0dd828e7ab1e5661857d826c49ecf
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248872"
---
# <a name="control-mapping-of-the-iso-27001-blueprint-sample"></a>De toewijzing van het ISO 27001 blauw druk-voor beeld bepalen

Het volgende artikel bevat informatie over de manier waarop het Azure blauw drukken ISO 27001 blauw druk-voor beeld wordt toegewezen aan de ISO 27001-besturings elementen. Zie [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html)voor meer informatie over de besturings elementen.

De volgende toewijzingen zijn de **ISO 27001:2013** -besturings elementen. Gebruik de navigatie aan de rechter kant om rechtstreeks naar een specifieke besturings element koppeling te gaan. Veel van de toegewezen besturings elementen worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md) -initiatief. Als u het complete initiatief wilt bekijken, opent u **beleid** in het Azure Portal en selecteert u de pagina **definities** . Zoek en selecteer vervolgens de **\[Preview @ no__t-2 audit ISO 27001:2013-besturings elementen en implementeer specifieke VM-extensies ter ondersteuning** van de ingebouwde beleids initiatieven voor controle vereisten.

> [!IMPORTANT]
> Elk besturings element hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md) definities. Met deze beleids regels kunt u de naleving van het besturings element [beoordelen](../../../policy/how-to/get-compliance-data.md) . Er is echter vaak geen 1:1-of volledige overeenkomst tussen een besturings element en een of meer beleids regels. Als zodanig is de **naleving** in azure Policy alleen bedoeld voor het beleid zelf. Dit garandeert niet dat u volledig compatibel bent met alle vereisten van een besturings element. Daarnaast bevat de nalevings standaard besturings elementen die niet worden behandeld door Azure Policy definities op dit moment. Daarom is naleving in Azure Policy slechts een gedeeltelijke weer gave van uw algemene nalevings status. De koppelingen tussen de besturings elementen en Azure Policy definities voor dit voor beeld van deze naleving blauw druk kunnen na verloop van tijd veranderen. Als u de wijzigings geschiedenis wilt weer geven, raadpleegt u de [github commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A. 6.1.2 schei ding van taken

Als er slechts één eigenaar van een Azure-abonnement is, is er geen administratieve redundantie toegestaan. Als er te veel eigen aars van Azure-abonnementen zijn, kan het mogelijk zijn om een schending te doen van een inbreuk op een eigenaars account. Deze blauw druk helpt u bij het onderhouden van een geschikt aantal eigen aren van Azure-abonnees door twee [Azure Policy](../../../policy/overview.md) definities toe te wijzen die het aantal eigen aren voor Azure-abonnementen controleren. De machtigingen van eigenaar van het abonnement beheren kan u helpen bij het implementeren van de juiste schei ding van taken.

- \[Preview @ no__t-1: het minimum aantal eigen aren voor een abonnement controleren
- \[Preview @ no__t-1: het maximum aantal eigen aars voor een abonnement controleren

## <a name="a821-classification-of-information"></a>A. 8.2.1 classificatie van informatie

De [evaluatie service van SQL-beveiligings problemen](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) kan u helpen bij het detecteren van gevoelige gegevens die zijn opgeslagen in uw data bases en bevat aanbevelingen voor het classificeren van die gegevens. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe om te controleren of beveiligings problemen die zijn geïdentificeerd tijdens de controle van SQL-beveiligings beoordeling, worden hersteld.

- \[Preview @ no__t-1: resultaten van de evaluatie van SQL-beveiligings problemen controleren in Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>A. 9.1.2 toegang tot netwerken en netwerk services

Azure implementeert op [rollen gebaseerd toegangs beheer](../../../../role-based-access-control/overview.md) (RBAC) voor het beheren van de toegang tot Azure-resources. Deze blauw druk helpt u de toegang tot Azure-resources te beheren door zeven [Azure Policy](../../../policy/overview.md) definities toe te wijzen. Met deze beleids regels wordt het gebruik van resource typen en configuraties gecontroleerd, waardoor de toegang tot bronnen kan worden uitgebreid.
Informatie over resources die in strijd zijn met deze beleids regels, kan u helpen corrigerende maat regelen te nemen om ervoor te zorgen dat toegang tot Azure-resources wordt beperkt tot gemachtigde gebruikers.

- \[Preview @ no__t-1: VM-extensie implementeren om Linux-VM-accounts zonder wacht woorden te controleren
- \[Preview @ no__t-1: VM-extensie implementeren om virtuele Linux-machines te controleren op externe verbindingen van accounts zonder wacht woorden
- \[Preview @ no__t-1: Linux VM-accounts controleren zonder wacht woorden
- \[Preview @ no__t-1: virtuele Linux-machine controleren op externe verbindingen van accounts zonder wacht woorden
- Het gebruik van klassieke opslag accounts controleren
- Het gebruik van klassieke virtuele machines controleren
- Virtuele machines controleren die geen beheerde schijven gebruiken

## <a name="a923-management-of-privileged-access-rights"></a>A. 9.2.3 beheer van privileged Access Rights

Deze blauw druk helpt u om privileged Access Rights te beperken en te beheren door vier [Azure Policy](../../../policy/overview.md) definities toe te wijzen om externe accounts te controleren met eigenaar en/of schrijf machtigingen en accounts met eigenaar en/of schrijf machtigingen die geen multi-factor Authentication is ingeschakeld. Azure implementeert op rollen gebaseerd toegangs beheer (RBAC) voor het beheren van de toegang tot Azure-resources. Met deze blauw druk worden ook drie Azure Policy definities toegewezen om het gebruik van Azure Active Directory-verificatie voor SQL-servers en Service Fabric te controleren. Met behulp van Azure Active Directory-verificatie kunt u eenvoudig beheer van machtigingen en gecentraliseerd identiteits beheer van database gebruikers en andere micro soft-Services. Deze blauw druk wijst ook een Azure Policy definitie toe om het gebruik van aangepaste RBAC-regels te controleren. Als u wilt weten waar aangepaste RBAC-regels worden geïmplementeerd, kunt u controleren of de juiste implementatie nodig is, omdat aangepaste RBAC-regels fout gevoelig zijn.

- \[Preview @ no__t-1: audit accounts met eigenaars machtigingen waarvoor geen MFA is ingeschakeld voor een abonnement
- \[Preview @ no__t-1: audit accounts met schrijf machtigingen waarvoor geen MFA is ingeschakeld voor een abonnement
- \[Preview @ no__t-1: externe accounts met eigenaars machtigingen voor een abonnement controleren
- \[Preview @ no__t-1: externe accounts met schrijf machtigingen voor een abonnement controleren
- Inrichting van een Azure Active Directory beheerder voor SQL Server controleren
- Gebruik van Azure Active Directory voor client verificatie in Service Fabric controleren
- Gebruik van aangepaste RBAC-regels controleren

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A. 9.2.4 beheer van geheime verificatie gegevens van gebruikers

Met deze blauw druk worden drie [Azure Policy](../../../policy/overview.md) definities toegewezen voor het controleren van accounts waarvoor multi-factor Authentication niet is ingeschakeld. Multi-factor Authentication helpt accounts veilig te houden, zelfs als er wordt geknoeid met één van de verificatie gegevens. Door accounts te controleren waarop multi-factor Authentication is ingeschakeld, kunt u accounts identificeren die waarschijnlijker worden aangetast. Met deze blauw druk worden ook twee Azure Policy definities toegewezen waarmee de machtigingen voor het Linux VM-wachtwoord bestand worden gecontroleerd op waarschuwing als ze onjuist zijn ingesteld. Met deze instelling kunt u corrigerende maat regelen treffen om te voor komen dat verificators worden aangetast.

- \[Preview @ no__t-1: audit accounts met eigenaars machtigingen waarvoor geen MFA is ingeschakeld voor een abonnement
- \[Preview @ no__t-1: audit accounts met lees machtigingen waarvoor geen MFA is ingeschakeld voor een abonnement
- \[Preview @ no__t-1: audit accounts met schrijf machtigingen waarvoor geen MFA is ingeschakeld voor een abonnement
- \[Preview @ no__t-1: VM-extensie implementeren om machtigingen voor het passwd-VM-bestand van de Linux-machine te controleren
- \[Preview @ no__t-1: de machtigingen van de Linux VM/etc/passwd-bestand controleren zijn ingesteld op 0644

## <a name="a925-review-of-user-access-rights"></a>A. 9.2.5 beoordeling van gebruikers toegangs rechten

Azure implementeert op [rollen gebaseerd toegangs beheer](../../../../role-based-access-control/overview.md) (RBAC) om u te helpen bij het beheren van de toegang tot resources in Azure. Met behulp van de Azure Portal kunt u controleren wie toegang heeft tot Azure-resources en de bijbehorende machtigingen. Met deze blauw druk worden vier [Azure Policy](../../../policy/overview.md) definities toegewezen voor het controleren van accounts waarvoor een prioriteit moet worden gegeven, waaronder afgeschreven accounts en externe accounts met verhoogde machtigingen.

- \[Preview @ no__t-1: afgeschafte accounts op een abonnement controleren
- \[Preview @ no__t-1: controleren van afgeschafte accounts met eigenaars machtigingen voor een abonnement
- \[Preview @ no__t-1: externe accounts met eigenaars machtigingen voor een abonnement controleren
- \[Preview @ no__t-1: externe accounts met schrijf machtigingen voor een abonnement controleren

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A. 9.2.6 verwijderen of aanpassen van toegangs rechten

Azure implementeert op [rollen gebaseerd toegangs beheer](../../../../role-based-access-control/overview.md) (RBAC) om u te helpen bij het beheren van de toegang tot resources in Azure. Met behulp van [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) en RBAC kunt u gebruikers rollen bijwerken om de wijzigingen in de organisatie te spie gelen. Zo nodig kunnen accounts worden geblokkeerd om zich aan te melden (of verwijderd), waardoor de toegangs rechten voor Azure-bronnen onmiddellijk worden verwijderd. Deze blauw druk wijst twee [Azure Policy](../../../policy/overview.md) definities toe aan het controleren van afgeschreven accounts die moeten worden beschouwd voor verwijdering.

- \[Preview @ no__t-1: afgeschafte accounts op een abonnement controleren
- \[Preview @ no__t-1: controleren van afgeschafte accounts met eigenaars machtigingen voor een abonnement

## <a name="a942-secure-log-on-procedures"></a>A. 9.4.2 beveiligde aanmeld procedures

Met deze blauw druk worden drie Azure Policy definities toegewezen voor het controleren van accounts waarvoor multi-factor Authentication niet is ingeschakeld. Azure Multi-Factor Authentication biedt extra beveiliging door een tweede vorm van verificatie te vereisen en sterke verificatie te bieden. Door accounts te controleren waarop multi-factor Authentication is ingeschakeld, kunt u accounts identificeren die waarschijnlijker worden aangetast.

- \[Preview @ no__t-1: audit accounts met eigenaars machtigingen waarvoor geen MFA is ingeschakeld voor een abonnement
- \[Preview @ no__t-1: audit accounts met lees machtigingen waarvoor geen MFA is ingeschakeld voor een abonnement
- \[Preview @ no__t-1: audit accounts met schrijf machtigingen waarvoor geen MFA is ingeschakeld voor een abonnement

## <a name="a943-password-management-system"></a>Een. 9.4.3-wachtwoord beheer systeem

Deze blauw druk helpt u sterke wacht woorden af te dwingen door tien [Azure Policy](../../../policy/overview.md) definities toe te wijzen die Windows-vm's controleren die geen minimale sterkte en andere wachtwoord vereisten afdwingen. Het bewustzijn van Vm's in strijd met het beleid voor wachtwoord sterkte helpt u bij het uitvoeren van corrigerende maat regelen om ervoor te zorgen dat wacht woorden voor alle VM-gebruikers accounts voldoen aan het beleid.

- \[Preview @ no__t-1: VM-extensie implementeren voor controle van Windows-VM dwingt vereisten voor wachtwoord complexiteit af
- \[Preview @ no__t-1: VM-extensie implementeren voor het controleren van de maximale wachtwoord duur van Windows-VM'S 70 dagen
- \[Preview @ no__t-1: VM-extensie implementeren voor het controleren van de minimale wachtwoord duur van Windows-VM'S 1 dag
- \[Preview @ no__t-1: VM-extensie implementeren om wacht woorden van Windows-VM'S te controleren moet ten minste 14 tekens bevatten
- \[Preview @ no__t-1: implementatie van de VM-extensie voor de controle van Windows-VM'S mag geen gebruik maken van de voor gaande 24 wacht woorden
- \[Preview @ no__t-1: controleren Windows-VM dwingt vereisten voor wachtwoord complexiteit af
- \[Preview @ no__t-1: de maximale wachtwoord duur van Windows-VM'S 70 dagen controleren
- \[Preview @ no__t-1: controleren van minimale wachtwoord duur van Windows-VM 1 dag
- \[Preview @ no__t-1: controleren of Windows VM-wacht woorden ten minste 14 tekens bevatten
- \[Preview @ no__t-1: audit Windows VM mag geen voor gaande 24 wacht woorden toestaan

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Een. 10.1.1-beleid voor het gebruik van cryptografische besturings elementen

Deze blauw druk helpt u bij het afdwingen van uw beleid voor het gebruik van cryptograph-besturings elementen door 13 [Azure Policy](../../../policy/overview.md) definities toe te wijzen die specifieke cryptograph-besturings elementen afdwingen en het gebruik van zwakke cryptografische instellingen controleren.
Als u wilt weten waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, kunt u corrigerende maat regelen nemen om ervoor te zorgen dat bronnen worden geconfigureerd in overeenstemming met uw informatie beveiligings beleid. Met name de beleids regels die door deze blauw drukken worden toegewezen, vereisen versleuteling voor Blob Storage-accounts en data Lake Storage-accounts. transparante gegevens versleuteling vereisen voor SQL-data bases; ontbrekende versleuteling controleren voor opslag accounts, SQL-data bases, schijven van virtuele machines en Automation-account variabelen; Controleer onbeveiligde verbindingen met opslag accounts, functie-apps, Web-app, API Apps en Redis Cache. de zwakke versleuteling van het wacht woord van de virtuele machine controleren; en niet-versleutelde Service Fabric communicatie controleren.

- \[Preview @ no__t-1: alleen controleren op HTTPS-toegang voor een functie-app
- \[Preview @ no__t-1: alleen HTTPS-toegang voor een webtoepassing controleren
- \[Preview @ no__t-1: alleen controleren op HTTPS-toegang voor een API-app
- \[Preview @ no__t-1: controle ontbrekende BLOB-versleuteling voor opslag accounts
- \[Preview @ no__t-1: VM-extensie implementeren om te controleren of Windows VM wacht woorden niet kan opslaan met behulp van omkeer bare versleuteling
- \[Preview @ no__t-1: audit Windows VM mag geen wacht woorden opslaan met omkeer bare versleuteling
- \[Preview @ no__t-1: niet-versleutelde VM-schijven bewaken in Azure Security Center
- Activering van de versleuteling van de Automation-account variabelen controleren
- Het inschakelen van alleen beveiligde verbindingen met uw Redis Cache controleren
- Beveiligde overdracht naar opslag accounts controleren
- Controleer de instelling van de eigenschap ClusterProtectionLevel op EncryptAndSign in Service Fabric
- Controlestatus van transparante gegevensversleuteling
- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld

## <a name="a1241-event-logging"></a>A. 12.4.1 gebeurtenis logboek registratie

Deze blauw druk helpt u om ervoor te zorgen dat systeem gebeurtenissen worden geregistreerd door zeven [Azure Policy](../../../policy/overview.md) definities toe te wijzen die logboek instellingen op Azure-resources controleren.
Diagnostische logboeken bieden inzicht in bewerkingen die zijn uitgevoerd in azure-resources.

- \[Preview @ no__t-1: audit Dependency Agent implementatie-VM-installatie kopie (OS) niet vermeld
- \[Preview @ no__t-1: audit Dependency Agent implementatie in VMSS-VM-installatie kopie (OS) niet vermeld
- \[Preview @ no__t-1: audit Log Analytics agent implementatie-VM-installatie kopie (OS) niet vermeld
- \[Preview @ no__t-1: audit Log Analytics agent implementatie in VMSS-VM-installatie kopie (OS) niet vermeld
- Diagnostische instelling voor controleren
- Controle-instellingen op SQL server-niveau controleren
- Controle moet worden ingeschakeld voor geavanceerde instellingen voor gegevens beveiliging op SQL Server

## <a name="a1243-administrator-and-operator-logs"></a>Een. 12.4.3-beheerder en-operator logboeken

Deze blauw druk helpt u om ervoor te zorgen dat systeem gebeurtenissen worden geregistreerd door zeven Azure Policy definities toe te wijzen die logboek instellingen op Azure-resources controleren. Diagnostische logboeken bieden inzicht in bewerkingen die zijn uitgevoerd in azure-resources.

- \[Preview @ no__t-1: audit Dependency Agent implementatie-VM-installatie kopie (OS) niet vermeld
- \[Preview @ no__t-1: audit Dependency Agent implementatie in VMSS-VM-installatie kopie (OS) niet vermeld
- \[Preview @ no__t-1: audit Log Analytics agent implementatie-VM-installatie kopie (OS) niet vermeld
- \[Preview @ no__t-1: audit Log Analytics agent implementatie in VMSS-VM-installatie kopie (OS) niet vermeld
- Diagnostische instelling voor controleren
- Controle-instellingen op SQL server-niveau controleren
- Controle moet worden ingeschakeld voor geavanceerde instellingen voor gegevens beveiliging op SQL Server

## <a name="a1244-clock-synchronization"></a>Een. 12.4.4-klok synchronisatie

Deze blauw druk helpt u om ervoor te zorgen dat systeem gebeurtenissen worden geregistreerd door zeven Azure Policy definities toe te wijzen die logboek instellingen op Azure-resources controleren. Azure-logboeken zijn afhankelijk van gesynchroniseerde interne klokken voor het maken van een tijdgebonden record met gebeurtenissen in resources.

- \[Preview @ no__t-1: audit Dependency Agent implementatie-VM-installatie kopie (OS) niet vermeld
- \[Preview @ no__t-1: audit Dependency Agent implementatie in VMSS-VM-installatie kopie (OS) niet vermeld
- \[Preview @ no__t-1: audit Log Analytics agent implementatie-VM-installatie kopie (OS) niet vermeld
- \[Preview @ no__t-1: audit Log Analytics agent implementatie in VMSS-VM-installatie kopie (OS) niet vermeld
- Diagnostische instelling voor controleren
- Controle-instellingen op SQL server-niveau controleren
- Controle moet worden ingeschakeld voor geavanceerde instellingen voor gegevens beveiliging op SQL Server

## <a name="a1251-installation-of-software-on-operational-systems"></a>A. 12.5.1 installatie van software op operationele systemen

Adaptief toepassings beheer is een oplossing van Azure Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw virtuele machines die zich in azure bevinden. Deze blauw druk wijst een Azure Policy definitie toe waarmee wijzigingen in de set toegestane toepassingen worden gecontroleerd. Met deze mogelijkheid kunt u de installatie van software en toepassingen op Azure-Vm's beheren.

- \[Preview @ no__t-1: Bewaak mogelijke app white list in Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>A. 12.6.1 beheer van technische beveiligings problemen

Deze blauw druk helpt u bij het beheren van beveiligings problemen met informatie systemen door vijf [Azure Policy](../../../policy/overview.md) definities toe te wijzen die ontbrekende systeem updates, besturingssysteem problemen met het besturings systeem, SQL-beveiligings problemen en virtuele-machine-beveiligings problemen bewaken in Azure Security Center. Azure Security Center biedt rapportage mogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligings status van geïmplementeerde Azure-resources.

- \[Preview @ no__t-1: ontbrekende Endpoint Protection in Azure Security Center monitor
- \[Preview @ no__t-1: ontbrekende systeem updates in Azure Security Center controleren
- \[Preview @ no__t-1: beveiligings problemen van besturings systeem in Azure Security Center bewaken
- \[Preview @ no__t-1: resultaten van de evaluatie van SQL-beveiligings problemen controleren in Azure Security Center
- \[Preview @ no__t-1: VM-beveiligings problemen in Azure Security Center bewaken

## <a name="a1262-restrictions-on-software-installation"></a>A. 12.6.2-beperkingen voor software-installatie

Adaptief toepassings beheer is een oplossing van Azure Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw virtuele machines die zich in azure bevinden. Deze blauw druk wijst een Azure Policy definitie toe waarmee wijzigingen in de set toegestane toepassingen worden gecontroleerd. Beperkingen voor de software-installatie kunnen u helpen de kans op het introduceren van software-zwakke plekken te verminderen.

- \[Preview @ no__t-1: Bewaak mogelijke app white list in Azure Security Center

## <a name="a1311-network-controls"></a>Een. 13.1.1-netwerk besturings elementen

Deze blauw druk helpt u bij het beheren en best uren van netwerken door een [Azure Policy](../../../policy/overview.md) definitie toe te wijzen waarmee netwerk beveiligings groepen met strikte regels worden bewaakt. Het kan zijn dat te beperken regels mogelijk onbedoelde netwerk toegang toestaan en moeten worden gecontroleerd. Met deze blauw druk worden ook drie Azure Policy definities toegewezen waarmee onbeveiligde eind punten, toepassingen en opslag accounts worden bewaakt. Eind punten en toepassingen die niet zijn beveiligd door een firewall en opslag accounts met onbeperkte toegang, kunnen onbedoelde toegang tot gegevens in het informatie systeem toestaan.

- \[Preview @ no__t-1: Bewaak strikte netwerk toegang in Azure Security Center
- \[Preview @ no__t-1: Bewaak onbeveiligde netwerk eindpunten in Azure Security Center
- \[Preview @ no__t-1: niet-beveiligde webtoepassing in Azure Security Center bewaken
- Onbeperkte netwerk toegang tot opslag accounts controleren

## <a name="a1321-information-transfer-policies-and-procedures"></a>A. 13.2.1 Information-beleid en procedures voor het overdragen van informatie

De blauw druk helpt u ervoor te zorgen dat de overdracht van gegevens met Azure-Services veilig is door twee [Azure Policy](../../../policy/overview.md) definities toe te wijzen voor het controleren van onbeveiligde verbindingen met opslag accounts en redis cache.

- Het inschakelen van alleen beveiligde verbindingen met uw Redis Cache controleren
- Beveiligde overdracht naar opslag accounts controleren

## <a name="next-steps"></a>Volgende stappen

Nu u de toewijzing van het besturings element van de ISO 27001-blauw druk hebt gecontroleerd, kunt u de volgende artikelen raadplegen voor meer informatie over de architectuur en hoe u dit voor beeld implementeert:

> [!div class="nextstepaction"]
> [Iso 27001 blauw druk-overzicht](./index.md)
> [ISO 27001 blauw druk-stappen implementeren](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).