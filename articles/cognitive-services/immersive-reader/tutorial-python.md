---
title: 'Zelfstudie: Start de meeslepende lezer met Python'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie maak je een Python-applicatie die de Immersive Reader lanceert.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.openlocfilehash: a252afae0a007ee0b791b56d19ffb0685848d30a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844357"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Zelfstudie: De meeslepende lezer starten met het Voorbeeldproject Python

In het [overzicht](./overview.md)leerde je over wat de Immersive Reader is en hoe het bewezen technieken implementeert om het begrijpend lezen te verbeteren voor taalstudenten, opkomende lezers en studenten met leerverschillen. Deze zelfstudie gaat over het maken van een Python-webtoepassing die de Immersive Reader lanceert. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Python-web-app maken met Pip, Flask, Jinja en virtualenv met behulp van een voorbeeldproject
> * Een toegangstoken verkrijgen
> * Start de meeslepende lezer met voorbeeldinhoud

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Immersive Reader-bron die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](./how-to-create-immersive-reader.md) om je op te stellen. U hebt een aantal waarden nodig die hier zijn gemaakt bij het configureren van de omgevingseigenschappen. Sla de uitvoer van uw sessie op in een tekstbestand voor toekomstige verwijzingen.
* [Git](https://git-scm.com/)
* [Meeslepende Reader SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) en [pip.](https://docs.python.org/3/installing/index.html) Beginnend met Python 3.4, is pip standaard inbegrepen bij de Binaire Installateurs van Python.
* [Kolf](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) en [virtualenvwrapper-win voor Windows](https://pypi.org/project/virtualenvwrapper-win/) of [virtualenvwrapper voor OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [module aanvragen](https://pypi.org/project/requests/2.7.0/)
* Een IDE zoals [Visual Studio Code](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Verificatiereferenties configureren

Maak een nieuw bestand genaamd _.env_en plak de volgende code erin, met de waarden die worden gegeven toen u uw Immersive Reader-bron maakte.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Zorg ervoor dat u dit bestand niet in bronbeheer verbindt, omdat het geheimen bevat die niet openbaar mogen worden gemaakt.

Het **getimmersivereadertoken** API-eindpunt moet worden beveiligd achter een of andere vorm van authenticatie (bijvoorbeeld [OAuth](https://oauth.net/2/)) om te voorkomen dat onbevoegde gebruikers tokens verkrijgen om te gebruiken tegen uw Immersive Reader-service en facturering; dat werk valt buiten het bereik van deze tutorial.

## <a name="create-a-python-web-app-on-windows"></a>Een Python-web-app maken in Windows

Maak een Python-web-app met `flask` behulp van Windows.

Installeer [Git](https://git-scm.com/).

Nadat Git is geïnstalleerd open een Opdrachtprompt en 'kloon' de Immersive Reader SDK Git repository naar een map op uw computer

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Python [Python](https://www.python.org/downloads/)installeren .

Schakel het selectievakje Python toevoegen aan PAD in.

![Dialoogvenster Python Windows installeren Stap 1](./media/pythoninstallone.jpg)

Voeg optionele functies toe door de vakjes aan te vinken en klik vervolgens op de knop 'Volgende'.

![Dialoogvenster Python Windows installeren Stap 2](./media/pythoninstalltwo.jpg)

Kies 'Aangepaste installatie' en stel het installatiepad in `C:\Python37-32\` als uw hoofdmap, bijvoorbeeld klik op de knop 'Installeren'.

![Dialoogvenster Python Windows installeren Stap 3](./media/pythoninstallthree.jpg)

Nadat python-installatie is voltooid, `cd` opent u een opdrachtprompt en naar de map Python Scripts.

```cmd
cd C:\Python37-32\Scripts
```

Installeer Flask.

```cmd
pip install flask
```

Installeer Jinja2. Een volledig uitgeruste sjabloonengine voor Python.

```cmd
pip install jinja2
```

Installeer virtualenv. Een tool om geïsoleerde Python-omgevingen te maken.

```cmd
pip install virtualenv
```

Installeer virtualenvwrapper-win. Het idee achter virtualenvwrapper is om het gebruik van virtualenv te vergemakkelijken.

```cmd
pip install virtualenvwrapper-win
```

Installeer de module aanvragen. Aanvragen is een HTTP-bibliotheek met Apache2-licentie, geschreven in Python.

```cmd
pip install requests
```

Installeer de python-dotenv module. Deze module leest het sleutelwaardepaar uit .env-bestand en voegt deze toe aan omgevingsvariabele.

```cmd
pip install python-dotenv
```

Maak een virtuele omgeving

```cmd
mkvirtualenv advanced-python
```

`cd`naar de hoofdmap van het voorbeeldproject.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Sluit het voorbeeldproject aan op de omgeving. Hiermee wordt de nieuw gemaakte virtuele omgeving toegewezen aan de hoofdmap van het voorbeeldproject.

```cmd
setprojectdir .
```

Activeer de virtuele omgeving.

```cmd
activate
```

Het project moet nu actief zijn en `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` u ziet iets als in de opdrachtprompt.

Deactiveer de omgeving.

```cmd
deactivate
```

Het `(advanced-python)` voorvoegsel moet nu worden verdwenen als de omgeving is nu gedeactiveerd.

Als u de `workon advanced-python` omgeving opnieuw wilt activeren, wordt deze uitgevoerd vanuit de hoofdmap van het voorbeeldproject.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Start de meeslepende lezer met voorbeeldinhoud

Wanneer de omgeving actief is, voert `flask run` u het voorbeeldproject uit door in te voeren vanuit de hoofdmap van het voorbeeldproject.

```cmd
flask run
```

Open uw browser _http://localhost:5000_en navigeer naar .

## <a name="create-a-python-web-app-on-osx"></a>Een Python-web-app maken op OSX

Maak een Python-web-app met `flask` osx.

Installeer [Git](https://git-scm.com/).

Nadat Git is geïnstalleerd open Terminal en 'kloon' de Immersive Reader SDK Git repository naar een map op uw computer

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Python [Python](https://www.python.org/downloads/)installeren .

De map Python-root `Python37-32` moet zich bijvoorbeeld nu in de map Toepassingen bevinden.

Nadat Python-installatie is voltooid, opent u Terminal en `cd` de map Python Scripts.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Installeer pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Voer vervolgens het volgende uit om pip te installeren voor de momenteel aangemelde gebruiker om problemen met machtigingen te voorkomen.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Voer uw wachtwoord in wanneer u daarom wordt gevraagd.
- Voeg het pad van uw pip-installatie toe aan de VARIABELE PATH.
- Ga naar de onderkant van het bestand en voer het pad in dat u `PATH=$PATH:/usr/local/bin`wilt toevoegen als het laatste item van de lijst, bijvoorbeeld .
- Druk op control-x om te stoppen.
- Voer `Y` in om de gewijzigde buffer op te slaan.
- Dat is alles. Om het te testen, typt `echo $PATH`u in het nieuwe terminalvenster: .

Installeer Flask.

```bash
pip install flask --user
```

Installeer Jinja2. Een volledig uitgeruste sjabloonengine voor Python.

```bash
pip install Jinja2 --user
```

Installeer virtualenv. Een tool om geïsoleerde Python-omgevingen te maken.

```bash
pip install virtualenv --user
```

Installeer virtualenvwrapper. Het idee achter virtualenvwrapper is om het gebruik van virtualenv te vergemakkelijken.

```bash
pip install virtualenvwrapper --user
```

Installeer de module aanvragen. Aanvragen is een HTTP-bibliotheek met Apache2-licentie, geschreven in Python.

```bash
pip install requests --user
```

Installeer de python-dotenv module. Deze module leest het sleutelwaardepaar uit .env-bestand en voegt deze toe aan omgevingsvariabele.

```bash
pip install python-dotenv --user
```

Kies een map waar u uw virtuele omgevingen wilt behouden en voer deze opdracht uit

```bash
mkdir ~/.virtualenvs
```

`cd`naar de voorbeeldmap van Immersive Reader SDK Python.to the Immersive Reader SDK Python sample application folder.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Maak een virtuele omgeving

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Sluit het voorbeeldproject aan op de omgeving. Hiermee wordt de nieuw gemaakte virtuele omgeving toegewezen aan de hoofdmap van het voorbeeldproject.

```bash
setprojectdir .
```

Activeer de virtuele omgeving.

```bash
activate
```

Het project moet nu actief zijn en `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` u ziet iets als in de opdrachtprompt.

Deactiveer de omgeving.

```bash
deactivate
```

Het `(advanced-python)` voorvoegsel moet nu worden verdwenen als de omgeving is nu gedeactiveerd.

Als u de `workon advanced-python` omgeving opnieuw wilt activeren, wordt deze uitgevoerd vanuit de hoofdmap van het voorbeeldproject.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Start de meeslepende lezer met voorbeeldinhoud

Wanneer de omgeving actief is, voert `flask run` u het voorbeeldproject uit door in te voeren vanuit de hoofdmap van het voorbeeldproject.

```bash
flask run
```

Open uw browser _http://localhost:5000_en navigeer naar .

## <a name="next-steps"></a>Volgende stappen

* Ontdek de [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) en de [Immersive Reader SDK Reference](./reference.md)
* Codevoorbeelden weergeven op [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
