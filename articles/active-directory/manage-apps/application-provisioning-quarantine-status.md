---
title: Toepassings inrichtings status van quarantaine | Microsoft Docs
description: Wanneer u een toepassing voor het automatisch inrichten van gebruikers hebt geconfigureerd, leest u wat een inrichtings status in quarantaine betekent en hoe u deze wist.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3ad689fb57c51d0deb698a723b93e6175bdbb5c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73882879"
---
# <a name="application-provisioning-in-quarantine-status"></a>Toepassing inrichten in quarantaine status

De Azure AD-inrichtings service bewaakt de status van uw configuratie en plaatst de beschadigde apps in de status ' quarantaine '. Als de meeste of alle aanroepen voor het doel systeem consistent mislukken vanwege een fout, bijvoorbeeld ongeldige beheerders referenties, wordt de inrichtings taak gemarkeerd als in quarantaine.

In quarantaine wordt de frequentie van incrementele cycli geleidelijk per dag gereduceerd. De inrichtings taak wordt uit quarantaine gehaald nadat alle fouten zijn opgelost en de volgende synchronisatie cyclus wordt gestart. Als de inrichtings taak langer dan vier weken in quarantaine blijft, wordt de inrichtings taak uitgeschakeld (stopt met uitvoeren).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Hoe kan ik weet of mijn toepassing in quarantaine is?

Er zijn drie manieren om te controleren of een toepassing in quarantaine is geplaatst:
  
- Ga in het Azure Portal naar **Azure Active Directory** > **zakelijke toepassingen** > &lt;*toepassings naam*&gt; > **inrichten** en schuif naar de voortgangs balk aan de onderkant.  

  ![Status balk van de inrichting met de status van de quarantaine](media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Gebruik de Microsoft Graph aanvraag [Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) om programmatisch de status van de inrichtings taak op te halen:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Controleer uw e-mail adres. Wanneer een toepassing in quarantaine wordt geplaatst, wordt er een eenmalige e-mail melding verzonden. Als de quarantaine reden wordt gewijzigd, wordt een bijgewerkt e-mail bericht verzonden met de nieuwe reden voor quarantaine. Als u geen e-mail bericht ziet:

  - Zorg ervoor dat u een geldig **e-mail adres voor meldingen** hebt opgegeven in de inrichtings configuratie voor de toepassing.
  - Zorg ervoor dat er geen spam filtering is in het postvak in van de meldings-e-mail.
  - Zorg ervoor dat u zich niet afmeldt bij e-mail berichten.

## <a name="why-is-my-application-in-quarantine"></a>Waarom wordt mijn toepassing in quarantaine geplaatst?

Een Microsoft Graph aanvraag voor het ophalen van de status van de inrichtings taak bevat de volgende reden voor quarantaine:

- `EncounteredQuarantineException` geeft aan dat er ongeldige referenties zijn geleverd. De inrichtings service kan geen verbinding tot stand brengen tussen het bron systeem en het doel systeem.

- `EncounteredEscrowProportionThreshold` geeft aan dat het inrichten de borg drempel heeft overschreden. Dit probleem treedt op wanneer meer dan 60% van de inrichtings gebeurtenissen mislukt.

- `QuarantineOnDemand` betekent dat er een probleem is gedetecteerd met uw toepassing en dat deze hand matig is ingesteld op quarantaine.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Hoe kan ik mijn toepassing uit quarantaine halen?

Los eerst het probleem op dat ertoe heeft geleid dat de toepassing in quarantaine is geplaatst.

- Controleer de inrichtings instellingen van de toepassing om er zeker van te zijn dat u [geldige beheerders referenties hebt ingevoerd](configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD moet in staat zijn om een vertrouwens relatie met de doel toepassing tot stand te brengen. Zorg ervoor dat u geldige referenties hebt ingevoerd en dat uw account over de benodigde machtigingen beschikt.

- Controleer de [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te onderzoeken welke fouten in quarantaine worden veroorzaakt en los de fout op. Toegang tot de inrichtings Logboeken in de Azure Portal door te gaan naar **Azure Active Directory** &gt; **enter prise apps** &gt; **inrichtings Logboeken (preview)** in de sectie **activiteit** .

Nadat u het probleem hebt opgelost, start u de inrichtings taak opnieuw. Bepaalde wijzigingen in de inrichtings instellingen van de toepassing, zoals kenmerk toewijzingen of bereik filters, worden automatisch opnieuw opgestart. De voortgangs balk op de **inrichtings** pagina van de toepassing geeft aan wanneer het inrichten voor het laatst is gestart. Als u de inrichtings taak hand matig opnieuw moet starten, gebruikt u een van de volgende methoden:  

- Gebruik de Azure Portal om de inrichtings taak opnieuw te starten. Op de **inrichtings** pagina van de toepassing onder **instellingen**selecteert u **status wissen en start u de synchronisatie opnieuw** en stelt u de **inrichtings status** **in op aan**. Met deze actie wordt de inrichtings service volledig opnieuw gestart. Dit kan enige tijd duren. Er wordt opnieuw een volledige eerste cyclus uitgevoerd. Hiermee wist u de toepassing, worden de apps uit quarantaine verwijderd en worden eventuele water merken gewist.

- Gebruik Microsoft Graph om [de inrichtings taak opnieuw te starten](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). U hebt volledige controle over wat u opnieuw opstart. U kunt ervoor kiezen om de borg te wissen (om de borg teller voor de quarantaine status opnieuw te starten), de quarantaine te wissen (de toepassing uit quarantaine te verwijderen) of door water merken te wissen. Gebruik de volgende aanvraag:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`