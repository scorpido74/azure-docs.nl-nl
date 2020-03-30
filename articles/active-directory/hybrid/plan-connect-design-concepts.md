---
title: 'Azure AD Connect: ontwerpconcepten | Microsoft Documenten'
description: In dit onderwerp worden bepaalde implementatieontwerpgebieden beschreven
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb41e14a7ecf41a2698a063c3067a98d8acf8f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253882"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: ontwerpconcepten
Het doel van dit document is om gebieden te beschrijven die moeten worden overwogen tijdens het implementatieontwerp van Azure AD Connect. Dit document is een diepe duik op bepaalde gebieden en deze concepten worden kort beschreven in andere documenten ook.

## <a name="sourceanchor"></a>sourceAnchor bronAnker
Het kenmerk sourceAnchor wordt gedefinieerd als *een attribuut onveranderlijk tijdens de levensduur van een object*. Het identificeert een object op unieke wijze als hetzelfde object on-premises en in Azure AD. Het attribuut wordt ook wel **onveranderlijkId** genoemd en de twee namen worden uitwisselbaar gebruikt.

Het woord onveranderlijk, dat is "kan niet worden gewijzigd", is belangrijk voor dit document. Aangezien de waarde van dit kenmerk niet kan worden gewijzigd nadat het is ingesteld, is het belangrijk om een ontwerp te kiezen dat uw scenario ondersteunt.

Het kenmerk wordt gebruikt voor de volgende scenario's:

* Wanneer een nieuwe synchronisatieengineserver is gebouwd of opnieuw wordt opgebouwd na een scenario voor noodherstel, koppelt dit kenmerk bestaande objecten in Azure AD aan on-premises objecten.
* Als u van een cloud-only identiteit naar een gesynchroniseerd identiteitsmodel gaat, kunnen objecten met dit kenmerk bestaande objecten in Azure AD 'hard matchen' met on-premises objecten.
* Als u federatie gebruikt, wordt dit kenmerk samen met de **userPrincipalName** gebruikt in de claim om een gebruiker op unieke wijze te identificeren.

Dit onderwerp praat alleen over sourceAnchor als het gaat om gebruikers. Dezelfde regels gelden voor alle objecttypen, maar het is alleen voor gebruikers dit probleem is meestal een punt van zorg.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Een goed sourceAnchor-kenmerk selecteren
De kenmerkwaarde moet de volgende regels volgen:

* Minder dan 60 tekens in lengte
  * Tekens die niet a-z, A-Z of 0-9 zijn, worden gecodeerd en geteld als 3 tekens
* Geen speciaal karakter bevatten: &#92; ! # $ & * + = ? ^ &#96; { } | ~ < >  . : , [ ] " \@ _
* Moet wereldwijd uniek zijn
* Moet een tekenreeks, geheel getal of binair zijn
* Moet niet gebaseerd zijn op de naam van de gebruiker, omdat deze kunnen veranderen
* Mag niet hoofdlettergevoelig zijn en waarden vermijden die per geval kunnen verschillen
* Moet worden toegewezen wanneer het object wordt gemaakt

Als de geselecteerde bronAnchor niet van typetekenreeks is, codeert Azure AD Connect Base64Code de kenmerkwaarde om ervoor te zorgen dat er geen speciale tekens worden weergegeven. Als u een andere federatieserver dan ADFS gebruikt, moet u ervoor zorgen dat uw server het kenmerk ook kan base64coderen.

Het kenmerk sourceAnchor is hoofdlettergevoelig. Een waarde van "JohnDoe" is niet hetzelfde als "johndoe". Maar je moet niet twee verschillende objecten met alleen een verschil in het geval.

Als u één forest on-premises hebt, is het kenmerk dat u moet gebruiken **objectGUID**. Dit is ook het kenmerk dat wordt gebruikt wanneer u expresinstellingen gebruikt in Azure AD Connect en ook het kenmerk dat door DirSync wordt gebruikt.

Als u meerdere forests hebt en gebruikers niet tussen forests en domeinen verplaatst, is **objectGUID** een goed kenmerk om zelfs in dit geval te gebruiken.

Als u gebruikers verplaatst tussen forests en domeinen, moet u een kenmerk vinden dat niet verandert of tijdens de verhuizing met de gebruikers kan worden verplaatst. Een aanbevolen aanpak is het introduceren van een synthetisch attribuut. Een attribuut dat iets kan bevatten dat op een GUID lijkt, zou geschikt zijn. Tijdens het maken van objecten wordt een nieuwe GUID gemaakt en gestempeld op de gebruiker. Er kan een aangepaste synchronisatieregel worden gemaakt in de synchronisatieengineserver om deze waarde te maken op basis van de **objectGUID** en het geselecteerde kenmerk bij te werken in VOEGT. Wanneer u het object verplaatst, moet u ook de inhoud van deze waarde kopiëren.

Een andere oplossing is om een bestaand kenmerk te kiezen dat niet verandert. Veelgebruikte kenmerken zijn **employeeID**. Als u een kenmerk overweegt dat letters bevat, moet u ervoor zorgen dat de aanvraag (hoofdletter vs. kleine letters) niet kan worden gewijzigd voor de waarde van het kenmerk. Slechte kenmerken die niet mogen worden gebruikt, bevatten deze kenmerken met de naam van de gebruiker. In een huwelijk of echtscheiding, de naam wordt verwacht te veranderen, die niet is toegestaan voor dit attribuut. Dit is ook een van de redenen waarom kenmerken zoals **userPrincipalName,** **e-mail**en **targetAddress** niet eens mogelijk zijn om te selecteren in de installatiewizard Azure AD Connect. Deze attributen bevatten ook\@het " karakter" dat niet is toegestaan in de sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Het kenmerk sourceAnchor wijzigen
De eigenschapswaarde sourceAnchor kan niet worden gewijzigd nadat het object is gemaakt in Azure AD en de identiteit is gesynchroniseerd.

Daarom gelden de volgende beperkingen voor Azure AD Connect:

* Het kenmerk sourceAnchor kan alleen worden ingesteld tijdens de eerste installatie. Als u de wizard Installatie opnieuw uitvoert, is deze optie alleen-lezen. Als u deze instelling moet wijzigen, moet u deze instelling verwijderen en opnieuw installeren.
* Als u een andere Azure AD Connect-server installeert, moet u hetzelfde kenmerk sourceAnchor selecteren als eerder gebruikt. Als u dirsync eerder hebt gebruikt en naar Azure AD Connect bent gegaan, moet u **objectGUID** gebruiken, omdat dat het kenmerk is dat door DirSync wordt gebruikt.
* Als de waarde voor sourceAnchor wordt gewijzigd nadat het object is geëxporteerd naar Azure AD, wordt er een fout gegenereerd met azure AD Connect-synchronisatie en worden er geen wijzigingen meer in dat object toegestaan voordat het probleem is opgelost en de bronAnchor is teruggezet in de bron Directory.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Ms-DS-ConsistencyGuid gebruiken als sourceAnchor
Azure AD Connect (versie 1.1.486.0 en ouder) gebruikt standaard objectGUID als kenmerk sourceAnchor. ObjectGUID is systeemgegenereerd. U de waarde ervan niet opgeven bij het maken van on-premises AD-objecten. Zoals uitgelegd in sectie [sourceAnchor](#sourceanchor), zijn er scenario's waarin u de waarde sourceAnchor moet opgeven. Als de scenario's op u van toepassing zijn, moet u een configureerbaar AD-kenmerk (bijvoorbeeld ms-DS-ConsistencyGuid) gebruiken als kenmerk sourceAnchor.

Azure AD Connect (versie 1.1.524.0 en daarna) vergemakkelijkt nu het gebruik van ms-DS-ConsistencyGuid als sourceAnchor-kenmerk. Wanneer u deze functie gebruikt, configureert Azure AD Connect automatisch de synchronisatieregels om:

1. Ms-DS-ConsistencyGuid gebruiken als het kenmerk sourceAnchor voor gebruikersobjecten. ObjectGUID wordt gebruikt voor andere objecttypen.

2. Voor een bepaald on-premises AD-gebruikersobject waarvan het kenmerk ms-DS-ConsistencyGuid niet is ingevuld, schrijft Azure AD Connect de objectGUID-waarde terug naar het kenmerk ms-DS-ConsistencyGuid in on-premises Active Directory. Nadat het kenmerk ms-DS-ConsistencyGuid is ingevuld, exporteert Azure AD Connect het object naar Azure AD.

>[!NOTE]
> Zodra een on-premises AD-object is geïmporteerd in Azure AD Connect (dat wil zeggen geïmporteerd in de AD-connectorruimte en geprojecteerd in de metaverse), u de bronankerwaarde niet meer wijzigen. Als u de bronAnkerwaarde voor een bepaald on-premises AD-object wilt opgeven, configureert u het kenmerk ms-DS-ConsistencyGuid voordat het wordt geïmporteerd in Azure AD Connect.

### <a name="permission-required"></a>Vereiste machtiging
Om deze functie te laten werken, moet het AD DS-account dat wordt gebruikt om te synchroniseren met on-premises Active Directory, schrijftoestemming krijgen voor het kenmerk ms-DS-ConsistencyGuid in on-premises Active Directory.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>De functie ConsistencyGuid inschakelen - Nieuwe installatie
U het gebruik van ConsistencyGuid inschakelen als sourceAnchor tijdens een nieuwe installatie. Deze sectie behandelt zowel Express als Aangepaste installatie in details.

  >[!NOTE]
  > Alleen nieuwere versies van Azure AD Connect (1.1.524.0 en daarna) ondersteunen het gebruik van ConsistencyGuid als sourceAnchor tijdens de nieuwe installatie.

### <a name="how-to-enable-the-consistencyguid-feature"></a>De functie ConsistencyGuid inschakelen

#### <a name="express-installation"></a>Express-installatie
Bij het installeren van de Azure AD Connect-modus bepaalt de wizard Azure AD Connect automatisch het meest geschikte AD-kenmerk dat als bronankerkenmerk moet worden gebruikt met behulp van de volgende logica:

* Eerst stelt de wizard Azure AD Connect uw Azure AD-tenant op om het AD-kenmerk op te halen dat wordt gebruikt als het kenmerk sourceAnchor in de vorige Azure AD Connect-installatie (indien van toepassing). Als deze informatie beschikbaar is, gebruikt Azure AD Connect hetzelfde AD-kenmerk.

  >[!NOTE]
  > Alleen nieuwere versies van Azure AD Connect (1.1.524.0 en daarna) slaan informatie op in uw Azure AD-tenant over het kenmerk sourceAnchor dat tijdens de installatie wordt gebruikt. Oudere versies van Azure AD Connect niet.

* Als er geen informatie beschikbaar is over het gebruikte kenmerk sourceAnchor, controleert de wizard de status van het kenmerk ms-DS-ConsistencyGuid in uw on-premises Active Directory. Als het kenmerk niet is geconfigureerd op een object in de map, gebruikt de wizard de ms-DS-ConsistencyGuid als het kenmerk sourceAnchor. Als het kenmerk is geconfigureerd op een of meer objecten in de map, concludeert de wizard dat het kenmerk wordt gebruikt door andere toepassingen en niet geschikt is als bronAnkerkenmerk...

* In dat geval valt de wizard terug naar het gebruik van objectGUID als het kenmerk sourceAnchor.

* Zodra het kenmerk sourceAnchor is bepaald, slaat de wizard de gegevens op in uw Azure AD-tenant. De informatie wordt gebruikt bij de toekomstige installatie van Azure AD Connect.

Zodra de Express-installatie is voltooid, informeert de wizard u welk kenmerk is gekozen als het kenmerk Bronanker.

![Wizard informeert AD-kenmerk dat is gekozen voor sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Aangepaste installatie
Bij het installeren van Azure AD Connect met de aangepaste modus biedt de wizard Azure AD Connect twee opties bij het configureren van het kenmerk sourceAnchor:

![Aangepaste installatie - sourceAnchor-configuratie](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Instelling | Beschrijving |
| --- | --- |
| Azure het bronanker voor mij laten beheren | Selecteer deze optie als u wilt dat Azure AD het kenmerk voor u selecteert. Als u deze optie selecteert, past de wizard Azure AD Connect dezelfde [bronAnchor-kenmerkselectielogica](#express-installation)toe die wordt gebruikt tijdens de installatie van Express . Net als bij de installatie Express informeert de wizard u welk kenmerk is gekozen als kenmerk Bronanker nadat de aangepaste installatie is voltooid. |
| Een specifiek kenmerk | Selecteer deze optie als u een bestaand AD-kenmerk opgeeft als het kenmerk sourceAnchor. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>De functie ConsistencyGuid inschakelen - Bestaande implementatie
Als u een bestaande Azure AD Connect-implementatie hebt die objectGUID als kenmerk Bronanker gebruikt, u deze in plaats daarvan overschakelen naar consistencyguid.

>[!NOTE]
> Alleen nieuwere versies van Azure AD Connect (1.1.552.0 en daarna) ondersteunen het overschakelen van ObjectGuid naar ConsistencyGuid als kenmerk Bronanker.

Ga als gevolg van objectGUID naar ConsistencyGuid als kenmerk Bronanker:

1. Start de wizard Azure AD Connect en klik op **Configureren** om naar het scherm Taken te gaan.

2. Selecteer de **taakoptie Bronanker configureren** en klik op **Volgende**.

   ![ConsistencyGuid inschakelen voor bestaande implementatie - stap 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Voer de referenties van uw Azure AD Administrator in en klik op **Volgende**.

4. De wizard Azure AD Connect analyseert de status van het kenmerk ms-DS-ConsistencyGuid in uw on-premises Active Directory. Als het kenmerk niet is geconfigureerd op een object in de map, concludeert Azure AD Connect dat geen enkele andere toepassing momenteel het kenmerk gebruikt en veilig is om het te gebruiken als kenmerk Bronanker. Klik op **Volgende** om verder te gaan.

   ![ConsistencyGuid inschakelen voor bestaande implementatie - stap 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. Klik in het scherm **Klaar om te configureren** op **Configureren** om de configuratie wijziging aan te brengen.

   ![ConsistencyGuid inschakelen voor bestaande implementatie - stap 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. Zodra de configuratie is voltooid, geeft de wizard aan dat ms-DS-ConsistencyGuid nu wordt gebruikt als kenmerk Bronanker.

   ![ConsistencyGuid inschakelen voor bestaande implementatie - stap 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Als het kenmerk tijdens de analyse (stap 4) is geconfigureerd op een of meer objecten in de map, concludeert de wizard dat het kenmerk door een andere toepassing wordt gebruikt en wordt een fout geretourneerd zoals in het onderstaande diagram wordt weergegeven. Deze fout kan ook optreden als u eerder de functie ConsistencyGuid op uw primaire Azure AD Connect-server hebt ingeschakeld en u hetzelfde probeert te doen op uw staging-server.

![ConsistencyGuid inschakelen voor bestaande implementatie - fout](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Als u zeker weet dat het kenmerk niet wordt gebruikt door andere bestaande toepassingen, u de fout onderdrukken door de wizard Azure AD Connect opnieuw te starten met de opgegeven **/SkipLdapSearch-switch.** Voer hiervoor de volgende opdracht opdrachtprompt uit:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Gevolgen voor AD FS of federatieconfiguratie van derden
Als u Azure AD Connect gebruikt om on-premises AD FS-implementatie te beheren, werkt Azure AD Connect de claimregels automatisch bij om hetzelfde AD-kenmerk als sourceAnchor te gebruiken. Dit zorgt ervoor dat de door ADFS gegenereerde immutableID-claim consistent is met de sourceAnchor-waarden die naar Azure AD worden geëxporteerd.

Als u AD FS buiten Azure AD Connect beheert of als u federatieservers van derden gebruikt voor verificatie, moet u de claimregels voor onveranderlijke ID-claim handmatig bijwerken om consistent te zijn met de bronAnkerwaarden die naar Azure AD worden geëxporteerd zoals beschreven in artikelsectie [Ad FS-claimregels wijzigen.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims) De wizard retourneert de volgende waarschuwing nadat de installatie is voltooid:

![Federatieconfiguratie van derden](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Nieuwe mappen toevoegen aan bestaande implementatie
Stel dat u Azure AD Connect hebt geïmplementeerd met de functie ConsistencyGuid ingeschakeld en nu wilt u een andere map aan de implementatie toevoegen. Wanneer u de map probeert toe te voegen, controleert de wizard Azure AD Connect de status van het kenmerk ms-DS-ConsistencyGuid in de map. Als het kenmerk is geconfigureerd op een of meer objecten in de map, concludeert de wizard dat het kenmerk wordt gebruikt door andere toepassingen en een fout retourneert zoals geïllustreerd in het onderstaande diagram. Als u zeker weet dat het kenmerk niet wordt gebruikt door bestaande toepassingen, u de fout onderdrukken door de wizard Azure AD Connect opnieuw te starten met de **/SkipLdapSearch-switch** die is opgegeven zoals hierboven beschreven of moet u contact opnemen met ondersteuning voor meer informatie.

![Nieuwe mappen toevoegen aan bestaande implementatie](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Azure AD-aanmelding
Hoewel u uw on-premises directory integreert met Azure AD, is het belangrijk om te begrijpen hoe de synchronisatie-instellingen van invloed kunnen zijn op de manier waarop gebruikers zich verifieert. Azure AD gebruikt userPrincipalName (UPN) om de gebruiker te verifiëren. Wanneer u uw gebruikers echter synchroniseert, moet u het kenmerk kiezen dat zorgvuldig moet worden gebruikt voor de waarde van userPrincipalName.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Het kenmerk voor userPrincipalName kiezen
Wanneer u het kenmerk selecteert voor het opgeven van de waarde van UPN die in Azure moet worden gebruikt, moet u ervoor zorgen dat

* De kenmerkwaarden voldoen aan de syntaxis VAN UPN (RFC 822), dat wil zeggen dat het domein van de indelinggebruikersnaam\@moet zijn
* Het achtervoegsel in de waarden komt overeen met een van de geverifieerde aangepaste domeinen in Azure AD

In expresinstellingen is de veronderstelde keuze voor het kenmerk userPrincipalName. Als het kenmerk userPrincipalName niet de waarde bevat die uw gebruikers moeten aanmelden bij Azure, moet u **Aangepaste installatie**kiezen.

### <a name="custom-domain-state-and-upn"></a>Aangepaste domeinstatus en UPN
Het is belangrijk om ervoor te zorgen dat er een geverifieerd domein is voor het UPN-achtervoegsel.

John is een gebruiker in contoso.com. U wilt dat John de on-premises UPN john\@contoso.com gebruikt om zich aan te melden bij Azure nadat u gebruikers hebt gesynchroniseerd met uw Azure AD-map contoso.onmicrosoft.com. Om dit te doen, moet u contoso.com toevoegen en verifiëren als een aangepast domein in Azure AD voordat u beginnen met het synchroniseren van de gebruikers. Als het UPN-achtervoegsel van John, bijvoorbeeld contoso.com, niet overeenkomt met een geverifieerd domein in Azure AD, vervangt Azure AD het UPN-achtervoegsel door contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Niet-routeerbare on-premises domeinen en UPN voor Azure AD
Sommige organisaties hebben niet-routable domeinen, zoals contoso.local, of eenvoudige single label domeinen zoals contoso. U een niet-routeerbaar domein in Azure AD niet verifiëren. Azure AD Connect kan alleen worden gesynchroniseerd met een geverifieerd domein in Azure AD. Wanneer u een Azure AD-map maakt, wordt hiermee een routable-domein gemaakt dat standaarddomein wordt voor bijvoorbeeld uw Azure AD contoso.onmicrosoft.com. Daarom wordt het noodzakelijk om een ander routable domein in een dergelijk scenario te verifiëren voor het geval u niet wilt synchroniseren met het standaardonmicrosoft.com domein.

Lees [Uw aangepaste domeinnaam toevoegen aan Azure Active Directory](../active-directory-domains-add-azure-portal.md) voor meer informatie over het toevoegen en verifiëren van domeinen.

Azure AD Connect detecteert of u actief bent in een niet-routeerbare domeinomgeving en waarschuwt u op de juiste manier om door te gaan met expresinstellingen. Als u actief bent in een niet-routable domein, dan is het waarschijnlijk dat de UPN, van de gebruikers, hebben niet-routeerbare achtervoegsels ook. Als u bijvoorbeeld onder contoso.local wordt uitgevoerd, stelt Azure AD Connect voor aangepaste instellingen te gebruiken in plaats van expresinstellingen te gebruiken. Met behulp van aangepaste instellingen u het kenmerk opgeven dat moet worden gebruikt als UPN om u aan te melden bij Azure nadat de gebruikers zijn gesynchroniseerd met Azure AD.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
