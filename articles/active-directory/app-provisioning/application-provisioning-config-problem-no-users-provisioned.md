---
title: Er worden geen gebruikers ingericht voor een Azure AD-galerie toepassing
description: Veelvoorkomende problemen oplossen die zich voordoen wanneer u geen gebruikers ziet die worden weer gegeven in een Azure AD Gallery-toepassing die u hebt geconfigureerd voor het inrichten van gebruikers met Azure AD
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
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522914"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Er worden geen gebruikers ingericht voor een Azure AD-galerie toepassing
Nadat automatische inrichting is geconfigureerd voor een toepassing (waaronder controleren of de referenties van de app die zijn opgegeven voor Azure AD om verbinding te maken met de app geldig zijn), worden gebruikers en/of groepen ingericht voor de app. Inrichting wordt bepaald door de volgende zaken:

-   Welke gebruikers en groepen zijn **toegewezen** aan de toepassing. Houd er rekening mee dat het inrichten van geneste groepen of Office 365-groepen niet wordt ondersteund. Zie [een gebruiker of groep toewijzen aan een bedrijfs-app in azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)voor meer informatie over de toewijzing.
-   Of **kenmerk toewijzingen** zijn ingeschakeld, en geconfigureerd om geldige kenmerken van Azure ad te synchroniseren met de app. Zie voor meer informatie over kenmerk toewijzingen [aanpassen van kenmerk toewijzingen van gebruikers inrichting voor SaaS-toepassingen in azure Active Directory](customize-application-attributes.md).
-   Hiermee wordt aangegeven of er een **bereik filter** aanwezig is waarmee gebruikers worden gefilterd op basis van specifieke kenmerk waarden. Zie voor meer informatie over het bereik van filters [kenmerk toepassing inrichten met bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
Als u ziet dat gebruikers niet worden ingericht, raadpleegt u de [inrichtings Logboeken (preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) in azure AD. Zoeken naar logboek vermeldingen voor een specifieke gebruiker.

U kunt toegang krijgen tot de inrichtings Logboeken in de Azure Portal door **Azure Active Directory** &gt; **enter prise apps** &gt; **inrichtings Logboeken (preview)** te selecteren in de sectie **activiteit** . U kunt de inrichtings gegevens zoeken op basis van de naam van de gebruiker of de id in het bron systeem of het doel systeem. Zie [inrichtings Logboeken (preview-versie)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)voor meer informatie. 

In de inrichtings logboeken worden alle bewerkingen geregistreerd die worden uitgevoerd door de inrichtings service, inclusief het opvragen van Azure AD voor toegewezen gebruikers die binnen het bereik van de inrichting vallen, het uitvoeren van een query op de doel-app voor het bestaan van deze gebruikers, het vergelijken van de gebruikers objecten tussen het systeem. Vervolgens kunt u het gebruikers account in het doel systeem toevoegen, bijwerken of uitschakelen op basis van de vergelijking.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Algemene probleem gebieden met het inrichten om te overwegen
Hieronder vindt u een lijst met algemene probleem gebieden die u kunt inzoomen als u een idee hebt van waar u moet beginnen.

- [De inrichtings service lijkt niet te worden gestart](#provisioning-service-does-not-appear-to-start)
- [Inrichtings logboeken zeggen dat gebruikers worden overgeslagen en niet ingericht, ook al toegewezen](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>De inrichtings service lijkt niet te worden gestart
Als u instelt dat de **inrichtings status** moet worden **ingeschakeld** in de **Azure Active Directory &gt; Enter prise Apps &gt; \[toepassings naam\] &gt;inrichtings** sectie van de Azure Portal. Er worden echter geen andere status gegevens op die pagina weer gegeven na de volgende keer opnieuw laden. de service wordt waarschijnlijk uitgevoerd, maar is nog niet klaar met het volt ooien van een eerste cyclus. Controleer de hierboven beschreven **inrichtings Logboeken (preview)** om te bepalen welke bewerkingen de service uitvoert en of er fouten zijn.

>[!NOTE]
>Een eerste cyclus kan 20 minuten tot enkele uren duren, afhankelijk van de grootte van de Azure AD-adres lijst en het aantal gebruikers binnen het bereik van de inrichting. Volgende synchronisaties na de eerste cyclus zijn sneller, omdat er door de inrichtings service water merken worden opgeslagen die de status van beide systemen na de eerste cyclus vertegenwoordigen. De eerste cyclus verbetert de prestaties van volgende synchronisaties.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Inrichtings logboeken zeggen dat gebruikers worden overgeslagen en niet ingericht, zelfs als ze worden toegewezen

Wanneer een gebruiker wordt weer gegeven als ' overgeslagen ' in de inrichtings logboeken, is het belang rijk om het tabblad **stappen** van het logboek te bekijken om de reden te bepalen. Hieronder vindt u enkele veelvoorkomende redenen en oplossingen:

- **Er is een bereik filter geconfigureerd** **waarmee de gebruiker wordt gefilterd op basis van een kenmerk waarde**. Zie [bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor meer informatie over het bereik van filters.
- **De gebruiker is niet effectief.** Als u dit specifieke fout bericht ziet, is er een probleem met de gebruikers toewijzings record die is opgeslagen in azure AD. U kunt dit probleem oplossen door de toewijzing van de gebruiker (of groep) uit de app ongedaan te verwijderen en opnieuw toe te wijzen. Zie [toegang toewijzen aan gebruikers of groepen](../manage-apps/assign-user-or-group-access-portal.md)voor meer informatie over de toewijzing.
- **Een vereist kenmerk ontbreekt of is niet ingevuld voor een gebruiker.** Het is belang rijk om rekening mee te houden bij het instellen van inrichting door de kenmerk toewijzingen en werk stromen te controleren en te configureren die bepalen welke gebruikers-(of groeps) eigenschappen stromen van Azure AD naar de toepassing. Deze configuratie omvat het instellen van de ' overeenkomende eigenschap ' die wordt gebruikt om gebruikers/groepen tussen de twee systemen uniek te identificeren en te vergelijken. Zie voor meer informatie over dit belang rijke proces de [toewijzing van kenmerk toewijzingen van gebruikers aanpassen voor SaaS-toepassingen in azure Active Directory](customize-application-attributes.md).
- **Kenmerk toewijzingen voor groepen:** Het inrichten van de groeps naam en groeps gegevens, naast de leden, als deze worden ondersteund voor sommige toepassingen. U kunt deze functie in-of uitschakelen door de **toewijzing** voor groeps objecten die op het tabblad **inrichten** wordt weer gegeven in of uit te scha kelen. Als inrichtings groepen is ingeschakeld, moet u de kenmerk toewijzingen controleren om ervoor te zorgen dat er een geschikt veld wordt gebruikt voor de ' overeenkomende ID '. De overeenkomende ID kan de weergave naam of e-mail alias zijn. De groep en de bijbehorende leden worden niet ingericht als de overeenkomende eigenschap leeg is of niet is ingevuld voor een groep in azure AD.

## <a name="next-steps"></a>Volgende stappen

[Azure AD Connect Sync: uitleg over declaratieve inrichting](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
