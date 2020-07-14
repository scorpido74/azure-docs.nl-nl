---
title: Beheeropties voor blauwdrukvoorbeeld voor ISO 27001 conforme ASE-/SQL-workloads
description: Toewijzing van beheeropties van het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads in Azure Policy en RBAC.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 518c6b86ac658273d428a328b433b35d667d7056
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044750"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>Toewijzing van beheeropties voor blauwdrukvoorbeeld voor ISO 27001 conforme ASE-/SQL Database-workloads

Het volgende artikel bevat details over de manier waarop het blauwdrukvoorbeeld voor Azure Blueprints ISO 27001 conforme ASE-/SQL-workloads wordt toegewezen aan de ISO 27001-beheeropties. Zie [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html) voor meer informatie over de beheeropties.

De volgende toewijzingen zijn voor de **ISO 27001:2013**-beheeropties. Gebruik de navigatie aan de rechterkant om rechtstreeks naar een toewijzing van een specifieke beheeroptie te gaan. Veel van de toegewezen beheeropties worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md)-initiatief. Als u het complete initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities**. Zoek en selecteer vervolgens het ingebouwde beleidsinitiatief **\[Preview\] ISO 27001:2013-beheeropties controleren en specifieke VM-extensies implementeren ter ondersteuning van de controlevereisten**.

> [!IMPORTANT]
> Elke beheeroptie hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md)-definities. Met deze beleidsregels kunt u de [compliance beoordelen](../../../policy/how-to/get-compliance-data.md) met de beheeroptie. Er is echter vaak geen één-op-één- of volledige overeenkomst tussen een beheeroptie en een of meer beleidsregels. Als zodanig verwijst de term **Conform** in Azure Policy alleen naar de beleidsregels zelf. Dit garandeert niet dat u volledig conform bent met alle vereisten van een beheeroptie. Daarnaast bevat de compliance standaard beheeropties die op dit moment nog niet worden beschreven door Azure Policy-definities. Daarom is compliance in Azure Policy slechts een gedeeltelijke weergave van uw algemene compliancestatus. De koppelingen tussen de beheeropties en Azure Policy definities voor dit blauwdrukvoorbeeld voor compliance kunnen na verloop van tijd veranderen. Als u de wijzigingsgeschiedenis wilt bekijken, raadpleegt u de [GitHub Commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-ase-sql-workload/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Scheiding van taken

Als er slechts één eigenaar van een Azure-abonnement is, is er geen administratieve redundantie toegestaan. Als er daarentegen te veel eigenaars van Azure-abonnementen zijn, is er een grotere kans op schendingen via een account van een gecompromitteerde eigenaar. Met deze blauwdruk kunt u een passend aantal eigenaren van Azure-abonnementen in stand houden door twee [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee het aantal eigenaren van Azure-abonnementen wordt gecontroleerd. Met het beheer van eigenaarsmachtigingen voor abonnementen kunt u een juiste scheiding van taken implementeren.

- \[Preview\]: Minimum aantal eigenaren voor een abonnement controleren
- \[Preview\]: Maximum aantal eigenaren voor een abonnement controleren

## <a name="a821-classification-of-information"></a>A.8.2.1 Classificatie van gegevens

Met de [service voor evaluatie van SQL-beveiligingsproblemen](../../../../azure-sql/database/sql-vulnerability-assessment.md) in Azure kunt u gevoelige gegevens ontdekken die zijn opgeslagen in uw databases. Het bevat tevens aanbevelingen voor het classificeren van die gegevens. Deze blauwdruk wijst een [Azure Policy](../../../policy/overview.md)-definitie toe om te controleren of de problemen die zijn geïdentificeerd tijdens de scan op SQL-beveiligingsproblemen, worden hersteld.

- \[Preview\]: Resultaten van evaluatie van SQL-beveiligingsproblemen in Azure Security Center bewaken

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Toegang tot netwerken en netwerkservices

In Azure wordt [op rollen gebaseerd toegangsbeheer](../../../../role-based-access-control/overview.md) (RBAC) geïmplementeerd om te beheren wie toegang heeft tot Azure-resources. Met deze blauwdruk kunt u de toegang tot Azure-resources beheren door zeven [Azure Policy](../../../policy/overview.md)-definities toe te wijzen. Met deze beleidsregels wordt het gebruik van resourcetypen en -configuraties gecontroleerd, waardoor de toegang tot resources kan worden uitgebreid.
Dankzij meer inzicht in resources die in strijd zijn met deze beleidsregels kunt u corrigerende maatregelen treffen om te garanderen dat toegang tot Azure-resources wordt beperkt tot gemachtigde gebruikers.

- \[Preview\]: VM-extensie implementeren om Linux VM-accounts zonder wachtwoorden te controleren
- \[Preview\]: VM-extensie implementeren om Linux-VM te controleren die externe verbindingen van accounts zonder wachtwoorden toestaat
- \[Preview\]: Linux VM-accounts zonder wachtwoorden controleren
- \[Preview\]: Linux-VM controleren die externe verbindingen van accounts zonder wachtwoorden toestaat
- Gebruik van klassieke opslagaccounts controleren
- Gebruik van klassieke virtuele machines controleren
- VM's controleren die niet gebruikmaken van beheerde schijven

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Beheer van bevoegde toegangsrechten

Met deze blauwdruk kunt u rechten voor bevoegde toegang beperken en beheren door vier [Azure Policy](../../../policy/overview.md)-definities toe te wijzen voor de controle van externe accounts met eigenaars- en/of schrijfrechten en accounts met eigenaars- en/of schrijfrechten waarvoor geen meervoudige verificatie is ingeschakeld. In Azure wordt op rollen gebaseerd toegangsbeheer (RBAC) geïmplementeerd om te beheren wie toegang heeft tot Azure-resources. Met deze blauwdruk wordt ook drie Azure Policy-definities toegewezen om het gebruik van Azure Active Directory-verificatie voor SQL-servers en Service Fabric te controleren. Met behulp van Azure Active Directory-verificatie maakt vereenvoudigd beheer van machtigingen en gecentraliseerd identiteitsbeheer van databasegebruikers en andere Microsoft-services mogelijk. Met deze blauwdruk wordt ook een Azure Policy-definitie toegewezen om het gebruik van aangepaste RBAC-regels te controleren. Wanneer u weet waar aangepaste RBAC-regels zijn geïmplementeerd, kan dit u helpen om te controleren of deze nodig zijn en of deze correct zijn geïmplementeerd, omdat aangepaste RBAC-regels foutgevoelig zijn.

- \[Preview\]: Accounts met eigenaarsmachtigingen, waarvoor MFA niet is ingeschakeld, voor een abonnement controleren
- \[Preview\]: Accounts met schrijfmachtigingen, waarvoor MFA niet is ingeschakeld, voor een abonnement controleren
- \[Preview\]: Externe accounts met eigenaarsmachtigingen voor een abonnement controleren
- \[Preview\]: Externe accounts met schrijfmachtigingen voor een abonnement controleren
- Inrichting van een Azure Active Directory-beheerder voor SQL-server controleren
- Gebruik van Azure Active Directory voor verificatie van clients in Service Fabric controlerenAudit usage of Azure Active Directory for client authentication in Service Fabric
- Het gebruik van aangepaste RBAC-regels controleren

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Beheer van geheime verificatiegegevens van gebruikers

Met deze blauwdruk worden drie [Azure Policy](../../../policy/overview.md)-definities toegewezen voor het controleren van accounts waarvoor meervoudige verificatie niet is ingeschakeld. U kunt met meervoudige verificatie accounts veilig houden, zelfs als er wordt geknoeid met één onderdeel van de verificatiegegevens. Door accounts te controleren waarvoor geen meervoudige verificatie is ingeschakeld, kunt u accounts identificeren waarvoor het waarschijnlijker is dat deze worden gecompromitteerd. Met deze blauwdruk worden ook twee Azure Policy-definities toegewezen waarmee wordt gecontroleerd of de machtigingen voor het wachtwoordbestand voor Linux-VM's een waarschuwing geven als deze niet juist zijn ingesteld. Met deze instelling kunt u corrigerende maatregelen treffen om te garanderen dat geen misbruik van authenticators wordt gemaakt.

- \[Preview\]: Accounts met eigenaarsmachtigingen, waarvoor MFA niet is ingeschakeld, voor een abonnement controleren
- \[Preview\]: Accounts met leesmachtigingen, waarvoor MFA niet is ingeschakeld, voor een abonnement controleren
- \[Preview\]: Accounts met schrijfmachtigingen, waarvoor MFA niet is ingeschakeld, voor een abonnement controleren
- \[Preview\]: VM-extensie implementeren om de machtigingen voor het passwd-bestand voor Linux-VM's te controleren
- \[Preview\]: Controleren of de machtigingen voor het /etc/passwd-bestand voor Linux-VM's zijn ingesteld op 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Beoordeling van toegangsrechten voor gebruikers

In Azure wordt [op rollen gebaseerde toegangsbeheer](../../../../role-based-access-control/overview.md) (RBAC) geïmplementeerd, waarmee u de toegang tot resources in Azure kunt beheren. Met behulp van de Azure-portal kunt u controleren wie toegang heeft tot Azure-resources en wat hun machtigingen zijn. Met deze blauwdruk worden vier [Azure Policy](../../../policy/overview.md)-definities toegewezen voor het controleren van accounts die prioriteit moeten krijgen om te worden beoordeeld, waaronder afgeschreven accounts en externe accounts met verhoogde rechten.

- \[Preview\]: Afgeschafte accounts voor een abonnement controleren
- \[Preview\]: Afgeschafte accounts met eigenaarsmachtigingen voor een abonnement controleren
- \[Preview\]: Externe accounts met eigenaarsmachtigingen voor een abonnement controleren
- \[Preview\]: Externe accounts met schrijfmachtigingen voor een abonnement controleren

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Verwijdering of aanpassing van toegangsrechten

In Azure wordt [op rollen gebaseerde toegangsbeheer](../../../../role-based-access-control/overview.md) (RBAC) geïmplementeerd, waarmee u de toegang tot resources in Azure kunt beheren. Met behulp van [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) en RBAC kunt u gebruikersrollen bijwerken om wijzigingen in de organisatie weer te geven. Zo nodig kunnen accounts voor aanmelding worden geblokkeerd (of verwijderd), waardoor de toegangsrechten tot Azure-resources onmiddellijk worden verwijderd. Deze blauwdruk wijst twee [Azure Policy](../../../policy/overview.md)-definities toe voor het controleren van afgeschreven accounts die voor verwijdering moeten worden aangemerkt.

- \[Preview\]: Afgeschafte accounts voor een abonnement controleren
- \[Preview\]: Afgeschafte accounts met eigenaarsmachtigingen voor een abonnement controleren

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 Beveiligde aanmeldingsprocedures

Met deze blauwdruk worden drie Azure Policy-definities toegewezen voor het controleren van accounts waarvoor meervoudige verificatie niet is ingeschakeld. Azure MFA biedt extra beveiliging door een tweede vorm van verificatie te vereisen en voorziet in sterke verificatie. Door accounts te controleren waarvoor geen meervoudige verificatie is ingeschakeld, kunt u accounts identificeren waarvoor het waarschijnlijker is dat deze worden gecompromitteerd.

- \[Preview\]: Accounts met eigenaarsmachtigingen, waarvoor MFA niet is ingeschakeld, voor een abonnement controleren
- \[Preview\]: Accounts met leesmachtigingen, waarvoor MFA niet is ingeschakeld, voor een abonnement controleren
- \[Preview\]: Accounts met schrijfmachtigingen, waarvoor MFA niet is ingeschakeld, voor een abonnement controleren

## <a name="a943-password-management-system"></a>A.9.4.3 Wachtwoordbeheersysteem

Met deze blauwdruk kunt u sterke wachtwoorden afdwingen door tien [Azure Policy](../../../policy/overview.md)-definities toe te wijzen waarmee Windows-VM's worden gecontroleerd die geen vereisten voor minimale sterkte en andere wachtwoordvereisten afdwingen. Wanneer u op de hoogte bent van VM's die in strijd zijn met het beleid voor wachtwoordsterkte, kunt u corrigerende maatregelen nemen om ervoor te zorgen dat wachtwoorden voor alle gebruikersaccounts van de VM's voldoen aan dit beleid.

- \[Preview\]: VM-extensie implementeren om te controleren of Windows-VM de vereisten voor wachtwoordcomplexiteit afdwingt
- \[Preview\]: VM-extensie implementeren om te controleren op de maximale levensduur van zeventig dagen voor het wachtwoord voor Windows-VM
- \[Preview\]: VM-extensie implementeren om te controleren op de maximale levensduur van één dag voor het wachtwoord voor Windows-VM
- \[Preview\]: VM-extensie implementeren om te controleren of wachtwoorden voor Windows-VM minstens 14 tekens bevatten
- \[Preview\]: VM-extensie implementeren om te controleren of Windows-VM de voorgaande 24 wachtwoorden niet toestaat
- \[Preview\]: Controleren of Windows-VM de vereisten voor wachtwoordcomplexiteit afdwingt
- \[Preview\]: Controleren op de maximale levensduur van zeventig dagen voor het wachtwoord voor Windows-VM
- \[Preview\]: Controleren op de minimale levensduur van één dag voor het wachtwoord voor Windows-VM
- \[Preview\]: Controleren of wachtwoorden voor Windows-VM minstens 14 tekens bevatten
- \[Preview\]: Controleren of Windows VM de voorgaande 24 wachtwoorden niet toestaat

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Beleid voor het gebruik van cryptografische beheeropties

Met deze blauwdruk kunt u uw beleid afdwingen voor het gebruik van cryptografische beheeropties door dertien [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die specifieke cryptografische beheeropties afdwingen en het gebruik van zwakke cryptografische instellingen controleren.
Wanneer u weet waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, kunt u corrigerende maatregelen nemen om ervoor te zorgen dat resources worden geconfigureerd in overeenstemming met uw informatiebeveiligingsbeleid. Met name de beleidsregels die met deze blauwdruk worden toegewezen, vereisen versleuteling voor blobopslagaccounts en data lake-opslagaccounts; vereisen transparante gegevensversleuteling voor SQL-databases; controleren op ontbrekende versleuteling voor opslagaccounts, SQL-databases, schijven van virtuele machines en variabelen van Automation-accounts; controleren op onbeveiligde verbindingen met opslagaccounts, functie-apps, web-apps, API-apps en Redis Cache; controleren op zwakke versleuteling van wachtwoorden van virtuele machines; en controleren op niet-versleutelde Service Fabric-communicatie.

- \[Preview\]: Exclusieve HTTPS-toegang controleren voor een functie-app
- \[Preview\]: Exclusieve HTTPS-toegang controleren voor een webtoepassing
- \[Preview\]: Exclusieve HTTPS-toegang controleren voor een API-app
- \[Preview\]: Ontbrekende blobversleuteling voor opslagaccounts controleren
- \[Preview\]: VM-extensie implementeren om Windows-VM's te controleren waarop geen wachtwoorden mogen worden opgeslagen met omkeerbare versleuteling
- \[Preview\]: Windows-VM's controleren waarop geen wachtwoorden mogen worden opgeslagen met omkeerbare versleuteling
- \[Preview\]: Niet-versleutelde VM-schijven in Azure Security Center bewaken
- Inschakeling van versleuteling van Automation-accountvariabelen controleren
- Inschakelen van uitsluitend beveiligde verbindingen met Redis Cache controleren
- Veilige overdracht naar opslagaccounts controleren
- Het instellen van de eigenschap ClusterProtectionLevel naar EncryptAndSign in Service Fabric controleren
- Controlestatus van transparante gegevensversleuteling
- Transparent Data Encryption in SQL-databases moet zijn ingeschakeld

## <a name="a1241-event-logging"></a>A.12.4.1 Logboekregistratie van gebeurtenissen

Met deze blauwdruk kunt u ervoor zorgen dat systeemgebeurtenissen worden geregistreerd door zeven [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die logboekinstellingen op Azure-resources controleren.
Diagnostische logboeken bieden inzicht in bewerkingen die zijn uitgevoerd in Azure-resources.

- \[Preview\]: Implementatie van Dependency Agent controleren - VM-installatiekopie (besturingssysteem) niet vermeld
- \[Preview\]: Implementatie van Dependency Agent in VMSS controleren - VM-installatiekopie (besturingssysteem) niet vermeld
- \[Preview\]: Implementatie van de Log Analytics-agent controleren - VM-installatiekopie (besturingssysteem) onvermeld
- \[Preview\]: Implementatie van de Log Analytics-agent controleren in VMSS - VM-installatiekopie (besturingssysteem) onvermeld
- Diagnostische instelling voor controleren
- Instellingen voor SQL-controle op serverniveau controleren
- De controle moet zijn ingeschakeld in de instellingen voor geavanceerde gegevensbeveiliging in SQL Server

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 Beheerders- en operatorlogboeken

Met deze blauwdruk kunt u ervoor zorgen dat systeemgebeurtenissen worden geregistreerd door zeven Azure Policy-definities toe te wijzen die logboekinstellingen op Azure-resources controleren. Diagnostische logboeken bieden inzicht in bewerkingen die zijn uitgevoerd in Azure-resources.

- \[Preview\]: Implementatie van Dependency Agent controleren - VM-installatiekopie (besturingssysteem) niet vermeld
- \[Preview\]: Implementatie van Dependency Agent in VMSS controleren - VM-installatiekopie (besturingssysteem) niet vermeld
- \[Preview\]: Implementatie van de Log Analytics-agent controleren - VM-installatiekopie (besturingssysteem) onvermeld
- \[Preview\]: Implementatie van de Log Analytics-agent controleren in VMSS - VM-installatiekopie (besturingssysteem) onvermeld
- Diagnostische instelling voor controleren
- Instellingen voor SQL-controle op serverniveau controleren
- De controle moet zijn ingeschakeld in de instellingen voor geavanceerde gegevensbeveiliging in SQL Server

## <a name="a1244-clock-synchronization"></a>A.12.4.4 Kloksynchronisatie

Met deze blauwdruk kunt u ervoor zorgen dat systeemgebeurtenissen worden geregistreerd door zeven Azure Policy-definities toe te wijzen die logboekinstellingen op Azure-resources controleren. Azure-logboeken zijn afhankelijk van gesynchroniseerde interne klokken voor het maken van een tijdgebonden record met gebeurtenissen in resources.

- \[Preview\]: Implementatie van Dependency Agent controleren - VM-installatiekopie (besturingssysteem) niet vermeld
- \[Preview\]: Implementatie van Dependency Agent in VMSS controleren - VM-installatiekopie (besturingssysteem) niet vermeld
- \[Preview\]: Implementatie van de Log Analytics-agent controleren - VM-installatiekopie (besturingssysteem) onvermeld
- \[Preview\]: Implementatie van de Log Analytics-agent controleren in VMSS - VM-installatiekopie (besturingssysteem) onvermeld
- Diagnostische instelling voor controleren
- Instellingen voor SQL-controle op serverniveau controleren
- De controle moet zijn ingeschakeld in de instellingen voor geavanceerde gegevensbeveiliging in SQL Server

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Installatie van software op operationele systemen

Adaptieve toepassingsregelaar is een oplossing van Azure Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw VM’s in Azure. Deze blauwdruk wijst een Azure Policy-definitie toe waarmee wijzigingen in de set toegestane toepassingen worden bewaakt. Met deze mogelijkheid kunt u de installatie van software en toepassingen op Azure-VM's beheren.

- \[Preview\]: Mogelijkheid voor de lijst met goedgekeurde apps bewaken in Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 Beheer van technische beveiligingsproblemen

Met deze blauwdruk kunt u beveiligingsproblemen in informatiesystemen beheren door vijf [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die controleren op ontbrekende systeemupdates, beveiligingsproblemen met het besturingssysteem, SQL en virtuele machines in Azure Security Center. Azure Security Center biedt rapportagemogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligingsstatus van geïmplementeerde Azure-resources.

- \[Preview\]: Ontbrekende Endpoint Protection bewaken in Azure Security Center
- \[Preview\]: Ontbrekende systeemupdates bewaken in Azure Security Center
- \[Preview\]: Beveiligingsproblemen van besturingssystemen bewaken in Azure Security Center
- \[Preview\]: Resultaten van evaluatie van SQL-beveiligingsproblemen in Azure Security Center bewaken
- \[Preview\]: Beveiligingsproblemen van virtuele machines bewaken in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Beperkingen op software-installatie

Adaptieve toepassingsregelaar is een oplossing van Azure Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw VM’s in Azure. Deze blauwdruk wijst een Azure Policy-definitie toe waarmee wijzigingen in de set toegestane toepassingen worden bewaakt. Beperkingen op software-installatie kunnen de kans op het introduceren van beveiligingsproblemen in de software verminderen.

- \[Preview\]: Mogelijkheid voor de lijst met goedgekeurde apps bewaken in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 Netwerkbeheeropties

Met deze blauwdruk kunt u netwerken beheren door een [Azure Policy](../../../policy/overview.md)-definitie toe te wijzen waarmee netwerkbeveiligingsgroepen met te ruime regels worden bewaakt. Regels die te ruim zijn, kunnen de oorzaak zijn van onbedoelde netwerktoegang en moeten worden gecontroleerd. Met deze blauwdruk worden ook die Azure Policy-definities toegewezen waarmee onbeveiligde eindpunten, toepassingen en opslagaccounts worden bewaakt. Eindpunten en toepassingen die niet zijn beveiligd door een firewall, en opslagaccounts met onbeperkte toegang, kunnen onbedoeld toegang verlenen tot gegevens in het informatiesysteem.

- \[Preview\]: Ruime netwerktoegang bewaken in Azure Security Center
- \[Preview\]: Niet-beveiligde netwerkeindpunten bewaken in Azure Security Center
- \[Preview\]: Niet-beveiligde webtoepassing bewaken in Azure Security Center
- Netwerktoegang tot opslagaccounts moet zijn beperkt

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Beleid en procedures voor informatieoverdracht

Deze blauwdruk zorgt voor een beveiligde overdracht van gegevens met Azure-services door twee [Azure Policy](../../../policy/overview.md)-definities toe te wijzen voor het controleren van onbeveiligde verbindingen met opslagaccounts en Redis Cache.

- Inschakelen van uitsluitend beveiligde verbindingen met Redis Cache controleren
- Veilige overdracht naar opslagaccounts controleren

## <a name="next-steps"></a>Volgende stappen

Nu u de toewijzing van beheeropties voor het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads hebt bekeken, kunt u naar de volgende artikelen gaan voor informatie over de architectuur en de implementatie van dit voorbeeld:

> [!div class="nextstepaction"]
> [Blauwdruk voor ISO 27001 conforme App Service Environment-/SQL Database-workloads - overzicht](./index.md)
> [Blauwdruk voor ISO 27001 conforme App Service Environment-/SQL Database-workloads - implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
