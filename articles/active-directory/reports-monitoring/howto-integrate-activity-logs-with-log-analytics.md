---
title: Azure Active Directory-logboeken streamen naar Azure Monitor-logboeken | Microsoft Documenten
description: Meer informatie over het integreren van Azure Active Directory-logboeken met Azure Monitor-logboeken
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d0ef55f6f5117ffa77052118155afea716125a4
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639729"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Azure AD-logboeken integreren met Azure Monitor-logboeken

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Met Azure Monitor-logboeken u gegevens opvragen om bepaalde gebeurtenissen te vinden, trends te analyseren en correlatie staken tussen verschillende gegevensbronnen. Met de integratie van Azure AD-activiteitslogboeken in Azure Monitor-logboeken u nu taken uitvoeren zoals:

 * Uw Azure AD-aanmeldingslogboeken vergelijken met beveiligingslogboeken die zijn gepubliceerd door Azure Security Center

 * Problemen met prestaties oplossen op de aanmeldingspagina van uw toepassing door toepassingsprestatiegegevens van Azure Application Insights te correleren.  

De volgende video van een Ignite-sessie toont de voordelen van het gebruik van Azure Monitor-logboeken voor Azure AD-logboeken in praktische gebruikersscenario's.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

In dit artikel vindt u informatie over het integreren van Azure Active Directory -logboeken (Azure AD) met Azure Monitor.

## <a name="supported-reports"></a>Ondersteunde rapporten

U controleactiviteitslogboeken en aanmeldingsactiviteitslogboeken naar Azure Monitor-logboeken routeren voor verdere analyse. 

* **Auditlogboeken**: het [activiteitenrapport voor auditlogboeken](concept-audit-logs.md) biedt u toegang tot de geschiedenis van elke taak die in uw tenant is uitgevoerd.
* **Aanmeldingslogboeken**: met het [activiteitenrapport voor aanmeldingen](concept-sign-ins.md) kunt u bepalen wie de taken heeft uitgevoerd die in het auditlogboek zijn gerapporteerd.

> [!NOTE]
> Auditlogboeken en aanmeldingslogboeken met betrekking tot B2C worden momenteel niet ondersteund.
>

## <a name="prerequisites"></a>Vereisten 

U hebt het volgende nodig om deze functie te gebruiken:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, kunt u zich registreren voor een [gratis proefversie](https://azure.microsoft.com/free/).
* Een Azure AD-tenant.
* Een gebruiker die een *globale beheerder* of *beveiligingsbeheerder* voor de Azure-tenant is.
* Een Werkruimte logAnalytics in uw Azure-abonnement. Meer informatie over het [maken van een Log Analytics-werkruimte](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="licensing-requirements"></a>Licentievereisten

Voor het gebruik van deze functie is een Azure AD Premium P1- of P2-licentie vereist. Zie [Algemeen beschikbare functies van de edities Gratis, Basic en Premium vergelijken](https://azure.microsoft.com/pricing/details/active-directory/) als u een licentie zoekt die bij uw vereisten past.

## <a name="send-logs-to-azure-monitor"></a>Logboeken verzenden naar Azure Monitor

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 

2. Selecteer diagnostische**instellingen** -> voor **Azure Active Directory** > **Voeg diagnostische instelling toe.** U ook **Exportinstellingen** selecteren op de pagina **Controlelogboeken** of **Aanmeldingsom** naar de configuratiepagina voor diagnostische instellingen te gaan.  
    
3. Schakel **in** het menu Diagnostische instellingen het **selectievakje Werkruimte Verzenden naar Logboekanalyse** in en selecteer **Configureren.**

4. Selecteer de werkruimte Log Analytics waarnaar u de logboeken wilt verzenden of maak een nieuwe werkruimte in het opgegeven dialoogvenster.  

5. Voer een van de volgende bewerkingen uit, of beide:
    * Als u controlelogboeken naar de werkruimte Log Analytics wilt verzenden, schakelt u het selectievakje **AuditLogs** in. 
    * Als u aanmeldingslogboeken naar de werkruimte Log Analytics wilt verzenden, schakelt u het selectievakje **SignInLogs** in.

6. Selecteer **Opslaan** om de instelling op te slaan.

    ![Diagnostische instellingen](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Controleer na ongeveer 15 minuten of gebeurtenissen worden gestreamd naar uw log analytics-werkruimte.

## <a name="next-steps"></a>Volgende stappen

* [Azure AD-activiteitslogboeken analyseren met Azure Monitor-logboeken](howto-analyze-activity-logs-log-analytics.md)
* [De weergave van logboekanalyses voor Azure Active Directory installeren en gebruiken](howto-install-use-log-analytics-views.md)
