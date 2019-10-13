---
title: Implementeren in Azure Analysis Services met behulp van Visual Studio (SSDT) | Microsoft Docs
description: Leer hoe u een tabellair model met behulp van SSDT implementeert op een Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 759d85db26ac7370c0b884d2e4839d3045384673
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301128"
---
# <a name="deploy-a-model-from-visual-studio"></a>Een model implementeren vanuit Visual Studio

Wanneer u een server in uw Azure-abonnement hebt gemaakt, kunt u een tabellaire modeldatabase op de server implementeren. U kunt SQL Server Data Tools (SSDT) gebruiken om een project voor een tabellair model te bouwen en implementeren waaraan u werkt. 

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* **Analysis Services-server** in Azure. Zie [Create an Azure Analysis Services server in Azure portal](analysis-services-create-server.md) (Een Azure Analysis Services-server maken in Azure Portal) voor meer informatie.
* **Project voor tabellair model** in SSDT of een bestaand tabellair model op het compatibiliteitsniveau 1200 of hoger. Nog nooit zo'n project gemaakt? Raadpleeg de [Adventure Works Internet sales tabular modeling tutorial](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) (Zelfstudie voor tabellaire modellen bij Adventure Works-internetverkoop).
* **On-premises gateway**: als een of meer gegevensbronnen on-premises zijn opgeslagen in het netwerk van uw organisatie, moet u een [on-premises gegevensgateway](analysis-services-gateway.md) installeren. De gateway is nodig om uw server in de cloud verbinding te laten maken met uw on-premises gegevensbronnen om gegevens in het model te verwerken en te vernieuwen.

> [!TIP]
> Voer de implementatie pas uit als u weet dat u de gegevens in de tabellen kunt verwerken. Klik in SSDT op **Model** > **Process** > **Process All**. Als de verwerking mislukt, kunt u niet implementeren.
> 
> 

## <a name="get-the-server-name"></a>De server naam ophalen

In **Azure Portal** > server > **Overview** > **Servernaam**,kopieer de servernaam.
   
![Servernaam bepalen in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-ssdt"></a>Implementeren vanuit SSDT

1. In SSDT > **Solution Explorer**, klik met de rechtermuisknop op het project > **Properties**. Plak vervolgens in **Deployment** > **Server** de naam van de server.   
   
    ![Servernaam plakken in de eigenschap Deployment Server](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. Klik in **Solution Explorer** met de rechtermuisknop op **Properties** en klik vervolgens op **Deploy**. U wordt mogelijk gevraagd u aan te melden bij Azure.
   
    ![Implementeren op server](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    De status van de implementatie wordt weergegeven in het uitvoervenster en in het venster Deploy.
   
    ![Implementatiestatus](./media/analysis-services-deploy/aas-deploy-status.png)

Dat is alles!


## <a name="troubleshooting"></a>Problemen oplossen

Als de implementatie mislukt tijdens het implementeren van metagegevens, is de kans groot dat SSDT geen verbinding kan maken met de server. Zorg ervoor dat u verbinding kunt maken met de server met behulp van SSMS. Controleer vervolgens of de eigenschap Deployment Server voor het project juist is ingesteld.

Als de implementatie mislukt bij een tabel, komt dat waarschijnlijk omdat de server geen verbinding kan maken met een gegevensbron. Als uw gegevensbron on-premises in het netwerk van uw organisatie is opgeslagen, moet u een [on-premises gegevensgateway](analysis-services-gateway.md) installeren.

## <a name="next-steps"></a>Volgende stappen

Uw tabellaire model is nu geïmplementeerd op de server en dus kunt u verbinding gaan maken met het model. Maak [verbinding via SSMS](analysis-services-manage.md) om het model te beheren. [Maak verbinding met een clienthulpprogramma](analysis-services-connect.md) zoals Power BI, Power BI Desktop of Excel om rapporten te gaan maken.

