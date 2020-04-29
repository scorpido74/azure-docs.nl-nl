---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 5ff25155529b9a763bcf5af6cfb15593e9da9853
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421936"
---
Het verwerken van gecomprimeerde audio wordt geïmplementeerd met behulp van [gstreamer](https://gstreamer.freedesktop.org). Om licentie redenen GStreamer binaire bestanden niet worden gecompileerd en gekoppeld aan de spraak-SDK. Ontwikkel aars moeten verschillende afhankelijkheden en invoeg toepassingen installeren.

# <a name="ubuntu-1604-1804-or-debian-9"></a>[Ubuntu, 16,04, 18,04 of Debian 9](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rehl--centos"></a>[REHL/CentOS](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> Volg de instructies in RHEL/CentOS voor het [configureren van openssl voor Linux](../../../../how-to-configure-openssl-linux.md).

---