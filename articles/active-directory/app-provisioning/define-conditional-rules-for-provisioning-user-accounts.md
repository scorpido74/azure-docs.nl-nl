---
title: Apps voorzien met scopingfilters | Microsoft Documenten
description: Lees hoe u scopingfilters gebruikt om te voorkomen dat objecten in apps die ondersteuning bieden voor geautomatiseerde gebruikersinrichting, worden ingericht als een object niet aan uw bedrijfsvereisten voldoet.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c667409f2abb9f1cf89ae3b34f08e0f9eec067e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138532"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Inrichten op basis van toepassingen met scopingfilters
Het doel van dit artikel is om uit te leggen hoe scopingfilters kunnen worden gebruikt om op kenmerken gebaseerde regels te definiëren die bepalen welke gebruikers zijn ingericht op een toepassing.

## <a name="scoping-filter-use-cases"></a>Use cases voor scoping-filters

Met een scopingfilter kan de Azure Active Directory-inrichtingsservice (Azure AD) gebruikers opnemen of uitsluiten die een kenmerk hebben dat overeenkomt met een specifieke waarde. Wanneer u bijvoorbeeld gebruikers van Azure AD indeert naar een SaaS-toepassing die door een verkoopteam wordt gebruikt, u opgeven dat alleen gebruikers met een kenmerk 'Afdeling' van 'Verkoop' in het bereik moeten zijn voor inrichten.

Scopingfilters kunnen op verschillende manieren worden gebruikt, afhankelijk van het type inrichtingsconnector:

* **Uitgaande voorziening van Azure AD naar SaaS-toepassingen**. Wanneer Azure AD het bronsysteem is, zijn [gebruikers- en groepstoewijzingen](../manage-apps/assign-user-or-group-access-portal.md) de meest voorkomende methode om te bepalen welke gebruikers in het bereik van de inrichting zijn. Deze toewijzingen worden ook gebruikt voor het inschakelen van één aanmelding en bieden één enkele methode om toegang en inrichting te beheren. Scopingfilters kunnen optioneel worden gebruikt, naast toewijzingen of in plaats daarvan, om gebruikers te filteren op basis van kenmerkwaarden.

    >[!TIP]
    > U de inrichting uitschakelen op basis van toewijzingen voor een bedrijfstoepassing door instellingen in het menu [Scope](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) te wijzigen onder de inrichtingsinstellingen om alle gebruikers en groepen te **synchroniseren.** Met deze optie plus op kenmerken gebaseerde scopingfilters bieden snellere prestaties dan het gebruik van groepstoewijzingen.  

* **Binnenkomende inrichting van HCM-toepassingen naar Azure AD en Active Directory**. Wanneer een [HCM-toepassing zoals Workday](../saas-apps/workday-tutorial.md) het bronsysteem is, zijn scopingfilters de primaire methode om te bepalen welke gebruikers moeten worden ingericht van de HCM-toepassing naar Active Directory of Azure AD.

Azure AD-inrichtingsconnectors hebben standaard geen op kenmerken gebaseerde scopingfilters geconfigureerd. 

## <a name="scoping-filter-construction"></a>Scoping filterconstructie

Een scopingfilter bestaat uit een of meer *clausules.* Clausules bepalen welke gebruikers door het scopingfilter mogen gaan door de kenmerken van elke gebruiker te evalueren. U bijvoorbeeld een clausule hebben die vereist dat het kenmerk 'Staat' van een gebruiker gelijk is aan 'New York', zodat alleen gebruikers in New York in de toepassing zijn opgenomen. 

Met één component wordt één voorwaarde gedefinieerd voor één kenmerkwaarde. Als er meerdere clausules in één scopingfilter worden gemaakt, worden ze samen geëvalueerd met behulp van 'AND'-logica. Dit betekent dat alle clausules moeten worden geëvalueerd om "waar" om een gebruiker te kunnen inrichten.

Ten slotte kunnen meerdere scopingfilters worden gemaakt voor één toepassing. Als er meerdere scopingfilters aanwezig zijn, worden ze samen geëvalueerd met behulp van 'OR'-logica. Dit betekent dat als alle clausules in een van de geconfigureerde scopingfilters worden beoordeeld op "true", de gebruiker is ingericht.

Elke gebruiker of groep die wordt verwerkt door de Azure AD-inrichtingsservice, wordt altijd afzonderlijk geëvalueerd op basis van elk scopingfilter.

Denk bijvoorbeeld aan het volgende scopingfilter:

![Scoping-filter](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

Volgens dit scopingfilter moeten gebruikers voldoen aan de volgende criteria die moeten worden bepaald:

* Ze moeten in New York zijn.
* Ze moeten op de afdeling Engineering werken.
* Hun bedrijfsmedewerker-ID moet tussen de 1.000.000 en 2.000.000 liggen.
* Hun functietitel mag niet niet null of leeg zijn.

## <a name="create-scoping-filters"></a>Scopingfilters maken
Scopingfilters zijn geconfigureerd als onderdeel van de kenmerktoewijzingen voor elke Azure AD-gebruikersinrichtingsconnector. In de volgende procedure wordt ervan uitgegaan dat u al automatische inrichting voor [een van de ondersteunde toepassingen hebt](../saas-apps/tutorial-list.md) ingesteld en er een scopingfilter aan toevoegt.

### <a name="create-a-scoping-filter"></a>Een scopingfilter maken
1. Ga in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory** > **Enterprise Applications** > **All applications.**

2. Selecteer de toepassing waarvoor u automatische inrichting hebt geconfigureerd: bijvoorbeeld "ServiceNow".

3. Selecteer het tabblad **Inrichten.**

4. Selecteer in de sectie **Toewijzingen** de toewijzing waarvoor u een scopingfilter wilt configureren: bijvoorbeeld 'Azure Active Directory Users to ServiceNow synchroniseren'.

5. Selecteer het menu **Bereik van het bronobject.**

6. Selecteer **Scopingfilter toevoegen**.

7. Definieer een clausule door een **bronkenmerknaam,** een **operator**en een **kenmerkwaarde** te selecteren waarmee u wilt overeenkomen. De volgende marktdeelnemers worden ondersteund:

   a. **GELIJKEN**. Clausule retourneert 'waar' als het geëvalueerde kenmerk exact overeenkomt met de waarde van de invoertekenreeks (hoofdlettergevoelig).

   b. **NIET GELIJK AAN**. Clausule retourneert 'waar' als het geëvalueerde kenmerk niet overeenkomt met de waarde van de invoertekenreeks (hoofdlettergevoelig).

   c. **IS WAAR.** Clausule retourneert 'waar' als het geëvalueerde kenmerk een Booleaanse waarde van true bevat.

   d. **is vals**. Clausule retourneert 'waar' als het geëvalueerde kenmerk een Booleaanse waarde van onwaar bevat.

   e. **IS NULL**. Clausule retourneert 'waar' als het geëvalueerde kenmerk leeg is.

   f. **IS NIET NULL**. Clausule retourneert 'waar' als het geëvalueerde kenmerk niet leeg is.

   g. **REGEX MATCH**. De clausule retourneert 'waar' als het geëvalueerde kenmerk overeenkomt met een normaal expressiepatroon. Bijvoorbeeld: ([1-9][0-9]) komt overeen met een getal tussen 10 en 99.

   h. **NIET REGEX MATCH**. De clausule retourneert 'waar' als het geëvalueerde kenmerk niet overeenkomt met een normaal expressiepatroon.
   
   i. **Greater_Than.** Clausule retourneert 'waar' als het geëvalueerde kenmerk groter is dan de waarde. De waarde die op het scopingfilter is opgegeven, moet een geheel getal zijn en het kenmerk op de gebruiker moet een geheel getal [0,1,2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** Clausule retourneert 'waar' als het geëvalueerde kenmerk groter is dan of gelijk is aan de waarde. De waarde die op het scopingfilter is opgegeven, moet een geheel getal zijn en het kenmerk op de gebruiker moet een geheel getal [0,1,2,...]. 
   
   k. **Bevat.** Clausule retourneert 'waar' als het geëvalueerde kenmerk de tekenreekswaarde (hoofdlettergevoelig) bevat zoals [hier](https://docs.microsoft.com/dotnet/api/system.string.contains?view=netframework-4.8)beschreven . 


>[!IMPORTANT] 
> - Het IsMemberOf-filter wordt momenteel niet ondersteund.
> - EQUALS en NOT EQUALS worden niet ondersteund voor multi-gewaardeerde kenmerken

9. Eventueel herhaalje stap 7-8 om meer scoping-clausules toe te voegen.

10. Voeg in **De titel Scoping-filter**een naam toe voor het scopingfilter.

11. Selecteer **OK**.

12. Selecteer **OK** opnieuw op het scherm **Scoping-filters.** Herhaal eventueel stap 6-11 om een ander scopingfilter toe te voegen.

13. Selecteer **Opslaan** in het scherm **Toewijzing van kenmerken.** 

>[!IMPORTANT] 
> Het opslaan van een nieuw scopingfilter activeert een nieuwe volledige synchronisatie voor de toepassing, waarbij alle gebruikers in het bronsysteem opnieuw worden geëvalueerd tegen het nieuwe scoping-filter. Als een gebruiker in de toepassing voorheen in het bereik van de inrichting, maar valt buiten het bereik, zijn account is uitgeschakeld of gedeprovisioneerd in de toepassing. Als u dit standaardgedrag wilt overschrijven, raadpleegt u [Verwijdering overslaan voor gebruikersaccounts die buiten het bereik vallen.](../app-provisioning/skip-out-of-scope-deletions.md)


## <a name="common-scoping-filters"></a>Algemene scopingfilters
| Kenmerk Target| Operator | Waarde | Beschrijving|
|----|----|----|----|
|userPrincipalName|REGEX-OVEREENKOMST|.\*@domain.com |Alle gebruikers met userPrincipal @domain.com die het domein heeft, hebben het bereik om|
|userPrincipalName|NIET REGEX MATCH|.\*@domain.com|Alle gebruikers met userPrincipal @domain.com die het domein heeft, hebben geen ruimte voor|
|department|GELIJKISTEN|Verkoop|Alle gebruikers van de verkoopafdeling zijn in het kader van|
|workerID|REGEX-OVEREENKOMST|(1[0-9][0-9][0-9][0-9][0-9][0-9])| Alle werknemers met werknemers met id's tussen 1000000 en 2000000 zijn in het kader van voorzieningen.|

## <a name="related-articles"></a>Verwante artikelen:
* [Gebruikersinrichting en deprovisioning automatiseren naar SaaS-toepassingen](../app-provisioning/user-provisioning.md)
* [Kenmerkentoewijzingen aanpassen voor gebruikersinrichting](../app-provisioning/customize-application-attributes.md)
* [Expressies schrijven voor kenmerktoewijzingen](functions-for-customizing-application-data.md)
* [Kennisgevingen voor het inrichten van een account](../app-provisioning/user-provisioning.md)
* [SCIM gebruiken om automatische inrichting van gebruikers en groepen van Azure Active Directory naar toepassingen mogelijk te maken](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Lijst met zelfstudies over het integreren van SaaS-apps](../saas-apps/tutorial-list.md)

