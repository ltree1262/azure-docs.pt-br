---
title: 'Início Rápido: Conteúdo específico do domínio – REST, Python'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você pode usar modelos de domínio para identificar celebridades e pontos de referência em uma imagem usando a API da Pesquisa Visual Computacional com Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 545cb16f331c4960a4ff0618caf0a38ed678f199
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684144"
---
# <a name="quickstart-use-a-domain-model-using-the-rest-api-and-python-in-computer-vision"></a>Início Rápido: Usar um modelo de domínio usando a API REST e o Python na Pesquisa Visual Computacional

Neste início rápido, você usará um modelo de domínio para identificar pontos de referência ou, opcionalmente, celebridades em uma imagem armazenada remotamente usando a API REST da Pesquisa Visual Computacional. Com o método [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200), você pode aplicar um modelo específico do domínio para reconhecer o conteúdo dentro de uma imagem.

Você pode executar este início rápido passo a passo usando um Jupyter Notebook em [MyBinder](https://mybinder.org). Para inicializar o Associador, selecione o botão a seguir:

[![Associador](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/try/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Será necessário ter o [Python](https://www.python.org/downloads/) instalado se quiser executar o exemplo localmente.
- Você precisa ter uma chave de assinatura para a Pesquisa Visual Computacional. É possível obter uma chave de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar a Pesquisa Visual Computacional e obter sua chave. Em seguida, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e a cadeia de caracteres do ponto de extremidade de serviço, denominadas `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`, respectivamente.

## <a name="create-and-run-the-landmarks-sample"></a>Criar e executar o exemplo de pontos de referência

Para criar e executar o exemplo de ponto de referência, siga estas etapas:

1. Copie o código a seguir em um editor de texto.
1. Outra opção é substituir o valor de `image_url` pela URL de uma imagem diferente para a qual você deseja detectar pontos de referência.
1. Salve o código como um arquivo com uma extensão `.py`. Por exemplo, `get-landmarks.py`.
1. Abra una janela de prompt de comando.
1. No prompt, use o comando `python` para executar o exemplo. Por exemplo, `python get-landmarks.py`.

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

landmark_analyze_url = endpoint + "vision/v3.0/models/landmarks/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/f/f6/" + \
    "Bunker_Hill_Monument_2005.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'landmarks'}
data = {'url': image_url}
response = requests.post(
    landmark_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant landmark for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["landmarks"] is not []
print(analysis)
landmark_name = analysis["result"]["landmarks"][0]["name"].capitalize()

# Display the image and overlay it with the landmark name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(landmark_name, size="x-large", y=-0.1)
```

## <a name="examine-the-response-for-the-landmarks-sample"></a>Examine a resposta para o exemplo de pontos de referência

Uma resposta com êxito é retornada em JSON. A página da Web de exemplo analisa e exibe uma resposta bem-sucedida na janela do prompt de comando, semelhante ao exemplo a seguir:

```json
{
  "result": {
    "landmarks": [
      {
        "name": "Bunker Hill Monument",
        "confidence": 0.9768505096435547
      }
    ]
  },
  "requestId": "659a10cd-44bb-44db-9147-a295b853b2b8",
  "metadata": {
    "height": 1600,
    "width": 1200,
    "format": "Jpeg"
  }
}
```

## <a name="create-and-run-the-celebrities-sample"></a>Criar e executar o exemplo de celebridades

Para criar e executar o exemplo de ponto de referência, siga estas etapas:

1. Copie o código a seguir em um editor de texto.
1. Faça as alterações a seguir no código quando necessário:
    1. Substitua o valor de `subscription_key` pela sua chave de assinatura.
    1. Substitua o valor de `vision_base_url` pela URL do ponto de extremidade para o recurso de Pesquisa Visual Computacional da região do Azure em que você adquiriu suas chaves de assinatura se necessário.
    1. Outra opção é substituir o valor de `image_url` pela URL de uma imagem diferente na qual você deseja detectar celebridades.
1. Salve o código como um arquivo com uma extensão `.py`. Por exemplo, `get-celebrities.py`.
1. Abra una janela de prompt de comando.
1. No prompt, use o comando `python` para executar o exemplo. Por exemplo, `python get-celebrities.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/"

celebrity_analyze_url = vision_base_url + "models/celebrities/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/d9/" + \
    "Bill_gates_portrait.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'celebrities'}
data = {'url': image_url}
response = requests.post(
    celebrity_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant celebrity for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["celebrities"] is not []
print(analysis)
celebrity_name = analysis["result"]["celebrities"][0]["name"].capitalize()

# Display the image and overlay it with the celebrity name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(celebrity_name, size="x-large", y=-0.1)
```

## <a name="examine-the-response-for-the-celebrities-sample"></a>Examine a resposta para o exemplo de celebridades

Uma resposta com êxito é retornada em JSON. A página da Web de exemplo analisa e exibe uma resposta bem-sucedida na janela do prompt de comando, semelhante ao exemplo a seguir:


```json
{
  "result": {
    "celebrities": [
      {
        "faceRectangle": {
          "top": 123,
          "left": 156,
          "width": 187,
          "height": 187
        },
        "name": "Bill Gates",
        "confidence": 0.9993845224380493
      }
    ]
  },
  "requestId": "f14ec1d0-62d4-4296-9ceb-6b5776dc2020",
  "metadata": {
    "height": 521,
    "width": 550,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua os arquivos para ambos os exemplos.

## <a name="next-steps"></a>Próximas etapas

Em seguida, explore um aplicativo Python que usa a Pesquisa Visual Computacional para executar OCR (reconhecimento óptico de caracteres), crie miniaturas com recorte inteligente e detecte, categorize, marque e descreva recursos visuais em imagens.

> [!div class="nextstepaction"]
> [Tutorial do Python da API da Pesquisa Visual Computacional](../Tutorials/PythonTutorial.md)

* Para testar rapidamente a API da Pesquisa Visual Computacional, experimente o [Abrir o console de teste de API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
