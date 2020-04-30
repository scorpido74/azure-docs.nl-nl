---
title: Zelf studie-een voorbeeld model toevoegen-Azure Analysis Services | Microsoft Docs
description: In deze zelfstudieles leert u hoe u een eenvoudig voorbeeldmodel toevoegt in Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a72236843c13e139f33a669b54f108e91679c8c6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74326580"
---
# <a name="tutorial-add-a-sample-model-from-the-portal"></a>Zelfstudie: Een voorbeeldmodel toevoegen via de portal

In deze zelfstudie voegt u een voorbeeldmodeldatabase in tabelvorm van Adventure Works toe aan uw server. Het voorbeeldmodel is een voltooide versie van het voorbeeldgegevensmodel Internetverkoop van Adventure Works (1200). Een voorbeeldmodel is handig voor het testen van modelbeheer, het maken van verbinding met hulpprogramma's en clienttoepassingen en het opvragen van modelgegevens. In de zelfstudie worden [Azure Portal](https://portal.azure.com) en [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) gebruikt voor de volgende taken: 

> [!div class="checklist"]
> * Een voltooid voorbeeldgegevensmodel in tabelvorm toevoegen aan een server 
> * Verbinding maken met het model via SSMS

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Voor deze zelfstudie hebt u het volgende nodig:

- Een Azure Analysis Services-server. Zie [Een server maken - portal](analysis-services-create-server.md) voor meer informatie.
- Machtigingen voor serverbeheerder
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [portal](https://portal.azure.com/).

## <a name="add-a-sample-model"></a>Een voorbeeldmodel toevoegen

1. Klik in **Overzicht** van server op **Nieuw model**.

    ![Een voorbeeldmodel maken](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. **Kies een gegevens bron**in **Nieuw model** > , Controleer of **voorbeeld gegevens** is geselecteerd en klik vervolgens op **toevoegen**.

    ![Voorbeeldgegevens selecteren](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. Controleer in **Overzicht** of het `adventureworks`-voorbeeldmodel is toegevoegd.

    ![Voorbeeldgegevens selecteren](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)


## <a name="clean-up-resources"></a>Resources opschonen

In het voorbeeldmodel worden cachegeheugenbronnen gebruikt. Verwijder het voorbeeldmodel van uw server als u dit niet gebruikt voor het testen.

In deze stappen wordt beschreven hoe u een model verwijdert van een server met behulp van SSMS.

1. Klik in SSMS > **Objectverkenner** op **Verbinding maken** > **Analysis Services**.

2. Plak de servernaam in **Verbinding maken met server** en kies vervolgens in **Verificatie** de optie **Active Directory - Universeel met MFA-ondersteuning**, typ uw gebruikersnaam en klik vervolgens op **Verbinding maken**.

    ![Aanmelden](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. Klik in **Objectverkenner** met de rechtermuisknop op de `adventureworks`-voorbeelddatabase en klik vervolgens op **Verwijderen**.

    ![De voorbeelddatabase verwijderen](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Volgende stappen 

In deze zelfstudie hebt u geleerd hoe u een eenvoudig voorbeeldmodel toevoegt aan uw server. Nu u een modeldatabase hebt, kunt u hiermee verbinding maken vanuit SQL Server Management Studio en hieraan gebruikersrollen toevoegen. Ga verder met de volgende zelfstudie voor meer informatie.

> [!div class="nextstepaction"]
> [Zelfstudie: Serverbeheerder en gebruikersrollen configureren](tutorials/analysis-services-tutorial-roles.md)


