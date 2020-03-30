---
title: 'Azure AD Connect-synchronisatie: de architectuur begrijpen - Azure'
description: In dit onderwerp wordt de architectuur van Azure AD Connect-synchronisatie beschreven en worden de gebruikte termen uitgelegd.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fac0f9143918d3f273812e53abfb88d6a56f7a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261617"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD Connect-synchronisatie: de architectuur begrijpen
Dit onderwerp behandelt de basisarchitectuur voor Azure AD Connect-synchronisatie. In veel opzichten is het vergelijkbaar met zijn voorgangers MIIS 2003, ILM 2007 en FIM 2010. Azure AD Connect-synchronisatie is de evolutie van deze technologieën. Als u bekend bent met een van deze eerdere technologieën, zal de inhoud van dit onderwerp bekend zijn voor u ook. Als u nieuw bent in synchronisatie, dan is dit onderwerp is voor jou. Het is echter geen vereiste om de details van dit onderwerp te kennen om succesvol te zijn in het maken van aanpassingen aan Azure AD Connect-synchronisatie (synchronisatieengine in dit onderwerp genoemd).

## <a name="architecture"></a>Architectuur
De synchronisatie-engine maakt een geïntegreerde weergave van objecten die zijn opgeslagen in meerdere verbonden gegevensbronnen en beheert identiteitsgegevens in die gegevensbronnen. Deze geïntegreerde weergave wordt bepaald door de identiteitsgegevens die zijn opgehaald uit verbonden gegevensbronnen en een reeks regels die bepalen hoe deze informatie moet worden verwerkt.

### <a name="connected-data-sources-and-connectors"></a>Verbonden gegevensbronnen en -connectoren
De synchronisatieengine verwerkt identiteitsgegevens uit verschillende gegevensopslagplaatsen, zoals Active Directory of een SQL Server-database. Elke gegevensopslagplaats die zijn gegevens in een database-achtige indeling organiseert en die standaard methoden voor gegevenstoegang biedt, is een potentiële gegevensbronkandidaat voor de synchronisatie-engine. De gegevensrepositories die worden gesynchroniseerd door sync engine worden **verbonden gegevensbronnen** of **verbonden mappen** (CD) genoemd.

De synchronisatie-engine bevat de interactie met een verbonden gegevensbron in een module die een **connector**wordt genoemd. Elk type verbonden gegevensbron heeft een specifieke connector. De connector vertaalt een vereiste bewerking naar de indeling die de verbonden gegevensbron begrijpt.

Connectors maken API-aanroepen om identiteitsgegevens uit te wisselen (zowel lezen als schrijven) met een verbonden gegevensbron. Het is ook mogelijk om een aangepaste connector toe te voegen met behulp van het uitbreidbare connectiviteitsframework. In de volgende afbeelding ziet u hoe een connector een verbonden gegevensbron verbindt met de synchronisatieengine.

![Boog1](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Gegevens kunnen in beide richtingen stromen, maar kunnen niet tegelijkertijd in beide richtingen stromen. Met andere woorden, een connector kan worden geconfigureerd zodat gegevens kunnen stromen van de verbonden gegevensbron naar synchronisatie-engine of van synchronisatie-engine naar de verbonden gegevensbron, maar slechts één van die bewerkingen kan op een bepaald moment plaatsvinden voor één object en kenmerk. De richting kan verschillend zijn voor verschillende objecten en voor verschillende attributen.

Als u een connector wilt configureren, geeft u de objecttypen op die u wilt synchroniseren. Als u de objecttypen opgeeft, wordt het bereik gedefinieerd van objecten die zijn opgenomen in het synchronisatieproces. De volgende stap is het selecteren van de kenmerken die moeten worden gesynchroniseerd, wat bekend staat als een lijst met kenmerkopname. Deze instellingen kunnen op elk gewenst moment worden gewijzigd als reactie op wijzigingen in uw bedrijfsregels. Wanneer u de wizard Installatie van Azure AD Connect gebruikt, worden deze instellingen voor u geconfigureerd.

Als u objecten wilt exporteren naar een verbonden gegevensbron, moet de lijst met kenmerkopneming ten minste de minimumkenmerken bevatten die nodig zijn om een specifiek objecttype in een verbonden gegevensbron te maken. Het kenmerk **sAMAccountName** moet bijvoorbeeld worden opgenomen in de lijst voor de opname van kenmerken om een gebruikersobject naar Active Directory te exporteren, omdat voor alle gebruikersobjecten in Active Directory een **kenmerk sAMAccountName** moet zijn gedefinieerd. Nogmaals, de installatiewizard doet deze configuratie voor u.

Als de verbonden gegevensbron structurele componenten gebruikt, zoals partities of containers om objecten te ordenen, u de gebieden in de verbonden gegevensbron beperken die voor een bepaalde oplossing worden gebruikt.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Interne structuur van de naamruimte van de synchronisatieengine
De volledige naamruimte van de synchronisatieengine bestaat uit twee naamruimten die de identiteitsgegevens opslaan. De twee naamruimten zijn:

* De verbindingsruimte (CS)
* De metaverse (MV)

De **verbindingsruimte** is een faseringsgebied dat weergaven van de aangewezen objecten uit een verbonden gegevensbron en de kenmerken bevat die zijn opgegeven in de lijst met kenmerkopneming. De synchronisatie-engine gebruikt de verbindingsruimte om te bepalen wat er is gewijzigd in de verbonden gegevensbron en om binnenkomende wijzigingen te verwerken. De synchronisatie-engine gebruikt ook de verbindingsruimte om uitgaande wijzigingen voor export naar de verbonden gegevensbron te verwerken. De synchronisatie-engine behoudt een afzonderlijke verbindingsruimte als faseringsgebied voor elke connector.

Door gebruik te maken van een faseringsgebied blijft de synchronisatieengine onafhankelijk van de verbonden gegevensbronnen en wordt deze niet beïnvloed door hun beschikbaarheid en toegankelijkheid. Als gevolg hiervan u op elk gewenst moment identiteitsgegevens verwerken met behulp van de gegevens in het faseringsgebied. De synchronisatie-engine kan alleen de wijzigingen opvragen die zijn aangebracht in de verbonden gegevensbron sinds de laatste communicatiesessie is beëindigd of alleen de wijzigingen in identiteitsgegevens die de verbonden gegevensbron nog heeft ontvangen, waardoor het netwerk wordt verminderd verkeer tussen de synchronisatie-engine en de verbonden gegevensbron.

Bovendien slaat de synchronisatieengine statusinformatie op over alle objecten die deze in de connectorruimte faseert. Wanneer nieuwe gegevens worden ontvangen, evalueert de synchronisatieengine altijd of de gegevens al zijn gesynchroniseerd.

De **metaverse** is een opslaggebied dat de geaggregeerde identiteitsgegevens uit meerdere verbonden gegevensbronnen bevat, die één globale, geïntegreerde weergave van alle gecombineerde objecten bieden. Metaverse objecten worden gemaakt op basis van de identiteitsgegevens die wordt opgehaald uit de verbonden gegevensbronnen en een set regels waarmee u het synchronisatieproces aanpassen.

In de volgende afbeelding ziet u de naamruimte van de connectorruimte en de metaverse naamruimte in de synchronisatieengine.

![Boog2](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Identiteitsobjecten van de engine synchroniseren
De objecten in de synchronisatie-engine zijn weergaven van objecten in de verbonden gegevensbron of de geïntegreerde weergave die de synchronisatie-engine van die objecten heeft. Elk object met synchronisatie-engine moet een wereldwijd unieke id (GUID) hebben. GUID's bieden gegevensintegriteit en drukken relaties tussen objecten uit.

### <a name="connector-space-objects"></a>Verbindingsruimteobjecten
Wanneer de synchronisatieengine communiceert met een verbonden gegevensbron, leest deze de identiteitsgegevens in de verbonden gegevensbron en gebruikt deze informatie om een weergave van het identiteitsobject in de connectorruimte te maken. U deze objecten niet afzonderlijk maken of verwijderen. U echter alle objecten in een connectorruimte handmatig verwijderen.

Alle objecten in de connectorruimte hebben twee kenmerken:

* Een wereldwijd unieke id (GUID)
* Een voorname naam (ook bekend als DN)

Als de verbonden gegevensbron een uniek kenmerk aan het object toewijst, kunnen objecten in de verbindingsruimte ook een ankerkenmerk hebben. Het ankerkenmerk identificeert op unieke wijze een object in de verbonden gegevensbron. De synchronisatieengine gebruikt het anker om de overeenkomstige weergave van dit object in de verbonden gegevensbron te vinden. De synchronisatie-engine gaat ervan uit dat het anker van een object nooit verandert gedurende de levensduur van het object.

Veel van de connectors gebruiken een bekende unieke id om automatisch een anker te genereren voor elk object wanneer het wordt geïmporteerd. De Active Directory Connector gebruikt bijvoorbeeld het **kenmerk objectGUID** voor een anker. Voor verbonden gegevensbronnen die geen duidelijk gedefinieerde unieke id bieden, u ankergeneratie opgeven als onderdeel van de connectorconfiguratie.

In dat geval is het anker opgebouwd uit een of meer unieke kenmerken van een objecttype, die geen van beide verandert, en dat identificeert het object in de connectorruimte (bijvoorbeeld een werknemersnummer of een gebruikers-ID).

Een object met verbindingsruimte kan een van de volgende opties zijn:

* Een faseringsobject
* Een tijdelijke aanduiding

### <a name="staging-objects"></a>Objecten met een fasering
Een faseringsobject vertegenwoordigt een instantie van de aangewezen objecttypen uit de verbonden gegevensbron. Naast de GUID en de gedistingeerde naam heeft een faseringsobject altijd een waarde die het objecttype aangeeft.

Tijdelijke objecten die zijn geïmporteerd, hebben altijd een waarde voor het ankerkenmerk. Tijdelijke objecten die onlangs zijn ingericht door de synchronisatieengine en die worden gemaakt in de verbonden gegevensbron, hebben geen waarde voor het ankerkenmerk.

Faseringsobjecten bevatten ook de huidige waarden van bedrijfskenmerken en operationele informatie die door de synchronisatieengine nodig is om het synchronisatieproces uit te voeren. Operationele informatie bevat vlaggen die het type updates aangeven dat op het staging-object wordt geënsceneerd. Als een faseringsobject nieuwe identiteitsgegevens heeft ontvangen van de verbonden gegevensbron die nog niet is verwerkt, wordt het object gemarkeerd als **in behandeling zijnde import**. Als een faseringsobject nieuwe identiteitsgegevens heeft die nog niet naar de verbonden gegevensbron zijn geëxporteerd, wordt het gemarkeerd als **in behandeling zijnde export**.

Een faseringsobject kan een importobject of een exportobject zijn. De synchronisatie-engine maakt een importobject met behulp van objectinformatie die is ontvangen van de verbonden gegevensbron. Wanneer synchronisatieengine informatie ontvangt over het bestaan van een nieuw object dat overeenkomt met een van de objecttypen die in de connector zijn geselecteerd, wordt een importobject in de verbindingsruimte gemaakt als een weergave van het object in de verbonden gegevensbron.

In de volgende afbeelding ziet u een importobject dat een object in de verbonden gegevensbron vertegenwoordigt.

![Boog3](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

De synchronisatie-engine maakt een exportobject met behulp van objectinformatie in de metaverse. Exportobjecten worden geëxporteerd naar de verbonden gegevensbron tijdens de volgende communicatiesessie. Vanuit het perspectief van de synchronisatie-engine bestaan exportobjecten nog niet in de verbonden gegevensbron. Daarom is het ankerkenmerk voor een exportobject niet beschikbaar. Nadat het object van de synchronisatieengine is ontvangen, wordt een unieke waarde voor het ankerkenmerk van het object gemaakt nadat het object is ontvangen.

In de volgende afbeelding ziet u hoe een exportobject wordt gemaakt met behulp van identiteitsgegevens in de metaverse.

![Boog4](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

De synchronisatie-engine bevestigt de export van het object door het object opnieuw te importeren uit de verbonden gegevensbron. Exportobjecten worden importobjecten wanneer de synchronisatie-engine ze ontvangt tijdens de volgende import uit die verbonden gegevensbron.

### <a name="placeholders"></a>Tijdelijke aanduidingen
De synchronisatie-engine gebruikt een platte naamruimte om objecten op te slaan. Sommige verbonden gegevensbronnen, zoals Active Directory, gebruiken echter een hiërarchische naamruimte. Als u informatie uit een hiërarchische naamruimte wilt omzetten in een platte naamruimte, gebruikt de synchronisatieengine tijdelijke aanduidingen om de hiërarchie te behouden.

Elke tijdelijke aanduiding vertegenwoordigt een component (bijvoorbeeld een organisatie-eenheid) van de hiërarchische naam van een object die niet is geïmporteerd in de synchronisatie-engine, maar die nodig is om de hiërarchische naam te construeren. Ze vullen hiaten op die zijn gemaakt door verwijzingen in de verbonden gegevensbron naar objecten die geen tijdelijke objecten in de verbindingsruimte vormen.

De synchronisatie-engine gebruikt ook tijdelijke aanduidingen om objecten waarnaar verwezen is die nog niet zijn geïmporteerd op te slaan. Als synchronisatie bijvoorbeeld is geconfigureerd om het kenmerk Manager voor het *object Abbie Spencer* op te nemen en de ontvangen waarde een object is dat nog niet is geïmporteerd, zoals *CN=Lee Sperry,CN=Users,DC=fabrikam,DC=com,* wordt de managerinformatie opgeslagen als tijdelijke aanduidingen in de connectorruimte. Als het managerobject later wordt geïmporteerd, wordt het object tijdelijke aanduiding overschreven door het faseringsobject dat de manager vertegenwoordigt.

### <a name="metaverse-objects"></a>Metaverse objecten
Een metaverse object bevat de geaggregeerde weergave die de synchronisatie-engine heeft van de staging-objecten in de verbindingsruimte. Synchronisatie-engine maakt metaverse objecten met behulp van de informatie in importobjecten. Verschillende verbindingsruimteobjecten kunnen worden gekoppeld aan één metaverse object, maar een object voor verbindingsruimte kan niet aan meer dan één metaverse object worden gekoppeld.

Metaverse objecten kunnen niet handmatig worden gemaakt of verwijderd. De synchronisatie-engine verwijdert automatisch metaverse objecten die geen koppeling hebben naar een verbindingsruimteobject in de connectorruimte.

Als u objecten in een verbonden gegevensbron wilt toewijzen aan een overeenkomstig objecttype in de metaverse, biedt de synchronisatieengine een uitbreidbaar schema met een vooraf gedefinieerde set objecttypen en bijbehorende kenmerken. U nieuwe objecttypen en -kenmerken maken voor metaverse objecten. Kenmerken kunnen worden gewaardeerd of met meerdere waarden gewaardeerd en de kenmerktypen kunnen tekenreeksen, verwijzingen, getallen en Booleaanse waarden zijn.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relaties tussen metende objecten en metaverse objecten
Binnen de naamruimte van de synchronisatieengine wordt de gegevensstroom ingeschakeld door de koppelingsrelatie tussen tijdelijke objecten en metaverse objecten. Een faseringsobject dat is gekoppeld aan een metaverse object wordt een **samengevoegd object** (of **verbindingsobject)** genoemd. Een faseringsobject dat niet is gekoppeld aan een metaverse object wordt een **losgekoppeld object** (of **loskoppelobject)** genoemd. De samengevoegde en samengevoegde voorwaarden hebben de voorkeur om niet te verwarren met de connectors die verantwoordelijk zijn voor het importeren en exporteren van gegevens uit een verbonden map.

Tijdelijke aanduidingen zijn nooit gekoppeld aan een metaverse object

Een samengevoegd object bestaat uit een faseringsobject en de gekoppelde relatie met één metaverse object. Samengevoegde objecten worden gebruikt om kenmerkwaarden te synchroniseren tussen een object met de connectorruimte en een metaverse object.

Wanneer een faseringsobject tijdens synchronisatie een samengevoegd object wordt, kunnen kenmerken tussen het staging-object en het metaverse object stromen. De kenmerkstroom is bidirectioneel en wordt geconfigureerd met importkenmerkregels en exportkenmerkregels.

Een enkel verbindingsruimteobject kan worden gekoppeld aan slechts één metaverse object. Elk metaverse object kan echter worden gekoppeld aan meerdere verbindingsruimteobjecten in dezelfde of in verschillende verbindingsruimten, zoals in de volgende afbeelding wordt weergegeven.

![Boog5](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

De gekoppelde relatie tussen het staging-object en een metaverse object is blijvend en kan alleen worden verwijderd door regels die u opgeeft.

Een gedesjoined object is een staging object dat niet is gekoppeld aan een metaverse object. De kenmerkwaarden van een samengevoegd object worden niet verder verwerkt binnen de metaverse. De kenmerkwaarden van het bijbehorende object in de verbonden gegevensbron worden niet bijgewerkt door de synchronisatieengine.

Door ongestoofde objecten te gebruiken, u identiteitsgegevens opslaan in synchronisatieengine en deze later verwerken. Het behouden van een staging object als een gedesjoined object in de connector ruimte heeft vele voordelen. Omdat het systeem de vereiste informatie over dit object al heeft geënsceneerd, is het niet nodig om opnieuw een weergave van dit object te maken tijdens de volgende import uit de verbonden gegevensbron. Op deze manier heeft de synchronisatieengine altijd een volledige momentopname van de verbonden gegevensbron, zelfs als er geen huidige verbinding is met de verbonden gegevensbron. Gedesjoinde objecten kunnen worden omgezet in samengevoegde objecten en vice versa, afhankelijk van de regels die u opgeeft.

Een importobject wordt gemaakt als een samengevoegd object. Een exportobject moet een samengevoegd object zijn. De systeemlogica dwingt deze regel af en verwijdert elk exportobject dat geen samengevoegd object is.

## <a name="sync-engine-identity-management-process"></a>Synchronisatie van het identiteitsbeheerproces van de engine
Het identiteitsbeheerproces bepaalt hoe identiteitsgegevens worden bijgewerkt tussen verschillende verbonden gegevensbronnen. Identiteitsbeheer vindt plaats in drie processen:

* Importeren
* Synchronisatie
* Exporteren

Tijdens het importproces evalueert de synchronisatieengine de binnenkomende identiteitsgegevens van een verbonden gegevensbron. Wanneer wijzigingen worden gedetecteerd, worden nieuwe staging-objecten gemaakt of worden bestaande staging-objecten in de verbindingsruimte bijgewerkt voor synchronisatie.

Tijdens het synchronisatieproces werkt de synchronisatieengine de metaverse bij om wijzigingen weer te geven die zich in de connectorruimte hebben voorgedaan en wordt de verbindingsruimte bijgewerkt om wijzigingen weer te geven die zich in het metaverse hebben voorgedaan.

Tijdens het exportproces pusht de synchronisatieengine wijzigingen die zijn gefaseerd op tijdelijke objecten en die worden gemarkeerd als in behandeling zijnde export.

De volgende illustratie geeft aan waar elk van de processen plaatsvindt als identiteitsinformatie van de ene verbonden gegevensbron naar de andere stroomt.

![Boog6](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Importproces
Tijdens het importproces evalueert de synchronisatieengine updates van identiteitsgegevens. De synchronisatie-engine vergelijkt de identiteitsgegevens die van de verbonden gegevensbron zijn ontvangen met de identiteitsgegevens over een faseringsobject en bepaalt of het staging-object updates vereist. Als het tijdelijke object moet worden bijgewerkt met nieuwe gegevens, wordt het staging-object gemarkeerd als import in behandeling.

Door objecten in de verbindingsruimte te fasen voor synchronisatie, kan de synchronisatieengine alleen de identiteitsgegevens verwerken die is gewijzigd. Dit proces biedt de volgende voordelen:

* **Efficiënte synchronisatie**. De hoeveelheid gegevens die tijdens synchronisatie wordt verwerkt, wordt geminimaliseerd.
* **Efficiënte resynchronisatie**. U wijzigen hoe synchronisatieengine identiteitsgegevens verwerkt zonder de synchronisatieengine opnieuw aan te sluiten op de gegevensbron.
* **Mogelijkheid om een voorbeeld van synchronisatie te bekijken**. U een voorbeeld van synchronisatie bekijken om te controleren of uw veronderstellingen over het identiteitsbeheerproces juist zijn.

Voor elk object dat in de connector is opgegeven, probeert de synchronisatieengine eerst een weergave van het object in de verbindingsruimte van de connector te zoeken. De synchronisatie-engine onderzoekt alle staging-objecten in de verbindingsruimte en probeert een bijbehorend faseringsobject te vinden met een overeenkomend ankerkenmerk. Als geen enkel bestaand faseringsobject een overeenkomend ankerkenmerk heeft, probeert de synchronisatieengine een bijbehorend faseringsobject met dezelfde voorname naam te vinden.

Wanneer de synchronisatieengine een staging-object vindt dat overeenkomt met de naam, maar niet op anker, treedt het volgende speciale gedrag op:

* Als het object in de connectorruimte geen anker heeft, verwijdert de synchronisatieengine dit object uit de verbindingsruimte en markeert het metaverse object waaraan het is gekoppeld als **het opnieuw inrichten op de volgende synchronisatierun**. Vervolgens wordt het nieuwe importobject gemaakt.
* Als het object in de connectorruimte een anker heeft, gaat de synchronisatieengine ervan uit dat dit object is hernoemd of verwijderd in de verbonden map. Hiermee wordt een tijdelijke, nieuwe onderscheiden naam voor het object connectorruimte toegekend, zodat het binnenkomende object kan worden opgenomen. Het oude object wordt dan **van voorbijgaande aard,** wachtend op het importeren van de naam of verwijdering van de connector om de situatie op te lossen.

Als de synchronisatieengine een faseringsobject zoekt dat overeenkomt met het object dat in de connector is opgegeven, bepaalt het wat voor soort wijzigingen moeten worden toegepast. Sync Engine kan bijvoorbeeld de naam van het object in de verbonden gegevensbron wijzigen of verwijderen, of het kan alleen de kenmerkwaarden van het object bijwerken.

Tijdelijke objecten met bijgewerkte gegevens worden gemarkeerd als import in behandeling. Er zijn verschillende soorten in behandeling zijnde invoer beschikbaar. Afhankelijk van het resultaat van het importproces heeft een faseringsobject in de verbindingsruimte een van de volgende importtypen in behandeling:

* **None** Er zijn geen wijzigingen in de kenmerken van het faseringsobject beschikbaar. De synchronisatieengine markeert dit type niet als in behandeling zijnde import.
* **Toevoegen**. Het faseringsobject is een nieuw importobject in de verbindingsruimte. De synchronisatieengine markeert dit type als in behandeling zijnde import voor extra verwerking in de metaverse.
* **Update**. Synchronisatie-engine vindt een overeenkomstig endend object in de connectorruimte en markeert dit type als in behandeling zijnde import, zodat updates van de kenmerken in de metaverse kunnen worden verwerkt. Updates omvatten objectnaam wijzigen.
* **Verwijderen**. Synchronisatie-engine vindt een overeenkomstig endend object in de connectorruimte en markeert dit type als in behandeling zijnde import, zodat het samengevoegde object kan worden verwijderd.
* **Verwijderen/toevoegen**. Synchronisatie-engine vindt een overeenkomstig endend object in de verbindingsruimte, maar de objecttypen komen niet overeen. In dit geval wordt een wijziging voor het verwijderen van toevoegen gefaseerd doorgevoerd. Een wijziging van de delete-add geeft aan de synchronisatie-engine aan dat een volledige hersynchronisatie van dit object moet plaatsvinden omdat er verschillende regels op dit object van toepassing zijn wanneer het objecttype verandert.

Door de in behandeling zijnde importstatus van een faseringsobject in te stellen, is het mogelijk om de hoeveelheid gegevens die tijdens synchronisatie worden verwerkt aanzienlijk te verminderen, omdat het systeem hierdoor alleen die objecten kan verwerken die bijgewerkte gegevens hebben bijgewerkt.

### <a name="synchronization-process"></a>Synchronisatieproces
Synchronisatie bestaat uit twee gerelateerde processen:

* Binnenkomende synchronisatie, wanneer de inhoud van het metaverse wordt bijgewerkt met behulp van de gegevens in de connectorruimte.
* Uitgaande synchronisatie, wanneer de inhoud van de connectorruimte wordt bijgewerkt met behulp van gegevens in de metaverse.

Door gebruik te maken van de informatie die in de connectorruimte wordt geënsceneerd, creëert het binnenkomende synchronisatieproces in de metaverse de geïntegreerde weergave van de gegevens die in de verbonden gegevensbronnen zijn opgeslagen. Alle tijdelijke objecten of alleen objecten met een importgegevens in behandeling worden samengevoegd, afhankelijk van hoe de regels zijn geconfigureerd.

Het uitgaande synchronisatieproces werkt exportobjecten bij wanneer metaverse objecten worden gewijzigd.

Inkomende synchronisatie creëert de geïntegreerde weergave in de metaverse van de identiteitsgegevens die wordt ontvangen van de verbonden gegevensbronnen. Sync engine kan identiteitsinformatie op elk gewenst moment verwerken met behulp van de meest recente identiteitsgegevens die het heeft van de verbonden gegevensbron.

**Binnenkomende synchronisatie**

Inkomende synchronisatie omvat de volgende processen:

* **Voorziening** (ook wel **Projectie** genoemd als het belangrijk is om dit proces te onderscheiden van uitgaande synchronisatiebepaling). De sync-engine maakt een nieuw metaverse object op basis van een staging object en koppelt ze. Voorziening is een bewerking op objectniveau.
* **Word lid**van . De synchronisatie-engine koppelt een staging-object aan een bestaand metaverse object. Een join is een bewerking op objectniveau.
* **Kenmerkstroom importeren**. Synchronisatie-engine werkt de kenmerkwaarden, de zogenaamde kenmerkstroom, van het object in de metaverse bij. De eigenschappenstroom importeren is een bewerking op kenmerkniveau waarvoor een koppeling nodig is tussen een faseringsobject en een metaverse object.

Voorziening is het enige proces dat objecten in de metaverse maakt. De voorziening is alleen van invloed op het importeren van objecten die zijn gedesjoind objecten. Tijdens de voorziening maakt de synchronisatie-engine een metaverse object dat overeenkomt met het objecttype van het importobject en een koppeling legt tussen beide objecten, waardoor een samengevoegd object ontstaat.

Het joinproces legt ook een koppeling tussen importobjecten en een metaverse object. Het verschil tussen join en bepaling is dat het joinproces vereist dat het importobject is gekoppeld aan een bestaand metaverse object, waarbij het inrichtingsproces een nieuw metaversobject creëert.

Synchronisatie-engine probeert een importobject aan een metaverse object te voegen met behulp van criteria die zijn opgegeven in de configuratie synchronisatieregel.

Tijdens de inrichtings- en joinprocessen koppelt de synchronisatieengine een ontjoind object aan een metaverse object, waardoor ze worden samengevoegd. Nadat deze bewerkingen op objectniveau zijn voltooid, kan de synchronisatieengine de kenmerkwaarden van het bijbehorende metaverseobject bijwerken. Dit proces wordt importkenmerkstroom genoemd.

De invoervan kenmerkstroom vindt plaats op alle importobjecten die nieuwe gegevens bevatten en zijn gekoppeld aan een metaverse object.

**Uitgaande synchronisatie**

Uitgaande synchronisatie-updates exporteren objecten wanneer een metaverse object veranderen, maar niet wordt verwijderd. Het doel van uitgaande synchronisatie is om te evalueren of wijzigingen in metaverse objecten updates vereisen voor het meten van objecten in de verbindingsruimten. In sommige gevallen kunnen de wijzigingen vereisen dat tijdelijke objecten in alle verbindingsruimten worden bijgewerkt. Tijdelijke objecten die zijn gewijzigd, worden gemarkeerd als in behandeling zijnde export, waardoor ze objecten exporteren. Deze exportobjecten worden later tijdens het exportproces naar de verbonden gegevensbron geduwd.

Uitgaande synchronisatie heeft drie processen:

* **Inrichten**
* **Deprovisioning**
* **Kenmerkstroom exporteren**

Provisioning en deprovisioning zijn beide object-level operations. Deprovisioning is afhankelijk van provisioning omdat alleen provisioning deze kan initiëren. Deprovisioning wordt geactiveerd wanneer provisioning de koppeling tussen een metaverse object en een exportobject verwijdert.

Inrichten wordt altijd geactiveerd wanneer wijzigingen worden toegepast op objecten in de metaverse. Wanneer wijzigingen worden aangebracht in metaverse objecten, kan de synchronisatieengine een van de volgende taken uitvoeren als onderdeel van het inrichtingsproces:

* Maak samengevoegde objecten, waarbij een metaverse object is gekoppeld aan een nieuw gemaakt exportobject.
* Wijzig de naam van een samengevoegd object.
* Schakel koppelingen tussen een metaverse object en staging objecten, het creëren van een ontjoined object.

Als voor inrichten de synchronisatie-engine nodig is om een nieuw verbindingsobject te maken, is het staging-object waaraan het metaverseobject is gekoppeld altijd een exportobject, omdat het object nog niet bestaat in de verbonden gegevensbron.

Als voor inrichten de synchronisatieengine vereist is dat een samengevoegd object wordt gedesjoind, wordt het maken van een samengevoegd object geactiveerd. Met het deprovisioneringsproces wordt het object verwijderd.

Als u tijdens het verwijderen een exportobject verwijdert, wordt het object niet fysiek verwijderd. Het object wordt gemarkeerd als **verwijderd,** wat betekent dat de verwijderingsbewerking op het object wordt geënsceneerd.

Exportkenmerkstroom vindt ook plaats tijdens het uitgaande synchronisatieproces, vergelijkbaar met de manier waarop de invoervan kenmerkstroom optreedt tijdens inkomende synchronisatie. Exportkenmerkstroom vindt alleen plaats tussen metaverse en exportobjecten die zijn samengevoegd.

### <a name="export-process"></a>Exportproces
Tijdens het exportproces onderzoekt de synchronisatieengine alle exportobjecten die zijn gemarkeerd als in behandeling zijnde export in de verbindingsruimte en verzendt vervolgens updates naar de verbonden gegevensbron.

De synchronisatie-engine kan het succes van een export bepalen, maar kan niet voldoende bepalen dat het identiteitsbeheerproces is voltooid. Objecten in de verbonden gegevensbron kunnen altijd worden gewijzigd door andere processen. Omdat de synchronisatieengine geen permanente verbinding met de verbonden gegevensbron heeft, volstaat het niet om veronderstellingen te maken over de eigenschappen van een object in de verbonden gegevensbron alleen op basis van een succesvolle exportmelding.

Een proces in de verbonden gegevensbron kan bijvoorbeeld de kenmerken van het object weer wijzigen naar hun oorspronkelijke waarden (dat wil zeggen dat de verbonden gegevensbron de waarden kan overschrijven onmiddellijk nadat de gegevens door de synchronisatieengine zijn verwijderd en met succes zijn toegepast in de verbonden gegevensbron).

De synchronisatieengine slaat export- en importstatusgegevens op over elk faseringsobject. Als de waarden van de kenmerken die zijn opgegeven in de lijst met kenmerkopneming zijn gewijzigd sinds de laatste export, kan de opslag van import- en exportstatus de synchronisatieengine op de juiste manier reageren. Synchronisatieengine gebruikt het importproces om kenmerkwaarden te bevestigen die naar de verbonden gegevensbron zijn geëxporteerd. Een vergelijking tussen de ingevoerde en geëxporteerde informatie, zoals weergegeven in de volgende afbeelding, stelt sync engine in staat om te bepalen of de export succesvol was of dat deze moet worden herhaald.

![Boog7](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Als bijvoorbeeld het exportkenmerk C van synchronisatieengine, dat een waarde van 5 heeft, wordt opgeslagen in een verbonden gegevensbron, c=5 in het geheugen van de exportstatus. Elke extra export op dit object resulteert in een poging om C=5 opnieuw naar de verbonden gegevensbron te exporteren, omdat de synchronisatie-engine ervan uitgaat dat deze waarde niet voortdurend is toegepast op het object (dat wil zeggen, tenzij onlangs een andere waarde is geïmporteerd verbonden gegevensbron). Het exportgeheugen wordt gewist wanneer C=5 wordt ontvangen tijdens een importbewerking op het object.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [synchronisatieconfiguratie van Azure AD Connect.](how-to-connect-sync-whatis.md)

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).

