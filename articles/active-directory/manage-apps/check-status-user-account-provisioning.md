---
title: Automatische gebruikers accounts inrichten voor SaaS-toepassingen | Microsoft Docs
description: Informatie over het controleren van de status van automatische toewijzing van gebruikers accounts en het oplossen van problemen met het inrichten van afzonderlijke gebruikers.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e5ef4067f22d0e9e015e4d9a646f8b92309010a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033536"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Zelfstudie: Rapportage over het automatisch inrichten van gebruikers accounts

Azure Active Directory (Azure AD) bevat een [service](user-provisioning.md) voor het inrichten van gebruikers accounts die helpt bij het automatiseren van het inrichten van gebruikers account in Saas-apps en andere systemen, met het oog op end-to-end identiteits levenscyclus beheer. Azure AD biedt ondersteuning voor vooraf geïntegreerde User Provisioning connectors voor alle toepassingen en systemen in de sectie ' Aanbevolen ' van de [Azure AD-toepassings galerie](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

In dit artikel wordt beschreven hoe u de status van inrichtings taken kunt controleren nadat deze zijn ingesteld en hoe u problemen met het inrichten van afzonderlijke gebruikers en groepen kunt oplossen.

## <a name="overview"></a>Overzicht

De inrichtings connectors worden ingesteld en geconfigureerd met behulp van de [Azure Portal](https://portal.azure.com), door de [meegeleverde documentatie](../saas-apps/tutorial-list.md) voor de ondersteunde toepassing te volgen. Zodra de configuratie is geconfigureerd en wordt uitgevoerd, kunnen er twee methoden worden gebruikt voor het inrichten van taken:

* **Azure Portal** : in dit artikel wordt voornamelijk beschreven hoe u rapport gegevens ophaalt op basis van de [Azure Portal](https://portal.azure.com), die zowel een samenvattings rapport voor de inrichting als gedetailleerde audit logboeken voor een bepaalde toepassing bevat.
* **Audit-API** -Azure Active Directory biedt ook een controle-API waarmee de gedetailleerde inrichtings audit logboeken kunnen worden opgehaald. Zie [Azure Active Directory audit API-naslag informatie](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) voor documentatie die specifiek is voor het gebruik van deze API. Hoewel dit artikel niet specifiek betrekking heeft op het gebruik van de API, worden de typen inrichtings gebeurtenissen die in het audit logboek zijn vastgelegd, gedetailleerd beschreven.

### <a name="definitions"></a>Definities

In dit artikel worden de volgende voor waarden gebruikt, zoals hieronder gedefinieerd:

* **Bron systeem** : de opslag plaats van gebruikers die de Azure AD Provisioning-service synchroniseert. Azure Active Directory is het bron systeem voor het meren deel van vooraf geïntegreerde inrichtings connectors. er zijn echter enkele uitzonde ringen (bijvoorbeeld: Werkdag inkomende synchronisatie).
* **Doel systeem** : de opslag plaats van gebruikers aan wie de Azure AD-inrichtings service synchroniseert. Dit is doorgaans een SaaS-toepassing (bijvoorbeeld: Sales Force, ServiceNow, G suite, Dropbox voor bedrijven, maar in sommige gevallen kan een on-premises systeem zijn, zoals Active Directory (bijvoorbeeld: Werkdag inkomende synchronisatie naar Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Inrichtings rapporten ophalen van de Azure Portal

Als u informatie over het inrichtings rapport voor een bepaalde toepassing wilt ophalen, start u de [Azure Portal](https://portal.azure.com) en **Azure Active Directory** &gt; **Enter prise apps** &gt; **Provisioning logs (preview)** in deDe sectie activiteit. U kunt ook bladeren naar de bedrijfs toepassing waarvoor het inrichten is geconfigureerd. Als u bijvoorbeeld gebruikers inricht voor LinkedIn, is het pad naar de details van de toepassing:

**Azure Active Directory > bedrijfs toepassingen > alle toepassingen > LinkedIn-uitbrei ding**

Hier kunt u toegang krijgen tot de voortgangs balk voor het inrichten en de inrichtings logboeken, zoals hieronder wordt beschreven.

## <a name="provisioning-progress-bar"></a>Voortgangs balk inrichten

De [voortgangs balk](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) voor het inrichten wordt weer gegeven op het tabblad **inrichten** voor de opgegeven toepassing. Deze bevindt zich in de **huidige status** sectie onder **instellingen**en toont de status van de huidige initiële of incrementele cyclus. In deze sectie ziet u ook:

* Het totale aantal gebruikers en/of groepen dat is gesynchroniseerd en zich momenteel in het bereik bevindt voor de inrichting van het bron systeem en het doel systeem.
* De laatste keer dat de synchronisatie is uitgevoerd. Synchronisaties worden meestal om de 20-40 minuten uitgevoerd nadat een [eerste cyclus](user-provisioning.md#what-happens-during-provisioning) is voltooid.
* Hiermee wordt aangegeven of een [eerste cyclus](user-provisioning.md#what-happens-during-provisioning) is voltooid.
* Of het inrichtings proces al dan niet in quarantaine is geplaatst en wat de reden voor de quarantaine status is (bijvoorbeeld fout bij het communiceren met het doel systeem als gevolg van ongeldige beheerders referenties).

De **huidige status** moet de eerste locatie beheerders zijn om de operationele status van de inrichtings taak te controleren.

 ![Samenvattend rapport](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Inrichtings Logboeken (preview-versie)

Alle activiteiten die worden uitgevoerd door de inrichtings service worden vastgelegd in de Azure AD- [inrichtings logboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). U kunt toegang krijgen tot de inrichtings Logboeken in de Azure portal door **Azure Active Directory** &gt; **Enter prise apps** &gt; **Provisioning logs (preview)** te selecteren in de sectie **activiteit** . U kunt de inrichtings gegevens zoeken op basis van de naam van de gebruiker of de id in het bron systeem of het doel systeem. Zie [inrichtings Logboeken (preview-versie)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)voor meer informatie. Gebeurtenis typen voor geregistreerde activiteiten zijn onder andere:

## <a name="troubleshooting"></a>Problemen oplossen

Het inrichtings samenvattings rapport en de inrichtings logboeken spelen een belang rijke rol om beheerders te helpen bij het oplossen van problemen met het inrichten van verschillende gebruikers accounts.

Zie problemen met het [configureren en inrichten van gebruikers voor een toepassing](application-provisioning-config-problem.md)voor op scenario's gebaseerde richt lijnen voor het oplossen van problemen met automatische gebruikers inrichting.

## <a name="additional-resources"></a>Aanvullende resources

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
