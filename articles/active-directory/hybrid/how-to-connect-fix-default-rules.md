---
title: Gewijzigde standaardregels oplossen - Azure AD Connect | Microsoft Documenten
description: Meer informatie over het oplossen van gewijzigde standaardregels die worden geleverd met Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4626e0149028a140d143fb8d0969a03b732201fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036984"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Gewijzigde standaardregels oplossen in Azure AD Connect

Azure Active Directory (Azure AD) Connect gebruikt standaardregels voor synchronisatie.  Helaas zijn deze regels niet universeel van toepassing op alle organisaties. Op basis van uw vereisten moet u deze mogelijk wijzigen. In dit artikel worden twee voorbeelden van de meest voorkomende aanpassingen besproken en wordt de juiste manier uitgelegd om deze aanpassingen te bereiken.

>[!NOTE] 
> Het wijzigen van bestaande standaardregels om een benodigde aanpassing te bereiken, wordt niet ondersteund. Als u dit doet, wordt voorkomen dat deze regels worden bijgewerkt naar de nieuwste versie in toekomstige versies. Je krijgt niet de bugfixes die je nodig hebt, of nieuwe functies. In dit document wordt uitgelegd hoe u hetzelfde resultaat bereiken zonder de bestaande standaardregels te wijzigen. 

## <a name="how-to-identify-modified-default-rules"></a>Gewijzigde standaardregels identificeren
Vanaf versie 1.3.7.0 van Azure AD Connect is het eenvoudig om de gewijzigde standaardregel te identificeren. Ga naar **Apps op desktop**en selecteer Editor voor **synchronisatieregels**.

![Azure AD Connect, met synchronisatieregelseditor gemarkeerd](media/how-to-connect-fix-default-rules/default1.png)

In de editor worden gewijzigde standaardregels weergegeven met een waarschuwingspictogram voor de naam.

![Waarschuwingspictogram](media/how-to-connect-fix-default-rules/default2.png)

 Er wordt ook een uitgeschakelde regel met dezelfde naam ernaast weergegeven (dit is de standaardstandaardregel).

![Synchronisatieregelseditor, met standaardstandaardregel en gewijzigde standaardregel](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Algemene aanpassingen
De volgende zijn algemene aanpassingen aan de standaardregels:

- Kenmerkstroom wijzigen
- Scoping-filter wijzigen
- De voorwaarde Join wijzigen

Voordat u regels wijzigt:

- Schakel de synchronisatieplanner uit. De planner wordt standaard elke 30 minuten uitgevoerd. Zorg ervoor dat het niet begint terwijl u wijzigingen aanbrengt en uw nieuwe regels oplost. Als u de planner tijdelijk wilt uitschakelen, `Set-ADSyncScheduler -SyncCycleEnabled $false`start u PowerShell en voert u .
 ![PowerShell-opdrachten om de synchronisatieplanner uit te schakelen](media/how-to-connect-fix-default-rules/default3.png)

- De wijziging in het scopingfilter kan leiden tot verwijdering van objecten in de doelmap. Wees voorzichtig voordat u wijzigingen aanbrengt in het scoping van objecten. We raden u aan wijzigingen aan te brengen in een stagingserver voordat u wijzigingen aanbrengt op de actieve server.
- Voer een voorbeeld uit op één object, zoals vermeld in de sectie [Synchronisatieregel valideren,](#validate-sync-rule) nadat u een nieuwe regel hebt toegevoegd.
- Voer een volledige synchronisatie uit nadat u een nieuwe regel hebt toegevoegd of een aangepaste synchronisatieregel hebt gewijzigd. Deze synchronisatie past nieuwe regels toe op alle objecten.

## <a name="change-attribute-flow"></a>Kenmerkstroom wijzigen
Er zijn drie verschillende scenario's voor het wijzigen van de kenmerkstroom:
- Een nieuw kenmerk toevoegen.
- De waarde van een bestaand kenmerk overschrijven.
- Kies ervoor om een bestaand kenmerk niet te synchroniseren.

U deze doen zonder de standaardstandaardregels te wijzigen.

### <a name="add-a-new-attribute"></a>Een nieuw kenmerk toevoegen
Als u merkt dat een kenmerk niet van uw bronmap naar de doelmap stroomt, gebruikt u de [azure AD Connect-synchronisatie: directory-extensies](how-to-connect-sync-feature-directory-extensions.md) om dit op te lossen.

Als de extensies niet voor u werken, u twee nieuwe synchronisatieregels toevoegen die in de volgende secties worden beschreven.


#### <a name="add-an-inbound-sync-rule"></a>Een binnenkomende synchronisatieregel toevoegen
Een inkomende synchronisatieregel betekent dat de bron voor het kenmerk een verbindingsruimte is en het doel de metaverse. Als u bijvoorbeeld een nieuwe kenmerkstroom wilt hebben van on-premises Active Directory naar Azure Active Directory, maakt u een nieuwe inkomende synchronisatieregel. Start de **editor synchronisatieregels,** selecteer **Binnenkomen als** richting en selecteer Nieuwe **regel toevoegen**. 

 ![Editor voor synchronisatieregels](media/how-to-connect-fix-default-rules/default3a.png)

Volg uw eigen naamgevingsconventie om de regel een naam te geven. Hier gebruiken we **Custom In van AD - Gebruiker**. Dit betekent dat de regel een aangepaste regel is en een binnenkomende regel is van de Active Directory-connectorruimte naar het metaverse.   

 ![Inkomende synchronisatieregel maken](media/how-to-connect-fix-default-rules/default3b.png)

Geef uw eigen beschrijving van de regel, zodat toekomstig onderhoud van de regel eenvoudig is. De beschrijving kan bijvoorbeeld worden gebaseerd op wat de doelstelling van de regel is en waarom deze nodig is.

Maak uw selecties voor de velden **Verbonden systeem,** **Verbonden systeemobjecttype**en **Metaverse Object Type.**

Geef de voorrangswaarde op van 0 tot en met 99 (hoe lager het getal, hoe hoger de prioriteit). Gebruik de standaardselecties voor de velden **Tag**, **Wachtwoordsynchronisatie**en **Uitgeschakeld.**

Houd **het Scoping-filter** leeg. Dit betekent dat de regel van toepassing is op alle objecten die zijn verbonden tussen het Active Directory Connected System en het metaverse.

Houd **Join-regels** leeg. Dit betekent dat deze regel de joinvoorwaarde gebruikt die is gedefinieerd in de standaardstandaardregel. Dit is een andere reden om de standaardstandaardregel niet uit te schakelen of te verwijderen. Als er geen joinvoorwaarde is, wordt het kenmerk niet doorstromen. 

Voeg de juiste transformaties toe voor uw kenmerk. U een constante toewijzen om een constante waardestroom aan uw doelkenmerk te maken. U directe toewijzing tussen het bron- of doelkenmerk gebruiken. U ook een expressie voor het kenmerk gebruiken. Hier zijn verschillende [expressiefuncties](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) die u gebruiken.

#### <a name="add-an-outbound-sync-rule"></a>Een uitgaande synchronisatieregel toevoegen
Als u het kenmerk wilt koppelen aan de doelmap, moet u een uitgaande regel maken. Dit betekent dat de bron de metaverse is, en het doel is het verbonden systeem. Als u een uitgaande regel wilt maken, start u de **editor synchronisatieregels,** wijzigt u de **richting** in **Uitgaand**en selecteert u **Nieuwe regel toevoegen**. 

![Editor voor synchronisatieregels](media/how-to-connect-fix-default-rules/default3c.png)

Net als bij de inkomende regel u uw eigen naamgevingsconventie gebruiken om de regel een naam te geven. Selecteer het **verbonden systeem** als de Azure AD-tenant en selecteer het verbonden systeemobject waarop u de kenmerkwaarde wilt instellen. Stel de voorrang in van 0 tot en met 99. 

![Uitgaande synchronisatieregel maken](media/how-to-connect-fix-default-rules/default3d.png)

Houd **scoping-filter-** en **joinregels** leeg. Vul de transformatie in als constant, direct of expressie. 

U weet nu hoe u een nieuw kenmerk voor een gebruikersobject maken van Active Directory naar Azure Active Directory. U deze stappen gebruiken om elk kenmerk van elk object naar bron en doel te toewijzen. Zie [Aangepaste synchronisatieregels maken](how-to-connect-create-custom-sync-rule.md) en [Voorbereiden op gebruikers voor](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization)meer informatie.

### <a name="override-the-value-of-an-existing-attribute"></a>De waarde van een bestaand kenmerk overschrijven
U de waarde van een kenmerk dat al is toegewezen, overschrijven. Als u bijvoorbeeld altijd een null-waarde wilt instellen op een kenmerk in Azure AD, maakt u alleen een inkomende regel. Maak de constante `AuthoritativeNull`waarde, doorstroom naar het doelkenmerk. 

>[!NOTE] 
> Gebruik `AuthoritativeNull` in `Null` plaats van in dit geval. Dit komt omdat de niet-null-waarde de null-waarde vervangt, zelfs als deze een lagere prioriteit heeft (een hogere getalwaarde in de regel). `AuthoritativeNull`, aan de andere kant, wordt niet vervangen door een niet-null waarde door andere regels. 

### <a name="dont-sync-existing-attribute"></a>Bestaande kenmerken niet synchroniseren
Als u een kenmerk wilt uitsluiten van synchronisatie, gebruikt u de kenmerkfilterfunctie die wordt geleverd in Azure AD Connect. Start **Azure AD Connect** vanaf het bureaubladpictogram en selecteer **Synchronisatieopties aanpassen**.

![Extra takenopties voor Azure AD Connect](media/how-to-connect-fix-default-rules/default4.png)

 Controleer of **Azure AD-app- en kenmerkfiltering** is geselecteerd en selecteer **Volgende**.

![Optionele functies azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

De kenmerken wissen die u wilt uitsluiten van synchronisatie.

![Azure AD Connect-kenmerken](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Scoping-filter wijzigen
Azure AD Sync verzorgt de meeste objecten. U het bereik van objecten verkleinen en het aantal objecten dat moet worden geëxporteerd verminderen, zonder de standaardstandaardsynchronisatieregels te wijzigen. 

Gebruik een van de volgende methoden om het bereik van de objecten die u synchroniseert te verkleinen:

- cloudFiltered-kenmerk
- Organisatie-eenheid filteren

Als u het bereik van de gebruikers die worden gesynchroniseerd, vermindert, stopt de wachtwoordhashsynchronisatie ook voor de gefilterde gebruikers. Als de objecten al worden gesynchroniseerd, worden de gefilterde objecten uit de doelmap verwijderd nadat u het bereik hebt verminderd. Zorg er daarom voor dat je heel zorgvuldig scopet.

>[!IMPORTANT] 
> Het wordt afgeraden om het bereik van objecten die zijn geconfigureerd door Azure AD Connect te vergroten. Hierdoor is het moeilijk voor het Microsoft-ondersteuningsteam om de aanpassingen te begrijpen. Als u het bereik van objecten moet vergroten, bewerkt u de bestaande regel, kloont u deze en schakelt u de oorspronkelijke regel uit. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered-kenmerk
U dit kenmerk niet instellen in Active Directory. Stel de waarde van dit kenmerk in door een nieuwe inkomende regel toe te voegen. U vervolgens **Transformatie** en **Expressie** gebruiken om dit kenmerk in de metaverse in te stellen. In het volgende voorbeeld wordt weergegeven dat u niet alle gebruikers wilt synchroniseren waarvan de afdelingsnaam begint met **HRD** (case-ongevoelig):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

We hebben de afdeling eerst geconverteerd van bron (Active Directory) naar kleine letters. Vervolgens, met `Left` behulp van de functie, namen we `hrd`alleen de eerste drie tekens en vergeleken het met . Als deze overeenkomen is, wordt `True`de `NULL`waarde ingesteld op , anders . Bij het instellen van de waarde op null, kan een andere regel met lagere prioriteit (een hogere getalwaarde) deze met een andere voorwaarde schrijven. Voer een voorbeeld uit op één object om de synchronisatieregel te valideren, zoals vermeld in de sectie [Synchronisatieregel valideren.](#validate-sync-rule)

![Inkomende synchronisatieregelopties maken](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Organisatorische eenheid filteren
U een of meer organisatie-eenheden (OE's) maken en de objecten verplaatsen die u niet wilt synchroniseren met deze ok's. Configureer vervolgens de O-filtering in Azure AD Connect. Start **Azure AD Connect** vanaf het bureaubladpictogram en selecteer de volgende opties. U de O-installatie ook configureren op het moment van installatie van Azure AD Connect. 

![Extra taken van Azure AD Connect](media/how-to-connect-fix-default-rules/default8.png)

Volg de wizard en schakel de ok's uit die u niet wilt synchroniseren.

![Filteropties voor Azure AD Connect-domein en -gegevens](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>De voorwaarde Join wijzigen
Gebruik de standaardverbindingsvoorwaarden die zijn geconfigureerd door Azure AD Connect. Als u standaardvoorwaarden voor join wijzigt, is het moeilijk voor Microsoft-ondersteuning om de aanpassingen te begrijpen en het product te ondersteunen.

## <a name="validate-sync-rule"></a>Synchronisatieregel valideren
U de nieuw toegevoegde synchronisatieregel valideren met behulp van de voorbeeldfunctie, zonder de volledige synchronisatiecyclus uit te voeren. Selecteer in Azure AD Connect de optie **Synchronisatieservice**.

![Azure AD Connect, met synchronisatieservice gemarkeerd](media/how-to-connect-fix-default-rules/default10.png)

Selecteer **Metaverse Zoeken**. Selecteer het bereikobject als **persoon,** selecteer **Clausule toevoegen**en vermeld uw zoekcriteria. Selecteer vervolgens **Zoeken**en dubbelklik op het object in de zoekresultaten. Zorg ervoor dat uw gegevens in Azure AD Connect up-to-date zijn voor dat object, door import en synchronisatie uit te voeren in het forest voordat u deze stap uitvoert.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

Selecteer **Metaverse Object Properties** **connectors,** selecteer het object in de bijbehorende connector (forest) en selecteer **Eigenschappen...**.

![Eigenschappen van metaverse objecten](media/how-to-connect-fix-default-rules/default12.png)

Selecteer **Voorbeeld...**

![Eigenschappen van objectconnectorruimte](media/how-to-connect-fix-default-rules/default13a.png)

Selecteer in het venster Voorbeeld de optie **Voorbeeld genereren** en **Kenmerkstroom importeren** in het linkerdeelvenster.

![Preview](media/how-to-connect-fix-default-rules/default14.png)
 
Hier wordt opgemerkt dat de nieuw toegevoegde regel op `cloudFiltered` het object wordt uitgevoerd en het kenmerk op true heeft ingesteld.

![Preview](media/how-to-connect-fix-default-rules/default15a.png)
 
Als u de gewijzigde regel wilt vergelijken met de standaardregel, exporteert u beide regels afzonderlijk, als tekstbestanden. Deze regels worden geëxporteerd als een PowerShell-scriptbestand. U ze vergelijken met behulp van een bestand vergelijking tool (bijvoorbeeld windiff) om de wijzigingen te zien. 
 
Merk op dat in `msExchMailboxGuid` de gewijzigde regel het kenmerk wordt gewijzigd in het **type Expressie** in plaats van **Direct**. Ook wordt de waarde gewijzigd in **null en** **ExecuteOnce** optie. U de verschillen tussen geïdentificeerde en voorrang negeren. 

![windiff-gereedschapuitvoer](media/how-to-connect-fix-default-rules/default17.png)
 
Als u uw regels wilt herstellen om ze weer in standaardinstellingen te wijzigen, verwijdert u de gewijzigde regel en schakelt u de standaardregel in. Zorg ervoor dat u de aanpassing die u probeert te bereiken niet verliest. Wanneer u klaar bent, voert u **Volledige synchronisatie**uit.

## <a name="next-steps"></a>Volgende stappen
- [Hardware en vereisten](how-to-connect-install-prerequisites.md) 
- [Snelle instellingen](how-to-connect-install-express.md)
- [Aangepaste instellingen](how-to-connect-install-custom.md)



