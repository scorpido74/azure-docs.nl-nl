---
title: Disenroll-apparaat van Azure IoT Hub Device Provisioning Service
description: Een apparaat destijderen om inrichten via Azure IoT Hub Device Provisioning Service (DPS) te voorkomen?
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: af883da67f4e1bc819514e88ff480526e16124db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974918"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Een apparaat destijderen van azure IoT Hub Device Provisioning Service

Goed beheer van apparaatreferenties is cruciaal voor spraakmakende systemen zoals IoT-oplossingen. Een aanbevolen toepassing voor dergelijke systemen is om een duidelijk plan te hebben van hoe de toegang voor apparaten kan worden ingetrokken wanneer hun referenties, of een SAS-token (Shared Access Signatures) of een X.509-certificaat zijn, in het gedrang kunnen komen. 

Met inschrijving in de apparaatinrichtingsservice kan een apparaat automatisch worden [ingericht.](concepts-auto-provisioning.md) Een ingerichte apparaat is een apparaat dat is geregistreerd bij IoT Hub, waardoor het de eerste [apparaattwinstate](~/articles/iot-hub/iot-hub-devguide-device-twins.md) kan ontvangen en telemetriegegevens kan rapporteren. In dit artikel wordt beschreven hoe u een apparaat losschrijven van uw inrichtingsservice-instantie, zodat het apparaat in de toekomst niet meer kan worden ingericht.

> [!NOTE] 
> Wees je bewust van het beleid voor het opnieuw proberen van apparaten waarvoor u de toegang intrekt. Een apparaat met een oneindig hernieuwend beleid kan bijvoorbeeld voortdurend proberen te registreren bij de inrichtingsservice. Die situatie verbruikt serviceresources en heeft mogelijk invloed op de prestaties.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Blacklist-apparaten met behulp van een afzonderlijke inschrijvingsvermelding

Individuele inschrijvingen zijn van toepassing op één apparaat en kunnen X.509-certificaten of SAS-tokens (in een echte of virtuele TPM) gebruiken als het attestmechanisme. (Apparaten die SAS-tokens gebruiken als hun attestmechanisme, kunnen alleen worden ingericht via een individuele inschrijving.) Als u een apparaat met een afzonderlijke inschrijving op de zwarte lijst wilt zetten, u de inschrijvingsvermelding uitschakelen of verwijderen. 

Ga als u het apparaat tijdelijk op de zwarte lijst zet door de inschrijvingsvermelding uit te schakelen: 

1. Meld u aan bij de Azure-portal en selecteer **Alle bronnen** in het linkermenu.
2. Selecteer in de lijst met bronnen de inrichtingsservice waarvan u uw apparaat op de zwarte lijst wilt zetten.
3. Selecteer in uw **inrichtingsservice Inschrijvingen beheren**en selecteer vervolgens het tabblad **Individuele inschrijvingen.**
4. Selecteer de inschrijvingsvermelding voor het apparaat dat u op de zwarte lijst wilt zetten. 

    ![Selecteer uw individuele inschrijving](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Schuif op de inschrijvingspagina naar de onderkant en selecteer **Uitschakelen** voor de **optie Item inschakelen** en selecteer **Opslaan**.  

   ![Afzonderlijke inschrijvingsvermelding in de portal uitschakelen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Ga als u het apparaat permanent op de zwarte lijst zet door de inschrijvingsvermelding te verwijderen:

1. Meld u aan bij de Azure-portal en selecteer **Alle bronnen** in het linkermenu.
2. Selecteer in de lijst met bronnen de inrichtingsservice waarvan u uw apparaat op de zwarte lijst wilt zetten.
3. Selecteer in uw **inrichtingsservice Inschrijvingen beheren**en selecteer vervolgens het tabblad **Individuele inschrijvingen.**
4. Schakel het selectievakje in naast de inschrijvingsvermelding voor het apparaat dat u op de zwarte lijst wilt zetten. 
5. Selecteer **Verwijderen** boven aan het venster en selecteer **Ja** om te bevestigen dat u de inschrijving wilt verwijderen. 

   ![Afzonderlijke inschrijvingsvermelding in de portal verwijderen](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Nadat u de procedure hebt voltooid, moet u zien dat uw vermelding is verwijderd uit de lijst met afzonderlijke inschrijvingen.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Een X.509 intermediate- of root CA-certificaat op de zwarte lijst zetten met behulp van een inschrijvingsgroep

X.509-certificaten zijn meestal gerangschikt in een vertrouwensketen van certificaten. Als een certificaat in een fase van een keten in het gedrang komt, wordt het vertrouwen verbroken. Het certificaat moet op de zwarte lijst staan om te voorkomen dat de apparaatvoorzieningsservice apparaten stroomafwaarts indient in een keten die dat certificaat bevat. Zie [X.509-certificaten](./concepts-security.md#x509-certificates)voor meer informatie over X.509-certificaten en hoe deze worden gebruikt met de inrichtingsservice. 

Een inschrijvingsgroep is een vermelding voor apparaten die een gemeenschappelijk attestmechanisme van X.509-certificaten delen dat is ondertekend door dezelfde tussenliggende of hoofd-CA. De inschrijvingsgroep-vermelding is geconfigureerd met het X.509-certificaat dat is gekoppeld aan de tussenliggende of hoofd-CA. De vermelding is ook geconfigureerd met alle configuratiewaarden, zoals twin state en IoT-hubverbinding, die worden gedeeld door apparaten met dat certificaat in hun certificaatketen. Als u het certificaat op de zwarte lijst wilt zetten, u de inschrijvingsgroep uitschakelen of verwijderen.

Ga als u het certificaat tijdelijk op de zwarte lijst zet door de inschrijvingsgroep uit te schakelen: 

1. Meld u aan bij de Azure-portal en selecteer **Alle bronnen** in het linkermenu.
2. Selecteer in de lijst met resources de inrichtingsservice waarvan u het ondertekeningscertificaat op de zwarte lijst wilt zetten.
3. Selecteer in uw **inrichtingsservice Inschrijvingen beheren**en selecteer vervolgens het tabblad **Inschrijvingsgroepen.**
4. Selecteer de inschrijvingsgroep met het certificaat dat u op de zwarte lijst wilt zetten.
5. Selecteer **Uitschakelen** op de **knop Invoer inschakelen** en selecteer **Opslaan**.  

   ![Inschrijvingsgroepitem in de portal uitschakelen](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Ga als u het certificaat permanent op de zwarte lijst zet door de inschrijvingsgroep te verwijderen:

1. Meld u aan bij de Azure-portal en selecteer **Alle bronnen** in het linkermenu.
2. Selecteer in de lijst met bronnen de inrichtingsservice waarvan u uw apparaat op de zwarte lijst wilt zetten.
3. Selecteer in uw **inrichtingsservice Inschrijvingen beheren**en selecteer vervolgens het tabblad **Inschrijvingsgroepen.**
4. Schakel het selectievakje in naast de inschrijvingsgroep voor het certificaat dat u op de zwarte lijst wilt zetten. 
5. Selecteer **Verwijderen** boven aan het venster en selecteer **Ja** om te bevestigen dat u de inschrijvingsgroep wilt verwijderen. 

   ![Inschrijvingsgroepvermelding in de portal verwijderen](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Nadat u de procedure hebt voltooid, moet u zien dat uw vermelding is verwijderd uit de lijst met inschrijvingsgroepen.  

> [!NOTE]
> Als u een inschrijvingsgroep voor een certificaat verwijdert, kunnen apparaten met het certificaat in hun certificaatketen zich mogelijk nog steeds inschrijven als een ingeschakelde inschrijvingsgroep voor het basiscertificaat of een ander tussencertificaat hoger in hun certificaat staat. ketting bestaat.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Specifieke apparaten in een inschrijvingsgroep op de zwarte lijst zetten

Apparaten die het X.509-attestmechanisme implementeren, gebruiken de certificaatketen en privésleutel van het apparaat om te verifiëren. Wanneer een apparaat verbinding maakt met en verifieert met Device Provisioning Service, zoekt de service eerst naar een individuele inschrijving die overeenkomt met de referenties van het apparaat. De service zoekt vervolgens in inschrijvingsgroepen om te bepalen of het apparaat kan worden ingericht. Als de service een uitgeschakelde individuele inschrijving voor het apparaat vindt, voorkomt dit dat het apparaat verbinding maakt. De service voorkomt de verbinding, zelfs als er een ingeschakelde inschrijvingsgroep voor een tussenliggende of hoofd-CA in de certificaatketen van het apparaat bestaat. 

Voer de volgende stappen uit om een afzonderlijk apparaat in een inschrijvingsgroep op de zwarte lijst te zetten:

1. Meld u aan bij de Azure-portal en selecteer **Alle bronnen** in het linkermenu.
2. Selecteer in de lijst met bronnen de inrichtingsservice die de inschrijvingsgroep bevat voor het apparaat dat u op de zwarte lijst wilt zetten.
3. Selecteer in uw **inrichtingsservice Inschrijvingen beheren**en selecteer vervolgens het tabblad **Individuele inschrijvingen.**
4. Selecteer bovenaan de knop **Afzonderlijke inschrijving toevoegen.** 
5. Selecteer **X.509** op de pagina **Inschrijving toevoegen** als het **attestmechanisme** voor het apparaat.

    Upload het apparaatcertificaat en voer de apparaat-id in van het apparaat dat op de zwarte lijst moet worden geplaatst. Gebruik voor het certificaat het ondertekende certificaat van de eindentiteit dat op het apparaat is geïnstalleerd. Het apparaat gebruikt het ondertekende certificaat van de eindentiteit voor verificatie.

    ![Apparaateigenschappen instellen voor het apparaat op de zwarte lijst](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Schuif naar de onderkant van de pagina **Inschrijving toevoegen** en selecteer **Uitschakelen** op de optie **Item inschakelen** en selecteer **Opslaan**. 

    [![Afzonderlijke inschrijvingstoegang voor uitgeschakelde personen gebruiken om apparaat uit groepsinschrijving uit te schakelen in de portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Wanneer u uw inschrijving hebt gemaakt, moet u de inschrijving voor een uitgeschakeld apparaat op het tabblad **Individuele inschrijvingen** zien. 

## <a name="next-steps"></a>Volgende stappen

Uitschrijving maakt ook deel uit van het grotere deprovisioningproces. Het deprovisioneren van een apparaat omvat zowel het verwijderen van de inprovisioningsservice als het verwijderen van de iot-hub. Zie Apparaten die voorheen [automatisch waren ingericht](how-to-unprovision-devices.md) voor meer informatie over het volledige proces. 

