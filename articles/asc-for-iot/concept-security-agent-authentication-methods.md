---
title: Verificatiemethoden voor beveiligingsagent
description: Meer informatie over de verschillende verificatiemethoden die beschikbaar zijn bij het gebruik van de Azure Security Center for IoT-service.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0d9d51292c3cae9634af917819b558cdfd2fa04b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311520"
---
# <a name="security-agent-authentication-methods"></a>Verificatiemethoden voor beveiligingsagent

In dit artikel worden de verschillende verificatiemethoden uitgelegd die u gebruiken met de AzureIoTSecurity-agent om te verifiëren met de IoT Hub.

Voor elk apparaat dat is aangesloten op Azure Security Center voor IoT in de IoT-hub, is een beveiligingsmodule vereist. Om het apparaat te verifiëren, kan Azure Security Center for IoT een van de twee methoden gebruiken. Kies de methode die het beste werkt voor uw bestaande IoT-oplossing.

> [!div class="checklist"]
> * SecurityModule, optie
> * Apparaatoptie

## <a name="authentication-methods"></a>Verificatiemethoden

De twee methoden voor de AzureIoTSecurity-agent om verificatie uit te voeren:

- **Verificatiemodus SecurityModule**<br>
De agent wordt geverifieerd met behulp van de identiteit van de beveiligingsmodule onafhankelijk van de identiteit van het apparaat.
Gebruik dit verificatietype als u wilt dat de beveiligingsagent een speciale verificatiemethode gebruikt via de beveiligingsmodule (alleen symmetrische sleutel).

- **Apparaatverificatiemodus**<br>
Bij deze methode verifieert de beveiligingsagent eerst met de identiteit van het apparaat. Na de eerste verificatie voert de Azure Security Center for IoT-agent een **REST-oproep** uit naar de IoT-hub met behulp van de REST-API met de verificatiegegevens van het apparaat. De Azure Security Center for IoT-agent vraagt vervolgens de verificatiemethode en gegevens van de beveiligingsmodule uit de IoT-hub. In de laatste stap voert de Azure Security Center for IoT-agent een verificatie uit ten opzichte van de Azure Security Center for IoT-module.

Gebruik dit verificatietype als u wilt dat de beveiligingsagent een bestaande apparaatverificatiemethode (zelfondertekend certificaat of symmetrische sleutel) opnieuw gebruikt.

Zie [Installatieparameters voor beveiligingsagents](#security-agent-installation-parameters) voor meer informatie over het configureren.

## <a name="authentication-methods-known-limitations"></a>Bekende beperkingen voor verificatiemethoden

- De verificatiemodus **securitymodule** ondersteunt alleen symmetrische sleutelverificatie.
- Ca-Signed certificaat wordt niet **Device** ondersteund door apparaatverificatiemodus.

## <a name="security-agent-installation-parameters"></a>Installatieparameters voor beveiligingsagent

Bij [het implementeren van een beveiligingsagent](how-to-deploy-agent.md)moeten verificatiegegevens als argumenten worden opgegeven.
Deze argumenten zijn gedocumenteerd in de volgende tabel.

|Naam van Linux-parameter | Windows-parameternaam | Stenoparameter |Beschrijving|Opties|
|---------------------|---------------|---------|---------------|---------------|
|verificatie-identiteit|VerificatieIdentiteit|aui aui|Verificatie-identiteit| **Beveiligingsmodule** of **apparaat**|
|verificatiemethode|Verificatiemethode|Aum|Verificatiemethode|**SymmetricKey** of **SelfSignedCertificate**|
|bestandspad|Filepath|v|Absoluut volledig pad voor het bestand met het certificaat of de symmetrische toets| |
|hostnaam|HostName|Hn|FQDN van de IoT Hub|Voorbeeld: ContosoIotHub.azure-devices.net|
|apparaat-id|DeviceId|Di|Apparaat-id|Voorbeeld: MyDevice1|
|certificaat-locatie-soort|CertificateLocationKind CertificateLocationKind|Cl|Opslaglocatie certificaat|**LocalFile** of **Store**|
|

Wanneer u het script van de installatiebeveiligingsagent gebruikt, wordt de volgende configuratie automatisch uitgevoerd. Als u de verificatie van de beveiligingsagent handmatig wilt bewerken, bewerkt u het config-bestand.

## <a name="change-authentication-method-after-deployment"></a>Verificatiemethode wijzigen na implementatie

Bij het implementeren van een beveiligingsagent met een installatiescript wordt automatisch een configuratiebestand gemaakt.

Als u verificatiemethoden na implementatie wilt wijzigen, is handmatig bewerken van het configuratiebestand vereist.

### <a name="c-based-security-agent"></a>C#-gebaseerde beveiligingsagent

_Verificatie.config_ bewerken met de volgende parameters:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>C-gebaseerde beveiligingsagent

Hiermee bewerkt _u LocalConfiguration.json_ met de volgende parameters:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Zie ook

- [Overzicht van beveiligingsagenten](security-agent-architecture.md)
- [Beveiligingsagent implementeren](how-to-deploy-agent.md)
- [Toegang tot onbewerkte beveiligingsgegevens](how-to-security-data-access.md)
