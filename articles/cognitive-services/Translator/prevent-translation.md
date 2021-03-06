---
title: Impedir tradução de conteúdo-Tradutor
titleSuffix: Azure Cognitive Services
description: Impedir tradução de conteúdo com o tradutor. O tradutor permite que você marque o conteúdo para que ele não seja traduzido.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 37d2e4c0131569ab50ebf49ff73b6adf7a420713
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996169"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Como impedir a tradução de conteúdo com o tradutor

O tradutor permite que você marque o conteúdo para que ele não seja traduzido. Por exemplo, você talvez queira marcar o código, um nome de marca ou uma palavra/frase que não precisa ser traduzida.

## <a name="methods-for-preventing-translation"></a>Métodos para evitar a tradução

1. Marcar seu conteúdo com `notranslate`. É por design que isso funciona somente quando o TextType de entrada é definido como HTML

   Exemplo:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. use o [dicionário dinâmico](dynamic-dictionary.md) para prescrever uma tradução específica.

3. Não transmita a cadeia de caracteres para o tradutor para tradução.

4. Tradutor personalizado: Use um [dicionário no Tradutor personalizado](custom-translator/what-is-dictionary.md) para prescrever a tradução de uma frase com uma probabilidade de 100%.


## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Evite a tradução em sua chamada do Tradutor](reference/v3-0-translate.md)
