---
title: 'Synchronisatie van Azure AD Connect: een configuratiewijziging aanbrengen in Azure AD Connect-synchronisatie'
description: Hiermee u de configuratie wijzigen in azure AD Connect-synchronisatie.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d77882817934d5ad98f16965aeb9dc246931c495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261162"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Synchronisatie van Azure AD Connect: een wijziging aanbrengen in de standaardconfiguratie
Het doel van dit artikel is om u te laten doorlopen hoe u wijzigingen aanbrengen in de standaardconfiguratie in Azure Active Directory (Azure AD) Connect-synchronisatie. Het biedt stappen voor een aantal veelvoorkomende scenario's. Met deze kennis moet u in staat zijn om eenvoudige wijzigingen aan te brengen in uw eigen configuratie op basis van uw eigen bedrijfsregels.

> [!WARNING]
> Als u wijzigingen aanbrengt in de standaard regels voor niet-boxsynchronisatie, worden deze wijzigingen overschreven wanneer Azure AD Connect de volgende keer wordt bijgewerkt, wat resulteert in onverwachte en waarschijnlijk ongewenste synchronisatieresultaten.
>
> De standaard out-of-box synchronisatieregels hebben een duimafdruk. Als u deze regels wijzigt, komt de duimafdruk niet meer overeen. U in de toekomst problemen ondervinden wanneer u een nieuwe versie van Azure AD Connect probeert toe te passen. Breng alleen wijzigingen aan zoals het in dit artikel wordt beschreven.

## <a name="synchronization-rules-editor"></a>Editor voor synchronisatieregels
De editor voor synchronisatieregels wordt gebruikt om de standaardconfiguratie te zien en te wijzigen. U vindt het in het menu **Start** onder de azure **AD Connect-groep.**  
![Menu Start met synchronisatieregeleditor](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Wanneer u de editor opent, ziet u de standaard regels voor out-of-box.

![Editor voor synchronisatieregels](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigeren in de editor
Met behulp van de vervolgkeuzelijst boven aan de editor u snel een specifieke regel vinden. Als u bijvoorbeeld de regels wilt zien waarin de kenmerkproxyAdressen is opgenomen, u de vervolgkeuzelijst als volgt wijzigen:  
![SRE-filtering](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Als u het filteren opnieuw wilt instellen en een nieuwe configuratie wilt laden, drukt u op F5 op het toetsenbord.

Rechtsboven bevindt zich de knop **Nieuwe regel toevoegen.** U gebruikt deze knop om uw eigen aangepaste regel te maken.

Aan de onderkant staan knoppen om te reageren op een geselecteerde synchronisatieregel. **Bewerken** en **verwijderen** doen wat je verwacht. **Export** produceert een PowerShell-script voor het opnieuw maken van de synchronisatieregel. Met deze procedure u een synchronisatieregel van de ene server naar de andere verplaatsen.

## <a name="create-your-first-custom-rule"></a>Uw eerste aangepaste regel maken
De meest voorkomende wijzigingen zijn de kenmerkstromen. De gegevens in uw bronmap zijn mogelijk niet hetzelfde als in Azure AD. Zorg er in het voorbeeld in deze sectie voor dat de voornaam van een gebruiker altijd in *het juiste geval*is.

### <a name="disable-the-scheduler"></a>De planner uitschakelen
De [planner](how-to-connect-sync-feature-scheduler.md) wordt standaard elke 30 minuten uitgevoerd. Zorg ervoor dat het niet wordt gestart terwijl u wijzigingen aanbrengt en uw nieuwe regels oplost. Als u de planner tijdelijk wilt uitschakelen, start u PowerShell en voert u . `Set-ADSyncScheduler -SyncCycleEnabled $false`

![De planner uitschakelen](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>De regel maken
1. Klik **op Nieuwe regel toevoegen**.
2. Voer **op de pagina Beschrijving** het volgende in:  
   ![Inkomende regelfiltering](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Naam:** Geef de regel een beschrijvende naam.
   * **Beschrijving**: Geef wat verduidelijking zodat iemand anders kan begrijpen waar de regel voor is.
   * **Connected System**: Dit is het systeem waarin het object te vinden is. Selecteer in dit geval **Active Directory Connector**.
   * **Verbonden systeem/metaverse objecttype:** selecteer respectievelijk **gebruiker** en **persoon.**
   * **Koppelingstype:** wijzig deze waarde in **Join**.
   * **Voorrang**: geef een waarde op die uniek is in het systeem. Een lagere numerieke waarde geeft een hogere prioriteit aan.
   * **Tag:** Laat dit leeg. Alleen kant-en-klare regels van Microsoft moeten dit vak vullen met een waarde.
3. Voer op de filterpagina **Scoping** **givenName ISNOTNULL in**.  
   ![Inkomende regelscopingfilter](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Deze sectie wordt gebruikt om te bepalen op welke objecten de regel moet worden toegepast. Als deze leeg blijft, is de regel van toepassing op alle gebruikersobjecten. Dat zou echter ook vergaderruimtes, serviceaccounts en andere niet-mensen gebruikersobjecten omvatten.
4. Laat het veld op de pagina **Regels deelnemen** leeg.
5. Wijzig Op de pagina **Transformaties** **FlowType** in **Expressie**. Selecteer **givenName**voor **Doelkenmerk**. En voer voor **Bron** **PCase([givenName]) in.**
   ![Inkomende regeltransformaties](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   De synchronisatie-engine is hoofdlettergevoelig voor zowel de functienaam als de naam van het kenmerk. Als u iets verkeerd typt, ziet u een waarschuwing wanneer u de regel toevoegt. U opslaan en doorgaan, maar u moet de regel opnieuw openen en corrigeren.
6. Klik **op Toevoegen** om de regel op te slaan.

Uw nieuwe aangepaste regel moet zichtbaar zijn met de andere synchronisatieregels in het systeem.

### <a name="verify-the-change"></a>De wijziging verifiëren
Met deze nieuwe wijziging, wilt u ervoor zorgen dat het werkt zoals verwacht en is geen fouten te gooien. Afhankelijk van het aantal objecten dat u hebt, zijn er twee manieren om deze stap te doen:

- Voer een volledige synchronisatie uit op alle objecten.
- Voer een voorbeeld en volledige synchronisatie uit op één object.

Open de **synchronisatieservice** in het menu **Start.** De stappen in deze sectie zijn allemaal in deze tool.

**Volledige synchronisatie op alle objecten**  

   1. Selecteer **Connectors** bovenaan. Identificeer de connector die u in de vorige sectie hebt gewijzigd (in dit geval Active Directory Domain Services) en selecteer deze. 
   2. Selecteer **Uitvoeren** **voor acties**.
   3. Selecteer **Volledige synchronisatie**en selecteer **OK**.
   ![Volledige synchronisatie](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   De objecten worden nu bijgewerkt in de metaverse. Controleer uw wijzigingen door naar het object in de metaverse te kijken.

**Voorbeeld en volledige synchronisatie op één object bekijken**  

   1. Selecteer **Connectors** bovenaan. Identificeer de connector die u in de vorige sectie hebt gewijzigd (in dit geval Active Directory Domain Services) en selecteer deze.
   2. Selecteer **zoekconnectorruimte**. 
   3. Gebruik **Scope** om een object te vinden dat u wilt gebruiken om de wijziging te testen. Selecteer het object en klik op **Voorbeeld**. 
   4. Selecteer **Commit Preview**in het nieuwe scherm .  
   ![Preview van commit](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   De verandering is nu toegewijd aan de metaverse.

**Het object in de metaverse weergeven**  

1. Kies een paar voorbeeldobjecten om ervoor te zorgen dat de waarde wordt verwacht en dat de regel wordt toegepast. 
2. Selecteer **Metaverse Zoeken** bovenaan. Voeg een filter toe dat u nodig hebt om de relevante objecten te vinden. 
3. Open in het zoekresultaat een object. Bekijk de kenmerkwaarden en controleer ook in de kolom **Regels synchroniseren** of de regel is toegepast zoals verwacht.  
![Metaverse zoeken](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>De planner inschakelen
Als alles is zoals verwacht, u de planner opnieuw inschakelen. Voer vanaf PowerShell uit. `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="other-common-attribute-flow-changes"></a>Andere veelvoorkomende wijzigingen in de kenmerkstroom
In de vorige sectie werd beschreven hoe u wijzigingen aanbrengt in een kenmerkstroom. In deze sectie worden enkele aanvullende voorbeelden gegeven. De stappen voor het maken van de synchronisatieregel worden afgekort, maar u de volledige stappen in de vorige sectie vinden.

### <a name="use-an-attribute-other-than-the-default"></a>Een ander kenmerk dan de standaardinstelling gebruiken
In dit Fabrikam-scenario is er een bos waar het lokale alfabet wordt gebruikt voor voornaam, achternaam en weergavenaam. De Latijnse karakterweergave van deze kenmerken is te vinden in de extensiekenmerken. Voor het maken van een algemene adreslijst in Azure AD en Office 365 wil de organisatie deze kenmerken in plaats daarvan gebruiken.

Bij een standaardconfiguratie ziet een object uit het lokale forest er als volgt uit:  
![Kenmerkstroom 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Ga als volgt te werk om een regel met andere kenmerkstromen te maken:

1. Open de **editor synchronisatieregels** in het menu **Start.**
2. Als **Binnenkomend** nog steeds aan de linkerkant is geselecteerd, klikt u op de knop **Nieuwe regel toevoegen.**
3. Geef de regel een naam en beschrijving. Selecteer de on-premises Active Directory-instantie en de relevante objecttypen. Selecteer **Join**in **Koppelingstype**. Kies **bij Voorrang**een getal dat niet door een andere regel wordt gebruikt. De out-of-box regels beginnen met 100, dus de waarde 50 kan worden gebruikt in dit voorbeeld.
  ![Kenmerkstroom 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Laat **het Scoping-filter** leeg. (Dat wil zeggen, het moet van toepassing zijn op alle gebruikersobjecten in het forest.)
5. Laat **Join regels** leeg. (Dat wil zeggen, laat de out-of-box regel omgaan met eventuele joins.)
6. Maak **in Transformaties**de volgende stromen:  
  ![Kenmerkstroom 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Klik **op Toevoegen** om de regel op te slaan.
8. Ga naar **Synchronisatieservicebeheer**. Selecteer **in Connectors**de connector waar u de regel hebt toegevoegd. Selecteer **Uitvoeren**en selecteer Vervolgens **Volledige synchronisatie**. Een volledige synchronisatie berekent alle objecten opnieuw met behulp van de huidige regels.

Dit is het resultaat voor hetzelfde object met deze aangepaste regel:  
![Kenmerkstroom 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Lengte van de kenmerken
Tekenreekskenmerken zijn standaard indexeerbaar en de maximale lengte is 448 tekens. Als u werkt met tekenreekskenmerken die mogelijk meer bevatten, moet u het volgende opnemen in de kenmerkstroom:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Het hoofdvoorvoegsel van de gebruiker wijzigen
Het kenmerk userPrincipalName in Active Directory is niet altijd bekend bij de gebruikers en is mogelijk niet geschikt als aanmeldings-id. Met de wizard Synchronisatieinstallatie van Azure AD Connect u een ander kenmerk kiezen, bijvoorbeeld *e-mail*. Maar in sommige gevallen moet het attribuut worden berekend.

Het bedrijf Contoso heeft bijvoorbeeld twee Azure AD-mappen, een voor productie en een voor het testen. Ze willen dat de gebruikers in hun testtenant een ander achtervoegsel in de aanmeldings-id gebruiken:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

Neem in deze expressie alles @-sign wat overblijft van het eerste (Woord) en concaten met een vaste tekenreeks.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Een kenmerk met meerdere waarden converteren naar één waarde
Sommige kenmerken in Active Directory worden in het schema met meerdere waarden gewaardeerd, ook al zien ze er met één waarde uit in Active Directory- gebruikers en computers. Een voorbeeld is het kenmerk beschrijving:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Als het kenmerk in deze expressie een waarde heeft, neemt u het eerste item (*Item*) in het kenmerk, verwijdert u voor- en naloopruimten *(Bijsnijden)* en houdt u de eerste 448 tekens *(links)* in de tekenreeks.

### <a name="do-not-flow-an-attribute"></a>Een kenmerk niet doorstromen
Zie [Het kenmerkstroomproces beheren](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process)voor achtergrondinformatie over het scenario voor deze sectie .

Er zijn twee manieren om een attribuut niet te laten stromen. De eerste is door de installatiewizard te gebruiken om geselecteerde kenmerken te [verwijderen.](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering) Deze optie werkt als u het kenmerk nog nooit eerder hebt gesynchroniseerd. Als u dit kenmerk echter hebt gesynchroniseerd en later met deze functie verwijdert, stopt de synchronisatieengine met het beheren van het kenmerk en blijven de bestaande waarden achter in Azure AD.

Als u de waarde van een kenmerk wilt verwijderen en ervoor wilt zorgen dat het in de toekomst niet meer wordt doorgestroomd, moet u een aangepaste regel maken.

In dit Fabrikam-scenario hebben we ons gerealiseerd dat sommige van de kenmerken die we synchroniseren met de cloud er niet mogen zijn. We willen ervoor zorgen dat deze kenmerken worden verwijderd uit Azure AD.  
![Slechte extensiekenmerken](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Maak een nieuwe binnenkomende synchronisatieregel en vul de beschrijving in.
  ![Beschrijvingen](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Kenmerkstromen maken met **Expressie** voor **FlowType** en met **AuthoritativeNull** voor **Bron**. De letterlijke **AuthoritativeNull** geeft aan dat de waarde leeg moet zijn in het metaverse, zelfs als een synchronisatieregel met lagere prioriteit de waarde probeert in te vullen.
  ![Transformatie voor extensiekenmerken](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Sla de synchronisatieregel op. Start de **synchronisatieservice,** zoek de connector, selecteer **Uitvoeren**en selecteer **Vervolgens Volledige synchronisatie**. Met deze stap worden alle kenmerkstromen opnieuw berekend.
4. Controleer of de beoogde wijzigingen op het punt staan te worden geëxporteerd door in de connectorruimte te zoeken.
  ![Gefaseerd verwijderen](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Regels maken met PowerShell
Het gebruik van de synchronisatieregeleditor werkt prima als u slechts een paar wijzigingen moet aanbrengen. Als u veel wijzigingen moet aanbrengen, is PowerShell mogelijk een betere optie. Sommige geavanceerde functies zijn alleen beschikbaar met PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Download het PowerShell-script voor een out-of-box-regel
Als u het PowerShell-script wilt zien waarin een regel die niet in eigen opgeert is gemaakt, selecteert u de regel in de editor voor synchronisatieregels en klikt u op **Exporteren**. Met deze actie krijgt u het PowerShell-script waarmee de regel is gemaakt.

### <a name="advanced-precedence"></a>Geavanceerde voorrang
De out-of-box synchronisatieregels beginnen met een voorrangswaarde van 100. Als u veel forests hebt en u veel aangepaste wijzigingen moet aanbrengen, zijn 99 synchronisatieregels mogelijk niet voldoende.

U de synchronisatieengine instrueren dat u extra regels wilt invoegen vóór de out-of-box regels. Voer de volgende stappen uit om dit gedrag te volgen:

1. Markeer de eerste out-of-box synchronisatieregel (**In van AD-User Join**) in de synchronisatieregelseditor en selecteer **Exporteren**. Kopieer de waarde van de SR-id.  
![PowerShell voor wijziging](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Maak de nieuwe synchronisatieregel. U de editor voor synchronisatieregels gebruiken om deze te maken. Exporteer de regel naar een PowerShell-script.
3. Plaats in de **eigenschap VoorrangVór**de id-waarde in de regel out-of-box. De **voorrang instellen** op **0**. Zorg ervoor dat het kenmerk Identifier uniek is en dat u een GUID van een andere regel niet hergebruikt. Zorg er ook voor dat de eigenschap **ImmutableTag** niet is ingesteld. Deze eigenschap mag alleen worden ingesteld voor een out-of-box regel.
4. Sla het PowerShell-script op en voer het uit. Het resultaat is dat aan uw aangepaste regel de voorrangswaarde van 100 is toegewezen en dat alle andere regels buiten het vak worden verhoogd.  
![PowerShell na wijziging](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

U veel aangepaste synchronisatieregels hebben met dezelfde **prioriteitVoordat** u de waarde gebruikt wanneer dat nodig is.

## <a name="enable-synchronization-of-usertype"></a>Synchronisatie van UserType inschakelen
Azure AD Connect ondersteunt synchronisatie van het kenmerk **UserType** voor **gebruikersobjecten** in versie 1.1.524.0 en hoger. Meer in het bijzonder zijn de volgende wijzigingen aangebracht:

- Het schema van het objecttype **Gebruiker** in de Azure AD Connector wordt uitgebreid met het kenmerk UserType, dat van de tekenreeks is en met één waarde is.
- Het schema van het objecttype **Persoon** in de metaverse wordt uitgebreid met het kenmerk UserType, dat van de tekenreeks is en met één waarde is.

Het kenmerk UserType is standaard niet ingeschakeld voor synchronisatie omdat er geen corresponderend kenmerk van UserType is in de on-premises Active Directory. U moet synchronisatie handmatig inschakelen. Voordat u dit doet, moet u rekening houden met het volgende gedrag dat wordt afgedwongen door Azure AD:

- Azure AD accepteert slechts twee waarden voor het kenmerk UserType: **Lid** en **Gast**.
- Als het kenmerk UserType niet is ingeschakeld voor synchronisatie in Azure AD Connect, hebben Azure AD-gebruikers die via adreslijstsynchronisatie zijn gemaakt, het kenmerk UserType ingesteld op **Lid.**
- Azure AD staat niet toe dat het kenmerk UserType van bestaande Azure AD-gebruikers wordt gewijzigd door Azure AD Connect. Het kan alleen worden ingesteld tijdens het maken van de Azure AD-gebruikers en [gewijzigd via Powershell](/powershell/module/azuread/set-azureaduser?view=azureadps-2.0).

Voordat u synchronisatie van het kenmerk UserType inschakelt, moet u eerst bepalen hoe het kenmerk is afgeleid van on-premises Active Directory. De volgende benaderingen zijn de meest voorkomende benaderingen:

- Wijs een ongebruikt on-premises AD-kenmerk aan (zoals extensieAttribuut1) dat moet worden gebruikt als bronkenmerk. Het aangewezen ad-kenmerk op locatie moet van de **tekenreeks**type zijn, een waarde met één waarde hebben en de waarde **Lid** of **Gast**bevatten . 

    Als u deze benadering kiest, moet u ervoor zorgen dat het aangewezen kenmerk wordt gevuld met de juiste waarde voor alle bestaande gebruikersobjecten in on-premises Active Directory die zijn gesynchroniseerd met Azure AD voordat synchronisatie van het kenmerk UserType wordt ingeschakeld .

- U de waarde voor het kenmerk UserType ook afleiden uit andere eigenschappen. U wilt bijvoorbeeld alle gebruikers synchroniseren als **gast** als hun kenmerk AD userPrincipalName eindigt met domeinonderdeel. <em>@partners.fabrikam123.org</em> 

    Zoals eerder vermeld, staat Azure AD Connect niet toe dat het kenmerk UserType op bestaande Azure AD-gebruikers wordt gewijzigd door Azure AD Connect. Daarom moet u ervoor zorgen dat de logica die u hebt gekozen, consistent is met de manier waarop het kenmerk UserType al is geconfigureerd voor alle bestaande Azure AD-gebruikers in uw tenant.

De stappen om synchronisatie van het kenmerk UserType in te schakelen, kunnen worden samengevat als:

1.  Schakel de synchronisatieplanner uit en controleer of er geen synchronisatie aan de gang is.
2.  Voeg het bronkenmerk toe aan het on-premises AD Connector-schema.
3.  Voeg het UserType toe aan het Azure AD Connector-schema.
4.  Maak een binnenkomende synchronisatieregel om de kenmerkwaarde uit on-premises Active Directory te laten stromen.
5.  Maak een uitgaande synchronisatieregel om de kenmerkwaarde door te geven aan Azure AD.
6.  Voer een volledige synchronisatiecyclus uit.
7.  Schakel de synchronisatieplanner in.

>[!NOTE]
> De rest van deze sectie behandelt deze stappen. Ze worden beschreven in de context van een Azure AD-implementatie met topologie met één forest en zonder aangepaste synchronisatieregels. Als u multiforesttopologie, aangepaste synchronisatieregels hebt geconfigureerd of een staging-server hebt, moet u de stappen dienovereenkomstig aanpassen.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Stap 1: Schakel de synchronisatieplanner uit en controleer of er geen synchronisatie wordt uitgevoerd
Om te voorkomen dat onbedoelde wijzigingen naar Azure AD worden geëxporteerd, moet u ervoor zorgen dat er geen synchronisatie plaatsvindt terwijl u bezig bent met het bijwerken van synchronisatieregels. Ga als bedoeld als u de ingebouwde synchronisatieplanner uitschakelt:

 1. Start een PowerShell-sessie op de Azure AD Connect-server.
 2. Schakel geplande synchronisatie uit door de `Set-ADSyncScheduler -SyncCycleEnabled $false`cmdlet uit te voeren.
 3. Open de synchronisatieservicebeheer door naar**Synchronisatieservice** **starten** > .
 4. Ga naar het tabblad **Bewerkingen** en controleer of er geen bewerking is met een status van *uitvoering*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Stap 2: Het bronkenmerk toevoegen aan het on-premises AD-connectorschema
Niet alle Azure AD-kenmerken worden geïmporteerd in de on-premises AD-connectorruimte. Ga als u het bronkenmerk toevoegt aan de lijst met geïmporteerde kenmerken:

 1. Ga naar het tabblad **Connectors** in de synchronisatieservicebeheer.
 2. Klik met de rechtermuisknop op de on-premises AD-connector en selecteer **Eigenschappen**.
 3. Ga in het pop-updialoogvenster naar het tabblad **Kenmerken selecteren.**
 4. Controleer of het bronkenmerk is ingeschakeld in de lijst met kenmerken.
 5. Klik op **OK** om op te slaan.
![Bronkenmerk toevoegen aan on-premises AD-connectorschema](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Stap 3: Het UserType toevoegen aan het Azure AD Connector-schema
Het kenmerk UserType wordt standaard niet geïmporteerd in de Azure AD Connect Space. Ga als u het kenmerk UserType toevoegt aan de lijst met geïmporteerde kenmerken:

 1. Ga naar het tabblad **Connectors** in de synchronisatieservicebeheer.
 2. Klik met de rechtermuisknop op de **Azure AD Connector** en selecteer **Eigenschappen**.
 3. Ga in het pop-updialoogvenster naar het tabblad **Kenmerken selecteren.**
 4. Controleer of het kenmerk UserType is ingeschakeld in de kenmerklijst.
 5. Klik op **OK** om op te slaan.

![Bronkenmerk toevoegen aan Azure AD Connector-schema](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Stap 4: Een binnenkomende synchronisatieregel maken om de kenmerkwaarde uit on-premises Active Directory te laten stromen
Met de inkomende synchronisatieregel kan de kenmerkwaarde van het bronkenmerk van de on-premises Active Directory naar de metaverse stromen:

1. Open de editor synchronisatieregels door naar**de editor synchronisatieregels** **starten** > .
2. Stel het zoekfilter **Richting** in als **binnenkomend**.
3. Klik **op** de knop Nieuwe regel toevoegen om een nieuwe inkomende regel te maken.
4. Geef onder het tabblad **Beschrijving** de volgende configuratie op:

    | Kenmerk | Waarde | Details |
    | --- | --- | --- |
    | Name | *Een naam opgeven* | Bijvoorbeeld, *In van AD – UserType* |
    | Beschrijving | *Een beschrijving geven* |  |
    | Verbonden systeem | *De on-premises AD-connector kiezen* |  |
    | Verbonden systeemobjecttype | **Gebruiker** |  |
    | Metaverse objecttype | **Person** |  |
    | Koppelingstype | **Toevoegen** |  |
    | Prioriteit | *Kies een getal tussen 1-99* | 1–99 is gereserveerd voor aangepaste synchronisatieregels. Kies geen waarde die wordt gebruikt door een andere synchronisatieregel. |

5. Ga naar het **filtertabblad Scoping** en voeg **één scopingfiltergroep** toe met de volgende component:

    | Kenmerk | Operator | Waarde |
    | --- | --- | --- |
    | Admindescription | NOTSTARTWITH | Gebruiker\_ |

    Het scopingfilter bepaalt op welke on-premises AD-objecten deze binnenkomende synchronisatieregel wordt toegepast. In dit voorbeeld gebruiken we hetzelfde scopingfilter dat wordt gebruikt in de algemene synchronisatieregel *Van AD : gebruiker,* waardoor de synchronisatieregel niet kan worden toegepast op gebruikersobjecten die zijn gemaakt via de schrijffunctie voor Azure AD-gebruiker. Mogelijk moet u het scopingfilter aanpassen op basis van uw Azure AD Connect-implementatie.

6. Ga naar het tabblad **Transformatie** en implementeer de gewenste transformatieregel. Als u bijvoorbeeld een ongebruikt on-premises AD-kenmerk (zoals extensieAttribute1) hebt aangewezen als bronkenmerk voor het UserType, u een directe kenmerkstroom implementeren:

    | Stroomtype | Kenmerk Target | Bron | Eén keer solliciteren | Tekst samenvoegen |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | extensieAttribuut1 | Niet ingeschakeld | Update |

    In een ander voorbeeld wilt u de waarde voor het kenmerk UserType afleiden van andere eigenschappen. U wilt bijvoorbeeld alle gebruikers synchroniseren als gast als hun kenmerk AD userPrincipalName eindigt met domeinonderdeel. <em>@partners.fabrikam123.org</em> U een expressie als volgt implementeren:

    | Stroomtype | Kenmerk Target | Bron | Eén keer solliciteren | Tekst samenvoegen |
    | --- | --- | --- | --- | --- |
    | Expressie | UserType | IIF(IsPresent([userPrincipalName]), IIF(CBool(InStr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0),"Member","Guest"),Error("UserPrincipalName is niet aanwezig om UserType te bepalen")) | Niet ingeschakeld | Update |

7. Klik **op Toevoegen** om de binnenkomende regel te maken.

![Inkomende synchronisatieregel maken](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Stap 5: Een uitgaande synchronisatieregel maken om de kenmerkwaarde door te geven aan Azure AD
Met de uitgaande synchronisatieregel kan de kenmerkwaarde van het metaverse naar het kenmerk UserType in Azure AD stromen:

1. Ga naar de editor voor synchronisatieregels.
2. Stel het zoekfilter **Richting** in als **Uitgaand**.
3. Klik op de knop **Nieuwe regel toevoegen.**
4. Geef onder het tabblad **Beschrijving** de volgende configuratie op:

    | Kenmerk | Waarde | Details |
    | ----- | ------ | --- |
    | Name | *Een naam opgeven* | Bijvoorbeeld *Uit naar AAD – UserType* |
    | Beschrijving | *Een beschrijving geven* ||
    | Verbonden systeem | *De AAD-connector selecteren* ||
    | Verbonden systeemobjecttype | **Gebruiker** ||
    | Metaverse objecttype | **Person** ||
    | Koppelingstype | **Toevoegen** ||
    | Prioriteit | *Kies een getal tussen 1-99* | 1–99 is gereserveerd voor aangepaste synchronisatieregels. Kies geen waarde die wordt gebruikt door een andere synchronisatieregel. |

5. Ga naar het **filtertabblad Scoping** en voeg **één scopingfiltergroep** toe met twee clausules:

    | Kenmerk | Operator | Waarde |
    | --- | --- | --- |
    | sourceObjectType | Gelijke | Gebruiker |
    | cloudMastered | NOTEQUAL NOTEQUAL | True |

    Het scopingfilter bepaalt op welke Azure AD-objecten deze uitgaande synchronisatieregel wordt toegepast. In dit voorbeeld gebruiken we hetzelfde scopingfilter van de *out- naar AD - Gebruikersidentiteit* out-of-box synchronisatieregel. Hiermee wordt voorkomen dat de synchronisatieregel wordt toegepast op gebruikersobjecten die niet zijn gesynchroniseerd vanuit on-premises Active Directory. Mogelijk moet u het scopingfilter aanpassen op basis van uw Azure AD Connect-implementatie.

6. Ga naar het tabblad **Transformatie** en implementeer de volgende transformatieregel:

    | Stroomtype | Kenmerk Target | Bron | Eén keer solliciteren | Tekst samenvoegen |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | UserType | Niet ingeschakeld | Update |

7. Klik **op Toevoegen** om de uitgaande regel te maken.

![Uitgaande synchronisatieregel maken](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Stap 6: Een volledige synchronisatiecyclus uitvoeren
Over het algemeen is een volledige synchronisatiecyclus vereist omdat we nieuwe kenmerken hebben toegevoegd aan zowel de Active Directory- als azure AD Connector-schema's en aangepaste synchronisatieregels hebben geïntroduceerd. U wilt de wijzigingen verifiëren voordat u ze exporteert naar Azure AD. 

U de volgende stappen gebruiken om de wijzigingen te verifiëren terwijl u handmatig de stappen uitvoert die deel uitmaken van een volledige synchronisatiecyclus.

1. Voer een **volledige import** uit op de **on-premises AD-connector:**

   1. Ga naar het tabblad **Bewerkingen** in de synchronisatieservicemanager.
   2. Klik met de rechtermuisknop op de **on-premises AD-connector** en selecteer **Uitvoeren**.
   3. Selecteer in het pop-updialoogvenster **Volledige invoer** en klik op **OK**.
   4. Wacht tot de bewerking is voltooid.

      > [!NOTE]
      > U een volledige import op de on-premises AD Connector overslaan als het bronkenmerk al is opgenomen in de lijst met geïmporteerde kenmerken. Met andere woorden, u hoefde geen wijzigingen aan te brengen tijdens [stap 2: Voeg het bronkenmerk toe aan het on-premises AD Connector-schema](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Voer een **volledige import uit** op de Azure AD **Connector:**

   1. Klik met de rechtermuisknop op de **Azure AD Connector** en selecteer **Uitvoeren**.
   2. Selecteer in het pop-updialoogvenster **Volledige invoer** en klik op **OK**.
   3. Wacht tot de bewerking is voltooid.

3. Controleer de wijzigingen van de synchronisatieregel op een bestaand object gebruiker:

    Het bronkenmerk van on-premises Active Directory en het UserType van Azure AD zijn geïmporteerd in hun respectievelijke connectorruimten. Voordat u verdergaat met een volledige synchronisatie, moet u een **voorbeeld doen** voor een bestaand object van gebruiker in de on-premises AD-connectorruimte. Het gekozen object moet het bronkenmerk hebben ingevuld.
    
    Een **geslaagdvoorbeeld** met het UserType dat in de metaverse wordt ingevuld, is een goede indicator dat u de synchronisatieregels correct hebt geconfigureerd. Raadpleeg de sectie [De wijziging verifiëren](#verify-the-change)voor informatie over het doen van een **voorbeeld**.

4. Een **volledige synchronisatie uitvoeren** op de **on-premises AD-connector:**

   1. Klik met de rechtermuisknop op de **on-premises AD-connector** en selecteer **Uitvoeren**.
   2. Selecteer in het pop-updialoogvenster **Volledige synchronisatie** en klik op **OK**.
   3. Wacht tot de bewerking is voltooid.

5. Controleer **in behandeling zijnde uitvoer** naar Azure AD:

   1. Klik met de rechtermuisknop op de **Azure AD Connector** en selecteer **Zoekconnectorruimte**.
   2. Ga als een van de **dialoogvensters Zoekconnectorruimte:**

      - **Bereik** instellen **op In behandeling zijnde export**.
      - Schakel alle drie de selectievakjes **in: Toevoegen,** **Wijzigen**en **verwijderen**.
      - Klik **op de** knop Zoeken om de lijst met objecten te krijgen waarop wijzigingen moeten worden geëxporteerd. Dubbelklik op het object om de wijzigingen voor een bepaald object te onderzoeken.
      - Controleer of de wijzigingen worden verwacht.

6. Uitvoeren **op** de **Azure AD-connector**uitvoeren:

   1. Klik met de rechtermuisknop op de **Azure AD Connector** en selecteer **Uitvoeren**.
   2. Selecteer in het pop-updialoogvenster **Connector uitvoeren** de optie **Exporteren** en klik op **OK**.
   3. Wacht tot de export naar Azure AD is voltooid.

> [!NOTE]
> Deze stappen bevatten niet de volledige synchronisatie- en exportstappen op de Azure AD Connector. Deze stappen zijn niet vereist omdat de kenmerkwaarden alleen van on-premises Active Directory naar Azure AD stromen.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Stap 7: De synchronisatieplanner opnieuw inschakelen
Schakel de ingebouwde synchronisatieplanner opnieuw in:

1. Start een PowerShell-sessie.
2. Geplande synchronisatie opnieuw inschakelen door de cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true`uit te voeren.


## <a name="next-steps"></a>Volgende stappen
* Lees meer over het configuratiemodel in [Declaratieve provisioning begrijpen.](concept-azure-ad-connect-sync-declarative-provisioning.md)
* Lees meer over de expressietaal in [Het begrijpen van declaratieve inrichtingsuitdrukkingen](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Overzichtsonderwerpen**

* [Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
