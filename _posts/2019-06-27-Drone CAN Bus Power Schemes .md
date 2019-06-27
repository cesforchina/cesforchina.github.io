---
title: "无人机CAN总线电源方案"
tags:
- CAN
- 电源
keywords: pages, authoring, exclusion, frontmatter
sidebar: mydoc_sidebar
permalink: mydoc_pages.html
summary: "This is some summary frontmatter for my sample post."
---
工作正在进行中！

在本文中，我将使用CAN总线作为通信骨干来评估无人机的电源。

我考虑的系统是带有Pixhawk型飞行控制器的无人机，它使用CAN总线与外围设备（CAN节点）进行通信，如GPS，磁力计，ESC等。然而，考虑因素应更广泛地适用。此外，我当然只从业余爱好者的角度来看情况，并特别考虑到[UAVCAN for Hobbyists](http://www.olliw.eu/2019/drone-can-bus-power-schemes/2017/uavcan-for-hobbyists/)（UC4H）项目。冗余，备用电源，12 V CAN总线等疯狂的东西在这里不相关。一个典型的无人机将有一个电池，一个5.3 V电源砖和一个飞行控制器。

在这样的系统上使用许多CAN节点的问题之一是它们的适当供电。嗯，从某种意义上讲，它实际上不是一个问题，因为会涉及任何火箭科学。这是一个问题（仅限）因为基本上所有当前可用的带CAN端口的飞行控制器，如Pixhawk 1，Pixhawk 2 / Cube，Pixracer，AUAV X2.1等等，都不适合这项任务，即他们的任务CAN总线电源选项设计不合理。

基本问题是这些飞行控制器上的CAN-5V可以从主要的5 V电源（电源砖）获得，但另外是电流限制。此外，该电流限制包括所有其他外设，也就是那些不在CAN总线上的外设（除了可能在Serial1上的外设）。

那么，让我们看一下第1章中的一些飞行控制器，在第2章中得出一些结论，并在第3章讨论电源方案。
# 1. Pixhawk型飞行控制器电源方案

## Pixhawk 1

电源方案由LTC4417电源开关组成，可选择各种输入（电源砖，伺服，USB插头），并将其分配给各种用户（5V-HIPOWER，5V-PERIPH）。CAN总线由5V-PERIPH电源供电。但是，这个轨道通过[BQ24315](https://www.ti.com/lit/ds/symlink/bq24315.pdf)保护IC，其电流限制设置为1A。这个5V-PERIPH轨道也为基本上所有其他端口的外设供电，但Serial1端口除外，这些端口由5V-HIPOWER供电。
![Screenshot](http://www.olliw.eu/uploads/canbuspowerschemes-pixhawk1-03b.png)

来源：[github.com/PX4/Hardware](github.com/PX4/Hardware)

## Pixhawk 2,Cube

除了电子设备分布在不同的PCB之外，电源方案基本上与Pixhawk 1的电源方案相同。关键部件，即[LTC4417](https://www.analog.com/media/en/technical-documentation/data-sheets/4417f.pdf)电源开关和[BQ24315](https://www.ti.com/lit/ds/symlink/bq24315.pdf)保护IC，位于PSM上。对于Pixhawk 1，CAN总线由5V-PERIPH电源供电，其电流限制为1 A，除了Serial1上的电路外，它还基本上为所有其他外设供电。

在我所知的所有基于立方体的飞行控制系统中都可以找到相同的PSM模块或方案，这意味着下面将进一步总结的所有内容也适用于它们。  
![Screenshot](http://www.olliw.eu/uploads/canbuspowerschemes-pixhawk2-cube-03.png)

[github.com/PX4/Hardware/tree/master/PSMv3_REV_C](github.com/PX4/Hardware/tree/master/PSMv3_REV_C)  
[github.com/PX4/Hardware/tree/master/FMUv3_REV_D](github.com/PX4/Hardware/tree/master/FMUv3_REV_D)  
[github.com/3drobotics/Pixhawk_OS_Hardware](github.com/3drobotics/Pixhawk_OS_Hardware)  
[github.com/proficnc/The-Cube](github.com/proficnc/The-Cube)

## Pixracer

Pixracer拥有最原始的电源方案。它由一个二极管OR-ing作为电源开关和可重置保险丝组成。CAN总线连接到5V-PERIPH导轨，该导轨由1 A保险丝保护，非常类似于Pixhawk 1和Cube。5V-PERIPH导轨为所有其他外设供电，包括Serial1上的外设。

后者是有问题的，因为通常遥测连接到Serial1，它可以消耗大量的电力。因此，这里的1A电流限制还包括耗电量遥测。

二极管OR-ing也是有问题的。它将电压降低了几个0.1 V，这很容易使5V-PERIPH电压低于[TJA1051T](https://www.nxp.com/docs/en/data-sheet/TJA1051.pdf) CAN收发器的欠压切断电平。这可能发生，特别是系统仅连接到USB插头。当发生欠压切断时，TJA1051与CANL和CANH断开连接，而Pixracer再也无法与CAN总线通信。

我想最后用我的Pixracer添加它，这肯定是来自亚洲的一个来源，从CAN总线中抽取1 A是不可思议的。我不知道原因，也许它有一个较弱的保险丝左右。因此，请注意，您的Pixracer可能无法达到这个1 A的限制。
![Screenshot](http://www.olliw.eu/uploads/canbuspowerschemes-pixracer-03.png)

来源：[github.com/ArduPilot/Schematics/tree/master/mRobotics](github.com/ArduPilot/Schematics/tree/master/mRobotics)

## AUAV X2.1

AUAV X2.1的电源方案更好（遗憾的是它没有过压保护）。输入电源开关通过使用[LTC4415](https://www.analog.com/media/en/technical-documentation/data-sheets/4415fa.pdf)理想二极管的二极管OR-ing实现，并且功率分配由TPS2062电源开关实现，其内部电流限制通常为1.5 A（根据数据表，实际限制可能会有所不同） ）。不幸的是，Serial1也是由这个轨道供电。因此，1.5 A限制还包括遥测的显着电流消耗
![Screenshot](http://www.olliw.eu/uploads/canbuspowerschemes-auavx21-03.png)

来源：github.com/ArduPilot/Schematics/tree/master/mRobotics

## 摘要

上面讨论的飞行控制器的动力方案可以粗略地总结如下图（应该理解不同的飞行控制器在细节上有所不同）：
![Screenshot](http://www.olliw.eu/uploads/uc4h-powerschemes-sketch-pixhawk-01.jpg)

结论很清楚：所考虑的飞行控制器确实只向CAN总线网络提供有限的电流，如果超过该限制，则所有CAN节点都将断电。

你不希望这种情况发生在飞行器上，因为它必然会降低飞行器的速度。只要CAN总线节点不消耗大量功率，所采用的电源方案肯定是好的，但随着网络的增长和功能的增加，它很容易成为瓶颈。UC4H项目实际上很快就达到了这一点，因为它增加了越来越多的节点，包括一些需要大量功率的节点，如LED闪电节点（UC4H OreoLEDs）或测距仪节点（UC4H RangeFinder）。

我想完成本章，提到任何CAN电源方案都涉及某种电流限制，如果它是用于提供主电源的电源。此外，任何配电方案都具有固有的电流限制。例如，二极管OR-ing方案中的肖特基二极管具有额定电流，理想二极管方案中的FET具有额定电流，等等。这应该是清楚的，显然不是这里的主题。这里的主题是上述电源方案中的“人为”电流限制。


# 2. CAN节点分类

CAN节点可大致分类如下：

1低功耗与高功率
1飞行危急与非飞行危急
USB可用与非USB可用
前两点应该是自我解释，第三点可能不那么重要。让我以相反的顺序浏览它们：

USB可用与非USB可用：对于维护或在地面上，仅通过插入USB电缆为系统供电通常更方便，而不必插入电池。例如，参数设置，磁力计校准，功能检查等等。然而，USB（通常）不会（通常）提供大量电流，因此在仅连接USB时是否应该可用来区分节点是有意义的。例如GPS或磁力计应该属于第一类，闪电系统的LED可能在第二类。

除USB的电流限制外，还存在电压等级问题。一些CAN收发器，如TJA1051[Dronecode推荐](https://wiki.dronecode.org/workgroup/connectors/start#can_tranceivers)具有欠压保护机制，其截止电压电平为4.7 V。USB可能无法提供该电压，尤其是在负载下，CAN节点可能会发生即使USB提供足够的电流，当USB供电时也会脱机。需要明确的是，5 V第三代CAN收发器，如TJA1051，是件好事！它们有助于实现CAN总线网络的高稳健性，这就是为什么它们首先被开发以及为什么它们是我最喜欢的CAN收发器。然而，他们对USB供电的CAN总线网络有所提及的含义。

飞行关键与非飞行关键：嗯，这是非常明显的，没有进一步的说法。

低功耗与高功率：这也显而易见，但实际上值得一提。许多CAN节点可以分为高功率和低功率部分。一个典型的例子是UAVCAN ESC或 [UC4H OreoLED](http://www.olliw.eu/2017/uavcan-for-hobbyists/#chapternotifier)。这里的低功耗部分由一个微控制器组成，除了主要任务外还处理CAN总线通信。高功率部分包括ESC和ESC的电机以及OreoLED的明亮，耗电的LED。对于正常大功率的节点，因为它们在正常操作中消耗大量功率，如果它们可以在只有低功率部分有效的模式下工作，而高功率部分不能工作则很方便。被供电。这将允许例如配置节点而不必绘制全电流。您当然会注意到这种操作模式对于ESC很常见：它们中的许多都可以配置而无需连接电池，只需提供5 V电源即可。本段的要点是指出CAN节点也存在类似的情况。因此，简而言之，对于一些CAN节点，如果它们将由两条电源线（低功率和高功率线路）供电将是方便的，其中低功率线路将用于操作例如节点的主微控制器。

如果出现任何问题，完美的电源方案将首先停止为非飞行关键节点供电，并且最好从非关键高功率节点离线开始，但在所有情况下保持飞行关键节点的供电。显然，这需要一些复杂的功率监控和保护电子设备。这不是本文将要提供的内容。

但是，通过相对简单的方法，可以显着改善仅将所有CAN节点连接到飞行控制器的CAN总线的默认情况。这主要是为了了解这个问题，并采取适当的步骤，在手头的特定无人机的可能性。

因此，本文的目的是提高对这一点的认识，即飞行控制器的CAN总线电源可以受到限制，并提出一些典型和简单的电源方案。它也可以被视为提醒飞行控制器设计师提出更好的设计（而不仅仅是廉价的调整）。


# 3.电源方案

如前所述，我们讨论了电流消耗，以及如何最好地使每个CAN节点满意。

很明显，如果所有连接的CAN节点的功耗都很低，则根本没有问题，只需将它们全部连接到飞行控制器的CAN总线端口即可。提醒您“低”取决于所选择的飞行控制器以及连接到其他端口的外围设备，请参阅第1章中的讨论。因此，无法进行类似“XX A以下”的一般声明。作为一个指示，我认为GPS，磁力计和电调计通常应该没问题。

我开始考虑像CAN伺服电源轨这样的CAN总线电源，因为这里也有类似的要点。为了解决这些问题，今天的飞行控制器已经非常系统地将伺服电源导轨与飞行控制器和连接它的外围设备的电源分开。例如，Pixhawk上的MAIN OUT和AUX OUT引脚接头上的5 V引脚排没有连接到飞行控制器及其连接的外设（除了用于测量电压电平的传感器线）。事实上，需要将一个外部BEC连接到OUT 5 V引脚接头，以便为连接到伺服输出的组件供电。

因此，飞行控制器实际上已经实现了双功率方案，用于区分诸如磁力计，GPS，气压计等外围设备，以及诸如ESC，伺服系统，起落架等组件。

缺少的是该方案的类似扩展，还包括CAN节点。从概念上讲，它实际上非常简单，因为双电源方案已经存在。低功率CAN节点可以进入飞行控制器的5V线路，低功率CAN节点可以进入伺服电源轨，通常来自外部5 V BEC。在系统概念中，人们更喜欢三功率方案，其另外区分CAN总线和伺服功率，以便将CAN总线网络与例如伺服功率浪涌尖峰分开。

从概念上讲，这个提议将解决许多问题 - 除了一些不便之处：其中之一就是当今飞行控制器的连接器和连接器布置不能很好地支持从飞行控制器的5 V或伺服驱动CAN节点供电5 V（或三路方案中的5 V CAN总线）。另一个是额外的复杂性，一些CAN节点本身具有双电源特性，并且希望连接到两个5 V电源，如前面第2章所述。

因此，对于给定的飞行控制器，下一个最佳方法是直接从5.3 V电源砖本身为所有CAN节点供电。这种方法确保所有CAN节点都获得足够的功率和电压，因此是最可靠的。通过不将CAN-5V线连接到飞行控制器，而不是连接到电源砖，可以很容易地实现。该方案的缺点在于，当仅从USB供电时，没有节点可用，即，还必须附接电池以用于例如维护工作。然而，鉴于其简单性和简单实现，它可能是实现稳健电源方案的最佳选择。

一个明显的扩展是拆分CAN-5V电源线，这样一些CAN节点由飞行控制器的CAN-5V线路供电，而另一些CAN节点则由5.3 V电源砖供电。通过将飞行控制器的CAN-5V线路和5.3 V电源布线连接到那些更喜欢双电源方案的节点，显然可以进一步扩展该方案。所有这些都可以以DIY方式容易地实现，但不方便，因为目前使用的连接器和目前可用的CAN总线扩展不支持这样的方案。
![Screenshot](http://www.olliw.eu/uploads/uc4h-powerschemes-sketch-uc4h-cubecarrier-01.jpg)
