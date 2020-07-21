---
title: Versies van uw API met Azure API Management publiceren | Microsoft Docs
description: Volg de stappen in deze zelfstudie voor informatie over het publiceren van meerdere versies in Azure API Management.
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
ms.openlocfilehash: b683910180e597cb8cbfa642bb2d9ac3200b42ee
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86255027"
---
# <a name="publish-multiple-versions-of-your-api"></a>Meerdere versies van uw API publiceren 

Er zijn tijden wanneer het niet praktisch is dat alle aanroepers voor uw API dezelfde versie gebruiken. Wanneer aanroepers willen upgraden naar een nieuwere versie willen ze dit met een gemakkelijk te begrijpen benadering kunnen doen. Het is mogelijk om dit te doen met behulp van **Versies** in Azure API Management. Zie voor meer informatie [Versies en revisies](https://azure.microsoft.com/blog/versions-revisions/).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe versie toevoegen aan een bestaande API
> * Een versieschema kiezen
> * Voeg de versie toe aan een product
> * Blader door de portal voor ontwikkelaars om de versie te zien

![Versie die wordt weergegeven op de portal voor ontwikkelaars](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>Vereisten

+ Informatie over de [terminologie van Azure API Management](api-management-terminology.md).
+ Voltooi de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
+ Voltooi ook de volgende zelfstudie: [Uw eerste API importeren en publiceren](import-and-publish.md).

## <a name="add-a-new-version"></a>Een nieuwe versie toevoegen

![Contextmenu van API - versie toevoegen](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. Selecteer **Demo Conference API** in de lijst met API's.
2. Selecteer het contextmenu ( **...** ) ernaast.
3. Selecteer **+ Versie toevoegen**.

> [!TIP]
> Versies kunnen ook worden ingeschakeld wanneer u eerst een nieuwe API maakt - selecteer **Versie van deze API?** op het scherm **API toevoegen**.

## <a name="choose-a-versioning-scheme"></a>Kies een versiebeheerschema

Met Azure API Management kunt u de manier kiezen waarop u het aanroepers mogelijk maakt om op te geven welke versie van uw API ze willen. U geeft op welke API-versie moet worden gebruikt door het selecteren van een **Versiebeheerschema**. Dit schema kan **pad, koptekst of query-tekenreeks**zijn. In het volgende voorbeeld wordt pad gebruikt voor het selecteren van het versieschema.

![Versiescherm toevoegen](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. Laat **pad** geselecteerd als uw **versiebeheerschema**.
2. Typ **demo-conference-api-v1** in het veld **Naam**.

    > [!NOTE]
    > Versie is in feite een nieuwe API die is gebaseerd op een revisie van de API. **Naam** is de naam van de nieuwe API en deze moet uniek zijn binnen het API Management-exemplaar.

3. Typ **v1** in het veld **Versie-id**.

    > [!TIP]
    > Als u **header** of **querytekenreeks** selecteert als een versiebeheerschema, moet u een extra waarde opgeven: de naam van de header of query-tekenreeksparameter.

4. Selecteer **Maken** voor het instellen van uw nieuwe versie.
5. Onder **Demo Conference API** in de lijst met API's ziet u nu twee verschillende API's: **Origineel** en **v1**.

    ![Versies vermeld in een API in Azure Portal](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Als u een versie aan een niet-samengestelde API toevoegt, wordt automatisch een **Origineel** gemaakt dat reageert op de standaard-URL. Dit zorgt ervoor dat eventuele bestaande aanroepfuncties niet worden onderbroken door het proces van het toevoegen van een versie. Als u een nieuwe API met versies maakt die aan het begin zijn ingeschakeld, wordt geen Origineel gemaakt.

6. U kunt nu **v1** bewerken en configureren als een API die verschilt van het **Origineel**. Wijzigingen in één versie hebben geen invloed op een andere.

## <a name="add-the-version-to-a-product"></a>Voeg de versie toe aan een product

Als aanroepers de nieuwe versie willen zien, moet deze worden toegevoegd aan een **product**.

![API Management-producten](media/api-management-getstarted-publish-versions/08-AddMultipleVersions-03-AddVersionToProduct.png)

1. Selecteer **Producten** via de klassieke implementatiemodel-pagina.
2. Selecteer **Onbeperkt**.
3. Selecteer **API's**.
4. Selecteer **Toevoegen**.
5. Selecteer **Demo Conference API, versie v1**.
6. Klik op **Selecteren**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Blader door de portal voor ontwikkelaars om de versie te zien

1. Selecteer **ontwikkelaarsportal** in het menu bovenaan.
2. Selecteer **API's** en klik op de **Demo Conference API**.
3. U ziet een vervolgkeuzelijst met meerdere versies naast de naam van de API.
4. Selecteer **v1**.
5. U ziet de **Verzoek-URL** van de eerste bewerking in de lijst. Het laat zien dat het API URL-pad **v1** bevat.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe versie toevoegen aan een bestaande API
> * Een versieschema kiezen 
> * Voeg de versie toe aan een product
> * Blader door de portal voor ontwikkelaars om de versie te zien

Ga door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [De stijl van de pagina's van de ontwikkelaarsportal aanpassen](api-management-customize-styles.md)
