---
title: Een aangepast CA-certificaat toevoegen - Azure API-beheer | Microsoft Documenten
description: Meer informatie over het toevoegen van een aangepast CA-certificaat in Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 21d5869f2bcdfb6383b6ef89869d8098135ea7ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073608"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Een aangepast CA-certificaat toevoegen in Azure API Management

Azure API Management maakt het installeren van CA-certificaten op de machine mogelijk in de vertrouwde root- en intermediate certificaatopslag. Deze functionaliteit moet worden gebruikt als voor uw services een aangepast CA-certificaat vereist is.

In het artikel ziet u hoe u CA-certificaten van een Azure API Management-serviceinstantie beheert in de Azure-portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Een CA-certificaat uploaden

![CA-certificaten toevoegen](media/api-management-howto-ca-certificates/00.png)

Volg de onderstaande stappen om een nieuw CA-certificaat te uploaden. Als u nog geen API Management-serviceinstantie hebt gemaakt, raadpleegt u de zelfstudie [Een API Management-serviceinstantie maken.](get-started-create-service-instance.md)

1. Navigeer naar de instantie azure API Management-service in de Azure-portal.

2. Selecteer **CA-certificaten** in het menu.

3. Klik op de knop **+ Toevoegen**.  

    ![CA-certificaten toevoegen](media/api-management-howto-ca-certificates/01.png)  

4. Blader naar het certificaat en bepaal het certificaatarchief. Alleen de openbare sleutel is nodig, zodat het wachtwoord niet vereist is.

    ![CA-certificaten toevoegen](media/api-management-howto-ca-certificates/02.png)  

5. Klik op **Opslaan**. Deze bewerking kan enkele minuten duren.

    ![CA-certificaten toevoegen](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> U een CA-certificaat uploaden met de `New-AzApiManagementSystemCertificate` opdracht Powershell.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Een clientcertificaat verwijderen

Als u een certificaat wilt verwijderen, klikt u op contextmenu **...** en selecteert u **Verwijderen** naast het certificaat.

![CA-certificaten verwijderen](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
