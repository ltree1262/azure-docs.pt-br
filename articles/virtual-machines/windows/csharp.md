---
title: 'Criar e gerenciar uma máquina virtual do Azure usando C #'
description: Use o C# e o Azure Resource Manager para implantar uma máquina virtual e todos os seus recursos de suporte.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 07c66b2955f3df1ffae1a0cb0c2b0888bdc790e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82082876"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Criar e gerenciar VMs Windows no Azure usando C# #

Uma [VM (Máquina Virtual) do Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) precisa de vários recursos do Azure suporte. Este artigo aborda a criação, o gerenciamento e a exclusão de recursos de VM utilizando C#. Você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto do Visual Studio
> * Instalar o pacote
> * Criar credenciais
> * Criar recursos
> * Executar tarefas de gerenciamento
> * Excluir recursos
> * Executar o aplicativo

São necessários cerca de 20 minutos para a conclusão destas etapas.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Se você ainda não fez isso, instale o [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selecione **Desenvolvimento para desktop com o .NET** na página Cargas de trabalho e, em seguida, clique em **Instalar**. No resumo, é possível verificar que as **ferramentas de desenvolvimento do .NET Framework 4 - 4.6** são automaticamente selecionadas. Se o Visual Studio já estiver instalado, você poderá adicionar a carga de trabalho .NET utilizando o Iniciador do Visual Studio.
2. No Visual Studio, clique em **arquivo** > **novo** > **projeto**.
3. Em **modelos** > **Visual C#**, selecione **aplicativo de console (.NET Framework)**, digite *myDotnetProject* para o nome do projeto, selecione o local do projeto e clique em **OK**.

## <a name="install-the-package"></a>Instalar o pacote

Os pacotes NuGet são a maneira mais fácil de instalar as bibliotecas de que você precisa para concluir estas etapas. Para obter as bibliotecas que você precisa no Visual Studio, siga estas etapas:

1. Clique em **ferramentas** > **Gerenciador de pacotes NuGet**e em **console do Gerenciador de pacotes**.
2. Digite este comando no console:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Criar credenciais

Antes de começar essa etapa, verifique se você tem acesso a uma [entidade de serviço do Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Você também deve registrar a ID do aplicativo, a chave de autenticação e a ID de locatário que precisará em uma etapa posterior.

### <a name="create-the-authorization-file"></a>Criar o arquivo de autorização

1. Em Gerenciador de soluções, clique com o botão direito do mouse em *myDotnetProject* > **Adicionar** > **novo item**e, em seguida, selecione **arquivo de texto** em *itens do Visual C#*. Nomeie o arquivo *azureauth.properties* e, em seguida, clique em **Adicionar**.
2. Adicione estas propriedades de autorização:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Substitua ** &lt;Subscription-ID&gt; ** pelo seu identificador de assinatura, ** &lt;ID&gt; do aplicativo** com o identificador do aplicativo Active Directory, ** &lt;a&gt; chave de autenticação** com a chave do aplicativo e ** &lt;a&gt; ID do locatário** pelo identificador do locatário.

3. Salve o arquivo azureauth.properties. 
4. Defina uma variável de ambiente no Windows chamada AZURE_AUTH_LOCATION com o caminho completo para o arquivo de autorização que você criou. Por exemplo, o seguinte comando do PowerShell pode ser usado:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Criar o cliente de gerenciamento

1. Abra o arquivo Program.cs para o projeto que você criou. Em seguida, adicione essas instruções using às instruções existentes na parte superior do arquivo:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Para criar o cliente de gerenciamento, adicione este código ao método Main:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-resources"></a>Criar recursos

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Todos os recursos devem estar contidos em um [grupo de recursos](../../azure-resource-manager/management/overview.md).

Para especificar valores para o aplicativo e criar o grupo de recursos, adicione este código ao método Main:

```csharp
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Criar o conjunto de disponibilidade

Os [conjuntos de disponibilidade](tutorial-availability-sets.md) facilitam a manutenção das máquinas virtuais usadas por seu aplicativo.

Para criar o conjunto de disponibilidade, adicione este código ao método Main:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Criar um endereço IP público

Um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) é necessário para se comunicar com a máquina virtual.

Para criar o endereço IP público para a máquina virtual, adicione este código ao método Main:
   
```csharp
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Uma máquina virtual precisa estar em uma sub-rede de uma [Rede virtual](../../virtual-network/virtual-networks-overview.md).

Para criar uma sub-rede e uma rede virtual, adicione este código ao método Main:

```csharp
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Criar a interface de rede

Uma máquina virtual precisa de uma interface de rede para se comunicar na rede virtual.

Para criar um adaptador de rede, adicione este código ao método Main:

```csharp
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Agora que você criou todos os recursos de suporte, você pode criar uma máquina virtual.

Para criar a máquina virtual, adicione este código ao método Main:

```csharp
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> Este tutorial cria uma máquina virtual executando uma versão do sistema operacional do Windows Server. Para saber mais sobre a seleção de outras imagens, veja [Navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Se você quiser usar um disco existente em vez de uma imagem do marketplace, use este código:

```csharp
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Executar tarefas de gerenciamento

Durante o ciclo de vida de uma máquina virtual, é possível que você queira executar tarefas de gerenciamento, como inicialização, interrupção ou exclusão de uma máquina virtual. Além disso, é possível que você queira criar um código para automatizar tarefas complexas ou repetitivas.

Quando você precisa fazer alguma coisa com a VM, você precisa obter uma instância dela:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Obter informações sobre a VM

Para obter informações sobre a máquina virtual, adicione este código ao método Main:

```csharp
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>Pare a VM.

Você pode parar uma máquina virtual e manter todas as suas configurações, mas continuará a ser cobrado por ela, ou pode parar uma máquina virtual e desalocá-la. Quando uma máquina virtual é desalocada, todos os recursos associados a ela também são desalocadas e a cobrança para eles termina.

Para interromper a máquina virtual sem desalocá-la, adicione este código ao método Main:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Se você desejar desalocar a máquina virtual, altere a chamada PowerOff para este código:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>Iniciar a VM

Para iniciar a máquina virtual, adicione este código ao método Main:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Redimensionar a VM

Muitos aspectos da implantação devem ser considerados ao decidir sobre um tamanho para sua máquina virtual. Para obter mais informações, consulte [Tamanhos de VM](sizes.md).  

Para alterar o tamanho da máquina virtual, adicione este código ao método Main:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adicionar um disco de dados à VM

Para adicionar um disco de dados à máquina virtual, adicione este código ao método Main. Este exemplo adiciona um disco de dados que tem 2 GB de tamanho, Han a LUN 0 e um tipo de cache de ReadWrite:

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Excluir recursos

Como você é cobrado pelos recursos utilizados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Se você quiser excluir as máquinas virtuais e todos os recursos de suporte, tudo o que precisa fazer é excluir o grupo de recursos.

Para excluir o grupo de recursos, adicione este código ao método Main:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Executar o aplicativo

Devem ser necessários cerca de cinco minutos para o aplicativo de console executar completamente do início ao fim. 

1. Para executar o aplicativo de console, clique em **Iniciar**.

2. Antes de pressionar **Enter** para iniciar a exclusão de recursos, reserve alguns minutos para verificar a criação dos recursos no portal do Azure. Clique no status de implantação para ver informações sobre a implantação.

## <a name="next-steps"></a>Próximas etapas
* Aproveite o uso de um modelo para criar uma máquina virtual usando as informações em [Implantar uma Máquina Virtual do Azure usando C# e um modelo do Resource Manager](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Saiba mais sobre o uso das [Bibliotecas do Azure para .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).
