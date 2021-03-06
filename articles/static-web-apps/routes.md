---
title: Rotas no serviço Aplicativos Web Estáticos do Azure
description: Saiba mais sobre as regras de roteamento de back-end e como proteger rotas com funções.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 4a9639343827ebc5bb17a6d62d9b65d0b561e932
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595125"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Rotas na Versão Prévia do serviço Aplicativos Web Estáticos do Azure

O roteamento no serviço Aplicativos Web Estáticos do Azure define as regras de roteamento de back-end e o comportamento de autorização para conteúdo estático e APIs. As regras são definidas como uma matriz de regras no arquivo _routes.json_.

- O arquivo _routes.json_ deve existir na raiz da pasta de artefatos do build do aplicativo.
- As regras são executadas na ordem em que aparecem na matriz de `routes`.
- A avaliação da regra é interrompida na primeira correspondência. As regras de roteamento não são encadeadas.
- As funções são definidas no arquivo _routes.json_ e os usuários estão associados a funções por meio de [convites](authentication-authorization.md).
- Você tem controle total sobre o nome das funções.

O tópico de roteamento é bastante semelhante aos conceitos de autenticação e autorização. Certifique-se de ler o guia de [autenticação e autorização,](authentication-authorization.md) juntamente com este artigo.

## <a name="location"></a>Location

O arquivo _routes.json_ deve existir na raiz da pasta de artefatos do build do aplicativo. Se o seu aplicativo Web inclui uma etapa de compilação que copia os arquivos de compilação de uma pasta específica para a sua pasta de artefato de compilação, então o arquivo _routes.json_ precisa existir nessa pasta específica.

A tabela a seguir lista o local apropriado para colocar seu arquivo _routes.json_ para uma série de estruturas e bibliotecas JavaScript de front-end.

|Estrutura / biblioteca | Location  |
|---------|----------|
| Angular | _assets_   |
| React   | _público_  |
| Svelte  | _público_   |
| Vue     | _público_ |

## <a name="defining-routes"></a>Definir rotas

As rotas são definidas no arquivo _routes.json_ como uma matriz de regras de rota na propriedade `routes`. Cada regra é composta por um padrão de rota, juntamente com uma ou mais das propriedades de regra opcionais. Veja o [exemplo de arquivo de rota](#example-route-file) para obter exemplos de uso.

| Propriedade de regra  | Obrigatório | Valor padrão | Comentário                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Sim      | n/d          | O padrão de rota solicitado pelo chamador.<ul><li>Há suporte para [curingas](#wildcards) no final dos caminhos de rota. Por exemplo, a rota _admin/\*_ corresponde a qualquer rota sob o caminho de _administrador_.<li>O arquivo padrão de uma rota é _index. html_.</ul>|
| `serve`        | Não       | n/d          | Define o arquivo ou caminho retornado da solicitação. O caminho e o nome do arquivo podem ser diferentes do caminho solicitado. Se um valor de `serve` for definido, o caminho solicitado será usado. |
| `allowedRoles` | Não       | anônimo     | Uma matriz de nomes de função. <ul><li>Os caracteres válidos incluem `a-z`, `A-Z`, `0-9` e `_`.<li>A função interna `anonymous` se aplica a todos os usuários não autenticados.<li>A função interna `authenticated` se aplica a todos os usuários não conectados.<li>Os usuários devem pertencer a pelo menos uma função.<li>As funções são comparadas usando o operador _OR_. Se um usuário estiver em qualquer uma das funções listadas, o acesso será concedido.<li>Os usuários individuais são associados a funções por meio de [convites](authentication-authorization.md).</ul> |
| `statusCode`   | Não       | 200           | A resposta do [código de status HTTP](https://wikipedia.org/wiki/List_of_HTTP_status_codes) para a solicitação. |

## <a name="securing-routes-with-roles"></a>Proteger rotas com funções

As rotas são protegidas pela adição de um ou mais nomes de função na matriz de `allowedRoles` de uma regra. Veja o [exemplo de arquivo de rota](#example-route-file) para obter exemplos de uso.

Por padrão, cada usuário pertence à função `anonymous` interna e todos os usuários conectados são membros da função `authenticated`. Por exemplo, para restringir uma rota somente a usuários autenticados, adicione a função `authenticated` interna à matriz de `allowedRoles`.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Você pode criar novas funções conforme necessário na matriz de `allowedRoles`. Para restringir uma rota a apenas administradores, você pode definir uma função de `administrator` na matriz de `allowedRoles`.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Você tem controle total sobre os nomes de função; não há nenhuma lista mestra à qual suas funções devem aderir.
- Os usuários individuais são associados a funções por meio de [convites](authentication-authorization.md).

## <a name="wildcards"></a>Curingas

As regras de curinga correspondem a todas as solicitações em um determinado padrão de rota. Se você definir um valor de `serve` em sua regra, o arquivo ou caminho nomeado será entregue como a resposta.

Por exemplo, para implementar rotas para um aplicativo de calendário, você pode mapear todas as URLs que se enquadram na rota _calendário_ para atender a um único arquivo.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

O arquivo _calendar.html_ pode, então, usar o roteamento do lado do cliente para atender a uma exibição diferente para variações de URL, como `/calendar/january/1`, `/calendar/2020` e `/calendar/overview`.

Você também pode proteger rotas com caracteres curinga. No exemplo a seguir, qualquer arquivo solicitado no caminho _admin_ requer um usuário autenticado que seja membro da função _administrador_.

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> As solicitações de arquivos referenciados por um arquivo entregue são avaliadas apenas para verificações de autenticação. Por exemplo, as solicitações para um arquivo CSS em um caminho curinga servem para o arquivo CSS e não para o que é definido como o arquivo `serve`. O arquivo CSS é entregue contanto que o usuário atenda aos requisitos de autenticação necessários.

## <a name="fallback-routes"></a>Rotas de fallback

As estruturas ou bibliotecas JavaScript de front-end geralmente dependem do roteamento do lado do cliente para navegação do aplicativo Web. Essas regras de roteamento do lado do cliente atualizam o local da janela do navegador sem fazer solicitações de volta ao servidor. Se você atualizar a página ou navegar diretamente para os locais gerados pelas regras de roteamento do lado do cliente, uma rota de fallback do lado do servidor será necessária para atender à página HTML apropriada.

Uma rota de fallback comum é mostrada no seguinte exemplo:

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

A rota de fallback deve ser listada por último em suas regras de roteamento, pois ela captura todas as solicitações não detectadas pelas regras definidas anteriormente.

## <a name="redirects"></a>Redirecionamentos

Você pode usar os códigos de status HTTP [301](https://en.wikipedia.org/wiki/HTTP_301) e [302](https://en.wikipedia.org/wiki/HTTP_302) para redirecionar solicitações de uma rota para outra.

Por exemplo, a regra a seguir cria um redirecionamento 301 de _old-page.html_ para _new-page.html_.

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

Os redirecionamentos também funcionam com caminhos que não definem arquivos distintos.

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>Páginas de erro personalizadas

Os usuários podem encontrar várias situações diferentes que podem resultar em um erro. Usando a matriz de `platformErrorOverrides`, você pode fornecer uma experiência personalizada em resposta a esses erros. Consulte o [exemplo de arquivo de rota](#example-route-file) para o posicionamento da matriz no arquivo _rotas.json_.

A tabela a seguir lista as substituições de erro de plataforma disponíveis:

| Tipo de erro  | Código de status HTTP | Descrição |
|---------|---------|---------|
| `NotFound` | 404  | Uma página não foi encontrada no servidor. |
| `Unauthenticated` | 401 | O usuário não está conectado com um [provedor de autenticação](authentication-authorization.md). |
| `Unauthorized_InsufficientUserInformation` | 401 | A conta do usuário no provedor de autenticação não está configurada para expor os dados necessários. Esse erro pode ocorrer em situações como quando o aplicativo solicita ao provedor de autenticação o endereço de email do usuário, mas o usuário optou por restringir o acesso ao endereço de email. |
| `Unauthorized_InvalidInvitationLink` | 401 | Um convite expirou ou o usuário seguiu um link de convite gerado para outro destinatário.  |
| `Unauthorized_MissingRoles` | 401 | O usuário não é membro de uma função necessária. |
| `Unauthorized_TooManyUsers` | 401 | O site atingiu o número máximo de usuários e o servidor está limitando mais adições. Esse erro é exposto ao cliente porque não há limite para o número de [convites](authentication-authorization.md) que você pode gerar, e alguns usuários podem nunca aceitar o convite.|
| `Unauthorized_Unknown` | 401 | Há um problema desconhecido ao tentar autenticar o usuário. Uma causa desse erro pode ser que o usuário não é reconhecido porque não deu consentimento ao aplicativo.|

## <a name="example-route-file"></a>Exemplo de arquivo de rota

O exemplo a seguir mostra como criar regras de rota para conteúdo estático e APIs em um arquivo _routes.json_. Algumas rotas usam a pasta [ _.auth_ do sistema](authentication-authorization.md) que acessam pontos de extremidade relacionados à autenticação.

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "serve": "/login"
    }
  ]
}
```

Os exemplos a seguir descrevem o que acontece quando uma solicitação corresponde a uma regra.

|Solicitações para...  | O resultado é... |
|---------|---------|---------|
| _/profile_ | Os usuários autenticados recebem o arquivo _/profile/index.html_. Usuários não autenticados são redirecionados para _/login_. |
| _/admin/reports_ | Usuários autenticados na função de _administradores_ recebem o arquivo _/admin/reports/index.html_. Os usuários autenticados que não estão na função _administradores_ recebem um erro 401<sup>1</sup>. Usuários não autenticados são redirecionados para _/login_. |
| _/api/admin_ | As solicitações de usuários autenticados na função _administradores_ são enviadas para a API. Os usuários autenticados que não estão na função _administradores_ e usuários não autenticados recebem um erro 401. |
| _/customers/contoso_ | Usuários autenticados que pertencem às funções _administradores_ ou _clientes\_contoso_ recebem o arquivo _/customers/contoso/index.html_<sup>1</sup>. Os usuários autenticados que não estão na função _administradores_ ou _clientes\_contoso_ recebem um erro 401. Usuários não autenticados são redirecionados para _/login_. |
| _/login_     | Os usuários não autenticados são desafiados a autenticar com o GitHub. |
| _/.auth/login/twitter_     | A autorização com o Twitter está desabilitada. O servidor responde com um erro 404. |
| _/logout_     | Os usuários são desconectados de qualquer provedor de autenticação. |
| _/calendar/2020/01_ | O navegador recebe o arquivo _/calendar.html_. |
| _/specials_ | O navegador é redirecionado para _/deals_. |
| _/unknown-folder_     | O arquivo _/custom-404.html_ é entregue. |

<sup>1</sup> Você pode fornecer uma página de erro personalizada definindo uma regra de `Unauthorized_MissingRoles` na matriz de `platformErrorOverrides`.

## <a name="restrictions"></a>Restrições

- O arquivo _routes.json_ não pode ter mais de 100 KB
- O arquivo _routes.json_ oferece suporte a um máximo de 50 funções distintas

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar autenticação e autorização](authentication-authorization.md)
