---
title: Besturingselementen voor iso 27001-blauwdruk
description: Beheer het ontwerp van het ISO 27001-blauwdrukvoorbeeld. Elk besturingselement wordt toegewezen aan een of meer Azure-beleidsregels die helpen bij de beoordeling.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 501884491dbef85cdf8a29cb5fdcef44a68235a9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75920575"
---
# <a name="control-mapping-of-the-iso-27001-blueprint-sample"></a>Controletoewijzing van het blauwdrukvoorbeeld van ISO 27001

In het volgende artikel wordt beschreven hoe het blueprintvoorbeeld azure blueprints iso 27001 wordt toegewezen aan de BESTURINGSELEMENTEN ISO 27001. Zie [ISO 27001 voor](https://www.iso.org/isoiec-27001-information-security.html)meer informatie over de besturingselementen.

De volgende toewijzingen zijn naar de **ISO 27001:2013** besturingselementen. Gebruik de navigatie aan de rechterkant om direct naar een specifieke besturingstoewijzing te springen. Veel van de toegewezen besturingselementen worden geïmplementeerd met een [Azure Policy-initiatief.](../../../policy/overview.md) Als u het volledige initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities.** Zoek en selecteer vervolgens de ** \[besturingselementen\] Voorcontrole ISO 27001:2013 en implementeer vervolgens specifieke VM-extensies ter ondersteuning van het** ingebouwde beleidsinitiatief voor ingebouwde auditvereisten.

> [!IMPORTANT]
> Elk besturingselement hieronder is gekoppeld aan een of meer [Azure Policy-definities.](../../../policy/overview.md) Met dit beleid u [beoordelen of de](../../../policy/how-to/get-compliance-data.md) controle is nageleefd; Er is echter vaak geen 1:1 of volledige overeenkomst tussen een besturingselement en een of meer beleidsregels. **Compliant** in Azure Policy verwijst daarom alleen naar het beleid zelf. Dit zorgt er niet voor dat u volledig voldoet aan alle vereisten van een controle. Bovendien bevat de nalevingsstandaard besturingselementen die op dit moment niet worden behandeld door azure-beleidsdefinities. Naleving in Azure Policy is daarom slechts een gedeeltelijke weergave van uw algemene nalevingsstatus. De koppelingen tussen besturingselementen en Azure Policy-definities voor dit voorbeeld van nalevingsblauwdrukken kunnen in de loop van de tijd veranderen. Zie de [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001/control-mapping.md)om de wijzigingsgeschiedenis te bekijken.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Scheiding van taken

Als u slechts één Azure-abonnementseigenaar hebt, is er geen beheerredundantie mogelijk. Omgekeerd kan het hebben van te veel Azure-abonnementseigenaren het potentieel voor een inbreuk vergroten via een gecompromitteerd eigenaaraccount. Met deze blauwdruk u een passend aantal Azure-abonnementseigenaren behouden door twee [Azure-beleidsdefinities](../../../policy/overview.md) toe te wijsen die het aantal eigenaren voor Azure-abonnementen controleren. Het beheren van machtigingen voor abonnementseigenaren kan u helpen bij het implementeren van de juiste scheiding van taken.

- \[Preview\]: Minimaal aantal eigenaren controleren voor een abonnement
- \[Preview\]: Maximaal aantal eigenaren controleren voor een abonnement

## <a name="a821-classification-of-information"></a>A.8.2.1 Classificatie van informatie

Azure's [SQL Vulnerability Assessment-service](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) kan u helpen bij het ontdekken van gevoelige gegevens die zijn opgeslagen in uw databases en bevat aanbevelingen om die gegevens te classificeren. In deze blauwdruk wordt een [Azure Policy-definitie](../../../policy/overview.md) toegegeven om te controleren of kwetsbaarheden die zijn geïdentificeerd tijdens SQL Vulnerability Assessment-scan worden verholpen.

- \[Preview:\]Resultaten van sql-kwetsbaarheidsbeoordeling in Azure Security Center controleren

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Toegang tot netwerken en netwerkdiensten

Azure implementeert [rbac (role-based access control)](../../../../role-based-access-control/overview.md) om te beheren wie toegang heeft tot Azure-bronnen. Met deze blauwdruk u de toegang tot Azure-bronnen beheren door zeven [Azure-beleidsdefinities](../../../policy/overview.md) toe te geven. Deze beleidsregels controleren het gebruik van resourcetypen en -configuraties die mogelijk meer tolerante toegang tot resources mogelijk maken.
Als u inzicht hebt in resources die in strijd zijn met dit beleid, u corrigerende maatregelen nemen om ervoor te zorgen dat de toegang tot Azure-bronnen beperkt is tot geautoriseerde gebruikers.

- \[Preview:\]VM-extensie implementeren om Linux VM-accounts zonder wachtwoorden te controleren
- \[Preview:\]VM-extensie implementeren om Linux VM te controleren, waardoor externe verbindingen van accounts zonder wachtwoorden mogelijk zijn
- \[Preview\]: Audit Linux VM accounts with no passwords Preview : Audit Linux VM accounts with no passwords Preview : Audit Linux VM accounts with no passwords Preview :
- \[Preview\]: Audit Linux VM waardoor externe verbindingen van accounts zonder wachtwoorden
- Controle gebruik van klassieke opslagaccounts
- Controle gebruik van klassieke virtuele machines
- VM's controleren die geen beheerde schijven gebruiken

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Beheer van bevoorrechte toegangsrechten

Met deze blauwdruk u bevoorrechte toegangsrechten beperken en beheren door vier [Azure](../../../policy/overview.md) Policy-definities toe te stellen om externe accounts te controleren met eigenaar en/of machtigingen en accounts te schrijven met eigenaar- en/of schrijfmachtigingen die geen multi-factor-verificatie hebben ingeschakeld. Azure implementeert rbac (role-based access control) om te beheren wie toegang heeft tot Azure-bronnen. In deze blauwdruk worden ook drie Azure-beleidsdefinities toekent om het gebruik van Azure Active Directory-verificatie voor SQL Servers en Service Fabric te controleren. Met Azure Active Directory-verificatie u vereenvoudigd machtigingsbeheer en gecentraliseerd identiteitsbeheer van databasegebruikers en andere Microsoft-services gebruiken. In deze blauwdruk wordt ook een Azure-beleidsdefinitie toegegeven om het gebruik van aangepaste RBAC-regels te controleren. Als u begrijpt waar aangepaste RBAC-regels worden geïmplementeerd, u de noodzaak en de juiste implementatie controleren, omdat aangepaste RBAC-regels foutgevoelig zijn.

- \[Voorbeeld:\]Accounts controleren met machtigingen voor eigenaren die niet zijn ingeschakeld voor MFA op een abonnement
- \[Voorbeeld:\]Controleaccounts met schrijfmachtigingen die niet zijn ingeschakeld voor MFA op een abonnement
- \[Voorbeeld:\]Externe accounts controleren met machtigingen voor eigenaren voor een abonnement
- \[Voorbeeld:\]Externe accounts controleren met schrijfmachtigingen voor een abonnement
- Controlebepaling van een Azure Active Directory-beheerder voor SQL-server
- Controle van het gebruik van Azure Active Directory voor clientverificatie in ServiceFabric
- Controle gebruik van aangepaste RBAC-regels

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Beheer van geheime authenticatiegegevens van gebruikers

In deze blauwdruk worden drie [Azure Policy-definities](../../../policy/overview.md) toegerekend aan controleaccounts die geen multifactorauthenticatie hebben ingeschakeld. Meervoudige verificatie helpt accounts veilig te houden, zelfs als één stuk verificatiegegevens wordt gecompromitteerd. Door accounts te controleren zonder dat multi-factor authenticatie is ingeschakeld, u accounts identificeren die waarschijnlijk meer kans hebben op gecompromitteerd. In deze blauwdruk worden ook twee Azure-beleidsdefinities toegestaan die de machtigingen voor Linux VM-wachtwoordbestanden controleren om te waarschuwen als deze onjuist zijn ingesteld. Met deze instelling u corrigerende maatregelen nemen om ervoor te zorgen dat authenticators niet worden gecompromitteerd.

- \[Voorbeeld:\]Accounts controleren met machtigingen voor eigenaren die niet zijn ingeschakeld voor MFA op een abonnement
- \[Voorbeeld:\]Accounts controleren met leesmachtigingen die niet zijn ingeschakeld voor MFA op een abonnement
- \[Voorbeeld:\]Controleaccounts met schrijfmachtigingen die niet zijn ingeschakeld voor MFA op een abonnement
- \[Voorbeeld:\]VM-extensie implementeren om linux VM-passwd-bestandsmachtigingen te controleren
- \[Preview\]: Audit Linux VM /etc/passwd bestandsmachtigingen zijn ingesteld op 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Beoordeling van de toegangsrechten van gebruikers

Azure implementeert [op rollen gebaseerd toegangscontrolebeheer](../../../../role-based-access-control/overview.md) (RBAC) om u te helpen beheren wie toegang heeft tot bronnen in Azure. Met behulp van de Azure-portal u controleren wie toegang heeft tot Azure-bronnen en hun machtigingen. In deze blauwdruk worden vier [Azure Policy-definities](../../../policy/overview.md) toegekend aan controleaccounts die moeten worden geprioriteerd voor controle, waaronder afgeschreven accounts en externe accounts met verhoogde machtigingen.

- \[Preview\]: Afgeschafte accounts controleren bij een abonnement
- \[Voorbeeld:\]Afgeschafte accounts controleren met eigenaarmachtigingen voor een abonnement
- \[Voorbeeld:\]Externe accounts controleren met machtigingen voor eigenaren voor een abonnement
- \[Voorbeeld:\]Externe accounts controleren met schrijfmachtigingen voor een abonnement

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Verwijdering of aanpassing van toegangsrechten

Azure implementeert [op rollen gebaseerd toegangscontrolebeheer](../../../../role-based-access-control/overview.md) (RBAC) om u te helpen beheren wie toegang heeft tot bronnen in Azure. Met [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) en RBAC u gebruikersrollen bijwerken om organisatorische wijzigingen weer te geven. Indien nodig kunnen accounts worden geblokkeerd voor het aanmelden (of verwijderd), waardoor de toegangsrechten tot Azure-bronnen onmiddellijk worden verwijderd. In deze blauwdruk worden twee [Azure Policy-definities](../../../policy/overview.md) toegekend voor het controleren van afgeschreven account dat moet worden overwogen voor verwijdering.

- \[Preview\]: Afgeschafte accounts controleren bij een abonnement
- \[Voorbeeld:\]Afgeschafte accounts controleren met eigenaarmachtigingen voor een abonnement

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 Veilige aanmeldingsprocedures

In deze blauwdruk worden drie Azure Policy-definities toegerekend aan controleaccounts die geen multifactorauthenticatie hebben ingeschakeld. Azure Multi-Factor Authentication biedt extra beveiliging door een tweede vorm van authenticatie te vereisen en levert sterke verificatie. Door accounts te controleren zonder dat multi-factor authenticatie is ingeschakeld, u accounts identificeren die waarschijnlijk meer kans hebben op gecompromitteerd.

- \[Voorbeeld:\]Accounts controleren met machtigingen voor eigenaren die niet zijn ingeschakeld voor MFA op een abonnement
- \[Voorbeeld:\]Accounts controleren met leesmachtigingen die niet zijn ingeschakeld voor MFA op een abonnement
- \[Voorbeeld:\]Controleaccounts met schrijfmachtigingen die niet zijn ingeschakeld voor MFA op een abonnement

## <a name="a943-password-management-system"></a>A.9.4.3 Wachtwoordbeheersysteem

Met deze blauwdruk u sterke wachtwoorden afdwingen door 10 [Azure Policy-definities](../../../policy/overview.md) toe te kennen die Windows VM's controleren die geen minimale sterkte en andere wachtwoordvereisten afdwingen. Door u te verzekeren van VM's in strijd met het wachtwoordsterktebeleid, u corrigerende maatregelen nemen om ervoor te zorgen dat wachtwoorden voor alle VM-gebruikersaccounts voldoen aan het beleid.

- \[Voorbeeld:\]VM-extensie implementeren om Windows VM te controleren, dwingt vereisten voor wachtwoordcomplexiteit af
- \[Voorbeeld:\]VM-extensie implementeren om de maximale wachtwoordleeftijd van Windows VM 70 dagen te controleren
- \[Voorbeeld:\]VM-extensie implementeren om de minimumleeftijd van Windows VM voor minimumwachtwoorden 1 dag te controleren
- \[Voorbeeld:\]VM-extensie implementeren om Windows VM-wachtwoorden te controleren, moeten ten minste 14 tekens bevatten
- \[Voorbeeld:\]VM-extensie implementeren om Windows VM te controleren, mag eerdere 24 wachtwoorden niet toestaan
- \[Preview:\]Controle Windows VM dwingt vereisten voor complexiteit van wachtwoorden af
- \[Preview:\]Windows VM maximale wachtwoordleeftijd 70 dagen controleren
- \[Preview:\]Windows VM minimum wachtwoordleeftijd 1 dag controleren
- \[Voorbeeld:\]Windows VM-wachtwoorden controleren moeten ten minste 14 tekens zijn
- \[Voorbeeld:\]Controle Windows VM mag eerdere 24 wachtwoorden niet toestaan

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Beleid inzake het gebruik van cryptografische besturingselementen

Met deze blauwdruk u uw beleid inzake het gebruik van cryptograafbesturingselementen afdwingen door 13 [Azure Policy-definities](../../../policy/overview.md) toe te stellen die specifieke cryptografiebesturingselementen afdwingen en het gebruik van zwakke cryptografische instellingen controleren.
Als u begrijpt waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, u corrigerende maatregelen nemen om ervoor te zorgen dat resources zijn geconfigureerd in overeenstemming met uw informatiebeveiligingsbeleid. In het bijzonder vereist het beleid dat door deze blauwdruk is toegewezen versleuteling voor blob-opslagaccounts en opslagaccounts voor gegevensbestanden. transparante gegevensversleuteling in SQL-databases vereisen; ontbrekende versleuteling op opslagaccounts, SQL-databases, virtuele machineschijven en automatiseringsaccountvariabelen te controleren; onveilige verbindingen met opslagaccounts, functie-apps, web-app,API-apps en Redis-cache te controleren; zwakke wachtwoordversleuteling voor virtuele machines te controleren; en controleer onversleutelde Service Fabric-communicatie.

- \[Voorbeeld:\]HTTPS-toegang alleen controleren voor een functie-app
- \[Voorbeeld:\]HTTPS-toegang alleen controleren voor een webtoepassing
- \[Voorbeeld:\]HTTPS-toegang alleen controleren voor een API-app
- \[Voorbeeld:\]Ontbrekende blobversleuteling controleren voor opslagaccounts
- \[Voorbeeld:\]VM-extensie implementeren om Windows VM te controleren, mag geen wachtwoorden opslaan met omkeerbare versleuteling
- \[Preview:\]Controle Windows VM mag wachtwoorden niet opslaan met omkeerbare versleuteling
- \[Voorbeeld:\]Onversleutelde VM-schijven controleren in Azure Security Center
- Audit enablement of encryption of Automation account variables Audit enablement of encryption of Automation account variables Audit enablement of encryption of Automation account variables Audit enable
- Controleren of alleen beveiligde verbindingen met uw Redis-cache kunnen worden beveiligd
- Veilige overdracht controleren naar opslagaccounts
- Controleer de instelling van de eigenschap ClusterProtectionLevel op EncryptAndSign in Service Fabric
- Controlestatus van transparante gegevensversleuteling
- Transparante gegevensversleuteling in SQL-databases moet worden ingeschakeld

## <a name="a1241-event-logging"></a>Logboekregistratie van a.12.4.1-gebeurtenissen

Met deze blauwdruk u ervoor zorgen dat systeemgebeurtenissen worden geregistreerd door zeven [Azure Policy-definities](../../../policy/overview.md) toe te geven die de instellingen voor logboeken op Azure-bronnen controleren.
Diagnostische logboeken bieden inzicht in bewerkingen die zijn uitgevoerd binnen Azure-resources.

- \[Voorbeeld:\]Implementatie van de controleafhankelijkheidsagent - VM-afbeelding (OS) niet-vermeld
- \[Voorbeeld:\]Implementatie van de afhankelijkheidsagent controleren in VMSS - VM Image (OS) niet-vermeld
- \[Preview:\]Audit Log Analytics Agent Deployment - VM Image (OS) niet vermeld
- \[Preview:\]Implementatie van auditloganalyseagent in VMSS - VM Image (OS) niet vermeld
- Diagnostische instelling voor controleren
- Controle-instellingen op SQL-serverniveau
- Controle moet worden ingeschakeld op geavanceerde instellingen voor gegevensbeveiliging op SQL Server

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 Logboeken van beheerders en operatoren

Met deze blauwdruk u ervoor zorgen dat systeemgebeurtenissen worden geregistreerd door zeven Azure Policy-definities toe te geven die de instellingen voor logboeken op Azure-bronnen controleren. Diagnostische logboeken bieden inzicht in bewerkingen die zijn uitgevoerd binnen Azure-resources.

- \[Voorbeeld:\]Implementatie van de controleafhankelijkheidsagent - VM-afbeelding (OS) niet-vermeld
- \[Voorbeeld:\]Implementatie van de afhankelijkheidsagent controleren in VMSS - VM Image (OS) niet-vermeld
- \[Preview:\]Audit Log Analytics Agent Deployment - VM Image (OS) niet vermeld
- \[Preview:\]Implementatie van auditloganalyseagent in VMSS - VM Image (OS) niet vermeld
- Diagnostische instelling voor controleren
- Controle-instellingen op SQL-serverniveau
- Controle moet worden ingeschakeld op geavanceerde instellingen voor gegevensbeveiliging op SQL Server

## <a name="a1244-clock-synchronization"></a>A.12.4.4 Kloksynchronisatie

Met deze blauwdruk u ervoor zorgen dat systeemgebeurtenissen worden geregistreerd door zeven Azure Policy-definities toe te geven die de instellingen voor logboeken op Azure-bronnen controleren. Azure-logboeken zijn afhankelijk van gesynchroniseerde interne klokken om een tijdgecorreleerde record van gebeurtenissen tussen resources te maken.

- \[Voorbeeld:\]Implementatie van de controleafhankelijkheidsagent - VM-afbeelding (OS) niet-vermeld
- \[Voorbeeld:\]Implementatie van de afhankelijkheidsagent controleren in VMSS - VM Image (OS) niet-vermeld
- \[Preview:\]Audit Log Analytics Agent Deployment - VM Image (OS) niet vermeld
- \[Preview:\]Implementatie van auditloganalyseagent in VMSS - VM Image (OS) niet vermeld
- Diagnostische instelling voor controleren
- Controle-instellingen op SQL-serverniveau
- Controle moet worden ingeschakeld op geavanceerde instellingen voor gegevensbeveiliging op SQL Server

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Installatie van software op operationele systemen

Adaptief toepassingsbeheer is een oplossing van Azure Security Center waarmee u bepalen welke toepassingen kunnen worden uitgevoerd op uw VM's in Azure. In deze blauwdruk wordt een Azure-beleidsdefinitie toegestaan die wijzigingen in de set toegestane toepassingen bewaakt. Met deze mogelijkheid u de installatie van software en toepassingen op Azure VM's beheren.

- \[Voorbeeld:\]Mogelijke whitelisting van apps controleren in Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 Beheer van technische kwetsbaarheden

Met deze blauwdruk u kwetsbaarheden in het informatiesysteem beheren door vijf [Azure Policy-definities](../../../policy/overview.md) toe te stellen die ontbrekende systeemupdates, kwetsbaarheden in het besturingssysteem, SQL-kwetsbaarheden en kwetsbaarheden in virtuele machines in Azure Security Center controleren. Azure Security Center biedt rapportagemogelijkheden waarmee u realtime inzicht hebt in de beveiligingsstatus van geïmplementeerde Azure-resources.

- \[Voorbeeld:\]Ontbrekende endpointbeveiliging controleren in Azure Security Center
- \[Voorbeeld:\]Ontbrekende systeemupdates controleren in Azure Security Center
- \[Preview:\]Kwetsbaarheden in besturingssysteem in Azure Security Center controleren
- \[Preview:\]Resultaten van sql-kwetsbaarheidsbeoordeling in Azure Security Center controleren
- \[Preview:\]VM-kwetsbaarheden in Azure Security Center controleren

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Beperkingen op software-installatie

Adaptief toepassingsbeheer is een oplossing van Azure Security Center waarmee u bepalen welke toepassingen kunnen worden uitgevoerd op uw VM's in Azure. In deze blauwdruk wordt een Azure-beleidsdefinitie toegestaan die wijzigingen in de set toegestane toepassingen bewaakt. Beperkingen op software-installatie kunnen u helpen de kans op het introduceren van softwarekwetsbaarheden te verkleinen.

- \[Voorbeeld:\]Mogelijke whitelisting van apps controleren in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 Netwerkbesturingselementen

Met deze blauwdruk u netwerken beheren en beheren door een [Azure Policy-definitie](../../../policy/overview.md) toe te stellen die netwerkbeveiligingsgroepen met tolerante regels bewaakt. Regels die te tolerant zijn, kunnen onbedoelde netwerktoegang toestaan en moeten worden herzien. In deze blauwdruk worden ook drie Azure-beleidsdefinities opgenomen die onbeveiligde eindpunten, toepassingen en opslagaccounts controleren. Eindpunten en toepassingen die niet worden beschermd door een firewall en opslagaccounts met onbeperkte toegang kunnen onbedoelde toegang geven tot informatie in het informatiesysteem.

- \[Voorbeeld:\]Monitor permissive netwerktoegang in Azure Security Center
- \[Voorbeeld:\]Onbeveiligde netwerkeindpunten controleren in Azure Security Center
- \[Voorbeeld:\]Onbeveiligde webtoepassing controleren in Azure Security Center
- Onbeperkte netwerktoegang tot opslagaccounts controleren

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Beleid en procedures voor informatieoverdracht

Met de blauwdruk u ervoor zorgen dat de overdracht van informatie met Azure-services wordt beveiligd door twee [Azure Policy-definities](../../../policy/overview.md) toe te staan om onveilige verbindingen met opslagaccounts en Redis-cache te controleren.

- Controleren of alleen beveiligde verbindingen met uw Redis-cache kunnen worden beveiligd
- Veilige overdracht controleren naar opslagaccounts

## <a name="next-steps"></a>Volgende stappen

Nu u de beheerkaart van de ISO 27001-blauwdruk hebt bekeken, gaat u naar de volgende artikelen voor meer informatie over de architectuur en hoe u dit voorbeeld implementeert:

> [!div class="nextstepaction"]
> [ISO 27001 blauwdruk - Overzicht](./index.md)
> [ISO 27001 blauwdruk - Stappen implementeren](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van de blauwdruk](../../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../../concepts/resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).