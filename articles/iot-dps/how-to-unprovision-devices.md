---
title: Apparaten deprovisionen die zijn ingericht met Azure IoT Hub Device Provisioning Service
description: Apparaten deprovisionen die zijn ingericht met Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 8a3677ba285f5b02407ca3d176979bf6c016ef9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974833"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Apparaten deprovisionen die voorheen automatisch waren ingericht 

Mogelijk is het nodig om apparaten te deprovisioneren die voorheen automatisch waren ingericht via de Service Apparaatinrichting. Een apparaat kan bijvoorbeeld worden verkocht of verplaatst naar een andere IoT-hub, of het kan verloren, gestolen of anderszins worden aangetast. 

In het algemeen omvat het deprovisioneren van een apparaat twee stappen:

1. Disenroll het apparaat van uw inrichting seinen service, om toekomstige auto-provisioning te voorkomen. Afhankelijk van of u de toegang tijdelijk of permanent wilt intrekken, u een inschrijvingsitem uitschakelen of verwijderen. Voor apparaten die x.509-attest gebruiken, u een vermelding in de hiërarchie van uw bestaande inschrijvingsgroepen uitschakelen/verwijderen.  
 
   - Zie [Een apparaat desrolen van een apparaat van Azure IoT Hub Device Provisioning Service](how-to-revoke-device-access-portal.md)voor meer informatie over het uitschrijven van een apparaat.
   - Zie [Apparaatinschrijvingen met service-SDK's](how-to-manage-enrollments-sdks.md)beheren voor meer informatie over het programmatisch disenrolleren van een apparaat met behulp van een van de inprovisioningsservice-SDK's.

2. Registreer het apparaat van uw IoT Hub om toekomstige communicatie en gegevensoverdracht te voorkomen. Nogmaals, u de vermelding van het apparaat in het identiteitsregister voor de IoT-hub waar het is ingericht, tijdelijk uitschakelen of permanent verwijderen. Zie [Apparaten uitschakelen](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) voor meer informatie over uitschakelen. Zie 'Apparaatbeheer / IoT-apparaten' voor uw IoT Hub-bron in de [Azure-portal.](https://portal.azure.com)

De exacte stappen die u neemt om een apparaat te deprovisioneren, zijn afhankelijk van het attestmechanisme en de toepasselijke inschrijvingsvermelding bij uw inrichtingsservice. De volgende secties geven een overzicht van het proces, op basis van het inschrijvings- en attesttype.

## <a name="individual-enrollments"></a>Individuele inschrijvingen
Apparaten die tpm-attest of X.509-attest met een bladcertificaat gebruiken, worden ingericht via een individuele inschrijvingsvermelding. 

Ga als lid van de voorziening over een apparaat met een individuele inschrijving: 

1. Disenroll het apparaat van uw inprovisioning service:

   - Voor apparaten die TPM-attest gebruiken, verwijdert u de individuele inschrijvingsvermelding om de toegang van het apparaat tot de inrichtingsservice permanent in te trekken of schakelt u de toegang uit om de toegang tijdelijk in te trekken. 
   - Voor apparaten die x.509-attest gebruiken, u de vermelding verwijderen of uitschakelen. Houd er echter rekening mee dat als u een afzonderlijke inschrijving verwijdert voor een apparaat dat X.509 gebruikt en er een ingeschakelde inschrijvingsgroep bestaat voor een ondertekeningscertificaat in de certificaatketen van dat apparaat, kan het apparaat zich opnieuw inschrijven. Voor dergelijke apparaten kan het veiliger zijn om de inschrijvingsvermelding uit te schakelen. Als u dit voorkomt dat het apparaat zich opnieuw inschrijft, ongeacht of er een ingeschakelde inschrijvingsgroep bestaat voor een van de ondertekeningscertificaten.

2. Schakel het apparaat uit of verwijder het in het identiteitsregister van de IoT-hub waarop het is ingericht. 


## <a name="enrollment-groups"></a>Inschrijvingsgroepen
Met x.509 attest kunnen apparaten ook worden ingericht via een inschrijvingsgroep. Inschrijvingsgroepen zijn geconfigureerd met een ondertekeningscertificaat, een tussentijds of hoofd-CA-certificaat, en beheren de toegang tot de inrichtingsservice voor apparaten met dat certificaat in hun certificaatketen. Zie [X.509-certificaten](concepts-security.md#x509-certificates)voor meer informatie over inschrijvingsgroepen en X.509-certificaten met de inrichtingsservice. 

Als u een lijst met apparaten wilt zien die via een inschrijvingsgroep zijn ingericht, u de gegevens van de inschrijvingsgroep bekijken. Dit is een eenvoudige manier om te begrijpen aan welke IoT-hub elk apparaat is ingericht. Ga als een overzicht van de lijst met apparaten: 

1. Meld u aan bij de Azure-portal en klik op **Alle bronnen** in het linkermenu.
2. Klik op uw inrichtingsservice in de lijst met bronnen.
3. Klik in uw **inrichtingsservice**op Inschrijvingen beheren en selecteer het tabblad **Inschrijvingsgroepen.**
4. Klik op de inschrijvingsgroep om deze te openen.

   ![Vermelding van de inschrijvingsgroep weergeven in de portal](./media/how-to-unprovision-devices/view-enrollment-group.png)

Bij inschrijvingsgroepen zijn er twee scenario's om rekening mee te houden:

- Ga als het gaat om het deprovisioneren van alle apparaten die zijn ingericht via een inschrijvingsgroep:
  1. Schakel de inschrijvingsgroep uit om het ondertekeningscertificaat op de zwarte lijst te zetten. 
  2. Gebruik de lijst met ingerichte apparaten voor die inschrijvingsgroep om elk apparaat uit te schakelen of te verwijderen uit het identiteitsregister van de desbetreffende IoT-hub. 
  3. Nadat u alle apparaten uit hun respectievelijke IoT-hubs hebt uitgeschakeld of verwijderd, u de inschrijvingsgroep optioneel verwijderen. Houd er echter rekening mee dat, als u de inschrijvingsgroep verwijdert en er een ingeschakelde inschrijvingsgroep is voor een ondertekeningscertificaat hoger in de certificaatketen van een of meer apparaten, deze apparaten zich opnieuw kunnen inschrijven. 

- Ga als lid van de groep om één apparaat uit een inschrijvingsgroep te verwijderen:
  1. Maak een uitgeschakelde individuele inschrijving voor het blad (apparaat) certificaat. Hiermee wordt de toegang tot de inrichtingsservice voor dat apparaat ingetrokken, terwijl het nog steeds toegang geeft tot andere apparaten die het ondertekeningscertificaat van de inschrijvingsgroep in hun keten hebben. Verwijder de uitgeschakelde individuele inschrijving voor het apparaat niet. Hierdoor kan het apparaat zich opnieuw inschrijven via de inschrijvingsgroep. 
  2. Gebruik de lijst met ingerichte apparaten voor die inschrijvingsgroep om de IoT-hub te vinden waarvoor het apparaat is ingericht en deze uit het identiteitsregister van die hub verwijderen of verwijderen. 
  
  










