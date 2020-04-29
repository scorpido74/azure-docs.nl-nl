---
title: Verificatie methoden voor beveiligings agenten
description: Meer informatie over de verschillende verificatie methoden die beschikbaar zijn bij het gebruik van de Azure Security Center voor IoT-service.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311520"
---
# <a name="security-agent-authentication-methods"></a>Verificatie methoden voor beveiligings agenten

In dit artikel worden de verschillende verificatie methoden beschreven die u met de AzureIoTSecurity-agent kunt gebruiken om te verifiëren met de IoT Hub.

Een beveiligings module is vereist voor elk apparaat waarvoor Azure Security Center voor IoT in de IoT Hub is geïnstalleerd. Om het apparaat te verifiëren, kunnen Azure Security Center voor IoT een van de volgende twee methoden gebruiken. Kies de methode die het beste werkt voor uw bestaande IoT-oplossing.

> [!div class="checklist"]
> * SecurityModule optie
> * Optie apparaat

## <a name="authentication-methods"></a>Verificatiemethoden

De twee methoden voor de AzureIoTSecurity-agent om verificatie uit te voeren:

- **SecurityModule** -verificatie modus<br>
De agent wordt geverifieerd met behulp van de identiteit van de beveiligings module, onafhankelijk van de apparaat-id.
Gebruik dit verificatie type als u wilt dat de beveiligings agent een specifieke verificatie methode gebruikt via de beveiligings module (alleen symmetrische sleutel).

- Verificatie modus **apparaat**<br>
In deze methode verifieert de beveiligings agent eerst met de apparaat-id. Na de initiële verificatie voert de Azure Security Center voor IoT-agent een **rest** -aanroep naar de IOT hub met behulp van de rest API met de verificatie gegevens van het apparaat. De Azure Security Center voor de IoT-agent vraagt vervolgens de verificatie methode van de beveiligings module en de gegevens van de IoT Hub. In de laatste stap voert de Azure Security Center voor IoT-agent een verificatie uit op basis van de Azure Security Center voor IoT-module.

Gebruik dit verificatie type als u wilt dat de beveiligings agent een bestaande verificatie methode voor apparaten (zelfondertekend certificaat of symmetrische sleutel) opnieuw gebruikt.

Zie [para meters voor de beveiligings agent installeren](#security-agent-installation-parameters) voor meer informatie over het configureren van.

## <a name="authentication-methods-known-limitations"></a>Bekende beperkingen van de verificatie methoden

- De **SecurityModule** -verificatie modus ondersteunt alleen symmetrische sleutel verificatie.
- CA-ondertekend certificaat wordt niet ondersteund door de verificatie modus van het **apparaat** .

## <a name="security-agent-installation-parameters"></a>Beveiligings agent-installatie parameters

Wanneer u [een beveiligings agent implementeert](how-to-deploy-agent.md), moeten de verificatie gegevens als argumenten worden gegeven.
Deze argumenten worden beschreven in de volgende tabel.

|Naam van Linux-para meter | Naam van Windows-para meter | Steno parameter |Beschrijving|Opties|
|---------------------|---------------|---------|---------------|---------------|
|verificatie-identiteit|AuthenticationIdentity|aui|Verificatie-identiteit| **SecurityModule** of **apparaat**|
|verificatie-methode|Authentic|aum|Verificatiemethode|**SymmetricKey** of **SelfSignedCertificate**|
|bestandspad|Bestandspad|v|Absoluut volledig pad voor het bestand met het certificaat of de symmetrische sleutel| |
|hostnaam|HostName|hn|FQDN-namen van de IoT Hub|Voor beeld: ContosoIotHub.azure-devices.net|
|apparaat-id|DeviceId|di|Apparaat-id|Voor beeld: MyDevice1|
|certificaat locatie-soort|CertificateLocationKind|cl|Opslag locatie van certificaat|**Lokaal** bestand of **Archief**|
|

Wanneer u het script voor de installatie van de beveiligings agent gebruikt, wordt de volgende configuratie automatisch uitgevoerd. Bewerk het configuratie bestand om de verificatie van de beveiligings agent hand matig te bewerken.

## <a name="change-authentication-method-after-deployment"></a>Verificatie methode na implementatie wijzigen

Wanneer u een beveiligings agent met een installatie script implementeert, wordt automatisch een configuratie bestand gemaakt.

Als u verificatie methoden na de implementatie wilt wijzigen, is hand matig bewerken van het configuratie bestand vereist.

### <a name="c-based-security-agent"></a>Beveiligings agent op basis van C#

Bewerk _Authentication. config_ met de volgende para meters:

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

### <a name="c-based-security-agent"></a>Op C gebaseerde beveiligings agent

Bewerk _LocalConfiguration. json_ met de volgende para meters:

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

- [Overzicht van beveiligings agenten](security-agent-architecture.md)
- [Beveiligings agent implementeren](how-to-deploy-agent.md)
- [Toegang tot onbewerkte beveiligingsgegevens](how-to-security-data-access.md)
