---
title: Concepten voor azure-gegevenscatalogusontwikkelaars
description: Inleiding tot de belangrijkste concepten in het conceptmodel azure datacatalogus, zoals weergegeven via de API Voor catalogusREST.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 80adc98255cfc9145d583ac775bbc490d599234e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976832"
---
# <a name="azure-data-catalog-developer-concepts"></a>Concepten voor azure-gegevenscatalogusontwikkelaars
Microsoft **Azure Data Catalog** is een volledig beheerde cloudservice die mogelijkheden biedt voor het ontdekken van gegevensbronnen en voor crowdsourcing-gegevensbronmetagegevens. Ontwikkelaars kunnen de service gebruiken via de REST API's. Inzicht in de concepten die in de service zijn geïmplementeerd, is belangrijk voor ontwikkelaars om succesvol te integreren met **Azure Data Catalog.**

## <a name="key-concepts"></a>Belangrijkste concepten
Het conceptmodel **azure-gegevenscatalogus** is gebaseerd op vier belangrijke concepten: de **catalogus**, **gebruikers,** **assets**en **annotaties**.

![Afbeelding van conceptueel model azure-gegevenscatalogus](./media/data-catalog-developer-concepts/concept2.png)

*Figuur 1 - Vereenvoudigd conceptueel model azure-gegevenscatalogus*

### <a name="catalog"></a>Catalogus
Een **catalogus** is de container op het hoogste niveau voor alle metagegevens die een organisatie opslaat. Er is één **catalogus** toegestaan per Azure-account. Catalogi zijn gekoppeld aan een Azure-abonnement, maar er kan slechts één **catalogus** worden gemaakt voor een bepaald Azure-account, ook al kan een account meerdere abonnementen hebben.

Een catalogus bevat **gebruikers** en **assets**.

### <a name="users"></a>Gebruikers
Gebruikers zijn beveiligingsprincipals die machtigingen hebben om acties uit te voeren (zoeken in de catalogus, items toevoegen, bewerken of verwijderen, enz.) in de catalogus.

Er zijn verschillende rollen die een gebruiker kan hebben. Zie de sectie Rollen en Autorisatie voor informatie over rollen.

Individuele gebruikers en beveiligingsgroepen kunnen worden toegevoegd.

Azure Data Catalog gebruikt Azure Active Directory voor identiteits- en toegangsbeheer. Elke catalogusgebruiker moet lid zijn van de Active Directory voor het account.

### <a name="assets"></a>Assets
Een **catalogus** bevat gegevenselementen. **Assets** zijn de eenheid granulariteit die wordt beheerd door de catalogus.

De granulariteit van een asset verschilt per gegevensbron. Voor SQL Server of Oracle Database kan een item een tabel of een weergave zijn. Voor SQL Server Analysis Services kan een asset een meting, een dimensie of een KPI (Key Performance Indicator) zijn. Voor SQL Server Reporting Services is een asset een rapport.

Een **asset** is het ding dat u toevoegt of verwijdert uit een catalogus. Het is de eenheid van het resultaat krijg je terug van **Zoeken**.

Een **asset** bestaat uit de naam, locatie en type en annotaties die het verder beschrijven.

### <a name="annotations"></a>Aantekeningen
Annotaties zijn items die metagegevens over assets vertegenwoordigen.

Voorbeelden van annotaties zijn beschrijving, tags, schema, documentatie, enz. Een volledige lijst met de activatypen en annotatietypen vindt u in de sectie Asset Object-model.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Crowdsourcing annotaties en gebruikersperspectief (veelheid van mening)
Een belangrijk aspect van Azure Data Catalog is hoe het de crowdsourcing van metadata in het systeem ondersteunt. In tegenstelling tot een wiki-benadering - waarbij er slechts één mening is en de laatste schrijver wint - staat het Azure Data Catalog-model meerdere meningen toe om naast elkaar in het systeem te leven.

Deze aanpak weerspiegelt de echte wereld van bedrijfsgegevens waar verschillende gebruikers verschillende perspectieven op een bepaald actief kunnen hebben:

* Een databasebeheerder kan informatie verstrekken over overeenkomsten op serviceniveau of het beschikbare verwerkingsvenster voor bulk-ETL-bewerkingen
* Een gegevensbeheerder kan informatie verstrekken over de bedrijfsprocessen waarop het actief van toepassing is, of de classificaties die het bedrijf op het bedrijf heeft toegepast
* Een financieel analist kan informatie verstrekken over hoe de gegevens worden gebruikt tijdens rapportagetaken aan het einde van de periode

Om dit voorbeeld te ondersteunen, kan elke gebruiker – de DBA, de gegevensbeheerder en de analist – een beschrijving toevoegen aan één tabel die is geregistreerd in de catalogus. Alle beschrijvingen worden in het systeem onderhouden en in de Azure Data Catalog-portal worden alle beschrijvingen weergegeven.

Dit patroon wordt toegepast op de meeste items in het objectmodel, dus objecttypen in de JSON-payload zijn vaak arrays voor eigenschappen waarvan u een singleton zou verwachten.

Onder de assetroot bevindt zich bijvoorbeeld een array met beschrijvingsobjecten. De arrayeigenschap heeft de naam 'beschrijvingen' genoemd. Een beschrijvingsobject heeft één eigenschap - beschrijving. Het patroon is dat elke gebruiker die beschrijving typt, een beschrijvingsobject krijgt dat is gemaakt voor de waarde die door de gebruiker wordt geleverd.

De UX kan vervolgens kiezen hoe de combinatie wordt weergegeven. Er zijn drie verschillende patronen voor weergave.

* Het eenvoudigste patroon is "Toon alles". In dit patroon worden alle objecten weergegeven in een lijstweergave. De UX van de Azure Data Catalog-portal gebruikt dit patroon voor beschrijving.
* Een ander patroon is "Samenvoegen". In dit patroon worden alle waarden van de verschillende gebruikers samengevoegd, waarbij duplicaat wordt verwijderd. Voorbeelden van dit patroon in de UX van de Azure Data Catalog-portal zijn de eigenschappen van de tags en experts.
* Een derde patroon is "laatste schrijver wint". In dit patroon wordt alleen de meest recente waarde weergegeven. friendlyName is een voorbeeld van dit patroon.

## <a name="asset-object-model"></a>Objectmodel asset
Zoals geïntroduceerd in de sectie Sleutelconcepten, bevat het objectmodel **Azure Data Catalog** items, die activa of annotaties kunnen zijn. Items hebben eigenschappen, die optioneel of vereist kunnen zijn. Sommige eigenschappen zijn van toepassing op alle artikelen. Sommige eigenschappen zijn van toepassing op alle activa. Sommige eigenschappen zijn alleen van toepassing op specifieke activatypen.

### <a name="system-properties"></a>Systeemeigenschappen
<table><tr><td><b>Naam van eigenschap</b></td><td><b>Gegevenstype</b></td><td><b>Opmerkingen</b></td></tr><tr><td>tijdstempel</td><td>DateTime</td><td>De laatste keer dat het item is gewijzigd. Dit veld wordt gegenereerd door de server wanneer een item wordt ingevoegd en elke keer dat een item wordt bijgewerkt. De waarde van deze eigenschap wordt genegeerd bij invoer van publicatiebewerkingen.</td></tr><tr><td>id</td><td>Uri</td><td>Absolute url van het item (alleen-lezen). Het is de unieke adresseerbare URI voor het item.  De waarde van deze eigenschap wordt genegeerd bij invoer van publicatiebewerkingen.</td></tr><tr><td>type</td><td>Tekenreeks</td><td>Het type asset (alleen-lezen).</td></tr><tr><td>etag etag</td><td>Tekenreeks</td><td>Een tekenreeks die overeenkomt met de versie van het item die kan worden gebruikt voor optimistisch gelijktijdigheidsbeheer bij het uitvoeren van bewerkingen die items in de catalogus bijwerken. "*" kan worden gebruikt om elke waarde te evenaren.</td></tr></table>

### <a name="common-properties"></a>Algemene eigenschappen
Deze eigenschappen zijn van toepassing op alle onderliggende activatypen en alle annotatietypen.

<table>
<tr><td><b>Naam van eigenschap</b></td><td><b>Gegevenstype</b></td><td><b>Opmerkingen</b></td></tr>
<tr><td>fromSourceSystem</td><td>Booleaans</td><td>Hiermee geeft u aan of de gegevens van het item afkomstig zijn van een bronsysteem (zoals Sql Server Database, Oracle Database) of zijn geschreven door een gebruiker.</td></tr>
</table>

### <a name="common-root-properties"></a>Algemene hoofdeigenschappen
<p>
Deze eigenschappen zijn van toepassing op alle onderliggende activatypen.

<table><tr><td><b>Naam van eigenschap</b></td><td><b>Gegevenstype</b></td><td><b>Opmerkingen</b></td></tr><tr><td>name</td><td>Tekenreeks</td><td>Een naam die is afgeleid van de locatiegegevens van de gegevensbron</td></tr><tr><td>Dsl</td><td>DataSourceLocatie</td><td>Uniek beschrijft de gegevensbron en is een van de id's voor het item. (Zie dubbele identiteit sectie).  De structuur van de dsl varieert per protocol en brontype.</td></tr><tr><td>Datasource</td><td>DataSourceInfo</td><td>Meer details over het type asset.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Beschrijft de gebruiker die dit actief het meest heeft geregistreerd.  Bevat zowel de unieke id voor de gebruiker (de upn) als een weergavenaam (voornaam en voornaam).</td></tr><tr><td>containerId</td><td>Tekenreeks</td><td>Id van het containeractief voor de gegevensbron. Deze eigenschap wordt niet ondersteund voor het type Container.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Algemene niet-singleton-annotatie-eigenschappen
Deze eigenschappen zijn van toepassing op alle niet-singleton annotatietypen (annotaties, die meerdere per actief mogen zijn).

<table>
<tr><td><b>Naam van eigenschap</b></td><td><b>Gegevenstype</b></td><td><b>Opmerkingen</b></td></tr>
<tr><td>sleutel</td><td>Tekenreeks</td><td>Een door de gebruiker opgegeven sleutel, die de annotatie in de huidige verzameling uniek identificeert. De sleutellengte mag niet hoger zijn dan 256 tekens.</td></tr>
</table>

### <a name="root-asset-types"></a>Basisactivatypen
Rootassettypen zijn de typen die de verschillende soorten gegevenselementen vertegenwoordigen die in de catalogus kunnen worden geregistreerd. Voor elk hoofdtype is er een weergave, waarin activa en annotaties in de weergave worden beschreven. Weergavenaam moet worden gebruikt in het bijbehorende {view_name} url-segment bij het publiceren van een asset met REST API.

<table><tr><td><b>Asset Type (Weergavenaam)</b></td><td><b>Aanvullende eigenschappen</b></td><td><b>Gegevenstype</b></td><td><b>Annotaties toegestaan</b></td><td><b>Opmerkingen</b></td></tr><tr><td>Tabel ('tabellen')</td><td></td><td></td><td>Beschrijving<p>FriendlyName<p>Label<p>Schema<p>ColumnBeschrijving<p>Kolomtag<p> Expert<p>Preview<p>AccessInstruction<p>TabelDataProfiel<p>ColumnDataProfiel<p>ColumnDataClassificatie<p>Documentatie<p></td><td>Een tabel vertegenwoordigt tabelgegevens.  Bijvoorbeeld: SQL Table, SQL View, Analysis Services Tabular Table, Analysis Services Multidimensional dimension, Oracle Table, etc.   </td></tr><tr><td>Maatregel ("maatregelen")</td><td></td><td></td><td>Beschrijving<p>FriendlyName<p>Label<p>Expert<p>AccessInstruction<p>Documentatie<p></td><td>Dit type vertegenwoordigt een analyseservicesmeting.</td></tr><tr><td></td><td>Maatregel</td><td>Kolom</td><td></td><td>Metagegevens die de maatregel beschrijven</td></tr><tr><td></td><td>isBerekend </td><td>Booleaans</td><td></td><td>Hiermee geeft u op of de meting is berekend of niet.</td></tr><tr><td></td><td>maatregelGroep</td><td>Tekenreeks</td><td></td><td>Fysieke container voor meting</td></tr><td>KPI ("kpi's")</td><td></td><td></td><td>Beschrijving<p>FriendlyName<p>Label<p>Expert<p>AccessInstruction<p>Documentatie</td><td></td></tr><tr><td></td><td>maatregelGroep</td><td>Tekenreeks</td><td></td><td>Fysieke container voor meting</td></tr><tr><td></td><td>doelExpressie</td><td>Tekenreeks</td><td></td><td>Een MDX-numerieke expressie of een berekening die de doelwaarde van de KPI retourneert.</td></tr><tr><td></td><td>waardeExpressie</td><td>Tekenreeks</td><td></td><td>Een MDX-numerieke expressie die de werkelijke waarde van de KPI retourneert.</td></tr><tr><td></td><td>statusExpressie</td><td>Tekenreeks</td><td></td><td>Een MDX-expressie die de status van de KPI op een bepaald tijdstip weergeeft.</td></tr><tr><td></td><td>trendExpression</td><td>Tekenreeks</td><td></td><td>Een MDX-expressie die de waarde van de KPI in de loop van de tijd evalueert. De trend kan elk op tijd gebaseerd criterium zijn dat nuttig is in een specifieke bedrijfscontext.</td>
<tr><td>Verslag ("rapporten")</td><td></td><td></td><td>Beschrijving<p>FriendlyName<p>Label<p>Expert<p>AccessInstruction<p>Documentatie<p></td><td>Dit type vertegenwoordigt een SQL Server Reporting Services-rapport </td></tr><tr><td></td><td>assetCreatedDate</td><td>Tekenreeks</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Tekenreeks</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Tekenreeks</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Tekenreeks</td><td></td><td></td></tr><tr><td>Container ("containers")</td><td></td><td></td><td>Beschrijving<p>FriendlyName<p>Label<p>Expert<p>AccessInstruction<p>Documentatie<p></td><td>Dit type vertegenwoordigt een container met andere elementen, zoals een SQL-database, een Azure Blobs-container of een Analysis Services-model.</td></tr></table>

### <a name="annotation-types"></a>Annotatietypen
Annotatietypen vertegenwoordigen typen metagegevens die kunnen worden toegewezen aan andere typen in de catalogus.

<table>
<tr><td><b>Annotatietype (naam geneste weergave)</b></td><td><b>Aanvullende eigenschappen</b></td><td><b>Gegevenstype</b></td><td><b>Opmerkingen</b></td></tr>

<tr><td>Beschrijving ("beschrijvingen")</td><td></td><td></td><td>Deze eigenschap bevat een beschrijving voor een actief. Elke gebruiker van het systeem kan zijn eigen beschrijving toevoegen.  Alleen die gebruiker kan het object Beschrijving bewerken.  (Beheerders en eigenaren van activa kunnen het object Beschrijving verwijderen, maar niet bewerken). Het systeem onderhoudt de beschrijvingen van gebruikers afzonderlijk.  Zo is er een scala aan beschrijvingen op elk actief (een voor elke gebruiker die heeft bijgedragen hun kennis over het actief, in aanvulling op eventueel een die informatie afkomstig van de gegevensbron bevat).</td></tr>
<tr><td></td><td>description</td><td>tekenreeks</td><td>Een korte beschrijving (2-3 lijnen) van het actief</td></tr>

<tr><td>Tag ("tags")</td><td></td><td></td><td>Deze eigenschap definieert een tag voor een actief. Elke gebruiker van het systeem kan meerdere tags toevoegen voor een asset.  Alleen de gebruiker die tagobjecten heeft gemaakt, kan deze bewerken.  (Beheerders en eigenaren van activa kunnen het tagobject verwijderen, maar niet bewerken). Het systeem onderhoudt de tags van gebruikers afzonderlijk.  Er is dus een array van Tag-objecten op elk item.</td></tr>
<tr><td></td><td>tag</td><td>tekenreeks</td><td>Een tag die de asset beschrijft.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Deze eigenschap bevat een vriendelijke naam voor een actief. FriendlyName is een singleton-annotatie - er kan slechts één FriendlyName aan een asset worden toegevoegd.  Alleen de gebruiker die het object FriendlyName heeft gemaakt, kan het object bewerken. (Beheerders en eigenaren van activa kunnen het object FriendlyName verwijderen, maar niet bewerken). Het systeem onderhoudt de vriendelijke namen van gebruikers afzonderlijk.</td></tr>
<tr><td></td><td>Friendlyname</td><td>tekenreeks</td><td>Een vriendelijke naam van de aanwinst.</td></tr>

<tr><td>Schema ("schema")</td><td></td><td></td><td>Het schema beschrijft de structuur van de gegevens.  Het bevat het kenmerk (kolom, attribuut, veld, enz.) namen, typen en andere metadata.  Deze informatie is allemaal afgeleid van de gegevensbron.  Schema is een singleton-annotatie - er kan slechts één schema worden toegevoegd voor een asset.</td></tr>
<tr><td></td><td>kolommen</td><td>Column[]</td><td>Een array met kolomobjecten. Ze beschrijven de kolom met informatie afkomstig van de gegevensbron.</td></tr>

<tr><td>ColumnBeschrijving ('kolomBeschrijvingen')</td><td></td><td></td><td>Deze eigenschap bevat een beschrijving voor een kolom.  Elke gebruiker van het systeem kan zijn eigen beschrijvingen toevoegen voor meerdere kolommen (hoogstens één per kolom). Alleen de gebruiker die objecten ColumnDescription heeft gemaakt, kan deze bewerken.  (Beheerders en eigenaren van activa kunnen het object ColumnDescription verwijderen, maar niet bewerken). Het systeem onderhoudt de kolombeschrijvingen van deze gebruikers afzonderlijk.  Er is dus een array van ColumnDescription-objecten op elk element (één per kolom voor elke gebruiker die zijn kennis over de kolom heeft bijgedragen naast eventueel een die informatie bevat die is afgeleid van de gegevensbron).  De ColumnDescription is losjes gebonden aan het schema, zodat deze niet synchroon loopt. In de kolomBeschrijving wordt mogelijk een kolom beschreven die niet meer in het schema bestaat.  Het is aan de schrijver om de beschrijving en het schema synchroon te houden.  De gegevensbron kan ook kolombeschrijvingsinformatie hebben en het zijn extra kolombeschrijvingsobjecten die worden gemaakt tijdens het uitvoeren van het gereedschap.</td></tr>
<tr><td></td><td>columnName</td><td>Tekenreeks</td><td>De naam van de kolom waarnaar deze beschrijving verwijst.</td></tr>
<tr><td></td><td>description</td><td>Tekenreeks</td><td>een korte beschrijving (2-3 lijnen) van de kolom.</td></tr>

<tr><td>Kolomtag ("kolomTags")</td><td></td><td></td><td>Deze eigenschap bevat een tag voor een kolom. Elke gebruiker van het systeem kan meerdere tags toevoegen voor een bepaalde kolom en tags toevoegen voor meerdere kolommen. Alleen de gebruiker die ColumnTag-objecten heeft gemaakt, kan deze bewerken. (Beheerders en eigenaren van activa kunnen het object ColumnTag verwijderen, maar niet bewerken). Het systeem onderhoudt de kolomtags van deze gebruikers afzonderlijk.  Er is dus een array van ColumnTag-objecten op elk item.  De ColumnTag is losjes gebonden aan het schema, zodat het niet synchroon kan lopen. De kolomtag kan een kolom beschrijven die niet meer bestaat in het schema.  Het is aan de schrijver om de kolomtag en het schema synchroon te houden.</td></tr>
<tr><td></td><td>columnName</td><td>Tekenreeks</td><td>De naam van de kolom waarnaar deze tag verwijst.</td></tr>
<tr><td></td><td>tag</td><td>Tekenreeks</td><td>Een tag die de kolom beschrijft.</td></tr>

<tr><td>Deskundige ("deskundigen")</td><td></td><td></td><td>Deze eigenschap bevat een gebruiker die wordt beschouwd als een expert in de gegevensset. De meningen (beschrijvingen) van de experts bellen naar de top van de UX bij het opsommen van beschrijvingen. Elke gebruiker kan zijn eigen experts opgeven. Alleen die gebruiker kan het object experts bewerken. (Beheerders en eigenaren van activa kunnen de expertobjecten verwijderen, maar deze niet bewerken).</td></tr>
<tr><td></td><td>Expert</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Voorbeeld ('voorvertoningen')</td><td></td><td></td><td>De preview bevat een momentopname van de bovenste 20 rijen met gegevens voor het item. Voorbeeld heeft alleen zin voor sommige typen elementen (het is zinvol voor tabel, maar niet voor Meten).</td></tr>
<tr><td></td><td>trailer</td><td>object[]</td><td>Array met objecten die een kolom vertegenwoordigen.  Elk object heeft een eigenschapstoewijzing aan een kolom met een waarde voor die kolom voor de rij.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Mimetype</td><td>tekenreeks</td><td>Het mimetype van de inhoud.</td></tr>
<tr><td></td><td>content</td><td>tekenreeks</td><td>De instructies voor hoe u toegang krijgen tot dit gegevensitem. De inhoud kan een URL, een e-mailadres of een reeks instructies zijn.</td></tr>

<tr><td>TabelDataProfiel ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>getalOfRows</td></td><td>int</td><td>Het aantal rijen in de gegevensset</td></tr>
<tr><td></td><td>grootte</td><td>long</td><td>De grootte in bytes van de gegevensset.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>tekenreeks</td><td>De laatste keer dat het schema is gewijzigd</td></tr>
<tr><td></td><td>gegevensGewijzigdTime</td><td>tekenreeks</td><td>De laatste keer dat de gegevensset is gewijzigd (gegevens zijn toegevoegd, gewijzigd of verwijderd)</td></tr>

<tr><td>ColumnsDataProfile ("kolommenDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>kolommen</td></td><td>ColumnDataProfiel[]</td><td>Een array van kolomgegevensprofielen.</td></tr>

<tr><td>ColumnDataClassificatie ("columnDataClassificaties")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>Tekenreeks</td><td>De naam van de kolom waarnaar deze classificatie verwijst.</td></tr>
<tr><td></td><td>classificatie</td><td>Tekenreeks</td><td>De classificatie van de gegevens in deze kolom.</td></tr>

<tr><td>Documentatie ("documentatie")</td><td></td><td></td><td>Aan een bepaald item kan slechts één documentatie zijn gekoppeld.</td></tr>
<tr><td></td><td>Mimetype</td><td>tekenreeks</td><td>Het mimetype van de inhoud.</td></tr>
<tr><td></td><td>content</td><td>tekenreeks</td><td>De documentatie-inhoud.</td></tr>

</table>

### <a name="common-types"></a>Veelvoorkomende typen
Veelvoorkomende typen kunnen worden gebruikt als de typen voor eigenschappen, maar zijn geen items.

<table>
<tr><td><b>Algemeen type</b></td><td><b>Eigenschappen</b></td><td><b>Gegevenstype</b></td><td><b>Opmerkingen</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Sourcetype</td><td>tekenreeks</td><td>Beschrijft het type gegevensbron.  Bijvoorbeeld: SQL Server, Oracle Database, enz.  </td></tr>
<tr><td></td><td>objectTekst</td><td>tekenreeks</td><td>Beschrijft het type object in de gegevensbron. Bijvoorbeeld: Tabel, Weergave voor SQL Server.</td></tr>

<tr><td>DataSourceLocatie</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Protocol</td><td>tekenreeks</td><td>Vereist. Beschrijft een protocol dat wordt gebruikt om te communiceren met de gegevensbron. Bijvoorbeeld: "tds" voor SQl Server, "oracle" voor Oracle, enz. Raadpleeg <a href="https://docs.microsoft.com/azure/data-catalog/data-catalog-dsr">de referentiespecificatie gegevensbron - DSL-structuur</a> voor de lijst met momenteel ondersteunde protocollen.</td></tr>
<tr><td></td><td>adres</td><td>Woordenboektekenreeks,&lt;object&gt;</td><td>Vereist. Adres is een set gegevens die specifiek zijn voor het protocol dat wordt gebruikt om de gegevensbron te identificeren waarnaar wordt verwezen. De adresgegevens die zijn beperkt tot een bepaald protocol, wat betekent dat het zinloos is zonder het protocol te kennen.</td></tr>
<tr><td></td><td>verificatie</td><td>tekenreeks</td><td>Optioneel. Het verificatieschema dat wordt gebruikt om te communiceren met de gegevensbron. Bijvoorbeeld: ramen, oauth, enz.</td></tr>
<tr><td></td><td>verbindingenEigenschappen</td><td>Woordenboektekenreeks,&lt;object&gt;</td><td>Optioneel. Aanvullende informatie over hoe u verbinding maken met een gegevensbron.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>De backend voert geen validatie uit van verstrekte eigenschappen tegen AAD tijdens het publiceren.</td></tr>
<tr><td></td><td>Upn</td><td>tekenreeks</td><td>Uniek e-mailadres van de gebruiker. Moet worden opgegeven als objectId niet wordt verstrekt of in de context van "lastRegisteredBy" eigenschap, anders optioneel.</td></tr>
<tr><td></td><td>objectId</td><td>GUID</td><td>Aad-identiteit van gebruikers of beveiligingsgroepen. Optioneel. Moet worden opgegeven als upn niet is voorzien, anders optioneel.</td></tr>
<tr><td></td><td>voornaam</td><td>tekenreeks</td><td>Voornaam van de gebruiker (voor weergavedoeleinden). Optioneel. Alleen geldig in de context van "lastRegisteredBy" eigenschap. Kan niet worden opgegeven bij het verstrekken van beveiligingsprincipal voor "rollen", "machtigingen" en "experts".</td></tr>
<tr><td></td><td>achternaam</td><td>tekenreeks</td><td>Achternaam van de gebruiker (voor weergavedoeleinden). Optioneel. Alleen geldig in de context van "lastRegisteredBy" eigenschap. Kan niet worden opgegeven bij het verstrekken van beveiligingsprincipal voor "rollen", "machtigingen" en "experts".</td></tr>

<tr><td>Kolom</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>tekenreeks</td><td>Naam van de kolom of het kenmerk.</td></tr>
<tr><td></td><td>type</td><td>tekenreeks</td><td>gegevenstype van de kolom of het kenmerk. De toegestane typen zijn afhankelijk van gegevensbronType van het actief.  Alleen een subset van typen wordt ondersteund.</td></tr>
<tr><td></td><td>Maxlength</td><td>int</td><td>De maximale lengte die is toegestaan voor de kolom of het kenmerk. Afgeleid van de gegevensbron. Alleen van toepassing op bepaalde brontypen.</td></tr>
<tr><td></td><td>Precisie</td><td>Byte</td><td>De precisie voor de kolom of het kenmerk. Afgeleid van de gegevensbron. Alleen van toepassing op bepaalde brontypen.</td></tr>
<tr><td></td><td>isNiet igable</td><td>Booleaans</td><td>Of de kolom een null-waarde mag hebben of niet. Afgeleid van de gegevensbron. Alleen van toepassing op bepaalde brontypen.</td></tr>
<tr><td></td><td>expressie</td><td>tekenreeks</td><td>Als de waarde een berekende kolom is, bevat dit veld de expressie die de waarde uitdrukt. Afgeleid van de gegevensbron. Alleen van toepassing op bepaalde brontypen.</td></tr>

<tr><td>ColumnDataProfiel</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>tekenreeks</td><td>De naam van de kolom</td></tr>
<tr><td></td><td>type </td><td>tekenreeks</td><td>Het type kolom</td></tr>
<tr><td></td><td>min. </td><td>tekenreeks</td><td>De minimumwaarde in de gegevensset</td></tr>
<tr><td></td><td>Max </td><td>tekenreeks</td><td>De maximale waarde in de gegevensset</td></tr>
<tr><td></td><td>Avg </td><td>double</td><td>De gemiddelde waarde in de gegevensset</td></tr>
<tr><td></td><td>Stdev </td><td>double</td><td>De standaarddeviatie voor de gegevensset</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Het aantal null-waarden in de gegevensset</td></tr>
<tr><td></td><td>distinctCount distinctCount distinctCount distinctCount  </td><td>int</td><td>Het aantal verschillende waarden in de gegevensset</td></tr>


</table>

## <a name="asset-identity"></a>Asset identiteit
Azure Data Catalog gebruikt 'protocol' en identiteitseigenschappen uit de eigenschap 'adres'-eigenschappenvan de eigenschap DataSourceLocation "dsl" om de identiteit van het item te genereren, dat wordt gebruikt om het item in de catalogus aan te pakken.
Het tds-protocol heeft bijvoorbeeld identiteitseigenschappen "server", "database", "schema" en "object". De combinaties van het protocol en de identiteitseigenschappen worden gebruikt om de identiteit van de SQL Server Table Asset te genereren.
Azure Data Catalog biedt verschillende ingebouwde gegevensbronprotocollen, die worden vermeld op [De referentiespecificatie gegevensbron - DSL-structuur](data-catalog-dsr.md).
De set ondersteunde protocollen kan programmatisch worden uitgebreid (Verwijs naar API-referentie voor gegevenscatalogusrest). Beheerders van de catalogus kunnen aangepaste gegevensbronprotocollen registreren. In de volgende tabel worden de eigenschappen beschreven die nodig zijn om een aangepast protocol te registreren.

### <a name="custom-data-source-protocol-specification"></a>Aangepaste gegevensbronprotocolspecificatie
<table>
<tr><td><b>Type</b></td><td><b>Eigenschappen</b></td><td><b>Gegevenstype</b></td><td><b>Opmerkingen</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>naamruimte</td><td>tekenreeks</td><td>De naamruimte van het protocol. De naamruimte moet 1 tot 255 tekens lang zijn, een of meer niet-lege delen bevatten die per punt zijn gescheiden (.). Elk onderdeel moet 1 tot 255 tekens lang zijn, beginnen met een letter en alleen letters en cijfers bevatten.</td></tr>
<tr><td></td><td>name</td><td>tekenreeks</td><td>De naam van het protocol. De naam moet 1 tot 255 tekens lang zijn, beginnen met een letter en alleen letters, cijfers en het streepje (-) teken bevatten.</td></tr>
<tr><td></td><td>identiteitsEigenschappen</td><td>DataSourceProtocolIdentityProperty[]</td><td>Lijst van identiteitseigenschappen moet ten minste één, maar niet meer dan 20 eigenschappen bevatten. Bijvoorbeeld: "server", "database", "schema", "object" zijn identiteitseigenschappen van het "tds"-protocol.</td></tr>
<tr><td></td><td>identiteitssets</td><td>DataSourceProtocolIdentitySet[]</td><td>Lijst met identiteitssets. Hiermee definieert u sets van identiteitseigenschappen die de identiteit van geldige activa vertegenwoordigen. Moet ten minste één, maar niet meer dan 20 sets bevatten. Bijvoorbeeld: {"server", "database", "schema" en "object"} is een identiteitsset voor "tds"-protocol, dat de identiteit van sql servertabel-asset definieert.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>tekenreeks</td><td>De naam van het pand. De naam moet 1 tot 100 tekens lang zijn, beginnen met een letter en alleen letters en cijfers bevatten.</td></tr>
<tr><td></td><td>type</td><td>tekenreeks</td><td>Het type van het pand. Ondersteunde waarden: "bool", booleaan", "byte", "guid", "int", "integer", "long", "string", "url"</td></tr>
<tr><td></td><td>negerenHoofdletter</td><td>Booleaanse waarde</td><td>Geeft aan of de aanvraag moet worden genegeerd bij het gebruik van de waarde van de eigenschap. Kan alleen worden opgegeven voor eigenschappen met "tekenreeks" type. De standaardwaarde is onwaar.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool[]</td><td>Geeft aan of de aanvraag moet worden genegeerd voor elk segment van het pad van de url. Kan alleen worden opgegeven voor eigenschappen met 'url'-type. Standaardwaarde is [false].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>tekenreeks</td><td>De naam van de identiteitsset.</td></tr>
<tr><td></td><td>properties</td><td>tekenreeks[]</td><td>De lijst met identiteitseigenschappen die in deze identiteitsset zijn opgenomen. Het mag geen duplicaten bevatten. Elke eigenschap waarnaar wordt verwezen door de identiteitsset moet worden gedefinieerd in de lijst met 'identiteitseigenschappen' van het protocol.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Rollen en autorisatie
Microsoft Azure Data Catalog biedt autorisatiemogelijkheden voor CRUD-bewerkingen op assets en annotaties.

## <a name="key-concepts"></a>Belangrijkste concepten
De Azure-gegevenscatalogus gebruikt twee autorisatiemechanismen:

* Op rollen gebaseerde autorisatie
* Machtiging op basis van machtigingen

### <a name="roles"></a>Rollen
Er zijn drie rollen: **Beheerder,** **Eigenaar**en **Bijdrager**.  Elke rol heeft zijn werkingssfeer en rechten, die in de volgende tabel worden samengevat.

<table><tr><td><b>Rol</b></td><td><b>Scope</b></td><td><b>Rechten</b></td></tr><tr><td>Beheerder</td><td>Catalogus (alle elementen/annotaties in de catalogus)</td><td>Weergaverollen verwijderen lezen

WeergaveMachtigingen voor zichtbaarheid van eigenaarswijziging wijzigen</td></tr><tr><td>Eigenaar</td><td>Elk element (hoofdobject)</td><td>Weergaverollen verwijderen lezen

WeergaveMachtigingen voor zichtbaarheid van eigenaarswijziging wijzigen</td></tr><tr><td>Inzender</td><td>Elk individueel actief en annotatie</td><td>Lees Weergavefunctie voor verwijderen bijwerken: alle rechten worden ingetrokken als het recht lezen op het item wordt ingetrokken uit de inzender</td></tr></table>

> [!NOTE]
> **Lees**, **Update**, **Verwijderen**, **ViewRoles** rechten zijn van toepassing op een item (asset of annotatie), terwijl **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** zijn alleen van toepassing op het hoofditem.
> 
> **Het** recht verwijderen is van toepassing op een item en eventuele subitems of enkel item eronder. Als u bijvoorbeeld een actief verwijdert, worden ook eventuele annotaties voor dat actief verwijderd.
> 
> 

### <a name="permissions"></a>Machtigingen
Toestemming is als lijst met toegangscontrole-items. Elke toegangscontrole-vermelding kent een reeks rechten toe aan een beveiligingsprincipal. Machtigingen kunnen alleen worden opgegeven op een actief (dat wil zeggen, hoofditem) en zijn van toepassing op het item en eventuele subitems.

Tijdens de **preview-voorvertoning van Azure Data Catalog** wordt alleen **Lezen** rechts ondersteund in de lijst met machtigingen om scenario's in te schakelen om de zichtbaarheid van een asset te beperken.

Standaard heeft elke geverifieerde gebruiker **Leeshet** recht voor elk item in de catalogus, tenzij de zichtbaarheid beperkt is tot de set principals in de machtigingen.

## <a name="rest-api"></a>REST API
**PUT-** en **POST-weergaveitemaanvragen** kunnen worden gebruikt om rollen en machtigingen te beheren: naast itempayload kunnen twee systeemeigenschappen en **machtigingen**worden **opgegeven.**

> [!NOTE]
> **machtigingen** die alleen van toepassing zijn op een hoofditem.
> 
> **De rol** van de eigenaar is alleen van toepassing op een hoofditem.
> 
> Standaard wanneer een item wordt gemaakt in de catalogus is **de bijdrager** ingesteld op de momenteel geverifieerde gebruiker. Als het item door iedereen updatable moet &lt;&gt; zijn, moet **de bijdrager** worden ingesteld op de speciale beveiligingsprincipal van Iedereen in de eigenschap **rollen** wanneer het item voor het eerst wordt gepubliceerd (zie het volgende voorbeeld). **Bijdrager** kan niet worden gewijzigd en blijft hetzelfde tijdens de levensduur van een item (zelfs **beheerder** of **eigenaar** heeft niet het recht om de **bijdrager**te wijzigen). De enige waarde die wordt ondersteund voor &lt;&gt;de expliciete instelling van de **bijdrager** &gt;is Iedereen: **Bijdrager** kan alleen een gebruiker zijn die een item heeft gemaakt of &lt;Iedereen .
> 
> 

### <a name="examples"></a>Voorbeelden
**Stel inopdragers in &lt;op iedereen&gt; wanneer u een item publiceert.**
Speciale security &lt;&gt; principal Iedereen heeft objectId "0000000-0000-0000-0000-0000000000000000201".
  **POST** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Sommige HTTP-clientimplementaties kunnen automatisch aanvragen opnieuw uitgeven in reactie op een 302 van de server, maar strippen doorgaans autorisatiekoppen van de aanvraag. Aangezien de machtigingskop vereist is om aanvragen in te dienen voor Azure Data Catalog, moet u ervoor zorgen dat de autorisatiekop nog steeds wordt verstrekt wanneer u een aanvraag naar een omleidingslocatie stuurt die is opgegeven door Azure Data Catalog. De volgende voorbeeldcode toont deze aan met het object .NET HttpWebRequest.
> 
> 

**Hoofdtekst**
```json
    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }
```

  **Eigenaren toewijzen en de zichtbaarheid van een bestaand hoofditem beperken:** **PUT** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

```json
    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }
```

> [!NOTE]
> In PUT is het niet nodig om een item payload in de body op te geven: PUT kan worden gebruikt om alleen rollen en/of machtigingen bij te werken.
> 
