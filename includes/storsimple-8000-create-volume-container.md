---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 467af776af95cf035121250fdcadd2fee65d9805
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67176080"
---
#### <a name="to-create-a-volume-container"></a>Een volumecontainer maken
1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**. Selecteer en klik op een apparaat in de lijst in tabelvorm met apparaten. 

    ![De blade Volumecontainer](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. Klik op het apparaatdashboard op **Volumecontainer toevoegen**

    ![De blade Volumecontainer](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. Doe het volgende op de blade **Volumecontainer toevoegen**:
   
   1. Het apparaat wordt automatisch geselecteerd.
   2. Geef een **naam** op voor uw volumecontainer. De naam moet 3 tot 32 tekens lang zijn. U kunt de naam van een volumecontainer niet wijzigen zodra deze is gemaakt.
   3. Schakel **Versleuteling van cloudopslag inschakelen** in om versleuteling in te schakelen van de gegevens die van het apparaat naar de cloud worden verzonden.
   4. Geef een **versleutelingssleutel voor cloudopslag** van 8 tot 32 tekens op en bevestig deze. De sleutel wordt door het apparaat gebruikt voor toegang tot versleutelde gegevens.
   5. Selecteer een **opslagaccount** om aan deze volumecontainer te koppelen. U kunt een bestaand opslagaccount kiezen of het standaardaccount dat is gegenereerd toen de service werd gemaakt. U kunt ook de optie **Nieuwe toevoegen** gebruiken om een opslagaccount op te geven dat niet is gekoppeld aan dit serviceabonnement.
   6. Selecteer **Onbeperkt** in de vervolgkeuzelijst **Bandbreedte opgeven** als u alle beschikbare bandbreedte wilt verbruiken. U kunt deze optie ook instellen op **Aangepast** om bandbreedtebesturingselementen te implementeren en een waarde tussen 1 Mbps en 1000 Mbps op te geven.
      Als u beschikt over informatie over uw bandbreedtegebruik, kunt u de bandbreedte mogelijk toewijzen op basis van een planning door een **bandbreedtesjabloon te selecteren**. Voor een stapsgewijze procedure gaat u naar [Add a bandwidth template](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template) (Een bandbreedtesjabloon toevoegen).

      ![De blade Volumecontainer](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. Klik op **Maken**.

        ![De blade Volumecontainer](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       U krijgt een melding wanneer de volumecontainer is gemaakt.

       ![Melding over volumecontainer](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   De nieuwe volumecontainer wordt vermeld in de lijst met volumecontainers voor uw apparaat.

   ![De blade Volumecontainer toevoegen](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


