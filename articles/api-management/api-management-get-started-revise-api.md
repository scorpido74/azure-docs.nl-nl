---
title: Revisies gebruiken om vaste wijzigingen veilig in Azure API Management aan te brengen | Microsoft Docs
description: Volg de stappen van deze zelfstudie voor meer informatie over hoe u vaste wijzigingen aanbrengt met revisies in API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 811591a3340f07b08b3a954ccd475e926b73a9cf
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176772"
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>Revisies gebruiken om vaste wijzigingen veilig in Azure API Management aan te brengen
Wanneer uw API klaar is voor gebruik en daadwerkelijk wordt ingezet door ontwikkelaars, moet u op een bepaald moment wijzigingen aanbrengen aan die API, zonder dat dit gevolgen heeft voor clients die de API aanroepen. Het is ook handig om ontwikkelaars te informeren over de aangebrachte wijzigingen. We kunnen dit doen in Azure API Management met **revisies**. Zie voor meer informatie [Versies en revisies](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/) en [API-versies met Azure API Management](https://blogs.msdn.microsoft.com/apimanagement/2017/09/13/api-versioning-with-azure-api-management/).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe revisie toevoegen
> * Vaste wijzigingen aanbrengen in uw revisie
> * Uw revisie actualiseren en een logboekvermelding over de wijziging toevoegen
> * Door de portal voor ontwikkelaars browsen om de wijzigingen en het logboek in te zien

![Wijzigingenlogboek voor de ontwikkelaarsportal](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Vereisten

+ Informatie over de [terminologie van Azure API Management](api-management-terminology.md).
+ Lees de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
+ Voltooi tevens de volgende zelfstudie: [Uw eerste API importeren en publiceren](import-and-publish.md).

## <a name="add-a-new-revision"></a>Een nieuwe revisie toevoegen

![API-revisie toevoegen](media/api-management-getstarted-revise-api/07-AddRevisions-01-AddNewRevision.png)

1. Selecteer de pagina **API's**.
2. Selecteer **Demo Conference API** in de lijst met API's (of een andere API die u wilt wijzigen).
3. Klik op het tabblad **Revisies** in het menu aan de bovenkant van de pagina.
4. Selecteer **+ Revisie toevoegen**

    > [!TIP]
    > U kunt ook **Revisie toevoegen** kiezen uit het contextmenu ( **...** ) van de API.

5. Geef een beschrijving voor de nieuwe revisie, om te onthouden waar deze voor wordt gebruikt.
6. Selecteer **Maken**
7. Uw nieuwe revisie wordt nu gemaakt.

    > [!NOTE]
    > Uw oorspronkelijke API blijft in **Revisie 1**. Dit is de revisie die uw gebruikers blijven aanroepen, totdat u ervoor kiest om een andere revisie de huidige te maken.

## <a name="make-non-breaking-changes-to-your-revision"></a>Vaste wijzigingen aanbrengen in uw revisie

![Revisie wijzigen](media/api-management-getstarted-revise-api/07-AddRevisions-02-MakeChanges.png)

1. Selecteer **Demo Conference API** in de lijst met API's.
2. Selecteer boven in het scherm het tabblad **Ontwerp**.
3. U ziet dat de **revisieselector** (direct boven het tabblad Ontwerp) **Revisie 2** aangeeft als de huidige selectie.

    > [!TIP]
    > Gebruik de revisieselector om te schakelen tussen de wijzigingen waaraan u wilt werken.

4. Klik op **+ Bewerking toevoegen**.
5. Stel uw nieuwe bewerking in als **POST**, en de naam, weergavenaam en URL van de bewerking als **test**
6. **Sla** uw nieuwe bewerking op.
7. We hebben nu een wijziging aangebracht in **Revisie 2**. Gebruik de **Revisieselector** bovenaan op de pagina om over te schakelen naar **Revisie 1**.
8. Merk op dat uw nieuwe bewerking niet wordt weergegeven in **Revisie 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Uw revisie actualiseren en een logboekvermelding over de wijziging toevoegen

1. Klik op het tabblad **Revisies** in het menu aan de bovenkant van de pagina.

    ![Het menu Revisie op het scherm Revisie.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)

2. Open het contextmenu ( **...** ) voor **Revisie 2**.
3. Selecteer **Instellen als huidige**.
4. Controleer **Op het openbare wijzigingenlogboek voor deze API plaatsen**, als u opmerkingen over deze wijziging wilt plaatsen. Geef een beschrijving op voor de wijziging die ontwikkel aars kunnen zien, bijvoorbeeld: **revisies testen. Nieuwe test bewerking toegevoegd.**
5. **Revisie 2** is nu actueel.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Door de portal voor ontwikkelaars browsen om de wijzigingen en het logboek in te zien

1. Selecteer **API's** in de Azure-portal.
2. Selecteer **ontwikkelaarsportal** in het menu bovenaan.
3. Selecteer **API's** en selecteer vervolgens **Demo Conference API**.
4. U ziet dat uw nieuwe **test**bewerking nu beschikbaar is.
5. Klik op **wijzigingen logboek** in de buurt van de API-naam.
6. U ziet dat de vermelding van het wijzigingslogboek in deze lijst wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een nieuwe revisie toevoegen
> * Vaste wijzigingen aanbrengen in uw revisie
> * Uw revisie actualiseren en een logboekvermelding over de wijziging toevoegen
> * Door de portal voor ontwikkelaars browsen om de wijzigingen en het logboek in te zien

Ga door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Meerdere versies van uw API publiceren](api-management-get-started-publish-versions.md)
