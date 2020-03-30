---
title: Aanvullende scenario's voor licenties op basis van groepen - Azure AD | Microsoft Documenten
description: Meer scenario's voor Azure Active Directory-groepslicenties
services: active-directory
keywords: Azure AD-licenties
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139d7e0cf2b57cc466dc97370b90a599257ce755
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266284"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scenario's, beperkingen en bekende problemen met groepen om licenties te beheren in Azure Active Directory

Gebruik de volgende informatie en voorbeelden om meer inzicht te krijgen in azure active directory-licenties op basis van azure active directory (Azure AD).

## <a name="usage-location"></a>Gebruikslocatie

Sommige services van Microsoft zijn niet op alle locaties beschikbaar. Voordat een licentie aan een gebruiker kan worden toegewezen, moet de beheerder de eigenschap **Gebruikslocatie** op de gebruiker opgeven. In [de Azure-portal](https://portal.azure.com)u de gebruikslocatie opgeven in **gebruikersprofielinstellingen** &gt; **Settings** **User** &gt; .

Voor groepslicentietoewijzing erven gebruikers zonder opgegeven gebruikslocatie de locatie van de map. Als u gebruikers op meerdere locaties hebt, moet u dat correct weergeven in uw gebruikersbronnen voordat u gebruikers toevoegt aan groepen met licenties.

> [!NOTE]
> Groepslicentietoewijzing wijzigt nooit de waarde van een bestaande gebruikslocatie voor een gebruiker. We raden u aan altijd de gebruikslocatie in te stellen als onderdeel van uw gebruikersstroom in Azure AD (bijvoorbeeld via AAD Connect-configuratie) - zodat het resultaat van licentietoewijzing altijd correct is en gebruikers geen services ontvangen op locaties die niet zijn toegestaan.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Groepslicenties gebruiken met dynamische groepen

U groepslicenties gebruiken voor elke beveiligingsgroep, wat betekent dat deze kan worden gecombineerd met azure AD-dynamische groepen. Dynamische groepen voeren regels uit tegen gebruikersbronnen om gebruikers automatisch toe te voegen en te verwijderen uit groepen.

U bijvoorbeeld een dynamische groep maken voor een aantal producten die u aan gebruikers wilt toewijzen. Elke groep wordt bevolkt door een regel die gebruikers toevoegt aan hun kenmerken en aan elke groep wordt de licenties toegewezen die ze moeten ontvangen. U het kenmerk on-premises toewijzen en synchroniseren met Azure AD, of u het kenmerk rechtstreeks in de cloud beheren.

Licenties worden toegewezen aan de gebruiker kort nadat ze zijn toegevoegd aan de groep. Wanneer het kenmerk wordt gewijzigd, verlaat de gebruiker de groepen en worden de licenties verwijderd.

### <a name="example"></a>Voorbeeld

Neem het voorbeeld van een on-premises oplossing voor identiteitsbeheer die bepaalt welke gebruikers toegang moeten hebben tot Microsoft-webservices. Het maakt gebruik **van extensieAttribute1** om een tekenreekswaarde op te slaan die de licenties vertegenwoordigt die de gebruiker moet hebben. Azure AD Connect synchroniseert het met Azure AD.

Gebruikers hebben mogelijk een licentie nodig, maar niet een andere, of hebben beide mogelijk nodig. Hier is een voorbeeld, waarin u Office 365 Enterprise E5- en Enterprise Mobility + Security (EMS)-licenties distribueert naar gebruikers in groepen:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: basisservices

![Schermafbeelding van basisservices van Office 365 Enterprise E5](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Beveiliging: gebruikers met een licentie

![Schermafbeelding van gebruikers met enterprisemobiliteit + beveiliging met licentie](./media/licensing-group-advanced/o365-e5-licensed-users.png)

Wijzig in dit voorbeeld één gebruiker en stel de `EMS;E5_baseservices;` extensieAttribute1 in op de waarde van als u wilt dat de gebruiker beide licenties heeft. U deze wijziging ter plaatse doen. Nadat de wijziging is gesynchroniseerd met de cloud, wordt de gebruiker automatisch aan beide groepen toegevoegd en worden licenties toegewezen.

![Schermafbeelding van het instellen van de extensieAttribute1 van de gebruiker](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Wees voorzichtig bij het wijzigen van de lidmaatschapsregel van een bestaande groep. Wanneer een regel wordt gewijzigd, wordt het lidmaatschap van de groep opnieuw geëvalueerd en worden gebruikers die niet meer aan de nieuwe regel voldoen verwijderd (gebruikers die nog steeds overeenkomen met de nieuwe regel worden tijdens dit proces niet beïnvloed). Deze gebruikers zullen hun licenties laten verwijderen tijdens het proces, wat kan leiden tot verlies van service of in sommige gevallen tot verlies van gegevens.
> 
> Als u een grote dynamische groep hebt waarop u afhankelijk bent voor licentietoewijzing, u overwegen belangrijke wijzigingen in een kleinere testgroep te valideren voordat u deze toepast op de hoofdgroep.

## <a name="multiple-groups-and-multiple-licenses"></a>Meerdere groepen en meerdere licenties

Een gebruiker kan lid zijn van meerdere groepen met licenties. Hier zijn een aantal dingen om te overwegen:

- Meerdere licenties voor hetzelfde product kunnen elkaar overlappen en resulteren in alle ingeschakelde services die op de gebruiker worden toegepast. In het volgende voorbeeld ziet u twee licentiegroepen: *E3-basisservices* bevatten de basisservices die eerst moeten worden geïmplementeerd, voor alle gebruikers. En *E3 extended services* bevat extra services (Sway en Planner) om alleen voor sommige gebruikers te implementeren. In dit voorbeeld is de gebruiker aan beide groepen toegevoegd:

  ![Schermafbeelding van ingeschakelde services](./media/licensing-group-advanced/view-enabled-services.png)

  Als gevolg hiervan heeft de gebruiker 7 van de 12 services in het product ingeschakeld, terwijl slechts één licentie voor dit product wordt gebruikt.

- Als u de *E3-licentie* selecteert, worden meer details weergegeven, waaronder informatie over welke services voor de gebruiker zijn ingeschakeld door de groepslicentietoewijzing.

## <a name="direct-licenses-coexist-with-group-licenses"></a>Directe licenties bestaan naast groepslicenties

Wanneer een gebruiker een licentie overneemt van een groep, u die licentietoewijzing niet rechtstreeks verwijderen of wijzigen in de eigenschappen van de gebruiker. Wijzigingen moeten worden aangebracht in de groep en vervolgens worden doorgegeven aan alle gebruikers.

Het is echter mogelijk om dezelfde productlicentie rechtstreeks aan de gebruiker toe te wijzen, naast de overgenomen licentie. U aanvullende services van het product alleen voor één gebruiker inschakelen, zonder dat dit gevolgen heeft voor andere gebruikers.

Direct toegewezen licenties kunnen worden verwijderd en hebben geen invloed op overgenomen licenties. Houd rekening met de gebruiker die een Office 365 Enterprise E3-licentie overneemt van een groep.

In eerste instantie neemt de gebruiker de licentie alleen over van de *E3-basisservicesgroep,* waardoor vier serviceplannen mogelijk zijn.

1. Selecteer **Toewijzen** om rechtstreeks een E3-licentie aan de gebruiker toe te wijzen. In dit geval schakelt u alle serviceplannen uit, behalve Yammer Enterprise.

    Als gevolg hiervan gebruikt de gebruiker nog steeds slechts één licentie van het E3-product. Met de directe toewijzing kan de Yammer Enterprise-service alleen voor die gebruiker worden uitgevoerd. U zien welke services zijn ingeschakeld door het groepslidmaatschap versus de directe toewijzing.

1. Wanneer u directe toewijzing gebruikt, zijn de volgende bewerkingen toegestaan:

   - Yammer Enterprise kan rechtstreeks worden uitgeschakeld op de gebruikersbron. De **aan/uit-schakelaar** in de illustratie is ingeschakeld voor deze service, in tegenstelling tot de andere servicewissels. Omdat de service rechtstreeks op de gebruiker is ingeschakeld, kan deze worden gewijzigd.
   - Aanvullende services kunnen ook worden ingeschakeld, als onderdeel van de direct toegewezen licentie.
   - De knop **Verwijderen** kan worden gebruikt om de directe licentie van de gebruiker te verwijderen. U zien dat de gebruiker nu alleen de overgenomen groepslicentie heeft en dat alleen de oorspronkelijke services zijn ingeschakeld:

## <a name="managing-new-services-added-to-products"></a>Nieuwe services beheren die aan producten zijn toegevoegd

Wanneer Microsoft een nieuwe service toevoegt aan een productlicentieplan, is deze standaard ingeschakeld in alle groepen waaraan u de productlicentie hebt toegewezen. Gebruikers in uw tenant die zijn geabonneerd op meldingen over productwijzigingen, ontvangen van tevoren e-mails waarin ze worden gewaarschuwd voor de komende servicetoevoegingen.

Als beheerder u alle groepen die door de wijziging zijn getroffen controleren en actie ondernemen, zoals het uitschakelen van de nieuwe service in elke groep. Als u bijvoorbeeld groepen hebt gemaakt die alleen specifieke services voor implementatie targeten, u deze groepen opnieuw bekijken en ervoor zorgen dat nieuw toegevoegde services zijn uitgeschakeld.

Hier is een voorbeeld van hoe dit proces eruit kan zien:

1. Oorspronkelijk hebt u het *Office 365 Enterprise E5-product* toegewezen aan verschillende groepen. Een van die groepen, genaamd *O365 E5 - Exchange* alleen is ontworpen om alleen de *Exchange Online (Plan 2)* service voor haar leden in te schakelen.

2. U hebt van Microsoft een melding ontvangen dat het E5-product wordt uitgebreid met een nieuwe service - *Microsoft Stream*. Wanneer de service beschikbaar komt in uw tenant, u het volgende doen:

3. Ga naar het [**Azure Active Directory > Licenties > Alle producten**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) blade en selecteer Office *365 Enterprise E5*en selecteer **Vervolgens Gelicentieerde groepen** om een lijst met alle groepen met dat product weer te geven.

4. Klik op de groep die u wilt bekijken (in dit geval *alleen O365 E5 - Exchange).* Hiermee wordt het tabblad **Licenties** geopend. Als u op de E5-licentie klikt, wordt een blade geopend met alle ingeschakelde services.
   > [!NOTE]
   > De *Microsoft Stream-service* is automatisch toegevoegd en ingeschakeld in deze groep, naast de *Exchange Online-service:*

   ![Schermafbeelding van nieuwe service die is toegevoegd aan een groepslicentie](./media/licensing-group-advanced/manage-new-services.png)

5. Als u de nieuwe service in deze groep wilt uitschakelen, klikt u op de **aan/uit-schakelaar** naast de service en klikt u op de knop **Opslaan** om de wijziging te bevestigen. Azure AD verwerkt nu alle gebruikers in de groep om de wijziging toe te passen. nieuwe gebruikers die aan de groep zijn toegevoegd, hebben de *Microsoft Stream-service* niet ingeschakeld.

   > [!NOTE]
   > Gebruikers kunnen de service nog steeds hebben ingeschakeld via een andere licentietoewijzing (een andere groep waarvan ze lid zijn of een directe licentietoewijzing).

6. Voer indien nodig dezelfde stappen uit voor andere groepen waarvoor dit product is toegewezen.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>PowerShell gebruiken om te zien wie licenties heeft geërfd en rechtstreeks heeft geleid
U een PowerShell-script gebruiken om te controleren of gebruikers een licentie hebben toegewezen die rechtstreeks of overgenomen zijn van een groep.

1. Voer `connect-msolservice` de cmdlet uit om te verifiëren en verbinding te maken met uw tenant.

2. `Get-MsolAccountSku`kan worden gebruikt om alle ingerichte productlicenties in de tenant te ontdekken.

   ![Schermafbeelding van de cmdlet Get-Msolaccountsku](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Gebruik de *AccountSkuId-waarde* voor de licentie waarin u geïnteresseerd bent met [dit PowerShell-script.](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group) Dit levert een lijst op van gebruikers die deze licentie hebben met de informatie over hoe de licentie is toegewezen.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Controlelogboeken gebruiken om groepsgebaseerde licentieactiviteiten te controleren

U [Azure AD-controlelogboeken](../reports-monitoring/concept-audit-logs.md#audit-logs) gebruiken om alle activiteiten te bekijken die verband houden met groepslicenties, waaronder:
- die licenties op groepen heeft gewijzigd
- wanneer het systeem begon met het verwerken van een groepslicentiewijziging en wanneer het is voltooid
- welke licentiewijzigingen zijn aangebracht aan een gebruiker als gevolg van een groepslicentietoewijzing.

>[!NOTE]
> Controlelogboeken zijn beschikbaar op de meeste blades in het Azure Active Directory-gedeelte van de portal. Afhankelijk van waar u ze hebt geopend, kunnen filters vooraf worden toegepast om alleen activiteit weer te geven die relevant is voor de context van het blad. Als u de verwachte resultaten niet ziet, onderzoekt u [de filteropties](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) of krijgt u toegang tot de ongefilterde controlelogboeken onder [**Azure Active Directory > Activity > Audit-logboeken**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Zoek uit wie een groepslicentie heeft gewijzigd

1. Stel het **filter Activiteit** in *op Groepslicentie instellen* en klik op **Toepassen**.
2. De resultaten omvatten alle gevallen van licenties worden ingesteld of gewijzigd op groepen.
   >[!TIP]
   > U ook de naam van de groep in *het* doelfilter typen om de resultaten te bereiken.

3. Selecteer een item in de lijst om de details te zien van wat er is gewijzigd. Onder *Gewijzigde eigenschappen* worden zowel oude als nieuwe waarden voor de licentietoewijzing weergegeven.

Hier is een voorbeeld van recente groepslicentiewijzigingen, met details:

![Wijzigingen in groepslicenties voor schermafbeeldingen](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Ontdek wanneer groepswijzigingen zijn gestart en voltooid met de verwerking

Wanneer een licentie in een groep verandert, gaat Azure AD de wijzigingen toepassen op alle gebruikers.

1. Als u wilt zien wanneer groepen zijn verwerkt, stelt u het **filter Activiteit** in op Het toepassen *van groepsgebaseerde licentie op gebruikers*. Houd er rekening mee dat de actor voor de bewerking *Microsoft Azure AD Group-based Licensing* is , een systeemaccount dat wordt gebruikt om alle groepslicentiewijzigingen uit te voeren.
   >[!TIP]
   > Klik op een item in de lijst om het veld *Gewijzigde eigenschappen* te bekijken - het toont de licentiewijzigingen die zijn opgehaald voor verwerking. Dit is handig als u meerdere wijzigingen in een groep hebt aangebracht en u niet zeker weet welke groep is verwerkt.

2. Als u wilt zien wanneer groepen de verwerking hebben voltooid, gebruikt u de filterwaarde *Voltooien van groepsgebaseerde licentie op gebruikers.*
   > [!TIP]
   > In dit geval bevat het veld *Gewijzigde eigenschappen* een overzicht van de resultaten - dit is handig om snel te controleren of verwerking heeft geleid tot fouten. Voorbeelduitvoer:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Als u het volledige logboek wilt bekijken voor de manier waarop een groep is verwerkt, inclusief alle wijzigingen van de gebruiker, stelt u de volgende filters in:
   - **Gestart door (Actor):**"Microsoft Azure AD-groepsgebaseerde licenties"
   - **Datumbereik** (optioneel): aangepast bereik voor wanneer u weet dat een specifieke groep is gestart en voltooid

Deze voorbeelduitvoer toont het begin van de verwerking, alle resulterende wijzigingen van de gebruiker en de afwerking van de verwerking.

![Wijzigingen in groepslicenties voor schermafbeeldingen](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Als u op items met betrekking tot *De gebruikerslicentie wijzigen* klikt, worden details weergegeven voor licentiewijzigingen die op elke afzonderlijke gebruiker worden toegepast.

## <a name="deleting-a-group-with-an-assigned-license"></a>Een groep met een toegewezen licentie verwijderen

Het is niet mogelijk om een groep te verwijderen met een actieve licentie toegewezen. Een beheerder kan een groep verwijderen die zich niet realiseert dat hiermee licenties van gebruikers worden verwijderd - daarom vereisen we dat licenties eerst uit de groep worden verwijderd voordat deze kan worden verwijderd.

Wanneer u een groep in de Azure-portal probeert te ![verwijderen, ziet u mogelijk een foutmelding als volgt: verwijdering van de groep Schermafbeelding is mislukt](./media/licensing-group-advanced/groupdeletionfailed.png)

Ga naar het tabblad **Licenties** in de groep en kijk of er licenties zijn toegewezen. Zo ja, verwijder deze licenties en probeer de groep opnieuw te verwijderen.

Mogelijk ziet u vergelijkbare fouten wanneer u de groep probeert te verwijderen via PowerShell of Graph API. Als u een groep gebruikt die is gesynchroniseerd vanuit on-premises, kan Azure AD Connect ook fouten melden als deze de groep niet in Azure AD verwijdert. Controleer in al deze gevallen of er licenties zijn toegewezen aan de groep en verwijder deze eerst.

## <a name="limitations-and-known-issues"></a>Beperkingen en bekende problemen

Als u groepslicenties gebruikt, is het een goed idee om vertrouwd te raken met de volgende lijst met beperkingen en bekende problemen.

- Groepslicenties bieden op dit moment geen ondersteuning voor groepen die andere groepen bevatten (geneste groepen). Als u een licentie toepast op een geneste groep, wordt die licentie alleen toegepast op de gebruikers die lid direct zijn van de groep.

- De functie kan alleen worden gebruikt met beveiligingsgroepen en Office 365-groepen met securityEnabled=TRUE.

- Het [Microsoft 365-beheercentrum](https://admin.microsoft.com) biedt momenteel geen ondersteuning voor groepslicenties. Als een gebruiker een licentie overneemt van een groep, wordt deze licentie in de Office-beheerportal weergegeven als een reguliere gebruikerslicentie. Als u probeert die licentie te wijzigen of probeert de licentie te verwijderen, retourneert de portal een foutbericht. Overgenomen groepslicenties kunnen niet rechtstreeks op een gebruiker worden gewijzigd.

- Wanneer licenties worden toegewezen of gewijzigd voor een grote groep (bijvoorbeeld 100.000 gebruikers), kan dit van invloed zijn op de prestaties. Met name het volume van de wijzigingen die worden gegenereerd door Azure AD-automatisering kan een negatieve invloed hebben op de prestaties van uw adreslijstsynchronisatie tussen Azure AD en on-premises systemen.

- Als u gebruikmaakt van dynamische groepen om het lidmaatschap van de gebruiker te beheren, controleert u of de gebruiker deel uitmaakt van de groep. Dit is noodzakelijk voor het toewijzen van licenties. Als dit niet het geval is, [controleert u de verwerkingsstatus voor de lidmaatschapsregel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) van de dynamische groep.

- In bepaalde situaties met hoge belasting kan het lang duren voordat licentiewijzigingen voor groepen of lidmaatschapswijzigingen worden verwerkt in groepen met bestaande licenties. Als het meer dan 24 uur duurt voordat uw wijzigingen zijn doorgevoerd om de groepsgrootte van 60.000 gebruikers of minder te verwerken, opent u [een ondersteuningsticket](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) om ons in staat te stellen dit te onderzoeken. 

- Automatisering van licentiebeheer reageert niet automatisch op alle soorten wijzigingen in de omgeving. U hebt bijvoorbeeld geen licenties meer, waardoor sommige gebruikers in een foutstatus verkeren. Als u het beschikbare aantal zitplaatsen wilt vrijmaken, u een aantal direct toegewezen licenties van andere gebruikers verwijderen. Het systeem reageert echter niet automatisch op deze wijziging en lost gebruikers in die foutstatus op.

  Als tijdelijke oplossing voor dit soort beperkingen u naar het **groepsblad** in Azure AD gaan en op **Opnieuw verwerken klikken.** Deze opdracht verwerkt alle gebruikers in die groep en lost de foutstatussen op, indien mogelijk.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over scenario’s voor licentiebeheer via licenties op basis van groepen, raadpleegt u:

* [Wat is groepsgebaseerde licenties in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Licenties toewijzen aan een groep in Azure Active Directory](licensing-groups-assign.md)
* [Licentieproblemen voor een groep vaststellen en oplossen in Azure Active Directory](licensing-groups-resolve-problems.md)
* [Gebruikers met een afzonderlijke licentie migreren naar licenties op basis van groepen in Azure Active Directory](licensing-groups-migrate-users.md)
* [Gebruikers migreren tussen productlicenties met groepslicenties in Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [PowerShell-voorbeelden voor groepslicenties in Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
