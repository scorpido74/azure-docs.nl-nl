---
title: Identiteitssynchronisatie en dubbele eigenschappentolerantie | Microsoft Documenten
description: Nieuw gedrag van het omgaan met objecten met UPN- of ProxyAddress-conflicten tijdens het synchroniseren van directorys met Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5585f0cd04dca4145f0322db9d625e35372b24b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298340"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Tolerantie voor synchronisatie- en duplicatiekenmerken identificeren
Dubbele tolerantie voor kenmerken is een functie in Azure Active Directory die frictie veroorzaakt door **UserPrincipalName-** en SMTP **ProxyAddress-conflicten** elimineert bij het uitvoeren van een van de synchronisatiehulpprogramma's van Microsoft.

Deze twee kenmerken zijn over het algemeen vereist om uniek te zijn voor alle **gebruikers-,** **groeps-** of **contactobjecten** in een bepaalde Azure Active Directory-tenant.

> [!NOTE]
> Alleen gebruikers kunnen UPN's hebben.
> 
> 

Het nieuwe gedrag dat deze functie mogelijk maakt, bevindt zich in het cloudgedeelte van de synchronisatiepijplijn, daarom is het clientagnostisch en relevant voor elk Microsoft-synchronisatieproduct, waaronder Azure AD Connect, DirSync en MIM + Connector. De algemene term "synchronisatieclient" wordt in dit document gebruikt om een van deze producten weer te geven.

## <a name="current-behavior"></a>Huidig gedrag
Als er wordt geprobeerd een nieuw object in te richten met een UPN- of ProxyAddress-waarde die deze uniciteitsbeperking schendt, blokkeert Azure Active Directory dat object dat object niet wordt gemaakt. Als een object wordt bijgewerkt met een niet-unieke UPN of ProxyAddress, mislukt de update. De inrichtingspoging of -update wordt opnieuw geprobeerd door de synchronisatieclient bij elke exportcyclus en blijft mislukken totdat het conflict is opgelost. Bij elke poging wordt een e-mail met foutrapport gegenereerd en wordt er een fout geregistreerd door de synchronisatieclient.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Gedrag met tolerantie voor dubbele kenmerken
In plaats van een object volledig niet in te richten of bij te werken met een duplicaatkenmerk, "quarantineeert" Azure Active Directory het dubbele kenmerk dat de uniciteitsbeperking zou schenden. Als dit kenmerk vereist is voor het inrichten, zoals UserPrincipalName, wijst de service een tijdelijke aanduidingtoe. De indeling van deze tijdelijke waarden is  
OriginalPrefix>_**+\<4DigitNumber \@ \<>InitialTenantDomain>.onmicrosoft.com . \<**_

Het tolerantieproces voor kenmerken verwerkt alleen UPN- en **SMTP-proxyadreswaarden.**

Als het kenmerk niet vereist is, zoals een **ProxyAddress,** wordt het conflictkenmerk in quarantaine geplaatst in Azure Active Directory en wordt verdergegaan met het maken of bijwerken van objecten.

Bij het quarantining van het kenmerk wordt informatie over het conflict verzonden in dezelfde e-mail met foutrapport die wordt gebruikt in het oude gedrag. Deze informatie wordt echter slechts één keer weergegeven in het foutrapport, wanneer de quarantaine plaatsvindt, wordt deze niet nog steeds geregistreerd in toekomstige e-mails. Aangezien de export voor dit object is geslaagd, registreert de synchronisatieclient ook geen fout en probeert hij de bewerking maken/bijwerken niet opnieuw bij volgende synchronisatiecycli.

Om dit gedrag te ondersteunen is een nieuw kenmerk toegevoegd aan de objectklassen Gebruiker, Groep en Contactpersoon:  
**DirSyncProvisioningFouten**

Dit is een kenmerk met meerdere waarden dat wordt gebruikt om de conflicterende kenmerken op te slaan die in strijd zijn met de uniciteitsbeperking als ze normaal worden toegevoegd. Er is een achtergrondtimertaak ingeschakeld in Azure Active Directory die elk uur wordt uitgevoerd om te zoeken naar dubbele kenmerkconflicten die zijn opgelost en waarbij de betreffende kenmerken automatisch uit quarantaine worden verwijderd.

### <a name="enabling-duplicate-attribute-resiliency"></a>Tolerantie voor dubbele kenmerken inschakelen
Tolerantie voor duplicaat kenmerken is het nieuwe standaardgedrag voor alle Azure Active Directory-tenants. Het is standaard ingeschakeld voor alle tenants die synchronisatie voor het eerst hebben ingeschakeld op 22 augustus 2016 of later. Tenants die voorafgaand aan deze datum synchronisatie hebben ingeschakeld, hebben de functie in batches ingeschakeld. Deze implementatie begint in september 2016 en er wordt een e-mailmelding verzonden naar het technische meldingscontact van elke tenant met de specifieke datum waarop de functie is ingeschakeld.

> [!NOTE]
> Zodra tolerantie voor duplicaatkenmerken is ingeschakeld, kan deze niet meer worden uitgeschakeld.

Als u wilt controleren of de functie is ingeschakeld voor uw tenant, u dit doen door de nieuwste versie van de Azure Active Directory PowerShell-module te downloaden en het uitvoeren van:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> U de cmdlet Set-MsolDirSyncFeature niet langer gebruiken om proactief de functie Tolerantie voor duplicaat kenmerken in te schakelen voordat deze is ingeschakeld voor uw tenant. Als u de functie wilt testen, moet u een nieuwe Azure Active Directory-tenant maken.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Objecten met DirSyncProvisioningErrors identificeren
Er zijn momenteel twee methoden om objecten te identificeren die deze fouten hebben als gevolg van dubbele eigenschapsconflicten, Azure Active Directory PowerShell en het [Microsoft 365-beheercentrum](https://admin.microsoft.com). Er zijn plannen om in de toekomst uit te breiden naar aanvullende portalgebaseerde rapportage.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Voor de PowerShell-cmdlets in dit onderwerp geldt het volgende:

* Alle volgende cmdlets zijn hoofdlettergevoelig.
* De **eigenschap -ErrorCategoryConflict** moet altijd worden opgenomen. Er zijn momenteel geen andere soorten **errorcategory,** maar dit kan in de toekomst worden uitgebreid.

Ga eerst aan de slag door **Connect-MsolService** uit te voeren en referenties in te voeren voor een tenantbeheerder.

Gebruik vervolgens de volgende cmdlets en operatoren om fouten op verschillende manieren weer te geven:

1. [Alles bekijken](#see-all)
2. [Op eigenschapstype](#by-property-type)
3. [Door conflicterende waarde](#by-conflicting-value)
4. [Een tekenreekszoekopdracht gebruiken](#using-a-string-search)
5. Gesorteerd
6. [In een beperkte hoeveelheid of alles](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Alles weergeven
Als u eenmaal bent verbonden, ziet u een algemene lijst met fouten in het inrichten van kenmerken in de tenantrun:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Dit levert een resultaat op als volgt:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Op eigenschapstype
Als u fouten wilt zien op eigenschapstype, voegt u de vlag **-PropertyName** toe met het argument **UserPrincipalName** of **ProxyAddresses:**

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

of

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Door conflicterende waarde
Als u fouten wilt zien met betrekking tot een specifieke eigenschap, moet ook de vlag **-PropertyValue** (**-PropertyName** worden gebruikt bij het toevoegen van deze vlag):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Een tekenreekszoekopdracht gebruiken
Als u een brede tekenreekszoekopdracht wilt doen, gebruikt u de vlag **-SearchString.** Dit kan onafhankelijk van alle bovenstaande vlaggen worden gebruikt, met uitzondering van **-ErrorCategory PropertyConflict**, wat altijd vereist is:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>In een beperkte hoeveelheid of alle
1. **MaxResults \<Int>** kan worden gebruikt om de query te beperken tot een specifiek aantal waarden.
2. **Alles** kan worden gebruikt om ervoor te zorgen dat alle resultaten worden opgehaald in het geval dat er een groot aantal fouten bestaat.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Microsoft 365-beheercentrum
U adreslijstsynchronisatiefouten weergeven in het Microsoft 365-beheercentrum. In het rapport in het Microsoft 365-beheercentrum worden alleen **gebruikersobjecten** weergegeven die deze fouten bevatten. Er wordt geen informatie weergegeven over conflicten tussen **groepen** en **contactpersonen.**

![Actieve gebruikers](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "Actieve gebruikers")

Zie [Adreslijstsynchronisatiefouten in Office 365 identificeren](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)voor instructies voor het weergeven van adresfouten in het Microsoft 365-beheercentrum.

### <a name="identity-synchronization-error-report"></a>Foutrapport identiteitssynchronisatie
Wanneer een object met een duplicaatconflict met dit nieuwe gedrag wordt verwerkt, wordt een melding opgenomen in de standaard e-mail met het adres van het adres identiteitssynchronisatie, dat wordt verzonden naar de contactpersoon voor de technische melding voor de tenant. Er is echter een belangrijke verandering in dit gedrag. In het verleden zou informatie over een duplicaat-kenmerkconflict worden opgenomen in elk volgend foutrapport totdat het conflict is opgelost. Met dit nieuwe gedrag wordt de foutmelding voor een bepaald conflict slechts één keer weergegeven, op het moment dat het conflicterende kenmerk in quarantaine wordt geplaatst.

Hier volgt een voorbeeld van hoe de e-mailmelding eruit ziet voor een ProxyAddress-conflict:  
    ![Actieve gebruikers](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Actieve gebruikers")  

## <a name="resolving-conflicts"></a>Conflicten oplossen
Probleemoplossingsstrategie en oplossingstactieken voor deze fouten mogen niet afwijken van de manier waarop dubbele attribuutfouten in het verleden zijn verwerkt. Het enige verschil is dat de timertaak door de tenant aan de servicezijde veegt om het betreffende kenmerk automatisch toe te voegen aan het juiste object zodra het conflict is opgelost.

In het volgende artikel worden verschillende probleemoplossings- en oplossingsstrategieën beschreven: [Dubbele of ongeldige kenmerken voorkomen adressynchronisatie in Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Bekende problemen
Geen van deze bekende problemen veroorzaakt gegevensverlies of servicedegradatie. Een aantal van hen zijn esthetisch, anderen veroorzaken standaard "*pre-veerkracht*" dubbele attribuut fouten worden gegooid in plaats van quarantining het conflict attribuut, en een ander veroorzaakt bepaalde fouten om extra handmatige fix-up vereisen.

**Kerngedrag:**

1. Objecten met specifieke kenmerkconfiguraties blijven exportfouten ontvangen, in tegenstelling tot het dubbele kenmerk(en) dat in quarantaine wordt geplaatst.  
   Bijvoorbeeld:
   
    a. Nieuwe gebruiker wordt gemaakt in AD met een UPN van **Joe\@contoso.com** en ProxyAddress **smtp:\@Joe contoso.com**
   
    b. De eigenschappen van dit object conflicteren met een bestaande groep, waarbij ProxyAddress **SMTP\@is:Joe contoso.com**.
   
    c. Bij export wordt een **proxyadresconflictfout** gegenereerd in plaats van de conflictkenmerken in quarantaine te plaatsen. De bewerking wordt opnieuw geprobeerd bij elke volgende synchronisatiecyclus, zoals het zou zijn geweest voordat de tolerantiefunctie was ingeschakeld.
2. Als twee groepen on-premises worden gemaakt met hetzelfde SMTP-adres, kan één niet voorzien in de eerste poging met een standaarddubbele **ProxyAddress-fout.** De dubbele waarde is echter goed in quarantaine geplaatst bij de volgende synchronisatiecyclus.

**Office Portal-rapport**:

1. Het gedetailleerde foutbericht voor twee objecten in een UPN-conflictset is hetzelfde. Dit geeft aan dat ze allebei hun UPN veranderd / in quarantaine hebben geplaatst, terwijl in feite slechts een van hen had geen gegevens veranderd.
2. Het gedetailleerde foutbericht voor een UPN-conflict toont de verkeerde displayName voor een gebruiker die zijn UPN heeft gewijzigd/in quarantaine heeft geplaatst. Bijvoorbeeld:
   
    a. **Gebruiker A** synchroniseert eerst met **UPN\@= User contoso.com**.
   
    b. **Gebruiker B** wordt geprobeerd om vervolgens te worden gesynchroniseerd met **UPN\@= User contoso.com**.
   
    c. **Gebruiker B's** UPN wordt gewijzigd in **User1234\@contoso.onmicrosoft.com** en **Gebruiker\@contoso.com** wordt toegevoegd aan **DirSyncProvisioningErrors**.
   
    d. Het foutbericht voor **gebruiker B** moet aangeven dat **gebruiker A** al **gebruikerscontoso.com\@** als UPN heeft, maar het toont de eigen displayName van gebruiker **B.**

**Foutrapport voor identiteitssynchronisatie:**

De koppeling voor *stappen om dit probleem op te lossen* is onjuist:  
    ![Actieve gebruikers](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Actieve gebruikers")  

Het zou [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency)moeten wijzen op.

## <a name="see-also"></a>Zie ook
* [Synchronisatie van Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
* [Adreslijstsynchronisatiefouten identificeren in Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

