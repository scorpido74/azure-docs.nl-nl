---
title: Eindpuntbeleid voor service maken en koppelen - Azure-portal
titlesuffix: Azure Virtual Network
description: Lees in dit artikel hoe u eindpuntbeleid voor service instelt en bijbehorend met de Azure-portal.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: d26fd2fec5f9d5ab8e9d82ff2c6bd83b11c72e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651159"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Eindpuntbeleid voor services maken, wijzigen of verwijderen met behulp van de Azure-portal

Met serviceeindpuntbeleid u virtueel netwerkverkeer filteren op specifieke Azure-resources via serviceeindpunten. Zie het overzicht van [eindpuntbeleidsregels](virtual-network-service-endpoint-policies-overview.md) voor service om meer te weten te komen als u niet bekend bent met eindpuntbeleidsregels voor services.

 In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een eindpuntbeleid voor service maken
> * Een definitie van serviceeindpuntbeleid maken
> * Een virtueel netwerk maken met een subnet
> * Een eindpuntbeleid voor service koppelen aan een subnet

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure 

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Een eindpuntbeleid voor service maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Typ in het zoekvenster 'Eindpuntbeleid service' en selecteer **serviceeindpuntbeleid** en selecteer **Vervolgens Maken**.

![Eindpuntbeleid voor service maken](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. De volgende gegevens invoeren of selecteren in **Basisbeginselen** 

   - Abonnement: Selecteer uw abonnement voor beleid
   - Resourcegroep : Selecteer **Nieuw maken** en voer *myResourceGroup* in
   - Naam : myEndpointPolicy
   - Locatie : Centraal VS
 
   ![Basisbeginselen voor serviceeindpuntbeleid maken](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Selecteren **+ Toevoegen** onder **Resources** en de volgende gegevens invoeren of selecteren in **Een resourcevenster toevoegen**

   - Service: alleen **Microsoft.Storage** is beschikbaar met Service Endpoint-beleid
   - Bereik : Selecteer er een uit **Single Account,** **Alle accounts in abonnement** en Alle accounts in **resourcegroep**
   - Abonnement: Selecteer uw abonnement voor opslagaccount. Beleids- en opslagaccounts kunnen in verschillende abonnementen zijn.
   - Resourcegroep: Selecteer uw resourcegroep. Vereist, als het bereik is ingesteld als 'Alle accounts in resourcegroep' of 'Eén account'.  
   - Resource: Selecteer uw Azure Storage-bron onder de geselecteerde abonnements- of brongroep
   - Klik op **Knop Toevoegen** onderaan om de toevoeging van de resource af te ronden

   ![Beleidsdefinitie van eindpunt service - resource](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Voeg meer resources toe door de bovenstaande stappen zo nodig te herhalen

5. Optioneel: Voer de volgende gegevens in **Tags**in of selecteer deze in:
   
   - Sleutel: Selecteer uw sleutel voor het beleid. Ex: Dept     
   - Waarde: Voer waardepaar voor de sleutel in. Ex: Financiën

6. Selecteer **Controleren + maken**. Valideer de informatie en klik op **Maken**. Als u verdere bewerkingen wilt aanbrengen, klikt u op **Vorige**. 

   ![Definitieve validaties voor serviceeindpuntbeleid maken](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Eindpuntbeleid weergeven 

1. Begin in het vak *Alle services* in de portal met het typen *van eindpuntbeleid voor service*. Selecteer **Serviceeindpuntbeleid**.
2. Selecteer **onder Abonnementen**uw abonnements- en resourcegroep, zoals in de volgende afbeelding wordt weergegeven

   ![Beleid weergeven](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Selecteer het beleid en klik op **Beleidsdefinities** om meer beleidsdefinities weer te geven of toe te voegen.

   ![Beleidsdefinities weergeven](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Selecteer **Gekoppelde subnetten** om de subnetten weer te geven waaraan het beleid is gekoppeld. Als er nog geen subnet is gekoppeld, volgt u de instructies in de volgende stap.

   ![Gekoppelde subnetten](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Een beleid koppelen aan een subnet

>[!WARNING] 
> Zorg ervoor dat alle bronnen die vanaf het subnet worden geopend, worden toegevoegd aan de beleidsdefinitie voordat u het beleid aan het gegeven subnet associeert. Zodra het beleid is gekoppeld, wordt alleen toegang tot de vermelde resources *toegestaan* toegestaan via serviceeindpunten. 
>
> Zorg er ook voor dat er geen beheerde Azure-services bestaan in het subnet dat wordt gekoppeld aan het eindpuntbeleid van de service

- Voordat u een beleid aan een subnet koppelen, moet u een virtueel netwerk en subnet maken. Raadpleeg het artikel [Een virtueel netwerk maken](./quick-create-portal.md) voor hulp hierbij.

- Zodra u het virtuele netwerk en subnet hebt ingesteld, moet u eindpunten voor virtual network service configureren voor Azure Storage. Selecteer **serviceeindpunten**in het blad Virtueel netwerk en selecteer in het volgende deelvenster **Microsoft.Storage** en selecteer onder **Subnetten** de gewenste VNet of Subnet

- U er nu voor kiezen om het serviceeindpuntbeleid te selecteren in de vervolgkeuzelijst in het bovenstaande deelvenster als u al Service Endpoint-beleid hebt gemaakt voordat u Service Endpoint voor het subnet configureert zoals hieronder wordt weergegeven

    ![Subnet koppelen tijdens het maken van serviceeindpunt](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- OF als u serviceeindpuntbeleid koppelt nadat serviceeindpunten al zijn geconfigureerd, u ervoor kiezen om het subnet te koppelen vanuit het beheer van serviceeindpuntbeleid door te navigeren naar het deelvenster **Gekoppelde subnetten** zoals hieronder wordt weergegeven

    ![Subnet associëren via SEP](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>De toegang tot Azure Storage-bronnen in alle regio's wordt per Service Endpoint-beleid beperkt vanuit dit subnet.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een eindpuntbeleid voor service gemaakt en het aan een subnet gekoppeld. Zie overzicht van [eindpuntbeleidsregels](virtual-network-service-endpoint-policies-overview.md) voor service voor meer informatie over eindpuntbeleid voor services.
