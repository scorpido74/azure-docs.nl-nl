---
title: Een model implementeren in Azure Analysis Services met Behulp van Visual Studio | Microsoft Documenten
description: Meer informatie over het implementeren van een tabelmodel naar een Azure Analysis Services-server met Behulp van Visual Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 71b3b7815d2a4b0b4de3afdca9db93156f505445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572880"
---
# <a name="deploy-a-model-from-visual-studio"></a>Een model implementeren vanuit Visual Studio

Wanneer u een server in uw Azure-abonnement hebt gemaakt, kunt u een tabellaire modeldatabase op de server implementeren. U Visual Studio met Analysis Services-projecten gebruiken om een tabelmodelproject te bouwen en te implementeren waaraan u werkt. 

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* **Analysis Services-server** in Azure. Zie [Create an Azure Analysis Services server in Azure portal](analysis-services-create-server.md) (Een Azure Analysis Services-server maken in Azure Portal) voor meer informatie.
* **Tabelvormig modelproject** in Visual Studio of een bestaand tabelmodel op het niveau van 1200 of hoger. Nog nooit zo'n project gemaakt? Raadpleeg de [Adventure Works Internet sales tabular modeling tutorial](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) (Zelfstudie voor tabellaire modellen bij Adventure Works-internetverkoop).
* **On-premises gateway**: als een of meer gegevensbronnen on-premises zijn opgeslagen in het netwerk van uw organisatie, moet u een [on-premises gegevensgateway](analysis-services-gateway.md) installeren. De gateway is nodig om uw server in de cloud verbinding te laten maken met uw on-premises gegevensbronnen om gegevens in het model te verwerken en te vernieuwen.

> [!TIP]
> Voer de implementatie pas uit als u weet dat u de gegevens in de tabellen kunt verwerken. Klik in Visual Studio op > **Procesproces proces** > **allemaal** **modelleren**. Als de verwerking mislukt, kunt u niet implementeren.
> 
> 

## <a name="get-the-server-name"></a>De servernaam oppakken

In **Azure Portal** > server > **Overview** > **Servernaam**,kopieer de servernaam.
   
![Servernaam bepalen in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Implementeren vanuit Visual Studio

1. Klik in Visual Studio > **Solution Explorer**met de rechtermuisknop op het project > **Eigenschappen**. Plak vervolgens in **Deployment** > **Server** de servernaam.   
   
    ![Servernaam plakken in de eigenschap Deployment Server](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. Klik in **Solution Explorer** met de rechtermuisknop op **Properties** en klik vervolgens op **Deploy**. U wordt mogelijk gevraagd u aan te melden bij Azure.
   
    ![Implementeren op server](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    De status van de implementatie wordt weergegeven in het uitvoervenster en in het venster Deploy.
   
    ![Implementatiestatus](./media/analysis-services-deploy/aas-deploy-status.png)

Dat is alles!


## <a name="troubleshooting"></a>Problemen oplossen

Als de implementatie mislukt bij het implementeren van metagegevens, is dit waarschijnlijk omdat Visual Studio geen verbinding kon maken met uw server. Zorg ervoor dat u verbinding kunt maken met de server met behulp van SSMS. Controleer vervolgens of de eigenschap Deployment Server voor het project juist is ingesteld.

Als de implementatie mislukt bij een tabel, komt dat waarschijnlijk omdat de server geen verbinding kan maken met een gegevensbron. Als uw gegevensbron on-premises in het netwerk van uw organisatie is opgeslagen, moet u een [on-premises gegevensgateway](analysis-services-gateway.md) installeren.

## <a name="next-steps"></a>Volgende stappen

Uw tabellaire model is nu geïmplementeerd op de server en dus kunt u verbinding gaan maken met het model. U [er verbinding mee maken met SQL Server Management Studio (SSMS)](analysis-services-manage.md) om het te beheren. [Maak verbinding met een clienthulpprogramma](analysis-services-connect.md) zoals Power BI, Power BI Desktop of Excel om rapporten te gaan maken.

