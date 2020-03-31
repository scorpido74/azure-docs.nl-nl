---
title: X.509-certificaten rollen in Azure IoT Hub Device Provisioning Service
description: X.509-certificaten rollen met het DPS-exemplaar (Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 4d5ddb229cd6a41235990437bc0f8db08e3381ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974884"
---
# <a name="how-to-roll-x509-device-certificates"></a>X.509-apparaatcertificaten rollen

Tijdens de levenscyclus van uw IoT-oplossing moet u certificaten rollen. Twee van de belangrijkste redenen voor het rollen van certificaten zou een inbreuk op de beveiliging, en certificaat verloopt. 

Rolling certificaten is een beveiligingsaanbevolen praktijk om uw systeem te beveiligen in het geval van een inbreuk. Als onderdeel van [Assume Breach Methodologie](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)pleit Microsoft voor de noodzaak van reactieve beveiligingsprocessen, samen met preventieve maatregelen. Het rollen van uw apparaatcertificaten moet worden opgenomen als onderdeel van deze beveiligingsprocessen. De frequentie waarin u uw certificaten rolt, is afhankelijk van de beveiligingsbehoeften van uw oplossing. Klanten met zeer gevoelige gegevens kunnen dagelijks een certificaat rollen, terwijl anderen hun certificaten om de paar jaar uitrollen.

Rolling device certificaten omvatten het bijwerken van het certificaat dat is opgeslagen op het apparaat en de IoT-hub. Daarna kan het apparaat zichzelf opnieuw inrichten met de [IoT-hub](concepts-auto-provisioning.md) met behulp van normale automatische inrichting met de Service Apparaatinrichting.


## <a name="obtain-new-certificates"></a>Nieuwe certificaten verkrijgen

Er zijn veel manieren om nieuwe certificaten voor uw IoT-apparaten te verkrijgen. Deze omvatten het verkrijgen van certificaten uit de apparaatfabriek, het genereren van uw eigen certificaten en het beheren van een derde partij voor het maken van certificaten voor u. 

Certificaten worden door elkaar ondertekend om een vertrouwensketen te vormen, van een basis-CA-certificaat tot een [bladcertificaat](concepts-security.md#end-entity-leaf-certificate). Een ondertekeningscertificaat is het certificaat dat wordt gebruikt om het bladcertificaat aan het einde van de vertrouwensketen te ondertekenen. Een ondertekeningscertificaat kan een root CA-certificaat zijn of een tussentijds certificaat in de vertrouwensketen. Zie [X.509-certificaten voor](concepts-security.md#x509-certificates)meer informatie.
 
Er zijn twee verschillende manieren om een ondertekeningscertificaat te verkrijgen. De eerste manier, die wordt aanbevolen voor productiesystemen, is om een ondertekeningscertificaat te kopen bij een rootcertificaatautoriteit (CA). Op deze manier ketent beveiliging naar een vertrouwde bron. 

De tweede manier is om je eigen X.509-certificaten te maken met behulp van een tool als OpenSSL. Deze aanpak is geweldig voor het testen van X.509-certificaten, maar biedt weinig garanties rond beveiliging. We raden u aan deze aanpak alleen te gebruiken voor het testen, tenzij u bereid bent om op te treden als uw eigen CA-provider.
 

## <a name="roll-the-certificate-on-the-device"></a>Het certificaat op het apparaat rollen

Certificaten op een apparaat moeten altijd op een veilige plaats worden opgeslagen, zoals een [hardwarebeveiligingsmodule (HSM).](concepts-device.md#hardware-security-module) De manier waarop u apparaatcertificaten rolt, is afhankelijk van de manier waarop ze zijn gemaakt en geïnstalleerd in de apparaten. 

Als u uw certificaten van een derde partij hebt gekregen, moet u nagaan hoe ze hun certificaten rollen. Het proces kan worden opgenomen in uw overeenkomst met hen, of het kan een aparte dienst die zij aanbieden. 

Als u uw eigen apparaatcertificaten beheert, moet u uw eigen pijplijn maken voor het bijwerken van certificaten. Zorg ervoor dat zowel oude als nieuwe bladcertificaten dezelfde algemene naam (CN) hebben. Door dezelfde CN te hebben, kan het apparaat zichzelf opnieuw inrichten zonder een dubbele registratierecord te maken. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Het certificaat in de IoT-hub rollen

Het apparaatcertificaat kan handmatig worden toegevoegd aan een IoT-hub. Het certificaat kan ook worden geautomatiseerd met behulp van een apparaatinrichtingsserviceexemplaar. In dit artikel gaan we ervan uit dat een apparaatinrichtingsinstantie wordt gebruikt om automatische inrichting te ondersteunen.

Wanneer een apparaat in eerste instantie is ingericht via automatische inrichting, start het op en neemt het contact op met de inrichtingsservice. De inrichtingsservice reageert door een identiteitscontrole uit te voeren voordat een apparaatidentiteit wordt gemaakt in een IoT-hub met het bladcertificaat van het apparaat als referentie. De inrichtingsservice vertelt vervolgens aan het apparaat aan welke IoT-hub het is toegewezen en het apparaat gebruikt vervolgens het bladcertificaat om de IoT-hub te verifiëren en verbinding te maken. 

Zodra een nieuw bladcertificaat naar het apparaat is gerold, kan het geen verbinding meer maken met de IoT-hub omdat het een nieuw certificaat gebruikt om verbinding te maken. De IoT-hub herkent het apparaat alleen met het oude certificaat. Het resultaat van de verbindingspoging van het apparaat is een "ongeautoriseerde" verbindingsfout. Als u deze fout wilt oplossen, moet u de inschrijvingsvermelding voor het apparaat bijwerken om rekening te houden met het nieuwe bladcertificaat van het apparaat. Vervolgens kan de inrichtingsservice de registergegevens van het IoT Hub-apparaat bijwerken wanneer het apparaat opnieuw wordt ingericht. 

Een mogelijke uitzondering op deze verbindingsfout is een scenario waarin u een [inschrijvingsgroep](concepts-service.md#enrollment-group) voor uw apparaat hebt gemaakt in de inrichtingsservice. Als u in dit geval de basis- of tussenliggende certificaten niet rolt in de vertrouwensketen van het apparaat, wordt het apparaat herkend als het nieuwe certificaat deel uitmaakt van de vertrouwensketen die is gedefinieerd in de inschrijvingsgroep. Als dit scenario zich voordoet als een reactie op een inbreuk op de beveiliging, moet u op zijn minst de specifieke apparaatcertificaten in de groep op de zwarte lijst zetten die als geschonden worden beschouwd. Zie [Specifieke apparaten in een inschrijvingsgroep op de zwarte lijst zetten](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group)voor meer informatie .

Het bijwerken van inschrijvingsitems voor opgerolde certificaten wordt uitgevoerd op de pagina **Inschrijvingen beheren.** Voer de volgende stappen uit om toegang te krijgen tot die pagina:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar het exemplaar van de IoT Hub Device Provisioning Service met de inschrijvingsvermelding voor uw apparaat.

2. Klik op **Inschrijvingen beheren**.

    ![Inschrijvingen beheren](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Hoe u de inschrijvingsvermelding bijwerkt, hangt af van de vraag of u afzonderlijke inschrijvingen of groepsinschrijvingen gebruikt. Ook de aanbevolen procedures verschillen afhankelijk van of u certificaten rolt vanwege een inbreuk op de beveiliging of het verlopen van certificaten. In de volgende secties wordt beschreven hoe u met deze updates omgaat.


## <a name="individual-enrollments-and-security-breaches"></a>Individuele inschrijvingen en inbreuken op de beveiliging

Als u certificaten rolt als reactie op een beveiligingslek, moet u de volgende benadering gebruiken die het huidige certificaat onmiddellijk verwijdert:

1. Klik **op Individuele inschrijvingen**en klik op het vermelding van de registratie-id in de lijst. 

2. Klik **op** de knop Huidig certificaat verwijderen en klik vervolgens op het mappictogram om het nieuwe certificaat te selecteren dat moet worden geüpload voor het inschrijvingsitem. Klik **op Opslaan** als u klaar bent.

    Deze stappen moeten worden voltooid voor het primaire en secundaire certificaat, als beide zijn aangetast.

    ![Afzonderlijke inschrijvingen beheren](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Zodra het gecompromitteerde certificaat uit de inrichtingsservice is verwijderd, kan het certificaat nog steeds worden gebruikt om apparaatverbindingen met de IoT-hub te maken zolang er een apparaatregistratie voor bestaat. U dit op twee manieren aanpakken: 

    De eerste manier zou zijn om handmatig naar uw IoT-hub te navigeren en onmiddellijk de apparaatregistratie te verwijderen die is gekoppeld aan het gecompromitteerde certificaat. Wanneer de apparaatvoorzieningen vervolgens opnieuw worden voorzien van een bijgewerkt certificaat, wordt er een nieuwe apparaatregistratie gemaakt.     

    ![Registratie van IoT-hub-apparaten verwijderen](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    De tweede manier zou zijn om reprovisioning ondersteuning te gebruiken om het apparaat te herinrichten naar dezelfde IoT-hub. Deze aanpak kan worden gebruikt om het certificaat voor de apparaatregistratie op de IoT-hub te vervangen. Zie [Apparaten opnieuw inrichten](how-to-reprovision.md)voor meer informatie .

## <a name="individual-enrollments-and-certificate-expiration"></a>Individuele inschrijvingen en het verlopen van certificaten

Als u certificaten rolt om certificaatverlopen te verwerken, moet u de secundaire certificaatconfiguratie als volgt gebruiken om downtime te verminderen voor apparaten die proberen in te richten.

Later, wanneer het secundaire certificaat ook bijna verloopt en moet worden uitgerold, u roteren naar het gebruik van de primaire configuratie. Roteren tussen de primaire en secundaire certificaten op deze manier vermindert downtime voor apparaten die proberen in te richten.


1. Klik **op Individuele inschrijvingen**en klik op het vermelding van de registratie-id in de lijst. 

2. Klik **op Secundair certificaat** en klik vervolgens op het mappictogram om het nieuwe certificaat te selecteren dat moet worden geüpload voor het inschrijvingsitem. Klik op **Opslaan**.

    ![Afzonderlijke inschrijvingen beheren met behulp van het secundaire certificaat](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Later, wanneer het primaire certificaat is verlopen, komt u terug en verwijdert u dat primaire certificaat door op de knop **Huidig certificaat verwijderen** te klikken.

## <a name="enrollment-groups-and-security-breaches"></a>Inschrijvingsgroepen en inbreuken op de beveiliging

Als u een groepsinschrijving wilt bijwerken als reactie op een beveiligingslek, moet u een van de volgende benaderingen gebruiken die de huidige hoofd-CA of het tussentijdse certificaat onmiddellijk verwijderen.

#### <a name="update-compromised-root-ca-certificates"></a>Gecompromitteerde basis-CA-certificaten bijwerken

1. Klik op het tabblad **Certificaten** voor de service-instantie Apparaatvoorziening.

2. Klik op het gecompromitteerde certificaat in de lijst en klik vervolgens op **verwijderen.** Bevestig de verwijdering door de certificaatnaam in te voeren en klik op **OK**. Herhaal dit proces voor alle gecompromitteerde certificaten.

    ![Root CA-certificaat verwijderen](./media/how-to-roll-certificates/delete-root-cert.png)

3. Volg de stappen die zijn beschreven in [Geverifieerde CA-certificaten configureren](how-to-verify-certificates.md) om nieuwe basisCA-certificaten toe te voegen en te verifiëren.

4. Klik op het tabblad **Inschrijvingen beheren** voor de service-instantie Apparaatvoorziening en klik op de lijst **Inschrijvingsgroepen.** Klik op de naam van uw inschrijvingsgroep in de lijst.

5. Klik **op CA-certificaat**en selecteer uw nieuwe basis-CA-certificaat. Klik vervolgens op **Opslaan**. 

    ![Het nieuwe basis-CA-certificaat selecteren](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Zodra het gecompromitteerde certificaat uit de inrichtingsservice is verwijderd, kan het certificaat nog steeds worden gebruikt om apparaatverbindingen met de IoT-hub te maken zolang apparaatregistraties daar bestaan. U dit op twee manieren aanpakken: 

    De eerste manier zou zijn om handmatig naar uw IoT-hub te navigeren en onmiddellijk de apparaatregistratie te verwijderen die is gekoppeld aan het gecompromitteerde certificaat. Wanneer uw apparaten vervolgens opnieuw worden voorzien van bijgewerkte certificaten, wordt voor elk apparaat een nieuwe apparaatregistratie gemaakt.     

    ![Registratie van IoT-hub-apparaten verwijderen](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    De tweede manier zou zijn om reprovisioning-ondersteuning te gebruiken om uw apparaten opnieuw te inrichten naar dezelfde IoT-hub. Deze aanpak kan worden gebruikt om certificaten te vervangen voor apparaatregistraties op de IoT-hub. Zie [Apparaten opnieuw inrichten](how-to-reprovision.md)voor meer informatie .



#### <a name="update-compromised-intermediate-certificates"></a>Gecompromitteerde tussenliggende certificaten bijwerken

1. Klik **op Inschrijvingsgroepen**en klik vervolgens op de groepsnaam in de lijst. 

2. Klik **op Tussencertificaat**en **huidige certificaten verwijderen**. Klik op het mappictogram om naar het nieuwe tussencertificaat te gaan dat voor de inschrijvingsgroep moet worden geüpload. Klik **op Opslaan** wanneer u klaar bent. Deze stappen moeten worden voltooid voor zowel het primaire als het secundaire certificaat, als beide zijn aangetast.

    Dit nieuwe tussencertificaat moet worden ondertekend met een geverifieerd basis-CA-certificaat dat al is toegevoegd aan de inrichtingsservice. Zie [X.509-certificaten voor](concepts-security.md#x509-certificates)meer informatie.

    ![Afzonderlijke inschrijvingen beheren](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Zodra het gecompromitteerde certificaat uit de inrichtingsservice is verwijderd, kan het certificaat nog steeds worden gebruikt om apparaatverbindingen met de IoT-hub te maken zolang apparaatregistraties daar bestaan. U dit op twee manieren aanpakken: 

    De eerste manier zou zijn om handmatig naar uw IoT-hub te navigeren en onmiddellijk de apparaatregistratie te verwijderen die is gekoppeld aan het gecompromitteerde certificaat. Wanneer uw apparaten vervolgens opnieuw worden voorzien van bijgewerkte certificaten, wordt voor elk apparaat een nieuwe apparaatregistratie gemaakt.     

    ![Registratie van IoT-hub-apparaten verwijderen](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    De tweede manier zou zijn om reprovisioning-ondersteuning te gebruiken om uw apparaten opnieuw te inrichten naar dezelfde IoT-hub. Deze aanpak kan worden gebruikt om certificaten te vervangen voor apparaatregistraties op de IoT-hub. Zie [Apparaten opnieuw inrichten](how-to-reprovision.md)voor meer informatie .


## <a name="enrollment-groups-and-certificate-expiration"></a>Inschrijvingsgroepen en certificaatverloop

Als u certificaten rolt om certificaatverlopen te verwerken, moet u de secundaire certificaatconfiguratie als volgt gebruiken om te voorkomen dat apparaten worden ingericht.

Later, wanneer het secundaire certificaat ook bijna verloopt en moet worden uitgerold, u roteren naar het gebruik van de primaire configuratie. Roteren tussen de primaire en secundaire certificaten op deze manier zorgt voor geen downtime voor apparaten die proberen in te richten. 

#### <a name="update-expiring-root-ca-certificates"></a>Verlopen basis-CA-certificaten bijwerken

1. Volg de stappen die zijn beschreven in [Geverifieerde CA-certificaten configureren](how-to-verify-certificates.md) om nieuwe basisCA-certificaten toe te voegen en te verifiëren.

2. Klik op het tabblad **Inschrijvingen beheren** voor de service-instantie Apparaatvoorziening en klik op de lijst **Inschrijvingsgroepen.** Klik op de naam van uw inschrijvingsgroep in de lijst.

3. Klik op **CA-certificaat**en selecteer uw nieuwe basis-CA-certificaat onder de configuratie **secundair certificaat.** Klik vervolgens op **Opslaan**. 

    ![Het nieuwe basis-CA-certificaat selecteren](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Klik later wanneer het primaire certificaat is verlopen op het tabblad **Certificaten** voor de serviceinstantie Apparaatvoorziening. Klik op het verlopen certificaat in de lijst en klik vervolgens op **verwijderen.** Bevestig de verwijdering door de certificaatnaam in te voeren en klik op **OK**.

    ![Root CA-certificaat verwijderen](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Verlopen tussentijdse certificaten bijwerken


1. Klik **op Inschrijvingsgroepen**en klik op de groepsnaam in de lijst. 

2. Klik **op Secundair certificaat** en klik vervolgens op het mappictogram om het nieuwe certificaat te selecteren dat moet worden geüpload voor het inschrijvingsitem. Klik op **Opslaan**.

    Dit nieuwe tussencertificaat moet worden ondertekend met een geverifieerd basis-CA-certificaat dat al is toegevoegd aan de inrichtingsservice. Zie [X.509-certificaten voor](concepts-security.md#x509-certificates)meer informatie.

   ![Afzonderlijke inschrijvingen beheren met behulp van het secundaire certificaat](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Later, wanneer het primaire certificaat is verlopen, komt u terug en verwijdert u dat primaire certificaat door op de knop **Huidig certificaat verwijderen** te klikken.


## <a name="reprovision-the-device"></a>Het apparaat opnieuw inrichten

Zodra het certificaat is uitgerold op zowel het apparaat als de apparaatinrichtingsservice, kan het apparaat zichzelf opnieuw inrichten door contact op te nemen met de service Apparaatvoorziening. 

Een eenvoudige manier om apparaten te programmeren om het apparaat opnieuw te inrichten, is door het apparaat te programmeren om contact op te nemen met de inrichtingsservice om door de inrichtingsstroom te gaan als het apparaat een "ongeautoriseerde" fout ontvangt van een poging om verbinding te maken met de IoT-hub.

Een andere manier is dat zowel de oude als de nieuwe certificaten geldig zijn voor een korte overlap en de IoT-hub gebruiken om een opdracht naar apparaten te sturen om ze opnieuw te laten registreren via de provisioning-service om hun IoT Hub-verbindingsgegevens bij te werken. Omdat elk apparaat opdrachten anders kan verwerken, moet u uw apparaat programmeren om te weten wat u moet doen wanneer de opdracht wordt aangeroepen. Er zijn verschillende manieren waarop u uw apparaat bevelen via IoT Hub en we raden u aan [directe methoden](../iot-hub/iot-hub-devguide-direct-methods.md) of [taken](../iot-hub/iot-hub-devguide-jobs.md) te gebruiken om het proces te starten.

Zodra de herinrichting is voltooid, kunnen apparaten verbinding maken met IoT Hub met hun nieuwe certificaten.


## <a name="blacklist-certificates"></a>Blacklist-certificaten

Als reactie op een inbreuk op de beveiliging moet u mogelijk een apparaatcertificaat op de zwarte lijst zetten. Als u een apparaatcertificaat op de zwarte lijst wilt zetten, schakelt u de inschrijvingsvermelding voor het doelapparaat/certificaat uit. Zie apparaten op de zwarte lijst plaatsen in het artikel [Deinschrijving beheren](how-to-revoke-device-access-portal.md) voor meer informatie.

Zodra een certificaat is opgenomen als onderdeel van een uitgeschakelde inschrijvingsvermelding, mislukken alle pogingen om zich te registreren bij een IoT-hub met behulp van die certificaten, zelfs als het is ingeschakeld als onderdeel van een andere inschrijvingsvermelding.
 



## <a name="next-steps"></a>Volgende stappen

- Zie [Beveiliging](concepts-security.md) voor meer informatie over X.509-certificaten in de service voor apparaatinrichting 
- Zie Hoe u certificaten verifiëren voor het bezit van X.509 CA-certificaten voor X.509 CA-certificaten voor meer informatie over het controleren van [bewijsbewijzen](how-to-verify-certificates.md) voor X.509 CA-certificaten
- Zie [Apparaatinschrijvingen beheren met Azure-portal](how-to-manage-enrollments.md)voor meer informatie over het gebruik van de portal om een inschrijvingsgroep te maken.










