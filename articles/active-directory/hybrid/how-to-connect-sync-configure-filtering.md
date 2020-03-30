---
title: 'Azure AD Connect-synchronisatie: filtering configureren | Microsoft Documenten'
description: Hier wordt uitgelegd hoe u filtering configureert in Azure AD Connect-synchronisatie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983699dfbfe3e8fa332da4810d1514a11029077f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261097"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Azure AD Connect-synchronisatie: filtering configureren
Met behulp van filtering u bepalen welke objecten worden weergegeven in Azure Active Directory (Azure AD) in uw on-premises directory. Met de standaardconfiguratie worden alle objecten in alle domeinen in de geconfigureerde forests uitgevoerd. In het algemeen is dit de aanbevolen configuratie. Gebruikers die Office 365-workloads gebruiken, zoals Exchange Online en Skype voor Bedrijven, profiteren van een volledige algemene adreslijst, zodat ze e-mail kunnen verzenden en iedereen kunnen bellen. Met de standaardconfiguratie zouden ze dezelfde ervaring hebben als met een on-premises implementatie van Exchange of Lync.

In sommige gevallen moet u echter enkele wijzigingen aanbrengen in de standaardconfiguratie. Hier volgen enkele voorbeelden:

* U bent van plan de topologie van de [MULTI-Azure AD-map te](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant)gebruiken. Vervolgens moet u een filter toepassen om te bepalen welke objecten zijn gesynchroniseerd met een bepaalde Azure AD-map.
* U voert een pilot uit voor Azure of Office 365 en u wilt alleen een subset van gebruikers in Azure AD. In de kleine pilot is het niet belangrijk om een volledige globale adreslijst te hebben om de functionaliteit aan te tonen.
* U hebt veel serviceaccounts en andere niet-persoonlijke accounts die u niet wilt in Azure AD.
* Om nalevingsredenen verwijdert u geen on-premises gebruikersaccounts. Je schakelt ze alleen maar uit. Maar in Azure AD wilt u alleen actieve accounts aanwezig zijn.

In dit artikel wordt uitgelegd hoe u de verschillende filtermethoden configureert.

> [!IMPORTANT]
> Microsoft biedt geen ondersteuning voor het wijzigen of uitvoeren van Azure AD Connect-synchronisatie anders dan op de manier die officieel is gedocumenteerd. Een van deze acties kan resulteren in een inconsistente of niet-ondersteunde status van Azure AD Connect-synchronisatie. Als gevolg hiervan kan Microsoft geen technische ondersteuning bieden voor dergelijke implementaties.

## <a name="basics-and-important-notes"></a>Basisbeginselen en belangrijke notities
In Azure AD Connect-synchronisatie u filteren op elk gewenst moment inschakelen. Als u begint met een standaardconfiguratie van adreslijstsynchronisatie en vervolgens het filteren configureert, worden de objecten die worden uitgefilterd niet langer gesynchroniseerd met Azure AD. Vanwege deze wijziging worden alle objecten in Azure AD die eerder zijn gesynchroniseerd maar vervolgens zijn gefilterd, verwijderd in Azure AD.

Voordat u wijzigingen in het filteren aanbrengt, moet u ervoor zorgen dat u [de geplande taak uitschakelt,](#disable-the-scheduled-task) zodat u niet per ongeluk wijzigingen exporteert waarvan u nog niet hebt geverifieerd dat deze correct zijn.

Omdat filteren veel objecten tegelijk kan verwijderen, wilt u ervoor zorgen dat uw nieuwe filters correct zijn voordat u wijzigingen naar Azure AD gaat exporteren. Nadat u de configuratiestappen hebt voltooid, raden we u ten zeerste aan de [verificatiestappen](#apply-and-verify-changes) te volgen voordat u Azure AD exporteert en wijzigt.

Om te voorkomen dat u per ongeluk veel objecten verwijdert, is de functie "[prevent accidental deletes](how-to-connect-sync-feature-prevent-accidental-deletes.md)" standaard ingeschakeld. Als u veel objecten verwijdert vanwege filtering (standaard 500), moet u de stappen in dit artikel volgen om de verwijderingen door te laten gaan naar Azure AD.

Als u een build vóór november 2015 gebruikt ([1.0.9125](reference-connect-version-history.md#1091250)), een wijziging aanbrengen in een filterconfiguratie en wachtwoordhashsynchronisatie gebruiken, moet u een volledige synchronisatie van alle wachtwoorden activeren nadat u de configuratie hebt voltooid. Zie Een volledige synchronisatie van alle [wachtwoorden](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords)activeren voor stappen over het activeren van een wachtwoord volledige synchronisatie. Als u op build 1.0.9125 of hoger bent, berekent de reguliere **volledige synchronisatieactie** ook of wachtwoorden moeten worden gesynchroniseerd en of deze extra stap niet meer nodig is.

Als **gebruikersobjecten** per ongeluk zijn verwijderd in Azure AD vanwege een filterfout, u de gebruikersobjecten in Azure AD opnieuw maken door uw filterconfiguraties te verwijderen. Dan u uw mappen opnieuw synchroniseren. Met deze actie worden de gebruikers uit de prullenbak in Azure AD hersteld. U echter andere objecttypen niet verwijderen. Als u bijvoorbeeld per ongeluk een beveiligingsgroep verwijdert en deze is gebruikt om een resource te aclen, kunnen de groep en de ACL's niet worden hersteld.

Azure AD Connect verwijdert alleen objecten waarvan het ooit als in het bereik is beschouwd. Als er objecten in Azure AD zijn gemaakt door een andere synchronisatieengine en deze objecten niet in het bereik zijn, worden deze niet verwijderd door filtering toe te voegen. Als u bijvoorbeeld begint met een DirSync-server die een volledige kopie van uw volledige directory in Azure AD heeft gemaakt en u een nieuwe Azure AD Connect-synchronisatieserver installeert, parallel met het filteren dat vanaf het begin is ingeschakeld, verwijdert Azure AD Connect de extra objecten niet die zijn gemaakt door DirSync.

De filterconfiguratie blijft behouden wanneer u een nieuwere versie van Azure AD Connect installeert of upgradet. Het is altijd een aanbevolen manier om te controleren of de configuratie niet per ongeluk is gewijzigd na een upgrade naar een nieuwere versie voordat de eerste synchronisatiecyclus wordt uitgevoerd.

Als u meer dan één forest hebt, moet u de filterconfiguraties die in dit onderwerp worden beschreven, toepassen op elk forest (ervan uitgaande dat u voor alle foresten dezelfde configuratie wilt).

### <a name="disable-the-scheduled-task"></a>De geplande taak uitschakelen
Voer de volgende stappen uit om de ingebouwde planner uit te schakelen die elke 30 minuten een synchronisatiecyclus activeert:

1. Ga naar een PowerShell-prompt.
2. Uitvoeren `Set-ADSyncScheduler -SyncCycleEnabled $False` om de planner uit te schakelen.
3. Breng de wijzigingen aan die in dit artikel zijn gedocumenteerd.
4. Uitvoeren `Set-ADSyncScheduler -SyncCycleEnabled $True` om de planner opnieuw in te schakelen.

**Als u een Azure AD Connect-build gebruikt vóór 1.1.105.0**  
Voer de volgende stappen uit om de geplande taak uit te schakelen die elke drie uur een synchronisatiecyclus activeert:

1. Taakplanner **starten** in het menu **Start.**
2. Zoek **Task Scheduler Library**de taak met de naam **Azure AD Sync Scheduler**met de rechtermuisknop en selecteer **Uitschakelen.**  
   ![Task Scheduler](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. U nu configuratiewijzigingen aanbrengen en de synchronisatieengine handmatig uitvoeren vanaf de console **Synchronisatieservicebeheer.**

Nadat u al uw filterwijzigingen hebt voltooid, vergeet dan niet om terug te komen en de taak opnieuw **in te schakelen.**

## <a name="filtering-options"></a>Filteropties
U de volgende filterconfiguratietypen toepassen op het hulpprogramma voor adreslijstsynchronisatie:

* [**Groepsgebaseerd**](#group-based-filtering): Filteren op basis van één groep kan alleen worden geconfigureerd bij de eerste installatie met behulp van de wizard installatie.
* [**Domeingebaseerd:**](#domain-based-filtering)Met deze optie u selecteren welke domeinen worden gesynchroniseerd met Azure AD. U ook domeinen toevoegen en verwijderen uit de configuratie van de synchronisatieengine wanneer u wijzigingen aanbrengt in uw on-premises infrastructuur nadat u Azure AD Connect-synchronisatie hebt geïnstalleerd.
* [**Organisatie-eenheid (OU)-gebaseerd:**](#organizational-unitbased-filtering)Met deze optie u selecteren welke OK's synchroniseren met Azure AD. Deze optie is bedoeld voor alle objecttypen in geselecteerde Ok's.
* [**Op basis van kenmerken:**](#attribute-based-filtering)Met deze optie u objecten filteren op basis van kenmerkwaarden op de objecten. U ook verschillende filters voor verschillende objecttypen hebben.

U meerdere filteropties tegelijk gebruiken. U bijvoorbeeld op OU gebaseerde filtering gebruiken om alleen objecten in één organisatieorganisatie op te nemen. Tegelijkertijd u filtering op basis van kenmerken gebruiken om de objecten verder te filteren. Wanneer u meerdere filtermethoden gebruikt, gebruiken de filters een logische "AND" tussen de filters.

## <a name="domain-based-filtering"></a>Filteren op basis van domeinen
In deze sectie vindt u de stappen om uw domeinfilter te configureren. Als u domeinen in uw forest hebt toegevoegd of verwijderd nadat u Azure AD Connect hebt geïnstalleerd, moet u ook de filterconfiguratie bijwerken.

De voorkeursmanier om domeingebaseerde filtering te wijzigen is door de installatiewizard uit te voeren en [domein- en organisatie-eenheidfiltering te](how-to-connect-install-custom.md#domain-and-ou-filtering)wijzigen. De wizard installatie automatiseert alle taken die in dit onderwerp zijn gedocumenteerd.

U moet deze stappen alleen volgen als u de installatiewizard om de een of andere reden niet uitvoeren.

Domeingebaseerde filterconfiguratie bestaat uit de volgende stappen:

1. Selecteer de domeinen die u in de synchronisatie wilt opnemen.
2. Pas voor elk toegevoegd en verwijderd domein de run-profielen aan.
3. [Wijzigingen toepassen en verifiëren](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Selecteer de domeinen die moeten worden gesynchroniseerd
Er zijn twee manieren om de domeinen te selecteren die moeten worden gesynchroniseerd:
    - De synchronisatieservice gebruiken
    - De wizard Azure AD Connect gebruiken.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Selecteer de domeinen die moeten worden gesynchroniseerd met de synchronisatieservice
Ga als volgt te werk om het domeinfilter in te stellen:

1. Meld u aan bij de server waarop Azure AD Connect wordt gesynchroniseerd met een account dat lid is van de beveiligingsgroep **ADSyncAdmins.**
2. Synchronisatieservice **starten** via het menu **Start.**
3. Selecteer **Connectors**en selecteer in de lijst **Connectors** de connector met het type Active Directory **Domain Services**. Selecteer **Eigenschappen**in **Acties**.  
   ![Eigenschappen van connector](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klik **op Mappartities configureren**.
5. Selecteer en selecteer de selectie van domeinen indien nodig in de lijst **Mappartities** selecteren. Controleer of alleen de partities zijn geselecteerd die u wilt synchroniseren.  
   ![Partities](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Als u uw on-premises Active Directory-infrastructuur hebt gewijzigd en domeinen uit het forest hebt toegevoegd of verwijderd, klikt u op de knop **Vernieuwen** om een bijgewerkte lijst te krijgen. Wanneer u vernieuwt, wordt u om referenties gevraagd. Geef alle referenties met leestoegang tot Windows Server Active Directory. Het hoeft niet de gebruiker te zijn die vooraf is ingevuld in het dialoogvenster.  
   ![Vernieuwen nodig](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Wanneer u klaar bent, sluit u het dialoogvenster **Eigenschappen** door op **OK**te klikken. Als u domeinen uit het forest hebt verwijderd, wordt in een pop-up van een bericht vermeld dat een domein is verwijderd en dat die configuratie wordt opgeschoond.
7. Ga door met het aanpassen van de runprofielen.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Selecteer de domeinen die moeten worden gesynchroniseerd met de wizard Azure AD Connect
Ga als volgt te werk om het domeinfilter in te stellen:

1.  De wizard Azure AD Connect starten
2.  Klik **op Configureren**.
3.  Selecteer **Synchronisatieopties aanpassen** en klik op **Volgende**.
4.  Voer uw Azure AD-referenties in
5.  Klik op het scherm **Verbonden mappen** op **Volgende**.
6.  Klik op de **filterpagina Domein en ORGANISATIE** op **Vernieuwen**.  Nieuwe domeinen ziek verschijnen nu en verwijderde domeinen zullen verdwijnen.
   ![Partities](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>De runprofielen bijwerken
Als u uw domeinfilter hebt bijgewerkt, moet u ook de runprofielen bijwerken.

1. Controleer in de lijst **Connectors** of de connector die u in de vorige stap hebt gewijzigd, is geselecteerd. Selecteer In **Acties**de optie **Run-profielen configureren**.  
   ![Connectorrunprofielen 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Zoek en identificeer de volgende profielen:
    * Volledig importbewerking
    * Volledige synchronisatie
    * Delta-Import
    * Deltasynchronisatie
    * Exporteren
3. Pas voor elk profiel de **toegevoegde** en **verwijderde** domeinen aan.
    1. Ga voor elk van de vijf profielen de volgende stappen uit voor elk **toegevoegd** domein:
        1. Selecteer het runprofiel en klik op **Nieuwe stap**.
        2. Selecteer op de pagina **Stap configureren** in de vervolgkeuzelijst **Type** het staptype met dezelfde naam als het profiel dat u configureert. Klik vervolgens op **Volgende**.  
        ![Connectorrunprofielen 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Selecteer op de pagina **Connectorconfiguratie** in de vervolgkeuzelijst **Partitie** de naam van het domein dat u aan uw domeinfilter hebt toegevoegd.  
        ![Connectorrunprofielen 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Als u het dialoogvenster **Run-profiel configureren** wilt sluiten, klikt u op **Voltooien**.
    2. Ga voor elk van de vijf profielen de volgende stappen uit voor elk **verwijderd** domein:
        1. Selecteer het runprofiel.
        2. Als de **waarde** van het kenmerk **Partitie** een GUID is, selecteert u de uitvoerenstap en klikt u op **Stap verwijderen**.  
        ![Connectorrunprofielen 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Controleer uw wijziging. Elk domein dat u wilt synchroniseren, moet worden weergegeven als een stap in elk runprofiel.
4. Als u het dialoogvenster **Run-profielen configureren** wilt sluiten, klikt u op **OK**.
5.  Als u de configuratie wilt voltooien, moet u een **volledige import** en een **Delta-synchronisatie**uitvoeren. Lees verder in de sectie [Wijzigingen toepassen en verifiëren](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filteren op organisatie-eenheid
De voorkeursmanier om op ou's gebaseerde filtering te wijzigen, is door de installatiewizard uit te voeren en [domein- en organisatie-eenheidfiltering te](how-to-connect-install-custom.md#domain-and-ou-filtering)wijzigen. De wizard installatie automatiseert alle taken die in dit onderwerp zijn gedocumenteerd.

U moet deze stappen alleen volgen als u de installatiewizard om de een of andere reden niet uitvoeren.

Ga als volgt te werk om filteren op basis van organisatie-eenheden te configureren:

1. Meld u aan bij de server waarop Azure AD Connect wordt gesynchroniseerd met een account dat lid is van de beveiligingsgroep **ADSyncAdmins.**
2. Synchronisatieservice **starten** via het menu **Start.**
3. Selecteer **Connectors**en selecteer in de lijst **Connectors** de connector met het type Active Directory **Domain Services**. Selecteer **Eigenschappen**in **Acties**.  
   ![Eigenschappen van connector](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klik **op Mappartities configureren,** selecteer het domein dat u wilt configureren en klik vervolgens op **Containers**.
5. Wanneer u wordt gevraagd, geeft u alle referenties met leestoegang tot uw on-premises Active Directory. Het hoeft niet de gebruiker te zijn die vooraf is ingevuld in het dialoogvenster.
6. Schakel **in** het dialoogvenster Containers selecteren de ok's uit die u niet wilt synchroniseren met de cloudmap en klik op **OK**.  
   ![Ok's in het dialoogvenster Containers selecteren](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * De container **Computers** moet worden geselecteerd om uw Windows 10-computers te synchroniseren met Azure AD. Als uw computers met domeinverbonden computers zich in andere ok's bevinden, controleert u of deze zijn geselecteerd.
   * De container **ForeignSecurityPrincipals** moet zijn geselecteerd als er meerdere forests met vertrouwensrelaties zijn. Dankzij deze container kan het lidmaatschap van de beveiligingsgroep van verschillende forests worden omgezet.
   * De **organisatie-eenheid RegisteredDevices** moet worden geselecteerd als u de terugschrijffunctie van het apparaat hebt ingeschakeld. Als u een andere terugschrijffunctie gebruikt, zoals groepsterugschrijftekst, controleert u of deze locaties zijn geselecteerd.
   * Selecteer een andere organisatiewaar gebruikers, iNetOrgPersons, Groepen, Contactpersonen en Computers zich bevinden. Op de foto bevinden al deze OU's zich in de ou ManagedObjects.
   * Als u groepsfiltering gebruikt, moet de organisatie-eenheid waarin de groep zich bevindt, worden opgenomen.
   * Houd er rekening mee dat u configureren of nieuwe OE's die worden toegevoegd nadat de filterconfiguratie is voltooid, zijn gesynchroniseerd of niet worden gesynchroniseerd. Zie de volgende sectie voor details.
7. Wanneer u klaar bent, sluit u het dialoogvenster **Eigenschappen** door op **OK**te klikken.
8. Als u de configuratie wilt voltooien, moet u een **volledige import** en een **Delta-synchronisatie**uitvoeren. Lees verder in de sectie [Wijzigingen toepassen en verifiëren](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Nieuwe oE's synchroniseren
Nieuwe O's die zijn gemaakt nadat het filteren is geconfigureerd, worden standaard gesynchroniseerd. Deze status wordt aangegeven door een geselecteerd selectievakje. U ook de selectie van bepaalde sub-OE's ongedaan maken. Als u dit gedrag wilt gebruiken, klikt u op het vakje totdat het wit wordt met een blauw vinkje (de standaardstatus). Schakel vervolgens de selectie uit van sub-OU's die u niet wilt synchroniseren.

Als alle sub-OU's zijn gesynchroniseerd, is het vakje wit met een blauw vinkje.  
![OU met alle geselecteerde vakken](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Als sommige sub-OU's niet zijn geselecteerd, is het vakje grijs met een wit vinkje.  
![Ou met een aantal sub-OU's niet geselecteerd](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

Met deze configuratie wordt een nieuwe organisatie-eenheid die is gemaakt onder ManagedObjects gesynchroniseerd.

Met de wizard Installatie van Azure AD Connect wordt deze configuratie altijd gemaakt.

### <a name="dont-synchronize-new-ous"></a>Nieuwe OK's niet synchroniseren
U de synchronisatieengine configureren om geen nieuwe U's te synchroniseren nadat de filterconfiguratie is voltooid. Deze status wordt in de gebruikersinterface aangegeven door het vakdat effen grijs wordt weergegeven zonder vinkje. Als u dit gedrag wilt volgen, klikt u op het vakje totdat het wit wordt zonder vinkje. Selecteer vervolgens de sub-OU's die u wilt synchroniseren.

![Ou met de hoofdmap niet geselecteerd](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

Met deze configuratie wordt een nieuwe organisatie-eenheid die is gemaakt onder ManagedObjects niet gesynchroniseerd.

## <a name="attribute-based-filtering"></a>Filtering op basis van kenmerken
Zorg ervoor dat u de bouw van november 2015[(1.0.9125)](reference-connect-version-history.md#1091250)of later gebruikt om deze stappen te laten werken.

> [!IMPORTANT]
>Microsoft raadt aan om de standaardregels van **Azure AD Connect**niet te wijzigen. Als u de regel wilt wijzigen, kloont u deze en schakelt u de oorspronkelijke regel uit. Breng wijzigingen aan in de gekloonde regel. Houd er rekening mee dat door dit te doen (het uitschakelen van de oorspronkelijke regel) u mist eventuele bug fixes of functies ingeschakeld via die regel.

Filtering op basis van kenmerken is de meest flexibele manier om objecten te filteren. U de kracht van [declaratieve inrichting](concept-azure-ad-connect-sync-declarative-provisioning.md) gebruiken om bijna elk aspect van wanneer een object is gesynchroniseerd met Azure AD te beheren.

U [binnenkomende](#inbound-filtering) filters van Active Directory toepassen op de metaverse en [uitgaande](#outbound-filtering) filtering van de metaverse naar Azure AD. We raden u aan inkomende filters toe te passen, omdat dit het gemakkelijkst te onderhouden is. U mag alleen uitgaande filters gebruiken als het nodig is om objecten uit meer dan één forest aan te sluiten voordat de evaluatie kan plaatsvinden.

### <a name="inbound-filtering"></a>Binnenkomende filtering
Inkomende filtering maakt gebruik van de standaardconfiguratie, waarbij objecten die naar Azure AD gaan, de metaverse kenmerkcloud moeten hebbendie niet is ingesteld op een te synchroniseren waarde. Als de waarde van dit kenmerk is ingesteld op **True,** wordt het object niet gesynchroniseerd. Het moet niet worden ingesteld op **False,** door het ontwerp. Om ervoor te zorgen dat andere regels de mogelijkheid hebben om een waarde bij te dragen, wordt dit kenmerk alleen verondersteld de waarden **Waar** of **NULL** (afwezig) te hebben.

Bij binnenkomende filtering gebruikt u de kracht van **het bereik** om te bepalen welke objecten u wel of niet wilt synchroniseren. Hier maakt u aanpassingen om aan de eisen van uw eigen organisatie te voldoen. De scopemodule heeft een **groep** en een **clausule** om te bepalen wanneer een synchronisatieregel binnen het bereik is. Een groep bevat een of meer clausules. Er is een logische "AND" tussen meerdere clausules, en een logische "OR" tussen meerdere groepen.

Laten we eens kijken naar een voorbeeld:  
![Bereik](./media/how-to-connect-sync-configure-filtering/scope.png)  
Dit moet worden gelezen als **(afdeling = IT) OF (afdeling = Verkoop EN c = US)**.

In de volgende voorbeelden en stappen gebruikt u het gebruikersobject als voorbeeld, maar u dit gebruiken voor alle objecttypen.

In de volgende monsters begint de prioriteitswaarde met 50. Dit kan een willekeurig aantal niet gebruikt, maar moet lager zijn dan 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Negatieve filtering: "synchroniseer deze niet"
In het volgende voorbeeld filtert u alle gebruikers uit (niet synchroniseren) waarbij **extensieAttribute15** de waarde **NoSync**heeft.

1. Meld u aan bij de server waarop Azure AD Connect wordt gesynchroniseerd met een account dat lid is van de beveiligingsgroep **ADSyncAdmins.**
2. De **editor synchronisatieregels starten** in het menu **Start.**
3. Controleer **of Binnenkomenis** is geselecteerd en klik op **Nieuwe regel toevoegen**.
4. Geef de regel een beschrijvende naam, zoals "*In van AD – Gebruiker DoNotSyncFilter*". Selecteer het juiste forest, selecteer **Gebruiker** als **objecttype CS**en selecteer **Persoon** als **het OBJECTtype MV**. Selecteer **Join**in **Koppelingstype**. Typ **in Voorrang**een waarde die momenteel niet wordt gebruikt door een andere synchronisatieregel (bijvoorbeeld 50) en klik op **Volgende**.  
   ![Inkomende 1 beschrijving](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. Klik **in het filter Scoping**op Groep **toevoegen**en klik op **Clausule toevoegen**. Selecteer **Extensieattribuut15**in **Attribuut**15 . Controleer of **Operator** is ingesteld op **EQUAL**en typ de waarde **NoSync** in het vak **Waarde.** Klik op **Volgende**.  
   ![Binnenkomend 2 bereik](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Laat de **regels voor deelnemen** leeg en klik op **Volgende**.
7. Klik **op Transformatie toevoegen,** selecteer **flowtype** als **constant**en selecteer **cloudFiltered** als **doelkenmerk**. Typ **True**in het tekstvak **Bron** . Klik **op Toevoegen** om de regel op te slaan.  
   ![Inkomende 3 transformatie](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. Als u de configuratie wilt voltooien, moet u een **volledige synchronisatie**uitvoeren. Lees verder in de sectie [Wijzigingen toepassen en verifiëren](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Positieve filtering: "alleen deze synchroniseren"
Het uitdrukken van positieve filtering kan uitdagender zijn omdat u ook objecten moet overwegen die niet voor de hand liggen om te worden gesynchroniseerd, zoals vergaderruimtes. U gaat ook het standaardfilter overschrijven in de regel Out-of-Box **In van AD - Gebruiker Join**. Wanneer u uw aangepaste filter maakt, moet u kritieke systeemobjecten, replicatieconflictobjecten, speciale postvakken en de serviceaccounts voor Azure AD Connect niet opnemen.

De optie voor positieve filtering vereist twee synchronisatieregels. U hebt één regel (of meerdere) nodig met het juiste bereik van objecten om te synchroniseren. U hebt ook een tweede catch-all synchronisatieregel nodig die alle objecten filtert die nog niet zijn geïdentificeerd als een object dat moet worden gesynchroniseerd.

In het volgende voorbeeld synchroniseert u alleen gebruikersobjecten waarbij het eigenschapkenmerk de waarde **Verkoop**heeft.

1. Meld u aan bij de server waarop Azure AD Connect wordt gesynchroniseerd met een account dat lid is van de beveiligingsgroep **ADSyncAdmins.**
2. De **editor synchronisatieregels starten** in het menu **Start.**
3. Controleer **of Binnenkomenis** is geselecteerd en klik op **Nieuwe regel toevoegen**.
4. Geef de regel een beschrijvende naam, zoals "*In van AD – User Sales sync*". Selecteer het juiste forest, selecteer **Gebruiker** als **objecttype CS**en selecteer **Persoon** als **het OBJECTtype MV**. Selecteer **Join**in **Koppelingstype**. Typ **in Voorrang**een waarde die momenteel niet wordt gebruikt door een andere synchronisatieregel (bijvoorbeeld 51) en klik op **Volgende**.  
   ![Inkomende 4 beschrijving](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. Klik **in het filter Scoping**op Groep **toevoegen**en klik op **Clausule toevoegen**. Selecteer **in Kenmerk**de **afdeling**. Controleer of Operator is ingesteld op **EQUAL**en typ de waarde **Verkoop** in het vak **Waarde.** Klik op **Volgende**.  
   ![Binnenkomend 5 toepassingsgebied](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Laat de **regels voor deelnemen** leeg en klik op **Volgende**.
7. Klik **op Transformatie toevoegen,** selecteer **Constant** als **flowtype**en selecteer de **cloudGefilterd** als **doelkenmerk**. Typ **False**in het vak **Bron** . Klik **op Toevoegen** om de regel op te slaan.  
   ![Inkomende 6 transformatie](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Dit is een speciaal geval waarbij u cloudFiltered expliciet instelt op **False.**
8. We moeten nu de catch-all sync regel maken. Geef de regel een beschrijvende naam, zoals "*In van AD – User Catch-all filter*". Selecteer het juiste forest, selecteer **Gebruiker** als **objecttype CS**en selecteer **Persoon** als **het OBJECTtype MV**. Selecteer **Join**in **Koppelingstype**. Typ **in Voorrang**een waarde die momenteel niet wordt gebruikt door een andere synchronisatieregel (bijvoorbeeld 99). U hebt een voorrangswaarde geselecteerd die hoger is (lagere prioriteit) dan de vorige synchronisatieregel. Maar je hebt ook wat ruimte gelaten, zodat je later meer filtersynchronisatieregels toevoegen wanneer je wilt beginnen met het synchroniseren van extra afdelingen. Klik op **Volgende**.  
   ![Inkomende 7 beschrijving](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Laat **het scopingfilter** leeg en klik op **Volgende**. Een leeg filter geeft aan dat de regel op alle objecten moet worden toegepast.
10. Laat de **regels voor deelnemen** leeg en klik op **Volgende**.
11. Klik **op Transformatie toevoegen,** selecteer **Constant** als **flowtype**en selecteer **cloudFiltered** als **doelkenmerk**. Typ **True**in het vak **Bron** . Klik **op Toevoegen** om de regel op te slaan.  
    ![Inkomende 3 transformatie](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. Als u de configuratie wilt voltooien, moet u een **volledige synchronisatie**uitvoeren. Lees verder in de sectie [Wijzigingen toepassen en verifiëren](#apply-and-verify-changes).

Als dat nodig is, u meer regels van het eerste type maken waarin u meer objecten in de synchronisatie opneemt.

### <a name="outbound-filtering"></a>Uitgaand filteren
In sommige gevallen is het noodzakelijk om het filteren alleen te doen nadat de objecten zijn samengevoegd in de metaverse. Het kan bijvoorbeeld nodig zijn om het e-mailkenmerk uit het resourceforest en het kenmerk userPrincipalName uit het accountforest te bekijken om te bepalen of een object moet worden gesynchroniseerd. In deze gevallen maakt u de filtering op de uitgaande regel.

In dit voorbeeld wijzigt u de filtering zodat alleen gebruikers die @contoso.com zowel hun e-mail als de userPrincipalName hebben eindigend, worden gesynchroniseerd:

1. Meld u aan bij de server waarop Azure AD Connect wordt gesynchroniseerd met een account dat lid is van de beveiligingsgroep **ADSyncAdmins.**
2. De **editor synchronisatieregels starten** in het menu **Start.**
3. Klik **onder Regelstype**op **Uitgaand**.
4. Afhankelijk van de versie van Connect die u gebruikt, zoekt u de regel met de naam **Out to AAD - User Join** or Out to AAD - User Join **SOAInAD**en klikt u op **Bewerken**.
5. Antwoord in de pop-up **Ja** om een kopie van de regel te maken.
6. Wijzig op de pagina **Beschrijving** **Voorrang** in een ongebruikte waarde, zoals 50.
7. Klik op **Het filter Scoping** op de linkernavigatie en klik vervolgens op **Clausule toevoegen**. Selecteer **in Kenmerk** **e-mail**. Selecteer **IN Operator** **ENDSMET**. Typ **in Waarde** ** \@contoso.com**en klik op Clausule **toevoegen**. Selecteer **in Kenmerk**de optie **userPrincipalName**. Selecteer **IN Operator** **ENDSMET**. Typ ** \@contoso.com**in **waarde**.
8. Klik op **Opslaan**.
9. Als u de configuratie wilt voltooien, moet u een **volledige synchronisatie**uitvoeren. Lees verder in de sectie [Wijzigingen toepassen en verifiëren](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Wijzigingen toepassen en verifiëren
Nadat u uw configuratiewijzigingen hebt aangebracht, moet u deze toepassen op de objecten die al in het systeem aanwezig zijn. Het kan ook zijn dat de objecten die zich momenteel niet in de synchronisatieengine bevindt, moeten worden verwerkt (en de synchronisatieengine moet het bronsysteem opnieuw lezen om de inhoud ervan te verifiëren).

Als u de configuratie hebt gewijzigd met behulp van **domein-** of **organisatie-eenheidfiltering,** moet u een **volledige import**doen, gevolgd door **Delta-synchronisatie**.

Als u de configuratie hebt gewijzigd met **behulp** van kenmerkfiltering, moet u een **volledige synchronisatie**doen.

Ga als volgt te werk:

1. Synchronisatieservice **starten** via het menu **Start.**
2. Selecteer **Connectors**. Selecteer **in** de lijst Connectors de connector waar u eerder een configuratiewijziging hebt aangebracht. Selecteer **Uitvoeren**in **Acties**.  
   ![Connectorrun](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. Selecteer **in Run-profielen**de bewerking die in de vorige sectie is vermeld. Als u twee acties moet uitvoeren, voert u de tweede uit nadat de eerste is voltooid. (De kolom **Status** is **Nietactief** voor de geselecteerde connector.)

Na de synchronisatie worden alle wijzigingen gefaseerd uitgevoerd om te worden geëxporteerd. Voordat u de wijzigingen in Azure AD daadwerkelijk aanbrengt, wilt u controleren of al deze wijzigingen juist zijn.

1. Start een opdrachtprompt en `%ProgramFiles%\Microsoft Azure AD Sync\bin`ga naar .
2. Voer `csexport "Name of Connector" %temp%\export.xml /f:x` uit.  
   De naam van de connector staat in de synchronisatieservice. Het heeft een naam die lijkt op 'contoso.com – AAD' voor Azure AD.
3. Voer `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` uit.
4. U hebt nu een bestand met de naam export.csv met de naam export.csv dat kan worden onderzocht in Microsoft Excel. Dit bestand bevat alle wijzigingen die op het punt staan te worden geëxporteerd.
5. Breng de nodige wijzigingen aan in de gegevens of configuratie en voer deze stappen opnieuw uit (Importeren, synchroniseren en verifiëren) totdat de wijzigingen die op het punt staan te worden geëxporteerd, zijn wat u verwacht.

Als u tevreden bent, exporteert u de wijzigingen naar Azure AD.

1. Selecteer **Connectors**. Selecteer in de lijst **Connectors** de Azure AD Connector. Selecteer **Uitvoeren**in **Acties**.
2. Selecteer **Exporteren**in **Run-profielen**.
3. Als uw configuratiewijzigingen veel objecten verwijderen, ziet u een fout in de export wanneer het getal meer is dan de geconfigureerde drempelwaarde (standaard 500). Als u deze fout ziet, moet u de functie "[per ongeluk verwijderen](how-to-connect-sync-feature-prevent-accidental-deletes.md)" tijdelijk uitschakelen.

Nu is het tijd om de planner weer in te schakelen.

1. Taakplanner **starten** in het menu **Start.**
2. Zoek direct onder **taakplannerbibliotheek**de taak met de naam **Azure AD Sync Scheduler**, klik met de rechtermuisknop en selecteer **Inschakelen**.

## <a name="group-based-filtering"></a>Filteren op basis van groepen
U groepsfiltering configureren de eerste keer dat u Azure AD Connect installeert met behulp van [aangepaste installatie](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Het is bedoeld voor een pilot-implementatie waarbij u slechts een kleine set objecten wilt synchroniseren. Wanneer u groepsfiltering uitschakelt, kan deze niet opnieuw worden ingeschakeld. Het wordt *niet ondersteund* voor het gebruik van groepsfiltering in een aangepaste configuratie. Deze functie wordt alleen ondersteund om deze functie te configureren met behulp van de wizard Installatie. Wanneer u uw pilot hebt voltooid, gebruikt u een van de andere filteropties in dit onderwerp. Bij het gebruik van OU-gebaseerde filtering in combinatie met groepsfiltering, moet de OU(s) waar de groep en haar leden zich bevinden, worden opgenomen.

Wanneer u meerdere AD-forests synchroniseert, u groepsfiltering configureren door voor elke AD-connector een andere groep op te geven. Als u een gebruiker in een AD-forest wilt synchroniseren en dezelfde gebruiker een of meer overeenkomstige objecten in andere AD-forests heeft, moet u ervoor zorgen dat het gebruikersobject en alle bijbehorende objecten zich binnen het filterbereik van de groep bevinden. Voor voorbeelden:

* U hebt een gebruiker in een forest met een corresponderend FSP-object (Foreign Security Principal) in een ander forest. Beide objecten moeten zich binnen het filterbereik van groepen begeven. Anders wordt de gebruiker niet gesynchroniseerd met Azure AD.

* U hebt een gebruiker in een forest met een bijbehorend resourceaccount (bijvoorbeeld gekoppeld postvak) in een ander forest. Verder hebt u Azure AD Connect geconfigureerd om de gebruiker te koppelen aan het bronaccount. Beide objecten moeten zich binnen het filterbereik van groepen begeven. Anders wordt de gebruiker niet gesynchroniseerd met Azure AD.

* U hebt een gebruiker in een forest met een bijbehorende e-mailcontactpersoon in een ander forest. Verder hebt u Azure AD Connect geconfigureerd om de gebruiker te koppelen aan de e-mailcontactpersoon. Beide objecten moeten zich binnen het filterbereik van groepen begeven. Anders wordt de gebruiker niet gesynchroniseerd met Azure AD.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [azure AD Connect-synchronisatieconfiguratie.](how-to-connect-sync-whatis.md)
- Meer informatie over [het integreren van uw on-premises identiteiten met Azure AD](whatis-hybrid-identity.md).
