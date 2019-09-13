---
title: Voor beeld-IRS 1075 blauw druk-besturings element toewijzing
description: De toewijzing van het voor beeld van IRS 1075-blauw druk aan Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/07/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 3157fd1e9790beb2230f8dee5b2d10afc4beff69
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918690"
---
# <a name="control-mapping-of-the-irs-1075-blueprint-sample"></a>De toewijzing van het voor beeld van IRS 1075 blauw druk controleren

Het volgende artikel bevat informatie over de manier waarop het voor beeld van de Azure blauw drukken IRS 1075 wordt toegewezen aan de IRS 1075-besturings elementen. Zie [IRS 1075](https://www.irs.gov/pub/irs-pdf/p1075.pdf)voor meer informatie over de besturings elementen.

De volgende toewijzingen zijn de **IRS 1075** -besturings elementen. Gebruik de navigatie aan de rechter kant om rechtstreeks naar een specifieke besturings element koppeling te gaan. Veel van de toegewezen besturings elementen worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md) -initiatief. Als u het complete initiatief wilt bekijken, opent u **beleid** in het Azure Portal en selecteert u de pagina **definities** . Zoek en selecteer vervolgens het  **\[voor beeld\]: Controleer IRS 1075-besturings elementen en implementeer specifieke VM-extensies ter** ondersteuning van het ingebouwde beleids initiatief voor controle vereisten.

> [!IMPORTANT]
> Elk besturings element hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md) definities. Met deze beleids regels kunt u de naleving van het besturings element [beoordelen](../../../policy/how-to/get-compliance-data.md) . Er is echter vaak geen 1:1-of volledige overeenkomst tussen een besturings element en een of meer beleids regels. Als zodanig is de **naleving** in azure Policy alleen bedoeld voor het beleid zelf. Dit garandeert niet dat u volledig compatibel bent met alle vereisten van een besturings element. Daarnaast bevat de nalevings standaard besturings elementen die niet worden behandeld door Azure Policy definities op dit moment. Daarom is naleving in Azure Policy slechts een gedeeltelijke weer gave van uw algemene nalevings status. De koppelingen tussen de besturings elementen en Azure Policy definities voor dit voor beeld van deze naleving blauw druk kunnen na verloop van tijd veranderen. Als u de wijzigings geschiedenis wilt weer geven, raadpleegt u de [github commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/irs-1075/control-mapping.md).

## <a name="9321-ac-2-account-management"></a>9.3.2.1 AC-2-account beheer

Deze blauw druk helpt u bij het controleren van accounts die mogelijk niet voldoen aan de account beheer vereisten van uw organisatie. Met deze blauw druk worden [Azure Policy](../../../policy/overview.md) definities toegewezen waarmee externe accounts met de machtigingen lezen, schrijven en eigenaar voor een abonnement en afgeschafte accounts worden gecontroleerd. Door de accounts te controleren die door dit beleid worden gecontroleerd, kunt u de juiste actie ondernemen om te controleren of aan de vereisten voor account beheer is voldaan.

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement

## <a name="9321-ac-2-7-account-management--role-based-schemes"></a>9.3.2.1 AC-2 (7) account beheer | Op rollen gebaseerde Schema's

Azure implementeert op [rollen gebaseerd toegangs beheer](../../../../role-based-access-control/overview.md) (RBAC) om u te helpen bij het beheren van de toegang tot resources in Azure. Met behulp van de Azure Portal kunt u controleren wie toegang heeft tot Azure-resources en de bijbehorende machtigingen. Deze blauw druk wijst ook [Azure Policy](../../../policy/overview.md) definities toe om het gebruik van Azure Active Directory-verificatie voor SQL-Servers en service Fabric te controleren. Met behulp van Azure Active Directory-verificatie kunt u eenvoudig beheer van machtigingen en gecentraliseerd identiteits beheer van database gebruikers en andere micro soft-Services. Daarnaast wijst deze blauw druk een Azure Policy definitie toe om het gebruik van aangepaste RBAC-regels te controleren. Als u wilt weten waar aangepaste RBAC-regels worden geïmplementeerd, kunt u controleren of de juiste implementatie nodig is, omdat aangepaste RBAC-regels fout gevoelig zijn.

- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers
- Gebruik van aangepaste RBAC-regels controleren
- Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor client verificatie

## <a name="9321-ac-2-12-account-management--account-monitoring--atypical-usage"></a>9.3.2.1 AC-2 (12) account beheer | Account bewaking/ongewoon gebruik

Met Just-in-time (JIT) toegang tot virtuele machines wordt het binnenkomende verkeer naar Azure virtual machine vergrendeld, waardoor de bloot stelling aan aanvallen wordt verkleind, terwijl er eenvoudig toegang wordt geboden tot Vm's wanneer dat nodig is. Alle JIT-aanvragen voor toegang tot virtuele machines worden vastgelegd in het activiteiten logboek, zodat u kunt controleren op ongewoon gebruik. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die u helpt bij het bewaken van virtuele machines die just-in-time-toegang kunnen ondersteunen, maar die nog niet zijn geconfigureerd.

- Just-in-time-netwerk toegangs beheer moet worden toegepast op virtuele machines

## <a name="9314-ac-4-information-flow-enforcement"></a>9.3.1.4 AC-4 informatie stroom afdwingen

Cross Origin Resource Sharing (CORS) kan toestaan dat App Services resources worden aangevraagd vanuit een extern domein. Micro soft raadt u aan om alleen vereiste domeinen te laten communiceren met uw API, functie en webtoepassingen. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe om u te helpen bij het controleren van de toegangs beperkingen voor CORS-bronnen in azure Security Center.
Met CORS-implementaties kunt u controleren of de besturings elementen voor informatie stromen zijn geïmplementeerd.

- CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw webtoepassing

## <a name="9315-ac-5-separation-of-duties"></a>9.3.1.5 AC-5 schei ding van taken

Als er slechts één eigenaar van een Azure-abonnement is, is er geen administratieve redundantie toegestaan. Als er te veel eigen aars van Azure-abonnementen zijn, kan het mogelijk zijn om een schending te doen van een inbreuk op een eigenaars account. Met deze blauw druk kunt u het juiste aantal eigen aars van Azure-abonnementen onderhouden door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die het aantal eigen aren voor Azure-abonnementen controleren. Met deze blauw druk worden ook Azure Policy definities toegewezen waarmee u het lidmaatschap van de groep Administrators op virtuele Windows-machines kunt beheren. Het beheren van abonnements-eigenaar en beheerders machtigingen voor virtuele machines kunnen u helpen bij het implementeren van de juiste schei ding van taken.

- Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement
- Virtuele Windows-machines controleren waarin de groep Administrators een van de opgegeven leden bevat
- Virtuele Windows-machines controleren waarbij de groep Administrators niet alle opgegeven leden bevat
- Vereisten implementeren voor het controleren van Windows-Vm's waarbij de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren voor het controleren van Windows-Vm's waarbij de groep Administrators niet alle opgegeven leden bevat
- Er moet meer dan één eigenaar aan uw abonnement zijn toegewezen

## <a name="9316-ac-6-7-least-privilege--review-of-user-privileges"></a>9.3.1.6 AC-6 (7) mini maal privilege | Gebruikers bevoegdheden controleren

Azure implementeert op [rollen gebaseerd toegangs beheer](../../../../role-based-access-control/overview.md) (RBAC) om u te helpen bij het beheren van de toegang tot resources in Azure. Met behulp van de Azure Portal kunt u controleren wie toegang heeft tot Azure-resources en de bijbehorende machtigingen. Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe aan controle accounts waarvoor een prioriteit moet worden gegeven. Door deze account indicatoren te controleren, kunt u ervoor zorgen dat de besturings elementen met minimale bevoegdheden worden geïmplementeerd.

- Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement
- Virtuele Windows-machines controleren waarin de groep Administrators een van de opgegeven leden bevat
- Virtuele Windows-machines controleren waarbij de groep Administrators niet alle opgegeven leden bevat
- Vereisten implementeren voor het controleren van Windows-Vm's waarbij de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren voor het controleren van Windows-Vm's waarbij de groep Administrators niet alle opgegeven leden bevat
- Er moet meer dan één eigenaar aan uw abonnement zijn toegewezen

## <a name="93112-ac-17-1-remote-access--automated-monitoring--control"></a>9.3.1.12 AC-17 (1) externe toegang | Geautomatiseerde controle/controle

Met deze blauw druk kunt u externe toegang bewaken en beheren door [Azure Policy](../../../policy/overview.md) definities toe te wijzen om te controleren of externe fout opsporing voor Azure app service toepassing is uitgeschakeld en beleids definities voor het controleren van virtuele Linux-machines die extern toestaan verbindingen van accounts zonder wacht woorden. Deze blauw druk wijst ook een Azure Policy definitie toe waarmee u onbeperkte toegang tot opslag accounts kunt controleren. Door deze indica toren te bewaken, kunt u ervoor zorgen dat externe toegangs methoden voldoen aan uw beveiligings beleid.

- \[Voor\]beeld: Virtuele Linux-machines controleren die externe verbindingen van accounts zonder wacht woorden toestaan
- \[Voor\]beeld: Vereisten implementeren voor het controleren van virtuele Linux-machines die externe verbindingen toestaan van accounts zonder wacht woorden
- Onbeperkte netwerk toegang tot opslag accounts controleren
- Fout opsporing op afstand moet worden uitgeschakeld voor de API-app
- Fout opsporing op afstand moet worden uitgeschakeld voor functie-app
- Foutopsporing op afstand moet worden uitgeschakeld voor Web-App

## <a name="9313-au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>9.3.1.3 AU-3 (2) inhoud van audit records | Gecentraliseerd beheer van geplande controle record inhoud

Logboek gegevens die door Azure Monitor worden verzameld, worden opgeslagen in een Log Analytics-werk ruimte, waardoor gecentraliseerde configuratie en beheer mogelijk wordt. Deze blauw druk helpt u ervoor te zorgen dat gebeurtenissen worden geregistreerd door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die de implementatie van de log Analytics agent op virtuele machines van Azure controleren en afdwingen.

- \[Voor\]beeld: Implementatie van Log Analytics agent controleren-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Log Analytics agent implementatie controleren in VMSS-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Controleren Log Analytics werk ruimte voor VM-niet-overeenkomend rapport
- \[Voor\]beeld: Log Analytics agent voor Linux-VM Scale Sets implementeren (VMSS)
- \[Voor\]beeld: Log Analytics-agent voor Linux-Vm's implementeren
- \[Voor\]beeld: Log Analytics agent voor Windows-VM Scale Sets (VMSS) implementeren
- \[Voor\]beeld: Log Analytics-agent voor Windows-Vm's implementeren

## <a name="9335-au-5-response-to-audit-processing-failures"></a>9.3.3.5 AU-5-reactie op fouten bij de controle verwerking

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe die de configuratie van controles en logboek registratie controleren. Het bewaken van deze configuraties kan een indicatie van een storing in een controle systeem of een verkeerde configuratie geven en helpt u bij het uitvoeren van corrigerende maat regelen.

- Diagnostische instelling voor controleren
- Controle-instellingen op SQL server-niveau controleren
- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers

## <a name="9336-au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>9.3.3.6 AU-6 (4) controle, analyse en rapportage | Centrale controle en analyse

Logboek gegevens die door Azure Monitor worden verzameld, worden opgeslagen in een Log Analytics-werk ruimte, waardoor gecentraliseerde rapportage en analyse mogelijk wordt. Deze blauw druk helpt u ervoor te zorgen dat gebeurtenissen worden geregistreerd door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die de implementatie van de log Analytics agent op virtuele machines van Azure controleren en afdwingen.

- \[Voor\]beeld: Implementatie van Log Analytics agent controleren-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Log Analytics agent implementatie controleren in VMSS-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Controleren Log Analytics werk ruimte voor VM-niet-overeenkomend rapport
- \[Voor\]beeld: Log Analytics agent voor Linux-VM Scale Sets implementeren (VMSS)
- \[Voor\]beeld: Log Analytics-agent voor Linux-Vm's implementeren
- \[Voor\]beeld: Log Analytics agent voor Windows-VM Scale Sets (VMSS) implementeren
- \[Voor\]beeld: Log Analytics-agent voor Windows-Vm's implementeren

## <a name="93311-au-12-audit-generation"></a>9.3.3.11 AU-12-audit genereren

Deze blauw druk helpt u om ervoor te zorgen dat systeem gebeurtenissen worden vastgelegd door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die logboek instellingen op Azure-resources controleren. Deze beleids definities controleren en afdwingen de implementatie van de Log Analytics-agent op virtuele machines van Azure en het configureren van controle-instellingen voor andere Azure-resource typen. Deze beleids definities controleren ook de configuratie van diagnostische Logboeken om inzicht te krijgen in bewerkingen die worden uitgevoerd binnen Azure-resources. Daarnaast zijn auditing en geavanceerde gegevens beveiliging geconfigureerd op SQL-servers.

- \[Voor\]beeld: Implementatie van Log Analytics agent controleren-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Log Analytics agent implementatie controleren in VMSS-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Controleren Log Analytics werk ruimte voor VM-niet-overeenkomend rapport
- \[Voor\]beeld: Log Analytics agent voor Linux-VM Scale Sets implementeren (VMSS)
- \[Voor\]beeld: Log Analytics-agent voor Linux-Vm's implementeren
- \[Voor\]beeld: Log Analytics agent voor Windows-VM Scale Sets (VMSS) implementeren
- \[Voor\]beeld: Log Analytics-agent voor Windows-Vm's implementeren
- Diagnostische instelling voor controleren
- Controle-instellingen op SQL server-niveau controleren
- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers
- Geavanceerde gegevens beveiliging implementeren op SQL-servers
- Controle op SQL-servers implementeren
- Diagnostische instellingen voor netwerk beveiligings groepen implementeren

## <a name="9357-cm-7-2-least-functionality--prevent-program-execution"></a>9.3.5.7 CM-7 (2) minste functionaliteit | Programma-uitvoering voor komen

Adaptief toepassings beheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor white list die ervoor kan zorgen dat specifieke software niet kan worden uitgevoerd op uw virtuele machines. Toepassings beheer kan worden uitgevoerd in een afdwingings modus waardoor niet-goedgekeurde toepassing niet kan worden uitgevoerd. Deze blauw druk wijst een Azure Policy definitie toe die u helpt bij het bewaken van virtuele machines waar een toepassing white list wordt aanbevolen, maar nog niet is geconfigureerd.

- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines

## <a name="9357-cm-7-5-least-functionality--authorized-software--whitelisting"></a>9.3.5.7 CM-7 (5) minste functionaliteit | Geautoriseerde software-white list

Adaptief toepassings beheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor white list die ervoor kan zorgen dat specifieke software niet kan worden uitgevoerd op uw virtuele machines. Met toepassings beheer kunt u goedgekeurde toepassings lijsten maken voor uw virtuele machines. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die u helpt bij het bewaken van virtuele machines waar een toepassing white list wordt aanbevolen, maar nog niet is geconfigureerd.

- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines

## <a name="93511-cm-11-user-installed-software"></a>9.3.5.11 CM-11 door de gebruiker geïnstalleerde software

Adaptief toepassings beheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor white list die ervoor kan zorgen dat specifieke software niet kan worden uitgevoerd op uw virtuele machines. Met toepassings beheer kunt u naleving van software restrictie beleid afdwingen en bewaken. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die u helpt bij het bewaken van virtuele machines waar een toepassing white list wordt aanbevolen, maar nog niet is geconfigureerd.

- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines

## <a name="9366-cp-7-alternate-processing-site"></a>9.3.6.6 CP-7-alternatieve verwerkings site

Azure Site Recovery worden workloads die op virtuele machines worden uitgevoerd, gerepliceerd van een primaire locatie naar een secundaire locatie. Als er een storing optreedt op de primaire site, mislukt de werk belasting via de secundaire locatie. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee de virtuele machines worden gecontroleerd zonder dat herstel na nood geval is geconfigureerd. Door deze indicator te bewaken, kunt u ervoor zorgen dat de nood zakelijke nood besturings elementen worden uitgevoerd.

- Virtuele machines controleren zonder nood herstel zijn geconfigureerd

## <a name="9372-ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>9.3.7.2 IA-2 (1) identificatie en verificatie (organisatie gebruikers) | Netwerk toegang tot bevoegde accounts

Deze blauw druk helpt u om bevoegde toegang te beperken en te beheren door [Azure Policy](../../../policy/overview.md) definities toe te wijzen aan controle accounts met eigenaar en/of schrijf machtigingen waarvoor geen multi-factor Authentication is ingeschakeld. Multi-factor Authentication helpt accounts veilig te houden, zelfs als er wordt geknoeid met één van de verificatie gegevens. Door accounts te controleren waarop multi-factor Authentication is ingeschakeld, kunt u accounts identificeren die waarschijnlijker worden aangetast.

- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement

## <a name="9372-ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>9.3.7.2 IA-2 (2) identificatie en verificatie (organisatie gebruikers) | Netwerk toegang tot niet-bevoegde accounts

Met deze blauw druk kunt u de toegang beperken en beheren door een [Azure Policy](../../../policy/overview.md) definitie toe te wijzen aan controle accounts met lees machtigingen waarvoor multi-factor Authentication niet is ingeschakeld. Multi-factor Authentication helpt accounts veilig te houden, zelfs als er wordt geknoeid met één van de verificatie gegevens. Door accounts te controleren waarop multi-factor Authentication is ingeschakeld, kunt u accounts identificeren die waarschijnlijker worden aangetast.

- MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement

## <a name="9375-ia-5-authenticator-management"></a>9.3.7.5 IA-5-verificatie beheer

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee virtuele Linux-machines worden gecontroleerd die externe verbindingen toestaan van accounts zonder wacht woorden en/of onjuiste machtigingen hebben ingesteld voor het passwd-bestand. Deze blauw druk wijst ook beleids definities toe waarmee de configuratie van het type wachtwoord versleuteling voor virtuele Windows-machines wordt gecontroleerd. Door deze indica toren te controleren, zorgt u ervoor dat systeem verificaties voldoen aan het identificatie-en verificatie beleid van uw organisatie.

- \[Voor\]beeld: Virtuele Linux-machines waarvoor de machtigingen voor het passwd-bestand niet zijn ingesteld op 0644 controleren
- \[Voor\]beeld: Virtuele Linux-machines met accounts zonder wacht woorden controleren
- \[Voor\]beeld: Windows-Vm's controleren die geen wacht woorden opslaan met omkeer bare versleuteling
- \[Voor\]beeld: Implementeer vereisten voor het controleren van virtuele Linux-machines waarop de machtigingen voor het passwd-bestand niet zijn ingesteld op 0644
- \[Voor\]beeld: Vereisten implementeren voor het controleren van virtuele Linux-machines met accounts zonder wacht woorden
- \[Voor\]beeld: Vereisten implementeren voor het controleren van Windows-Vm's die geen wacht woorden opslaan met omkeer bare versleuteling

## <a name="9375-ia-5-1-authenticator-management--password-based-authentication"></a>9.3.7.5 IA-5 (1) verificatie beheer | Verificatie op basis van wacht woorden

Deze blauw druk helpt u bij het afdwingen van sterke wacht woorden door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die virtuele Windows-machines controleren die geen minimale sterkte en andere wachtwoord vereisten afdwingen. Het bewustzijn van virtuele machines met een schending van het beleid voor wachtwoord sterkte helpt u bij het uitvoeren van corrigerende maat regelen om ervoor te zorgen dat wacht woorden voor alle gebruikers accounts van de virtuele machine voldoen aan het wachtwoord beleid van uw organisatie.

- \[Voor\]beeld: Virtuele Windows-machines controleren die het opnieuw gebruiken van de voor gaande 24 wacht woorden toestaan
- \[Voor\]beeld: Windows-Vm's met een maximale wachtwoord duur van 70 dagen controleren
- \[Voor\]beeld: Windows-Vm's met een minimale wachtwoord leeftijd van 1 dag controleren
- \[Voor\]beeld: Windows-Vm's controleren waarvoor de instelling voor wachtwoord complexiteit niet is ingeschakeld
- \[Voor\]beeld: Windows-Vm's controleren die de minimale wachtwoord lengte niet beperken tot 14 tekens
- \[Voor\]beeld: Windows-Vm's controleren die geen wacht woorden opslaan met omkeer bare versleuteling
- \[Voor\]beeld: Vereisten implementeren voor het controleren van virtuele Windows-machines die het opnieuw gebruiken van de voor gaande 24 wacht woorden toestaan
- \[Voor\]beeld: Vereisten implementeren voor het controleren van Windows-Vm's die geen maximale wachtwoord duur van 70 dagen hebben
- \[Voor\]beeld: Vereisten implementeren voor het controleren van Windows-Vm's die geen minimale wachtwoord duur van 1 dag hebben
- \[Voor\]beeld: Vereisten implementeren voor het controleren van Windows-Vm's waarvoor de instelling voor wachtwoord complexiteit niet is ingeschakeld
- \[Voor\]beeld: Vereisten implementeren om Windows-Vm's te controleren die de minimale wachtwoord lengte niet beperken tot 14 tekens
- \[Voor\]beeld: Vereisten implementeren voor het controleren van Windows-Vm's die geen wacht woorden opslaan met omkeer bare versleuteling

## <a name="93143-ra-5-vulnerability-scanning"></a>9.3.14.3 RA-5-beveiligings problemen scannen

Deze blauw druk helpt u bij het beheren van beveiligings problemen met informatie systemen door [Azure Policy](../../../policy/overview.md) definities toe te wijzen waarmee beveiligings problemen met het besturings systeem, SQL-beveiligings problemen en beveiligings problemen met virtuele machines in azure Security Center worden bewaakt Azure Security Center biedt rapportage mogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligings status van geïmplementeerde Azure-resources. Deze blauw druk wijst ook beleids definities toe die geavanceerde gegevens beveiliging controleren en afdwingen op SQL-servers. Met geavanceerde gegevens beveiliging zijn de evaluatie van beveiligings problemen en geavanceerde functies voor bedreigings beveiliging beschikbaar om u te helpen bij het begrijpen van de kwets baarheid van uw geïmplementeerde

- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers
- Geavanceerde gegevens beveiliging implementeren op SQL-servers
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Beveiligings problemen in de beveiligings configuratie op uw virtuele machines moeten worden hersteld
- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken

## <a name="93164-sc-5-denial-of-service-protection"></a>9.3.16.4 SC-5-denial of service-beveiliging

De Standard-laag DDoS (Distributed Denial of service) van Azure biedt extra functies en mogelijkheden voor risico beperking via de Basic-servicelaag. Deze aanvullende functies omvatten Azure Monitor integratie en de mogelijkheid om te controleren of er meldingen over de risico beperking na aanvallen worden weer gegeven. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die controleert of de DDoS Standard-laag is ingeschakeld. Het verschil tussen de functionaliteit van de service lagen kan u helpen bij het selecteren van de beste oplossing voor denial of service-beveiligingen voor uw Azure-omgeving.

- DDoS Protection standaard moet zijn ingeschakeld

## <a name="93165-sc-7-boundary-protection"></a>9.3.16.5 SC-7 boundary Protection

Deze blauw druk helpt u bij het beheren en best uren van de systeem grens door een [Azure Policy](../../../policy/overview.md) definitie toe te wijzen die de aanbevelingen voor de beveiliging van netwerk beveiligings groepen in azure Security Center controleert. Azure Security Center analyseert de verkeers patronen van Internet gerichte virtuele machines en biedt regel aanbevelingen voor de netwerk beveiligings groep om de mogelijke kwets baarheid te verminderen.
Daarnaast wijst deze blauw druk ook beleids definities toe waarmee onbeveiligde eind punten, toepassingen en opslag accounts worden bewaakt. Eind punten en toepassingen die niet zijn beveiligd door een firewall en opslag accounts met onbeperkte toegang, kunnen onbedoelde toegang tot gegevens in het informatie systeem toestaan.

- De regels voor de netwerk beveiligings groep voor virtuele machines die zijn gericht op internet, moeten worden gehard
- Toegang via Internet gericht eind punt moet worden beperkt
- De Nsg's-regels voor webtoepassingen op IaaS moeten een harde verbinding hebben
- Onbeperkte netwerk toegang tot opslag accounts controleren

## <a name="93165-sc-7-3-boundary-protection--access-points"></a>9.3.16.5 SC-7 (3) grens beveiliging | Toegangs punten

Met Just-in-time (JIT) toegang tot virtuele machines wordt het binnenkomende verkeer naar Azure virtual machine vergrendeld, waardoor de bloot stelling aan aanvallen wordt verkleind, terwijl er eenvoudig toegang wordt geboden tot Vm's wanneer dat nodig is. Met toegang tot virtuele JIT-machines kunt u het aantal externe verbindingen met uw resources in azure beperken. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die u helpt bij het bewaken van virtuele machines die just-in-time-toegang kunnen ondersteunen, maar die nog niet zijn geconfigureerd.

- Just-in-time-netwerk toegangs beheer moet worden toegepast op virtuele machines

## <a name="93165-sc-7-4-boundary-protection--external-telecommunications-services"></a>9.3.16.5 SC-7 (4) grens beveiliging | Externe telecommunicatie Services

Met Just-in-time (JIT) toegang tot virtuele machines wordt het binnenkomende verkeer naar Azure virtual machine vergrendeld, waardoor de bloot stelling aan aanvallen wordt verkleind, terwijl er eenvoudig toegang wordt geboden tot Vm's wanneer dat nodig is. Met toegang tot virtuele JIT-machines kunt u uitzonde ringen beheren voor uw Traffic Flow-beleid door de processen voor toegangs aanvragen en-goed keuring te vergemakkelijken. Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die u helpt bij het bewaken van virtuele machines die just-in-time-toegang kunnen ondersteunen, maar die nog niet zijn geconfigureerd.

- Just-in-time-netwerk toegangs beheer moet worden toegepast op virtuele machines

## <a name="96163-sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>9.6.16.3 SC-8 (1) verzen ding van vertrouwelijkheid en integriteit | Cryptografische of alternatieve fysieke beveiliging

Deze blauw druk helpt u om het vertrouwelijke en de integriteit van verzonden informatie te beschermen door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die u helpen bij het bewaken van het cryptografische mechanisme dat is geïmplementeerd voor communicatie protocollen. Zorg ervoor dat de communicatie op de juiste wijze wordt versleuteld, zodat u de vereisten van uw organisatie kunt nagaan of informatie beschermt tegen onbevoegde openbaar making en wijzigingen

- De API-app mag alleen toegankelijk zijn via HTTPS
- Windows-webservers controleren die geen protocollen voor beveiligde communicatie gebruiken
- Vereisten implementeren voor het controleren van Windows-webservers die geen beveiligde communicatie protocollen gebruiken
- Functie-App moet alleen toegankelijk zijn via HTTPS
- Alleen beveiligde verbindingen met uw Redis Cache moeten worden ingeschakeld
- Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld
- Web-App moet alleen toegankelijk zijn via HTTPS

## <a name="93166-sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>9.3.16.6 SC-28 (1) beveiliging van informatie in rust | Cryptografische beveiliging

Deze blauw druk helpt u bij het afdwingen van uw beleid voor het gebruik van cryptograph-besturings elementen om informatie te beveiligen door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die specifieke cryptograph-besturings elementen afdwingen en het gebruik van zwakke cryptografische instellingen te controleren. Als u wilt weten waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, kunt u corrigerende maat regelen nemen om ervoor te zorgen dat bronnen worden geconfigureerd in overeenstemming met uw informatie beveiligings beleid. Met name de beleids definities die door deze blauw drukken worden toegewezen, vereisen versleuteling voor data Lake Storage-accounts. transparante gegevens versleuteling vereisen voor SQL-data bases; en controleren op ontbrekende versleuteling voor SQL-data bases, schijven van virtuele machines en Automation-account variabelen.

- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers
- Geavanceerde gegevens beveiliging implementeren op SQL-servers
- Transparante gegevens versleuteling van SQL DB implementeren
- Schijf versleuteling moet worden toegepast op virtuele machines
- Versleuteling vereisen voor Data Lake Store accounts
- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld

## <a name="93172-si-2-flaw-remediation"></a>9.3.17.2 SI-2-fout herstel

Deze blauw druk helpt u bij het beheren van gegevens systeem fouten door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die ontbrekende systeem updates, problemen met het besturings systeem, SQL-beveiligings problemen en beveiligings problemen met virtuele machines in azure-beveiliging bewaken Projecten. Azure Security Center biedt rapportage mogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligings status van geïmplementeerde Azure-resources. Deze blauw druk wijst ook een beleids definitie toe die ervoor zorgt dat het besturings systeem wordt geïnstalleerd voor schaal sets voor virtuele machines.

- Automatische patching van besturingssysteem installatie kopieën vereisen op Virtual Machine Scale Sets
- Systeem updates op virtuele-machine schaal sets moeten worden geïnstalleerd
- Systeem updates moeten worden geïnstalleerd op uw virtuele machines
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Beveiligings problemen in de beveiligings configuratie op uw virtuele machines moeten worden hersteld
- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken

## <a name="93173-si-3-malicious-code-protection"></a>9.3.17.3 SI-3-beveiliging tegen schadelijke code

Deze blauw druk helpt u bij het beheren van Endpoint Protection, met inbegrip van schadelijke code beveiliging, door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die controleren op ontbrekende Endpoint Protection op virtuele machines in azure Security Center en afdwingen van micro soft antimalware-oplossing op virtuele Windows-machines.

- Standaard micro soft IaaSAntimalware-extensie voor Windows Server implementeren
- Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machine schaal sets
- Ontbrekende Endpoint Protection in Azure Security Center controleren

## <a name="93173-si-3-1-malicious-code-protection--central-management"></a>9.3.17.3 SI-3 (1) schadelijke code beveiliging | Centraal beheer

Deze blauw druk helpt u bij het beheren van Endpoint Protection, met inbegrip van schadelijke code beveiliging, door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die controleren op ontbrekende Endpoint Protection op virtuele machines in azure Security Center. Azure Security Center biedt gecentraliseerde beheer-en rapportage mogelijkheden waarmee u real-time inzicht kunt krijgen in de beveiligings status van geïmplementeerde Azure-resources.

- Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machine schaal sets
- Ontbrekende Endpoint Protection in Azure Security Center controleren

## <a name="93174-si-4-information-system-monitoring"></a>9.3.17.4 SI-4-informatie systeem bewaking

Deze blauw druk helpt u bij het controleren van uw systeem door logboek registratie en gegevens beveiliging in azure-resources te controleren en af te dwingen. Met name aan de beleids regels is het controleren en afdwingen van de implementatie van de Log Analytics agent en verbeterde beveiligings instellingen voor SQL-data bases, opslag accounts en netwerk bronnen toegewezen. Deze mogelijkheden kunnen u helpen bij het detecteren van afwijkend gedrag en indica toren van aanvallen, zodat u de juiste actie kunt ondernemen.

- \[Voor\]beeld: Implementatie van Log Analytics agent controleren-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Log Analytics agent implementatie controleren in VMSS-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: Controleren Log Analytics werk ruimte voor VM-niet-overeenkomend rapport
- \[Voor\]beeld: Log Analytics agent voor Linux-VM Scale Sets implementeren (VMSS)
- \[Voor\]beeld: Log Analytics-agent voor Linux-Vm's implementeren
- \[Voor\]beeld: Log Analytics agent voor Windows-VM Scale Sets (VMSS) implementeren
- \[Voor\]beeld: Log Analytics-agent voor Windows-Vm's implementeren
- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers
- Geavanceerde gegevens beveiliging implementeren op SQL-servers
- Geavanceerde beveiliging tegen bedreigingen implementeren voor opslag accounts
- Controle op SQL-servers implementeren
- Network Watcher implementeren bij het maken van virtuele netwerken
- Detectie van bedreigingen op SQL-servers implementeren

## <a name="93174-si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>9.3.17.4 SI-4 (18) bewaking van informatie systeem | Verkeer analyseren/exfiltration converteren

Advanced Threat Protection voor Azure Storage detecteert ongebruikelijke en mogelijk schadelijke pogingen om opslag accounts te openen of misbruik te maken. Beveiligings waarschuwingen zijn afwijkende toegangs patronen, afwijkende extracten/uploads en verdachte opslag activiteit. Deze indica toren helpen u bij het detecteren van gegevens over het converteren van exfiltration.

- Geavanceerde beveiliging tegen bedreigingen implementeren voor opslag accounts

> [!NOTE]
> De beschik baarheid van specifieke Azure Policy definities kan verschillen in Azure Government en andere nationale Clouds. 

## <a name="next-steps"></a>Volgende stappen

Nu u de controle toewijzing van de 1075 blauw druk hebt gecontroleerd, gaat u naar de volgende artikelen voor meer informatie over de blauw druk en hoe u dit voor beeld implementeert:

> [!div class="nextstepaction"]
> [Blauw druk van IRS 1075-overzicht](./index.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).