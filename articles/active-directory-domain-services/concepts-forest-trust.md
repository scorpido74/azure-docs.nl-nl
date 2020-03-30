---
title: Hoe vertrouwensrelaties werken voor Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over hoe forestvertrouwensrelaties werken met Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 8b79e0fb24c15d2e9f16640e90d62f7df5c21f32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233699"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Hoe vertrouwensrelaties werken voor resourceforests in Azure Active Directory Domain Services

Active Directory Domain Services (AD DS) biedt beveiliging voor meerdere domeinen of forests via domein- en forestvertrouwensrelaties. Voordat verificatie kan plaatsvinden tussen vertrouwensrelaties, moet Windows eerst controleren of het domein dat wordt aangevraagd door een gebruiker, computer of service een vertrouwensrelatie heeft met het domein van het aanvragende account.

Als u deze vertrouwensrelatie wilt controleren, berekent het Windows-beveiligingssysteem een vertrouwenspad tussen de domeincontroller (DC) voor de server die de aanvraag ontvangt en een DC in het domein van het aanvragende account.

De toegangscontrolemechanismen van AD DS en het windows gedistribueerde beveiligingsmodel bieden een omgeving voor de werking van domein- en forestvertrouwensrelaties. Om deze vertrouwensrelaties goed te laten werken, moet elke resource of computer een direct vertrouwenspad naar een DC hebben in het domein waarin deze zich bevindt.

Het vertrouwenspad wordt geïmplementeerd door de Net Logon-service met behulp van een RPC-verbinding (Authenticated Remote Procedure Call) met de vertrouwde domeinautoriteit. Een beveiligd kanaal strekt zich ook uit tot andere AD DS-domeinen via interdomeinvertrouwensrelaties. Dit beveiligde kanaal wordt gebruikt om beveiligingsinformatie te verkrijgen en te verifiëren, waaronder beveiligings-id's (SID's) voor gebruikers en groepen.

## <a name="trust-relationship-flows"></a>Vertrouwensrelatiestromen

De stroom van beveiligde communicatie over vertrouwensrelaties bepaalt de elasticiteit van een vertrouwensrelatie. Hoe u een vertrouwensrelatie maakt of configureert, bepaalt hoe ver de communicatie zich binnen of over forests uitstrekt.

De stroom van communicatie over vertrouwensrelaties wordt bepaald door de richting van het vertrouwen. Vertrouwensrelaties kunnen eenrichtingsverkeer of tweerichtingsverkeer zijn en kunnen transitief of niet-transitief zijn.

In het volgende diagram ziet u dat alle domeinen in *Boom 1* en *Boom 2* standaard tijdelijke vertrouwensrelaties hebben. Als gevolg hiervan hebben gebruikers in *Boom 1* toegang tot bronnen in domeinen in *Boom 2* en kunnen gebruikers in Boom *1* toegang krijgen tot bronnen in *Structuur 2*, wanneer de juiste machtigingen aan de resource zijn toegewezen.

![Diagram van vertrouwensrelaties tussen twee forests](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Eenrichtingsverkeer en tweerichtingsvertrouwen

Vertrouwensrelaties bieden toegang tot resources eenrichtingsverkeer of tweerichtingsverkeer.

Een eenrichtingsvertrouwensrelatie is een unidirectioneel verificatiepad dat is gemaakt tussen twee domeinen. In een eenrichtingsvertrouwensrelatie tussen *domein A* en *domein B*hebben gebruikers in *domein A* toegang tot bronnen in *domein B.* Gebruikers in *Domein B* hebben echter geen toegang tot bronnen in *domein A.*

Sommige eenrichtingsvertrouwensrelaties kunnen niet-transitief of transitief zijn, afhankelijk van het type vertrouwen dat wordt gemaakt.

In een tweerichtingsvertrouwensrelatie vertrouwt *Domein A* *domein B* en *domein B* vertrouwt *domein A.* Deze configuratie betekent dat verificatieaanvragen tussen de twee domeinen in beide richtingen kunnen worden doorgegeven. Sommige tweerichtingsrelaties kunnen niet-transitief of transitief zijn, afhankelijk van het type vertrouwen dat wordt gemaakt.

Alle domeinvertrouwensrelaties in een AD DS-forest zijn tweerichtingsvertrouwensrelaties. Wanneer een nieuw onderliggend domein wordt gemaakt, wordt er automatisch een tijdelijke vertrouwensrelatie in twee richtingen gemaakt tussen het nieuwe onderliggende domein en het bovenliggende domein.

### <a name="transitive-and-non-transitive-trusts"></a>Transitieve en niet-transitieve trusts

Transitiviteit bepaalt of een vertrouwensrelatie kan worden uitgebreid buiten de twee domeinen waarmee het is gevormd.

* Een transitieve vertrouwensrelatie kan worden gebruikt om vertrouwensrelaties met andere domeinen uit te breiden.
* Een niet-transitieve vertrouwensrelatie kan worden gebruikt om vertrouwensrelaties met andere domeinen te weigeren.

Elke keer dat u een nieuw domein in een forest maakt, wordt er automatisch een tijdelijke vertrouwensrelatie in twee richtingen gemaakt tussen het nieuwe domein en het bovenliggende domein. Als onderliggende domeinen aan het nieuwe domein worden toegevoegd, stroomt het vertrouwenspad omhoog door de domeinhiërarchie en breidt het oorspronkelijke vertrouwenspad uit dat is gemaakt tussen het nieuwe domein en het bovenliggende domein. Transitieve vertrouwensrelaties stromen omhoog door een domeinstructuur terwijl deze wordt gevormd, waardoor transitieve vertrouwensrelaties ontstaan tussen alle domeinen in de domeinstructuur.

Verificatieaanvragen volgen deze vertrouwenspaden, zodat accounts uit elk domein in het forest kunnen worden geverifieerd door een ander domein in het forest. Met één aanmeldingsproces hebben accounts met de juiste machtigingen toegang tot bronnen in elk domein in het forest.

## <a name="forest-trusts"></a>Forestvertrouwensrelaties

Forestvertrouwensrelaties helpen u bij het beheren van een gesegmenteerde AD DS-infrastructuur en ondersteunen toegang tot bronnen en andere objecten in meerdere forests. Forest trusts zijn nuttig voor dienstverleners, bedrijven die fusies of overnames ondergaan, collaboratieve business extranetten en bedrijven die op zoek zijn naar een oplossing voor administratieve autonomie.

Met forestvertrouwensrelaties u twee verschillende forests koppelen om een eenrichtings- of tweerichtingsvertrouwensrelatie te vormen. Met een forestvertrouwensrelatie kunnen beheerders twee AD DS-forests verbinden met één vertrouwensrelatie om een naadloze verificatie- en autorisatie-ervaring in de forests te bieden.

Een forestvertrouwensrelatie kan alleen worden gemaakt tussen een forestrootdomein in het ene forest en een forestrootdomein in een ander forest. Forestvertrouwensrelaties kunnen alleen tussen twee forests worden gemaakt en kunnen niet impliciet worden uitgebreid tot een derde bos. Dit gedrag betekent dat als er een forestvertrouwensrelatie wordt gemaakt tussen *Forest 1* en *Forest 2*en een andere forestvertrouwensrelatie wordt gemaakt tussen Forest *2* en *Forest 3*, Forest *1* geen impliciete vertrouwensrelatie heeft met *Forest 3*.

In het volgende diagram ziet u twee afzonderlijke forestvertrouwensrelaties tussen drie AD DS-forests in één organisatie.

![Diagram met forestvertrouwensrelaties binnen één organisatie](./media/concepts-forest-trust/forest-trusts.png)

Deze voorbeeldconfiguratie biedt de volgende toegang:

* Gebruikers in *Forest 2* hebben toegang tot bronnen in elk domein in *Forest 1* of Forest *3*
* Gebruikers in *Forest 3* hebben toegang tot bronnen in elk domein in *Forest 2*
* Gebruikers in *Forest 1* hebben toegang tot bronnen in elk domein in *Forest 2*

Met deze configuratie kunnen gebruikers in *Forest 1* geen toegang krijgen tot bronnen in *Forest 3* of vice versa. Om gebruikers in zowel *Forest 1* als *Forest 3* in staat te stellen bronnen te delen, moet er een transitief vertrouwen in twee richtingen tussen de twee forests worden gecreëerd.

Als er een eenrichtingsbosvertrouwensrelatie wordt gemaakt tussen twee forests, kunnen leden van het vertrouwde forest resources gebruiken die zich in het vertrouwende forest bevinden. Het vertrouwen werkt echter slechts in één richting.

Wanneer er bijvoorbeeld een eenrichtingsvertrouwensrelatie wordt gemaakt tussen *Forest 1* (het vertrouwde forest) en *Forest 2* (het vertrouwende forest):

* Leden van *Forest 1* hebben toegang tot bronnen in *Forest 2.*
* Leden van *Forest 2* hebben geen toegang tot bronnen in *Forest 1* met dezelfde vertrouwensrelatie.

> [!IMPORTANT]
> Azure AD Domain Services-bronforest ondersteunt alleen een eenrichtingsforestvertrouwensrelatie voor on-premises Active Directory.

### <a name="forest-trust-requirements"></a>Vereisten voor forestvertrouwensrelaties

Voordat u een forestvertrouwensrelatie maken, moet u controleren of u over de juiste DNS-infrastructuur (Domain Name System) beschikt. Forestvertrouwensrelaties kunnen alleen worden gemaakt wanneer een van de volgende DNS-configuraties beschikbaar is:

* Eén root DNS-server is de hoofd-DNS-server voor beide forest DNS-naamruimten - de hoofdzone bevat delegaties voor elk van de DNS-naamruimten en de hoofdhints van alle DNS-servers bevatten de hoofd-DNS-server.
* Als er geen gedeelde hoofd-DNS-server is en de hoofdDNS-servers voor elke forest DNS-naamruimte DNS-voorwaardelijke doorstuurers gebruiken voor elke DNS-naamruimte om query's voor namen in de andere naamruimte te routeren.

    > [!IMPORTANT]
    > Het bronforest van Azure AD Domain Services moet deze DNS-configuratie gebruiken. Het hosten van een andere DNS-naamruimte dan de DNS-naamruimte van het bronforest is geen functie van Azure AD Domain Services. Voorwaardelijke expediteurs is de juiste configuratie.

* Als er geen gedeelde hoofd-DNS-server is en de hoofdDNS-servers voor elke dns-naamruimte in het forest dns-secundaire zones gebruiken, worden in elke DNS-naamruimte de naamruimte geconfigureerd om query's voor namen in de andere naamruimte te routeren.

Als u een forestvertrouwensrelatie wilt maken, moet u lid zijn van de groep Domeinbeheerders (in het foresthoofddomein) of de groep Ondernemingsadministrators in Active Directory. Aan elke vertrouwensrelatie wordt een wachtwoord toegewezen dat de beheerders in beide forests moeten kennen. Leden van Enterprise Admins in beide forests kunnen de vertrouwensrelaties in beide forests tegelijk maken en in dit scenario wordt een wachtwoord dat cryptografisch willekeurig is automatisch gegenereerd en geschreven voor beide forests.

De uitgaande forestvertrouwensrelatie voor Azure AD Domain Services wordt gemaakt in de Azure-portal. U maakt de vertrouwensrelatie niet handmatig met het beheerde domein zelf. De binnenkomende forestvertrouwensrelatie moet worden geconfigureerd door een gebruiker met de eerder genoemde bevoegdheden in de on-premises Active Directory.

## <a name="trust-processes-and-interactions"></a>Vertrouwensprocessen en interacties

Veel interdomein- en interforesttransacties zijn afhankelijk van domein- of forestvertrouwensrelaties om verschillende taken uit te voeren. In deze sectie worden de processen en interacties beschreven die optreden wanneer resources worden geopend voor vertrouwensrelaties en worden verificatieverwijzingen geëvalueerd.

### <a name="overview-of-authentication-referral-processing"></a>Overzicht van verificatieverwijzingsverwerking

Wanneer een verzoek om verificatie naar een domein wordt verwezen, moet de domeincontroller in dat domein bepalen of er een vertrouwensrelatie bestaat met het domein waaruit de aanvraag afkomstig is. De richting van de vertrouwensrelatie en of de vertrouwensrelatie transitief of niet-transitief is, moet ook worden bepaald voordat de gebruiker wordt geverifieerd om toegang te krijgen tot bronnen in het domein. Het verificatieproces tussen vertrouwde domeinen is afhankelijk van het gebruikte verificatieprotocol. De Kerberos V5- en NTLM-protocollen verwerken verwijzingen voor verificatie naar een domein anders

### <a name="kerberos-v5-referral-processing"></a>Kerberos V5 Referral Processing

Het Kerberos V5-verificatieprotocol is afhankelijk van de Net Logon-service voor domeincontrollers voor clientverificatie en autorisatiegegevens. Het Kerberos-protocol maakt verbinding met een online Key Distribution Center (KDC) en het Active Directory-accountarchief voor sessietickets.

Het Kerberos-protocol maakt ook gebruik van vertrouwensrelaties voor cross-realm-ticket-granting services (TGS) en om Privilege Attribute Certificates (PACs) te valideren via een beveiligd kanaal. Het Kerberos-protocol voert cross-realm-verificatie alleen uit met kerberos-gebieden met een niet-Windows-merkbesturingssysteem en hoeft niet te communiceren met de Net Logon-service.

Als de client Kerberos V5 gebruikt voor verificatie, vraagt deze een ticket naar de server in het doeldomein van een domeincontroller in het accountdomein. De Kerberos KDC fungeert als een vertrouwde tussenpersoon tussen de client en de server en biedt een sessiesleutel waarmee de twee partijen elkaar kunnen verifiëren. Als het doeldomein verschilt van het huidige domein, volgt de KDC een logisch proces om te bepalen of een verificatieaanvraag kan worden doorverwezen:

1. Wordt het huidige domein rechtstreeks vertrouwd door het domein van de server dat wordt aangevraagd?
    * Zo ja, stuur de client een verwijzing naar het gevraagde domein.
    * Zo nee, ga dan naar de volgende stap.

2. Bestaat er een transitieve vertrouwensrelatie tussen het huidige domein en het volgende domein op het vertrouwenspad?
    * Zo ja, stuur de client een verwijzing naar het volgende domein op het vertrouwenspad.
    * Zo nee, stuur de klant een aanmeldinggeweigerd bericht.

### <a name="ntlm-referral-processing"></a>NTLM-verwijzingsverwerking

Het NTLM-verificatieprotocol is afhankelijk van de Net Logon-service voor domeincontrollers voor clientverificatie- en autorisatiegegevens. Dit protocol verifieert clients die geen Kerberos-verificatie gebruiken. NTLM gebruikt vertrouwensrelaties om verificatieverzoeken tussen domeinen door te geven.

Als de client NTLM gebruikt voor verificatie, gaat het oorspronkelijke verzoek om verificatie rechtstreeks van de client naar de resourceserver in het doeldomein. Deze server creëert een uitdaging waarop de client reageert. De server stuurt vervolgens het antwoord van de gebruiker naar een domeincontroller in het domein van het computeraccount. Deze domeincontroller controleert het gebruikersaccount op de database met beveiligingsaccounts.

Als het account niet in de database bestaat, bepaalt de domeincontroller of de verificatie van doorgeefverificatie moet worden uitgevoerd, de aanvraag moet worden doorgestuurd of de aanvraag moet worden verwijderd met behulp van de volgende logica:

1. Heeft het huidige domein een directe vertrouwensrelatie met het domein van de gebruiker?
    * Zo ja, dan stuurt de domeincontroller de referenties van de client naar een domeincontroller in het domein van de gebruiker voor pass-through-verificatie.
    * Zo nee, ga dan naar de volgende stap.

2. Heeft het huidige domein een transitieve vertrouwensrelatie met het domein van de gebruiker?
    * Zo ja, geef de verificatieaanvraag door aan het volgende domein in het vertrouwenspad. Deze domeincontroller herhaalt het proces door de referenties van de gebruiker te controleren op basis van de database met eigen beveiligingsaccounts.
    * Zo nee, stuur de klant een aanmeldinggeweigerd bericht.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Kerberos-gebaseerde verwerking van verificatieaanvragen via forestvertrouwensrelaties

Wanneer twee forests zijn verbonden door een forestvertrouwensrelatie, kunnen verificatieverzoeken die worden gedaan met behulp van de Kerberos V5- of NTLM-protocollen worden gerouteerd tussen forests om toegang te bieden tot bronnen in beide forests.

Wanneer een forestvertrouwensrelatie voor het eerst wordt ingesteld, verzamelt elk forest alle vertrouwde naamruimten in het partnerforest en slaat de informatie op in een [vertrouwd domeinobject.](#trusted-domain-object) Vertrouwde naamruimten omvatten domeinnamen van domeinnamen, upn-achtervoegsels (User Principal Name), SPN-achtervoegsels (Service Principal Name) en sid-naamruimten (Security ID) die in het andere forest worden gebruikt. TDO-objecten worden gerepliceerd naar de globale catalogus.

Voordat verificatieprotocollen het forestvertrouwenspad kunnen volgen, moet de hoofdnaam van de serviceclient van de resourcecomputer worden opgelost naar een locatie in het andere forest. Een SPN kan een van de volgende:

* De DNS-naam van een host.
* De DNS-naam van een domein.
* De voorname naam van een object serviceverbindingspunt.

Wanneer een werkstation in het ene forest toegang probeert te krijgen tot gegevens op een resourcecomputer in een ander forest, neemt het Kerberos-verificatieproces contact op met de domeincontroller voor een serviceticket naar de SPN van de resourcecomputer. Zodra de domeincontroller de globale catalogus opvraagt en bepaalt dat de SPN zich niet in hetzelfde forest bevindt als de domeincontroller, stuurt de domeincontroller een verwijzing voor het bovenliggende domein terug naar het werkstation. Op dat moment vraagt het werkstation het bovenliggende domein voor het serviceticket en blijft het de verwijzingsketen volgen totdat het domein is bereikt waar de bron zich bevindt.

In het volgende diagram en de volgende stappen vindt u een gedetailleerde beschrijving van het Kerberos-verificatieproces dat wordt gebruikt wanneer computers met Windows toegang proberen te krijgen tot bronnen vanaf een computer in een ander forest.

![Diagram van het Kerberos-proces over een forestvertrouwensrelatie](media/concepts-forest-trust/kerberos-over-forest-trust-process.png)

1. *User1* logt in op *Workstation1* met referenties uit het *europe.tailspintoys.com* domein. De gebruiker probeert vervolgens toegang te krijgen tot een gedeelde bron op *FileServer1* in het *usa.wingtiptoys.com* forest.

2. *Workstation1* neemt contact op met de Kerberos KDC op een domeincontroller in het domein *ChildDC1*en vraagt een serviceticket aan voor de *FileServer1* SPN.

3. *ChildDC1* vindt de SPN niet in de domeindatabase en queriet de globale catalogus om te zien of domeinen in het *tailspintoys.com* forest deze SPN bevatten. Omdat een globale catalogus beperkt is tot een eigen forest, wordt het SPN niet gevonden.

    De globale catalogus controleert vervolgens de database op informatie over forestvertrouwensrelaties die met het forest zijn ingesteld. Als deze wordt gevonden, worden de naamachtervoegsels in het vertrouwde domeinobject Forest Trust (TDO) vergeleken met het achtervoegsel van het doel SPN om een overeenkomst te vinden. Zodra een overeenkomst is gevonden, biedt de globale catalogus een routeringshint terug naar *ChildDC1*.

    Routeringstips helpen bij het indienen van verificatieaanvragen in de richting van het doelforest. Hints worden alleen gebruikt als alle traditionele verificatiekanalen, zoals lokale domeincontroller en vervolgens globale catalogus, een SPN niet vinden.

4. *ChildDC1* stuurt een verwijzing voor het bovenliggende domein terug naar *Workstation1*.

5. *Workstation1* neemt contact op met een domeincontroller in *ForestRootDC1* (het bovenliggende domein) voor een verwijzing naar een domeincontroller *(ForestRootDC2)* in het forestrootdomein van het *wingtiptoys.com* forest.

6. *Workstation1* neemt contact op met *ForestRootDC2* in het *wingtiptoys.com* forest voor een serviceticket naar de gevraagde service.

7. *ForestRootDC2* neemt contact op met de globale catalogus om de SPN te vinden, en de globale catalogus vindt een overeenkomst voor de SPN en stuurt deze terug naar *ForestRootDC2.*

8. *ForestRootDC2* stuurt de verwijzing vervolgens naar *usa.wingtiptoys.com* terug naar *Workstation1*.

9. *Workstation1* neemt contact op met de KDC op *ChildDC2* en onderhandelt over het ticket voor *Gebruiker1* om toegang te krijgen tot *FileServer1.*

10. Zodra *Workstation1* een serviceticket heeft, stuurt het het serviceticket naar *FileServer1,* dat de beveiligingsreferenties van *User1*leest en een toegangstoken dienovereenkomstig construeert.

## <a name="trusted-domain-object"></a>Vertrouwd domeinobject

Elke domein- of forestvertrouwensrelatie binnen een organisatie wordt vertegenwoordigd door een Trusted Domain Object (TDO) dat is opgeslagen in de *systeemcontainer* binnen het domein.

### <a name="tdo-contents"></a>TDO-inhoud

De informatie in een TDO is afhankelijk van of een TDO is gemaakt door een domeinvertrouwensrelatie of door een forestvertrouwensrelatie.

Wanneer een domeinvertrouwensrelatie wordt gemaakt, worden kenmerken zoals de DNS-domeinnaam, domeinSID, vertrouwenstype, vertrouwenstransitiviteit en de wederzijdse domeinnaam weergegeven in de TDO. TTO's met forestvertrouwensrelaties slaan extra kenmerken op om alle vertrouwde naamruimten uit het partnerforest te identificeren. Deze kenmerken omvatten domeinnamen van de domeinnaam, upn-achtervoegsels (User Principal Name), SPN-achtervoegsels (Service Principal Name) en sid-naamruimten (Security ID).

Omdat vertrouwensrelaties worden opgeslagen in Active Directory als TDO's, hebben alle domeinen in een forest kennis van de vertrouwensrelaties die in het hele forest zijn uitgevoerd. Op dezelfde manier hebben de forestrootdomeinen in elk forest kennis van de vertrouwensrelaties die in alle domeinen in vertrouwde forests bestaan.

### <a name="tdo-password-changes"></a>TDO-wachtwoordwijzigingen

Beide domeinen in een vertrouwensrelatie delen een wachtwoord dat is opgeslagen in het TDO-object in Active Directory. Als onderdeel van het accountonderhoudsproces wijzigt de vertrouwende domeincontroller elke 30 dagen het wachtwoord dat is opgeslagen in de TDO. Omdat alle tweerichtingsvertrouwensrelaties eigenlijk twee eenrichtingsvertrouwensrelaties zijn die in tegengestelde richting gaan, vindt het proces twee keer plaats voor tweerichtingsvertrouwensrelaties.

Een trust heeft een vertrouwens- en vertrouwde kant. Aan de vertrouwde kant kan elke schrijfbare domeincontroller worden gebruikt voor het proces. Aan de vertrouwende kant voert de PDC-emulator de wachtwoordwijziging uit.

Als u een wachtwoord wilt wijzigen, voltooien de domeincontrollers het volgende proces:

1. De primaire domeincontroller (PDC) emulator in het vertrouwende domein maakt een nieuw wachtwoord. Een domeincontroller in het vertrouwde domein initieert nooit de wachtwoordwijziging. Het wordt altijd geïnitieerd door de vertrouwde domein PDC emulator.

2. De PDC-emulator in het vertrouwende domein stelt het veld *OldPassword* van het TDO-object in op het huidige *veld NewPassword.*

3. De PDC-emulator in het vertrouwende domein stelt het veld *NewPassword* van het TDO-object in op het nieuwe wachtwoord. Het bewaren van een kopie van het vorige wachtwoord maakt het mogelijk om terug te keren naar het oude wachtwoord als de domeincontroller in het vertrouwde domein de wijziging niet ontvangt of als de wijziging niet wordt gerepliceerd voordat een verzoek wordt ingediend dat het nieuwe vertrouwenswachtwoord gebruikt.

4. De PDC-emulator in het vertrouwende domein voert op afstand naar een domeincontroller in het vertrouwde domein met de vraag om het wachtwoord op het vertrouwensaccount in te stellen op het nieuwe wachtwoord.

5. De domeincontroller in het vertrouwde domein wijzigt het vertrouwenswachtwoord in het nieuwe wachtwoord.

6. Aan elke kant van de vertrouwensrelatie worden de updates gerepliceerd naar de andere domeincontrollers in het domein. In het vertrouwende domein activeert de wijziging een dringende replicatie van het vertrouwde domeinobject.

Het wachtwoord is nu gewijzigd op beide domeincontrollers. Normale replicatie verdeelt de TDO-objecten naar de andere domeincontrollers in het domein. Het is echter mogelijk dat de domeincontroller in het vertrouwende domein het wachtwoord wijzigt zonder een domeincontroller in het vertrouwde domein bij te werken. Dit scenario kan zich voordoen omdat een beveiligd kanaal, dat nodig is om de wachtwoordwijziging te verwerken, niet kon worden vastgesteld. Het is ook mogelijk dat de domeincontroller in het vertrouwde domein op een bepaald moment tijdens het proces niet beschikbaar is en mogelijk niet het bijgewerkte wachtwoord ontvangt.

Om situaties aan te pakken waarin de wachtwoordwijziging niet wordt gecommuniceerd, wijzigt de domeincontroller in het vertrouwende domein het nieuwe wachtwoord nooit, tenzij het nieuwe wachtwoord is geverifieerd (een beveiligd kanaal heeft ingesteld) met behulp van het nieuwe wachtwoord. Dit gedrag is de reden waarom zowel de oude als de nieuwe wachtwoorden worden bewaard in het TDO-object van het vertrouwende domein.

Een wachtwoordwijziging wordt pas voltooid als de verificatie met het wachtwoord is geslaagd. Het oude, opgeslagen wachtwoord kan via het beveiligde kanaal worden gebruikt totdat de domeincontroller in het vertrouwde domein het nieuwe wachtwoord ontvangt, waardoor ononderbroken service mogelijk wordt.

Als verificatie met het nieuwe wachtwoord mislukt omdat het wachtwoord ongeldig is, probeert de vertrouwde domeincontroller zich te verifiëren met het oude wachtwoord. Als het oude wachtwoord is geverifieerd, wordt het proces voor wachtwoordwijziging binnen 15 minuten hervat.

Updates voor vertrouwenswachtwoorden moeten binnen 30 dagen worden gerepliceerd naar de domeincontrollers van beide zijden van de vertrouwensrelatie. Als het vertrouwenswachtwoord na 30 dagen wordt gewijzigd en een domeincontroller dan alleen het N-2-wachtwoord heeft, kan het de vertrouwensrelatie van de vertrouwende kant niet gebruiken en kan het geen beveiligd kanaal aan de vertrouwde kant maken.

## <a name="network-ports-used-by-trusts"></a>Netwerkpoorten die worden gebruikt door vertrouwensrelaties

Omdat vertrouwensrelaties over verschillende netwerkgrenzen moeten worden geïmplementeerd, moeten ze mogelijk een of meer firewalls overspannen. Wanneer dit het geval is, u het vertrouwensverkeer over een firewall tunnelen of specifieke poorten in de firewall openen om het verkeer door te laten gaan.

> [!IMPORTANT]
> Active Directory Domain Services biedt geen ondersteuning voor het beperken van Active Directory RPC-verkeer tot specifieke poorten.

Lees het gedeelte **Windows Server 2008 en latere versies** van het Microsoft Support-artikel Hoe u een firewall [configureert voor Active Directory-domeinen en vertrouwensrelaties](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) om meer te weten te komen over de poorten die nodig zijn voor een forestvertrouwensrelatie.

## <a name="supporting-services-and-tools"></a>Ondersteunende diensten en tools

Om vertrouwensrelaties en verificatie te ondersteunen, worden enkele extra functies en beheertools gebruikt.

### <a name="net-logon"></a>Net-aanmelding

De Net Logon-service onderhoudt een beveiligd kanaal van een Windows-computer naar een DC. Het wordt ook gebruikt in de volgende vertrouwensgerelateerde processen:

* Vertrouwensinstelling en -beheer - Net Logon helpt bij het onderhouden van vertrouwenswachtwoorden, verzamelt vertrouwensgegevens en verifieert vertrouwensrelaties door interactie met het LSA-proces en het TDO.

    Voor forestvertrouwensrelaties bevat de vertrouwensgegevens de record Forest Trust Information *(FTInfo),* die de set naamruimten bevat die een vertrouwd forest beweert te beheren, geannoteerd met een veld dat aangeeft of elke claim wordt vertrouwd door het vertrouwende forest.

* Verificatie : levert gebruikersreferenties via een beveiligd kanaal aan een domeincontroller en retourneert de domein-ID's en gebruikersrechten voor de gebruiker.

* Locatie domeincontroller - Helpt bij het vinden of lokaliseren van domeincontrollers in een domein of tussen domeinen.

* Pass-through validatie – Referenties van gebruikers in andere domeinen worden verwerkt door Net Logon. Wanneer een vertrouwend domein de identiteit van een gebruiker moet verifiëren, geeft het de referenties van de gebruiker door via Net Logon aan het vertrouwde domein voor verificatie.

* Pac-verificatie (Privilege Attribute Certificate) - Wanneer een server die het Kerberos-protocol voor verificatie gebruikt, de PAC in een serviceticket moet verifiëren, stuurt deze de PAC over het beveiligde kanaal naar de domeincontroller voor verificatie.

### <a name="local-security-authority"></a>Lokale certificeringsinstantie

De Local Security Authority (LSA) is een beschermd subsysteem dat informatie over alle aspecten van lokale veiligheid op een systeem bijhoudt. Gezamenlijk bekend als lokaal beveiligingsbeleid, de LSA biedt verschillende diensten voor de vertaling tussen namen en id's.

Het beveiligingssubsysteem LSA biedt services in zowel de kernelmodus als de gebruikersmodus voor het valideren van toegang tot objecten, het controleren van gebruikersrechten en het genereren van controleberichten. LSA is verantwoordelijk voor het controleren van de geldigheid van alle sessietickets die worden aangeboden door services in vertrouwde of niet-vertrouwde domeinen.

### <a name="management-tools"></a>Beheerhulpprogramma's

Beheerders kunnen *Active Directory Domains and Trusts*, *Netdom* en *Nltest* gebruiken om vertrouwensrelaties bloot te leggen, te maken, te verwijderen of te wijzigen.

* *Active Directory Domains and Trusts* is de Microsoft Management Console (MMC) die wordt gebruikt voor het beheer van domeinvertrouwensrelaties, domein- en forestfunctionaliteitsniveaus en de hoofdnaamachtervoegsels van gebruikersnamen.
* De *opdrachtregelgereedschappen Netdom* en *Nltest* kunnen worden gebruikt om vertrouwensrelaties te vinden, weer te geven, te maken en te beheren. Deze tools communiceren rechtstreeks met de LSA-autoriteit op een domeincontroller.

## <a name="next-steps"></a>Volgende stappen

Zie [Hoe werken forestvertrouwensrelaties in Azure AD DS voor][concepts-trust] meer informatie over resourceforests?

Zie Een beheerd [Azure AD DS-beheerd domein maken en configureren][tutorial-create-advanced]om aan de slag te gaan met het maken van een door Azure AD DS beheerd domein met een resourceforest. U vervolgens [een uitgaande forestvertrouwensrelatie maken naar een on-premises domein (voorbeeld).][create-forest-trust]

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
