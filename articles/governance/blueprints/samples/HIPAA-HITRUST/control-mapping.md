---
title: Voor beelden van HIPAA HITRUST blauw drukken
description: De toewijzing van de voor beelden van HIPAA HITRUST-blauw drukken. Elk besturings element wordt toegewezen aan een of meer Azure-beleids regels die helpen bij de evaluatie.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472308"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>De toewijzing van het HITRUST blauw druk-voor beeld van HIPAA

In het volgende artikel wordt uitgelegd hoe het voor beeld van Azure blauw drukken HIPAA HITRUST wordt toegewezen aan de HIPAA HITRUST-besturings elementen. Zie [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)voor meer informatie over de besturings elementen.

De volgende toewijzingen zijn de **HIPAA HITRUST** -besturings elementen. Gebruik de navigatie aan de rechter kant om rechtstreeks naar een specifieke besturings element koppeling te gaan. Veel van de toegewezen besturings elementen worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md) -initiatief. Als u het complete initiatief wilt bekijken, opent u **beleid** in het Azure Portal en selecteert u de pagina **definities** . Zoek en selecteer vervolgens de **\[Preview\]: audit HIPAA HITRUST Controls** built-in Policy Initiative.

> [!IMPORTANT]
> Elk besturings element hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md) definities. Met deze beleids regels kunt u de naleving van het besturings element [beoordelen](../../../policy/how-to/get-compliance-data.md) . Er is echter vaak geen 1:1-of volledige overeenkomst tussen een besturings element en een of meer beleids regels. Als zodanig is de **naleving** in azure Policy alleen bedoeld voor het beleid zelf. Dit garandeert niet dat u volledig compatibel bent met alle vereisten van een besturings element. Daarnaast bevat de nalevings standaard besturings elementen die niet worden behandeld door Azure Policy definities op dit moment. Daarom is naleving in Azure Policy slechts een gedeeltelijke weer gave van uw algemene nalevings status. De koppelingen tussen de besturings elementen en Azure Policy definities voor dit voor beeld van deze naleving blauw druk kunnen na verloop van tijd veranderen. Als u de wijzigings geschiedenis wilt weer geven, raadpleegt u de [github commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Beheer tegen schadelijke code

Deze blauw druk helpt u bij het beheren van Endpoint Protection, met inbegrip van schadelijke code beveiliging, door [Azure Policy](../../../policy/overview.md) definities toe te wijzen die controleren op ontbrekende Endpoint Protection op virtuele machines in azure Security Center en de oplossing micro soft antimalware afdwingen op virtuele Windows-machines.

- Standaard micro soft IaaS antimalware-extensie voor Windows Server implementeren
- Diagnostische logboeken in batch-accounts moeten worden ingeschakeld
- Er moeten systeemupdates op uw computers zijn geïnstalleerd


## <a name="management-of-removable-media"></a>Beheer van Verwissel bare media

De organisatie, op basis van het niveau van de gegevens classificatie, registreert media (inclusief laptops) voor gebruik, plaatst redelijke beperkingen voor de manier waarop dergelijke media worden gebruikt en biedt een passend niveau van fysieke en logische beveiliging (inclusief versleuteling) voor media die gedekte informatie bevatten tot ze juist zijn vernietigd of verwijderd.

- Versleuteling vereisen voor Data Lake Store accounts
- TDE-beveiliging van SQL Managed instance moet worden versleuteld met uw eigen sleutel
- Schijfversleuteling moet worden toegepast op virtuele machines
- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld


## <a name="information-exchange-policies-and-procedures"></a>Beleid en procedures voor het uitwisselen van informatie

Cloud serviceproviders gebruiken een door de branche herkend virtualisatieplatform en standaard indelingen voor virtualisatie (bijvoorbeeld Open Virtualization Format, OVF) om interoperabiliteit te garanderen en heeft gedocumenteerde aangepaste wijzigingen aangebracht in een Hyper Visor die in gebruik is en alle specifieke virtualisatie hooks die beschikbaar zijn voor de klant beoordeling.

- Vereisten implementeren voor het controleren van Windows-Vm's waarop de opgegeven toepassingen niet zijn geïnstalleerd

## <a name="control-of-operational-software"></a>Beheer van operationele software 

De organisatie identificeert niet-geautoriseerde software op het informatie systeem, met inbegrip van servers, werk stations en laptops, maakt gebruik van een beleid voor toestaan, weigeren per uitzonde ring om de uitvoering van bekende niet-geautoriseerde software op het informatie systeem te verbieden en controleert en werkt de lijst met niet-geautoriseerde software regel matig af, maar niet minder dan jaarlijks.

- \[preview\] controle resultaten van Windows-Vm's configuraties weer geven in beveiligings opties-audit
- \[preview\] controle resultaten van configuraties van Windows-Vm's weer geven in systeem controle beleid-account beheer

## <a name="change-control-procedures"></a>Beheer procedures wijzigen

De integriteit van alle installatie kopieën van virtuele machines wordt op elk moment gewaarborgd door een waarschuwing in te scha kelen en op te nemen voor wijzigingen in installatie kopieën van virtuele machines en ter beschikking te stellen aan de bedrijfs eigenaar (en) en/of klant (en) via elektronische methoden (zoals portals of waarschuwingen) de resultaten van een wijziging of verplaatsing en de volgende validatie van de integriteit van de afbeelding

- \[preview\] controle resultaten van de configuratie van virtuele Windows-machines weer geven in systeem controle beleid-gedetailleerde tracking

## <a name="inventory-of-assets"></a>Inventaris van assets 

Er wordt een inventaris van activa en services onderhouden

- Diagnostische logboeken in Search Services moeten worden ingeschakeld.
- \[preview-\] implementeren van vereisten voor het controleren van Windows-Vm's in beveiligings opties-micro soft-netwerk server
- \[preview-\] implementeren van vereisten voor het controleren van Windows-Vm's in ' Beheersjablonen-Netwerk '

## <a name="control-of-technical-vulnerabilities"></a>Controle van technische beveiligings problemen 

Er bestaat een beveiligde configuratie standaard voor alle systeem-en netwerk onderdelen.

- Controle Virtual Machines zonder nood herstel geconfigureerd
- De evaluatie van beveiligings problemen moet worden ingeschakeld voor uw door SQL beheerde instanties
- Beveiligings probleem moet worden opgelost door een oplossing voor de evaluatie van beveiligings problemen

## <a name="segregation-in-networks"></a>Schei ding in netwerken

De beveiligings gateways van de organisatie (zoals firewalls) dwingen beveiligings beleid af en worden geconfigureerd om verkeer tussen domeinen te filteren, onbevoegde toegang te blok keren en worden gebruikt voor het onderhouden van schei ding tussen interne bekabelde, interne draadloze en externe netwerk segmenten (bijvoorbeeld Internet), waaronder Dmz's en afdwingen van beleid voor toegangs beheer voor elk van de domeinen.

- Automatische inrichting van de agent voor beveiligings bewaking
- Network Watcher implementeren bij het maken van virtuele netwerken

## <a name="input-data-validation"></a>Validatie van invoer gegevens

Voor alle open bare webtoepassingen worden firewalls op toepassings niveau geïmplementeerd om verkeer te beheren. Voor open bare toepassingen die niet zijn gebaseerd op het web, heeft de organisatie een op het netwerk gebaseerde firewall geïmplementeerd die specifiek is voor het toepassings type. Als het verkeer naar de open bare toepassing is versleuteld, bevindt het apparaat zich achter de versleuteling of kan het verkeer voorafgaand aan de analyse worden ontsleuteld.

- \[preview\] controle resultaten van de configuratie van Windows-Vm's in Windows Firewall eigenschappen weer geven


## <a name="network-connection-control"></a>Beheer van netwerk verbindingen

Netwerk verkeer wordt beheerd in overeenstemming met het toegangs beheer beleid van de organisatie via firewall en andere netwerk beperkingen voor elk netwerk toegangs punt of de beheerde interface van de externe telecommunicatie service.

- Toegang via Internet gerichte eind punten moet worden beperkt
- TDE-beveiliging van SQL Managed instance moet worden versleuteld met uw eigen sleutel
- Externe fout opsporing moet worden uitgeschakeld voor API apps

## <a name="network-controls"></a>Netwerk besturings elementen

De organisatie gebruikt beveiligde en versleutelde communicatie kanalen bij het migreren van fysieke servers, toepassingen of gegevens naar gevirtualiseerde servers.

- Schijf versleuteling moet worden toegepast op Vm's
- De TDE-beveiliging van de SQL-server moet zijn versleuteld met uw eigen sleutel
- \[preview\] resultaten van Windows-Vm's weer geven in beveiligings opties-netwerk toegang
- Onbeperkte netwerk toegang tot opslag accounts controleren
- \[preview\] de resultaten van de configuratie van Windows-Vm's in Windows Firewall eigenschappen weer geven
- Diagnostische instellingen implementeren vanuit netwerk beveiligings groepen
- Toegang via Internet gerichte eind punten moet worden beperkt

## <a name="sensitive-system-isolation"></a>Gevoelige systeem isolatie

Gedeelde systeem bronnen (bijvoorbeeld registers, hoofd geheugen, secundaire opslag) worden teruggebracht naar het systeem, beveiligd tegen openbaar making aan andere systemen/toepassingen/gebruikers, en gebruikers kunnen opzettelijk of onbedoeld geen toegang krijgen tot informatie-resten.

- Virtual Machines moeten worden gemigreerd naar nieuwe Azure Resource Manager resources

## <a name="security-of-network-services"></a>Beveiliging van Network Services

Overeengekomen services die door een netwerk serviceprovider/Manager worden verschaft, worden formeel beheerd en bewaakt om ervoor te zorgen dat ze veilig worden ingediend.

- Virtual Machines moeten worden gemigreerd naar nieuwe Azure Resource Manager resources

## <a name="network-routing-control"></a>Routerings beheer voor netwerk

Routerings besturings elementen worden geïmplementeerd via beveiligings gateways (zoals firewalls) die worden gebruikt tussen interne en externe netwerken (bijvoorbeeld het internet en netwerken van derden).

- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines

## <a name="information-exchange-policies-and-procedures"></a>Beleid en procedures voor het uitwisselen van informatie

De organisatie beperkt het gebruik van Portable Storage-Media die door de organisatie worden beheerd door geautoriseerde personen op externe informatie systemen.

- Onbeperkte netwerk toegang tot opslag accounts controleren
- Externe fout opsporing moet worden uitgeschakeld voor webtoepassingen
- De APi-app mag alleen toegankelijk zijn via HTTPS

## <a name="electronic-messaging"></a>E-mail berichten

Goed keuringen worden verkregen vóór het gebruik van externe open bare Services, waaronder Instant Messa ging of het delen van bestanden.

- \[preview\] controle resultaten weer geven van Linux-Vm's waarvoor de machtigingen voor het wachtwoord bestand niet zijn ingesteld op 0644

## <a name="on-line-transactions"></a>Online transacties

De organisatie vereist het gebruik van versleuteling tussen en het gebruik van elektronische hand tekeningen door elk van de partijen bij de trans actie. De organisatie garandeert dat de opslag van de transactie gegevens zich buiten een openbaar toegankelijke omgeving bevindt (bijvoorbeeld op een opslag platform dat op het intranet van de organisatie aanwezig is, en niet wordt bewaard en op een opslag medium rechtstreeks toegankelijk is vanaf internet. Wanneer een vertrouwde instantie wordt gebruikt (bijvoorbeeld voor het verlenen en onderhouden van digitale hand tekeningen en/of digitale certificaten), is de beveiliging geïntegreerd en Inge sloten in het volledige end-to-end-beheer van certificaten en hand tekeningen host.

- Schijf versleuteling moet worden toegepast op Vm's
- \[preview\] controle resultaten weer geven van Windows-Vm's die de opgegeven certificaten niet in vertrouwde basis bevatten

## <a name="password-management"></a>Wachtwoordbeheer

Wacht woorden worden versleuteld tijdens de overdracht en opslag op alle systeem onderdelen.

- \[preview\] controle resultaten weer geven van Windows-Vm's waarvoor de instelling voor wachtwoord complexiteit niet is ingeschakeld

## <a name="user-authentication-for-external-connections"></a>Gebruikers verificatie voor externe verbindingen

Krachtige verificatie methoden zoals multi-factor, RADIUS of Kerberos (voor bevoegde toegang) en CHAP (voor versleuteling van referenties voor inbel methoden) worden geïmplementeerd voor alle externe verbindingen met het netwerk van de organisatie.

- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement
- Just-in-time-netwerk toegangs beheer moet worden toegepast op Vm's

## <a name="user-identification-and-authentication"></a>Gebruikers identificatie en-verificatie

Gebruikers die geprivilegieerde functies (zoals systeem beheer) hebben uitgevoerd, gebruiken afzonderlijke accounts bij het uitvoeren van deze geprivilegieerde functies. Multi-factor Authentication-methoden worden gebruikt in overeenstemming met het organisatie beleid (bijvoorbeeld voor externe netwerk toegang).

- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement
- Just-in-time-netwerk toegangs beheer moet worden toegepast op Vm's

## <a name="privilege-management"></a>Bevoegdheids beheer

Toegang tot beheer functies of beheer consoles voor systemen die als host fungeren voor gevirtualiseerde systemen zijn beperkt tot personeel op basis van het principe van minimale bevoegdheden en worden ondersteund door middel van technische controles.

- Just-in-time-netwerk toegangs beheer moet worden toegepast op Vm's
- \[preview\] op rollen gebaseerde Access Control (RBAC) moet worden gebruikt voor Kubernetes-Services

## <a name="review-of-user-access-rights"></a>Beoordeling van gebruikers toegangs rechten

De organisatie onderhoudt een gedocumenteerde lijst met gemachtigde gebruikers van informatie-assets.

- \[preview\] de resultaten van de Windows-Vm's in beveiligings opties-accounts weer geven

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Externe diagnose-en configuratie poort beveiliging

Poorten, services en soort gelijke toepassingen die zijn geïnstalleerd op een computer of netwerk systemen die niet specifiek vereist zijn voor bedrijfs functionaliteit, worden uitgeschakeld of verwijderd.

- Beheerpoorten moeten worden gesloten op uw virtuele machines
- Beveiligings problemen in de beveiligings configuratie op uw virtuele machines schaal sets moeten worden hersteld

## <a name="audit-logging"></a>Controle logboek registratie

Logboeken van verzonden en ontvangen berichten worden bewaard, inclusief de datum, de tijd, de oorsprong en de bestemming van het bericht, maar niet de inhoud ervan. Controle is altijd beschikbaar wanneer het systeem actief is en traceert, geslaagde/mislukte gegevens toegang, wijzigingen in de systeem beveiligings configuratie, privilege of het gebruik van het hulp programma, alle waarschuwingen die zijn opgetreden, activering en de activering van beveiligings systemen (bijvoorbeeld A/V en ID'S), activering en deactivering van identificatie-en verificatie mechanismen en het maken en verwijderen van objecten op systeem niveau

- Diagnostische logboeken in Event hub moeten worden ingeschakeld
- Systeemupdates op virtuele-machineschaalsets moeten worden geïnstalleerd

## <a name="monitoring-system-use"></a>Systeem gebruik bewaken

Geautomatiseerde systemen die in de hele organisatie omgeving worden geïmplementeerd, worden gebruikt voor het bewaken van belang rijke gebeurtenissen en afwijkende activiteiten, en het analyseren van systeem logboeken waarvan de resultaten regel matig worden gecontroleerd. Bewaking omvat geprivilegieerde bewerkingen, geautoriseerde toegang of pogingen tot onbevoegde toegang, waaronder pogingen om toegang te krijgen tot gedeactiveerde accounts, en systeem waarschuwingen of fouten.

- Diagnostische logboeken in virtuele-machine schaal sets moeten zijn ingeschakeld

## <a name="segregation-of-duties"></a>Schei ding van taken

Schei ding van taken wordt gebruikt om het risico op ongeoorloofde of onbedoelde wijziging van informatie en systemen te beperken. Geen enkele persoon kan geen gegevens systemen openen, wijzigen of gebruiken zonder autorisatie of detectie. Toegang voor personen die verantwoordelijk zijn voor beheer-en toegangs beheer is beperkt tot het minimum dat nodig is op basis van de rol en verantwoordelijkheden van elke gebruiker en deze personen hebben geen toegang tot controle functies die betrekking hebben op deze besturings elementen.

- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement

## <a name="administrator-and-operator-logs"></a>Beheerders-en operator logboeken

De organisatie zorgt ervoor dat de juiste logboek registratie is ingeschakeld om de beheerders activiteiten te controleren. en beoordeelt regel matig de systeem beheerder en de operator Logboeken.

- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement

## <a name="identification-of-risks-related-to-external-parties"></a>Identificatie van Risico's met betrekking tot externe partijen

Externe toegangs verbindingen tussen de organisatie en externe partijen worden versleuteld

- Schijf versleuteling moet worden toegepast op virtuele machines

## <a name="business-continuity-and-risk-assessment"></a>Bedrijfs continuïteit en risico analyse

De organisatie identificeert haar essentiële bedrijfs processen en integreert de vereisten voor het beheer van gegevens beveiliging van bedrijfs continuïteit met andere continuïteits vereisten met betrekking tot dergelijke aspecten van activiteiten, personeel, materialen, Trans Port en faciliteiten.

- \[preview\] controle resultaten van de configuratie van virtuele Windows-machines weer geven in beveiligings opties-herstel console

> [!NOTE]
> De beschik baarheid van specifieke Azure Policy definities kan verschillen in Azure Government en andere nationale Clouds. 

## <a name="next-steps"></a>Volgende stappen

U hebt de beheer toewijzing van het voor beeld van HIPAA HITRUST-blauw drukken gecontroleerd. Ga vervolgens naar de volgende artikelen voor meer informatie over het overzicht en hoe u dit voor beeld implementeert:

> [!div class="next step action"]
> [HITRUST blauw druk-overzicht](./control-mapping.md)
> [HIPAA HITRUST](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
