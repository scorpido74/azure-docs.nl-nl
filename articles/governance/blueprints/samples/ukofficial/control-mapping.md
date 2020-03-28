---
title: Britse officiële & UK NHS blauwdruk steekproef controles
description: Controle mapping van de Britse officiële en Britse NHS blauwdruk monsters. Elk besturingselement wordt toegewezen aan een of meer Azure-beleidsregels die helpen bij de beoordeling.
ms.date: 12/04/2019
ms.topic: sample
ms.openlocfilehash: 5bef590013a9ef06b791e58dc6c82e74dffe1a17
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74851363"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Controle mapping van de Britse officiële en Britse NHS blauwdruk monsters

Het volgende artikel beschrijft hoe de Britse officiële en Britse NHS blauwdruk monsters kaart aan het Verenigd Koninkrijk officiële en Britse NHS controles. Zie [UK OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications)voor meer informatie over de controles.

De volgende mappings zijn aan de **Britse officiële** en **Britse NHS** controles. Gebruik de navigatie aan de rechterkant om direct naar een specifieke besturingstoewijzing te springen. Veel van de toegewezen besturingselementen worden geïmplementeerd met een [Azure Policy-initiatief.](../../../policy/overview.md) Als u het volledige initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities.** Zoek en selecteer vervolgens de ** \[BEsturingselementen voor preview-audit\] van de VS en de NHS in het Verenigd Koninkrijk en implementeer vervolgens specifieke VM-extensies ter ondersteuning van ingebouwde beleidsvereisten** voor auditvereisten.

> [!IMPORTANT]
> Elk besturingselement hieronder is gekoppeld aan een of meer [Azure Policy-definities.](../../../policy/overview.md) Met dit beleid u [beoordelen of de](../../../policy/how-to/get-compliance-data.md) controle is nageleefd; Er is echter vaak geen 1:1 of volledige overeenkomst tussen een besturingselement en een of meer beleidsregels. **Compliant** in Azure Policy verwijst daarom alleen naar het beleid zelf. Dit zorgt er niet voor dat u volledig voldoet aan alle vereisten van een controle. Bovendien bevat de nalevingsstandaard besturingselementen die op dit moment niet worden behandeld door azure-beleidsdefinities. Naleving in Azure Policy is daarom slechts een gedeeltelijke weergave van uw algemene nalevingsstatus. De koppelingen tussen besturingselementen en Azure Policy-definities voor dit voorbeeld van nalevingsblauwdrukken kunnen in de loop van de tijd veranderen. Zie de [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md)om de wijzigingsgeschiedenis te bekijken.

## <a name="1-data-in-transit-protection"></a>1 Gegevens in de bescherming van het douanevervoer

Met de blauwdruk u ervoor zorgen dat de overdracht van informatie met Azure-services wordt beveiligd door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen die onveilige verbindingen met opslagaccounts en Redis-cache controleren.

- Alleen beveiligde verbindingen met uw Redis-cache moeten worden ingeschakeld
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Controleresultaten weergeven van Windows-webservers die geen beveiligde communicatieprotocollen gebruiken
- Vereisten implementeren om Windows-webservers te controleren die geen beveiligde communicatieprotocollen gebruiken
- Nieuwste TLS-versie moet worden gebruikt in uw API-app
- Nieuwste TLS-versie moet worden gebruikt in uw web-app
- Nieuwste TLS-versie moet worden gebruikt in uw functie-app

## <a name="23-data-at-rest-protection"></a>2.3 Bescherming gegevens in rust

Met deze blauwdruk u uw beleid inzake het gebruik van cryptograafbesturingselementen afdwingen door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen die specifieke cryptografiebesturingselementen afdwingen en het gebruik van zwakke cryptografische instellingen te controleren.
Als u begrijpt waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, u corrigerende maatregelen nemen om ervoor te zorgen dat resources zijn geconfigureerd in overeenstemming met uw informatiebeveiligingsbeleid. In het bijzonder vereist het beleid dat door deze blauwdruk is toegewezen versleuteling voor opslagaccounts in gegevensmeer; transparante gegevensversleuteling in SQL-databases vereisen; ontbrekende versleuteling op opslagaccounts, SQL-databases, virtuele machineschijven en automatiseringsaccountvariabelen te controleren; onveilige verbindingen met opslagaccounts en Redis-cache te controleren; zwakke wachtwoordversleuteling voor virtuele machines te controleren; en controleer onversleutelde Service Fabric-communicatie.

- Schijfversleuteling moet worden toegepast op virtuele machines
- Variabelen van automatiseringsaccount moeten worden versleuteld
- Clusterfabricclusters moeten de eigenschap ClusterProtectionLevel hebben ingesteld op EncryptAndSign
- Transparante gegevensversleuteling in SQL-databases moet worden ingeschakeld
- SQL DB-transparante gegevensversleuteling implementeren
- Versleuteling vereisen voor Data Lake Store-accounts
- Toegestane locaties (is hard gecodeerd naar "UK SOUTH" en "UK WEST")
- Toegestane locaties voor resourcegroepen (is hard gecodeerd naar "UK SOUTH" en "UK WEST")

## <a name="52-vulnerability-management"></a>5.2 Beheer van kwetsbaarheden

Met deze blauwdruk u kwetsbaarheden in het informatiesysteem beheren door [Azure Policy-definities](../../../policy/overview.md) toe te wijzen die ontbrekende endpoint-beveiliging, ontbrekende systeemupdates, kwetsbaarheden in het besturingssysteem, SQL-kwetsbaarheden en kwetsbaarheden in virtuele machines controleren. Deze inzichten bieden realtime informatie over de beveiligingsstatus van uw geïmplementeerde resources en kunnen u helpen bij het prioriteren van herstelacties.

- Ontbrekende endpointbeveiliging controleren in Azure Security Center
- Er moeten systeemupdates op uw computers zijn geïnstalleerd
- Systeemupdates op virtuele machineschaalsets moeten worden geïnstalleerd
- Kwetsbaarheden in beveiligingsconfiguratie op uw machines moeten worden verholpen
- Kwetsbaarheden in uw SQL-databases moeten worden verholpen
- Kwetsbaarheden moeten worden verholpen door een oplossing voor kwetsbaarheidsbeoordeling
- Kwetsbaarheidsbeoordeling moet worden ingeschakeld op uw SQL-servers
- Kwetsbaarheidsbeoordeling moet worden ingeschakeld op uw SQL-beheerde exemplaren
- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machineschaalsets moeten worden verholpen
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-beheerde exemplaren
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-servers

## <a name="53-protective-monitoring"></a>5.3 Beschermende controle

Met deze blauwdruk u de elementen van het informatiesysteem beschermen door [Azure Policy-definities](../../../policy/overview.md) toe te stellen die beschermende controle bieden over onbeperkte toegang, lijstactiviteit en bedreigingen toestaan.

- Onbeperkte netwerktoegang tot opslagaccounts controleren
- Adaptieve toepassingsbesturingselementen moeten worden ingeschakeld op virtuele machines
- Virtuele machines controleren zonder herstel van noodgevallen geconfigureerd
- DDoS Protection Standard moet ingeschakeld zijn
- Geavanceerde typen bedreigingsbeveiliging moeten worden ingesteld op 'Alles' in SQL-beheerde instantie Advanced Data Security-instellingen
- Geavanceerde typen bedreigingsbeveiliging moeten worden ingesteld op 'Alles' in sql-server geavanceerde gegevensbeveiligingsinstellingen
- Bedreigingsdetectie implementeren op SQL-servers
- Standaard Microsoft IaaSAntimalware-extensie implementeren voor Windows Server

## <a name="9-secure-user-management"></a>9 Veilig gebruikersbeheer 

Azure implementeert op rollen gebaseerd toegangscontrolebeheer (RBAC) om te beheren wie toegang heeft tot bronnen in Azure. Met behulp van de Azure-portal u controleren wie toegang heeft tot Azure-bronnen en hun machtigingen. Met deze blauwdruk u toegangsrechten beperken en beheren door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen voor het controleren van externe accounts met eigenaar- en/of lees-/schrijfmachtigingen en -accounts met eigenaar, lees- en/of schrijfmachtigingen die geen multi-factor authenticatie hebben ingeschakeld.

- MFA moet zijn ingeschakeld voor accounts met eigenaarmachtigingen voor uw abonnement
- MFA moet accounts zijn ingeschakeld met schrijfmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement
- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met schrijfmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met leesmachtigingen moeten uit uw abonnement worden verwijderd

## <a name="10-identity-and-authentication"></a>10 Identiteit en verificatie

Met deze blauwdruk u toegangsrechten beperken en beheren door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen voor het controleren van externe accounts met eigenaar- en/of lees-/schrijfmachtigingen en -accounts met eigenaar, lees- en/of schrijfmachtigingen die geen multi-factor authenticatie hebben ingeschakeld.

- MFA moet zijn ingeschakeld voor accounts met eigenaarmachtigingen voor uw abonnement
- MFA moet accounts zijn ingeschakeld met schrijfmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement
- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met schrijfmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met leesmachtigingen moeten uit uw abonnement worden verwijderd

In deze blauwdruk worden Azure Policy-definities toebedeeld om het gebruik van Azure Active Directory-verificatie voor SQL-servers en Service Fabric te controleren. Met Azure Active Directory-verificatie u vereenvoudigd machtigingsbeheer en gecentraliseerd identiteitsbeheer van databasegebruikers en andere Microsoft-services gebruiken.

- Een Azure Active Directory-beheerder moet zijn ingericht voor SQL-servers
- Servicesfabricclusters mogen alleen Azure Active Directory gebruiken voor clientverificatie

In deze blauwdruk worden ook Azure Policy-definities toegekend aan controleaccounts die moeten worden geprioriteerd voor controle, waaronder afgeschreven accounts en externe accounts. Indien nodig kunnen accounts worden geblokkeerd voor het aanmelden (of verwijderd), waardoor de toegangsrechten tot Azure-bronnen onmiddellijk worden verwijderd. In deze blauwdruk worden twee Azure Policy-definities toegekend voor het controleren van afgeschreven account dat moet worden overwogen voor verwijdering.

- Afgeschafte accounts moeten uit uw abonnement worden verwijderd
- Afgeschafte accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met schrijfmachtigingen moeten uit uw abonnement worden verwijderd

In deze blauwdruk wordt ook een Azure-beleidsdefinitie opgenomen die de machtigingen voor Linux VM-wachtwoordbestanden controleert om te waarschuwen als deze onjuist zijn ingesteld. Dit ontwerp stelt u in staat om corrigerende maatregelen te nemen om ervoor te zorgen dat authenticators niet worden aangetast.

- \[Voorbeeld:\]Controleresultaten van Linux VM's weergeven die niet zijn ingesteld op 0644

Met deze blauwdruk u sterke wachtwoorden afdwingen door Azure Policy-definities toe te kennen die Windows VM's controleren die geen minimale sterkte en andere wachtwoordvereisten afdwingen. Door u te verzekeren van VM's in strijd met het wachtwoordsterktebeleid, u corrigerende maatregelen nemen om ervoor te zorgen dat wachtwoorden voor alle VM-gebruikersaccounts voldoen aan het beleid.

- \[Voorbeeld:\]Vereiste vereisten implementeren om Windows VM's te controleren die niet zijn ingeschakeld voor de instelling voor de complexiteit van wachtwoorden
- \[Voorbeeld:\]Vereisten implementeren om Windows VM's te controleren die geen maximale wachtwoordleeftijd van 70 dagen hebben
- \[Voorbeeld:\]Vereisten implementeren om Windows VM's te controleren die geen minimumwachtwoordleeftijd van 1 dag hebben
- \[Voorbeeld:\]Vereiste vereisten implementeren om Windows VM's te controleren die de minimale wachtwoordlengte niet beperken tot 14 tekens
- \[Voorbeeld:\]Vereisten implementeren om Windows VM's te controleren waarmee de vorige 24 wachtwoorden opnieuw kunnen worden gebruikt
- \[Voorbeeld:\]controleresultaten van Windows VM's weergeven waarop de instelling voor complexiteit van wachtwoorden niet is ingeschakeld
- \[Voorbeeld:\]Controleresultaten van Windows VM's weergeven die geen maximale wachtwoordleeftijd van 70 dagen hebben
- \[Voorbeeld:\]Controleresultaten van Windows VM's weergeven die geen minimumwachtwoordleeftijd van 1 dag hebben
- \[Voorbeeld:\]controleresultaten van Windows VM's weergeven die de minimale wachtwoordlengte niet beperken tot 14 tekens
- \[Voorbeeld:\]Controleresultaten van Windows VM's weergeven waarmee de vorige 24 wachtwoorden opnieuw kunnen worden gebruikt

Met deze blauwdruk u ook de toegang tot Azure-bronnen beheren door Azure-beleidsdefinities toe te wijst. Deze beleidsregels controleren het gebruik van resourcetypen en -configuraties die mogelijk meer tolerante toegang tot resources mogelijk maken. Als u inzicht hebt in resources die in strijd zijn met dit beleid, u corrigerende maatregelen nemen om ervoor te zorgen dat de toegang tot Azure-bronnen beperkt is tot geautoriseerde gebruikers.

- \[Preview:\]Vereisten implementeren om Linux VM's met accounts zonder wachtwoorden te controleren
- \[Preview:\]Implementatievereisten voor het controleren van Linux VM's die externe verbindingen van accounts zonder wachtwoorden mogelijk maken
- \[Preview\]: Linux VM's controleren die accounts hebben zonder wachtwoorden
- \[Preview:\]Linux VM's controleren die externe verbindingen van accounts zonder wachtwoorden mogelijk maken
- Opslagaccounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-bronnen
- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-bronnen
- VM's controleren die geen beheerde schijven gebruiken

## <a name="11-external-interface-protection"></a>11 Beveiliging van externe interface

Anders dan het gebruik van meer dan 25 beleidsregels voor geschikt beveiligd gebruikersbeheer, helpt deze blauwdruk u serviceinterfaces te beschermen tegen ongeautoriseerde toegang door een [Azure Policy-definitie](../../../policy/overview.md) toe te kennen die onbeperkte opslagaccounts bewaakt. Opslagaccounts met onbeperkte toegang kunnen onbedoelde toegang geven tot informatie in het informatiesysteem. Deze blauwdruk wijst ook een beleid toe dat adaptieve toepassingsbesturingselementen op virtuele machines mogelijk maakt.

- Onbeperkte netwerktoegang tot opslagaccounts controleren
- Adaptieve toepassingsbesturingselementen moeten worden ingeschakeld op virtuele machines
- De NSGs-regels voor webapplicaties op IaaS moeten worden verhard
- Toegang via het eindpunt van internet moet worden beperkt
- Netwerkbeveiligingsgroepregels voor internet waarmee virtuele machines worden geconfronteerd, moeten worden verhard
- Endpoint-beveiligingsoplossing moet worden geïnstalleerd op virtuele machineschaalsets
- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines
- Onbeperkte netwerktoegang tot opslagaccounts controleren
- Externe foutopsporing moet worden uitgeschakeld voor functie-app
- Externe foutopsporing moet worden uitgeschakeld voor webtoepassing
- Externe foutopsporing moet worden uitgeschakeld voor API-app
- Webtoepassing mag alleen toegankelijk zijn via HTTPS
- Functie-app mag alleen toegankelijk zijn via HTTPS
- API-app mag alleen toegankelijk zijn via HTTPS

## <a name="12-secure-service-administration"></a>12 Beveiligde servicebeheer

Azure implementeert op rollen gebaseerd toegangscontrolebeheer (RBAC) om te beheren wie toegang heeft tot bronnen in Azure. Met behulp van de Azure-portal u controleren wie toegang heeft tot Azure-bronnen en hun machtigingen. Met deze blauwdruk u bevoorrechte toegangsrechten beperken en beheren door vijf [Azure](../../../policy/overview.md) Policy-definities toe te stellen om externe accounts te controleren met eigenaar en/of machtigingen en accounts te schrijven met de eigenaar en/of machtigingen te schrijven die geen multi-factor-authenticatie hebben ingeschakeld.

Systemen die worden gebruikt voor het beheer van een cloudservice, hebben een zeer bevoorrechte toegang tot die service. Hun compromis zou aanzienlijke gevolgen hebben, waaronder de middelen om beveiligingscontroles te omzeilen en grote hoeveelheden gegevens te stelen of te manipuleren. De methoden die door de beheerders van de dienstverlener worden gebruikt om de operationele service te beheren, moeten zodanig zijn ontworpen dat elk risico op uitbuiting dat de veiligheid van de service zou kunnen ondermijnen, wordt beperkt. Als dit principe niet wordt geïmplementeerd, kan een aanvaller de middelen hebben om beveiligingscontroles te omzeilen en grote hoeveelheden gegevens te stelen of te manipuleren.

- MFA moet zijn ingeschakeld voor accounts met eigenaarmachtigingen voor uw abonnement
- MFA moet accounts zijn ingeschakeld met schrijfmachtigingen voor uw abonnement
- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met schrijfmachtigingen moeten uit uw abonnement worden verwijderd

In deze blauwdruk worden Azure Policy-definities toebedeeld om het gebruik van Azure Active Directory-verificatie voor SQL-servers en Service Fabric te controleren. Met Azure Active Directory-verificatie u vereenvoudigd machtigingsbeheer en gecentraliseerd identiteitsbeheer van databasegebruikers en andere Microsoft-services gebruiken.

- Een Azure Active Directory-beheerder moet zijn ingericht voor SQL-servers
- Servicesfabricclusters mogen alleen Azure Active Directory gebruiken voor clientverificatie

In deze blauwdruk worden ook Azure Policy-definities toegekend aan controleaccounts die moeten worden geprioriteerd voor controle, inclusief afgeschreven accounts en externe accounts met verhoogde machtigingen. Indien nodig kunnen accounts worden geblokkeerd voor het aanmelden (of verwijderd), waardoor de toegangsrechten tot Azure-bronnen onmiddellijk worden verwijderd. In deze blauwdruk worden twee Azure Policy-definities toegekend voor het controleren van afgeschreven account dat moet worden overwogen voor verwijdering.

- Afgeschafte accounts moeten uit uw abonnement worden verwijderd
- Afgeschafte accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met schrijfmachtigingen moeten uit uw abonnement worden verwijderd

In deze blauwdruk wordt ook een Azure-beleidsdefinitie opgenomen die de machtigingen voor Linux VM-wachtwoordbestanden controleert om te waarschuwen als deze onjuist zijn ingesteld. Dit ontwerp stelt u in staat om corrigerende maatregelen te nemen om ervoor te zorgen dat authenticators niet worden aangetast.

- \[Preview\]: Audit Linux VM /etc/passwd bestandsmachtigingen zijn ingesteld op 0644

## <a name="13-audit-information-for-users"></a>13 Controle-informatie voor gebruikers

Met deze blauwdruk u ervoor zorgen dat systeemgebeurtenissen worden geregistreerd door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen die de instellingen van het logboek controleren op Azure-bronnen. Een toegewezen beleid controleert ook als virtuele machines geen logboeken verzenden naar een opgegeven werkruimte voor logboekanalyse.

- Controle moet worden ingeschakeld op geavanceerde instellingen voor gegevensbeveiliging op SQL Server
- Diagnostische instelling voor controleren
- \[Preview:\]Log Analytics Agent implementeren voor Linux VM's
- \[Voorbeeld:\]Logboekanalyseagent implementeren voor Windows VM's
- Netwerkwatcher implementeren wanneer virtuele netwerken worden gemaakt

## <a name="next-steps"></a>Volgende stappen

Nu u de controletoewijzing van de BRITSE OFFICIËLE en Britse NHS-blauwdrukken hebt bekeken, bezoekt u de volgende artikelen om meer te weten te komen over het overzicht en hoe u dit voorbeeld implementeert:

> [!div class="nextstepaction"]
> [Britse officiële en Britse NHS blauwdrukken - Overzicht](./index.md)
> [UK OFFICIËLE en BRITSE NHS blauwdrukken - Deploy stappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van de blauwdruk](../../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../../concepts/resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
