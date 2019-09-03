---
title: Voor beeld-Canada Federal PBMM blauw druk-Control-toewijzing
description: De toewijzing van het voor beeld van Canada Federal PBMM blauw drukken tot Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/21/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 96b58a84befcd4a5661804b1265eae119f09977f
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232830"
---
# <a name="control-mapping-of-the-canada-federal-pbmm-blueprint-sample"></a>De toewijzing van het voor beeld van Canada Federal PBMM blauw drukken

In het volgende artikel wordt uitgelegd hoe de Azure-blauw drukken Canada, PBMM (gemiddelde integriteit, gemiddelde Beschik baarheid), blauw voor beeld wordt toegewezen aan de Canada Federal PBMM Controls. Zie [Canada Federal PBMM](https://www.canada.ca/en/government/system/digital-government/modern-emerging-technologies/cloud-services/government-canada-security-control-profile-cloud-based-it-services.html#toc4)(Engelstalig) voor meer informatie over de besturings elementen.

De volgende toewijzingen zijn de **Canadese Federal PBMM** -besturings elementen. Gebruik de navigatie aan de rechter kant om rechtstreeks naar een specifieke besturings element koppeling te gaan. Veel van de toegewezen besturings elementen worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md) -initiatief. Als u het complete initiatief wilt bekijken, opent u **beleid** in het Azure Portal en selecteert u de pagina **definities** . Zoek en selecteer vervolgens het  **\[voor beeld\]: Controleer het ingebouwde beleid voor** de bewaking van Canada Federal PBMM Controls.

## <a name="location-constraints"></a>Locatie beperkingen

Met deze blauw druk kunt u de locatie voor de implementatie van alle resources en resource groepen beperken tot Canada-centraal en Canada-oost door de volgende Azure Policy definities toe te wijzen:

- Toegestane locaties (is vastgelegd in ' Canada-centraal ' en ' Canada-oost ')
- Toegestane locaties voor resource groepen (is vastgelegd in ' Canada-centraal ' en ' Canada-oost ')

## <a name="ac-2-account-management"></a>AC-2-account beheer

Deze blauw druk helpt u bij het controleren van accounts die mogelijk niet voldoen aan de account beheer vereisten van uw organisatie. Met deze blauw druk worden [Azure Policy](../../../policy/overview.md) definities toegewezen waarmee externe accounts met de machtigingen lezen, schrijven en eigenaar voor een abonnement en afgeschafte accounts worden gecontroleerd. Door de accounts te controleren die door dit beleid worden gecontroleerd, kunt u de juiste actie ondernemen om te controleren of aan de vereisten voor account beheer is voldaan.

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement


## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) account beheer | Op rollen gebaseerde Schema's

Azure implementeert op [rollen gebaseerd toegangs beheer](../../../../role-based-access-control/overview.md) (RBAC) om u te helpen bij het beheren van de toegang tot resources in Azure. Met behulp van de Azure Portal kunt u controleren wie toegang heeft tot Azure-resources en de bijbehorende machtigingen. Deze blauw druk wijst ook [Azure Policy](../../../policy/overview.md) definities toe om het gebruik van Azure Active Directory-verificatie voor SQL-Servers en service Fabric te controleren. Met behulp van Azure Active Directory-verificatie kunt u eenvoudig beheer van machtigingen en gecentraliseerd identiteits beheer van database gebruikers en andere micro soft-Services. Daarnaast wijst deze blauw druk een Azure Policy definitie toe om het gebruik van aangepaste RBAC-regels te controleren. Als u wilt weten waar aangepaste RBAC-regels worden geïmplementeerd, kunt u controleren of de juiste implementatie nodig is, omdat aangepaste RBAC-regels fout gevoelig zijn.

- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers
- Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor client verificatie

## <a name="ac-4-information-flow-enforcement"></a>AC-4-afdwinging van gegevens stromen

Cross Origin Resource Sharing (CORS) kan toestaan dat App Services resources worden aangevraagd vanuit een extern domein. Micro soft raadt u aan om alleen vereiste domeinen te laten communiceren met uw API, functie en webtoepassingen. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe om u te helpen bij het controleren van de toegangs beperkingen voor CORS-bronnen in azure Security Center.
Met CORS-implementaties kunt u controleren of de besturings elementen voor informatie stromen zijn geïmplementeerd.

- CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw webtoepassing

## <a name="ac-5-separation-of-duties"></a>AC-5-schei ding van taken

Als er slechts één eigenaar van een Azure-abonnement is, is er geen administratieve redundantie toegestaan. Als er te veel eigen aars van Azure-abonnementen zijn, kan het mogelijk zijn om een schending te doen van een inbreuk op een eigenaars account. Met deze blauw druk kunt u het juiste aantal eigen aars van Azure-abonnementen onderhouden door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die het aantal eigen aren voor Azure-abonnementen controleren. Met deze blauw druk worden ook Azure Policy definities toegewezen waarmee u het lidmaatschap van de groep Administrators op virtuele Windows-machines kunt beheren. Het beheren van abonnements-eigenaar en beheerders machtigingen voor virtuele machines kunnen u helpen bij het implementeren van de juiste schei ding van taken.

- Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement
- Er moet meer dan één eigenaar aan uw abonnement zijn toegewezen
- Virtuele Windows-machines controleren waarin de groep Administrators een van de opgegeven leden bevat
- Virtuele Windows-machines controleren waarbij de groep Administrators niet alle opgegeven leden bevat
- Vereisten implementeren voor het controleren van Windows-Vm's waarbij de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren voor het controleren van Windows-Vm's waarbij de groep Administrators niet alle opgegeven leden bevat

## <a name="ac-6-least-privilege"></a>Minimale bevoegdheid voor AC-6

Azure implementeert op [rollen gebaseerd toegangs beheer](../../../../role-based-access-control/overview.md) (RBAC) om u te helpen bij het beheren van de toegang tot resources in Azure. Met behulp van de Azure Portal kunt u controleren wie toegang heeft tot Azure-resources en de bijbehorende machtigingen. Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe aan controle accounts waarvoor een prioriteit moet worden gegeven. Door deze account indicatoren te controleren, kunt u ervoor zorgen dat de besturings elementen met minimale bevoegdheden worden geïmplementeerd.

- Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement
- Er moet meer dan één eigenaar aan uw abonnement zijn toegewezen
- Virtuele Windows-machines controleren waarin de groep Administrators een van de opgegeven leden bevat
- Virtuele Windows-machines controleren waarbij de groep Administrators niet alle opgegeven leden bevat
- Vereisten implementeren voor het controleren van Windows-Vm's waarbij de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren voor het controleren van Windows-Vm's waarbij de groep Administrators niet alle opgegeven leden bevat

## <a name="ac-7-security-attributes"></a>AC-7-beveiligings kenmerken

De mogelijkheden voor gegevens detectie en-classificatie van geavanceerde gegevens beveiliging voor Azure SQL Database biedt mogelijkheden voor het detecteren, classificeren, labelen en beveiligen van gevoelige gegevens in uw data bases. Het kan worden gebruikt voor het zichtbaar maken van de classificatiestatus van gegevens in uw database, en het traceren van de toegang tot gevoelige gegevens binnen en buiten de database. Geavanceerde gegevens beveiliging kan u helpen de gegevens te waarborgen die zijn gekoppeld aan de juiste beveiligings kenmerken voor uw organisatie. Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe om het gebruik van geavanceerde gegevens beveiliging op SQL Server te controleren en af te dwingen.

- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw door SQL beheerde instanties
- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers
- Geavanceerde gegevens beveiliging implementeren op SQL-servers

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) externe toegang | Geautomatiseerde controle/controle

Met deze blauw druk kunt u externe toegang bewaken en beheren door [Azure Policy](../../../policy/overview.md) definities toe te wijzen om te controleren of externe fout opsporing voor Azure app service toepassing is uitgeschakeld en beleids definities voor het controleren van virtuele Linux-machines die extern toestaan verbindingen van accounts zonder wacht woorden. Deze blauw druk wijst ook een Azure Policy definitie toe waarmee u onbeperkte toegang tot opslag accounts kunt controleren. Door deze indica toren te bewaken, kunt u ervoor zorgen dat externe toegangs methoden voldoen aan uw beveiligings beleid.

- \[Voor\]beeld: Virtuele Linux-machines controleren die externe verbindingen van accounts zonder wacht woorden toestaan
- \[Voor\]beeld: Vereisten implementeren voor het controleren van virtuele Linux-machines die externe verbindingen toestaan van accounts zonder wacht woorden
- Onbeperkte netwerk toegang tot opslag accounts controleren
- Fout opsporing op afstand moet worden uitgeschakeld voor de API-app
- Fout opsporing op afstand moet worden uitgeschakeld voor functie-app
- Foutopsporing op afstand moet worden uitgeschakeld voor Web-App

## <a name="au-3-2-content-of-audit-records"></a>AU-3 (2) inhoud van audit records

Logboek gegevens die door Azure Monitor worden verzameld, worden opgeslagen in een Log Analytics-werk ruimte, waardoor gecentraliseerde configuratie en beheer mogelijk wordt. Deze blauw druk helpt u ervoor te zorgen dat gebeurtenissen worden geregistreerd door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die de implementatie van de log Analytics agent op virtuele machines van Azure controleren en afdwingen.

- \[Voor\]beeld: Implementatie van Log Analytics agent controleren-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Log Analytics agent implementatie controleren in VMSS-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Controleren Log Analytics werk ruimte voor VM-niet-overeenkomend rapport
- \[Voor\]beeld: Log Analytics-agent voor Linux-Vm's implementeren
- \[Voor\]beeld: Log Analytics-agent voor Windows-Vm's implementeren

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5-antwoord op mislukte controle verwerking

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe die de configuratie van controles en logboek registratie controleren. Het bewaken van deze configuraties kan een indicatie van een storing in een controle systeem of een verkeerde configuratie geven en helpt u bij het uitvoeren van corrigerende maat regelen.

- Diagnostische instelling voor controleren
- Controle-instellingen op SQL server-niveau controleren
- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) controle-, analyse-en rapportage doeleinden | Centrale controle en analyse

Logboek gegevens die door Azure Monitor worden verzameld, worden opgeslagen in een Log Analytics-werk ruimte, waardoor gecentraliseerde rapportage en analyse mogelijk wordt. Deze blauw druk helpt u ervoor te zorgen dat gebeurtenissen worden geregistreerd door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die de implementatie van de log Analytics agent op virtuele machines van Azure controleren en afdwingen.

- \[Voor\]beeld: Implementatie van Log Analytics agent controleren-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Log Analytics agent implementatie controleren in VMSS-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Controleren Log Analytics werk ruimte voor VM-niet-overeenkomend rapport
- \[Voor\]beeld: Log Analytics-agent voor Linux-Vm's implementeren
- \[Voor\]beeld: Log Analytics-agent voor Windows-Vm's implementeren

## <a name="au-12-audit-generation"></a>Generatie van AU-12-audit

Deze blauw druk helpt u om ervoor te zorgen dat systeem gebeurtenissen worden vastgelegd door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die logboek instellingen op Azure-resources controleren. Deze beleids definities controleren en afdwingen de implementatie van de Log Analytics-agent op virtuele machines van Azure en het configureren van controle-instellingen voor andere Azure-resource typen. Deze beleids definities controleren ook de configuratie van diagnostische Logboeken om inzicht te krijgen in bewerkingen die worden uitgevoerd binnen Azure-resources. Daarnaast zijn auditing en geavanceerde gegevens beveiliging geconfigureerd op SQL-servers.

- \[Voor\]beeld: Implementatie van Log Analytics agent controleren-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Log Analytics agent implementatie controleren in VMSS-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Controleren Log Analytics werk ruimte voor VM-niet-overeenkomend rapport
- \[Voor\]beeld: Log Analytics-agent voor Linux-Vm's implementeren
- \[Voor\]beeld: Log Analytics-agent voor Windows-Vm's implementeren
- Diagnostische instelling voor controleren
- Controle-instellingen op SQL server-niveau controleren
- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers
- Geavanceerde gegevens beveiliging implementeren op SQL-servers
- Controle op SQL-servers implementeren
- Diagnostische instellingen voor netwerk beveiligings groepen implementeren

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) minste functionaliteit | Geautoriseerde software-white list

Adaptief toepassings beheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor white list die ervoor kan zorgen dat specifieke software niet kan worden uitgevoerd op uw virtuele machines. Met toepassings beheer kunt u goedgekeurde toepassings lijsten maken voor uw virtuele machines. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die u helpt bij het bewaken van virtuele machines waar een toepassing white list wordt aanbevolen, maar nog niet is geconfigureerd.

- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines

## <a name="cm-11-user-installed-software"></a>CM-11 door de gebruiker geïnstalleerde software

Adaptief toepassings beheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor white list die ervoor kan zorgen dat specifieke software niet kan worden uitgevoerd op uw virtuele machines. Met toepassings beheer kunt u naleving van software restrictie beleid afdwingen en bewaken. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die u helpt bij het bewaken van virtuele machines waar een toepassing white list wordt aanbevolen, maar nog niet is geconfigureerd.

- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines

## <a name="cp-7-alternate-processing-site"></a>CP-7-alternatieve verwerkings site

Azure Site Recovery worden workloads die op virtuele machines worden uitgevoerd, gerepliceerd van een primaire locatie naar een secundaire locatie. Als er een storing optreedt op de primaire site, mislukt de werk belasting via de secundaire locatie. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee de virtuele machines worden gecontroleerd zonder dat herstel na nood geval is geconfigureerd. Door deze indicator te bewaken, kunt u ervoor zorgen dat de nood zakelijke nood besturings elementen worden uitgevoerd.

- Virtuele machines controleren zonder nood herstel zijn geconfigureerd

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) identificatie en verificatie (organisatie gebruikers) | Netwerk toegang tot bevoegde accounts

Deze blauw druk helpt u om bevoegde toegang te beperken en te beheren door [Azure Policy](../../../policy/overview.md) definities toe te wijzen aan controle accounts met eigenaar en/of schrijf machtigingen waarvoor geen multi-factor Authentication is ingeschakeld. Multi-factor Authentication helpt accounts veilig te houden, zelfs als er wordt geknoeid met één van de verificatie gegevens. Door accounts te controleren waarop multi-factor Authentication is ingeschakeld, kunt u accounts identificeren die waarschijnlijker worden aangetast.

- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement

## <a name="ia-5-authenticator-management"></a>Verificatie beheer IA-5

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee virtuele Linux-machines worden gecontroleerd die externe verbindingen toestaan van accounts zonder wacht woorden en/of onjuiste machtigingen hebben ingesteld voor het passwd-bestand. Deze blauw druk wijst ook beleids definities toe waarmee de configuratie van het type wachtwoord versleuteling voor virtuele Windows-machines wordt gecontroleerd. Door deze indica toren te controleren, zorgt u ervoor dat systeem verificaties voldoen aan het identificatie-en verificatie beleid van uw organisatie.

- \[Voor\]beeld: Virtuele Linux-machines waarvoor de machtigingen voor het passwd-bestand niet zijn ingesteld op 0644 controleren
- \[Voor\]beeld: Virtuele Linux-machines met accounts zonder wacht woorden controleren
- \[Voor\]beeld: Implementeer vereisten voor het controleren van virtuele Linux-machines waarop de machtigingen voor het passwd-bestand niet zijn ingesteld op 0644
- \[Voor\]beeld: Vereisten implementeren voor het controleren van virtuele Linux-machines met accounts zonder wacht woorden

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) verificatie beheer | Verificatie op basis van wacht woorden

Deze blauw druk helpt u bij het afdwingen van sterke wacht woorden door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die virtuele Windows-machines controleren die geen minimale sterkte en andere wachtwoord vereisten afdwingen. Het bewustzijn van virtuele machines met een schending van het beleid voor wachtwoord sterkte helpt u bij het uitvoeren van corrigerende maat regelen om ervoor te zorgen dat wacht woorden voor alle gebruikers accounts van de virtuele machine voldoen aan het wachtwoord beleid van uw organisatie.

- \[Voor\]beeld: Virtuele Windows-machines controleren die het opnieuw gebruiken van de voor gaande 24 wacht woorden toestaan
- \[Voor\]beeld: Windows-Vm's met een maximale wachtwoord duur van 70 dagen controleren
- \[Voor\]beeld: Windows-Vm's met een minimale wachtwoord leeftijd van 1 dag controleren
- \[Voor\]beeld: Windows-Vm's controleren waarvoor de instelling voor wachtwoord complexiteit niet is ingeschakeld
- \[Voor\]beeld: Windows-Vm's controleren die de minimale wachtwoord lengte niet beperken tot 14 tekens
- \[Voor\]beeld: Vereisten implementeren voor het controleren van virtuele Windows-machines die het opnieuw gebruiken van de voor gaande 24 wacht woorden toestaan
- \[Voor\]beeld: Vereisten implementeren voor het controleren van Windows-Vm's die geen maximale wachtwoord duur van 70 dagen hebben
- \[Voor\]beeld: Vereisten implementeren voor het controleren van Windows-Vm's die geen minimale wachtwoord duur van 1 dag hebben
- \[Voor\]beeld: Vereisten implementeren voor het controleren van Windows-Vm's waarvoor de instelling voor wachtwoord complexiteit niet is ingeschakeld
- \[Voor\]beeld: Vereisten implementeren om Windows-Vm's te controleren die de minimale wachtwoord lengte niet beperken tot 14 tekens

## <a name="ia-8-100-identification-and-authentication-non-organizational-users--identity-and-credential-assurance-levels"></a>IA-8 (100) identificatie en verificatie (niet-organisatie gebruikers) | Identiteits-en referentie Assurance niveaus

Deze blauw druk helpt u om bevoegde toegang te beperken en te beheren door [Azure Policy](../../../policy/overview.md) definities toe te wijzen aan controle accounts met eigenaar en/of schrijf machtigingen waarvoor geen multi-factor Authentication is ingeschakeld. Multi-factor Authentication helpt accounts veilig te houden, zelfs als er wordt geknoeid met één van de verificatie gegevens. Door accounts te controleren waarop multi-factor Authentication is ingeschakeld, kunt u accounts identificeren die waarschijnlijker worden aangetast.

- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement

## <a name="ra-5-vulnerability-scanning"></a>Scannen op beveiligings problemen met RA-5

Deze blauw druk helpt u bij het beheren van beveiligings problemen met informatie systemen door [Azure Policy](../../../policy/overview.md) definities toe te wijzen waarmee beveiligings problemen met het besturings systeem, SQL-beveiligings problemen en beveiligings problemen met virtuele machines in azure Security Center worden bewaakt Azure Security Center biedt rapportage mogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligings status van geïmplementeerde Azure-resources. Deze blauw druk wijst ook beleids definities toe die geavanceerde gegevens beveiliging controleren en afdwingen op SQL-servers. Met geavanceerde gegevens beveiliging zijn de evaluatie van beveiligings problemen en geavanceerde functies voor bedreigings beveiliging beschikbaar om u te helpen bij het begrijpen van de kwets baarheid van uw geïmplementeerde

- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers
- Geavanceerde gegevens beveiliging implementeren op SQL-servers
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Beveiligings problemen in de beveiligings configuratie op uw virtuele machines moeten worden hersteld
- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken

## <a name="sc-5-denial-of-service-protection"></a>SC-5-denial of service-beveiliging

De Standard-laag DDoS (Distributed Denial of service) van Azure biedt extra functies en mogelijkheden voor risico beperking via de Basic-servicelaag. Deze aanvullende functies omvatten Azure Monitor integratie en de mogelijkheid om te controleren of er meldingen over de risico beperking na aanvallen worden weer gegeven. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die controleert of de DDoS Standard-laag is ingeschakeld. Het verschil tussen de functionaliteit van de service lagen kan u helpen bij het selecteren van de beste oplossing voor denial of service-beveiligingen voor uw Azure-omgeving.

- DDoS Protection standaard moet zijn ingeschakeld

## <a name="sc-7-boundary-protection"></a>SC-7-grens beveiliging

Deze blauw druk helpt u bij het beheren en best uren van de systeem grens door een [Azure Policy](../../../policy/overview.md) definitie toe te wijzen die de aanbevelingen voor de beveiliging van netwerk beveiligings groepen in azure Security Center controleert. Azure Security Center analyseert de verkeers patronen van Internet gerichte virtuele machines en biedt regel aanbevelingen voor de netwerk beveiligings groep om de mogelijke kwets baarheid te verminderen.
Daarnaast wijst deze blauw druk ook beleids definities toe waarmee onbeveiligde eind punten, toepassingen en opslag accounts worden bewaakt. Eind punten en toepassingen die niet zijn beveiligd door een firewall en opslag accounts met onbeperkte toegang, kunnen onbedoelde toegang tot gegevens in het informatie systeem toestaan.

- De regels voor de netwerk beveiligings groep voor virtuele machines die zijn gericht op internet, moeten worden gehard
- Toegang via Internet gericht eind punt moet worden beperkt
- Onbeperkte netwerk toegang tot opslag accounts controleren
- De Nsg's-regels voor webtoepassingen op IaaS moeten een harde verbinding hebben

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) grens beveiliging | Toegangs punten

Met Just-in-time (JIT) toegang tot virtuele machines wordt het binnenkomende verkeer naar Azure virtual machine vergrendeld, waardoor de bloot stelling aan aanvallen wordt verkleind, terwijl er eenvoudig toegang wordt geboden tot Vm's wanneer dat nodig is. Met toegang tot virtuele JIT-machines kunt u het aantal externe verbindingen met uw resources in azure beperken. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die u helpt bij het bewaken van virtuele machines die just-in-time-toegang kunnen ondersteunen, maar die nog niet zijn geconfigureerd.

- Just-in-time-netwerk toegangs beheer moet worden toegepast op virtuele machines

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) grens beveiliging | Externe telecommunicatie Services

Met Just-in-time (JIT) toegang tot virtuele machines wordt het binnenkomende verkeer naar Azure virtual machine vergrendeld, waardoor de bloot stelling aan aanvallen wordt verkleind, terwijl er eenvoudig toegang wordt geboden tot Vm's wanneer dat nodig is. Met toegang tot virtuele JIT-machines kunt u uitzonde ringen beheren voor uw Traffic Flow-beleid door de processen voor toegangs aanvragen en-goed keuring te vergemakkelijken. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die u helpt bij het bewaken van virtuele machines die just-in-time-toegang kunnen ondersteunen, maar die nog niet zijn geconfigureerd.

- Just-in-time-netwerk toegangs beheer moet worden toegepast op virtuele machines

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) verzen ding van vertrouwelijkheid en integriteit | Cryptografische of alternatieve fysieke beveiliging

Deze blauw druk helpt u om het vertrouwelijke en de integriteit van verzonden informatie te beschermen door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die u helpen bij het bewaken van het cryptografische mechanisme dat is geïmplementeerd voor communicatie protocollen. Zorg ervoor dat de communicatie op de juiste wijze wordt versleuteld, zodat u de vereisten van uw organisatie kunt nagaan of informatie beschermt tegen onbevoegde openbaar making en wijzigingen

- De API-app mag alleen toegankelijk zijn via HTTPS
- Windows-webservers controleren die geen protocollen voor beveiligde communicatie gebruiken
- Vereisten implementeren voor het controleren van Windows-webservers die geen beveiligde communicatie protocollen gebruiken
- Functie-App moet alleen toegankelijk zijn via HTTPS
- Alleen beveiligde verbindingen met uw Redis Cache moeten worden ingeschakeld
- Web-App moet alleen toegankelijk zijn via HTTPS
- Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld

## <a name="sc-28-1-protection-of-information-at-rest"></a>SC-28 (1) bescherming van informatie bij rust

Deze blauw druk helpt u bij het afdwingen van uw beleid voor het gebruik van cryptograph-besturings elementen om informatie te beveiligen door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die specifieke cryptograph-besturings elementen afdwingen en het gebruik van zwakke cryptografische instellingen te controleren. Als u wilt weten waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, kunt u corrigerende maat regelen nemen om ervoor te zorgen dat bronnen worden geconfigureerd in overeenstemming met uw informatie beveiligings beleid. Met name de beleids definities die door deze blauw drukken worden toegewezen, vereisen versleuteling voor data Lake Storage-accounts. transparante gegevens versleuteling vereisen voor SQL-data bases; en controleren op ontbrekende versleuteling voor SQL-data bases, schijven van virtuele machines en Automation-account variabelen.

- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers
- Geavanceerde gegevens beveiliging implementeren op SQL-servers
- Transparante gegevens versleuteling van SQL DB implementeren
- Schijf versleuteling moet worden toegepast op virtuele machines
- Versleuteling vereisen voor Data Lake Store accounts
- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld

## <a name="si-2-flaw-remediation"></a>Fout herstel van SI-2-fouten

Deze blauw druk helpt u bij het beheren van gegevens systeem fouten door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die ontbrekende systeem updates, problemen met het besturings systeem, SQL-beveiligings problemen en beveiligings problemen met virtuele machines in azure-beveiliging bewaken Projecten. Azure Security Center biedt rapportage mogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligings status van geïmplementeerde Azure-resources. Deze blauw druk wijst ook een beleids definitie toe die ervoor zorgt dat het besturings systeem wordt geïnstalleerd voor schaal sets voor virtuele machines.

- Automatische patching van besturingssysteem installatie kopieën vereisen op Virtual Machine Scale Sets
- Systeem updates op virtuele-machine schaal sets moeten worden geïnstalleerd
- Systeem updates moeten worden geïnstalleerd op uw virtuele machines
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Beveiligings problemen in de beveiligings configuratie op uw virtuele machines moeten worden hersteld
- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken

## <a name="si-3-malicious-code-protection"></a>SI-3-beveiliging tegen schadelijke code

Deze blauw druk helpt u bij het beheren van Endpoint Protection, met inbegrip van schadelijke code beveiliging, door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die controleren op ontbrekende Endpoint Protection op virtuele machines in azure Security Center en afdwingen van micro soft antimalware-oplossing op virtuele Windows-machines.

- Standaard micro soft IaaSAntimalware-extensie voor Windows Server implementeren
- Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machine schaal sets
- Ontbrekende Endpoint Protection in Azure Security Center controleren

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) schadelijke code beveiliging | Centraal beheer

Deze blauw druk helpt u bij het beheren van Endpoint Protection, met inbegrip van schadelijke code beveiliging, door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die controleren op ontbrekende Endpoint Protection op virtuele machines in azure Security Center. Azure Security Center biedt gecentraliseerde beheer-en rapportage mogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligings status van geïmplementeerde Azure-resources.

- Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machine schaal sets
- Ontbrekende Endpoint Protection in Azure Security Center controleren

## <a name="si-4-information-system-monitoring"></a>Informatie systeem bewaking SI-4

Deze blauw druk helpt u bij het controleren van uw systeem door logboek registratie en gegevens beveiliging in azure-resources te controleren en af te dwingen. Met name aan de beleids regels is het controleren en afdwingen van de implementatie van de Log Analytics agent en verbeterde beveiligings instellingen voor SQL-data bases, opslag accounts en netwerk bronnen toegewezen. Deze mogelijkheden kunnen u helpen bij het detecteren van afwijkend gedrag en indica toren van aanvallen, zodat u de juiste actie kunt ondernemen.

- \[Voor\]beeld: Implementatie van Log Analytics agent controleren-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Log Analytics agent implementatie controleren in VMSS-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Controleren Log Analytics werk ruimte voor VM-niet-overeenkomend rapport
- \[Voor\]beeld: Log Analytics-agent voor Linux-Vm's implementeren
- \[Voor\]beeld: Log Analytics-agent voor Windows-Vm's implementeren
- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers
- Geavanceerde gegevens beveiliging implementeren op SQL-servers
- Geavanceerde beveiliging tegen bedreigingen implementeren voor opslag accounts
- Controle op SQL-servers implementeren
- Network Watcher implementeren bij het maken van virtuele netwerken
- Detectie van bedreigingen op SQL-servers implementeren

> [!NOTE]
> De beschik baarheid van specifieke Azure Policy definities kan verschillen in Azure Government en andere nationale Clouds. 

## <a name="next-steps"></a>Volgende stappen

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levens duur van de blauw druk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).