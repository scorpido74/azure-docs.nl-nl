---
title: 'Azure AD Connect: fouten oplossen tijdens synchronisatie | Microsoft Documenten'
description: Hier wordt uitgelegd hoe u fouten oplossen die zijn opgetreden tijdens synchronisatie met Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 745ddcc95bb91e61478307265aec1ac8a7ebba54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609193"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Problemen oplossen tijdens synchronisatie
Er kunnen fouten optreden wanneer identiteitsgegevens worden gesynchroniseerd van Windows Server Active Directory (AD DS) naar Azure Active Directory (Azure AD). In dit artikel vindt u een overzicht van verschillende typen synchronisatiefouten, enkele van de mogelijke scenario's die deze fouten veroorzaken en mogelijke manieren om de fouten op te lossen. Dit artikel bevat de algemene fouttypen en dekt mogelijk niet alle mogelijke fouten.

 In dit artikel wordt ervan uitgegaan dat de lezer bekend is met de onderliggende [ontwerpconcepten van Azure AD en Azure AD Connect.](plan-connect-design-concepts.md)

Met de nieuwste versie \(van Azure AD Connect\)augustus 2016 of hoger is een rapport van synchronisatiefouten beschikbaar in de [Azure-portal](https://aka.ms/aadconnecthealth) als onderdeel van Azure AD Connect-status voor synchronisatie.

Vanaf 1 september 2016 wordt de functie tolerantie tolerantie voor [tolerantie voor dubbele kenmerken](how-to-connect-syncservice-duplicate-attribute-resiliency.md) van Azure Directory standaard ingeschakeld voor alle *nieuwe* Azure Active Directory-tenants. Deze functie wordt de komende maanden automatisch ingeschakeld voor bestaande tenants.

Azure AD Connect voert drie typen bewerkingen uit vanuit de mappen die het synchroon houdt: Importeren, synchroniseren en exporteren. Fouten kunnen plaatsvinden in alle bewerkingen. Dit artikel richt zich voornamelijk op fouten tijdens exporteren naar Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Fouten tijdens exporteren naar Azure AD
Volgende sectie beschrijft verschillende typen synchronisatiefouten die kunnen optreden tijdens de exportbewerking naar Azure AD met behulp van de Azure AD-connector. Deze connector kan worden geïdentificeerd door de naam formaat wordt "contoso. *onmicrosoft.com*".
Fouten tijdens exporteren naar Azure \(AD geven aan dat\) de bewerking wordt \(toegevoegd, bijgewerkt, verwijderd, enz.\)

![Overzicht van exportfouten](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Fouten bij gegevensmismatch
### <a name="invalidsoftmatch"></a>OngeldigSoftMatch
#### <a name="description"></a>Beschrijving
* Wanneer Azure \(AD\) Connect-synchronisatieengine Azure Active Directory instrueert om objecten toe te voegen of bij te werken, koppelt Azure AD het binnenkomende object aan het **kenmerk sourceAnchor** aan het **kenmerk ununbaar id** van objecten in Azure AD. Deze wedstrijd wordt een **Hard Match**genoemd.
* Wanneer Azure AD geen object **vindt** dat overeenkomt met het **kenmerk UntableId** met het kenmerk **sourceAnchor** van het inkomende object, valt het object terug om de kenmerken ProxyAddresses en UserPrincipalName te gebruiken om een overeenkomst te vinden. Deze match wordt een **Soft Match**genoemd. De Soft Match is ontworpen om objecten die al aanwezig zijn in Azure AD (die afkomstig zijn uit Azure AD) te matchen met de nieuwe objecten die worden toegevoegd/bijgewerkt tijdens synchronisatie die dezelfde entiteit (gebruikers, groepen) op locatie vertegenwoordigen.
* **Ongeldige SoftMatch-fout** treedt op wanneer de harde overeenkomst geen overeenkomend object vindt **en** soft match een overeenkomend object vindt, maar dat object een andere waarde van *onveranderlijke Id* heeft dan de *SourceAnchor*van het binnenkomende object, wat suggereert dat het overeenkomende object is gesynchroniseerd met een ander object van on premises Active Directory.

Met andere woorden, om de zachte match te laten werken, mag het object waarmee het zacht moet worden afgestemd, geen waarde hebben voor de *onveranderlijke id.* Als een object met *een onveranderlijke id-set* met een waarde niet voldoet aan de hard-match, maar voldoet aan de soft-match criteria, zou de bewerking resulteren in een OngeldigeSoftMatch synchronisatiefout.

Azure Active Directory-schema staat niet toe dat twee of meer objecten dezelfde waarde hebben als de volgende kenmerken. \(Dit is geen uitputtende lijst.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> De functie Tolerantie voor dubbele kenmerken van [Azure AD-kenmerk](how-to-connect-syncservice-duplicate-attribute-resiliency.md) wordt ook uitgerold als het standaardgedrag van Azure Active Directory.  Dit vermindert het aantal synchronisatiefouten dat wordt gezien door Azure AD Connect (evenals andere synchronisatieclients) door Azure AD veerkrachtiger te maken in de manier waarop het dubbele ProxyAddresses en UserPrincipalName-kenmerken verwerkt die aanwezig zijn in on-premises AD-omgevingen. Deze functie lost de duplicatiefouten niet op. Dus de gegevens moeten nog worden vastgesteld. Maar het maakt het mogelijk om nieuwe objecten te inrichten die anders worden geblokkeerd vanwege gedupliceerde waarden in Azure AD. Dit vermindert ook het aantal synchronisatiefouten dat wordt geretourneerd naar de synchronisatieclient.
> Als deze functie is ingeschakeld voor uw tenant, ziet u de ongeldige synchronisatiefouten van SoftMatch niet tijdens het inrichten van nieuwe objecten.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Voorbeeldscenario's voor InvalidSoftMatch
1. Twee of meer objecten met dezelfde waarde voor het kenmerk ProxyAddresses bestaan in on-premises Active Directory. Slechts één wordt ingericht in Azure AD.
2. Twee of meer objecten met dezelfde waarde voor het kenmerk UserPrincipalName staan in on-premises Active Directory. Slechts één wordt ingericht in Azure AD.
3. Er is een object toegevoegd in de On-premises Active Directory met dezelfde waarde van het kenmerk ProxyAddresses als dat van een bestaand object in Azure Active Directory. Het object dat on-premises is toegevoegd, wordt niet ingericht in Azure Active Directory.
4. Er is een object toegevoegd aan On premises Active Directory met dezelfde waarde van het kenmerk UserPrincipalName als dat van een account in Azure Active Directory. Het object wordt niet ingericht in Azure Active Directory.
5. Een gesynchroniseerd account is verplaatst van Forest A naar Forest B. Azure AD Connect (synchronisatie-engine) gebruikte objectGUId-kenmerk om het SourceAnchor te berekenen. Na de bosverplaatsing is de waarde van het SourceAnchor anders. Het nieuwe object (van Forest B) synchroniseert niet met het bestaande object in Azure AD.
6. Een gesynchroniseerd object is per ongeluk verwijderd uit Active Directory en er is een nieuw object gemaakt in Active Directory voor dezelfde entiteit (zoals gebruiker) zonder het account in Azure Active Directory te verwijderen. Het nieuwe account kan niet worden gesynchroniseerd met het bestaande Azure AD-object.
7. Azure AD Connect is verwijderd en opnieuw geïnstalleerd. Tijdens de herinstallatie werd een ander attribuut gekozen als SourceAnchor. Alle objecten die eerder gesynchroniseerd waren, zijn gestopt met synchroniseren met de fout OngeldigeSoftMatch.

#### <a name="example-case"></a>Voorbeeldvoorbeeld:
1. **Bob Smith** is een gesynchroniseerde gebruiker in Azure Active Directory van on-premises Active Directory of *contoso.com*
2. De **UserPrincipalName** van Bob Smith is ingesteld als **\@bobs contoso.com**.
3. **"abcdefghijklmnopqrstuv=="** is het **SourceAnchor** berekend door Azure AD Connect met behulp van Bob Smith's **objectGUID** van on-premises Active Directory, de **onveranderlijke Id** voor Bob Smith in Azure Active Directory.
4. Bob heeft ook volgende waarden voor het kenmerk **proxyAdressen:**
   * Smtp:bobs@contoso.com
   * Smtp:bob.smith@contoso.com
   * **smtp:\@bob contoso.com**
5. Een nieuwe gebruiker, **Bob Taylor,** wordt toegevoegd aan de on-premises Active Directory.
6. De **UserPrincipalName** van Bob Taylor is ingesteld als **\@bobt contoso.com**.
7. **"abcdefghijkl0123456789==""** is de **sourceAnchor** berekend door Azure AD Connect met behulp van Bob Taylor's **objectGUID** van on-premises Active Directory. Het object van Bob Taylor is nog niet gesynchroniseerd met Azure Active Directory.
8. Bob Taylor heeft de volgende waarden voor het kenmerk proxyAddresses
   * Smtp:bobt@contoso.com
   * Smtp:bob.taylor@contoso.com
   * **smtp:\@bob contoso.com**
9. Tijdens het synchroniseren herkent Azure AD Connect de toevoeging van Bob Taylor in on-premises Active Directory en vraagt Azure AD om dezelfde wijziging aan te brengen.
10. Azure AD zal eerst harde overeenkomst uitvoeren. Dat wil zeggen, het zal zoeken of er een object met de onveranderlijkeId gelijk aan "abcdefghijkl0123456789==". Hard Match mislukt omdat geen enkel ander object in Azure AD die onveranderlijke Id heeft.
11. Azure AD zal dan proberen om soft-match Bob Taylor. Dat wil zeggen, zal het zoeken als er een object met proxyAdressen gelijk aan de drie waarden, met inbegrip van smtp:bob@contoso.com
12. Azure AD vindt het object van Bob Smith dat voldoet aan de criteria voor soft-match. Maar dit object heeft de waarde van onveranderlijkEId = "abcdefghijhijnopqrstuv==". die aangeeft dat dit object is gesynchroniseerd vanuit een ander object van on-premises Active Directory. Azure AD kan deze objecten dus niet soft-matchen en resulteert in een **ongeldige SoftMatch-synchronisatiefout.**

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Ongeldige SoftMatch-fout oplossen
De meest voorkomende reden voor de fout Van InvalidSoftMatch zijn twee objecten met verschillende SourceAnchor-onveranderlijke \(id\) hebben dezelfde waarde voor de kenmerken ProxyAddresses en/of UserPrincipalName, die worden gebruikt tijdens het soft-match-proces op Azure AD. Om de ongeldige soft match te repareren

1. Identificeer de dubbele proxyAdressen, userPrincipalName of een andere kenmerkwaarde die de fout veroorzaakt. Identificeer ook \(welke\) twee of meer objecten betrokken zijn bij het conflict. Met het rapport dat is gegenereerd door [Azure AD Connect Health for sync,](https://aka.ms/aadchsyncerrors) u de twee objecten identificeren.
2. Bepaal welk object de gedupliceerde waarde moet blijven hebben en welk object dat niet mag.
3. Verwijder de gedupliceerde waarde uit het object dat niet die waarde zou moeten hebben. U moet de wijziging aanbrengen in de map waar het object vandaan komt. In sommige gevallen moet u mogelijk een van de objecten in conflict verwijderen.
4. Als u de wijziging hebt aangebracht in het ad op locatie, laat u Azure AD Connect de wijziging synchroniseren.

Synchronisatiefoutrapporten binnen Azure AD Connect Health voor synchronisatie worden elke 30 minuten bijgewerkt en bevatten de fouten van de laatste synchronisatiepoging.

> [!NOTE]
> Onveranderlijke id, per definitie, mag niet veranderen in de levensduur van het object. Als Azure AD Connect niet is geconfigureerd met een aantal scenario's in gedachten uit de bovenstaande lijst, u in een situatie terechtkomen waarin Azure AD Connect een andere waarde berekent van het SourceAnchor voor het AD-object dat dezelfde entiteit vertegenwoordigt (dezelfde gebruiker/groep/contactpersoon, enz.) die een bestaand Azure AD-object heeft dat u wilt blijven gebruiken.
>
>

#### <a name="related-articles"></a>Verwante artikelen
* [Dubbele of ongeldige kenmerken voorkomen adreslijstsynchronisatie in Office 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch ObjectTypeMismatch
#### <a name="description"></a>Beschrijving
Wanneer Azure AD probeert twee objecten te softmatchen, is het mogelijk dat twee objecten van verschillende 'objecttype' (zoals Gebruiker, Groep, Contactpersoon, enz.) dezelfde waarden hebben voor de kenmerken die worden gebruikt om de zachte overeenkomst uit te voeren. Omdat duplicatie van deze kenmerken niet is toegestaan in Azure AD, kan de bewerking resulteren in synchronisatiefout 'ObjectTypeMismatch'.

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Voorbeeldscenario's voor objecttypemismatch-fout
* Er wordt een beveiligingsgroep met e-mail gemaakt in Office 365. Beheerder voegt een nieuwe gebruiker of contactpersoon toe in on-premises AD (dat nog niet is gesynchroniseerd met Azure AD) met dezelfde waarde voor het kenmerk ProxyAddresses als die van de Office 365-groep.

#### <a name="example-case"></a>Voorbeeldvoorbeeld
1. Beheerder maakt een nieuwe beveiligingsgroep met e-mail in Office 365 tax@contoso.comvoor de belastingdienst en geeft een e-mailadres als . Aan deze groep wordt de eigenschapwaarde ProxyAddresses van **smtp toegewezen: contoso.com\@**
2. Een nieuwe gebruiker wordt lid Contoso.com en een account wordt gemaakt voor de gebruiker on premises met de proxyAddress als **smtp:\@belasting contoso.com**
3. Wanneer Azure AD Connect het nieuwe gebruikersaccount synchroniseert, wordt de fout ObjectTypeMismatch weergegeven.

#### <a name="how-to-fix-objecttypemismatch-error"></a>Fout objecttypemismatch oplossen
De meest voorkomende reden voor de objecttypemismatch-fout zijn twee objecten van verschillend type (Gebruiker, Groep, Contactpersoon, enz.) hebben dezelfde waarde voor het kenmerk ProxyAddresses. Om de ObjectTypeMismatch op te lossen:

1. Identificeer de dubbele proxyAdressen (of een ander kenmerk) dat de fout veroorzaakt. Identificeer ook \(welke\) twee of meer objecten betrokken zijn bij het conflict. Met het rapport dat is gegenereerd door [Azure AD Connect Health for sync,](https://aka.ms/aadchsyncerrors) u de twee objecten identificeren.
2. Bepaal welk object de gedupliceerde waarde moet blijven hebben en welk object dat niet mag.
3. Verwijder de gedupliceerde waarde uit het object dat niet die waarde zou moeten hebben. Houd er rekening mee dat u de wijziging moet aanbrengen in de map waar het object vandaan komt. In sommige gevallen moet u mogelijk een van de objecten in conflict verwijderen.
4. Als u de wijziging hebt aangebracht in het ad op locatie, laat u Azure AD Connect de wijziging synchroniseren. Het foutrapport synchroniseren in Azure AD Connect Health for sync wordt elke 30 minuten bijgewerkt en bevat de fouten van de laatste synchronisatiepoging.

## <a name="duplicate-attributes"></a>Kenmerken dupliceren
### <a name="attributevaluemustbeunique"></a>KenmerkwaardeMustBeUnique
#### <a name="description"></a>Beschrijving
Azure Active Directory-schema staat niet toe dat twee of meer objecten dezelfde waarde hebben als de volgende kenmerken. Dat is elk object in Azure AD wordt gedwongen om een unieke waarde van deze kenmerken hebben op een bepaalde instantie.

* ProxyAddresses
* UserPrincipalName

Als Azure AD Connect probeert een nieuw object toe te voegen of een bestaand object bij te werken met een waarde voor de bovenstaande kenmerken die al is toegewezen aan een ander object in Azure Active Directory, resulteert de bewerking in de synchronisatiefout 'AttributeValueMustMustBeUnique'.

#### <a name="possible-scenarios"></a>Mogelijke scenario's:
1. Dubbele waarde wordt toegewezen aan een al gesynchroniseerd object, dat in strijd is met een ander gesynchroniseerd object.

#### <a name="example-case"></a>Voorbeeldvoorbeeld:
1. **Bob Smith** is een gesynchroniseerde gebruiker in Azure Active Directory van on-premises Active Directory of contoso.com
2. **De UserPrincipalName** on-premises van Bob Smith is ingesteld als **\@bobs contoso.com**.
3. Bob heeft ook volgende waarden voor het kenmerk **proxyAdressen:**
   * Smtp:bobs@contoso.com
   * Smtp:bob.smith@contoso.com
   * **smtp:\@bob contoso.com**
4. Een nieuwe gebruiker, **Bob Taylor,** wordt toegevoegd aan de on-premises Active Directory.
5. De **UserPrincipalName** van Bob Taylor is ingesteld als **\@bobt contoso.com**.
6. **Bob Taylor** heeft de volgende waarden voor het kenmerk **ProxyAddresses** i. smtp: bobt@contoso.com ii. Smtp:bob.taylor@contoso.com
7. Het object van Bob Taylor is gesynchroniseerd met Azure AD.
8. Admin besloten om Bob Taylor's **ProxyAddresses** attribuut bijwerken met de volgende waarde: i. **smtp:\@bob contoso.com**
9. Azure AD probeert het object van Bob Taylor in Azure AD bij te werken met de bovenstaande waarde, maar die bewerking mislukt omdat de proxyadressen-waarde al is toegewezen aan Bob Smith, wat resulteert in de fout 'AttributeValueMustBeUnique'.

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Hoe fixer en de fout AttributeValueMustBeUnique
De meest voorkomende reden voor de fout AttributeValueMustBeUnieke \(is\) twee objecten met verschillende SourceAnchor-onveranderlijke id hebben dezelfde waarde voor de kenmerken ProxyAddresses en/of UserPrincipalName. Om de fout AttributeValueMustBeUnique op te lossen

1. Identificeer de gedupliceerde proxyAdressen, userPrincipalName of andere kenmerkwaarde die de fout veroorzaakt. Identificeer ook \(welke\) twee of meer objecten betrokken zijn bij het conflict. Met het rapport dat is gegenereerd door [Azure AD Connect Health for sync,](https://aka.ms/aadchsyncerrors) u de twee objecten identificeren.
2. Bepaal welk object de gedupliceerde waarde moet blijven hebben en welk object dat niet mag.
3. Verwijder de gedupliceerde waarde uit het object dat niet die waarde zou moeten hebben. Houd er rekening mee dat u de wijziging moet aanbrengen in de map waar het object vandaan komt. In sommige gevallen moet u mogelijk een van de objecten in conflict verwijderen.
4. Als u de wijziging hebt aangebracht in het ad op locatie, laat U Azure AD Connect de wijziging synchroniseren om de fout te laten oplossen.

#### <a name="related-articles"></a>Verwante artikelen
-[Dubbele of ongeldige kenmerken voorkomen adreslijstsynchronisatie in Office 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Fouten in gegevensvalidatie
### <a name="identitydatavalidationfailed"></a>IdentityDataValidation Mislukt
#### <a name="description"></a>Beschrijving
Azure Active Directory handhaaft verschillende beperkingen op de gegevens zelf voordat die gegevens in de map kunnen worden geschreven. Deze beperkingen moeten ervoor zorgen dat eindgebruikers de best mogelijke ervaringen krijgen tijdens het gebruik van de toepassingen die afhankelijk zijn van deze gegevens.

#### <a name="scenarios"></a>Scenario's
a. De kenmerkwaarde UserPrincipalName heeft ongeldige/niet-ondersteunde tekens.
b. Het kenmerk UserPrincipalName volgt niet de vereiste indeling.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Hoe oplossen IdentityDataValidationMislukte fout
a. Controleer of het kenmerk userPrincipalName ondersteunde tekens en vereiste indeling heeft.

#### <a name="related-articles"></a>Verwante artikelen
* [Voorbereiden op het inrichten van gebruikers via adreslijstsynchronisatie naar Office 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Beschrijving
Deze case resulteert in een synchronisatiefout **'FederatedDomainChangeError'** wanneer het achtervoegsel van de UserPrincipalName van een gebruiker wordt gewijzigd van het ene federatieve domein naar een ander federatief domein.

#### <a name="scenarios"></a>Scenario's
Voor een gesynchroniseerde gebruiker is het userprincipalname-achtervoegsel gewijzigd van het ene federatieve domein naar een ander federatief domein op locatie. *UserPrincipalName\@= bob contoso.com* is bijvoorbeeld gewijzigd in *UserPrincipalName = bob\@fabrikam.com*.

#### <a name="example"></a>Voorbeeld
1. Bob Smith, een account voor Contoso.com, wordt toegevoegd als nieuwe gebruiker in Active Directory met de UserPrincipalNamebob@contoso.com
2. Bob gaat naar een andere divisie van Contoso.com genaamd Fabrikam.com en hun UserPrincipalName wordt gewijzigd inbob@fabrikam.com
3. Zowel contoso.com- als fabrikam.com domeinen zijn federatieve domeinen met Azure Active Directory.
4. Bob's userPrincipalName wordt niet bijgewerkt en resulteert in een synchronisatiefout 'FederatedDomainChangeError'.

#### <a name="how-to-fix"></a>Hoe oplossen?
Als het UserPrincipalName-achtervoegsel van een gebruiker\@is bijgewerkt van bob@**contoso.com** naar bob**fabrikam.com**, waar zowel **contoso.com** als **fabrikam.com** **federatieve domeinen**zijn, volg dan deze stappen om de synchronisatiefout op te lossen

1. Update de UserPrincipalName van de bob@contoso.com gebruiker bob@contoso.onmicrosoft.comin Azure AD van naar . U de volgende PowerShell-opdracht gebruiken met de Azure AD PowerShell-module:`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Laat de volgende synchronisatiecyclus proberen te synchroniseren. Deze keer is synchronisatie succesvol en wordt de UserPrincipalName bob@fabrikam.com van Bob bijgewerkt naar zoals verwacht.

#### <a name="related-articles"></a>Verwante artikelen
* [Wijzigingen worden niet gesynchroniseerd met het Azure Active Directory Sync-hulpprogramma nadat u de UPN van een gebruikersaccount hebt gewijzigd om een ander federatief domein te gebruiken](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>GrootObject
### <a name="description"></a>Beschrijving
Wanneer een kenmerk de toegestane groottelimiet, lengtelimiet of tellimiet overschrijdt die is ingesteld door het Azure Active Directory-schema, resulteert de synchronisatiebewerking in de synchronisatiefout **LargeObject** of **ExceededAllowedLength.** Deze fout treedt meestal op voor de volgende kenmerken

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Mogelijke scenario's
1. Het kenmerk userCertificate van Bob slaat te veel certificaten op die aan Bob zijn toegewezen. Deze kunnen oudere, verlopen certificaten omvatten. De harde limiet is 15 certificaten. Raadpleeg artikel Omgaan met [LargeObject-fouten als gevolg van het kenmerk UserCertificate](tshoot-connect-largeobjecterror-usercertificate.md)voor meer informatie over het verwerken van LargeObject-fouten.
2. Het kenmerk van Bob's userSMIMECertificate slaat te veel certificaten op die aan Bob zijn toegewezen. Deze kunnen oudere, verlopen certificaten omvatten. De harde limiet is 15 certificaten.
3. Bob's thumbnailPhoto-set in Active Directory is te groot om te worden gesynchroniseerd in Azure AD.
4. Tijdens de automatische populatie van het kenmerk ProxyAddresses in Active Directory heeft een object te veel proxyadressen toegewezen.

### <a name="how-to-fix"></a>Hoe oplossen?
1. Controleer of het kenmerk dat de fout veroorzaakt binnen de toegestane beperking valt.

## <a name="existing-admin-role-conflict"></a>Bestaand conflict over beheerdersrollen

### <a name="description"></a>Beschrijving
Een **bestaand conflict met beheerdersrollen** treedt op een gebruikersobject op tijdens synchronisatie wanneer dat object:

- administratieve machtigingen en
- dezelfde UserPrincipalName als een bestaand Azure AD-object

Azure AD Connect is niet toegestaan om een gebruikersobject van on-premises AD te koppelen aan een gebruikersobject in Azure AD waaraan een beheerrol is toegewezen.  Zie Azure [AD UserPrincipalName-populatie](plan-connect-userprincipalname.md) voor meer informatie

![Bestaande beheerder](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Hoe oplossen?
Ga als volgt te werk om dit probleem op te lossen:

1. Verwijder het Azure AD-account (eigenaar) uit alle beheerdersrollen. 
2. **Hard Verwijder** het in quarantaine geplaatste object in de cloud. 
3. De volgende synchronisatiecyclus zorgt ervoor dat de on-premises gebruiker wordt afgestemd op het cloudaccount (aangezien de cloudgebruiker nu geen globale GA meer is). 
4. Herstel de rollidmaatschappen voor de eigenaar. 

>[!NOTE]
>U de beheerrol opnieuw toewijzen aan het bestaande gebruikersobject nadat de zachte overeenkomst tussen het on-premises gebruikersobject en het Azure AD-gebruikersobject is voltooid.

## <a name="related-links"></a>Verwante koppelingen
* [Active Directory-objecten zoeken in Active Directory-beheercentrum](https://technet.microsoft.com/library/dd560661.aspx)
* [Azure Active Directory voor een object opvragen met Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
