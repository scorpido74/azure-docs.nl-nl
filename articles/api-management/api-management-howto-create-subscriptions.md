---
title: Abonnementen maken in Azure API Management | Microsoft Documenten
description: Meer informatie over het maken van abonnementen in Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: f8b2238eb0fab9aeeb42d11b4176c0d681b5f8e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073527"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Abonnementen maken in Azure API Management

Wanneer u API's publiceert via Azure API-beheer, is het eenvoudig en gebruikelijk om de toegang tot deze API's te beveiligen met abonnementssleutels. Clienttoepassingen die de gepubliceerde API's moeten gebruiken, moeten een geldige abonnementssleutel bevatten in HTTP-verzoeken wanneer ze naar deze API's bellen. Om een abonnementssleutel te krijgen voor toegang tot API's, is een abonnement vereist. Zie [Abonnementen in Azure API Management](api-management-subscriptions.md)voor meer informatie over abonnementen.

In dit artikel worden de stappen doorlopen voor het maken van abonnementen in de Azure-portal.

## <a name="prerequisites"></a>Vereisten

Om de stappen in dit artikel te nemen, zijn de voorwaarden als volgt:

+ [Een API-beheerexemplaar maken](get-started-create-service-instance.md).
+ Inzicht [in abonnementen in API-beheer](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Een nieuw abonnement maken

1. Selecteer **Abonnementen** in het menu aan de linkerkant.
2. Selecteer **Abonnement toevoegen**.
3. Geef een naam van het abonnement op en selecteer het bereik.
4. Kies eventueel of het abonnement aan een gebruiker moet worden gekoppeld.
5. Selecteer **Opslaan**.

![Flexibele abonnementen](./media/api-management-subscriptions/flexible-subscription.png)

Nadat u het abonnement hebt gemaakt, worden twee API-sleutels verstrekt om toegang te krijgen tot de API's. Een sleutel is primair, en een is secundair. 

## <a name="next-steps"></a>Volgende stappen
Meer informatie over API-beheer:

+ Leer andere [concepten](api-management-terminology.md) in API-beheer.
+ Volg onze [tutorials](import-and-publish.md) voor meer informatie over API Management.
+ Kijk op onze [FAQ-pagina](api-management-faq.md) voor veelgestelde vragen.