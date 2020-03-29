---
title: 'Azure AD Connect: declaratieve inrichting begrijpen | Microsoft Documenten'
description: Hiermee wordt het declaratieve configuratiemodel voor het inrichten in Azure AD Connect uitgelegd.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 543c1a6706f794b81c4f93fc6fff3a61ed3fb9e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60246361"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect-synchronisatie: declaratieve inrichting begrijpen
In dit onderwerp wordt het configuratiemodel in Azure AD Connect uitgelegd. Het model heet Declarative Provisioning en het stelt u in staat om een configuratiewijziging met gemak aan te brengen. Veel dingen beschreven in dit onderwerp zijn geavanceerd en niet vereist voor de meeste klantscenario's.

## <a name="overview"></a>Overzicht
Declaratieve inrichting is het verwerken van objecten die binnenkomen uit een bron verbonden map en bepaalt hoe het object en de kenmerken moeten worden getransformeerd van een bron naar een doel. Een object wordt verwerkt in een synchronisatiepijplijn en de pijplijn is hetzelfde voor binnenkomende en uitgaande regels. Een inkomende regel is van een connectorruimte naar de metaverse en een uitgaande regel is van de metaverse naar een connectorruimte.

![Pijplijn synchroniseren](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

De pijplijn heeft verschillende modules. Elk concept is verantwoordelijk voor één concept in objectsynchronisatie.

![Pijplijn synchroniseren](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Bron, Het bronobject
* [Bereik](#scope), zoekt alle synchronisatieregels die binnen het bereik vallen
* [Join](#join), Bepaalt de relatie tussen connectorruimte en metaverse
* Transformeren, berekent hoe kenmerken moeten worden getransformeerd en stromen
* [Voorrang](#precedence), Hiermee worden conflicterende kenmerkbijdragen opgelost
* Doel, Het doelobject

## <a name="scope"></a>Bereik
De scopemodule evalueert een object en bepaalt de regels die binnen het toepassingsgebied vallen en in de verwerking moeten worden opgenomen. Afhankelijk van de kenmerkenwaarden op het object worden verschillende synchronisatieregels geëvalueerd om binnen het bereik te zijn. Een uitgeschakelde gebruiker zonder Exchange-postvak heeft bijvoorbeeld andere regels dan een ingeschakelde gebruiker met een postvak.  
![Bereik](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

Het toepassingsgebied wordt gedefinieerd als groepen en clausules. De clausules zijn binnen een groep. Een logische AND wordt gebruikt tussen alle clausules in een groep. Bijvoorbeeld (afdeling =IT EN land = Denemarken). Er wordt een logische OK gebruikt tussen groepen.

![Bereik](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
De reikwijdte op deze foto moet worden gelezen als (afdeling = IT EN land = Denemarken) OF (land=Zweden). Als groep 1 of groep 2 wordt geëvalueerd op true, is de regel in het bereik.

De scopemodule ondersteunt de volgende bewerkingen.

| Bewerking | Beschrijving |
| --- | --- |
| GELIJK, NOTEQUAL |Een tekenreeksvergelijking die evalueert of de waarde gelijk is aan de waarde in het kenmerk. Zie ISIN en ISNOTIN voor kenmerken met meerdere waarden. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Een tekenreeksvergelijking die evalueert of de waarde kleiner is dan de waarde in het kenmerk. |
| CONTAINS, NOTCONTAINS |Een tekenreeksvergelijking die evalueert of de waarde ergens binnen de waarde in het kenmerk kan worden gevonden. |
| BEGINT MET, NOTSTARTSWITH |Een tekenreeksvergelijking die evalueert of de waarde zich in het begin van de waarde in het kenmerk bevindt. |
| ENDSWITH, NOTENDSWITH |Een tekenreeksvergelijking die evalueert of de waarde zich aan het einde van de waarde in het kenmerk bevindt. |
| GROTER DAN, GREATERTHAN_OR_EQUAL |Een tekenreeksvergelijking die evalueert of de waarde groter is dan de waarde in het kenmerk. |
| ISNULL, ISNOTNULL |Evalueert of het kenmerk afwezig is in het object. Als het kenmerk niet aanwezig is en dus null, dan is de regel in het toepassingsgebied. |
| ISIN, ISNOTIN |Evalueert of de waarde aanwezig is in het gedefinieerde kenmerk. Deze bewerking is de multi-gewaardeerde variant van EQUAL en NOTEQUAL. Het kenmerk wordt verondersteld een kenmerk met meerdere waarden te zijn en als de waarde kan worden gevonden in een van de kenmerkwaarden, is de regel in het bereik. |
| ISBITSET, ISNOTBITSET |Evalueert of een bepaalde bit is ingesteld. U bijvoorbeeld de bits in userAccountControl evalueren om te zien of een gebruiker is ingeschakeld of uitgeschakeld. |
| ISMEMBEROF, ISNOTMEMBEROF |De waarde moet een DN bevatten voor een groep in de verbindingsruimte. Als het object lid is van de opgegeven groep, is de regel in het bereik. |

## <a name="join"></a>Koppelen
De joinmodule in de synchronisatiepijplijn is verantwoordelijk voor het vinden van de relatie tussen het object in de bron en een object in het doel. Bij een binnenkomende regel zou deze relatie een object in een verbindingsruimte zijn bij het vinden van een relatie met een object in de metaverse.  
![Deelnemen tussen cs en mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
Het doel is om te zien of er een object al in de metaverse, gemaakt door een andere connector, moet worden gekoppeld aan. In een accountbronforest moet de gebruiker van het accountforest bijvoorbeeld worden samengevoegd met de gebruiker uit het bronforest.

Joins worden meestal gebruikt op binnenkomende regels om verbindingsruimteobjecten samen te voegen met hetzelfde metaverse object.

De joins worden gedefinieerd als een of meer groepen. Binnen een groep heb je clausules. Een logische AND wordt gebruikt tussen alle clausules in een groep. Er wordt een logische OK gebruikt tussen groepen. De groepen worden van boven naar beneden in volgorde verwerkt. Wanneer een groep precies één overeenkomst heeft gevonden met een object in het doel, worden er geen andere joinregels geëvalueerd. Als er nul of meer dan één object wordt gevonden, gaat de verwerking door naar de volgende groep regels. Om deze reden moeten de regels worden gemaakt in de volgorde van de meest expliciete eerste en meer fuzzy aan het einde.  
![Lid worden van definitie](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
De joins in deze afbeelding worden van boven naar beneden verwerkt. Eerst ziet de synchronisatiepijplijn of er een overeenkomst is op employeeID. Als dit niet het zo is, wordt in de tweede regel gezien of de accountnaam kan worden gebruikt om de objecten samen te voegen. Als dat ook geen overeenkomst is, is de derde en laatste regel een meer vage overeenkomst met behulp van de naam van de gebruiker.

Als alle joinregels zijn geëvalueerd en er niet precies één overeenkomst is, wordt het **koppelingstype** op de pagina **Beschrijving** gebruikt. Als deze optie is ingesteld op **Voorziening,** wordt een nieuw object in het doel gemaakt.  
![Voorziening of lid worden](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Een object mag slechts één synchronisatieregel hebben met joinregels in het bereik. Als er meerdere synchronisatieregels zijn waarbij join wordt gedefinieerd, treedt er een fout op. Voorrang wordt niet gebruikt om joinconflicten op te lossen. Een object moet een joinregel hebben in het bereik voor kenmerken die met dezelfde inkomende/uitgaande richting kunnen stromen. Als u zowel inkomende als uitgaande kenmerken naar hetzelfde object moet stromen, moet u zowel een binnenkomende als een uitgaande synchronisatieregel hebben met join.

Uitgaande join heeft een speciaal gedrag wanneer het probeert om een object in te richten op een doelconnector ruimte. Het kenmerk DN wordt gebruikt om eerst een reverse-join uit te proberen. Als er al een object in de doelconnectorruimte met dezelfde DN staat, worden de objecten samengevoegd.

De joinmodule wordt slechts één keer geëvalueerd wanneer een nieuwe synchronisatieregel in het bereik komt. Wanneer een object is lid geworden, wordt het niet meer afaangesloten, zelfs niet als niet langer aan de joincriteria wordt voldaan. Als u een object wilt losmaken, moet de synchronisatieregel die is toegetreden tot de objecten buiten het bereik vallen.

### <a name="metaverse-delete"></a>Metaverse verwijderen
Een metaverse object blijft staan zolang er één synchronisatieregel in het bereik is met **Koppelingstype** ingesteld op **Voorziening** of **StickyJoin**. Een StickyJoin wordt gebruikt wanneer een connector geen nieuw object aan de metaverse mag inrichten, maar wanneer het lid is geworden, moet het in de bron worden verwijderd voordat het metaverse object wordt verwijderd.

Wanneer een metaverse object wordt verwijderd, worden alle objecten die zijn gekoppeld aan een uitgaande synchronisatieregel die is gemarkeerd voor **voorziening** gemarkeerd voor een verwijdering gemarkeerd.

## <a name="transformations"></a>Transformaties
De transformaties worden gebruikt om te definiëren hoe kenmerken van de bron naar het doel moeten stromen. De stromen kunnen een van de volgende **stroomtypen**hebben: Direct, Constant of Expressie. Een directe stroom, stroomt een attribuutwaarde zoals-is zonder extra transformaties. Met een constante waarde wordt de opgegeven waarde ingesteld. Een expressie gebruikt de declaratieve indatieexpressietaal om uit te drukken hoe de transformatie moet zijn. De details voor de expressietaal zijn te vinden in het [artikel declaratieve inrichting van expressietaalonderwerp.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)

![Voorziening of lid worden](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

Het selectievakje **Toepassen zodra,** definieert dat het kenmerk alleen moet worden ingesteld wanneer het object in eerste instantie wordt gemaakt. Deze configuratie kan bijvoorbeeld worden gebruikt om een eerste wachtwoord in te stellen voor een nieuw gebruikersobject.

### <a name="merging-attribute-values"></a>Kenmerkwaarden samenvoegen
In de kenmerkstromen is er een instelling om te bepalen of kenmerken met meerdere waarden moeten worden samengevoegd vanuit verschillende connectors. De standaardwaarde is **Bijwerken,** wat aangeeft dat de synchronisatieregel met de hoogste prioriteit moet winnen.

![Samenvoegingstypen](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Er is ook **Merge** en **MergeCaseInsensitive**. Met deze opties u waarden uit verschillende bronnen samenvoegen. Het kan bijvoorbeeld worden gebruikt om het kenmerk member of proxyAddresses uit verschillende forests samen te voegen. Wanneer u deze optie gebruikt, moeten alle synchronisatieregels in het bereik van een object hetzelfde samenvoegtype gebruiken. U **Bijwerken** niet definiëren vanaf de ene connector en **samenvoegen** vanuit een andere connector. Als u het probeert, ontvangt u een foutmelding.

Het verschil tussen **Samenvoegen** en **MergeCaseInsensitive** is het verwerken van dubbele kenmerkwaarden. De synchronisatieengine zorgt ervoor dat dubbele waarden niet in het doelkenmerk worden ingevoegd. Met **MergeCaseInsensitive**zullen dubbele waarden met slechts een verschil in het geval niet aanwezig zijn. U moet bijvoorbeeld niet zowelSMTP:bob@contoso.com"smtp:bob@contoso.com" als " in het doelkenmerk zien. **Bij Merge** wordt alleen gekeken naar de exacte waarden en meerdere waarden waarbij er alleen een verschil is in het geval aanwezig zou kunnen zijn.

De optie **Vervangen** is hetzelfde als **Bijwerken,** maar het wordt niet gebruikt.

### <a name="control-the-attribute-flow-process"></a>Het kenmerkstroomproces beheren
Wanneer meerdere binnenkomende synchronisatieregels zijn geconfigureerd om bij te dragen aan hetzelfde metaverse kenmerk, wordt voorrang gebruikt om de winnaar te bepalen. De synchronisatieregel met hoogste prioriteit (laagste numerieke waarde) draagt de waarde bij. Hetzelfde gebeurt voor uitgaande regels. De synchronisatieregel met de hoogste prioriteit wint en draagt de waarde bij aan de verbonden map.

In sommige gevallen moet de synchronisatieregel bepalen hoe andere regels zich moeten gedragen in plaats van een waarde bij te dragen. Er zijn een aantal speciale literals gebruikt voor deze zaak.

Voor binnenkomende synchronisatieregels kan de letterlijke **NULL** worden gebruikt om aan te geven dat de stroom geen waarde heeft om bij te dragen. Een andere regel met een lagere prioriteit kan een waarde bijdragen. Als er geen regel heeft bijgedragen aan een waarde, wordt het metaverse kenmerk verwijderd. Als **NULL** voor een uitgaande regel de eindwaarde is nadat alle synchronisatieregels zijn verwerkt, wordt de waarde verwijderd in de verbonden map.

De letterlijke **AuthoritativeNull** is vergelijkbaar met **NULL,** maar met het verschil dat er geen lagere voorrangsregels een waarde kunnen bijdragen.

Een kenmerkstroom kan ook **IgnoreThisFlow**gebruiken. Het is vergelijkbaar met NULL in de zin dat het aangeeft dat er niets bij te dragen. Het verschil is dat het een reeds bestaande waarde in het doel niet verwijdert. Het is alsof de attribuutstroom er nooit is geweest.

Hier volgt een voorbeeld:

In *Uit naar AD - User Exchange-hybride* is de volgende stroom te vinden:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Deze expressie moet worden gelezen als: als het gebruikerspostvak zich in Azure AD bevindt, voert u het kenmerk vervolgens van Azure AD naar AD. Zo niet, dan hoeft u niets terug te sturen naar Active Directory. In dit geval zou het de bestaande waarde in AD behouden.

### <a name="importedvalue"></a>Geïmporteerde waarde
De functie Geïmporteerdwaarde is anders dan alle andere functies, omdat de kenmerknaam moet worden ingesloten tussen aanhalingstekens in plaats van vierkante haakjes:  
`ImportedValue("proxyAddresses")`.

Meestal gebruikt een kenmerk tijdens synchronisatie de verwachte waarde, zelfs als het nog niet is geëxporteerd of als er een fout is ontvangen tijdens het exporteren ("boven in de toren"). Een binnenkomende synchronisatie gaat ervan uit dat een kenmerk dat nog geen verbonden map heeft bereikt, het uiteindelijk bereikt. In sommige gevallen is het belangrijk om alleen een waarde te synchroniseren die is bevestigd door de verbonden map ("hologram en delta import tower").

Een voorbeeld van deze functie is te vinden in de out-of-box synchronisatieregel *in van AD – User Common from Exchange*. In Hybrid Exchange mag de toegevoegde waarde van Exchange online alleen worden gesynchroniseerd wanneer is bevestigd dat de waarde met succes is geëxporteerd:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Prioriteit
Wanneer verschillende synchronisatieregels dezelfde kenmerkwaarde aan het doel proberen bij te dragen, wordt de prioriteitswaarde gebruikt om de winnaar te bepalen. De regel met de hoogste prioriteit, de laagste numerieke waarde, zal het kenmerk bijdragen in een conflict.

![Samenvoegingstypen](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Deze volgorde kan worden gebruikt om preciezere kenmerkstromen voor een kleine subset van objecten te definiëren. De kant-en-klare regels zorgen er bijvoorbeeld voor dat kenmerken van een ingeschakeld account (**User AccountEnabled**) voorrang hebben van andere accounts.

Voorrang kan worden gedefinieerd tussen connectors. Dat stelt Connectors met betere gegevens in staat om eerst waarden bij te dragen.

### <a name="multiple-objects-from-the-same-connector-space"></a>Meerdere objecten uit dezelfde verbindingsruimte
Als u meerdere objecten in dezelfde verbindingsruimte hebt aangesloten op hetzelfde metaverse object, moet de voorrang worden aangepast. Als meerdere objecten zich in het bereik van dezelfde synchronisatieregel bevinden, kan de synchronisatieengine de voorrang niet bepalen. Het is onduidelijk welk bronobject de waarde aan het metaverse moet bijdragen. Deze configuratie wordt gerapporteerd als dubbelzinnig, zelfs als de kenmerken in de bron dezelfde waarde hebben.  
![Meerdere objecten die zijn samengevoegd met hetzelfde mv-object](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

Voor dit scenario moet u het bereik van de synchronisatieregels wijzigen, zodat de bronobjecten verschillende synchronisatieregels in het bereik hebben. Dat stelt u in staat om verschillende voorrang te definiëren.  
![Meerdere objecten die zijn samengevoegd met hetzelfde mv-object](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Volgende stappen
* Lees meer over de expressietaal in [Het begrijpen van declaratieve inrichtingsuitdrukkingen](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Bekijk hoe declaratieve inrichting out-of-box wordt gebruikt in Het begrijpen van [de standaardconfiguratie.](concept-azure-ad-connect-sync-default-configuration.md)
* Zie hoe u een praktische wijziging aanbrengen met declaratieve inrichting in [Hoe u een wijziging in de standaardconfiguratie aanbrengen.](how-to-connect-sync-change-the-configuration.md)
* Lees verder hoe gebruikers en contactpersonen samenwerken in [Gebruikers en contactpersonen begrijpen.](concept-azure-ad-connect-sync-user-and-contacts.md)

**Overzichtsonderwerpen**

* [Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)

**Onderwerpen met naslaginformatie**

* [Synchronisatie van Azure AD Connect: naslaginformatie over functies](reference-connect-sync-functions-reference.md)
