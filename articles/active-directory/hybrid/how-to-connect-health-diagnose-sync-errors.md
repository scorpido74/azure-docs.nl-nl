---
title: Azure AD Connect-status - Dubbele synchronisatiefouten van kenmerken diagnosticeren | Microsoft Documenten
description: In dit document wordt het diagnoseproces van dubbele synchronisatiefouten van kenmerken beschreven en een potentiële oplossing van de scenario's voor verweesde objecten rechtstreeks vanuit de Azure-portal.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48ed9abf3e088e2581a3dd81b7c89e6b99da3ceb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897191"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnose and remediate duplicated attribute sync errors (Synchronisatiefouten door dubbel kenmerk analyseren en herstellen)

## <a name="overview"></a>Overzicht
Azure Active Directory (Azure AD) Connect Health, die een stap verder gaat om synchronisatiefouten te markeren, introduceert selfserviceherstel. Het lost dubbele synchronisatiefouten op en lost objecten op die afkomstig zijn uit Azure AD.
De diagnose functie heeft deze voordelen:
- Het biedt een diagnostische procedure die dubbele fouten bij het synchroniseren van kenmerken beperkt. En het geeft specifieke oplossingen.
- Het past een oplossing toe voor speciale scenario's van Azure AD om de fout in één stap op te lossen.
- Er is geen upgrade of configuratie vereist om deze functie in te schakelen.
Zie [Identiteitssynchronisatie en dubbele kenmerktolerantie](how-to-connect-syncservice-duplicate-attribute-resiliency.md)voor meer informatie over Azure AD.

## <a name="problems"></a>Problemen
### <a name="a-common-scenario"></a>Een veelvoorkomend scenario
Wanneer **quarantinedattributevalueMustBeUnique** en **AttributeValueMustBeUnieke** synchronisatiefouten optreden, is het gebruikelijk om een conflict met **UserPrincipalName** of **Proxy-adressen** in Azure AD te zien. U de synchronisatiefouten oplossen door het conflicterende bronobject van de on-premises kant bij te werken. De synchronisatiefout wordt na de volgende synchronisatie opgelost. Deze afbeelding geeft bijvoorbeeld aan dat twee gebruikers een conflict hebben met hun **UserPrincipalName**. Beide zijn **Joe.J\@contoso.com**. De conflicterende objecten worden in quarantaine geplaatst in Azure AD.

![Vaak voorkomende synchronisatiefoutscenario diagnosticeren](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scenario voor verweesde objecten
Af en toe zult u merken dat een bestaande gebruiker het **bronanker**verliest. Het verwijderen van het bronobject gebeurde in on-premises Active Directory. Maar de wijziging van het verwijderingssignaal is nooit gesynchroniseerd met Azure AD. Dit verlies gebeurt om redenen zoals synchronisatie-engine problemen of domeinmigratie. Wanneer hetzelfde object wordt hersteld of opnieuw wordt gemaakt, moet een bestaande gebruiker logischerwijs de gebruiker zijn die moet synchroniseren vanuit het **bronanker.** 

Wanneer een bestaande gebruiker een object is dat alleen in de cloud is, u ook de conflicterende gebruiker zien die is gesynchroniseerd met Azure AD. De gebruiker kan niet synchroon worden gekoppeld aan het bestaande object. Er is geen directe manier om het bronanker opnieuw in kaart te **brengen.** Zie meer over de [bestaande kennisbank.](https://support.microsoft.com/help/2647098) 

Als voorbeeld behoudt het bestaande object in Azure AD de licentie van Joe. Een nieuw gesynchroniseerd object met een ander **bronanker** vindt plaats in een dubbele kenmerkstatus in Azure AD. Wijzigingen voor Joe in on-premises Active Directory worden niet toegepast op de oorspronkelijke gebruiker van Joe (bestaand object) in Azure AD.  

![Diagnose van het scenario van de fout met de status van een weesobject](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnostische en probleemoplossingsstappen in Connect Health 
De diagnosefunctie ondersteunt gebruikersobjecten met de volgende gedupliceerde kenmerken:

| Kenmerknaam | Synchronisatiefouttypen|
| ------------------ | -----------------|
| UserPrincipalName | QuarantaineAttribuutWaardeMustBeUnique of AttribuutValueMustBeUnique | 
| ProxyAddresses | QuarantaineAttribuutWaardeMustBeUnique of AttribuutValueMustBeUnique | 
| SipProxyAddress | KenmerkwaardeMustBeUnique | 
| OnPremiseSecurityIdentifier |  KenmerkwaardeMustBeUnique |

>[!IMPORTANT]
> Om toegang te krijgen tot deze functie is **toestemming voor globale beheerders** of toestemming voor **inzender vereist** via de RBAC-instellingen.
>

Volg de stappen van de Azure-portal om de details van de synchronisatiefout te beperken en meer specifieke oplossingen te bieden:

![Stappen voor foutdiagnose synchroniseren](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Neem vanuit de Azure-portal een paar stappen om specifieke aanpasbare scenario's te identificeren:  
1.  Controleer de **kolom Diagnosestatus.** De status geeft aan of er een mogelijke manier is om een synchronisatiefout rechtstreeks vanuit Azure Active Directory op te lossen. Met andere woorden, er bestaat een probleemoplossingsstroom die de foutcase kan beperken en mogelijk kan oplossen.

| Status | Wat betekent dit? |
| ------------------ | -----------------|
| Niet gestart | Je hebt dit diagnoseproces niet bezocht. Afhankelijk van het diagnostische resultaat is er een mogelijke manier om de synchronisatiefout rechtstreeks vanuit de portal op te lossen. |
| Handmatige oplossing vereist | De fout voldoet niet aan de criteria van beschikbare oplossingen van de portal. Conflicterende objecttypen zijn geen gebruikers of u hebt de diagnostische stappen al doorlopen en er was geen oplossing beschikbaar via de portal. In het laatste geval is een fix van de on-premises kant nog steeds een van de oplossingen. [Lees meer over on-premises fixes.](https://support.microsoft.com/help/2647098) | 
| Synchronisatie in behandeling | Er is een oplossing toegepast. De portal wacht op de volgende synchronisatiecyclus om de fout te wissen. |

  >[!IMPORTANT]
  > De kolom diagnostische status wordt na elke synchronisatiecyclus opnieuw ingesteld. 
  >

1. Selecteer de knop **Diagnose** onder de foutdetails. U beantwoordt een paar vragen en identificeert de details van de synchronisatiefout. Antwoorden op de vragen helpen bij het identificeren van een verweesde objectcase.

1. Als er een knop **Sluiten** wordt weergegeven aan het einde van de diagnose, is er geen snelle oplossing beschikbaar via de portal op basis van uw antwoorden. Raadpleeg de oplossing die in de laatste stap wordt weergegeven. Fixes van on-premises zijn nog steeds de oplossingen. Selecteer de knop **Sluiten.** De status van de huidige synchronisatiefout schakelt naar **Handmatige oplossing vereist**. De status blijft tijdens de huidige synchronisatiecyclus.

1. Nadat een zwevende objectaanvraag is geïdentificeerd, u de dubbele kenmerken synchronisatiefouten rechtstreeks vanuit de portal oplossen. Als u het proces wilt activeren, selecteert u de knop **Fix toepassen.** De status van de huidige synchronisatiefoutwordt bijgewerkt **naar synchronisatie in behandeling**.

1. Na de volgende synchronisatiecyclus moet de fout uit de lijst worden verwijderd.

## <a name="how-to-answer-the-diagnosis-questions"></a>Hoe de diagnosevragen te beantwoorden 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Bestaat de gebruiker in uw on-premises Active Directory?

Met deze vraag wordt geprobeerd het bronobject van de bestaande gebruiker te identificeren vanuit de on-premises Active Directory.  
1. Controleer of Azure Active Directory een object heeft met de opgegeven **UserPrincipalName**. Zo niet, antwoord **nee**.
2. Controleer als u dit doet of het object nog steeds in het bereik is voor synchronisatie.  
   - Zoek in de Azure AD-connectorruimte met de DN.
   - Als het object wordt gevonden in de status **In behandeling toevoegen,** antwoordt u **Nee**. Azure AD Connect kan het object niet verbinden met het juiste Azure AD-object.
   - Als het object niet wordt gevonden, antwoordt **u ja**.

In deze voorbeelden probeert de vraag te identificeren of **Joe Jackson** nog steeds bestaat in on-premises Active Directory.
Voor het **gemeenschappelijke scenario**zijn zowel gebruikers **Joe Johnson** als **Joe Jackson** aanwezig in on-premises Active Directory. De in quarantaine geplaatste objecten zijn twee verschillende gebruikers.

![Vaak voorkomende synchronisatiefoutscenario diagnosticeren](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

Voor het **scenario voor verweesde objecten**is alleen de enkele gebruiker Joe **Johnson** aanwezig in on-premises Active Directory:

![Diagnose synchronisatiefout orphaned object * bestaat de gebruiker * scenario](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Zijn beide accounts van dezelfde gebruiker?
Met deze vraag worden een binnenkomende conflicterende gebruiker en het bestaande gebruikersobject in Azure AD gecontroleerd om te zien of deze van dezelfde gebruiker zijn.  
1. Het conflicterende object is onlangs gesynchroniseerd met Azure Active Directory. De kenmerken van de objecten vergelijken:  
   - Weergavenaam
   - User principal name
   - Object-id
2. Als Azure AD deze niet kan vergelijken, controleert u of Active Directory objecten heeft met de meegeleverde **UserPrincipalNames**. Antwoord **nee** als je beide vindt.

In het volgende voorbeeld behoren de twee objecten toe aan dezelfde gebruiker **Joe Johnson**.

![Diagnose synchronisatiefout orphaned object *zelfde gebruiker* scenario](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Wat gebeurt er nadat de correctie is toegepast in het scenario voor verweesde objecten
Op basis van de antwoorden op de voorgaande vragen ziet u de knop **Fix toepassen** wanneer er een oplossing beschikbaar is in Azure AD. In dit geval wordt het on-premises object gesynchroniseerd met een onverwacht Azure AD-object. De twee objecten worden in kaart gebracht met behulp van het **bronanker**. Met de wijziging **Fix toepassen** worden deze of vergelijkbare stappen genomen:
1. Hiermee wordt het **bronanker** bijgewerkt naar het juiste object in Azure AD.
2. Hiermee verwijdert u het conflicterende object in Azure AD als het aanwezig is.

![Synchronisatiefout diagnosticeren na de oplossing](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> De wijziging **Fix toepassen** is alleen van toepassing op gevallen van zwevende objecten.
>

Na de voorgaande stappen heeft de gebruiker toegang tot de oorspronkelijke bron, een koppeling naar een bestaand object. De **statuswaarde Diagnosticin** de lijstweergave wordt bijgewerkt naar **Synchronisatie in behandeling**. De synchronisatiefout wordt na de volgende synchronisatie opgelost. Als verbinding status wordt de opgeloste synchronisatiefout niet meer weergegeven in de lijstweergave.

## <a name="failures-and-error-messages"></a>Fouten en foutberichten
**Gebruiker met conflicterend kenmerk wordt zacht verwijderd in de Azure Active Directory. Zorg ervoor dat de gebruiker hard wordt verwijderd voordat u het opnieuw probeert.**  
De gebruiker met een conflicterend kenmerk in Azure AD moet worden gereinigd voordat u een oplossing toepassen. Bekijk [hoe u de gebruiker permanent verwijderen in Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) voordat u de oplossing opnieuw probeert. De gebruiker wordt ook automatisch definitief verwijderd na 30 dagen in zachte verwijderde staat. 

**Het bijwerken van bronanker naar cloudgebruiker in uw tenant wordt niet ondersteund.**  
Cloudgebruiker in Azure AD mag geen bronanker hebben. Het bijwerken van bronanker wordt in dit geval niet ondersteund. Handmatige oplossing is vereist van on-premises. 

## <a name="faq"></a>Veelgestelde vragen
**V:** Wat gebeurt er als de uitvoering van de **apply fix** mislukt?  
**A.** Als de uitvoering mislukt, is het mogelijk dat Azure AD Connect een exportfout uitvoert. Vernieuw de portalpagina en probeer het opnieuw na de volgende synchronisatie. De standaardsynchronisatiecyclus is 30 minuten. 


**V:** Wat gebeurt er als het **bestaande object** het object moet zijn dat moet worden verwijderd?  
**A.** Als het **bestaande object** moet worden verwijderd, houdt het proces geen wijziging van **Bronanker in.** Meestal u het oplossen vanuit on-premises Active Directory. 


**V:** Welke toestemming heeft een gebruiker nodig om de oplossing toe te passen?  
**A.** **Globale beheerder**of **bijdrager** van de RBAC-instellingen heeft toestemming om toegang te krijgen tot het diagnostische en probleemoplossingsproces.


**V:** Moet ik Azure AD Connect configureren of de Azure AD Connect-statusagent voor deze functie bijwerken?  
**A.** Nee, het diagnoseproces is een complete cloudgebaseerde functie.


**V:** Als het bestaande object zacht wordt verwijderd, wordt het object dan opnieuw actief met het diagnoseproces?  
**A.** Nee, de oplossing werkt geen andere objectkenmerken bij dan **Bronanker.**
