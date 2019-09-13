---
title: Voor beeld-PCI-DSS v 3.2.1 blauw druk-besturings element toewijzen
description: Bepaal de toewijzing van het voor beeld van de betalings kaart branche gegevens beveiliging standaard v 3.2.1-blauw druk op Azure Policy en RBAC.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 2ec299a79f852c553763439290b014a91d3a9414
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918603"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>De toewijzing van het voor beeld van het PCI-DSS v 3.2.1-blauw druk

Het volgende artikel bevat informatie over de manier waarop het Azure-blauw drukken van het PCI-DSS v 3.2.1-voor beeld wordt toegewezen aan de besturings elementen voor PCI-DSS v 3.2.1. Zie [PCI-DSS v 3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf)voor meer informatie over de besturings elementen.

De volgende toewijzingen zijn de **PCI-DSS v 3.2.1:2018** -besturings elementen. Gebruik de navigatie aan de rechter kant om rechtstreeks naar een specifieke besturings element koppeling te gaan. Veel van de toegewezen besturings elementen worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md) -initiatief. Als u het complete initiatief wilt bekijken, opent u **beleid** in het Azure Portal en selecteert u de pagina **definities** . Zoek en selecteer vervolgens de  **\[preview\] -controle PCI v 3.2.1:2018-besturings elementen en implementeer specifieke VM-extensies ter ondersteuning** van de ingebouwde beleids initiatieven voor controle vereisten.

> [!IMPORTANT]
> Elk besturings element hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md) definities. Met deze beleids regels kunt u de naleving van het besturings element [beoordelen](../../../policy/how-to/get-compliance-data.md) . Er is echter vaak geen 1:1-of volledige overeenkomst tussen een besturings element en een of meer beleids regels. Als zodanig is de **naleving** in azure Policy alleen bedoeld voor het beleid zelf. Dit garandeert niet dat u volledig compatibel bent met alle vereisten van een besturings element. Daarnaast bevat de nalevings standaard besturings elementen die niet worden behandeld door Azure Policy definities op dit moment. Daarom is naleving in Azure Policy slechts een gedeeltelijke weer gave van uw algemene nalevings status. De koppelingen tussen de besturings elementen en Azure Policy definities voor dit voor beeld van deze naleving blauw druk kunnen na verloop van tijd veranderen. Als u de wijzigings geschiedenis wilt weer geven, raadpleegt u de [github commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>Grens beveiliging voor 1.3.2 en 1.3.4

Deze blauw druk helpt u bij het beheren en best uren van netwerken door [Azure Policy](../../../policy/overview.md) definities toe te wijzen waarmee netwerk beveiligings groepen met strikte regels worden bewaakt. Het kan zijn dat te beperken regels mogelijk onbedoelde netwerk toegang toestaan en moeten worden gecontroleerd. Deze blauw druk wijst een Azure Policy definities toe waarmee onbeveiligde eind punten, toepassingen en opslag accounts worden bewaakt. Eind punten en toepassingen die niet zijn beveiligd door een firewall en opslag accounts met onbeperkte toegang, kunnen onbedoelde toegang tot gegevens in het informatie systeem toestaan.

- Onbeperkte netwerk toegang tot opslag accounts controleren
- Toegang via Internet gericht eind punt moet worden beperkt

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4. a, 4,1, 4.1. g, 4.1. h en 6.5.3 cryptografische bescherming

Deze blauw druk helpt u bij het afdwingen van uw beleid met het gebruik van cryptograph-besturings elementen door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die specifieke cryptograph-besturings elementen afdwingen en het gebruik van zwakke cryptografische instellingen te controleren. Als u wilt weten waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, kunt u corrigerende maat regelen nemen om ervoor te zorgen dat bronnen worden geconfigureerd in overeenstemming met uw informatie beveiligings beleid. Met name de beleids regels die door deze blauw druk worden toegewezen, vereisen transparante gegevens versleuteling voor SQL-data bases. controleren op ontbrekende versleuteling voor opslag accounts en Automation-account variabelen. Er zijn ook beleids regels voor het controleren van onbeveiligde verbindingen met opslag accounts, functie-apps, WebApp, API Apps en Redis Cache en het controleren van niet-versleutelde Service Fabric communicatie.

- Functie-App moet alleen toegankelijk zijn via HTTPS
- Web-App moet alleen toegankelijk zijn via HTTPS
- De API-app mag alleen toegankelijk zijn via HTTPS
- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld
- Schijf versleuteling moet worden toegepast op virtuele machines
- De variabelen van het Automation-account moeten worden versleuteld
- Alleen beveiligde verbindingen met uw Redis Cache moeten worden ingeschakeld
- Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld
- Voor Service Fabric clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign
- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld
- Transparante gegevens versleuteling van SQL DB implementeren

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5,1, 6,2, 6,6 en 11.2.1 beveiligings problemen met scannen en systeem updates

Deze blauw druk helpt u bij het beheren van beveiligings problemen met informatie systemen door [Azure Policy](../../../policy/overview.md) definities toe te wijzen waarmee ontbrekende systeem updates, besturingssysteem problemen met het besturings systeem, SQL-beveiligings problemen en beveiligings problemen met virtuele machines in Azure worden bewaakt Security Center. Azure Security Center biedt rapportage mogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligings status van geïmplementeerde Azure-resources.

- Ontbrekende Endpoint Protection in Azure Security Center controleren
- Standaard micro soft IaaSAntimalware-extensie voor Windows Server implementeren
- Detectie van bedreigingen op SQL-servers implementeren
- Systeem updates moeten worden geïnstalleerd op uw computers
- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld
- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken

## <a name="711-712-and-713-separation-of-duties"></a>inspectie. 7.1.2 en 7.1.3 schei ding van taken

Als er slechts één eigenaar van een Azure-abonnement is, is er geen administratieve redundantie toegestaan. Als er te veel eigen aars van Azure-abonnementen zijn, kan het mogelijk zijn om een schending te doen van een inbreuk op een eigenaars account. Met deze blauw druk kunt u het juiste aantal eigen aars van Azure-abonnementen onderhouden door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die het aantal eigen aren voor Azure-abonnementen controleren. De machtigingen van eigenaar van het abonnement beheren kan u helpen bij het implementeren van de juiste schei ding van taken.

- Er moet meer dan één eigenaar aan uw abonnement zijn toegewezen
- Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3,2, 7.2.1, 8.3.1. a en 8.3.1. b beheer van privileged Access Rights

Deze blauw druk helpt u om privileged Access Rights te beperken en te beheren door [Azure Policy](../../../policy/overview.md) definities toe te wijzen voor het controleren van externe accounts met de machtigingen eigenaar, schrijven en/of lezen en voor werknemers accounts met eigenaar en/of schrijf machtigingen die geen multi-factor Authentication is ingeschakeld. Azure implementeert op rollen gebaseerd toegangs beheer (RBAC) voor het beheren van de toegang tot Azure-resources. Als u wilt weten waar aangepaste RBAC-regels worden geïmplementeerd, kunt u controleren of de juiste implementatie nodig is, omdat aangepaste RBAC-regels fout gevoelig zijn. Deze blauw druk wijst ook [Azure Policy](../../../policy/overview.md) definities toe om het gebruik van Azure Active Directory-verificatie voor SQL-servers te controleren. Het gebruik van Azure Active Directory verificatie vereenvoudigt het beheer van machtigingen en Centraliseer het identiteits beheer van database gebruikers en andere micro soft  
Onderzoeksservices.
 
- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement
- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement
- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers
- Gebruik van aangepaste RBAC-regels controleren

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>versie 8.1.2-en 8.1.5-minimale bevoegdheid en beoordeling van gebruikers toegangs rechten

Azure implementeert op rollen gebaseerd toegangs beheer (RBAC) om u te helpen bij het beheren van de toegang tot resources in Azure. Met behulp van de Azure Portal kunt u controleren wie toegang heeft tot Azure-resources en de bijbehorende machtigingen. Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe aan de controle van accounts waarvoor een prioriteit moet worden gegeven, waaronder afgeschreven accounts en externe accounts met verhoogde machtigingen.

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 verwijderen of aanpassen van toegangs rechten

Azure implementeert op rollen gebaseerd toegangs beheer (RBAC) om u te helpen bij het beheren van de toegang tot resources in Azure. Met behulp van Azure Active Directory en RBAC kunt u gebruikers rollen bijwerken om de wijzigingen in de organisatie te spie gelen. Zo nodig kunnen accounts worden geblokkeerd om zich aan te melden (of verwijderd), waardoor de toegangs rechten voor Azure-bronnen onmiddellijk worden verwijderd. Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe aan het controleren van afgeschreven accounts die moeten worden beschouwd voor verwijdering.

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3. a, b, 8.2.4. a, b en 8.2.5 verificatie op basis van wacht woorden

Deze blauw druk helpt u sterke wacht woorden af te dwingen door [Azure Policy](../../../policy/overview.md) definities toe te wijzen waarmee Windows-vm's worden gecontroleerd die geen minimale sterkte en andere wachtwoord vereisten afdwingen. Het bewustzijn van Vm's in strijd met het beleid voor wachtwoord sterkte helpt u bij het uitvoeren van corrigerende maat regelen om ervoor te zorgen dat wacht woorden voor alle VM-gebruikers accounts voldoen aan het beleid.

- \[Voor\]beeld: Windows-Vm's met een maximale wachtwoord duur van 70 dagen controleren
- \[Voor\]beeld: Vereisten implementeren voor het controleren van Windows-Vm's die geen maximale wachtwoord duur van 70 dagen hebben
- \[Voor\]beeld: Windows-Vm's controleren die de minimale wachtwoord lengte niet beperken tot 14 tekens
- \[Voor\]beeld: Vereisten implementeren om Windows-Vm's te controleren die de minimale wachtwoord lengte niet beperken tot 14 tekens
- \[Voor\]beeld: Virtuele Windows-machines controleren die het opnieuw gebruiken van de voor gaande 24 wacht woorden toestaan
- \[Voor\]beeld: Vereisten implementeren voor het controleren van virtuele Windows-machines die het opnieuw gebruiken van de voor gaande 24 wacht woorden toestaan

## <a name="103-and-1054-audit-generation"></a>10,3 en 10.5.4-controle genereren

Deze blauw druk helpt u om ervoor te zorgen dat systeem gebeurtenissen worden vastgelegd door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die logboek instellingen op Azure-resources controleren.
Diagnostische logboeken bieden inzicht in bewerkingen die zijn uitgevoerd in azure-resources. Azure-logboeken zijn afhankelijk van gesynchroniseerde interne klokken voor het maken van een tijdgebonden record met gebeurtenissen in resources.

- Controle moet worden ingeschakeld voor geavanceerde instellingen voor gegevens beveiliging op SQL Server
- Diagnostische instelling voor controleren
- Controle-instellingen op SQL server-niveau controleren
- Controle op SQL-servers implementeren
- Opslag accounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources

## <a name="1236-and-1237-information-security"></a>12.3.6 en 12.3.7 Information Security

Deze blauw druk helpt u bij het beheren en bepalen van uw netwerk door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die de acceptabele netwerk locaties controleren en de goedgekeurde bedrijfs producten die voor de omgeving zijn toegestaan. Deze kunnen door elk bedrijf worden aangepast via de beleids parameters in elk van deze beleids regels.

- Toegestane locaties
- Toegestane locaties voor resourcegroepen

## <a name="next-steps"></a>Volgende stappen

Nu u de controle toewijzing van de PCI-DSS v 3.2.1-blauw druk hebt gecontroleerd, gaat u naar de volgende artikelen voor meer informatie over het overzicht en hoe u dit voor beeld implementeert:

> [!div class="nextstepaction"]
> [PCI-DSS v 3.2.1 blauw druk-overzicht](./index.md)
> [PCI-DSS v 3.2.1 blauw druk-implementatie stappen](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).