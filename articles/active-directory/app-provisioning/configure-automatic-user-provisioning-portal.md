---
title: Beheer van gebruikersinrichting voor bedrijfsapps in Azure AD
description: Meer informatie over het beheren van gebruikersaccountinrichting voor bedrijfsapps met behulp van de Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f9bd1afeebedf4cbf37d75d9c57f3d8be0f288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264126"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gebruikersaccountvoorziening voor bedrijfsapps beheren in de Azure-portal

In dit artikel worden de algemene stappen beschreven voor het beheren van automatische gebruikersaccountvoorzieningen en de-provisioning voor toepassingen die het ondersteunen. *Gebruikersaccountinrichting* is de handeling van het maken, bijwerken en/of uitschakelen van gebruikersaccountrecords in de lokale gebruikersprofielwinkel van een toepassing. De meeste cloud- en SaaS-toepassingen slaan de gebruikersrol en machtigingen op in de eigen lokale gebruikersprofielwinkel van de gebruiker, en de aanwezigheid van een dergelijk gebruikersrecord in de lokale winkel van de gebruiker is *vereist* voor eenmalige aanmelding en toegang tot werk. Zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen automatiseren met Azure Active Directory](user-provisioning.md)voor meer informatie over het inrichten van automatische gebruikersaccounts.

> [!IMPORTANT]
> Azure Active Directory (Azure AD) heeft een galerie met duizenden vooraf geïntegreerde toepassingen die zijn ingeschakeld voor automatische inlevering met Azure AD. U moet beginnen met het vinden van de voorname-installatiezelfstudie die specifiek is voor uw toepassing in de [lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory.](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) U vindt waarschijnlijk stapsgewijze richtlijnen voor het configureren van zowel de app als Azure AD om de inrichtingsverbinding te maken.

## <a name="finding-your-apps-in-the-portal"></a>Uw apps zoeken in de portal

Gebruik de Azure Active Directory-portal om alle toepassingen weer te geven en te beheren die zijn geconfigureerd voor één aanmelding in een map. Enterprise-apps zijn apps die binnen uw organisatie worden geïmplementeerd en gebruikt. Volg de volgende stappen om uw bedrijfstoepassingen te bekijken en te beheren:

1. Open de [Azure Active Directory-portal](https://aad.portal.azure.com).
1. Selecteer **Enterprise-toepassingen** in het linkerdeelvenster. Er wordt een lijst met alle geconfigureerde apps weergegeven, inclusief apps die vanuit de galerie zijn toegevoegd.
1. Selecteer een app om het bronvenster te laden, waar u rapporten bekijken en app-instellingen beheren.
1. Selecteer **Inrichten** om instellingen voor het inrichten van gebruikersaccounts voor de geselecteerde app te beheren.

   ![Inrichtingsscherm voor het beheren van instellingen voor het inrichten van gebruikersaccounts](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Inrichtingsmodi

Het **inrichtingsvenster** begint met een menu **Modus,** waarin de inrichtingsmodi worden weergegeven die worden ondersteund voor een bedrijfstoepassing en waarmee u deze configureren. De beschikbare opties zijn:

* **Automatisch** : deze optie wordt weergegeven als Azure AD automatische API-gebaseerde inrichting of devoorziening van gebruikersaccounts voor deze toepassing ondersteunt. Selecteer deze modus om een interface weer te geven die beheerders helpt:

  * Azure AD configureren om verbinding te maken met de gebruikersbeheer-API van de toepassing
  * Accounttoewijzingen en werkstromen maken die bepalen hoe gebruikersaccountgegevens moeten worden weergegeven tussen Azure AD en de app
  * De Azure AD-inrichtingsservice beheren

* **Handleiding** : deze optie wordt weergegeven als Azure AD geen ondersteuning biedt voor automatische inrichting van gebruikersaccounts voor deze toepassing. In dit geval moeten gebruikersaccountrecords die in de toepassing zijn opgeslagen, worden beheerd met behulp van een extern proces, op basis van de gebruikersbeheer- en inrichtingsmogelijkheden die door die toepassing worden geboden (waaronder SAML Just-In-Time-inrichting).

## <a name="configuring-automatic-user-account-provisioning"></a>Automatische gebruikersaccountvoorziening configureren

Selecteer de optie **Automatisch** om instellingen op te geven voor beheerdersreferenties, toewijzingen, starten en stoppen en synchronisatie.

### <a name="admin-credentials"></a>Beheerdersreferenties

Vouw **beheerdersreferenties** uit om de referenties in te voeren die nodig zijn voor Azure AD om verbinding te maken met de gebruikersbeheer-API van de toepassing. De vereiste invoer is afhankelijk van de toepassing. Zie de [configuratiezelfstudie voor die specifieke toepassing voor](user-provisioning.md)meer informatie over de referentietypen en vereisten voor specifieke toepassingen.

Selecteer **Verbinding testen** om de referenties te testen door Azure AD te laten proberen verbinding te maken met de inrichtingsapp van de app met behulp van de meegeleverde referenties.

### <a name="mappings"></a>Toewijzingen

**Vouw Toewijzingen** uit om de gebruikerskenmerken weer te geven en te bewerken die tussen Azure AD en de doeltoepassing stromen wanneer gebruikersaccounts worden ingericht of bijgewerkt.

Er is een vooraf geconfigureerde set toewijzingen tussen Azure AD-gebruikersobjecten en de gebruikersobjecten van elke SaaS-app. Sommige apps beheren andere typen objecten, zoals Groepen of Contactpersonen. Selecteer een toewijzing in de tabel om de toewijzingseditor rechts te openen, waar u deze bekijken en aanpassen.

![Toont het scherm Toewijzing van kenmerken weer](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Ondersteunde aanpassingen zijn onder andere:

* Toewijzingen voor specifieke objecten in- en uitschakelen, zoals het Azure AD-gebruikersobject voor het gebruikersobject van de SaaS-app.
* Het bewerken van de kenmerken die vanuit het Azure AD-gebruikersobject naar het gebruikersobject van de app stromen. Zie [Kenmerkentoewijzingstypen begrijpen](customize-application-attributes.md#understanding-attribute-mapping-types)voor meer informatie over kenmerktoewijzing.
* Filteren van de inrichtingsacties die Azure AD uitvoert op de beoogde toepassing. In plaats van dat Azure AD objecten volledig synchroniseert, u de uitvoeren van de acties beperken.

  Selecteer bijvoorbeeld alleen **Bijwerken** en Azure AD werkt alleen bestaande gebruikersaccounts in een toepassing bij, maar maakt geen nieuwe accounts. Selecteer **Alleen Maken** en Azure maakt alleen nieuwe gebruikersaccounts, maar werkt bestaande accounts niet bij. Met deze functie kunnen beheerders verschillende toewijzingen maken voor het maken en bijwerken van werkstromen voor het maken van een account.

* Een nieuwe kenmerktoewijzing toevoegen. Selecteer **Nieuwe toewijzing toevoegen** onder aan het deelvenster Toewijzing van **kenmerken.** Vul het formulier **Kenmerk bewerken** in en selecteer **Ok** om de nieuwe toewijzing aan de lijst toe te voegen.

### <a name="settings"></a>Instellingen

U de Azure AD-inrichtingsservice voor de geselecteerde toepassing starten en stoppen in het gebied **Instellingen** van **het scherm Inrichting.** U er ook voor kiezen om de inrichtingscache te wissen en de service opnieuw te starten.

Als inrichten voor het eerst is ingeschakeld voor een toepassing, schakelt u de service in door de **indikstatus** te wijzigen in **Aan**. Met deze wijziging wordt de Azure AD-inrichtingsservice een eerste cyclus uitgevoerd. Hierin worden de gebruikers gelezen die zijn toegewezen in de sectie **Gebruikers en groepen,** worden de doeltoepassing voor hen opgevraagd en worden vervolgens de inrichtingsacties uitgevoerd die zijn gedefinieerd in de sectie Azure **AD-toewijzingen.** Tijdens dit proces worden gegevens over welke gebruikersaccounts deze beheren opgeslagen in de cache, zodat niet-beheerde accounts binnen de doeltoepassingen die nooit in het bereik voor toewijzing zijn geweest, niet worden beïnvloed door de-provisioning-bewerkingen. Na de eerste cyclus synchroniseert de inrichtingsservice automatisch gebruikers- en groepsobjecten op een interval van veertig minuten.

Wijzig de **inrichtingsstatus** in **Uit** om de inrichtingsservice te pauzeren. In deze status maakt, werkt Azure geen gebruikers- of groepsobjecten in de app. Wijzig de status terug naar **Aan** en de service gaat verder waar deze was gebleven.

**Als u de huidige status wissen en synchronisatie opnieuw opstarten,** wordt een eerste cyclus geactiveerd. De service evalueert vervolgens alle gebruikers in het bronsysteem opnieuw en bepaalt of ze in het ruimte zijn voor inrichten. Dit kan handig zijn wanneer uw toepassing momenteel in quarantaine is of wanneer u een wijziging moet aanbrengen in uw kenmerktoewijzingen. Houd er rekening mee dat de eerste cyclus langer duurt dan de typische incrementele cyclus vanwege het aantal objecten dat moet worden geëvalueerd. U [hier](application-provisioning-when-will-provisioning-finish-specific-user.md)meer te weten komen over de prestaties van initiële en incrementele cycli. 
