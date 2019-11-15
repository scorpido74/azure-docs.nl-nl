---
title: Ankers maken en vinden met behulp van sensors op apparaat C++in/NDK | Microsoft Docs
description: Uitgebreide uitleg over het maken en vinden van ankers met behulp van sensors op apparaat C++in/NDK.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f5de4ae050ff01bc86f8c1e11a2afb2887fd8bd7
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093158"
---
# <a name="how-to-create-and-locate-anchors-using-on-device-sensors-in-cndk"></a>Ankers maken en vinden met behulp van sensors op apparaat C++in/NDK

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Ruimtelijke ankers van Azure kunnen worden gekoppeld aan een apparaat, positionerings sensor gegevens met de ankers die u maakt. Deze gegevens kunnen ook worden gebruikt om snel te bepalen of er ankers zich in de buurt van uw apparaat bevinden. Zie [Grove relokalisatie](../concepts/coarse-reloc.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze hand leiding te volt ooien:

- Basis kennis op C++ en de <a href="https://developer.android.com/ndk/" target="_blank">Android Native Development Kit</a>.
- Lees het [overzicht van ruimtelijke ankers in azure](../overview.md).
- Een van de Quick starts van [5 minuten](../index.yml)is voltooid.
- Lees de [instructies voor het maken en zoeken naar ankers](../create-locate-anchors-overview.md).

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Allow GPS
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);

// Allow WiFi scanning
sensors->WifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

// Allow the set of known BLE beacons
sensors->BluetoothEnabled(true);
sensors->KnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```cpp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);

// Configure the near-device criteria
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();
nearDeviceCriteria->DistanceInMeters(5.0f);
nearDeviceCriteria->MaxResultCount(25);

// Set the session's locate criteria
auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]