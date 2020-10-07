---
title: Azure VMware Solution implementeren
description: Stappen voor het implementeren van Azure VMware Solution met behulp van de Azure-portal.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 4d0881721cd8c13d1b6c9fb3a29e4cdb6d6a753f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578309"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Een nieuwe resource maken**. Typ in het tekstvak **Marketplace doorzoeken** `Azure VMware Solution` en selecteer **Azure VMware Solution** uit de lijst. Selecteer **Maken** in het venster **Azure VMware Solution**.

1. Voer op het tabblad **Basisinstellingen** waarden in voor de velden. De volgende tabel geeft de eigenschappen weer voor de velden.

   | Veld   | Waarde  |
   | ---| --- |
   | **Abonnement** | Het abonnement dat u wilt gebruiken voor de implementatie.|
   | **Resourcegroep** | De resourcegroep voor uw privécloud-resources. |
   | **Locatie** | Selecteer een locatie, zoals **VS-Oost**.|
   | **Resourcenaam** | De naam van uw Azure VMware Solution-privécloud. |
   | **SKU** | Selecteer de volgende SKU-waarde: AV36 |
   | **Hosts** | Dit is het aantal hosts dat aan het privécloudcluster moet worden toegevoegd. De standaardwaarde is 3, maar kan na de implementatie worden verhoogd of verlaagd.  |
   | **Beheerderswachtwoord van vCenter** | Voer een wachtwoord voor de cloudbeheerder in. |
   | **Manager-wachtwoord van NSX-T** | Voer een NSX-T-beheerderswachtwoord in. |
   | **Adres blokkeren** | Voer een IP-adresblok in voor het CIDR-netwerk voor de privécloud, zoals 10.175.0.0/22. |
   | **Virtual Network** | Selecteer een virtueel netwerk of maak een nieuw exemplaar voor de Azure VMware Solution-privécloud.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Voer op het tabblad Basisinstellingen waarden in voor de velden." border="true":::

1. Als dit gereed is, selecteert u **Controleren + maken**. Controleer de ingevoerde gegevens in het volgende scherm. Als de gegevens juist zijn, selecteert u **Maken**.

   > [!NOTE]
   > Deze stap duurt ongeveer twee uur. 

1. Controleer of de implementatie is gelukt. Ga naar de resourcegroep die u hebt gemaakt en selecteer uw privécloud.  U ziet de status **Geslaagd** wanneer de implementatie is voltooid. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Voer op het tabblad Basisinstellingen waarden in voor de velden." border="true":::