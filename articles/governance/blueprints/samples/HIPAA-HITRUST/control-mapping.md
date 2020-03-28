---
title: HIPAA HITRUST blueprint sample controls HIPAA HITRUST blueprint sample controls HIPAA HITRUST blueprint sample controls HIPA
description: Controle mapping van de HIPAA HITRUST blueprint samples. Elk besturingselement wordt toegewezen aan een of meer Azure-beleidsregels die helpen bij de beoordeling.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75472308"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Controle mapping van de HIPAA HITRUST blauwdruk monster

In het volgende artikel wordt beschreven hoe het blueprintsample van Azure Blueprints HIPAA HITRUST wordt toegewezen aan de HIPAA HITRUST-besturingselementen. Zie [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)voor meer informatie over de besturingselementen.

De volgende mappings zijn aan de **HIPAA HITRUST** controles. Gebruik de navigatie aan de rechterkant om direct naar een specifieke besturingstoewijzing te springen. Veel van de toegewezen besturingselementen worden geïmplementeerd met een [Azure Policy-initiatief.](../../../policy/overview.md) Als u het volledige initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities.** Zoek en selecteer vervolgens de ** \[Preview\]: Audit HIPAA HITRUST besturingselementen** ingebouwd beleidsinitiatief.

> [!IMPORTANT]
> Elk besturingselement hieronder is gekoppeld aan een of meer [Azure Policy-definities.](../../../policy/overview.md) Met dit beleid u [beoordelen of de](../../../policy/how-to/get-compliance-data.md) controle is nageleefd; Er is echter vaak geen 1:1 of volledige overeenkomst tussen een besturingselement en een of meer beleidsregels. **Compliant** in Azure Policy verwijst daarom alleen naar het beleid zelf. Dit zorgt er niet voor dat u volledig voldoet aan alle vereisten van een controle. Bovendien bevat de nalevingsstandaard besturingselementen die op dit moment niet worden behandeld door azure-beleidsdefinities. Naleving in Azure Policy is daarom slechts een gedeeltelijke weergave van uw algemene nalevingsstatus. De koppelingen tussen besturingselementen en Azure Policy-definities voor dit voorbeeld van nalevingsblauwdrukken kunnen in de loop van de tijd veranderen. Zie de [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md)om de wijzigingsgeschiedenis te bekijken.

## <a name="control-against-malicious-code"></a>Controle tegen schadelijke code

Met deze blauwdruk u de beveiliging van eindpunten beheren, inclusief schadelijke codebeveiliging, door [Azure Policy-definities](../../../policy/overview.md) toe te wijzen die controleren op ontbrekende endpointbeveiliging op virtuele machines in Azure Security Center en de Microsoft-antimalwareoplossing op virtuele Windows-machines af te dwingen.

- Standaard Microsoft IaaS Antimalware-extensie implementeren voor windows-server
- Diagnostische logboeken in Batch-accounts moeten zijn ingeschakeld
- Er moeten systeemupdates op uw computers zijn geïnstalleerd


## <a name="management-of-removable-media"></a>Beheer van verwisselbare media

De organisatie, op basis van het niveau van de gegevensclassificatie, registreert media (inclusief laptops) voorafgaand aan het gebruik, legt redelijke beperkingen op aan de manier waarop dergelijke media worden gebruikt en biedt een passend niveau van fysieke en logische bescherming (inclusief versleuteling) voor media met gedekte informatie totdat deze op de juiste wijze zijn vernietigd of ontsmet.

- Versleuteling vereisen voor Data Lake Store-accounts
- SQL managed instance TDE protector moet worden versleuteld met uw eigen sleutel
- Schijfversleuteling moet worden toegepast op virtuele machines
- Transparante gegevensversleuteling in SQL-databases moet worden ingeschakeld


## <a name="information-exchange-policies-and-procedures"></a>Beleid en procedures voor informatie-uitwisseling

Cloudserviceproviders maken gebruik van een door de industrie erkend virtualisatieplatform en standaardvirtualisatieformaten (bijvoorbeeld Open Virtualisatie-indeling, OVF) om de interoperabiliteit te waarborgen en heeft aangepaste wijzigingen gedocumenteerd die zijn aangebracht in elke hypervisor die in gebruik is en alle oplossingsspecifieke virtualisatiehaken beschikbaar voor klantbeoordeling.

- Vereisten implementeren om Windows VM's te controleren die niet zijn geïnstalleerd op de opgegeven toepassingen

## <a name="control-of-operational-software"></a>Controle van operationele software 

De organisatie identificeert ongeautoriseerde software op het informatiesysteem, waaronder servers, werkstations en laptops, hanteert een allow-all, deny-by-exception beleid om de uitvoering van bekende ongeautoriseerde software op het informatiesysteem te verbieden, en beoordeelt en actualiseert de lijst van ongeautoriseerde software periodiek, maar niet minder dan jaarlijks.

- \[Voorbeeld\] Van de controleresultaten van Windows VM's weergeven in 'Beveiligingsopties-audit'
- \[Preview\] Bekijk de controleresultaten van Windows VM's weergeven in 'Systeemcontrolebeleid- Accountbeheer'

## <a name="change-control-procedures"></a>Controleprocedures wijzigen

De integriteit van alle afbeeldingen van virtuele machines wordt te allen tijde gewaarborgd door de resultaten van een wijziging of verplaatsing en de daaropvolgende validatie van de integriteit van de afbeelding (bijvoorbeeld portals of waarschuwingen) via elektronische methoden (bijvoorbeeld portals of waarschuwingen) te registreren en een waarschuwing te geven voor eventuele wijzigingen in afbeeldingen van virtuele machines en ter beschikking te stellen aan de bedrijfseigenaar(s) en/of de klant(en).

- \[Preview\] Bekijk de controleresultaten van de configuratie van Windows VM's weergeven in 'Systeemcontrolebeleid -Gedetailleerde tracking'

## <a name="inventory-of-assets"></a>Inventaris van activa 

Een inventaris van activa en diensten wordt bijgehouden

- Diagnostische logboeken in zoekservices moeten zijn ingeschakeld.
- \[Voorbeeld\] van implementatievereisten voor het controleren van Windows VM's-configuraties in 'Beveiligingsopties- Microsoft Network Server'
- \[Voorbeeld\] van implementatievereisten bekijken om Windows VM's-configuraties te controleren in 'Beheersjablonen-netwerk'

## <a name="control-of-technical-vulnerabilities"></a>Controle van technische kwetsbaarheden 

Er bestaat een geharde configuratiestandaard voor alle systeem- en netwerkcomponenten.

- Virtuele machines controleren zonder herstel van noodgevallen geconfigureerd
- Kwetsbaarheidsbeoordeling moet worden ingeschakeld op uw SQL-beheerde exemplaren
- Kwetsbaarheid moet worden verholpen door een oplossing voor kwetsbaarheidsbeoordeling

## <a name="segregation-in-networks"></a>Segregatie in netwerken

De beveiligingsgateways van de organisatie (bijvoorbeeld firewalls) handhaven beveiligingsbeleid en zijn geconfigureerd om verkeer tussen domeinen te filteren, ongeautoriseerde toegang te blokkeren en worden gebruikt om de scheiding tussen interne bedrade, interne draadloze en externe netwerksegmenten (bijvoorbeeld het internet) waaronder DMZ's en afdwingbeleid voor toegangscontrole voor elk van de domeinen.

- Automatische inrichting van de agent voor veiligheidsbewaking
- Netwerkwatcher implementeren wanneer virtuele netwerken worden gemaakt

## <a name="input-data-validation"></a>Validatie van invoergegevens

Voor openbare webtoepassingen worden firewalls op toepassingsniveau geïmplementeerd om het verkeer te beheren. Voor toepassingen die niet op het web zijn gericht, heeft de organisatie een netwerkgebaseerde firewall geïmplementeerd die specifiek is voor het toepassingstype. Als het verkeer naar de openbare toepassing is versleuteld, zit het apparaat achter de versleuteling of is het in staat om het verkeer te decoderen voorafgaand aan de analyse.

- \[Voorbeeld\] Controleresultaten weergeven van de configuratie van Windows VM's in 'Eigenschappen van Windows Firewall'


## <a name="network-connection-control"></a>Beheer van netwerkverbindingen

Netwerkverkeer wordt beheerd in overeenstemming met het toegangscontrolebeleid van de organisatie via firewall- en andere netwerkgerelateerde beperkingen voor de beheerde interface van elk netwerktoegangspunt of externe telecommunicatieservice.

- Toegang via internetgerichte eindpunten moet worden beperkt
- SQL managed instance TDE protector moet worden versleuteld met uw eigen sleutel
- Externe foutopsporing moet worden uitgeschakeld voor API-apps

## <a name="network-controls"></a>Netwerkbesturingselementen

De organisatie maakt gebruik van beveiligde en versleutelde communicatiekanalen bij het migreren van fysieke servers, toepassingen of gegevens naar gevirtualiseerde servers.

- Schijfversleuteling moet worden toegepast op VM's
- SQL server TDE protector moet worden versleuteld met uw eigen sleutel
- \[Preview\] Bekijk de resultaten van audits weergeven van Windows VM's in 'Beveiligingsopties- Netwerktoegang'
- Onbeperkte netwerktoegang tot opslagaccounts controleren
- \[Preview\] De resultaten van audits van Windows VM's weergeven in 'Windows Firewall-eigenschappen'
- Diagnostische instellingen implementeren vanuit netwerkbeveiligingsgroepen
- Toegang via internetgerichte eindpunten moet worden beperkt

## <a name="sensitive-system-isolation"></a>Gevoelige systeemisolatie

Gedeelde systeembronnen (bijvoorbeeld registers, hoofdgeheugen, secundaire opslag) worden weer in het systeem vrijgegeven, beschermd tegen openbaarmaking aan andere systemen/toepassingen/gebruikers, en gebruikers kunnen niet opzettelijk of onbedoeld toegang krijgen tot informatieresten.

- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-bronnen

## <a name="security-of-network-services"></a>Beveiliging van netwerkdiensten

Overeengekomen diensten van een netwerkserviceprovider/-manager worden formeel beheerd en gecontroleerd om ervoor te zorgen dat ze veilig worden geleverd.

- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-bronnen

## <a name="network-routing-control"></a>Beheer van netwerkroutering

Routeringsbesturingselementen worden geïmplementeerd via beveiligingsgateways (bijvoorbeeld firewalls) die worden gebruikt tussen interne en externe netwerken (bijvoorbeeld het internet en netwerken van derden).

- Adaptieve toepassingsbesturingselementen moeten worden ingeschakeld op virtuele machines

## <a name="information-exchange-policies-and-procedures"></a>Beleid en procedures voor informatie-uitwisseling

De organisatie beperkt het gebruik van door de organisatie gecontroleerde draagbare opslagmedia door geautoriseerde personen op externe informatiesystemen.

- Onbeperkte netwerktoegang tot opslagaccounts controleren
- Externe foutopsporing moet worden uitgeschakeld voor webtoepassingen
- APi-app mag alleen toegankelijk zijn via HTTPS

## <a name="electronic-messaging"></a>Elektronische berichten

Goedkeuringen worden verkregen voordat externe openbare diensten worden gebruikt, waaronder instant messaging of het delen van bestanden.

- \[Voorbeeld\] Controleresultaten weergeven van Linux VM's die niet zijn ingesteld op 0644

## <a name="on-line-transactions"></a>On-line transacties

De organisatie vereist het gebruik van encryptie tussen, en het gebruik van elektronische handtekeningen door, elk van de partijen die betrokken zijn bij de transactie. De organisatie zorgt ervoor dat de opslag van de transactiegegevens zich buiten openbaar toegankelijke omgevingen bevindt (bijvoorbeeld op een opslagplatform dat op het intranet van de organisatie bestaat) en niet wordt bewaard en blootgesteld op een opslagmedium dat rechtstreeks toegankelijk is vanaf het internet.Wanneer een vertrouwde autoriteit wordt gebruikt (bijvoorbeeld voor het uitgeven en onderhouden van digitale handtekeningen en/of digitale certificaten), is beveiliging geïntegreerd en ingebed in het gehele end-to-end certificaat/handtekeningbeheer Proces.

- Schijfversleuteling moet worden toegepast op VM's
- \[Voorbeeld\] van controleresultaten weergeven van Windows VM's die de opgegeven certificaten niet bevatten in vertrouwde root

## <a name="password-management"></a>Wachtwoordbeheer

Wachtwoorden worden versleuteld tijdens verzending en opslag op alle systeemonderdelen.

- \[Voorbeeld\] Van een voorbeeld Controleresultaten weergeven van windows VM's die de instelling voor wachtwoordcomplexiteit niet hebben ingeschakeld

## <a name="user-authentication-for-external-connections"></a>Gebruikersverificatie voor externe verbindingen

Sterke verificatiemethoden zoals multi-factor, Radius of Kerberos (voor bevoorrechte toegang) en CHAP (voor versleuteling van referenties voor inbelmethoden) worden geïmplementeerd voor alle externe verbindingen met het organisatienetwerk.

- MFA moet accounts zijn ingeschakeld met schrijfmachtigingen voor uw abonnement
- Just In Time-netwerktoegangscontrole moet worden toegepast op VM's

## <a name="user-identification-and-authentication"></a>Gebruikersidentificatie en -verificatie

Gebruikers die bevoorrechte functies hebben uitgevoerd (bijvoorbeeld systeembeheer) gebruiken afzonderlijke accounts bij het uitvoeren van deze bevoorrechte functies. Multi-factor authenticatie methoden worden gebruikt in overeenstemming met het organisatiebeleid, (bijvoorbeeld voor externe netwerktoegang).

- MFA moet accounts zijn ingeschakeld met schrijfmachtigingen voor uw abonnement
- Just In Time-netwerktoegangscontrole moet worden toegepast op VM's

## <a name="privilege-management"></a>Privilege Management

Toegang tot beheerfuncties of administratieve consoles voor systemen die gevirtualiseerde systemen hosten, is beperkt tot personeel dat is gebaseerd op het principe van het minste voorrecht en wordt ondersteund door technische controles.

- Just In Time-netwerktoegangscontrole moet worden toegepast op VM's
- \[Preview\] Role Based Access Control (RBAC) moet worden gebruikt op Kubernetes-services

## <a name="review-of-user-access-rights"></a>Controle van toegangsrechten voor gebruikers

De organisatie houdt een gedocumenteerde lijst bij van geautoriseerde gebruikers van informatiemiddelen.

- \[Preview\] Bekijk de resultaten van audits weergeven van Windows VM's in 'Beveiligingsopties- accounts'

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Beveiliging van diagnostische en configuratiepoort op afstand

Poorten, services en soortgelijke toepassingen die zijn geïnstalleerd op een computer of netwerksystemen, die niet specifiek vereist zijn voor zakelijke functionaliteit, worden uitgeschakeld of verwijderd.

- Beheerpoorten moeten worden gesloten op uw virtuele machines
- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machines schaalsets moeten worden verholpen

## <a name="audit-logging"></a>Controlelogboekregistratie

Logboeken van verzonden en ontvangen berichten worden bijgehouden, inclusief de datum, tijd, oorsprong en bestemming van het bericht, maar niet de inhoud ervan. Auditing is altijd beschikbaar terwijl het systeem actief is en belangrijke gebeurtenissen, succes/mislukte gegevenstoegang, wijzigingen in de systeembeveiliging, geprivilegieerd of gebruik van het nutsgebruik, alle alarmen die worden geactiveerd, activering en deactivering van beveiligingssystemen (bijvoorbeeld A/V en IDS), activering en deactivering van identificatie- en verificatiemechanismen en het aanmaken en verwijderen van systeemobjecten bijhoudt.

- Diagnostische logboeken in gebeurtenishub moeten zijn ingeschakeld
- Systeemupdates op virtuele machineschaalsets moeten worden geïnstalleerd

## <a name="monitoring-system-use"></a>Gebruik van bewakingssysteem

Geautomatiseerde systemen die in de hele omgeving van de organisatie worden geïmplementeerd, worden gebruikt om belangrijke gebeurtenissen en afwijkende activiteiten te controleren en systeemlogboeken te analyseren, waarvan de resultaten regelmatig worden beoordeeld. Monitoring omvat geprivilegieerde bewerkingen, geautoriseerde toegang of ongeautoriseerde toegangspogingen, waaronder pogingen om toegang te krijgen tot gedeactiveerde accounts en systeemwaarschuwingen of -fouten.

- Diagnostische logboeken in virtuele machineschaalsets moeten zijn ingeschakeld

## <a name="segregation-of-duties"></a>Scheiding van taken

Scheiding van rechten wordt gebruikt om het risico van ongeoorloofde of onbedoelde wijziging van informatie en systemen te beperken. Geen enkele persoon is in staat om toegang te krijgen tot, te wijzigen of informatiesystemen te gebruiken zonder autorisatie of detectie. De toegang voor personen die verantwoordelijk zijn voor het beheer en de toegangscontroles is beperkt tot het minimum dat nodig is op basis van de rol en verantwoordelijkheden van elke gebruiker en deze personen hebben geen toegang tot auditfuncties die verband houden met deze controles.

- MFA moet accounts zijn ingeschakeld met schrijfmachtigingen voor uw abonnement

## <a name="administrator-and-operator-logs"></a>Logboeken beheerder en operator

De organisatie zorgt ervoor dat een goede logging is ingeschakeld om beheerdersactiviteiten te controleren; en controleert regelmatig systeembeheerders- en operatorlogboeken.

- MFA moet accounts zijn ingeschakeld met schrijfmachtigingen voor uw abonnement

## <a name="identification-of-risks-related-to-external-parties"></a>Identificatie van risico's voor externe partijen

Externe toegangsverbindingen tussen de organisatie en externe partijen worden versleuteld

- Schijfversleuteling moet worden toegepast op virtuele machines

## <a name="business-continuity-and-risk-assessment"></a>Bedrijfscontinuïteit en risicobeoordeling

De organisatie identificeert haar kritieke bedrijfsprocessen en integreert de vereisten voor informatiebeveiliging beheer van bedrijfscontinuïteit met andere continuïteitsvereisten met betrekking tot aspecten zoals bedrijfsvoering, personeel, materialen, transport en faciliteiten.

- \[Preview\] Bekijk de controleresultaten van de configuratie van Windows VM's weergeven in 'Beveiligingsopties- herstelconsole'

> [!NOTE]
> De beschikbaarheid van specifieke Azure Policy-definities kan variëren in Azure Government en andere nationale clouds. 

## <a name="next-steps"></a>Volgende stappen

Je hebt de controlemapping van het HIPAA HITRUST-blauwdrukmonster bekeken. Ga vervolgens naar de volgende artikelen voor meer informatie over het overzicht en hoe u dit voorbeeld implementeert:

> [!div class="next step action"]
> [HIPAA HITRUST blauwdruk - Overzicht](./control-mapping.md)
> [HIPAA HITRUST blauwdruk - Deploy steps](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van de blauwdruk](../../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../../concepts/resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
