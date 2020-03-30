---
title: Enterprise state roaming inschakelen in Azure Active Directory
description: Veelgestelde vragen over enterprise state roaming-instellingen in Windows-apparaten.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c44d6266f5ea8cdd4f75d0449cb49852e71c905
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672400"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Enterprise state roaming inschakelen in Azure Active Directory

Enterprise State Roaming is beschikbaar voor elke organisatie met een Azure AD Premium- of Enterprise Mobility + Security (EMS)-licentie. Zie de [productpagina van Azure AD](https://azure.microsoft.com/services/active-directory)voor meer informatie over het aanschaffen van een Azure AD-abonnement.

Wanneer u Enterprise State Roaming inschakelt, krijgt uw organisatie automatisch een gratis licentie voor beperkt gebruik voor Azure Rights Management-bescherming tegen Azure Information Protection. Dit gratis abonnement is beperkt tot het versleutelen en ontsleutelen van bedrijfsinstellingen en toepassingsgegevens die worden gesynchroniseerd door Enterprise State Roaming. U moet [een betaald abonnement](https://azure.microsoft.com/pricing/details/information-protection/) hebben om de volledige mogelijkheden van de Azure Rights Management-service te kunnen gebruiken.

> [!NOTE]
> Dit artikel is van toepassing op de Microsoft Edge Legacy HTML-gebaseerde browser die in juli 2015 met Windows 10 is gelanceerd. Het artikel is niet van toepassing op de nieuwe Microsoft Edge Chromium-gebaseerde browser uitgebracht op 15 januari 2020. Zie het artikel Microsoft Edge Sync voor meer informatie over het gedrag Synchroniseren voor de nieuwe Microsoft [Edge.](/deployedge/microsoft-edge-enterprise-sync)

## <a name="to-enable-enterprise-state-roaming"></a>Enterprise State Roaming inschakelen

1. Meld u aan bij [azure AD-beheercentrum](https://aad.portal.azure.com/).
1. Selecteer **Azure Active Directory** &gt; **Devices** &gt; **Enterprise State Roaming**.
1. Selecteer **Gebruikers kunnen instellingen en app-gegevens op verschillende apparaten synchroniseren.** Zie voor meer informatie [hoe u apparaatinstellingen configureert](/azure/active-directory/device-management-azure-portal).
  
   ![afbeelding van apparaatinstelling met het label Gebruikers kunnen instellingen en app-gegevens op verschillende apparaten synchroniseren](./media/enterprise-state-roaming-enable/device-settings.png)
  
Voor een Windows 10-apparaat dat de Enterprise State Roaming-service kan gebruiken, moet het apparaat zich verifiëren met een Azure AD-identiteit. Voor apparaten die zijn verbonden met Azure AD, is de primaire aanmeldingsidentiteit van de gebruiker de Azure AD-identiteit, dus er is geen extra configuratie vereist. Voor apparaten die on-premises Active Directory gebruiken, moet de IT-beheerder [hybride Azure Active Directory-samengevoegde apparaten configureren.](hybrid-azuread-join-manual-steps.md) 

## <a name="data-storage"></a>Gegevensopslag

Roaminggegevens van ondernemingsstatus worden gehost in een of meer [Azure-regio's](https://azure.microsoft.com/regions/) die het best overeenkomen met de land-/regiowaarde die is ingesteld in de instantie Azure Active Directory. Gegevens over roaming van ondernemingsstaten worden verdeeld op basis van drie belangrijke geografische regio's: Noord-Amerika, EMEA en APAC. Roaminggegevens voor de tenant voor ondernemingsstatus bevinden zich lokaal met de geografische regio en worden niet gerepliceerd tussen regio's.  Bijvoorbeeld:

| Land/regio waarde | heeft hun gegevens gehost in |
| -------------------- | ------------------------ |
| Een EMEA-land/regio zoals Frankrijk of Zambia | Een of meer van de Azure-regio's binnen Europa |
| Een Noord-Amerikaans land/regio zoals de Verenigde Staten of Canada | Een of meer azure-regio's binnen de VS |
| Een APAC-land/regio zoals Australië of Nieuw-Zeeland | Een of meer van de Azure-regio's binnen Azië |
| Zuid-Amerikaanse en Antarctica regio's | Een of meer Azure-regio's binnen de VS |

De land-/regiowaarde wordt ingesteld als onderdeel van het proces voor het maken van Azure AD-directoryen en kan vervolgens niet worden gewijzigd. Als u meer informatie wilt over uw locatie voor gegevensopslag, dient u een ticket in met [Azure-ondersteuning.](https://azure.microsoft.com/support/options/)

## <a name="view-per-user-device-sync-status"></a>Synchronisatiestatus per gebruiker weergeven

Volg deze stappen om een synchronisatiestatusrapport per gebruiker weer te geven.

1. Meld u aan bij [azure AD-beheercentrum](https://aad.portal.azure.com/).
1. Selecteer **Azure Active Directory** &gt; **Users** &gt; **All users All users**.
1. Selecteer de gebruiker en selecteer **Apparaten**.
1. Selecteer **onder Weergeven**de optie **Instellingen voor synchroniseren en app-gegevens van apparaten** om de synchronisatiestatus weer te geven.
  
   ![afbeelding van de instellingen voor het synchroniseren van apparaten](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Als er apparaten zijn die voor deze gebruiker worden gesynchroniseerd, ziet u de apparaten zoals hier weergegeven.
  
   ![afbeelding van kolomkolomgegevens voor apparaatsynchronisatie](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Bewaartijd van gegevens

Gegevens die met Enterprise State Roaming met enterprise state roaming worden gesynchroniseerd met de Microsoft-cloud, worden bewaard totdat deze handmatig worden verwijderd of totdat is vastgesteld dat de gegevens in kwestie verouderd zijn. 

### <a name="explicit-deletion"></a>Expliciete verwijdering

Expliciete verwijdering is wanneer een Azure-beheerder een gebruiker of een map verwijdert of anderszins expliciet verzoekt dat gegevens moeten worden verwijderd.

* **Gebruikersverwijdering:** Wanneer een gebruiker wordt verwijderd in Azure AD, worden de roaminggegevens van het gebruikersaccount na 90 tot 180 dagen verwijderd. 
* **Directory verwijderen:** het verwijderen van een volledige map in Azure AD is een onmiddellijke bewerking. Alle instellingengegevens die aan die map zijn gekoppeld, worden na 90 tot 180 dagen verwijderd. 
* **Op verzoek verwijderen:** Als de Azure AD-beheerder handmatig de gegevens of instellingengegevens van een specifieke gebruiker wil verwijderen, kan de beheerder een ticket met [Azure-ondersteuning indienen.](https://azure.microsoft.com/support/) 

### <a name="stale-data-deletion"></a>Verouderde gegevens verwijderen

Gegevens die een jaar lang niet zijn geopend ('de bewaarperiode') worden als verouderd behandeld en kunnen worden verwijderd uit de Microsoft-cloud. De bewaartermijn is onderhevig aan wijzigingen, maar bedraagt niet minder dan 90 dagen. De verouderde gegevens kunnen een specifieke set windows/toepassingsinstellingen of alle instellingen voor een gebruiker zijn. Bijvoorbeeld:

* Als geen apparaten toegang hebben tot een bepaalde instellingenverzameling (bijvoorbeeld een toepassing wordt verwijderd van het apparaat of een instellingengroep zoals 'Thema' is uitgeschakeld voor alle apparaten van een gebruiker), wordt die verzameling na de bewaarperiode verouderd en kan deze worden verwijderd . 
* Als een gebruiker de synchronisatie van instellingen op al zijn apparaten heeft uitgeschakeld, worden geen van de instellingengegevens geopend en worden alle instellingengegevens voor die gebruiker verouderd en kunnen ze na de bewaarperiode worden verwijderd. 
* Als de Azure AD-mapbeheerder Enterprise State Roaming voor de gehele map uitschakelt, stoppen alle gebruikers in die map met synchronisatie-instellingen en worden alle instellingengegevens voor alle gebruikers verouderd en kunnen ze na de bewaarperiode worden verwijderd. 

### <a name="deleted-data-recovery"></a>Verwijderde gegevensherstel

Het beleid voor gegevensbewaring is niet configureerbaar. Zodra de gegevens permanent zijn verwijderd, kan deze niet meer worden hersteld. De instellingengegevens worden echter alleen uit de Microsoft-cloud verwijderd, niet van het apparaat van de eindgebruiker. Als een apparaat later opnieuw verbinding maakt met de Enterprise State Roaming-service, worden de instellingen opnieuw gesynchroniseerd en opgeslagen in de Microsoft-cloud.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van enterprise state roaming](enterprise-state-roaming-overview.md)
* [Veelgestelde vragen over instellingen en gegevensroaming](enterprise-state-roaming-faqs.md)
* [Groepsbeleid en MDM-instellingen voor synchronisatie van instellingen](enterprise-state-roaming-group-policy-settings.md)
* [Naslaginformatie over roaminginstellingen voor Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Probleemoplossing](enterprise-state-roaming-troubleshooting.md)
