---
title: Problemen oplossen met een object dat niet wordt gesynchroniseerd met Azure Active Directory | Microsoft Docs'
description: Problemen oplossen met een object dat niet synchroniseert met Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 931865803328189d89c0fbae15caa801c3f7f7c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253531"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Problemen oplossen met een object dat niet synchroniseert met Azure Active Directory

Als een object niet synchroniseert zoals verwacht met Microsoft Azure Active Directory (Azure AD), kan dit om verschillende redenen zijn. Als u een foute-mail van Azure AD hebt ontvangen of als u de fout ziet in Azure AD Connect-status, leest u in plaats daarvan [fouten oplossen tijdens synchronisatie.](tshoot-connect-sync-errors.md) Maar als u een probleem oplost waarbij het object zich niet in Azure AD bevindt, is dit artikel iets voor u. Hierin wordt beschreven hoe u fouten vinden in de on-premises synchronisatie van Azure AD Connect.

>[!IMPORTANT]
>Gebruik voor Azure AD Connect-implementatie met versie 1.1.749.0 of hoger de [taak voor het oplossen van problemen](tshoot-connect-objectsync.md) met het oplossen van objecten in de wizard om problemen met het synchroniseren van objecten op te lossen. 

## <a name="synchronization-process"></a>Synchronisatieproces

Voordat we synchronisatieproblemen onderzoeken, laten we het synchronisatieproces van Azure AD Connect begrijpen:

  ![Diagram van Azure AD Connect-synchronisatieproces](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **CS:** Verbindingsruimte, een tabel in een database
* **MV:** Metaverse, een tabel in een database

### <a name="synchronization-steps"></a>**Synchronisatiestappen**
Het synchronisatieproces omvat de volgende stappen:

1. **Importeren uit AD:** Active Directory-objecten worden in active directory CS gebracht.

2. **Importeren uit Azure AD:** Azure AD-objecten worden naar de Azure AD CS gebracht.

3. **Synchronisatie:** Binnenkomende synchronisatieregels en uitgaande synchronisatieregels worden uitgevoerd in de volgorde van het voorrangsgetal, van lager naar hoger. Als u de synchronisatieregels wilt weergeven, gaat u naar de editor voor synchronisatieregels vanuit de bureaubladtoepassingen. De binnenkomende synchronisatieregels brengen gegevens van CS naar MV. De uitgaande synchronisatieregels verplaatsen gegevens van MV naar CS.

4. **Exporteren naar AD:** Na synchronisatie worden objecten geëxporteerd van active directory CS naar Active Directory.

5. **Exporteren naar Azure AD:** Na synchronisatie worden objecten geëxporteerd van het Azure AD CS naar Azure AD.

## <a name="troubleshooting"></a>Problemen oplossen

Als u de fouten wilt vinden, bekijkt u een paar verschillende plaatsen in de volgende volgorde:

1. De [bewerking wordt logboeken](#operations) gevonden om fouten te vinden die zijn geïdentificeerd door de synchronisatie-engine tijdens het importeren en synchroniseren.
2. De [verbindingsruimte](#connector-space-object-properties) voor het vinden van ontbrekende objecten en synchronisatiefouten.
3. De [metaverse](#metaverse-object-properties) om gegevensgerelateerde problemen te vinden.

Start [Synchronisatieservicebeheer](how-to-connect-sync-service-manager-ui.md) voordat u met deze stappen begint.

## <a name="operations"></a>Bewerkingen
Op het tabblad **Bewerkingen** in Synchronisatieservicebeheer moet u beginnen met het oplossen van problemen. Op dit tabblad worden de resultaten van de meest recente bewerkingen weergegeven. 

![Schermafbeelding van Synchronisatieservicebeheer, waarop het tabblad Bewerkingen is geselecteerd](./media/tshoot-connect-object-not-syncing/operations.png)  

De bovenste helft van het tabblad **Bewerkingen** toont alle runs in chronologische volgorde. Standaard houdt het logboek van bewerkingen informatie over de afgelopen zeven dagen bij, maar deze instelling kan worden gewijzigd met de [planner](how-to-connect-sync-feature-scheduler.md). Kijk voor een run die niet een **succes** status tonen. U de sortering wijzigen door op de kopteksten te klikken.

De kolom **Status** bevat de belangrijkste informatie en toont het ernstigste probleem voor een run. Hier vindt u een korte samenvatting van de meest voorkomende statussen in volgorde van prioriteit voor onderzoek (waarbij * verschillende mogelijke fouttekenreeksen aangeeft).

| Status | Opmerking |
| --- | --- |
| gestopt-* |De run kon niet afmaken. Dit kan bijvoorbeeld gebeuren als het externe systeem uitvalt en niet kan worden gecontacteerd. |
| stop-fout-limiet |Er zijn meer dan 5.000 fouten. De run werd automatisch gestopt vanwege het grote aantal fouten. |
| voltooide-\*-fouten |De run is afgelopen, maar er zijn fouten (minder dan 5.000) die moeten worden onderzocht. |
| voltooid-\*-waarschuwingen |De run is voltooid, maar sommige gegevens zijn niet in de verwachte status. Als u fouten hebt, is dit bericht meestal slechts een symptoom. Onderzoek geen waarschuwingen totdat u fouten hebt verholpen. |
| voltooid |Geen problemen. |

Wanneer u een rij selecteert, wordt de onderkant van het tabblad **Bewerkingen** bijgewerkt om de details van die run weer te geven. Aan de uiterst linkse kant van dit gebied hebt u mogelijk een lijst met de titel **Stap #**. Deze lijst wordt alleen weergegeven als u meerdere domeinen in uw forest hebt en elk domein wordt vertegenwoordigd door een stap. De domeinnaam is te vinden onder het kopje **Partitie**. Onder de kop **Synchronisatiestatistieken** vindt u meer informatie over het aantal wijzigingen dat is verwerkt. Selecteer de koppelingen om een lijst met de gewijzigde objecten te krijgen. Als u objecten met fouten hebt, worden deze fouten weergegeven onder de kop **Synchronisatiefouten.**

### <a name="errors-on-the-operations-tab"></a>Fouten op het tabblad Bewerkingen
Wanneer u fouten hebt, toont Synchronisatieservicebeheer zowel het object als de fout zelf als koppelingen die meer informatie bieden.

![Schermafbeelding van fouten in Synchronisatieservicebeheer](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Begin met het selecteren van de fouttekenreeks. (In de vorige afbeelding wordt de fouttekenreeks **synchronisatieregel-fout-functie geactiveerd**.) U krijgt eerst een overzicht van het object te zien. Als u de werkelijke fout wilt zien, selecteert u **Stapeltracering**. Deze tracering biedt informatie op foutopsporingsniveau voor de fout.

Klik met de rechtermuisknop op het vak **Informatie van de oproepstapel,** klik op **Alles selecteren**en selecteer **Kopiëren**. Kopieer vervolgens de stapel en kijk naar de fout in uw favoriete editor, zoals Kladblok.

Als de fout afkomstig is van **SyncRulesEngine,** worden in de callstack-informatie eerst alle kenmerken van het object weergegeven. Scroll naar beneden totdat u de kop **InnerException =>** ziet.  

  ![Schermafbeelding van synchronisatieservicebeheer, waarin foutgegevens worden weergegeven onder het kopje InnerException =>](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
De regel na de kop toont de fout. In de voorgaande figuur is de fout afkomstig van een aangepaste synchronisatieregel die Fabrikam heeft gemaakt.

Als de fout niet genoeg informatie geeft, is het tijd om te kijken naar de gegevens zelf. Selecteer de koppeling met de object-id en blijf het [geïmporteerde object van](#cs-import)de connectorruimte oplossen .

## <a name="connector-space-object-properties"></a>Eigenschappen van het object van het connectorgebied
Als het tabblad [**Bewerkingen**](#operations) geen fouten weergeeft, volgt u het verbindingsruimteobject van Active Directory naar de metaverse naar Azure AD. Op dit pad moet u vinden waar het probleem zich bevindt.

### <a name="searching-for-an-object-in-the-cs"></a>Zoeken naar een object in de CS

Selecteer in Synchronisatieservicebeheer **connectors,** selecteer de Active Directory Connector en selecteer **Zoekconnectorruimte**.

Selecteer **RDN** in **RDN** het vak Bereik wanneer u wilt zoeken op het kenmerk CN of selecteer **DN of anker** wanneer u wilt zoeken op het kenmerk **DistinguishedName.** Voer een waarde in en selecteer **Zoeken**. 
 
![Schermafbeelding van een zoekopdracht in de connectorruimte](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Als u het object dat u zoekt niet vindt, is het mogelijk gefilterd met [filtering op basis van domeinen](how-to-connect-sync-configure-filtering.md#domain-based-filtering) of op een [ou-gebaseerde filtering](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Als u wilt controleren of de filtering is geconfigureerd zoals verwacht, leest u [Azure AD Connect-synchronisatie: Filtering configureren](how-to-connect-sync-configure-filtering.md).

U een andere nuttige zoekopdracht uitvoeren door de Azure AD Connector te selecteren. Schakel **in** het vak Bereik de optie **In behandeling bij importeren**in en schakel het selectievakje Toevoegen **in.** Met deze zoekopdracht u alle gesynchroniseerde objecten in Azure AD gebruiken die niet kunnen worden gekoppeld aan een on-premises object.  

![Schermafbeelding van wezen in een zoekopdracht naar de connectorruimte](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Deze objecten zijn gemaakt door een andere synchronisatie-engine of een synchronisatie-engine met een andere filterconfiguratie. Deze weesobjecten worden niet meer beheerd. Bekijk deze lijst en overweeg deze objecten te verwijderen met behulp van de Azure AD PowerShell-cmdlets. [Azure AD PowerShell](https://aka.ms/aadposh)

### <a name="cs-import"></a>CS importeren
Wanneer u een CS-object opent, staan er verschillende tabbladen bovenaan. Op het tabblad **Importeren** worden de gegevens weergegeven die na een import zijn uitgevoerd.  

![Schermafbeelding van het venster Eigenschappen van objectverbindingsruimte connector, waarbij het tabblad Importeren is geselecteerd](./media/tshoot-connect-object-not-syncing/csobject.png)    

In de kolom **Oude waarde** ziet u wat momenteel is opgeslagen in Verbinding en in de kolom Nieuwe **waarde** wordt weergegeven wat er van het bronsysteem is ontvangen en nog niet is toegepast. Als er een fout op het object optreedt, worden wijzigingen niet verwerkt.

Het tabblad **Synchronisatiefout** is alleen zichtbaar in het venster **Eigenschappen van objectverbindingsruimte** als er een probleem is met het object. Bekijk op [het tabblad **Bewerkingen** ](#errors-on-the-operations-tab)hoe u synchronisatiefouten oplossen voor meer informatie.

![Schermafbeelding van het tabblad Synchronisatiefout in het venster Eigenschappen van objectverbindingsruimte connectorruimte](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS-regel
Op het tabblad **Regellijn** in het venster **Eigenschappen van object Verbindingsruimte** ziet u hoe het object voor de connectorruimte is gerelateerd aan het metaverseobject. U zien wanneer de connector voor het laatst een wijziging van het verbonden systeem heeft geïmporteerd en welke regels van toepassing zijn op het invullen van gegevens in de metaverse.  

![Schermafbeelding van het tabblad Regelrij in het venster Eigenschappen van objectverbindingsruimte connector](./media/tshoot-connect-object-not-syncing/cslineage.png)  

In de voorgaande afbeelding wordt in de kolom **Actie** een binnenkomende synchronisatieregel weergegeven met de **actievoorziening**. Dit geeft aan dat zolang dit verbindingsruimteobject aanwezig is, het metaverse object blijft. Als in de lijst met synchronisatieregels in plaats daarvan een uitgaande synchronisatieregel met een **actie Voorziening** wordt weergegeven, wordt dit object verwijderd wanneer het doelmetkeerobject wordt verwijderd.  

![Schermafbeelding van een regelvenster op het tabblad Regelrij in het venster Eigenschappen van objectverbindingsruimte connector](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

In de voorgaande afbeelding u in de kolom **PasswordSync** ook zien dat de binnenkomende connectorruimte wijzigingen in het wachtwoord kan bijdragen, omdat één synchronisatieregel de waarde **True**heeft. Dit wachtwoord wordt via de uitgaande regel naar Azure AD verzonden.

Op het tabblad **Regelrij** u bij de metaverse komen door [**metaverse objecteigenschappen te**](#mv-attributes)selecteren.

### <a name="preview"></a>Preview
In de linkerbenedenhoek van het venster **Eigenschappen van het verbindingsruimteobject** bevindt zich de knop **Voorbeeld.** Selecteer deze knop om de **voorbeeldpagina** te openen, waar u één object synchroniseren. Deze pagina is handig als u een aantal aangepaste synchronisatieregels oplost en het effect van een wijziging op één object wilt zien. U een **volledige synchronisatie** of een **Delta-synchronisatie**selecteren. U ook **Voorbeeld genereren**selecteren, waardoor de wijziging alleen in het geheugen wordt bijhouden. Of selecteer **Commit Preview**, die de metaverse bijwerkt en alle wijzigingen in doelconnectorruimten opvoert.  

![Schermafbeelding van de voorbeeldpagina, waarbij StartPreview is geselecteerd](./media/tshoot-connect-object-not-syncing/preview.png)  

In het voorbeeld u het object inspecteren en zien welke regel voor een bepaalde kenmerkstroom is toegepast.  

![Schermafbeelding van de voorbeeldpagina met invoerkenmerkstroom](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Logboek
Selecteer naast de knop **Voorbeeld** de knop **Logboek en** open de **pagina Logboek.** Hier ziet u de status en geschiedenis van wachtwoordsynchronisatie. Zie [Problemen met wachtwoordhashsynchronisatie oplossen met Azure AD Connect-synchronisatie](tshoot-connect-password-hash-synchronization.md)voor meer informatie.

## <a name="metaverse-object-properties"></a>Metaverse objecteigenschappen
Het is meestal beter om te beginnen met zoeken vanuit de bron Active Directory-connectorruimte. Maar je ook beginnen met zoeken vanaf de metaverse.

### <a name="searching-for-an-object-in-the-mv"></a>Zoeken naar een object op de MV
Selecteer **metaverse zoeken**in Synchronisatieservicebeheer, zoals in de volgende afbeelding. Maak een query waarvan u weet dat deze de gebruiker vindt. Zoeken naar algemene kenmerken, zoals **accountName** (**sAMAccountName**) en **userPrincipalName**. Zie [Metaverse zoeken voor](how-to-connect-sync-service-manager-ui-mvsearch.md)Meer informatie.

![Schermafbeelding van Synchronisatieservicebeheer, waarbij het tabblad Metaverse zoeken is geselecteerd](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Klik **in** het venster Zoekresultaten op het object.

Als u het object niet hebt gevonden, heeft het object nog niet bereikt. Ga verder met zoeken naar het object in de [Active Directory-connectorruimte](#connector-space-object-properties). Als u het object in de Active Directory-connectorruimte vindt, kan er een synchronisatiefout zijn waardoor het object niet naar de metaverse kan komen of dat er mogelijk een synchronisatieregelscopingfilter wordt toegepast.

### <a name="object-not-found-in-the-mv"></a>Object dat niet op de MV is gevonden
Als het object zich in de Active Directory CS bevindt, maar niet aanwezig is op de MV, wordt een scopingfilter toegepast. Als u het scopingfilter wilt bekijken, gaat u naar het menu van de bureaubladtoepassing en selecteert u **De editor voor synchronisatieregels**. Filter de regels die van toepassing zijn op het object door het onderstaande filter aan te passen.

  ![Schermafbeelding van de editor voor synchronisatieregels, met een zoeken naar binnenkomende synchronisatieregels](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Bekijk elke regel in de lijst van bovenaf en controleer het **filter Scoping**. In het volgende scopingfilter is de waarde **van isCriticalSystemObject** null of FALSE of leeg.

  ![Schermafbeelding van een scopingfilter in een binnenkomend zoeken naar synchronisatieregels](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Ga naar de kenmerklijst [CS importeren](#cs-import) en controleer welk filter het object blokkeert om naar de MV te gaan. In de kenmerklijst **Connectorruimte** worden alleen niet-null- en niet-lege kenmerken weergegeven. Als **isCriticalSystemObject** bijvoorbeeld niet in de lijst wordt weergegeven, is de waarde van dit kenmerk ongeldig of leeg.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Object dat niet is gevonden in de Azure AD CS
Als het object niet aanwezig is in de connectorruimte van Azure AD, maar wel aanwezig is op de MV, kijkt u naar het scopingfilter van de uitgaande regels van de bijbehorende connectorruimte en zoekt u uit of het object is uitgefilterd omdat de [MV-kenmerken](#mv-attributes) niet aan de criteria voldoen.

Als u het uitgaande scopingfilter wilt bekijken, selecteert u de toepasselijke regels voor het object door het onderstaande filter aan te passen. Bekijk elke regel en kijk naar de bijbehorende [MV-attribuutwaarde.](#mv-attributes)

  ![Schermafbeelding van een zoekopdracht voor uitgaande synchronisatieregels in de editor synchronisatieregels](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV-kenmerken
Op het tabblad **Kenmerken** ziet u de waarden en welke connectoren deze hebben bijgedragen.  

![Schermafbeelding van het venster Eigenschappen van metaverse objecten, waarbij het tabblad Kenmerken is geselecteerd](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Als een object niet synchroniseert, stelt u de volgende vragen over kenmerkstatussen in de metaverse:
- Is het kenmerk **cloudFiltered** aanwezig en ingesteld op **True?** Als dit het is, is het gefilterd op basis van de stappen in [filtering op basis van kenmerken.](how-to-connect-sync-configure-filtering.md#attribute-based-filtering)
- Is het kenmerk **sourceAnchor** aanwezig? Zo niet, heeft u een account-resource forest topologie? Als een object wordt geïdentificeerd als een gekoppeld postvak (het kenmerk **msExchRecipientTypeDetails** heeft de waarde **2),** wordt het **sourceAnchor** door het forest bijgedragen met een ingeschakeld Active Directory-account. Controleer of het hoofdaccount correct is geïmporteerd en gesynchroniseerd. Het hoofdaccount moet worden weergegeven tussen de [connectors](#mv-connectors) voor het object.

### <a name="mv-connectors"></a>MV-connectoren
Op het tabblad **Connectors** worden alle verbindingsruimten weergegeven die een weergave van het object hebben. 
 
![Schermafbeelding van het venster Eigenschappen van metaverse objecten, waarbij het tabblad Connectors is geselecteerd](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Je moet een connector hebben om:

- Elk Active Directory-forest waarin de gebruiker is vertegenwoordigd. Deze vertegenwoordiging kan **foreignSecurityPrincipals** en **Contact** objecten omvatten.
- Een connector in Azure AD.

Als u de connector voor Azure AD mist, controleert u de sectie over [MV-kenmerken](#mv-attributes) om de criteria voor de inrichting van Azure AD te verifiëren.

Via het tabblad **Connectors** u ook naar het [object connectorruimte](#connector-space-object-properties)gaan. Selecteer een rij en klik op **Eigenschappen**.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure AD Connect-synchronisatie](how-to-connect-sync-whatis.md).
- Meer informatie over [hybride identiteit](whatis-hybrid-identity.md).
