---
title: Verbinding maken met Azure Analysis Services met Excel | Microsoft Documenten
description: Meer informatie over het maken van verbinding met een Azure Analysis Services-server met Excel. Zodra gebruikers zijn verbonden, kunnen ze draaitabellen maken om gegevens te verkennen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bb76accfef94327386d0ff58d003782a9fe3b0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573140"
---
# <a name="connect-with-excel"></a>Verbinden met Excel

Zodra u een server hebt gemaakt en er een tabelmodel op hebt geïmplementeerd, kunnen clients verbinding maken en beginnen met het verkennen van gegevens. 

## <a name="before-you-begin"></a>Voordat u begint

Het account waarmee u zich aanmeldt, moet deel uitmaken van een modeldatabaserol met ten minste leesmachtigingen. Raadpleeg voor meer informatie [Verificatie en gebruikersmachtigingen](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Verbinding maken in Excel

Verbinding maken met een server in Excel wordt ondersteund door Gegevens opte halen in Excel 2016 en hoger. Verbinding maken met de wizard Tabel importeren in Power Pivot wordt niet ondersteund. 

1. Klik in Excel op het lint **Gegevens** op **Externe gegevens** > **ophalen uit andere bronnen** > **van analyseservices**.

2. Voer in de wizard Gegevensverbinding in **Servernaam**de servernaam in, inclusief protocol en URI. Bijvoorbeeld asazure://westcentralus.asazure.windows.net/advworks. Selecteer vervolgens in **aanmeldingsreferenties** **de optie De volgende gebruikersnaam en wachtwoord gebruiken** nancy@adventureworks.comen typ vervolgens bijvoorbeeld de gebruikersnaam en het wachtwoord van de organisatie.

    > [!IMPORTANT]
    > Als u zich aanmeldt met een Microsoft-account, Live ID, Yahoo, Gmail, enz., of als u zich moet aanmelden met meervoudige verificatie, laat u het wachtwoordveld leeg. U wordt gevraagd om een wachtwoord nadat u op Volgende hebt geklikt. 

    ![Verbinding maken vanuit Excel-aanmelding](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. Selecteer **in Database en tabel selecteren**de database en het model of het perspectief en klik op **Voltooien**.
   
    ![Verbinding maken vanuit excel-selectiemodel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Zie ook

[Clientbibliotheken](analysis-services-data-providers.md)   
[Uw server beheren](analysis-services-manage.md)     


