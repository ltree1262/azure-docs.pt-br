---
title: Usar um feed NuGet personalizado
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Use um feed NuGet personalizado para acessar e usar os pacotes NuGet em um Azure Dev Space.
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "74325735"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Usar um feed do NuGet personalizado com o Azure Dev Spaces

Um feed NuGet fornece uma maneira conveniente de incluir fontes de pacotes em um projeto. Azure Dev Spaces precisa acessar esse feed para que as dependências sejam instaladas corretamente no contêiner do Docker.

## <a name="set-up-a-nuget-feed"></a>Configurar um feed NuGet

Adicione uma [referência de pacote](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) para sua dependência no `*.csproj` arquivo sob o `PackageReference` nó. Por exemplo:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Crie um arquivo [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) na pasta do projeto e defina as `packageSources` seções `packageSourceCredentials` e para o feed do NuGet. A `packageSources` seção contém a URL do feed, que deve ser acessível do seu cluster AKs. O `packageSourceCredentials` são as credenciais para acessar o feed. Por exemplo:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Atualize seu Dockerfiles para copiar o `NuGet.Config` arquivo para a imagem. Por exemplo:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> No Windows, `NuGet.Config`, `Nuget.Config`e `nuget.config` todos funcionam como nomes de arquivo válidos. No Linux, apenas `NuGet.Config` um nome de arquivo válido para esse arquivo. Como o Azure Dev Spaces usa o Docker e o Linux, esse arquivo `NuGet.Config`deve ser nomeado. Você pode corrigir a nomenclatura manualmente ou executando `dotnet restore --configfile nuget.config`.


Se você estiver usando o Git, não deverá ter as credenciais para o feed do NuGet no controle de versão. Adicione `NuGet.Config` ao `.gitignore` para o seu projeto para que o `NuGet.Config` arquivo não seja adicionado ao controle de versão. Azure Dev Spaces precisará desse arquivo durante o processo de criação da imagem de contêiner, mas, por padrão, ele respeita `.gitignore` as `.dockerignore` regras definidas no e durante a sincronização. Para alterar o padrão e permitir que Azure Dev Spaces Sincronize o `NuGet.Config` arquivo, atualize o `azds.yaml` arquivo:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Se você não estiver usando o Git, poderá ignorar esta etapa.

Na próxima vez que você `azds up` executar ou `F5` acessar o Visual Studio Code ou o Visual Studio, Azure dev Spaces sincronizará o arquivo e o usará para instalar as dependências do `NuGet.Config` pacote.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o NuGet e como ele funciona](https://docs.microsoft.com/nuget/what-is-nuget).