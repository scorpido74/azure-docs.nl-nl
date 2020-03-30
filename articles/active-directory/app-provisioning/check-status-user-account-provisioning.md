---
title: Automatische gebruikersaccountvoorziening rapporteren aan SaaS-toepassingen
description: Meer informatie over het controleren van de status van automatische gebruikersaccountinrichtingstaken en hoe u de inrichting van individuele gebruikers oplossen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19d76f69669ffa13d1d55ffa807e6c4818b8840c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282185"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Zelfstudie: Rapportage over het inrichten van automatische gebruikersaccounts

Azure Active Directory (Azure AD) bevat een service voor [het inrichten](user-provisioning.md) van gebruikersaccounts die helpt bij het automatiseren van de inrichting van gebruikersaccounts in SaaS-apps en andere systemen, met het oog op end-to-end identiteitslevenscyclusbeheer. Azure AD ondersteunt vooraf geïntegreerde gebruikersinrichtingsconnectors voor alle toepassingen en systemen met [gebruikersinrichtingstutorials hier.](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

In dit artikel wordt beschreven hoe u de status van het inrichten van taken controleren nadat deze zijn ingesteld en hoe u de inrichting van individuele gebruikers en groepen oplossen.

## <a name="overview"></a>Overzicht

Inrichtingsconnectors worden ingesteld en geconfigureerd met behulp van de [Azure-portal](https://portal.azure.com), door de [meegeleverde documentatie](../saas-apps/tutorial-list.md) voor de ondersteunde toepassing te volgen. Eenmaal geconfigureerd en uitgevoerd, kunnen inprovisioning-taken worden gerapporteerd over het gebruik van een van de twee methoden:

* **Azure-portal** - In dit artikel wordt voornamelijk beschreven in het ophalen van rapportgegevens uit de [Azure-portal,](https://portal.azure.com)die zowel een inrichtingsrapport als gedetailleerde inrichtingscontrolelogboeken voor een bepaalde toepassing biedt.
* **Audit API** - Azure Active Directory biedt ook een Audit API waarmee programmatisch kan worden opgehaald van de gedetailleerde provisioning auditlogs. Zie [Azure Active Directory audit API-referentie](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) voor documentatie die specifiek is voor het gebruik van deze API. Hoewel dit artikel niet specifiek betrekking heeft op het gebruik van de API, worden de typen inrichtingsgebeurtenissen beschreven die in het controlelogboek zijn geregistreerd.

### <a name="definitions"></a>Definities

In dit artikel worden de volgende termen gebruikt, die hieronder worden gedefinieerd:

* **Bronsysteem** - De opslagplaats van gebruikers waarvan de Azure AD-inrichtingsservice synchroniseert. Azure Active Directory is het bronsysteem voor de meeste vooraf geïntegreerde inrichtingsconnectors, maar er zijn enkele uitzonderingen (bijvoorbeeld Workday Inbound Synchronization).
* **Doelsysteem** - De opslagplaats van gebruikers waarmee de Azure AD-inrichtingsservice synchroniseert. Dit is meestal een SaaS-toepassing (bijvoorbeeld Salesforce, ServiceNow, G Suite, Dropbox for Business), maar kan in sommige gevallen een on-premises systeem zijn, zoals Active Directory (bijvoorbeeld: Workday Inbound Synchronization to Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Rapportages voor het inrichten van de Azure-portal ophalen

Als u rapportgegevens voor een bepaalde toepassing wilt inrichten, start u eerst de [Azure-portal](https://portal.azure.com) en **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning logs (preview)** in de sectie **Activiteit.** U ook naar de Ondernemingstoepassing bladeren waarvoor de inrichting is geconfigureerd. Als u gebruikers bijvoorbeeld instelt op LinkedIn Elevate, is het navigatiepad naar de toepassingsgegevens:

**Azure Active Directory > Enterprise-toepassingen > alle toepassingen > LinkedIn Elevate**

Vanaf hier hebt u toegang tot zowel de inrichtende voortgangsbalk als de inprovisioning logs, hieronder beschreven.

## <a name="provisioning-progress-bar"></a>Voortgangsbalk inrichten

De [voortgangsbalk voor het inrichten](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) is zichtbaar op het tabblad **Inname** voor een bepaalde toepassing. Het bevindt zich in de sectie **Huidige status** onder **Instellingen**en toont de status van de huidige initiële of incrementele cyclus. In deze sectie ziet u ook:

* Het totale aantal gebruikers en/of groepen dat is gesynchroniseerd en momenteel in de mogelijkheden is om te provisioneren tussen het bronsysteem en het doelsysteem.
* De laatste keer dat de synchronisatie is uitgevoerd. Synchronisaties komen meestal elke 20-40 minuten voor, nadat een [eerste cyclus](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) is voltooid.
* Of een [eerste cyclus](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) al dan niet is voltooid.
* Of het inrichtingsproces al dan niet in quarantaine is geplaatst en wat de reden voor de quarantainestatus is (bijvoorbeeld het niet communiceren met het doelsysteem vanwege ongeldige beheerdersreferenties).

De **huidige status** moet de eerste plaats admins kijken om te controleren op de operationele status van de inrichting taak.

 ![Samenvattend rapport](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Logboeken inrichten (voorbeeld)

Alle activiteiten die door de inrichtingsservice worden uitgevoerd, worden geregistreerd in de [azure AD-inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). U hebt toegang tot de inrichtingslogboeken in de Azure-portal door **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning logs (preview)** te selecteren in de sectie **Activiteit.** U de inrichtingsgegevens doorzoeken op basis van de naam van de gebruiker of de id in het bronsysteem of het doelsysteem. Zie [Logboeken inrichten (voorbeeld) voor](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)meer informatie. Log-gebeurtenistypen zijn:

## <a name="troubleshooting"></a>Problemen oplossen

Het inrichtingsoverzichtsrapport en de inrichtingslogboeken spelen een belangrijke rol bij het oplossen van verschillende problemen met het inrichten van gebruikersaccounts.

Zie [Problemen met configureren en inrichten van gebruikers voor een toepassing](../app-provisioning/application-provisioning-config-problem.md)voor op scenario's gebaseerde richtlijnen voor het oplossen van automatische gebruikersvoorzieningen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
