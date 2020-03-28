---
title: BEsturingselementen voor pci-DSS v3.2.1-blauwblauwen
description: Beheer de toewijzing van het voorbeeld van de betaalkaartindustriegegevensbeveiligingsstandaard v3.2.1 aan Azure Policy en RBAC.
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: db21ac9d628e777b6ff2cc86516cfb1497f5a62f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76905647"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Beheer van het pci-DSS v3.2.1-blauwdrukvoorbeeld

In het volgende artikel wordt beschreven hoe de Azure Blueprints PCI-DSS v3.2.1-blauwdrukvoorbeeld wordt toegewezen aan de BEsturingselementen voor PCI-DSS v3.2.1. Zie [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf)voor meer informatie over de besturingselementen.

De volgende toewijzingen zijn naar de **BEsturingselementen voor PCI-DSS v3.2.1:2018.** Gebruik de navigatie aan de rechterkant om direct naar een specifieke besturingstoewijzing te springen. Veel van de toegewezen besturingselementen worden geïmplementeerd met een [Azure Policy-initiatief.](../../../policy/overview.md) Als u het volledige initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities.** Zoek en selecteer vervolgens de ** \[besturingselementen Preview\] Audit PCI v3.2.1:2018 en implementeer specifieke VM-extensies ter ondersteuning van ingebouwde** beleidsinitiatieven voor auditvereisten.

> [!IMPORTANT]
> Elk besturingselement hieronder is gekoppeld aan een of meer [Azure Policy-definities.](../../../policy/overview.md) Met dit beleid u [beoordelen of de](../../../policy/how-to/get-compliance-data.md) controle is nageleefd; Er is echter vaak geen 1:1 of volledige overeenkomst tussen een besturingselement en een of meer beleidsregels. **Compliant** in Azure Policy verwijst daarom alleen naar het beleid zelf. Dit zorgt er niet voor dat u volledig voldoet aan alle vereisten van een controle. Bovendien bevat de nalevingsstandaard besturingselementen die op dit moment niet worden behandeld door azure-beleidsdefinities. Naleving in Azure Policy is daarom slechts een gedeeltelijke weergave van uw algemene nalevingsstatus. De koppelingen tussen besturingselementen en Azure Policy-definities voor dit voorbeeld van nalevingsblauwdrukken kunnen in de loop van de tijd veranderen. Zie de [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md)om de wijzigingsgeschiedenis te bekijken.

## <a name="132-and-134-boundary-protection"></a>1.3.2 en 1.3.4 Grensbeveiliging

Met deze blauwdruk u netwerken beheren en beheren door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen die netwerkbeveiligingsgroepen met tolerante regels controleren. Regels die te tolerant zijn, kunnen onbedoelde netwerktoegang toestaan en moeten worden herzien. In deze blauwdruk worden één Azure-beleidsdefinities toegevallen die onbeveiligde eindpunten, toepassingen en opslagaccounts bewaken. Eindpunten en toepassingen die niet worden beschermd door een firewall en opslagaccounts met onbeperkte toegang kunnen onbedoelde toegang geven tot informatie in het informatiesysteem.

- Onbeperkte netwerktoegang tot opslagaccounts controleren
- Toegang via het eindpunt van internet moet worden beperkt

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h en 6.5.3 Cryptografische bescherming

Met deze blauwdruk u uw beleid afdwingen met het gebruik van cryptograph-besturingselementen door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen die specifieke cryptograafbesturingselementen afdwingen en het gebruik van zwakke cryptografische instellingen controleren. Als u begrijpt waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, u corrigerende maatregelen nemen om ervoor te zorgen dat resources zijn geconfigureerd in overeenstemming met uw informatiebeveiligingsbeleid. In het bijzonder vereist het beleid dat door deze blauwdruk is toegewezen, transparante gegevensversleuteling op SQL-databases; controleren ontbrekende versleuteling op opslagaccounts en automatiseringsaccountvariabelen. Er zijn ook beleidsregels die controleonveilige verbindingen met opslagaccounts, functie-apps, WebApp,API-apps en Redis-cache aanpakken en onversleutelde Service Fabric-communicatie controleren.

- Functie-app mag alleen toegankelijk zijn via HTTPS
- Webtoepassing mag alleen toegankelijk zijn via HTTPS
- API-app mag alleen toegankelijk zijn via HTTPS
- Transparante gegevensversleuteling in SQL-databases moet worden ingeschakeld
- Schijfversleuteling moet worden toegepast op virtuele machines
- Variabelen van automatiseringsaccount moeten worden versleuteld
- Alleen beveiligde verbindingen met uw Redis-cache moeten worden ingeschakeld
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Clusterfabricclusters moeten de eigenschap ClusterProtectionLevel hebben ingesteld op EncryptAndSign
- Transparante gegevensversleuteling in SQL-databases moet worden ingeschakeld
- SQL DB-transparante gegevensversleuteling implementeren

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 en 11.2.1 Kwetsbaarheid scannen en systeemupdates

Met deze blauwdruk u kwetsbaarheden in het informatiesysteem beheren door [Azure Policy-definities](../../../policy/overview.md) toe te stellen die ontbrekende systeemupdates, kwetsbaarheden in het besturingssysteem, SQL-kwetsbaarheden en kwetsbaarheden in virtuele machines in Azure Security Center controleren. Azure Security Center biedt rapportagemogelijkheden waarmee u realtime inzicht hebt in de beveiligingsstatus van geïmplementeerde Azure-resources.

- Ontbrekende endpointbeveiliging controleren in Azure Security Center
- Standaard Microsoft IaaSAntimalware-extensie implementeren voor Windows Server
- Bedreigingsdetectie implementeren op SQL-servers
- Er moeten systeemupdates op uw computers zijn geïnstalleerd
- Kwetsbaarheden in beveiligingsconfiguratie op uw machines moeten worden verholpen
- Kwetsbaarheden in uw SQL-databases moeten worden verholpen
- Kwetsbaarheden moeten worden verholpen door een oplossing voor kwetsbaarheidsbeoordeling

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 en 7.1.3 Scheiding van rechten

Als u slechts één Azure-abonnementseigenaar hebt, is er geen beheerredundantie mogelijk. Omgekeerd kan het hebben van te veel Azure-abonnementseigenaren het potentieel voor een inbreuk vergroten via een gecompromitteerd eigenaaraccount. Met deze blauwdruk u een passend aantal Azure-abonnementseigenaren behouden door [Azure-beleidsdefinities](../../../policy/overview.md) toe te wijsen die het aantal eigenaren voor Azure-abonnementen controleren. Het beheren van machtigingen voor abonnementseigenaren kan u helpen bij het implementeren van de juiste scheiding van taken.

- Er moeten meer dan één eigenaar aan uw abonnement zijn toegewezen
- Er moeten maximaal 3 eigenaren worden aangewezen voor uw abonnement 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a en 8.3.1.b Beheer van bevoorrechte toegangsrechten

Met deze blauwdruk u bevoorrechte toegangsrechten beperken en beheren door [Azure Policy-definities](../../../policy/overview.md) toe te wijs en toe te wijs externe accounts met eigenaar, schrijf- en/of leesmachtigingen en werknemersaccounts toe met eigenaar- en/of schrijfmachtigingen die geen multi-factor authenticatie hebben ingeschakeld. Azure implementeert rbac (role-based access control) om te beheren wie toegang heeft tot Azure-bronnen. Als u begrijpt waar aangepaste RBAC-regels worden geïmplementeerd, u de noodzaak en de juiste implementatie controleren, omdat aangepaste RBAC-regels foutgevoelig zijn. In deze blauwdruk worden ook [Azure Policy-definities](../../../policy/overview.md) toebedeeld om het gebruik van Azure Active Directory-verificatie voor SQL-servers te controleren. Het gebruik van Azure Active Directory-verificatie vereenvoudigt het beheer van machtigingen en centraliseert het identiteitsbeheer van databasegebruikers en andere Microsoft  
cloudservices moet worden gebruikt.
 
- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met schrijfmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met leesmachtigingen moeten uit uw abonnement worden verwijderd
- MFA moet zijn ingeschakeld voor accounts met eigenaarmachtigingen voor uw abonnement
- MFA moet accounts zijn ingeschakeld met schrijfmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement
- Een Azure Active Directory-beheerder moet zijn ingericht voor SQL-servers
- Controle gebruik van aangepaste RBAC-regels

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 en 8.1.5 Minste bevoegdheden en beoordeling van toegangsrechten voor gebruikers

Azure implementeert op rollen gebaseerd toegangscontrolebeheer (RBAC) om te beheren wie toegang heeft tot bronnen in Azure. Met behulp van de Azure-portal u controleren wie toegang heeft tot Azure-bronnen en hun machtigingen. In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toegekend aan controleaccounts die moeten worden geprioriteerd voor controle, inclusief afgeschreven accounts en externe accounts met verhoogde machtigingen.

- Afgeschafte accounts moeten uit uw abonnement worden verwijderd
- Afgeschafte accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met schrijfmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met leesmachtigingen moeten uit uw abonnement worden verwijderd

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 Verwijdering of aanpassing van toegangsrechten

Azure implementeert op rollen gebaseerd toegangscontrolebeheer (RBAC) om u te helpen beheren wie toegang heeft tot bronnen in Azure. Met Azure Active Directory en RBAC u gebruikersrollen bijwerken om organisatorische wijzigingen weer te geven. Indien nodig kunnen accounts worden geblokkeerd voor het aanmelden (of verwijderd), waardoor de toegangsrechten tot Azure-bronnen onmiddellijk worden verwijderd. In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toegekend voor het controleren van afgeschreven account dat moet worden overwogen voor verwijdering.

- Afgeschafte accounts moeten uit uw abonnement worden verwijderd
- Afgeschafte accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b en 8.2.5 Verificatie op basis van wachtwoord

Met deze blauwdruk u sterke wachtwoorden afdwingen door [Azure Policy-definities](../../../policy/overview.md) toe te kennen die Windows VM's controleren die geen minimale sterkte en andere wachtwoordvereisten afdwingen. Door u te verzekeren van VM's in strijd met het wachtwoordsterktebeleid, u corrigerende maatregelen nemen om ervoor te zorgen dat wachtwoorden voor alle VM-gebruikersaccounts voldoen aan het beleid.

- \[Voorbeeld:\]Windows VM's controleren die geen maximale wachtwoordleeftijd van 70 dagen hebben
- \[Voorbeeld:\]Vereisten implementeren om Windows VM's te controleren die geen maximale wachtwoordleeftijd van 70 dagen hebben
- \[Voorbeeld:\]Windows VM's controleren die de minimale wachtwoordlengte niet beperken tot 14 tekens
- \[Voorbeeld:\]Vereisten implementeren om Windows VM's te controleren die de minimale wachtwoordlengte niet beperken tot 14 tekens
- \[Voorbeeld:\]Windows VM's controleren waarmee de vorige 24 wachtwoorden opnieuw kunnen worden gebruikt
- \[Voorbeeld:\]Vereisten implementeren om Windows VM's te controleren waarmee de vorige 24 wachtwoorden opnieuw kunnen worden gebruikt

## <a name="103-and-1054-audit-generation"></a>10.3 en 10.5.4 Audit generatie

Met deze blauwdruk u ervoor zorgen dat systeemgebeurtenissen worden geregistreerd door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen die de instellingen van het logboek controleren op Azure-bronnen.
Diagnostische logboeken bieden inzicht in bewerkingen die zijn uitgevoerd binnen Azure-resources. Azure-logboeken zijn afhankelijk van gesynchroniseerde interne klokken om een tijdgecorreleerde record van gebeurtenissen tussen resources te maken.

- Controle moet worden ingeschakeld op geavanceerde instellingen voor gegevensbeveiliging op SQL Server
- Diagnostische instelling voor controleren
- Controle-instellingen op SQL-serverniveau
- Auditing implementeren op SQL-servers
- Opslagaccounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-bronnen
- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-bronnen

## <a name="1236-and-1237-information-security"></a>12.3.6 en 12.3.7 Informatiebeveiliging

Met deze blauwdruk u uw netwerk beheren en beheren door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen die de aanvaardbare netwerklocaties en de goedgekeurde bedrijfsproducten die voor de omgeving zijn toegestaan, controleren. Deze zijn aanpasbaar door elk bedrijf via de beleidsparameters binnen elk van deze beleidsregels.

- Toegestane locaties
- Toegestane locaties voor resourcegroepen

## <a name="next-steps"></a>Volgende stappen

Nu u de besturingstoewijzing van de PCI-DSS v3.2.1-blauwdruk hebt bekeken, gaat u naar de volgende artikelen voor meer informatie over het overzicht en hoe u dit voorbeeld implementeert:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1-blauwdruk - Overzicht](./index.md)
> [PCI-DSS v3.2.1-blauwdruk - Stappen implementeren](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van de blauwdruk](../../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../../concepts/resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).