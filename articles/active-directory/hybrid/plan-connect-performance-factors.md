---
title: Factoren die invloed hebben op de prestaties van Azure AD Connect
description: In dit document wordt uitgelegd hoe verschillende factoren van invloed zijn op de Azure AD Connect-inrichtingsengine. Met deze factoren kunnen organisaties hun Azure AD Connect-implementatie plannen om ervoor te zorgen dat deze voldoet aan hun synchronisatievereisten.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5518d516848ba7c006827faa41ff76bbca35d0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897049"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Factoren die invloed hebben op de prestaties van Azure AD Connect

Azure AD Connect synchroniseert uw Active Directory met Azure AD. Deze server is een essentieel onderdeel van het verplaatsen van uw gebruikersidentiteiten naar de cloud. De belangrijkste factoren die van invloed zijn op de prestaties van een Azure AD Connect zijn:

| **Ontwerpfactor**| **Definitie** |
|:-|-|
| Topologie| De distributie van de eindpunten en onderdelen die Azure AD Connect op het netwerk moet beheren. |
| Schalen| Het aantal objecten, zoals de gebruikers, groepen en de Belastingdienst, dat moet worden beheerd door Azure AD Connect. |
| Hardware| De hardware (fysiek of virtueel) voor de Azure AD Connect en de afhankelijke prestatiecapaciteit van elke hardwarecomponent, inclusief CPU, geheugen, netwerk en configuratie van de harde schijf. |
| Configuratie| Hoe Azure AD Connect de mappen en informatie verwerkt. |
| Belasting| Frequentie van objectwijzigingen. De ladingen kunnen variëren gedurende een uur, dag of week. Afhankelijk van het onderdeel moet u mogelijk ontwerpen voor piekbelasting of gemiddelde belasting. |

Het doel van dit document is om de factoren te beschrijven die van invloed zijn op de prestaties van de Azure AD Connect-inrichtingsengine. Grote of complexe organisaties (organisaties die meer dan 100.000 objecten inrichten) kunnen de aanbevelingen gebruiken om hun Azure AD Connect-implementatie te optimaliseren, als ze prestatieproblemen ondervinden die hier worden beschreven. De andere onderdelen van Azure AD Connect, zoals [azure AD Connect-status](how-to-connect-health-agent-install.md) en agents, worden hier niet behandeld.

> [!IMPORTANT]
> Microsoft ondersteunt geen wijziging of exploitatie van Azure AD Connect buiten de acties die formeel zijn gedocumenteerd. Een van deze acties kan resulteren in een inconsistente of niet-ondersteunde status van Azure AD Connect-synchronisatie. Als gevolg hiervan kan Microsoft geen technische ondersteuning bieden voor dergelijke implementaties.

## <a name="azure-ad-connect-component-factors"></a>Azure AD Connect-componentfactoren

Het volgende diagram toont een architectuur op hoog niveau van het inrichten van de engine die verbinding maakt met één forest, hoewel meerdere forests worden ondersteund. Deze architectuur laat zien hoe de verschillende componenten met elkaar omgaan.

![AzureADConnentInternal](media/plan-connect-performance-factors/AzureADConnentInternal.png)

De inrichtingsengine maakt verbinding met elk Active Directory-forest en met Azure AD. Het proces van het lezen van informatie uit elke map heet Import. Export verwijst naar het bijwerken van de mappen van de inrichtingsmotor. Sync evalueert de regels van hoe de objecten binnen de inrichtingsengine zullen stromen. Voor een diepere duik u verwijzen naar [Azure AD Connect-synchronisatie: de architectuur begrijpen.](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)

Azure AD Connect gebruikt de volgende faseringsgebieden, regels en processen om de synchronisatie van Active Directory naar Azure AD toe te staan:

* **Connector Space (CS)** - Objecten uit elke verbonden map (CD), de werkelijke mappen, worden hier eerst geënsceneerd voordat ze kunnen worden verwerkt door de inrichtingsengine. Azure AD heeft zijn eigen CS en elk forest waarmee u verbinding maakt, heeft zijn eigen CS.
* **Metaverse (MV)** - Objecten die moeten worden gesynchroniseerd, worden hier gemaakt op basis van de synchronisatieregels. Objecten moeten op de MV staan voordat ze objecten en kenmerken kunnen vullen aan de andere verbonden mappen. Er is maar één MV.
* **Synchronisatieregels** - Ze bepalen welke objecten worden gemaakt (geprojecteerd) of verbonden (samengevoegd) met objecten in de MV. De synchronisatieregels bepalen ook welke kenmerkwaarden worden gekopieerd of getransformeerd van en naar de mappen.
* **Profielen uitvoeren** : bundelt de processtappen voor het kopiëren van objecten en hun kenmerkwaarden volgens de synchronisatieregels tussen de faseringsgebieden en verbonden mappen.

Er bestaan verschillende runprofielen om de prestaties van de inrichtingsmotor te optimaliseren. De meeste organisaties gebruiken de standaardschema's en voeren profielen uit voor normale bewerkingen, maar sommige organisaties moeten mogelijk [het schema wijzigen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) of andere runprofielen activeren om te voorzien in ongewone situaties. De volgende runprofielen zijn beschikbaar:

### <a name="initial-sync-profile"></a>Initieel synchronisatieprofiel

Het initiële synchronisatieprofiel is het proces waarbij de verbonden mappen, zoals een Active Directory-forest, voor het eerst worden gelezen. Het doet dan een analyse op alle items in de sync engine database. De eerste cyclus maakt nieuwe objecten in Azure AD en neemt extra tijd in beslag als uw Active Directory-forests groot zijn. De eerste synchronisatie bevat de volgende stappen:

1. Volledige import op alle connectoren
2. Volledige synchronisatie op alle connectoren
3. Exporteren op alle connectoren

### <a name="delta-sync-profile"></a>Delta-synchronisatieprofiel

Om het synchronisatieproces te optimaliseren, verwerkt dit run-profiel alleen de wijzigingen (maakt, verwijdert en wordt bijgewerkt) van objecten in uw verbonden mappen, sinds het laatste synchronisatieproces. Standaard wordt het deltasynchronisatieprofiel elke 30 minuten uitgevoerd. Organisaties moeten ernaar streven om de tijd die nodig is om minder dan 30 minuten te houden, om ervoor te zorgen dat de Azure AD up-to-date is. Als u de status van Azure AD Connect wilt controleren, gebruikt u de [statusbewakingsagent](how-to-connect-health-sync.md) om eventuele problemen met het proces te zien. Het deltasynchronisatieprofiel bevat de volgende stappen:

1. Delta importeren op alle connectoren
2. Delta-synchronisatie op alle connectoren
3. Exporteren op alle connectoren

Een typisch deltasynchronisatiescenario voor bedrijfsorganisaties is:

- ~1% van de objecten worden verwijderd
- ~1% van de objecten wordt gemaakt
- ~5% van de objecten worden gewijzigd

Uw wijzigingssnelheid kan variëren, afhankelijk van hoe vaak uw organisatie gebruikers in uw Active Directory bijwerkt. Bijvoorbeeld, hogere tarieven van verandering kan optreden met de seizoensgebondenheid van het inhuren en het verminderen van de beroepsbevolking.

### <a name="full-sync-profile"></a>Volledig synchronisatieprofiel

Een volledige synchronisatiecyclus is vereist als u een van de volgende configuratiewijzigingen hebt aangebracht:



- Vergroot het bereik van de objecten of kenmerken die moeten worden geïmporteerd uit de verbonden mappen. Bijvoorbeeld wanneer u een domein of een organisatie-eenheid toevoegt aan uw importbereik.
- Wijzigingen aangebracht in de synchronisatieregels. Wanneer u bijvoorbeeld een nieuwe regel maakt om de titel van een gebruiker in Azure AD in te vullen vanuit extension_attribute3 in Active Directory. Deze update vereist dat de inrichtingsengine alle bestaande gebruikers opnieuw onderzoekt om hun titels bij te werken om de wijziging in de toekomst toe te passen.

De volgende bewerkingen zijn opgenomen in een volledige synchronisatiecyclus:

1. Volledige import op alle connectoren
2. Volledige/Delta-synchronisatie op alle connectoren
3. Exporteren op alle connectoren

> [!NOTE]
> Een zorgvuldige planning is vereist bij het uitvoeren van bulkupdates voor veel objecten in uw Active Directory of Azure AD. Bulkupdates zorgen ervoor dat het deltasynchronisatieproces langer duurt bij het importeren, omdat veel objecten zijn gewijzigd. Lange importen kunnen zelfs plaatsvinden als de bulkupdate geen invloed heeft op het synchronisatieproces. Als u bijvoorbeeld licenties aan veel gebruikers in Azure AD toekent, wordt een lange importcyclus van Azure AD veroorzaakt, maar leidt dit niet tot wijzigingen in het kenmerk in Active Directory.

### <a name="synchronization"></a>Synchronisatie

De runtime van het synchronisatieproces heeft de volgende prestatiekenmerken:

* Synchronisatie is single threaded, wat betekent dat de inrichtingsengine geen parallelle verwerking van runprofielen van verbonden mappen, objecten of kenmerken uitvoert.
* Importtijd groeit lineair met het aantal objecten dat wordt gesynchroniseerd. Als bijvoorbeeld 10.000 objecten 10 minuten nodig hebben om te importeren, duurt het ongeveer 20.000 minuten voordat 20.000 objecten op dezelfde server zijn.
* Export is ook lineair.
* De synchronisatie groeit exponentieel op basis van het aantal objecten met verwijzingen naar andere objecten. Groepslidmaatschappen en geneste groepen hebben de belangrijkste impact op de prestaties, omdat hun leden verwijzen naar gebruikersobjecten of andere groepen. Deze verwijzingen moeten worden gevonden en verwezen naar werkelijke objecten in de MV om de synchronisatiecyclus te voltooien.

### <a name="filtering"></a>Filteren

De grootte van de Active Directory-topologie die u wilt importeren, is de belangrijkste factor die de prestaties en de totale tijd beïnvloedt die de interne componenten van de inrichtingsengine in beslag nemen.

[Filteren](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) moet worden gebruikt om de objecten te verminderen tot de gesynchroniseerde. Het voorkomt dat onnodige objecten worden verwerkt en geëxporteerd naar Azure AD. In volgorde van voorkeur zijn de volgende technieken voor filtering beschikbaar:



- **Filteren op basis van domeinen** : gebruik deze optie om specifieke domeinen te selecteren die u wilt synchroniseren met Azure AD. U moet domeinen toevoegen en verwijderen uit de configuratie van de synchronisatieengine wanneer u wijzigingen aanbrengt in uw on-premises infrastructuur nadat u Azure AD Connect-synchronisatie hebt geïnstalleerd.
- **Organisatie-eenheid (OU) filteren** - gebruikt OU's om specifieke objecten in Active Directory-domeinen te targeten voor het inrichten op Azure AD. Het filteren van de organisatie van de organisatie van de organisatie van de organisatie is het tweede aanbevolen filtermechanisme, omdat het eenvoudige LDAP-scopequery's gebruikt om een kleinere subset van objecten uit Active Directory te importeren.
- **Kenmerkfiltering per object** : gebruikt de kenmerkwaarden voor objecten om te bepalen of een specifiek object in Active Directory is ingericht in Azure AD. Kenmerkfiltering is ideaal voor het verfijnen van uw filters, wanneer domein- en OU-filtering niet voldoet aan de specifieke filtervereisten. Kenmerkfiltering vermindert de importtijd niet, maar kan de synchronisatie- en exporttijden verkorten.
- **Groepsfiltering** - gebruikt groepslidmaatschap om te beslissen of objecten moeten worden ingericht in Azure AD. Groepsfiltering is alleen geschikt voor testsituaties en wordt niet aanbevolen voor productie, vanwege de extra overhead die nodig is om het groepslidmaatschap tijdens de synchronisatiecyclus te controleren.

Veel permanente [disconnector-objecten](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) in uw Active Directory CS kunnen langere synchronisatietijden veroorzaken, omdat de inrichtingsengine elk disconnector-object opnieuw moet evalueren voor mogelijke verbinding in de synchronisatiecyclus. Als u dit probleem wilt oplossen, moet u een van de volgende aanbevelingen overwegen:



- Plaats de disconnector-objecten buiten het bereik voor importeren met behulp van domein- of organisatie-eenheid-filtering.
- Project/join the objects to the MV and set the [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) attribute equal to True, to prevent provisioning of these objects in the Azure AD CS.

> [!NOTE]
> Gebruikers kunnen in de war raken of problemen met toepassingsmachtigingen kunnen optreden wanneer te veel objecten worden gefilterd. In een hybride Online-implementatie van Exchange zien gebruikers met on-premises postvakken bijvoorbeeld meer gebruikers in hun wereldwijde adreslijst dan gebruikers met postvakken in Exchange online. In andere gevallen kan een gebruiker toegang in een cloud-app willen verlenen aan een andere gebruiker die geen deel uitmaakt van het bereik van de gefilterde set objecten.

### <a name="attribute-flows"></a>Kenmerkstromen

Kenmerkstromen is het proces voor het kopiëren of transformeren van de kenmerkwaarden van objecten van de ene verbonden map naar een andere verbonden map. Ze zijn gedefinieerd als onderdeel van de synchronisatieregels. Wanneer bijvoorbeeld het telefoonnummer van een gebruiker wordt gewijzigd in uw Active Directory, wordt het telefoonnummer in Azure AD bijgewerkt. Organisaties kunnen [de kenmerkstromen aanpassen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration) aan verschillende vereisten. U raadt u aan de bestaande kenmerkstromen te kopiëren voordat u deze wijzigt.

Eenvoudige omleidingen, zoals het vloeiende van een kenmerkwaarde naar een ander kenmerk, hebben geen impact op de materiële prestaties. Een voorbeeld van een omleiding is het genereren van een mobiel nummer in Active Directory naar het kantoortelefoonnummer in Azure AD.

Het transformeren van kenmerkwaarden kan een prestatie-impact hebben op het synchronisatieproces. Het transformeren van kenmerkwaarden omvat het wijzigen, opnieuw formatteren, concatenenof of aftrekken van waarden van kenmerken.

Organisaties kunnen voorkomen dat bepaalde kenmerken naar Azure AD worden doorgestroomd, maar dit heeft geen invloed op de prestaties van de inrichtingsengine.

> [!NOTE]
> Verwijder geen ongewenste kenmerkstromen in uw synchronisatieregels. Het wordt aanbevolen dat u ze liever uitschakelt, omdat verwijderde regels opnieuw worden gemaakt tijdens Azure AD Connect-upgrades.

## <a name="azure-ad-connect-dependency-factors"></a>Afhankelijkheidsfactoren van Azure AD Connect

De prestaties van Azure AD Connect zijn afhankelijk van de prestaties van de verbonden mappen waarnaar het importeert en exporteert. Bijvoorbeeld de grootte van de Active Directory die moet worden geïmporteerd of de netwerklatentie naar de Azure AD-service. De SQL-database die de inrichtingsengine gebruikt, heeft ook invloed op de algehele prestaties van de synchronisatiecyclus.

### <a name="active-directory-factors"></a>Active Directory-factoren

Zoals eerder vermeld, beïnvloedt het aantal te importeren objecten de prestaties aanzienlijk. De [hardware en vereisten voor Azure AD Connect](how-to-connect-install-prerequisites.md) geven een overzicht van specifieke hardwarelagen op basis van de grootte van uw implementatie. Azure AD Connect ondersteunt alleen specifieke topologieën zoals beschreven in [Topologieën voor Azure AD Connect.](plan-connect-topologies.md) Er zijn geen prestatieoptimalisaties en aanbevelingen voor niet-ondersteunde topologieën.

Controleer of uw Azure AD Connect-server voldoet aan de hardwarevereisten op basis van de grootte van uw Active Directory die u wilt importeren. Slechte of trage netwerkconnectiviteit tussen de Azure AD Connect-server en uw Active Directory-domeincontrollers kan uw import vertragen.

### <a name="azure-ad-factors"></a>Azure AD-factoren

Azure AD gebruikt beperking om de cloudservice te beschermen tegen DoS-aanvallen (denial-of-service). Momenteel heeft Azure AD een beperkingslimiet van 7.000 schrijfbewerkingen per 5 minuten (84.000 per uur). De volgende bewerkingen kunnen bijvoorbeeld worden beperkt:



- Azure AD Connect exporteert naar Azure AD.
- PowerShell-scripts of -toepassingen die de Azure AD rechtstreeks bijwerken, zelfs op de achtergrond, zoals Dynamische groepslidmaatschappen.
- Gebruikers die hun eigen identiteitsrecords bijwerken, zoals registratie voor MFA of SSPR (selfservice wachtwoord reset).
- Bewerkingen binnen de grafische gebruikersinterface.

Plan implementatie- en onderhoudstaken om ervoor te zorgen dat uw Azure AD Connect-synchronisatiecyclus niet wordt beïnvloed door beperkingslimieten. Als u bijvoorbeeld een grote wervingsgolf hebt waarbij u duizenden gebruikersidentiteiten maakt, kan dit leiden tot updates voor dynamische groepslidmaatschappen, licentietoewijzingen en zelfservicewachtwoordresetregistraties. Het is beter om deze schrijft te verspreiden over enkele uren of een paar dagen.

### <a name="sql-database-factors"></a>SQL-databasefactoren

De grootte van uw bron Active Directory-topologie heeft invloed op de prestaties van uw SQL-database. Volg de [hardwarevereisten](how-to-connect-install-prerequisites.md) voor de SQL-serverdatabase en houd rekening met de volgende aanbevelingen:



- Organisaties met meer dan 100.000 gebruikers kunnen netwerklatencies verminderen door sql-database en de inrichtingsengine op dezelfde server te verplaatsen.
- Vanwege de hoge schijfinvoer- en uitvoervereisten (I/O) van het synchronisatieproces, gebruikt u Solid State Drives (SSD) voor de SQL-database van de inrichtingsengine voor optimale resultaten, zo niet mogelijk, raid 0- of RAID 1-configuraties overwegen.
- Doe geen volledige synchronisatie preventief; het veroorzaakt onnodige churn en langzamere reactietijden.

## <a name="conclusion"></a>Conclusie

Als u de prestaties van uw Azure AD Connect-implementatie wilt optimaliseren, moet u de volgende aanbevelingen overwegen:



- Gebruik de [aanbevolen hardwareconfiguratie](how-to-connect-install-prerequisites.md) op basis van de implementatiegrootte voor de Azure AD Connect-server.
- Wanneer u Azure AD Connect upgradet in grootschalige implementaties, u overwegen [de swingmigratiemethode](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration)te gebruiken om ervoor te zorgen dat u de minste downtime en de beste betrouwbaarheid hebt. 
- Gebruik SSD voor de SQL-database voor de beste schrijfprestaties.
- Filter het Active Directory-bereik om alleen objecten op te nemen die moeten worden ingericht in Azure AD, met behulp van domein-, ou- of kenmerkfiltering.
- Als u de standaardregels voor kenmerkstroom moet wijzigen, kopieert u de regel eerst en wijzigt u de kopie en schakelt u de oorspronkelijke regel uit. Vergeet niet om een volledige synchronisatie opnieuw uit te voeren.
- Plan voldoende tijd voor het eerste volledige synchronisatieprofiel.
- Streef ernaar om de deltasynchronisatiecyclus in 30 minuten te voltooien. Als het deltasynchronisatieprofiel niet binnen 30 minuten is voltooid, wijzigt u de standaardsynchronisatiefrequentie om een volledige deltasynchronisatiecyclus op te nemen.
- Houd uw [synchronisatiestatus van Azure AD Connect in](how-to-connect-health-agent-install.md) Azure AD in de gaten.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
