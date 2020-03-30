---
title: Probleem met het configureren van gebruikersinrichting naar een Azure AD Gallery-app
description: Algemene problemen oplossen bij het configureren van gebruikersinrichting voor een toepassing die al in de Azure AD Application Gallery is vermeld
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3955b96e4a7edfc79a229d927523bdd4473409d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522761"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Probleem met het configureren van gebruikersinrichting voor een Azure AD Gallery-toepassing

Voor het configureren van [automatische gebruikersinrichting](user-provisioning.md) voor een app (indien ondersteund) moeten specifieke instructies worden gevolgd om de toepassing voor te bereiden op automatische inrichting. Vervolgens u de Azure-portal gebruiken om de inrichtingsservice te configureren om gebruikersaccounts te synchroniseren met de toepassing.

U moet altijd beginnen met het vinden van de setup tutorial specifiek voor het opzetten van provisioning voor uw toepassing. Volg vervolgens deze stappen om zowel de app als Azure AD te configureren om de inrichtingsverbinding te maken. Een lijst met zelfstudies voor apps is te vinden in [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory.](../saas-apps/tutorial-list.md)

## <a name="how-to-see-if-provisioning-is-working"></a>Hoe om te zien of provisioning werkt 

Zodra de service is geconfigureerd, kunnen de meeste inzichten in de werking van de service worden getrokken uit twee plaatsen:

-   **Logboekregistratielogboeken (voorbeeld)** : de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) registreren alle bewerkingen die door de inrichtingsservice worden uitgevoerd, inclusief het opvragen van Azure AD voor toegewezen gebruikers die in het bereik van de inrichting zijn. Query de doel-app voor het bestaan van deze gebruikers, het vergelijken van de gebruikersobjecten tussen het systeem. Voeg vervolgens het gebruikersaccount toe, bij of schakel het uit in het doelsysteem op basis van de vergelijking. U hebt toegang tot de inrichtingslogboeken in de Azure-portal door **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning logs (preview)** te selecteren in de sectie **Activiteit.**

-   **Huidige status –** Een overzicht van de laatste inrichtingsuitvoering voor een bepaalde app is te zien in de sectie **Azure Active &gt; Directory Enterprise Apps &gt; \[Application Name\] &gt;Provisioning,** onder aan het scherm onder de service-instellingen. In de sectie Huidige status wordt weergegeven of een inrichtingscyclus is begonnen met het inrichten van gebruikersaccounts. U de voortgang van de cyclus bekijken, zien hoeveel gebruikers en groepen zijn ingericht en zien hoeveel rollen er zijn gemaakt. Als er fouten zijn, zijn details te vinden in de [Provisioning logs (.. /reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Algemene probleemgebieden met voorzieningen om te overwegen

Hieronder vindt u een lijst van de algemene probleemgebieden waarin u inzoomen als u een idee hebt van waar u moet beginnen.

* [De inddienst lijkt niet te starten](#provisioning-service-does-not-appear-to-start)
* Kan configuratie niet opslaan omdat app-referenties niet werken
* [Inprovisioning logs zeggen dat gebruikers worden "overgeslagen" en niet ingericht, ook al zijn ze toegewezen](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>De inddienst lijkt niet te starten

Als u de **inrichtingsstatus** instelt **in** de sectie **Azure Active &gt; Directory Enterprise Apps &gt; \[Application Name Provisioning\] &gt;** sectie van de Azure portal. Er worden echter geen andere statusdetails op die pagina weergegeven na volgende herladen. Het is waarschijnlijk dat de service wordt uitgevoerd, maar nog geen eerste cyclus heeft voltooid. Controleer de hierboven beschreven **indiklogboeken** om te bepalen welke bewerkingen de service uitvoert en of er fouten zijn.

>[!NOTE]
>Een eerste cyclus kan 20 minuten tot enkele uren duren, afhankelijk van de grootte van de Azure AD-map en het aantal gebruikers dat in staatis. Latere synchronisaties na de eerste cyclus zijn sneller, omdat de inrichtingsservice watermerken opslaat die de status van beide systemen na de eerste cyclus vertegenwoordigen, waardoor de prestaties van de volgende synchronisaties worden verbeterd.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Kan configuratie niet opslaan omdat app-referenties niet werken

Azure AD heeft geldige referenties nodig waarmee verbinding kan worden gemaakt met een API voor gebruikersbeheer die door die app wordt geleverd. Als deze referenties niet werken of als u niet weet wat ze zijn, bekijkt u de zelfstudie voor het instellen van deze eerder beschreven app.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Inprovisioning logs zeggen dat gebruikers worden overgeslagen en niet ingericht, ook al zijn ze toegewezen

Wanneer een gebruiker wordt weergegeven als "overgeslagen" in de provisioning logs, is het zeer belangrijk om de uitgebreide details in het logboekbericht te lezen om de reden te bepalen. Hieronder staan veelvoorkomende redenen en oplossingen:

- **Er is een scopingfilter geconfigureerd** **dat de gebruiker filtert op basis van een kenmerkwaarde.** Zie [Toepassingsinrichting op basis van toepassingen op basis van kenmerken met scopingfilters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor meer informatie.

- **De gebruiker is "niet effectief gerechtigd".** Als u dit specifieke foutbericht ziet, is dit omdat er een probleem is met de gebruikerstoewijzingsrecord die is opgeslagen in Azure AD. Als u dit probleem wilt oplossen, wijst u de gebruiker (of groep) uit de app af en wijst u het opnieuw toe. Zie [Een gebruiker of groep toewijzen aan een bedrijfsapp voor](../manage-apps/assign-user-or-group-access-portal.md)meer informatie .

- **Een vereist kenmerk ontbreekt of wordt niet ingevuld voor een gebruiker.** Een belangrijk ding om te overwegen bij het instellen van inrichting zijn het controleren en configureren van de attribuut toewijzingen en werkstromen die bepalen welke gebruiker (of groep) eigenschappen stromen van Azure AD naar de toepassing. Dit omvat het instellen van de "overeenkomende eigenschap" die wordt gebruikt om gebruikers/groepen tussen de twee systemen op unieke wijze te identificeren en te matchen. Zie [Het inrichten van kenmerktoewijzingen](../app-provisioning/customize-application-attributes.md)van gebruikers aanpassen voor meer informatie over dit belangrijke proces.

  * **Toewijzingen voor groepen:** Inrichten van de groepsnaam en groepsgegevens, naast de leden, indien ondersteund voor sommige toepassingen. U deze functionaliteit in- of uitschakelen door de **toewijzing** voor groepsobjecten in te schakelen of uit te schakelen die worden weergegeven op het tabblad **Inrichten.** Als inrichtingsgroepen is ingeschakeld, moet u de kenmerktoewijzingen controleren om ervoor te zorgen dat een geschikt veld wordt gebruikt voor de 'overeenkomende id'. Dit kan de weergavenaam of e-mailalias zijn), omdat de groep en haar leden niet zijn ingericht als de overeenkomende eigenschap leeg is of niet wordt ingevuld voor een groep in Azure AD.

## <a name="next-steps"></a>Volgende stappen
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatisch gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory)
