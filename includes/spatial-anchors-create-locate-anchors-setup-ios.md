---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "83006421"
---
## <a name="setting-up-the-library"></a>De bibliotheek instellen

Roep `Start()` aan om uw sessie in staat te stellen omgevingsgegevens te verwerken.

Als u gebeurtenissen wilt afhandelen die door de sessie worden gegenereerd, stelt u de eigenschap `delegate` van uw sessie in op een object, zoals uw weergave. Dit object moet het `SSCCloudSpatialAnchorSessionDelegate`-protocol implementeren.
