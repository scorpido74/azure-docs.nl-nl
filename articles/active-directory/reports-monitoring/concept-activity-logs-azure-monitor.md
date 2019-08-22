---
title: Azure Active Directory activiteiten Logboeken in Azure Monitor | Microsoft Docs
description: Inleiding tot Azure Active Directory activiteiten Logboeken in Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/22/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f62ad020d2ec3b5ab712f50dca2dddd3b981f098
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656471"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Azure AD-activiteiten Logboeken in Azure Monitor

U kunt de activiteiten logboeken van Azure Active Directory (Azure AD) naar verschillende eind punten routeren voor lange termijn retentie en gegevens inzichten. Met deze functie kunt u het volgende doen:

* Archiveer Azure AD-activiteiten logboeken naar een Azure-opslag account om de gegevens lange tijd te bewaren.
* Stream Azure AD-activiteiten logboeken naar een Azure Event Hub voor analyse, met behulp van populaire Security Information and Event Management (SIEM)-hulpprogram ma's, zoals Splunk en QRadar.
* Integreer Azure AD-activiteiten logboeken met uw eigen aangepaste logboek oplossingen door ze te streamen naar een Event Hub.
* Verzend Azure AD-activiteiten logboeken naar Azure Monitor-Logboeken om uitgebreide visualisaties, bewaking en waarschuwingen voor de verbonden gegevens mogelijk te maken.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Ondersteunde rapporten

U kunt Azure AD-controle logboeken en aanmeldings logboeken door sturen naar uw Azure Storage-account, Event Hub, Azure Monitor Logboeken of aangepaste oplossing met behulp van deze functie. 

* **Audit logboeken**: Het [rapport activiteiten van controle logboeken](concept-audit-logs.md) geeft u toegang tot de geschiedenis van elke taak die wordt uitgevoerd in uw Tenant.
* **Aanmeld logboeken**: Met het [rapport aanmeldings activiteit](concept-sign-ins.md)kunt u bepalen wie de taken heeft uitgevoerd die worden gerapporteerd in de audit Logboeken.

> [!NOTE]
> Auditlogboeken en aanmeldingslogboeken met betrekking tot B2C worden momenteel niet ondersteund.
>

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze functie te gebruiken:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, kunt u zich registreren voor een [gratis proefversie](https://azure.microsoft.com/free/).
* Een [licentie](https://azure.microsoft.com/pricing/details/active-directory/) voor Azure AD Free, Basic, Premium 1 of Premium 2 voor toegang tot de Azure AD-auditlogboeken in de Azure-portal. 
* Een Azure AD-tenant.
* Een gebruiker die een **globale beheerder** of **beveiligingsbeheerder** voor de Azure-tenant is.
* Een [licentie](https://azure.microsoft.com/pricing/details/active-directory/) voor Azure AD Premium 1 of Premium 2 voor toegang tot de Azure AD-aanmeldingslogboeken in de Azure-portal. 

Afhankelijk van waarnaar u uw auditlogboekgegevens wilt doorsturen, hebt u het volgende nodig:

* Een Azure-opslagaccount waarop u *ListKeys*-machtigingen hebt. We raden u aan om een algemeen opslagaccount te gebruiken en geen Blob Storage-account. Raadpleeg de [Azure Storage-prijscalculator](https://azure.microsoft.com/pricing/calculator/?service=storage) voor opslagprijzen. 
* Een Azure Event Hubs-naamruimte om te integreren met oplossingen van derden.
* Een Azure Log Analytics-werk ruimte om logboeken naar Azure Monitor-logboeken te verzenden.

## <a name="cost-considerations"></a>Kostenoverwegingen

Als u al een Azure AD-licentie hebt, moet u een Azure-abonnement hebben om het opslagaccount en de Event Hub op te zetten. Het Azure-abonnement is gratis, maar u moet betalen om Azure-resources te gebruiken, waaronder het opslagaccount dat u gebruikt om te archiveren en de Event Hub die u gebruikt om te streamen. De hoeveelheid gegevens, en dus de in rekening gebrachte kosten, variëren sterk, afhankelijk van de grootte van de tenant. 

### <a name="storage-size-for-activity-logs"></a>Opslaggrootte voor activiteitenlogboeken

Elke auditlogboekgebeurtenis neemt ongeveer 2 KB aan opslagruimte in beslag. Gebeurtenis logboeken voor aanmelden zijn ongeveer 4 KB aan gegevens opslag. Zo hebt u voor een tenant met 100.000 gebruikers, wat neerkomt op ongeveer 1,5 miljoen gebeurtenissen per dag, ongeveer 3 GB aan gegevensopslag per dag nodig. Aangezien schrijfbewerkingen in batches van ongeveer 5 minuten voorkomen, kunt u om en nabij 9000 schrijfbewerkingen per maand verwachten. 


De volgende tabel bevat een kostenraming, afhankelijk van de grootte van de tenant, voor een opslagaccount voor algemeen gebruik v2 in US - west met een bewaringstermijn van ten minste één jaar. Gebruik de [Azure Storage-prijscalculator](https://azure.microsoft.com/pricing/details/storage/blobs/) om een meer nauwkeurige inschatting te maken van het gegevensvolume dat u denkt te gebruiken voor uw toepassing.


| Logboekcategorie | Aantal gebruikers | Gebeurtenissen per dag | Gegevensvolume per maand (geschat) | Kosten per maand (geschat) | Kosten per jaar (geschat) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Controleren | 100,000 | 1,5&nbsp;miljoen | 90 GB | $ 1,93 | $ 23,12 |
| Controleren | 1000 | 15,000 | 900 MB | $ 0,02 | $ 0,24 |
| Aanmeldingen | 1000 | 34.800 | 4 GB | $ 0,13 | $ 1,56 |
| Aanmeldingen | 100,000 | 15&nbsp;miljoen | 1,7 TB | $ 35,41 | $ 424,92 |
 









### <a name="event-hub-messages-for-activity-logs"></a>Event Hub-berichten voor activiteitenlogboeken

Gebeurtenissen worden ongeveer vijf minuten lang gebatched en vervolgens als één bericht met alle gebeurtenissen in die vijf minuten verstuurd. Een bericht in de Event Hub heeft een maximale grootte van 256 kB en als de totale grootte van alle berichten binnen de vijf minuten die grootte overstijgt, worden er meerdere berichten gestuurd. 

Zo vinden er voor een grote tenant met meer dan 100.000 gebruikers bijvoorbeeld doorgaans 18 gebeurtenissen per seconde plaats, een aantal dat overeenkomt met 5400 gebeurtenissen per vijf minuten. Omdat auditlogboeken ongeveer 2 kB per gebeurtenis groot zijn, komt dit overeen met 10,8 MB aan gegevens. Dat is de reden waarom er met die interval van 5 minuten 43 berichten naar de Event Hub worden gestuurd. 

De volgende tabel bevat een raming van de maandelijkse kosten voor een eenvoudige Event Hub in US - west, afhankelijk van het volume van de gebeurtenisgegevens. Gebruik de [Event Hubs-prijscalculator](https://azure.microsoft.com/pricing/details/event-hubs/) om een nauwkeurige inschatting te berekenen voor het gegevensvolume dat u voor uw toepassing verwacht.

| Logboekcategorie | Aantal gebruikers | Gebeurtenissen per seconde | Gebeurtenissen met een interval van vijf minuten | Volume per interval | Berichten per interval | Berichten per maand | Kosten per maand (geschat) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Controleren | 100,000 | 18 | 5400 | 10,8 MB | 43 | 371.520 | $ 10,83 |
| Controleren | 1000 | 0.1 | 52 | 104 kB | 1 | 8640 | $ 10,80 |
| Aanmeldingen | 1000 | 178 | 53.400 | 106,8&nbsp;MB | 418 | 3\.611.520 | $ 11,06 |  

### <a name="azure-monitor-logs-cost-considerations"></a>Kosten overwegingen voor Azure Monitor-logboeken



| Logboekcategorie       | Aantal gebruikers | Gebeurtenissen per dag | Gebeurtenissen per maand (30 dagen) | Kosten per maand in USD (EST.) |
| :--                | ---             | ---            | ---                        | --:                          |
| Controle en aanmeldingen | 100,000         | 16.500.000     | 495.000.000                |  $1093,00                       |
| Controleren              | 100,000         | 1\.500.000      | 45,000,000                 |  $246,66                     |
| Aanmeldingen           | 100,000         | 15,000,000     | 450.000.000                |  $847,28                     |










Als u de kosten voor het beheren van de Azure Monitor logboeken wilt bekijken, raadpleegt u [kosten beheren door het gegevens volume en de retentie in azure monitor logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-cost-storage)te beheren.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

Deze sectie bevat antwoorden op veelgestelde vragen en bekende problemen met betrekking tot Azure AD-logboeken in Azure Monitor.

**V: Welke logboeken zijn opgenomen?**

**A**: De logboeken en audit logboeken van de aanmeldings activiteiten zijn beide beschikbaar voor route ring via deze functie, hoewel B2C controle gebeurtenissen momenteel niet zijn opgenomen. Raadpleeg het [auditlogboekschema](reference-azure-monitor-audit-log-schema.md) en het [aanmeldingslogboekschema](reference-azure-monitor-sign-ins-log-schema.md) om uit te vinden welke typen logboeken en welke op functie gebaseerde logboeken momenteel worden ondersteund. 

---

**V: Hoe snel na een actie worden de bijbehorende logboeken weer gegeven in mijn Event Hub?**

**A**: De logboeken moeten binnen twee tot vijf minuten na het uitvoeren van de actie in uw Event Hub worden weer gegeven. Raadpleeg [Wat is Azure Event Hubs?](../../event-hubs/event-hubs-about.md) voor meer informatie over Event Hubs.

---

**V: Hoe snel na een actie worden de bijbehorende logboeken weer gegeven in mijn opslag account?**

**A**: Voor Azure Storage-accounts is de latentie een periode van 5 tot 15 minuten nadat de actie is uitgevoerd.

---

**V: Wat gebeurt er als een beheerder de Bewaar periode van een diagnostische instelling wijzigt?**

**A**: Het nieuwe Bewaar beleid wordt toegepast op Logboeken die na de wijziging zijn verzameld. Logboeken die worden verzameld voordat het beleid wordt gewijzigd, worden niet beïnvloed.

---

**V: Wat zijn de kosten voor het opslaan van mijn gegevens?**

**A**: De opslag kosten zijn afhankelijk van de grootte van uw logboeken en de retentie periode die u kiest. Raadpleeg de sectie [Opslaggrootte voor activiteitenlogboeken](#storage-size-for-activity-logs) voor een lijst van de geschatte kosten voor tenants. De kosten zijn afhankelijk van het aantal logboeken dat wordt gegenereerd.

---

**V: Hoeveel kost het om mijn gegevens te streamen naar een Event Hub?**

**A**: De kosten voor streaming zijn afhankelijk van het aantal berichten dat u per minuut ontvangt. In dit artikel wordt beschreven hoe de kosten worden berekend en vindt u een lijst met geschatte kosten, die zijn gebaseerd op het aantal berichten. 

---

**V: Hoe kan ik Azure AD-activiteiten logboeken integreren met mijn SIEM-systeem?**

**A**: U kunt dit op twee manieren doen:

- Azure Monitor gebruiken met Event Hubs om logboeken naar uw SIEM-systeem te streamen. [Stream de logboeken naar een event hub](tutorial-azure-monitor-stream-logs-to-event-hub.md) en [stel vervolgens uw SIEM-hulpprogramma](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub) in met de geconfigureerde event hub. 

- Gebruik de [Reporting Graph API](concept-reporting-api.md) voor toegang tot de gegevens. Push de gegevens daarna naar uw SIEM-systeem met uw eigen scripts.

---

**V: Welke SIEM-hulpprogram ma's worden momenteel ondersteund?** 

**A**: Momenteel wordt Azure Monitor ondersteund door de logica [Splunk](tutorial-integrate-activity-logs-with-splunk.md), QRadar en [Sumo](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory). Raadpleeg [Azure-bewakingsgegevens streamen naar een Event Hub voor gebruik door een extern hulpprogramma](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md) voor meer informatie over hoe de connectors werken.

---

**V: Hoe kan ik Azure AD-activiteiten logboeken integreren met mijn Splunk-exemplaar?**

**A**: Stuur eerst [de Azure AD-activiteiten logboeken naar een event hub](quickstart-azure-monitor-stream-logs-to-event-hub.md)en volg de stappen om [activiteiten logboeken te integreren met Splunk](tutorial-integrate-activity-logs-with-splunk.md).

---

**V: Hoe kan ik Azure AD-activiteiten logboeken integreren met Sumo Logic?** 

**A**: U moet eerst [de Azure AD-activiteiten logboeken naar een event hub routeren](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory)en vervolgens de stappen volgen om [de Azure AD-toepassing te installeren en de Dash boards in SumoLogic te bekijken](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

---

**V: Kan ik toegang krijgen tot de gegevens van een Event Hub zonder een extern SIEM-hulp programma te gebruiken?** 

**A**: Ja. U kunt de [Event Hubs-API](../../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) gebruiken om de logboeken vanuit uw eigen aangepaste toepassing te bekijken. 

---


## <a name="next-steps"></a>Volgende stappen

* [Activiteitenlogboeken in een opslagaccount archiveren](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Activiteitenlogboeken doorsturen naar een Event Hub](quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [Activiteiten logboeken integreren met Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)
