---
title: Ontdek wanneer een specifieke gebruiker toegang heeft tot een app
description: Hoe u erachter komt wanneer een uiterst belangrijke gebruiker toegang heeft tot een toepassing die u hebt geconfigureerd voor gebruikersvoorziening met Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48370e2806b70d550bce95ceff3857a79561f247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264139"
---
# <a name="check-the-status-of-user-provisioning"></a>Controleer de status van de gebruikersinrichting

De Azure AD-inrichtingsservice voert een eerste inrichtingscyclus uit ten opzichte van het bronsysteem en het doelsysteem, gevolgd door periodieke incrementele cycli. Wanneer u de inrichting voor een app configureert, u de huidige status van de inrichtingsservice controleren en zien wanneer een gebruiker toegang heeft tot een app.

## <a name="view-the-provisioning-progress-bar"></a>De voortgangsbalk voor het inrichten weergeven

 Op de **inrichtingspagina** voor een app u de status van de Azure AD-inrichtingsservice weergeven. In de sectie **Huidige status** onder aan de pagina wordt weergegeven of een inrichtingscyclus is begonnen met het inrichten van gebruikersaccounts. U de voortgang van de cyclus bekijken, zien hoeveel gebruikers en groepen zijn ingericht en zien hoeveel rollen er zijn gemaakt.

Wanneer u de automatische inrichting voor het eerst configureert, wordt in de sectie **Huidige status** onder aan de pagina de status van de oorspronkelijke inrichtingscyclus weergegeven. Deze sectie wordt bijgewerkt telkens wanneer een incrementele cyclus wordt uitgevoerd. De volgende details worden weergegeven:
- Het type inrichtingscyclus (initieel of incrementeel) dat momenteel wordt uitgevoerd of voor het laatst is voltooid.
- Een **voortgangsbalk** met het percentage van de inrichtingscyclus dat is voltooid. Het percentage geeft het aantal ingerichte pagina's weer. Houd er rekening mee dat elke pagina meerdere gebruikers of groepen kan bevatten, zodat het percentage niet direct correleert met het aantal gebruikers, groepen of ingerichte rollen.
- Een **knop Vernieuwen** die u gebruiken om de weergave bijgewerkt te houden.
- Het aantal **gebruikers** en **groepen** in het connectorgegevensarchief. Het aantal neemt toe wanneer een object wordt toegevoegd aan het bereik van de inrichting. Het aantal wordt niet afgebroken als een gebruiker is soft-verwijderd of hard-verwijderd als dit niet het object te verwijderen uit de connector gegevens archief. De telling wordt opnieuw ingesteld op de eerste synchronisatie nadat de CDS is [gereset](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) 
- Een koppeling **Controlelogboeken weergeven,** waarmee de azure AD-inrichtingslogboeken worden geopend voor meer informatie over alle bewerkingen die worden uitgevoerd door de service voor het inrichten van de gebruiker, inclusief de inrichtingsstatus voor individuele gebruikers (zie de sectie [Logboeken voor het inrichten van het gebruik](#use-provisioning-logs-to-check-a-users-provisioning-status) hieronder).

Nadat een inrichtingscyclus is voltooid, toont de sectie **Statistieken tot op heden** het cumulatieve aantal gebruikers en groepen dat tot nu toe is ingericht, samen met de voltooiingsdatum en duur van de laatste cyclus. De **activiteits-id** identificeert op unieke wijze de meest recente inrichtingscyclus. De **taak-id** is een unieke id voor de inrichtingstaak en is specifiek voor de app in uw tenant.

De inrichtingsvoortgang kan worden weergegeven in de Azure-portal op het tabblad **Azure Active &gt; Directory Enterprise Apps-toepassingsnaam &gt; \[\] &gt; Provisioning.**

![Voortgangsbalk voor het inrichten van pagina's](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Inrichtingslogboeken gebruiken om de inrichtingsstatus van een gebruiker te controleren

Raadpleeg de [provisioninglogs (preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) in Azure AD om de inrichtingsstatus voor een geselecteerde gebruiker te bekijken. Alle bewerkingen die door de gebruikersinrichtingsservice worden uitgevoerd, worden geregistreerd in de Azure AD-inrichtingslogboeken. Dit omvat alle lees- en schrijfbewerkingen die zijn uitgevoerd naar de bron- en doelsystemen en de gebruikersgegevens die tijdens elke bewerking zijn gelezen of geschreven.

U hebt toegang tot de inrichtingslogboeken in de Azure-portal door **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning logs (preview)** te selecteren in de sectie **Activiteit.** U de inrichtingsgegevens doorzoeken op basis van de naam van de gebruiker of de id in het bronsysteem of het doelsysteem. Zie [Logboeken inrichten (voorbeeld) voor](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)meer informatie. 

In de inrichtingslogboeken worden alle bewerkingen van de inrichtingsservice geregistreerd, waaronder:

* Azure AD opvragen voor toegewezen gebruikers die in het bereik van de inrichting zijn
* De doel-app opvragen voor het bestaan van die gebruikers
* De gebruikersobjecten tussen het systeem vergelijken
* Het gebruikersaccount in het doelsysteem toevoegen, bijwerken of uitschakelen op basis van de vergelijking

Zie de [rapportagehandleiding voor het inrichten](check-status-user-account-provisioning.md)van de inloglogboeken in de Azure-portal voor meer informatie over het lezen van de inrichtingslogboeken in de Azure-portal.

## <a name="how-long-will-it-take-to-provision-users"></a>Hoe lang duurt het om gebruikers te voorzien?
Wanneer azure AD automatische gebruikersinrichting met een toepassing gebruikt, worden gebruikersaccounts in een app automatisch ingericht en bijgewerkt op basis van zaken als [gebruikers- en groepstoewijzing](../manage-apps/assign-user-or-group-access-portal.md) op een regelmatig gepland tijdsinterval, meestal elke 40 minuten.

De tijd die nodig is voor een bepaalde gebruiker om te worden ingericht, hangt voornamelijk af van de vraag of uw inrichtingstaak een eerste cyclus of een incrementele cyclus uitvoert.

- Voor de **eerste cyclus**is de taaktijd afhankelijk van vele factoren, waaronder het aantal gebruikers en groepen dat geschikt is voor inrichten en het totale aantal gebruikers en groepen in het bronsysteem. De eerste synchronisatie tussen Azure AD en een app kan 20 minuten tot enkele uren duren, afhankelijk van de grootte van de Azure AD-map en het aantal gebruikers dat in het bereik is voor inlevering. Een uitgebreide lijst met factoren die van invloed zijn op de initiële cyclusprestaties worden later in deze sectie samengevat.

- Voor **incrementele cycli** na de eerste cyclus zijn de taaktijden meestal sneller (bijvoorbeeld binnen 10 minuten), omdat de provisioningservice watermerken opslaat die de status van beide systemen na de eerste cyclus vertegenwoordigen, waardoor de prestaties van de volgende synchronisaties worden verbeterd. De werktijd is afhankelijk van het aantal wijzigingen dat in die inrichtingscyclus is gedetecteerd. Als er minder dan 5.000 wijzigingen in het gebruikers- of groepslidmaatschap zijn, kan de taak binnen één incrementele inrichtingscyclus worden voltooid. 

In de volgende tabel worden synchronisatietijden voor algemene inrichtingsscenario's samengevat. In deze scenario's is het bronsysteem Azure AD en is het doelsysteem een SaaS-toepassing. De synchronisatietijden zijn afgeleid van een statistische analyse van synchronisatietaken voor de SaaS-toepassingen ServiceNow, Workplace, Salesforce en G Suite.


| Scope-configuratie | Gebruikers, groepen en leden in scope | Initiële cyclustijd | Incrementele cyclustijd |
| -------- | -------- | -------- | -------- |
| Alleen toegewezen gebruikers en groepen synchroniseren |  < 1.000 |  < 30 minuten | < 30 minuten |
| Alleen toegewezen gebruikers en groepen synchroniseren |  1,000 - 10,000 | 142 - 708 minuten | < 30 minuten |
| Alleen toegewezen gebruikers en groepen synchroniseren |   10,000 - 100,000 | 1.170 - 2.340 minuten | < 30 minuten |
| Alle gebruikers en groepen synchroniseren in Azure AD |  < 1.000 | < 30 minuten  | < 30 minuten |
| Alle gebruikers en groepen synchroniseren in Azure AD |  1,000 - 10,000 | < 30 - 120 minuten | < 30 minuten |
| Alle gebruikers en groepen synchroniseren in Azure AD |  10,000 - 100,000  | 713 - 1.425 minuten | < 30 minuten |
| Alle gebruikers synchroniseren in Azure AD|  < 1.000  | < 30 minuten | < 30 minuten |
| Alle gebruikers synchroniseren in Azure AD | 1,000 - 10,000  | 43 - 86 minuten | < 30 minuten |


Alleen voor de **toegewezen gebruiker en groepen met configuratiesynchronisatie**u de volgende formules gebruiken om de geschatte minimale en maximale verwachte **initiële cyclustijden** te bepalen:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Samenvatting van factoren die van invloed zijn op de tijd die nodig is om een **eerste cyclus**te voltooien:

- Het totale aantal gebruikers en groepen dat geschikt is voor voorzieningen.

- Het totale aantal gebruikers, groepen en groepsleden dat aanwezig is in het bronsysteem (Azure AD).

- Of gebruikers die geschikt zijn voor inrichten, zijn afgestemd op bestaande gebruikers in de doeltoepassing of voor het eerst moeten worden gemaakt. Synchronisatietaken waarvoor alle gebruikers voor het eerst zijn gemaakt, duurt ongeveer *twee keer zo lang als* synchronisatietaken waarvoor alle gebruikers zijn gekoppeld aan bestaande gebruikers.

- Aantal fouten in de [inrichtingslogboeken](check-status-user-account-provisioning.md). De prestaties zijn trager als er veel fouten zijn en de inrichtingsservice in een quarantainestatus is gegaan. 

- Verzoek tarieflimieten en beperking geïmplementeerd door het doelsysteem. Sommige doelsystemen implementeren limieten voor het beginvan de aanvraag en beperking, wat van invloed kan zijn op de prestaties tijdens grote synchronisatiebewerkingen. Onder deze voorwaarden kan een app die te snel te veel aanvragen ontvangt, de responssnelheid vertragen of de verbinding sluiten. Om de prestaties te verbeteren, moet de connector worden aangepast door de app-verzoeken niet sneller te verzenden dan de app kan verwerken. Het inrichten van connectors die door Microsoft zijn gebouwd, voeren deze aanpassing uit. 

- Het aantal en de grootte van toegewezen groepen. Het synchroniseren van toegewezen groepen duurt langer dan het synchroniseren van gebruikers. Zowel het aantal als de grootte van de toegewezen groepen hebben invloed op de prestaties. Als een toepassing toewijzingen heeft [ingeschakeld voor het synchroniseren van groepsobjecten,](customize-application-attributes.md#editing-group-attribute-mappings)worden groepseigenschappen zoals groepsnamen en lidmaatschappen naast gebruikers gesynchroniseerd. Deze extra synchronisaties duren langer dan alleen het synchroniseren van gebruikersobjecten.

- Als de prestaties een probleem worden en u probeert de meerderheid van de gebruikers en groepen in uw tenant in te richten, gebruikt u scopingfilters. Met scopingfilters u de gegevens die de inrichtingsservice uit Azure AD haalt, verfijnen door gebruikers uit te filteren op basis van specifieke kenmerkwaarden. Zie [Op kenmerken gebaseerde toepassingsinrichting met scopingfilters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor meer informatie over scopingfilters.

## <a name="next-steps"></a>Volgende stappen
[Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren met Azure Active Directory](user-provisioning.md)
