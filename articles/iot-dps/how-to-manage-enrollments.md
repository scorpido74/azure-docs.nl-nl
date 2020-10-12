---
title: Registraties van apparaten voor Azure IoT Hub Device Provisioning Service beheren in de Azure Portal
description: Registraties van apparaten beheren voor uw Device Provisioning Service (DPS) in azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8bd896573dbd0b24df11c0d66009098de19654ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74974935"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Registratie van apparaten beheren met Azure Portal

Een *apparaatregistratie* maakt een record van één apparaat of een groep apparaten die op een bepaald moment bij de Azure-IOT hub Device Provisioning Service kunnen worden geregistreerd. De registratie record bevat de eerste gewenste configuratie voor de apparaten die deel uitmaken van deze inschrijving, inclusief de gewenste IoT-hub. In dit artikel leest u hoe u de registratie van apparaten voor uw inrichtings service kunt beheren.


## <a name="create-a-device-enrollment"></a>Een apparaatinschrijving maken

Er zijn twee manieren waarop u uw apparaten kunt inschrijven bij de inrichtings service:

* Een **registratie groep** is een vermelding voor een groep apparaten die een gemeen schappelijk Attestation-mechanisme van X. 509-certificaten delen, ondertekend door hetzelfde handtekening certificaat, dat het [basis certificaat](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) of het [tussenliggende certificaat](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)kan zijn, dat wordt gebruikt voor het produceren van een apparaat certificaat op een fysiek apparaat. U kunt het beste een registratie groep gebruiken voor een groot aantal apparaten die een gewenste initiële configuratie delen, of voor apparaten die allemaal naar dezelfde Tenant gaan. Houd er rekening mee dat u alleen apparaten kunt inschrijven die gebruikmaken van het 509 Attestation-mechanisme van *X.* 

    U kunt met behulp van de volgende stappen een inschrijvings groep maken in de portal voor een groep apparaten:

  1. Meld u aan bij de Azure Portal en klik op **alle resources** in het menu aan de linkerkant.  
  1. Klik in de lijst met resources op de Device Provisioning Service waarop u uw apparaat wilt registreren.  
  1. In uw inrichtings service:  
     a. Klik op **inschrijvingen beheren**en selecteer vervolgens het tabblad **registratie groepen** .  
     b. Klik bovenaan op de knop **Toevoegen**.  
     c. Wanneer het paneel registratie groep toevoegen wordt weer gegeven, voert u de gegevens voor de vermelding van de registratie lijst in.  **Groeps naam** is vereist. Selecteer ook "CA of tussenliggend" voor het **certificaat type**en upload het **primaire hoofd certificaat** voor de groep apparaten.  
     d. Klik op **Opslaan**. Wanneer u de registratie groep hebt gemaakt, ziet u dat de groeps naam wordt weer gegeven op het tabblad **registratie groepen** .  

     [![Registratie groep in de portal](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* Een **afzonderlijke inschrijving** is een vermelding voor één apparaat dat kan worden geregistreerd. Individuele inschrijvingen kunnen ofwel x509-certificaten of SAS-tokens (van een fysieke of virtuele TPM) als Attestation-mechanismen gebruiken. We raden u aan om afzonderlijke inschrijvingen te gebruiken voor apparaten die unieke initiële configuraties vereisen, of voor apparaten die alleen SAS-tokens via TPM of virtuele TPM kunnen gebruiken als Attestation-mechanisme. Afzonderlijke inschrijvingen hebben mogelijk de gewenste apparaat-id voor IoT Hub die is opgegeven.

    U kunt met behulp van de volgende stappen een afzonderlijke inschrijving maken in de portal:

    1. Meld u aan bij de Azure Portal en klik op **alle resources** in het menu aan de linkerkant.
    1. Klik in de lijst met resources op de Device Provisioning Service waarop u uw apparaat wilt registreren.
    1. In uw inrichtings service:  
       a. Klik op **inschrijvingen beheren**en selecteer vervolgens het tabblad **afzonderlijke registraties** .  
       b. Klik bovenaan op de knop **Toevoegen**.   
       c. Wanneer het deel venster inschrijving toevoegen wordt weer gegeven, voert u de gegevens voor de vermelding van de registratie lijst in. Selecteer eerst het Attestation- **mechanisme** voor het apparaat (X. 509 of TPM). X. 509-Attestation vereist dat u het primaire Leaf- **certificaat** voor het apparaat uploadt. Voor TPM moet u de **Attestation-sleutel** en **registratie-id** voor het apparaat invoeren.  
       d. Klik op **Opslaan**. Wanneer u de registratie groep hebt gemaakt, ziet u dat uw apparaat wordt weer gegeven op het tabblad **afzonderlijke registraties** .  

       [![Afzonderlijke inschrijving in de portal](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Een inschrijvings vermelding bijwerken
U kunt een bestaande inschrijvings vermelding in de portal bijwerken met behulp van de volgende stappen:

1. Open uw Device Provisioning Service in de Azure Portal en klik op **inschrijvingen beheren**. 
1. Navigeer naar de inschrijvings vermelding die u wilt wijzigen. Klik op het item om een samen vatting te openen van de registratie van uw apparaat. 
1. Op deze pagina kunt u andere items dan het beveiligings type en de referenties wijzigen, zoals de IoT-hub waaraan het apparaat moet worden gekoppeld, evenals de apparaat-ID. U kunt ook de eerste dubbele status van het apparaat wijzigen. 
1. Zodra u klaar bent, klikt u op **Opslaan** om de registratie van uw apparaat bij te werken. 

    ![Inschrijving in de portal bijwerken](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Registratie van een apparaat verwijderen
Als uw apparaat (en) niet moeten worden ingericht voor een IoT-hub, kunt u de bijbehorende inschrijvings vermelding in de Portal verwijderen door de volgende stappen uit te voeren:

1. Open uw Device Provisioning Service in de Azure Portal en klik op **inschrijvingen beheren**. 
1. Navigeer naar en selecteer de inschrijvings vermelding die u wilt verwijderen. 
1. Klik bovenaan op de knop **verwijderen** en selecteer vervolgens **Ja** wanneer u wordt gevraagd om te bevestigen. 
1. Zodra de actie is voltooid, ziet u dat uw vermelding is verwijderd uit de lijst met geregistreerde apparaten. 
 
    ![Inschrijving in de Portal verwijderen](./media/how-to-manage-enrollments/remove-enrollment.png)


