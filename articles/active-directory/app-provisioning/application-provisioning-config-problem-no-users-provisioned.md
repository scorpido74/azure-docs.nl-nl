---
title: Er worden geen gebruikers ingericht voor een Azure AD Gallery-toepassing
description: Veelvoorkomende problemen oplossen wanneer u geen gebruikers ziet die worden weergegeven in een Azure AD Gallery-toepassing die u hebt geconfigureerd voor gebruikersvoorziening met Azure AD
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
ms.openlocfilehash: 9b60261d63e1bcb75aea9d2e8a6b74902520f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522914"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Er worden geen gebruikers ingericht voor een Azure AD Gallery-toepassing
Nadat de automatische inrichting is geconfigureerd voor een toepassing (inclusief het verifiëren dat de app-referenties die aan Azure AD zijn verstrekt om verbinding te maken met de app geldig zijn), worden gebruikers en/of groepen in de app ingericht. De inrichting wordt bepaald door de volgende dingen:

-   Welke gebruikers en groepen zijn **toegewezen aan** de toepassing. Houd er rekening mee dat het inrichten van geneste groepen of Office 365-groepen niet wordt ondersteund. Zie [Een gebruiker of groep toewijzen aan een bedrijfsapp in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)voor meer informatie over toewijzing.
-   Of **toewijzingstoewijzingen** zijn ingeschakeld en geconfigureerd om geldige kenmerken van Azure AD naar de app te synchroniseren. Zie [Toewijzingen voor gebruikerstoewijzingen voor toewijzingen voor gebruikers voor SaaS-toepassingen aanpassen in Azure Active Directory](customize-application-attributes.md).
-   Of er al dan niet een **scopingfilter** aanwezig is die gebruikers filtert op basis van specifieke kenmerkwaarden. Zie [Op kenmerken gebaseerde toepassingsinrichting met scopingfilters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor meer informatie over scopingfilters.
  
Als u merkt dat gebruikers niet worden ingericht, raadpleegt u de [provisioning-logboeken (voorbeeld)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) in Azure AD. Zoek naar logboekvermeldingen voor een specifieke gebruiker.

U hebt toegang tot de inrichtingslogboeken in de Azure-portal door **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning logs (preview)** te selecteren in de sectie **Activiteit.** U de inrichtingsgegevens doorzoeken op basis van de naam van de gebruiker of de id in het bronsysteem of het doelsysteem. Zie [Logboeken inrichten (voorbeeld) voor](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)meer informatie. 

De provisioning logs registreren alle bewerkingen die door de inrichtingsservice worden uitgevoerd, inclusief het opvragen van Azure AD voor toegewezen gebruikers die in het bereik zijn voor inrichten, het opvragen van de doel-app voor het bestaan van die gebruikers, het vergelijken van de gebruikersobjecten tussen het systeem. Voeg vervolgens het gebruikersaccount toe, bij of schakel het uit in het doelsysteem op basis van de vergelijking.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Algemene probleemgebieden met provisioning te overwegen
Hieronder vindt u een lijst van de algemene probleemgebieden waarin u inzoomen als u een idee hebt van waar u moet beginnen.

- [De inddienst lijkt niet te starten](#provisioning-service-does-not-appear-to-start)
- [Inprovisioning logs zeggen dat gebruikers worden overgeslagen en niet ingericht, ook al zijn ze toegewezen](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>De inddienst lijkt niet te starten
Als u de **inrichtingsstatus** instelt **in** de sectie **Azure Active &gt; Directory Enterprise Apps &gt; \[Application Name Provisioning\] &gt;** sectie van de Azure portal. Er worden echter geen andere statusdetails weergegeven op die pagina na volgende herladen, het is waarschijnlijk dat de service wordt uitgevoerd, maar nog geen eerste cyclus heeft voltooid. Controleer de hierboven beschreven **inrichtingslogboeken (voorbeeld)** om te bepalen welke bewerkingen de service uitvoert en of er fouten zijn.

>[!NOTE]
>Een eerste cyclus kan 20 minuten tot enkele uren duren, afhankelijk van de grootte van de Azure AD-map en het aantal gebruikers dat in staatis. Latere synchronisaties na de eerste cyclus zijn sneller, omdat de provisioning service watermerken opslaat die de status van beide systemen na de eerste cyclus vertegenwoordigen. De eerste cyclus verbetert de prestaties van de volgende synchronisaties.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Inprovisioning logs zeggen dat gebruikers worden overgeslagen en niet ingericht, ook al zijn ze toegewezen

Wanneer een gebruiker wordt weergegeven als 'overgeslagen' in de inrichtingslogboeken, is het belangrijk om het tabblad **Stappen** van het logboek te controleren om de reden te bepalen. Hieronder staan veelvoorkomende redenen en oplossingen:

- **Er is een scopingfilter geconfigureerd** **dat de gebruiker filtert op basis van een kenmerkwaarde.** Zie [scopingfilters voor](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)meer informatie over scopingfilters.
- **De gebruiker is "niet effectief gerechtigd".** Als u dit specifieke foutbericht ziet, is dit omdat er een probleem is met de gebruikerstoewijzingsrecord die is opgeslagen in Azure AD. Als u dit probleem wilt oplossen, wijst u de gebruiker (of groep) uit de app af en wijst u het opnieuw toe. Zie [Gebruikers- of groepstoegang toewijzen](../manage-apps/assign-user-or-group-access-portal.md)voor meer informatie over toewijzing.
- **Een vereist kenmerk ontbreekt of wordt niet ingevuld voor een gebruiker.** Een belangrijk ding om te overwegen bij het instellen van inrichting is het controleren en configureren van de attribuuttoewijzingen en -werkstromen die bepalen welke gebruikerseigenschappen (of groep) van Azure AD naar de toepassing stromen. Deze configuratie omvat het instellen van de "overeenkomende eigenschap" die wordt gebruikt om gebruikers/groepen tussen de twee systemen op unieke wijze te identificeren en te matchen. Zie [Toewijzingen van gebruikersvoor saas-toepassingen aanpassen voor saas-toepassingen in Azure Active Directory voor](customize-application-attributes.md)meer informatie over dit belangrijke proces.
- **Toewijzingen voor groepen:** Inrichten van de groepsnaam en groepsgegevens, naast de leden, indien ondersteund voor sommige toepassingen. U deze functionaliteit in- of uitschakelen door de **toewijzing** voor groepsobjecten in te schakelen of uit te schakelen die worden weergegeven op het tabblad **Inrichten.** Als inrichtingsgroepen is ingeschakeld, moet u de kenmerktoewijzingen controleren om ervoor te zorgen dat een geschikt veld wordt gebruikt voor de 'overeenkomende id'. De overeenkomende ID kan de weergavenaam of e-mailalias zijn. De groep en haar leden zijn niet ingericht als de overeenkomende eigenschap leeg is of niet wordt ingevuld voor een groep in Azure AD.

## <a name="next-steps"></a>Volgende stappen

[Azure AD Connect-synchronisatie: declaratieve inrichting begrijpen](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
