---
title: 'Azure AD Connect-synchronisatie: inzicht in de standaardconfiguratie | Microsoft Documenten'
description: In dit artikel wordt de standaardconfiguratie beschreven in azure AD Connect-synchronisatie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2886b842aab81732beec0fdd7957aab8e2b4f5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548863"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure AD Connect-synchronisatie: inzicht in de standaardconfiguratie
In dit artikel worden de kant-en-klare configuratieregels uitgelegd. Het documenteert de regels en hoe deze regels van invloed zijn op de configuratie. Het leidt u ook door de standaardconfiguratie van Azure AD Connect-synchronisatie. Het doel is dat de lezer begrijpt hoe het configuratiemodel, genaamd declaratieve inrichting, werkt in een real-world voorbeeld. In dit artikel wordt ervan uitgegaan dat u azure AD Connect-synchronisatie al hebt geïnstalleerd en configureren met behulp van de wizard Installatie.

Als u de details van het configuratiemodel wilt begrijpen, leest u [Declaratieve provisioning begrijpen](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Kant-en-klare regels van on-premises naar Azure AD
De volgende expressies zijn te vinden in de out-of-box configuratie.

### <a name="user-out-of-box-rules"></a>Out-of-box regels voor gebruikers
Deze regels zijn ook van toepassing op het objecttype iNetOrgPerson.

Een gebruikersobject moet voldoen aan het volgende dat moet worden gesynchroniseerd:

* Moet een sourceAnchor hebben.
* Nadat het object is gemaakt in Azure AD, kan sourceAnchor niet wijzigen. Als de waarde on-premises wordt gewijzigd, stopt het object met synchroniseren totdat de bronAnchor is teruggezet naar de vorige waarde.
* Het kenmerk accountEnabled (userAccountControl) moet zijn ingevuld. Bij een on-premises Active Directory is dit kenmerk altijd aanwezig en gevuld.

De volgende gebruikersobjecten worden **niet** gesynchroniseerd met Azure AD:

* `IsPresent([isCriticalSystemObject])`. Zorg ervoor dat veel out-of-box objecten in Active Directory, zoals het ingebouwde beheerdersaccount, niet worden gesynchroniseerd.
* `IsPresent([sAMAccountName]) = False`. Zorg ervoor dat gebruikersobjecten zonder sAMAccountName-kenmerk niet worden gesynchroniseerd. Deze zaak zou alleen praktisch gebeuren in een domein opgewaardeerd van NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Synchroniseer het serviceaccount dat wordt gebruikt door Azure AD Connect-synchronisatie en de eerdere versies.
* Geen Exchange-accounts synchroniseren die niet zouden werken in Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Synchroniseer geen objecten die niet zouden werken in Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Met dit bitmask (&H21C07000) worden de volgende objecten gefilterd:
  * Openbare map met e-mail (in voorbeeld vanaf versie 1.1.524.0)
  * Postvak systeemattendant
  * Postvak postvak (postvak van het postvak)
  * Universal Security Group (zou niet van toepassing zijn voor een gebruiker, maar is aanwezig om verouderde redenen)
  * Niet-universele groep (zou niet van toepassing zijn voor een gebruiker, maar is aanwezig om verouderde redenen)
  * Postvakplan
  * Detectiepostvak
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Geen replicatieslachtofferobjecten synchroniseren.

De volgende attribuutregels zijn van toepassing:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Het kenmerk sourceAnchor wordt niet bijgedragen vanuit een gekoppeld postvak. Er wordt van uitgegaan dat als een gekoppeld postvak is gevonden, het werkelijke account later wordt samengevoegd.
* Exchange-gerelateerde kenmerken worden alleen gesynchroniseerd als de **kenmerkmailNickName** een waarde heeft.
* Wanneer er meerdere forests zijn, worden kenmerken in de volgende volgorde verbruikt:
  1. Kenmerken met betrekking tot aanmelding (bijvoorbeeld userPrincipalName) worden vanuit het forest bijgedragen met een ingeschakeld account.
  2. Kenmerken die kunnen worden gevonden in een Exchange GAL (Global Address List) worden vanuit het forest bijgedragen met een Exchange-postvak.
  3. Als er geen postvak kan worden gevonden, kunnen deze kenmerken afkomstig zijn uit elk forest.
  4. Exchange-gerelateerde kenmerken (technische kenmerken die niet zichtbaar zijn in `mailNickname ISNOTNULL`de GAL) worden bijgedragen vanuit het forest waar .
  5. Als er meerdere forests zijn die aan een van deze regels voldoen, wordt de creatievolgorde (datum/tijd) van de Connectors (forests) gebruikt om te bepalen welk forest bijdraagt aan de kenmerken. Het eerste forest dat is verbonden, is het eerste forest dat wordt gesynchroniseerd. 

### <a name="contact-out-of-box-rules"></a>Contact opnemen met out-of-box regels
Een contactobject moet voldoen aan het volgende dat moet worden gesynchroniseerd:

* De contactpersoon moet per e-mail zijn ingeschakeld. Het wordt geverifieerd met de volgende regels:
  * `IsPresent([proxyAddresses]) = True)`. Het kenmerk proxyAdressen moet worden ingevuld.
  * Een primair e-mailadres is te vinden in het kenmerk proxyAddresses of het e-mailkenmerk. De aanwezigheid \@ van een wordt gebruikt om te controleren of de inhoud een e-mailadres is. Een van deze twee regels moet worden geëvalueerd op True.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Is er een vermelding met "SMTP:" en \@ als er is, kan een worden gevonden in de string?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Is het e-mailattribuut ingevuld en \@ kan er, als dat zo is, een in de tekenreeks worden gevonden?

De volgende contactobjecten worden **niet** gesynchroniseerd met Azure AD:

* `IsPresent([isCriticalSystemObject])`. Zorg ervoor dat er geen contactobjecten worden gesynchroniseerd die als kritiek zijn gemarkeerd. Zou niet moeten worden met een standaard configuratie.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Deze objecten zouden niet werken in Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Geen replicatieslachtofferobjecten synchroniseren.

### <a name="group-out-of-box-rules"></a>Out-of-box regels groeperen
Een groepsobject moet voldoen aan het volgende dat moet worden gesynchroniseerd:

* Moet minder dan 50.000 leden hebben. Deze telling is het aantal leden in de on-premises groep.
  * Als er meer leden zijn voordat de synchronisatie de eerste keer wordt gestart, wordt de groep niet gesynchroniseerd.
  * Als het aantal leden groeit vanaf het moment dat het in eerste instantie werd gemaakt, stopt het wanneer het 50.000 leden bereikt het synchroniseren totdat het aantal leden weer lager is dan 50.000.
  * Opmerking: Het aantal leden van 50.000 wordt ook afgedwongen door Azure AD. U groepen niet synchroniseren met meer leden, zelfs niet als u deze regel wijzigt of verwijdert.
* Als de groep een **distributiegroep**is, moet deze ook worden ingeschakeld. Zie [Out-of-box regels](#contact-out-of-box-rules) voor contact opnemen voor deze regel wordt afgedwongen.

De volgende groepsobjecten worden **niet** gesynchroniseerd met Azure AD:

* `IsPresent([isCriticalSystemObject])`. Zorg ervoor dat veel out-of-box objecten in Active Directory, zoals de groep ingebouwde beheerders, niet worden gesynchroniseerd.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Legacy-groep die wordt gebruikt door DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Rolgroep.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Geen replicatieslachtofferobjecten synchroniseren.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal out-of-box regels
FSP's worden samengevoegd met\*"elk" ( ) object in de metaverse. In werkelijkheid gebeurt deze join alleen voor gebruikers en beveiligingsgroepen. Deze configuratie zorgt ervoor dat cross-forest-lidmaatschappen worden opgelost en correct worden weergegeven in Azure AD.

### <a name="computer-out-of-box-rules"></a>Computer-out-of-box-regels
Een computerobject moet voldoen aan het volgende dat moet worden gesynchroniseerd:

* `userCertificate ISNOTNULL`. Alleen Windows 10-computers vullen dit kenmerk in. Alle computerobjecten met een waarde in dit kenmerk worden gesynchroniseerd.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Inzicht in het scenario voor out-of-boxregels
In dit voorbeeld gebruiken we een implementatie met één accountforest (A), één resourceforest (R) en één Azure AD-map.

![Afbeelding met scenariobeschrijving](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

In deze configuratie wordt ervan uitgegaan dat er een ingeschakeld account in het accountforest is en een uitgeschakeld account in het resourceforest met een gekoppeld postvak.

Ons doel met de standaardconfiguratie is:

* Kenmerken met betrekking tot aanmelding worden vanuit het forest gesynchroniseerd met het ingeschakelde account.
* Kenmerken die in de GAL-lijst (Globale adreslijst) kunnen worden gevonden, worden vanuit het forest gesynchroniseerd met het postvak. Als er geen postvak kan worden gevonden, wordt een ander forest gebruikt.
* Als er een gekoppeld postvak wordt gevonden, moet het gekoppelde account worden gevonden om het object te exporteren naar Azure AD.

### <a name="synchronization-rule-editor"></a>Synchronisatieregeleditor
De configuratie kan worden bekeken en gewijzigd met de tool Synchronization Rules Editor (SRE) en een snelkoppeling naar deze is te vinden in het startmenu.

![Pictogram Editor voor synchronisatieregels](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

De SRE is een hulpprogramma voor resourcekit en is geïnstalleerd met Azure AD Connect-synchronisatie. Als u het wilt starten, moet u lid zijn van de adsyncadmins-groep. Wanneer het begint, zie je zoiets als dit:

![Synchronisatieregels binnenkomen](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

In dit deelvenster ziet u alle synchronisatieregels die voor uw configuratie zijn gemaakt. Elke regel in de tabel is één synchronisatieregel. Links onder Regeltypen worden de twee verschillende typen weergegeven: Binnenkomend en Uitgaand. Inkomende en Uitgaande is vanuit de weergave van de metaverse. Je gaat je vooral richten op de inkomende regels in dit overzicht. De werkelijke lijst met synchronisatieregels is afhankelijk van het gedetecteerde schema in AD. In de bovenstaande afbeelding heeft het accountforest (fabrikamonline.com) geen services, zoals Exchange en Lync, en zijn er geen synchronisatieregels voor deze services gemaakt. In het bronforest (res.fabrikamonline.com) vindt u echter synchronisatieregels voor deze services. De inhoud van de regels is anders afhankelijk van de gedetecteerde versie. In een implementatie met Exchange 2013 zijn er bijvoorbeeld meer attribuutstromen geconfigureerd dan in Exchange 2010/2007.

### <a name="synchronization-rule"></a>Synchronisatieregel
Een synchronisatieregel is een configuratieobject met een set kenmerken die stromen wanneer aan een voorwaarde is voldaan. Het wordt ook gebruikt om te beschrijven hoe een object in een connectorruimte is gerelateerd aan een object in de metaverse, bekend als **join** of **match**. De synchronisatieregels hebben een voorrangswaarde die aangeeft hoe ze zich tot elkaar verhouden. Een synchronisatieregel met een lagere numerieke waarde heeft een hogere prioriteit en in een kenmerkstroomconflict wint een hogere prioriteit de conflictoplossing.

Kijk bijvoorbeeld naar de synchronisatieregel **in van AD – User AccountEnabled**. Markeer deze regel in de SRE en selecteer **Bewerken**.

Aangezien deze regel een kant-en-klare regel is, ontvangt u een waarschuwing wanneer u de regel opent. U moet geen [wijzigingen aanbrengen in out-of-box regels,](how-to-connect-sync-best-practices-changing-default-configuration.md)dus u wordt gevraagd wat uw bedoelingen zijn. In dit geval wilt u alleen de regel weergeven. Selecteer **Nee**.

![Waarschuwing synchronisatieregels](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Een synchronisatieregel heeft vier configuratiesecties: beschrijving, scopingfilter, Joinregels en Transformaties.

#### <a name="description"></a>Beschrijving
Het eerste deel bevat basisinformatie, zoals een naam en beschrijving.

![Tabblad Beschrijving in de editor voor synchronisatieregels](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

U vindt ook informatie over aan welk verbonden systeem deze regel is gerelateerd, welk objecttype in het aangesloten systeem waarop het van toepassing is en het metaverse objecttype. Het doeltype metaverse object is altijd persoon, ongeacht wanneer het bronobjecttype een gebruiker, iNetOrgPerson of contactpersoon is. Het doeltype metaverse objecten mag nooit veranderen, zodat het wordt gemaakt als een algemeen type. Het koppelingstype kan worden ingesteld op Deelnemen, StickyJoin of Voorziening. Deze instelling werkt samen met de sectie Join regels en wordt later behandeld.

U ook zien dat deze synchronisatieregel wordt gebruikt voor wachtwoordsynchronisatie. Als een gebruiker in het bereik van deze synchronisatieregel is, wordt het wachtwoord gesynchroniseerd van on-premises naar cloud (ervan uitgaande dat u de functie voor wachtwoordsynchronisatie hebt ingeschakeld).

#### <a name="scoping-filter"></a>Scoping-filter
De sectie Scopingfilter wordt gebruikt om te configureren wanneer een synchronisatieregel moet worden toegepast. Aangezien de naam van de synchronisatieregel die u bekijkt aangeeft dat deze alleen moet worden toegepast voor ingeschakelde gebruikers, is het bereik geconfigureerd, zodat het AD-kenmerk **userAccountControl** niet de bit 2-set mag hebben. Wanneer de synchronisatieengine een gebruiker in AD vindt, past deze synchronisatieregel toe wanneer **userAccountControl** is ingesteld op de decimale waarde 512 (inschakelde normale gebruiker). De regel is niet van toepassing wanneer de gebruiker **AccountControl** heeft ingesteld op 514 (uitgeschakelde normale gebruiker).

![Tabblad Scoping in de editor voor synchronisatieregels](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

Het scopingfilter heeft groepen en clausules die kunnen worden genest. Aan alle clausules binnen een groep moet worden voldaan om een synchronisatieregel toe te passen. Wanneer meerdere groepen zijn gedefinieerd, moet ten minste één groep worden voldaan om de regel toe te passen. Dat wil zeggen, een logische OR wordt geëvalueerd tussen groepen en een logische EN wordt geëvalueerd binnen een groep. Een voorbeeld van deze configuratie is te vinden in de uitgaande synchronisatieregel **uit naar AAD – Groepslid**. Er zijn verschillende synchronisatiefiltergroepen, bijvoorbeeld een voor`securityEnabled EQUAL True`beveiligingsgroepen ( )`securityEnabled EQUAL False`en een voor distributiegroepen ( ).

![Tabblad Scoping in de editor voor synchronisatieregels](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Deze regel wordt gebruikt om te bepalen welke groepen moeten worden ingericht in Azure AD. Distributiegroepen moeten e-mail zijn ingeschakeld om te worden gesynchroniseerd met Azure AD, maar voor beveiligingsgroepen is een e-mail niet vereist.

#### <a name="join-rules"></a>Lid worden van regels
Het derde deel wordt gebruikt om te configureren hoe objecten in de verbindingsruimte zich verhouden tot objecten in de metaverse. De regel die u eerder hebt bekeken, heeft geen configuratie voor Join-regels, dus in plaats daarvan gaat u kijken naar **In van AD - User Join**.

![Tabblad Regels deelnemen in de editor voor regelsynchronisatie](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

De inhoud van de joinregel is afhankelijk van de overeenkomende optie die is geselecteerd in de wizard installatie. Voor een binnenkomende regel begint de evaluatie met een object in de bronconnectorruimte en wordt elke groep in de joinregels achter elkaar geëvalueerd. Als een bronobject wordt geëvalueerd op precies één object in het metaverse met behulp van een van de joinregels, worden de objecten samengevoegd. Als alle regels zijn geëvalueerd en er geen overeenkomst is, wordt het koppelingstype op de beschrijvingspagina gebruikt. Als deze configuratie is ingesteld op **Voorziening,** wordt er een nieuw object gemaakt in het doel, het metaverse, als er ten minste één kenmerk in de joincriteria aanwezig is (heeft een waarde). Voor het inrichten van een nieuw object op het metaverse is ook bekend als een **object project** tegen de metaverse.

De joinregels worden slechts één keer geëvalueerd. Wanneer een verbindingsruimteobject en een metaverse object zijn samengevoegd, blijven ze verbonden zolang het bereik van de synchronisatieregel nog steeds is voldaan.

Bij het evalueren van synchronisatieregels moet slechts één synchronisatieregel met de gedefinieerde joinregels binnen het bereik zijn. Als er meerdere synchronisatieregels met joinregels voor één object worden gevonden, wordt er een fout gegenereerd. Daarom is het beste om slechts één synchronisatieregel te hebben met join gedefinieerd wanneer meerdere synchronisatieregels in het bereik van een object zijn. In de kant-en-klare configuratie voor Azure AD Connect-synchronisatie kunnen deze regels worden gevonden door naar de naam te kijken en deze te vinden met het woord **Join** aan het einde van de naam. Een synchronisatieregel zonder dat er joinregels zijn gedefinieerd, past de kenmerkstromen toe wanneer een andere synchronisatieregel de objecten samenvoegde of een nieuw object in het doel heeft ingericht.

Als u de bovenstaande afbeelding bekijkt, u zien dat de regel **objectSID** probeert aan te sluiten met **msExchMasterAccountSid** (Exchange) en **msRTCSIP-OriginatorSid** (Lync), wat we verwachten in een foresttopologie van accountbronnen. Je vindt dezelfde regel voor alle bossen. De veronderstelling is dat elk bos een account of resourceforest kan zijn. Deze configuratie werkt ook als u accounts hebt die in één forest leven en niet hoeven te worden samengevoegd.

#### <a name="transformations"></a>Transformaties
In de sectie transformatie worden alle kenmerkstromen gedefinieerd die van toepassing zijn op het doelobject wanneer de objecten worden samengevoegd en het bereikfilter is voldaan. Als u teruggaat naar de **regel In van AD – GebruikersaccountIngeschakeldsynchronisatieregel,** vindt u de volgende transformaties:

![Tabblad Transformaties in de editor voor regelsynchronisatie](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Als u deze configuratie in de context wilt plaatsen, wordt verwacht dat deze in een forestimplementatie account-resource wordt geplaatst, een ingeschakeld account in het accountforest en een uitgeschakeld account in het bronforest met Exchange- en Lync-instellingen. De synchronisatieregel die u bekijkt, bevat de kenmerken die nodig zijn voor aanmelding en deze kenmerken moeten uit het forest stromen waar een ingeschakeld account is ingeschakeld. Al deze attribuutstromen worden samengevoegd in één synchronisatieregel.

Een transformatie kan verschillende typen hebben: Constant, Direct en Expressie.

* Een constante stroom stroomt altijd een hardcoded waarde. In het bovenstaande geval stelt het altijd de waarde **True** in het metaverse kenmerk met de naam **accountEnabled**.
* Een directe stroom stroomt altijd de waarde van het kenmerk in de bron naar het doelkenmerk as-is.
* Het derde stroomtype is Expressie en zorgt voor meer geavanceerde configuraties.

De expressietaal is VBA (Visual Basic for Applications), zodat mensen met ervaring met Microsoft Office of VBScript de indeling herkennen. Kenmerken zijn ingesloten in vierkante haakjes,[attributeName]. Attribuutnamen en functienamen zijn hoofdlettergevoelig, maar de editor synchronisatieregels evalueert de expressies en geeft een waarschuwing als de expressie niet geldig is. Alle expressies worden uitgedrukt op één regel met geneste functies. Om de kracht van de configuratietaal weer te geven, is hier de stroom voor pwdLastSet, maar met extra opmerkingen ingevoegd:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Zie [Declaratieve inrichtingsexpressies begrijpen](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) voor meer informatie over de expressietaal voor kenmerkstromen.

### <a name="precedence"></a>Prioriteit
U hebt nu enkele afzonderlijke synchronisatieregels bekeken, maar de regels werken samen in de configuratie. In sommige gevallen wordt een kenmerkwaarde bijgedragen van meerdere synchronisatieregels naar hetzelfde doelkenmerk. In dit geval wordt kenmerkvoorrang gebruikt om te bepalen welk kenmerk wint. Kijk bijvoorbeeld naar het kenmerk sourceAnchor. Dit kenmerk is een belangrijk kenmerk om u aan te melden bij Azure AD. U een kenmerkstroom voor dit kenmerk vinden in twee verschillende synchronisatieregels, **In van AD – Gebruikersaccountingeschakeld** en in van AD – Algemeen **gebruiker**. Vanwege de prioriteit van de synchronisatieregel wordt het kenmerk sourceAnchor vanuit het forest eerst bijgedragen met een ingeschakeld account wanneer er meerdere objecten zijn gekoppeld aan het metaverse object. Als er geen ingeschakelde accounts zijn, gebruikt de synchronisatieengine de catch-all synchronisatieregel **in van AD – Algemeen gebruiker**. Deze configuratie zorgt ervoor dat zelfs voor accounts die zijn uitgeschakeld, is er nog steeds een sourceAnchor.

![Synchronisatieregels binnenkomen](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

De prioriteit voor synchronisatieregels wordt in groepen ingesteld door de wizard installatie. Alle regels in een groep hebben dezelfde naam, maar ze zijn verbonden met verschillende verbonden mappen. De installatiewizard geeft de regel **In van AD – User Join** hoogste prioriteit en deze wijzigt alle verbonden AD-mappen. Vervolgens gaat het verder met de volgende groepen regels in een vooraf gedefinieerde volgorde. Binnen een groep worden de regels toegevoegd in de volgorde waarin de connectors in de wizard zijn toegevoegd. Als er via de wizard een andere connector wordt toegevoegd, worden de synchronisatieregels opnieuw geordend en worden de regels van de nieuwe connector als laatste in elke groep ingevoegd.

### <a name="putting-it-all-together"></a>Alles samenvoegen
We weten nu genoeg over synchronisatieregels om te kunnen begrijpen hoe de configuratie werkt met de verschillende synchronisatieregels. Als u kijkt naar een gebruiker en de kenmerken die worden bijgedragen aan de metaverse, worden de regels in de volgende volgorde toegepast:

| Name | Opmerking |
|:--- |:--- |
| Binnenkomen vanuit AD – Gebruiker Join |Regel voor het samenvoegen van verbindingsruimteobjecten met metaverse. |
| Binnenkomen vanuit AD – UserAccount ingeschakeld |Kenmerken die nodig zijn voor aanmelding bij Azure AD en Office 365. We willen deze kenmerken van het ingeschakelde account. |
| In from AD – User Common from Exchange |Kenmerken die zijn gevonden in de lijst met globale adressen. We gaan ervan uit dat de kwaliteit van de gegevens het beste is in het bos waar we het postvak van de gebruiker hebben gevonden. |
| In van AD – User Common |Kenmerken die zijn gevonden in de lijst met globale adressen. Als we geen postvak hebben gevonden, kan elk ander samengevoegd object de kenmerkwaarde bijdragen. |
| Binnenkomen vanaf AD – User Exchange |Bestaat alleen als Exchange is gedetecteerd. Het stroomt alle infrastructuur Exchange attributen. |
| Binnenkomen vanuit AD – Gebruikerslync |Bestaat alleen als Lync is gedetecteerd. Het stroomt alle Infrastructuur Lync-kenmerken. |

## <a name="next-steps"></a>Volgende stappen
* Lees meer over het configuratiemodel in [Declaratieve provisioning begrijpen.](concept-azure-ad-connect-sync-declarative-provisioning.md)
* Lees meer over de expressietaal in [Het begrijpen van declaratieve inrichtingsuitdrukkingen](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Lees verder hoe de out-of-box configuratie werkt in [Het begrijpen van gebruikers en contactpersonen](concept-azure-ad-connect-sync-user-and-contacts.md)
* Zie hoe u een praktische wijziging aanbrengen met declaratieve inrichting in [Hoe u een wijziging in de standaardconfiguratie aanbrengen.](how-to-connect-sync-change-the-configuration.md)

**Overzichtsonderwerpen**

* [Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)

