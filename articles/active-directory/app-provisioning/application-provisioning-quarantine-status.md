---
title: Toepassingsvoorzieningsstatus quarantaine | Microsoft Documenten
description: Wanneer u een toepassing voor automatische gebruikersinrichting hebt geconfigureerd, leest u wat een inrichtingsstatus van Quarantaine betekent en hoe u deze wissen.
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
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563c049bf3d1606e87db54e3b003dac987594610
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154624"
---
# <a name="application-provisioning-in-quarantine-status"></a>Toepassingsinrichting in quarantainestatus

De Azure AD-inrichtingsservice bewaakt de status van uw configuratie en plaatst ongezonde apps in een quarantainestatus. Als de meeste of alle oproepen tegen het doelsysteem consistent mislukken vanwege een fout, bijvoorbeeld ongeldige beheerdersreferenties, wordt de inrichtingstaak gemarkeerd als in quarantaine.

Terwijl in quarantaine, de frequentie van incrementele cycli wordt geleidelijk teruggebracht tot eenmaal per dag. De inrichtingstaak wordt uit quarantaine verwijderd nadat alle fouten zijn opgelost en de volgende synchronisatiecyclus wordt gestart. Als de inrichtingstaak langer dan vier weken in quarantaine blijft, is de inrichtingstaak uitgeschakeld (stopt met draaien).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Hoe weet ik of mijn aanvraag in quarantaine staat?

Er zijn drie manieren om te controleren of een toepassing in quarantaine is:
  
- Navigeer in de Azure-portal naar **Azure Active Directory** > **Enterprise-toepassingsnaam** > &lt;*application name*&gt; > **Provisioning** en blader naar de voortgangsbalk onderaan.  

  ![Statusbalk voor inrichten met quarantainestatus](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Navigeer in de Azure Portal naar **Azure Active Directory** > **Audit Logs** > filter op **Activiteit: quarantaine** en controleer de quarantainegeschiedenis. Terwijl de weergave in de voortgangsbalk zoals hierboven is beschreven, aangeeft of de inrichting momenteel in quarantaine is, u in de controlelogboeken de quarantainegeschiedenis voor een toepassing bekijken. 

- Gebruik de Microsoft Graph-aanvraag [Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) om programmatisch de status van de inrichtingstaak te krijgen:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Controleer uw e-mail. Wanneer een toepassing in quarantaine wordt geplaatst, wordt een eenmalige meldingsmail verzonden. Als de quarantainereden verandert, wordt een bijgewerkte e-mail verzonden met de nieuwe reden voor quarantaine. Als u geen e-mail ziet:

  - Zorg ervoor dat u een geldige **meldingse-mail** hebt opgegeven in de inrichtingsconfiguratie voor de toepassing.
  - Zorg ervoor dat er geen spamfilter t.m.v. de inbox van de meldingse-mail is.
  - Zorg ervoor dat je je niet hebt afgemeld voor e-mails.

## <a name="why-is-my-application-in-quarantine"></a>Waarom is mijn aanvraag in quarantaine?

In een Microsoft Graph-verzoek om de status van de inrichtingstaak te krijgen, wordt de volgende reden voor quarantaine weergegeven:

- `EncounteredQuarantineException`geeft aan dat ongeldige referenties zijn verstrekt. De inleveringsdienst kan geen verbinding tot stand brengen tussen het bronsysteem en het doelsysteem.

- `EncounteredEscrowProportionThreshold`geeft aan dat de voorziening de borgdrempel heeft overschreden. Deze voorwaarde treedt op wanneer meer dan 60% van de provisioning-gebeurtenissen zijn mislukt.

- `QuarantineOnDemand`betekent dat we een probleem met uw toepassing hebben ontdekt en handmatig in quarantaine hebben geplaatst.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Hoe haal ik mijn aanvraag uit quarantaine?

Los eerst het probleem op waardoor de toepassing in quarantaine is geplaatst.

- Controleer de inrichtingsinstellingen van de toepassing om te controleren of u [geldige beheerdersreferenties](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)hebt ingevoerd. Azure AD moet een vertrouwensrelatie met de doeltoepassing kunnen instellen. Zorg ervoor dat u geldige referenties hebt ingevoerd en dat uw account over de vereiste machtigingen beschikt.

- Controleer de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om verder te onderzoeken welke fouten quarantaine veroorzaken en de fout te verpakken. Toegang tot de inrichtingslogboeken in de Azure-portal door naar **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning logs (preview)** te gaan in de sectie **Activiteit.**

Nadat u het probleem hebt opgelost, start u de inrichtingstaak opnieuw. Bepaalde wijzigingen in de inrichtingsinstellingen van de toepassing, zoals toewijzingstoewijzingen of scopingfilters, worden automatisch opnieuw inrichten voor u. De voortgangsbalk op de **inrichtingspagina van** de toepassing geeft aan wanneer de inrichting voor het laatst is gestart. Als u de inrichtingstaak handmatig opnieuw wilt starten, gebruikt u een van de volgende methoden:  

- Gebruik de Azure-portal om de inrichtingstaak opnieuw te starten. Selecteer op de **inrichtingspagina van** de toepassing onder **Instellingen**de optie Status wissen en synchronisatie **opnieuw starten** en stel De **inrichtingsstatus** in **op Aan**. Met deze actie wordt de inprovisioningservice volledig opnieuw opgestart, wat enige tijd kan duren. Een volledige eerste cyclus wordt opnieuw uitgevoerd, waardoor borgs wordt gewist, de app uit quarantaine wordt verwijderd en eventuele watermerken worden gewist.

- Gebruik Microsoft Graph om [de inrichtingstaak opnieuw te starten.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) Je hebt volledige controle over wat je opnieuw start. U ervoor kiezen om borgsen te wissen (om de borgteller die naar de quarantainestatus wordt gegenereerd opnieuw op te starten), quarantaine vrij te maken (om de toepassing uit quarantaine te verwijderen) of watermerken wissen. Gebruik de volgende aanvraag:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
