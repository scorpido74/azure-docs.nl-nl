---
title: Apparaatinschrijvingen voor Azure IoT Hub Device Provisioning Service beheren in de Azure-portal
description: Apparaatinschrijvingen beheren voor uw DPS (Device Provisioning Service) in de Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8bd896573dbd0b24df11c0d66009098de19654ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974935"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Apparaatinschrijvingen beheren met Azure Portal

Een *apparaatinschrijving* maakt een record van één apparaat of een groep apparaten die zich op een bepaald moment kunnen registreren bij de Azure IoT Hub Device Provisioning Service. De inschrijvingsrecord bevat de initiële gewenste configuratie voor het apparaat(en) als onderdeel van die inschrijving, inclusief de gewenste IoT-hub. In dit artikel ziet u hoe u apparaatinschrijvingen voor uw inrichtingsservice beheert.


## <a name="create-a-device-enrollment"></a>Een apparaatinschrijving maken

Er zijn twee manieren waarop u uw apparaten inschrijven bij de inrichtingsservice:

* Een **inschrijvingsgroep** is een vermelding voor een groep apparaten die een gemeenschappelijk attestmechanisme van X.509-certificaten delen, ondertekend door hetzelfde ondertekeningscertificaat, dat het [basiscertificaat](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) of het [tussencertificaat](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)kan zijn, dat wordt gebruikt om apparaatcertificaat op fysiek apparaat te produceren. We raden u aan een inschrijvingsgroep te gebruiken voor een groot aantal apparaten die een gewenste initiële configuratie delen, of voor apparaten die allemaal naar dezelfde tenant gaan. Houd er rekening mee dat u alleen apparaten inschrijven die het x.509-attestmechanisme gebruiken als *inschrijvingsgroepen*. 

    U een inschrijvingsgroep maken in de portal voor een groep apparaten met de volgende stappen:

  1. Meld u aan bij de Azure-portal en klik op **Alle bronnen** in het linkermenu.  
  1. Klik op de service Apparaatvoorziening waarop u uw apparaat wilt inschrijven in de lijst met bronnen.  
  1. In uw inrichtingsdienst:  
     a. Klik **op Inschrijvingen beheren**en selecteer het tabblad **Inschrijvingsgroepen.**  
     b. Klik bovenaan op de knop **Toevoegen**.  
     c. Wanneer het deelvenster Inschrijvingsgroep toevoegen wordt weergegeven, voert u de gegevens voor de vermelding van de inschrijvingslijst in.  **Groepsnaam** is vereist. Selecteer ook 'CA of Intermediate' voor **certificaattype**en upload het **basishoofdcertificaat** voor de groep apparaten.  
     d. Klik op **Opslaan**. Als u uw inschrijvingsgroep succesvol maakt, wordt de groepsnaam weergegeven onder het tabblad **Inschrijvingsgroepen.**  

     [![Inschrijvingsgroep in het portaal](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* Een **individuele inschrijving** is een vermelding voor één apparaat dat zich kan registreren. Individuele inschrijvingen kunnen x509-certificaten of SAS-tokens (van een fysieke of virtuele TPM) gebruiken als attestmechanismen. We raden u aan afzonderlijke inschrijvingen te gebruiken voor apparaten waarvoor unieke initiële configuraties nodig zijn, of voor apparaten die alleen SAS-tokens kunnen gebruiken via TPM of virtuele TPM als het attestmechanisme. Afzonderlijke inschrijvingen hebben mogelijk de gewenste apparaat-id voor IoT Hub die is opgegeven.

    U een individuele inschrijving in de portal maken met de volgende stappen:

    1. Meld u aan bij de Azure-portal en klik op **Alle bronnen** in het linkermenu.
    1. Klik op de service Apparaatvoorziening waarop u uw apparaat wilt inschrijven in de lijst met bronnen.
    1. In uw inrichtingsdienst:  
       a. Klik **op Inschrijvingen beheren**en selecteer vervolgens het tabblad Individuele **inschrijvingen.**  
       b. Klik bovenaan op de knop **Toevoegen**.   
       c. Wanneer het deelvenster Inschrijving toevoegen wordt weergegeven, voert u de gegevens voor de vermelding van de inschrijvingslijst in. Selecteer eerst het **attestmechanisme** voor het apparaat (X.509 of TPM). X.509 attestation vereist dat u het blad **primaire certificaat** voor het apparaat uploaden. TPM vereist dat u de **attestation-sleutel-** en **registratie-id** voor het apparaat invoert.  
       d. Klik op **Opslaan**. Als u uw **inschrijvingsgroep** succesvol maakt, moet u uw apparaat onder het tabblad Individuele inschrijvingen zien verschijnen.  

       [![Individuele inschrijving in het portaal](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Een inschrijvingsitem bijwerken
U een bestaande inschrijvingsvermelding in de portal bijwerken met de volgende stappen:

1. Open de service Voor het inrichten van apparaten in de Azure-portal en klik op **Inschrijvingen beheren**. 
1. Navigeer naar het inschrijvingsitem dat u wilt wijzigen. Klik op het item, waarmee een overzichtsinformatie over de inschrijving van uw apparaat wordt geopend. 
1. Op deze pagina u andere items dan het beveiligingstype en de referenties wijzigen, zoals de IoT-hub waaraan het apparaat moet worden gekoppeld, evenals de apparaat-id. U ook de initiële apparaatdubbele status wijzigen. 
1. Klik op **Opslaan** om de inschrijving van uw apparaat bij te werken nadat u klaar bent. 

    ![Inschrijving in de portal bijwerken](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Een apparaatinschrijving verwijderen
In gevallen waarin uw apparaat(en) niet hoeft te worden ingericht op een IoT-hub, u de bijbehorende inschrijvingsvermelding in de portal verwijderen met de volgende stappen:

1. Open de service Voor het inrichten van apparaten in de Azure-portal en klik op **Inschrijvingen beheren**. 
1. Navigeer naar en selecteer het inschrijvingsitem dat u wilt verwijderen. 
1. Klik op de knop **Verwijderen** bovenaan en selecteer **Ja** wanneer u wordt gevraagd om te bevestigen. 
1. Zodra de actie is voltooid, ziet u uw vermelding verwijderd uit de lijst met apparaatinschrijvingen. 
 
    ![Inschrijving in de portal verwijderen](./media/how-to-manage-enrollments/remove-enrollment.png)


