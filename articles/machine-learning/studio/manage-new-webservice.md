---
title: Webservices beheren
titleSuffix: ML Studio (classic) - Azure
description: Beheer uw nieuwe en klassieke webservices voor Machine Learning via de Microsoft Azure Machine Learning Web Services-portal. Aangezien Classic Web services en New Web services zijn gebaseerd op verschillende onderliggende technologieën, hebt u voor elk van deze technologieën iets andere beheermogelijkheden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 2277aa3de5955efe5a3e4cb938fa557352f89006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217964"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Een webservice beheren met de Azure Machine Learning Studio (klassieke) Web Services-portal

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

U uw Machine Learning New and Classic Web services beheren via de Microsoft Azure Machine Learning Web Services-portal. Aangezien Classic Web services en New Web services zijn gebaseerd op verschillende onderliggende technologieën, hebt u voor elk van deze technologieën iets andere beheermogelijkheden.

In de Machine Learning Web Services-portal u:

* Controleer hoe de webservice wordt gebruikt.
* Configureer de beschrijving, werk de sleutels voor de webservice bij (alleen nieuw), werk uw opslagaccountsleutel bij (alleen nieuw), schakel logboekregistratie in en schakel voorbeeldgegevens in of schakel deze uit.
* Verwijder de webservice.
* Factureringsplannen maken, verwijderen of bijwerken (alleen nieuw).
* Eindpunten toevoegen en verwijderen (alleen klassiek)

>[!NOTE]
>U klassieke webservices ook beheren in [Machine Learning Studio (klassiek)](https://studio.azureml.net) op het tabblad **Webservices.**

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Machtigingen voor het beheren van webservices op basis van nieuwe resources manager

Nieuwe webservices worden geïmplementeerd als Azure-bronnen. Als zodanig moet u over de juiste machtigingen beschikken om nieuwe webservices te implementeren en te beheren.  Als u Nieuwe webservices wilt implementeren of beheren, moet u een bijdrager of beheerder toegewezen krijgen aan het abonnement waarvoor de webservice is geïmplementeerd. Als u een andere gebruiker uitnodigt voor een machine learning-werkruimte, moet u deze toewijzen aan een bijdrager of beheerdersrol in het abonnement voordat deze webservices kan implementeren of beheren. 

Als de gebruiker niet over de juiste machtigingen beschikt om toegang te krijgen tot bronnen in de Azure Machine Learning Web Services-portal, ontvangt deze gebruiker de volgende fout wanneer hij een webservice probeert te implementeren:

*De implementatie van webservice is mislukt. Dit account heeft onvoldoende toegang tot het Azure-abonnement dat de Workspace bevat. Als u een webservice wilt implementeren in Azure, moet hetzelfde account worden uitgenodigd voor de werkruimte en toegang krijgen tot het Azure-abonnement dat de werkruimte bevat.*

Zie [Een Azure Machine Learning Studio -werkruimte maken en delen](create-workspace.md)voor meer informatie over het maken van een werkruimte.

Zie [Toegang beheren met RBAC en de Azure-portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het instellen van toegangsmachtigingen.


## <a name="manage-new-web-services"></a>Nieuwe webservices beheren
Ga als volgende over het beheer van uw nieuwe webservices:

1. Meld u aan bij de [Microsoft Azure Machine Learning Web Services-portal](https://services.azureml.net/quickstart) met uw Microsoft Azure-account : gebruik het account dat is gekoppeld aan het Azure-abonnement.
2. Klik in het menu op **Webservices**.

Hier wordt een lijst met geïmplementeerde webservices voor uw abonnement weergegeven. 

Als u een webservice wilt beheren, klikt u op Webservices. Vanaf de webpagina WebServices u:

* Klik op de webservice om deze te beheren.
* Klik op het factureringsplan voor de webservice om deze bij te werken.
* Een webservice verwijderen.
* Kopieer een webservice en implementeer deze naar een andere regio.

Wanneer u op een webservice klikt, wordt de pagina Quickstart van de webservice geopend. De pagina Quickstart voor webservice heeft twee menuopties waarmee u uw webservice beheren:

* **DASHBOARD** - Hiermee u het gebruik van de webservice weergeven.
* **CONFIGUREREN** - Hiermee u beschrijvende tekst toevoegen, de sleutel bijwerken voor het opslagaccount dat is gekoppeld aan de webservice en voorbeeldgegevens in- of uitschakelen.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Controleren hoe de webservice wordt gebruikt
Klik op het tabblad **DASHBOARD.**

Vanuit het dashboard u het algehele gebruik van uw webservice gedurende een bepaalde periode bekijken. U de periode selecteren die u wilt weergeven in het vervolgkeuzemenu Periode rechtsboven in de gebruiksgrafieken. Het dashboard toont de volgende informatie:

* **Aanvragen in de loop van de tijd** geeft een stapgrafiek weer van het aantal aanvragen over de geselecteerde periode. Het kan helpen identificeren of u pieken in het gebruik ervaart.
* **Aanvragen voor aanvragen geeft** het totale aantal aanroepen voor aanvragen en antwoord weer die de service heeft ontvangen gedurende de geselecteerde periode en hoeveel van deze oproepen zijn mislukt.
* **De gemiddelde rekentijd van de aanvraagrespons** geeft een gemiddelde weer van de tijd die nodig is om de ontvangen aanvragen uit te voeren.
* **Batchaanvragen** geeft het totale aantal batchaanvragen weer dat de service heeft ontvangen gedurende de geselecteerde periode en hoeveel van deze aanvragen zijn mislukt.
* **De gemiddelde taaklatentie** geeft een gemiddelde weer van de tijd die nodig is om de ontvangen aanvragen uit te voeren.
* **In fouten** wordt het geaggregeerde aantal fouten weergegeven dat is opgetreden bij oproepen naar de webservice.
* **Serviceskosten** geven de kosten weer voor het factureringsplan dat aan de service is gekoppeld.

### <a name="configuring-the-web-service"></a>De webservice configureren
Klik op de **menuoptie CONFIGUREREN.**

U de volgende eigenschappen bijwerken:

* **Met** de beschrijving u een beschrijving voor de webservice invoeren.
* **Met** titel u een titel voor de webservice invoeren
* **Met toetsen** u uw primaire en secundaire API-sleutels roteren.
* **Met de opslagaccountsleutel** u de sleutel bijwerken voor het opslagaccount dat is gekoppeld aan de wijzigingen in de webservice. 
* **Met Voorbeeldgegevens** u voorbeeldgegevens verstrekken die u gebruiken om de service Verzoek-antwoord te testen. Als u de webservice hebt gemaakt in Machine Learning Studio (klassiek), worden de voorbeeldgegevens ontleend aan de gegevens die u hebt gebruikt om uw model te trainen. Als u de service programmatisch hebt gemaakt, worden de gegevens ontleend aan de voorbeeldgegevens die u hebt opgegeven als onderdeel van het JSON-pakket.

### <a name="managing-billing-plans"></a>Factureringsplannen beheren
Klik op de **menuoptie Plannen** op de quickstartpagina van webservices. U ook op het abonnement klikken dat is gekoppeld aan een specifieke webservice om dat abonnement te beheren.

* **Met Nieuw** u een nieuw plan maken.
* **Met de instantie Plan toevoegen/verwijderen** u een bestaand plan uitschalen om capaciteit toe te voegen.
* **Met Upgrade/DownGrade** u een bestaand plan opschalen om capaciteit toe te voegen.
* **Met Delete** u een abonnement verwijderen.

Klik op een plan om het dashboard weer te geven. Het dashboard geeft u een momentopname of plangebruik over een bepaalde periode. Als u de periode wilt selecteren die u wilt weergeven, klikt u op de **vervolgkeuzelijst Periode** rechtsboven in het dashboard. 

Het plandashboard bevat de volgende informatie:

* **Met de beschrijving van het** abonnement wordt informatie weergegeven over de kosten en capaciteit die aan het plan zijn gekoppeld.
* **Plangebruik** geeft het aantal transacties en rekenuren weer dat in rekening is gebracht tegen het plan.
* **Web Services** geeft het aantal webservices weer dat dit abonnement gebruikt.
* **Top Web Service By Calls** geeft de top vier webservices weer die gesprekken voeren die in rekening worden gebracht tegen het abonnement.
* **Top Web Services by Compute Hrs** geeft de vier beste webservices weer die rekenbronnen gebruiken die in rekening worden gebracht tegen het abonnement.

## <a name="manage-classic-web-services"></a>Klassieke webservices beheren
> [!NOTE]
> De procedures in deze sectie zijn relevant voor het beheer van klassieke webservices via de Azure Machine Learning Web Services-portal. Zie [Een Azure Machine Learning Studio -werkruimte beheren](manage-workspace.md)voor informatie over het beheren van klassieke webservices via de Machine Learning Studio (klassiek) en de Azure-portal.
> 
> 

Ga als u uw Klassieke Webservices beheren:

1. Meld u aan bij de [Microsoft Azure Machine Learning Web Services-portal](https://services.azureml.net/quickstart) met uw Microsoft Azure-account : gebruik het account dat is gekoppeld aan het Azure-abonnement.
2. Klik in het menu op **Klassieke webservices**.

Als u een klassieke webservice wilt beheren, klikt u op **Klassieke webservices**. Vanaf de pagina Classic Web Services u:

* Klik op de webservice om de bijbehorende eindpunten weer te geven.
* Een webservice verwijderen.

Wanneer u een Classic Web-service beheert, beheert u elk van de eindpunten afzonderlijk. Wanneer u op een webservice klikt op de webpagina Webservices, wordt de lijst met eindpunten die aan de service zijn gekoppeld, geopend. 

Op de pagina Classic Web Service-eindpunten u eindpunten aan de service toevoegen en verwijderen. Zie Eindpunten maken voor meer informatie over het toevoegen [van eindpunten](create-endpoint.md).

Klik op een van de eindpunten om de pagina Quickstart van de webservice te openen. Op de pagina Snelstart zijn er twee menu-opties waarmee u uw webservice beheren:

* **DASHBOARD** - Hiermee u het gebruik van de webservice weergeven.
* **CONFIGUREREN** - Hiermee u beschrijvende tekst toevoegen, foutlogboekregistratie in- en uitschakelen, de sleutel voor het opslagaccount bijwerken dat is gekoppeld aan de webservice en voorbeeldgegevens in- en uitschakelen.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Controleren hoe de webservice wordt gebruikt
Klik op het tabblad **DASHBOARD.**

Vanuit het dashboard u het algehele gebruik van uw webservice gedurende een bepaalde periode bekijken. U de periode selecteren die u wilt weergeven in het vervolgkeuzemenu Periode rechtsboven in de gebruiksgrafieken. Het dashboard toont de volgende informatie:

* **Aanvragen in de loop van de tijd** geeft een stapgrafiek weer van het aantal aanvragen over de geselecteerde periode. Het kan helpen identificeren of u pieken in het gebruik ervaart.
* **Aanvragen voor aanvragen geeft** het totale aantal aanroepen voor aanvragen en antwoord weer die de service heeft ontvangen gedurende de geselecteerde periode en hoeveel van deze oproepen zijn mislukt.
* **De gemiddelde rekentijd van de aanvraagrespons** geeft een gemiddelde weer van de tijd die nodig is om de ontvangen aanvragen uit te voeren.
* **Batchaanvragen** geeft het totale aantal batchaanvragen weer dat de service heeft ontvangen gedurende de geselecteerde periode en hoeveel van deze aanvragen zijn mislukt.
* **De gemiddelde taaklatentie** geeft een gemiddelde weer van de tijd die nodig is om de ontvangen aanvragen uit te voeren.
* **In fouten** wordt het geaggregeerde aantal fouten weergegeven dat is opgetreden bij oproepen naar de webservice.
* **Serviceskosten** geven de kosten weer voor het factureringsplan dat aan de service is gekoppeld.

### <a name="configuring-the-web-service"></a>De webservice configureren
Klik op de **menuoptie CONFIGUREREN.**

U de volgende eigenschappen bijwerken:

* **Met** de beschrijving u een beschrijving voor de webservice invoeren. Beschrijving is een vereist veld.
* **Met logboekregistratie** u foutlogboekregistratie op het eindpunt in- of uitschakelen. Zie Logboekregistratie inschakelen [voor Machine Learning-webservices voor](web-services-logging.md)meer informatie over logboekregistratie.
* **Met Voorbeeldgegevens** u voorbeeldgegevens verstrekken die u gebruiken om de service Verzoek-antwoord te testen. Als u de webservice hebt gemaakt in Machine Learning Studio (klassiek), worden de voorbeeldgegevens ontleend aan de gegevens die u hebt gebruikt om uw model te trainen. Als u de service programmatisch hebt gemaakt, worden de gegevens ontleend aan de voorbeeldgegevens die u hebt opgegeven als onderdeel van het JSON-pakket.


