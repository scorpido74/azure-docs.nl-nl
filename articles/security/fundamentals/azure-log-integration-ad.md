---
title: Azure Log Integration met Azure Active Directory-controle logboeken | Microsoft Docs
description: Meer informatie over het installeren van de Azure Log Integration-service en het integreren van Logboeken in azure audit logs
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: e7f3a9bc9aade5f8ade857e9c2f14f8e9898a7da
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244677"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Azure Active Directory audit logboeken integreren

Met controle gebeurtenissen van Azure Active Directory (Azure AD) kunt u geprivilegieerde acties identificeren die zijn opgetreden in Azure Active Directory. U kunt de typen gebeurtenissen zien die u kunt volgen door [Azure Active Directory controle rapport gebeurtenissen](../../active-directory/reports-monitoring/concept-audit-logs.md)te controleren.


>[!IMPORTANT]
> De Azure-functie voor logboek integratie wordt afgeschaft door 06/15/2019. AzLog-down loads zijn op 27 juni 2018 uitgeschakeld. Raadpleeg voor meer informatie over wat u verder kunt doen met [Azure monitor om te integreren met Siem-hulpprogram ma's](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Stappen voor het integreren van Azure Active Directory audit logboeken

> [!NOTE]
> Voordat u de stappen in dit artikel probeert uit te voeren, moet u het artikel [aan de slag](azure-log-integration-get-started.md) bekijken en de relevante stappen daar volt ooien.

1. Open de opdracht prompt en voer deze opdracht uit:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Voer deze opdracht uit: 
 
   ``azlog createazureid``

   Met deze opdracht wordt u gevraagd om uw Azure-aanmelding. De opdracht maakt vervolgens een Azure Active Directory Service-Principal in de Azure AD-tenants die als host fungeren voor de Azure-abonnementen waarin de aangemelde gebruiker een beheerder, een mede beheerder of een eigenaar is. Als de aangemelde gebruiker alleen een gast gebruiker in de Azure AD-Tenant is, mislukt de opdracht. Verificatie voor Azure wordt uitgevoerd via Azure AD. Het maken van een service-principal voor Azure Log Integration maakt de Azure AD-identiteit die toegang krijgt voor het lezen van Azure-abonnementen.

3. Voer de volgende opdracht uit om uw Tenant-ID op te geven. U moet lid zijn van de rol Tenant beheerder om de opdracht uit te voeren.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Voorbeeld:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Controleer de volgende mappen om te controleren of de Azure Active Directory de JSON-bestanden van het audit logboek in deze zijn gemaakt:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

In de volgende video ziet u de stappen die in dit artikel worden behandeld:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Neem contact op met de leverancier van uw SIEM voor specifieke instructies over het inbrengen van de informatie in de JSON-bestanden in uw SIEM-systeem (Security Information and Event Management).

Community-ondersteuning is beschikbaar via het [MSDN-forum van Azure log Integration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Met dit forum kunnen mensen in de Azure Log Integration Community elkaar ondersteunen met behulp van vragen, antwoorden, tips en trucs. Daarnaast controleert het Azure Log Integration-team dit forum en kunt u altijd op elk gewenst moment.

U kunt ook een [ondersteunings aanvraag](../../azure-supportability/how-to-create-azure-support-request.md)openen. Selecteer **integratie van Logboeken** als de service waarvoor u ondersteuning aanvraagt.

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over Azure Log Integration raadpleegt u:

* [Microsoft Azure log Integration voor Azure-logboeken](https://azure.microsoft.com/services/monitor/): deze download centrum pagina bevat details, systeem vereisten en installatie-instructies voor Azure log Integration.
* [Inleiding tot Azure log Integration](azure-log-integration-overview.md): in dit artikel wordt beschreven hoe u kunt Azure log Integration, de belangrijkste mogelijkheden en hoe het werkt.
* [Azure log Integration Veelgestelde vragen](azure-log-integration-faq.md): in dit artikel vindt u antwoorden op vragen over Azure log Integration.
* [Nieuwe functies voor de controle logboeken van Azure Diagnostics en Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): in dit blog bericht wordt u gekennismaken met Azure audit logs en andere functies die u helpen inzicht te krijgen in de bewerkingen van uw Azure-resources.
