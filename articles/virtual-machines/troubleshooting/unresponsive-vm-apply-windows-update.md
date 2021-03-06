---
title: A VM do Azure não está respondendo com o erro C01A001D ao aplicar Windows Update
description: Este artigo fornece etapas para resolver problemas em que o Windows Update gera um erro e deixa de responder em uma VM do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 16c8eed3377c2191b4345ec59ec1eba8be01369d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633951"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>A VM não está respondendo com o erro "C01A001D" ao aplicar Windows Update

Este artigo fornece etapas para resolver problemas em que Windows Update (KB) gera um erro e deixa de responder em uma VM do Azure.

## <a name="symptoms"></a>Sintomas

Ao usar o [diagnóstico de inicialização](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para exibir a captura de tela da VM, a Windows Update (KB) em andamento é exibida, mas falha com o código de erro: ' C01A001D '.

![Windows Update sem resposta](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Causa

Um arquivo de núcleo não pode ser criado no sistema de arquivos. O sistema operacional não pode gravar arquivos no disco.

## <a name="resolution"></a>Resolução

### <a name="process-overview"></a>Visão geral do processo

1. [Crie e acesse uma VM de reparo](#create-and-access-a-repair-vm).
2. [Libere espaço no disco rígido](#free-up-space-on-the-hard-disk).
3. [Recomendado: antes de recriar a VM, habilite o console serial e a coleção de despejo de memória](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. [Reconstrua a VM](#rebuild-the-vm).

> [!NOTE]
> Quando esse erro ocorre, o SO convidado não está operacional. Você deve solucionar problemas no modo offline para resolver esse problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Siga [as etapas 1-3 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar uma VM de reparo.
2. Conecte-se à VM de reparo usando Conexão de Área de Trabalho Remota.

### <a name="free-up-space-on-the-hard-disk"></a>Liberar espaço no disco rígido

Se o disco ainda não for de 1 TB, você deverá redimensioná-lo. Depois que o disco for de 1 TB, execute uma limpeza de disco e uma desfragmentação da unidade.

1. Verifique se o disco está cheio. Se o disco estiver abaixo de 1 TB, [expanda-o para um máximo de 1 TB usando o PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
2. Depois que o disco for de 1 TB, execute uma limpeza de disco.
    - [Desanexe o disco de dados da VM quebrada](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk).
    - [Anexe o disco de dados a uma VM em funcionamento](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm).
    - Use a [ferramenta limpeza de disco](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) para liberar espaço.
3. Após o redimensionamento e a limpeza, desfragmente a unidade:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    Dependendo do nível de fragmentação, isso pode levar horas.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: antes de recriar a VM, habilite o console serial e a coleção de despejo de memória

1. Abra uma sessão de prompt de comando com privilégios elevados (executar como administrador).
2. Execute os comandos a seguir:

    Habilitar console serial:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Certifique-se de que o espaço livre no disco do sistema operacional seja pelo menos igual ao tamanho da RAM (memória da VM).

    Se não houver espaço suficiente no disco do sistema operacional, altere o local em que o arquivo de despejo de memória será criado e o informe a um disco de dados anexado à VM e com espaço livre suficiente. Para alterar o local, substitua `%SystemRoot%` pela letra da unidade (por exemplo, "F:") do disco de dados nos comandos abaixo:

    **Habilitar a configuração sugerida de despejo de so:**

    Carregar disco do so quebrado:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Habilitar em ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Habilitar em ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Descarregar disco do so quebrado:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Recriar a VM

Use [a etapa 5 dos comandos de reparo da VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para remontar a VM.
