4 种用于构建嵌入式 Linux 系统的工具
======

> 了解 Yocto、Buildroot、 OpenWRT，和改造过的桌面发行版以确定哪种方式最适合你的项目。

![](https://opensource.com/sites/default/files/styles/image-full-size/public/lead-images/desk_clock_job_work.jpg?itok=Nj4fuhl6)


Linux 被部署到比 Linus Torvalds 在他的宿舍里开发时所预期的更广泛的设备。令人震惊的支持了各种芯片，使得Linux 可以应用于大大小小的设备上：从 [IBM 的巨型机][1]到不如其连接的端口大的[微型设备][2]，以及各种大小的设备。它被用于大型企业数据中心、互联网基础设施设备和个人的开发系统。它还为消费类电子产品、移动电话和许多物联网设备提供了动力。

在为桌面和企业级设备构建 Linux 软件时，开发者通常在他们的构建机器上使用桌面发行版，如 [Ubuntu][3] 以便尽可能与被部署的机器相似。如 [VirtualBox][4] 和 [Docker][5] 这样的工具使得开发、测试和生产环境更好的保持了一致。

### 什么是嵌入式系统？

维基百科将[嵌入式系统][6]定义为：“在更大的机械或电气系统中具有专用功能的计算机系统，往往伴随着实时计算限制。”

我觉得可以很简单地说，嵌入式系统是大多数人不认为是计算机的计算机。它的主要作用是作为某种设备，而不被视为通用计算平台。

嵌入式系统编程的开发环境通常与测试和生产环境大不相同。它们可能会使用不同的芯片架构、软件堆栈甚至操作系统。开发工作流程对于嵌入式开发人员与桌面和 Web 开发人员来说是非常不同的。通常，其构建后的输出将包含目标设备的整个软件映像，包括内核、设备驱动程序、库和应用程序软件（有时也包括引导加载程序）。

在本文中，我将对构建嵌入式 Linux 系统的四种常用方式进行纵览。我将介绍一下每种产品的工作原理，并提供足够的信息来帮助读者确定使用哪种工具进行设计。我不会教你如何使用它们中的任何一个；一旦缩小了选择范围，就有大量深入的在线学习资源。没有任何选择适用于所有情况，我希望提供足够的细节来指导您的决定。

### Yocto

[Yocto][7] 项目 [定义][8]为“一个开源协作项目，提供模板、工具和方法，帮助您为嵌入式产品创建定制的基于 Linux 的系统，而不管硬件架构如何。”它是用于创建定制的 Linux 运行时映像的配方、配置值和依赖关系的集合，可根据您的特定需求进行定制。

完全公开：我在嵌入式 Linux 中的大部分工作都集中在 Yocto 项目上，而且我对这个系统的认识和偏见可能很明显。

Yocto 使用 [Openembedded][9] 作为其构建系统。从技术上讲，这两个是独立的项目；然而，在实践中，用户不需要了解区别，项目名称经常可以互换使用。

Yocto 项目的输出大致由三部分组成：

  * **目标运行时二进制文件：**这些包括引导加载程序、内核、内核模块、根文件系统映像。以及将 Linux 部署到目标平台所需的任何其他辅助文件。
  * **包流：**这是可以安装在目标上的软件包集合。您可以根据需要选择软件包格式（例如，deb、rpm、ipk）。其中一些可能预先安装在目标运行时二进制文件中，但可以构建用于安装到已部署系统的软件包。
  * **目标 SDK：**这些是安装在目标平台上的软件的库和头文件的集合。应用程序开发人员在构建代码时使用它们，以确保它们与适当的库链接

#### 优点

Yocto 项目在行业中得到广泛应用，并得到许多有影响力的公司的支持。此外，它还拥有一个庞大且充满活力的开发人员[社区][10]和[生态系统][11]。开源爱好者和企业赞助商的结合的方式有助于推动 Yocto 项目。

获得 Yocto 的支持有很多选择。如果您想自己动手，有书籍和其他培训材料。如果您想获得专业知识，有许多有 Yocto 经验的工程师。而且许多商业组织可以为您的设计提供基于 Yocto 的 Turnkey 产品或基于服务的实施和定制。

Yocto 项目很容易通过 [层][12] 进行扩展，层可以独立发布以添加额外的功能，或针对项目发布时尚不可用的平台，或用于保存系统特有定制功能。层可以添加到你的配置中，以添加未特别包含在市面上版本中的独特功能；例如，“[meta-browser] [13]” 层包含 Web 浏览器的清单，可以轻松为您的系统进行构建。因为它们是独立维护的，所以层可以按不同的时间发布（根据层的开发速度），而不是跟着标准的 Yocto 版本发布。

Yocto 可以说是本文讨论的任何方式中最广泛的设备支持。由于许多半导体和电路板制造商的支持，Yocto 很可能能够支持您选择的任何目标平台。主版本 Yocto [分支][14]仅支持少数几块主板（以便达成合理的测试和发布周期），但是，标准工作模式是使用外部主板支持层。

最后，Yocto 非常灵活和可定制。您的特定应用程序的自定义可以存储在一个层进行封装和隔离，通常将要素层特有的自定义项存储为层本身的一部分，这可以将相同的设置同时应用于多个系统配置。Yocto 还提供了一个定义良好的层优先和覆盖功能。这使您可以定义层应用和搜索元数据的顺序。它还使您可以覆盖具有更高优先级的层的设置；例如，现有清单的许多自定义功能都将保留。

#### 缺点

Yocto 项目最大的缺点是学习曲线陡峭。学习该系统并真正理解系统需要花费大量的时间和精力。 根据您的需求，这可能对您的应用程序不重要的技术和能力投入太大。 在这种情况下，与一家商业供应商合作可能是一个不错的选择。

Yocto 项目的开发时间和资源相当高。 需要构建的包（包括工具链，内核和所有目标运行时组件）的数量相当不少。 Yocto 开发人员的开发工作站往往是大型系统。 不建议使用小型笔记本电脑。 这可以通过使用许多提供商提供的基于云的构建服务器来缓解。 另外，Yocto 有一个内置的缓存机制，当它确定用于构建特定包的参数没有改变时，它允许它重新使用先前构建的组件。

#### 建议

为您的下一个嵌入式 Linux 设计使用 Yocto 项目是一个强有力的选择。 在这里介绍的选项中，无论您的目标用例如何，它都是最广泛适用的。 广泛的行业支持，积极的社区和广泛的平台支持使其成为必须设计师的不错选择。

### Buildroot

[Buildroot][15] 项目定义为“通过交叉编译生成嵌入式 Linux 系统的简单、高效且易于使用的工具。”它与 Yocto 项目具有许多相同的目标，但它注重简单性和简约性。一般来说，Buildroot 会禁用所有软件包的所有可选编译时设置（有一些值得注意的例外），从而生成尽可能小的系统。系统设计人员需要启用适用于给定设备的设置。

Buildroot 从源代码构建所有组件，但不支持按目标包管理。因此，它有时称为固件生成器，因为镜像在构建时大部分是固定的。应用程序可以更新目标文件系统，但是没有机制将新软件包安装到正在运行的系统中。

Buildroot 输出主要由三部分组成：

  * 将 Linux 部署到目标平台所需的根文件系统映像和任何其他辅助文件
  * 适用于目标硬件的内核，引导加载程序和内核模块
  * 用于构建所有目标二进制文件的工具链。

#### 优点

Buildroot 对简单性的关注意味着，一般来说，它比 Yocto 更容易学习。核心构建系统用 Make 编写，并且足够短以便开发人员了解整个系统，同时可扩展到足以满足嵌入式 Linux 开发人员的需求。 Buildroot 核心通常只处理常见用例，但它可以通过脚本进行扩展。

Buildroot 系统使用普通的 Makefile 和 Kconfig 语言来进行配置。 Kconfig 由 Linux 内核社区开发，广泛用于开源项目，使得许多开发人员都熟悉它。

由于禁用所有可选的构建时设置的设计目标，Buildroot 通常会使用开箱即用的配置生成尽可能最小的镜像。一般来说，构建时间和构建主机资源的规模将比 Yocto 项目的规模更小。

#### 缺点

关注简单性和最小化启用的构建方式意味着您可能需要执行大量的自定义来为应用程序配置 Buildroot 构建。此外，所有配置选项都存储在单个文件中，这意味着如果您有多个硬件平台，则需要为每个平台进行每个定制更改。

对系统配置文件的任何更改都需要全部重新构建所有软件包。与 Yocto 相比，这个问题通过最小的镜像大小和构建时间得到了一定的解决，但在你调整配置时可能会导致构建时间过长。

中间软件包状态缓存默认情况下未启用，并且不像 Yocto 实施那么彻底。这意味着，虽然第一次构建可能比等效的 Yocto 构建短，但后续构建可能需要重建许多组件。

#### 建议

对于大多数应用程序，使用 Buildroot 进行下一个嵌入式 Linux 设计是一个不错的选择。如果您的设计需要多种硬件类型或其他差异，但由于同步多个配置的复杂性，您可能需要重新考虑，但对于由单一设置组成的系统，Buildroot 可能适合您。

### OpenWRT/LEDE

[OpenWRT][16] 项目开始为消费类路由器开发定制固件。您当地零售商提供的许多低成本路由器都可以运行 Linux 系统，但可能无法开箱即用。这些路由器的制造商可能无法提供频繁的更新来解决新的威胁，即使他们这样做，安装更新镜像的机制也很困难且容易出错。 OpenWRT 项目为许多已被其制造商放弃的设备生成更新的固件镜像，让这些设备焕发新生。

OpenWRT 项目的主要交付物是可用于大量商业设备的二进制镜像。它有网络可访问的软件包存储库，允许设备最终用户将新软件添加到他们的系统中。 OpenWRT 构建系统是一个通用构建系统，它允许开发人员创建自定义版本以满足他们自己的需求并添加新软件包，但其主要重点是目标二进制文件。

#### 优点

如果您正在为商业设备寻找替代固件，则 OpenWRT 应位于您的选项列表中。它的维护良好，可以保护您免受制造商固件无法解决的问题。您也可以添加额外的功能，使您的设备更有用。

如果您的嵌入式设计专注于网络，则 OpenWRT 是一个不错的选择。网络应用程序是 OpenWRT 的主要用例，您可能会发现许多可用的软件包。

#### 缺点

OpenWRT 对您的设计限制很多（与 Yocto 和 Buildroot 相比）。如果这些决定不符合您的设计目标，则可能需要进行大量的修改。

在部署的设备中允许基于软件包的更新是很难管理的。按照其定义，这会导致与您的 QA 团队测试的软件负载不同。此外，很难保证大多数软件包管理器的原子安装，以及错误的电源循环可能会使您的设备处于不可预知的状态。

#### 建议

OpenWRT 是爱好者项目或商用硬件再利用的不错选择。它也是网络应用程序的不错选择。如果您需要从默认设置进行大量定制，您可能更喜欢 Buildroot 或 Yocto。

### 桌面发行版

设计嵌入式 Linux 系统的一种常见方法是从桌面发行版开始，例如 [Debian][17] 或 [Red Hat][18]，并删除不需要的组件，直到安装的镜像符合目标设备的占用空间。这是 [Raspberry Pi][20] 平台流行的 [Raspbian][19]发行版的方法。

#### 优点

这种方法的主要优点是熟悉。通常，嵌入式 Linux 开发人员也是桌面 Linux 用户，并且精通他们的选择发行版。在目标上使用类似的环境可能会让开发人员更快地入门。根据所选的分布，可以使用 apt 和 yum 等标准封装工具安装许多其他工具。

可以将显示器和键盘连接到目标设备，并直接在那里进行所有的开发。对于不熟悉嵌入式空间的开发人员来说，这可能是一个更为熟悉的环境，无需配置和使用棘手的跨开发平台设置。

大多数桌面发行版可用的软件包数量通常大于前面讨论的嵌入式特定的构建器可用软件包数量。由于较大的用户群和更广泛的用例，您可能能够找到您的应用程序所需的所有运行时包，这些包已经构建并可供使用。

#### 缺点

将目标平台作为您的主要开发环境可能会很慢。运行编译器工具是一项资源密集型操作，根据您构建的代码的多少，这可能会严重妨碍您的性能。

除了一些例外情况，桌面发行版的设计并不适合低资源系统，并且可能难以充分裁剪目标映像。同样，桌面环境中的预设工作流程对于大多数嵌入式设计来说都不理想。以这种方式获得可再现的环境很困难。手动添加和删除软件包很容易出错。这可以使用特定于发行版的工具进行脚本化，例如基于 Debian 系统的 [debootstrap][21]。为了进一步提高[可再现性][21]，您可以使用配置管理工具，如 [CFEngine][22]（我的雇主 [Mender.io][23] 完整披露了
这一工具）。但是，您仍然受发行版提供商的支配，他们将更新软件包以满足他们的需求，而不是您的需求。

#### 建议

对于您打算推向市场的产品，请谨慎使用此方法。这对于爱好者应用程序来说是一个很好的模型；但是，对于需要支持的产品，这种方法很可能会遇到麻烦。虽然您可能能够获得更快的起步，但从长远来看，您可能会花费您的时间和精力。

### 其他考虑

这个讨论集中在构建系统的功能上，但通常有非功能性需求可能会影响您的决定。如果您已经选择了片上系统（SoC）或电路板，则您的选择很可能由供应商决定。如果您的供应商为特定系统提供板级支持包（BSP），使用它通常会节省相当多的时间，但请研究 BSP 的质量以避免在开发周期后期发生问题。

如果您的预算允许，您可能需要考虑为目标操作系统使用商业供应商。有些公司会为这里讨论的许多选项提供经过验证和支持的配置，除非您拥有嵌入式 Linux 构建系统方面的专业知识，否则这是一个不错的选择，可以让您专注于核心能力。

作为替代，您可以考虑为您的开发人员进行商业培训。这可能比商业操作系统供应商便宜，并且可以让你更加自给自足。这是快速找到您选择的构建系统基础知识的学习曲线。

最后，您可能已经有一些开发人员拥有一个或多个系统的经验。如果你的工程师有倾向性，当你做出决定时，肯定值得考虑。

### 总结

构建嵌入式 Linux 系统有多种选择，每种都有优点和缺点。将这部分设计放在优先位置至关重要，因为在以后的过程中切换系统的成本非常高。除了这些选择之外，还有新的系统在开发中。希望这次讨论能够为评估新的系统（以及这里提到的系统）提供一些背景，并帮助您为下一个项目做出坚实的决定。

--------------------------------------------------------------------------------

via: https://opensource.com/article/18/6/embedded-linux-build-tools

作者：[Drew Moseley][a]
选题：[lujun9972](https://github.com/lujun9972)
译者：[LHRChina](https://github.com/LHRChina)
校对：[wxy](https://github.com/wxy)

本文由 [LCTT](https://github.com/LCTT/TranslateProject) 原创编译，[Linux中国](https://linux.cn/) 荣誉推出

[a]:https://opensource.com/users/drewmoseley
[1]:https://en.wikipedia.org/wiki/Linux_on_z_Systems
[2]:http://www.picotux.com/
[3]:https://www.ubuntu.com/
[4]:https://www.virtualbox.org/
[5]:https://www.docker.com/
[6]:https://en.wikipedia.org/wiki/Embedded_system
[7]:https://yoctoproject.org/
[8]:https://www.yoctoproject.org/about/
[9]:https://www.openembedded.org/
[10]:https://www.yoctoproject.org/community/
[11]:https://www.yoctoproject.org/ecosystem/participants/
[12]:https://layers.openembedded.org/layerindex/branch/master/layers/
[13]:https://layers.openembedded.org/layerindex/branch/master/layer/meta-browser/
[14]:https://yoctoproject.org/downloads
[15]:https://buildroot.org/
[16]:https://openwrt.org/
[17]:https://www.debian.org/
[18]:https://www.redhat.com/
[19]:https://www.raspbian.org/
[20]:https://www.raspberrypi.org/
[21]:https://wiki.debian.org/Debootstrap
[22]:https://cfengine.com/
[23]:http://Mender.io