---

copyright:
  years: 2015, 2018
lastupdated: "2018-04-04"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:tip: .tip}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}

本文档适用于 {{site.data.keyword.cloud}} 上的 {{site.data.keyword.knowledgestudiofull}}。要查看 {{site.data.keyword.IBM_notm}} Marketplace 上先前版本的 {{site.data.keyword.knowledgestudioshort}} 的文档，请[单击此链接 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://console.bluemix.net/docs/services/knowledge-studio/build-groundtruth.html){: new_window}。
{: tip}

# 构建参考标准
{: #build-groundtruth}

注释项目的目标是获取参考标准；参考标准是用于使 {{site.data.keyword.watson}} 适应特定领域的已检查数据的集合。在 {{site.data.keyword.knowledgestudioshort}} 中，人类注释者（通常是目标领域的主题专家）在确定参考标准方面扮演着主要角色。
{: shortdesc}

典型工作流程包括以下步骤：

1. 人类注释者提交已注释的文档以供复查。
1. 项目经理（可能是一位高级领域专家）复查其工作的准确性，并比较人类注释者对注释集之间重叠的文档进行注释的一致程度。
1. 如果注释者间一致性分数太低，那么项目经理会拒绝该注释集，并将其返回给人类注释者以进行改进。注释集被拒绝后，该集内的所有文档都会恢复为可编辑方式。
1. 如果项目经理核准了注释集，那么在注释集之间不重叠的文档会升级为参考标准，以便可以使用注释来培训模型。
1. 项目经理复查重叠的文档并解决注释冲突。此阶段称为“裁定”；在此阶段中，团队可能会复查和修改注释准则，以帮助澄清导致不同人类注释者以不正确或不一致方式对文本进行注释的误解之处。

    在某些情况下，项目经理可能希望用于对注释者间一致性求值的重叠百分比高于用于裁定差异的重叠百分比。例如，如果注释者间一致性看起来正常，那么项目经理可能会断定将任一注释升级为参考标准没问题。

1. 项目经理解决注释冲突后，核准的注释将升级为参考标准。

请记住，人工注释始终需要进行主观判断。注释准则可以执行许多操作来确保以正确、一致的方式对文本进行注释，但即便是最佳准则也有赖于人来解释。要获取参考标准，您需要花时间对人类注释者进行培训和教育，使其在分析领域内容时能够做出最佳判断。

## 注释者间一致性
{: #wks_haiaa}

人类注释者对文档进行了注释后，您必须确定哪些文档可升级为参考标准。首先，请复查注释者间一致性分数。分数较低的文档是要拒绝并返回给人类注释者进行改进的候选文档。

计算注释者间一致性分数时，系统会检查任务中所有注释集内的所有重叠文档，而不考虑这些集的状态。由于在注释集处于“已提交”状态之前，您无法接受或拒绝注释集，因此您可能希望在提交所有注释集后再对注释者间一致性求值，或者您可能希望将复查仅限于提交了已完成的注释集的人类注释者。

注释者间一致性分数说明不同的人类注释者如何对提及项、关系和指代链进行注释。您可以通过比较一对人类注释者（例如，将 John 的所有提及项注释与 Mary 的所有提及项注释进行比较）来查看分数。您还可以通过比较特定文档（例如，将 John 在一个文档中所做的关系注释与 Mary 在同一文档中所做的关系注释进行比较）来查看分数。

为了帮助您识别需要调查的区域，低于指定的注释者间一致性阈值的分数将以红色突出显示。在注释项目的早期阶段中，您可能会发现关系分数通常比提及项分数更差，因为完美关系注释要求用于定义关系的提及项首先一致。

**所有**列中的分数是 *Fleiss Kappa 分数*。此分数表示在任务中的所有重叠文档之间，多个人类注释者应用同一注释的一致程度。该值可以帮助您识别注释准则或特定人类注释者中的薄弱环节；该值的范围上限为 1，甚至可以是负数。以下准则（*Landis 和 Koch，1977*）为评估总体性能提供了起点：

<table cellpadding="4" cellspacing="0" summary="" id="wks_haiaa__table_p5s_dx1_f5" class="table" rules="rows" frame="void" border="0"><thead class="thead" align="left"><tr class="row"><th class="entry ncol thleft" align="left" valign="top" id="d12741e148">分数</th>
<th class="entry ncol thleft" align="left" valign="top" id="d12741e150">一致程度</th>
</tr>
</thead>
<tbody class="tbody"><tr class="row"><td class="entry ncol tdleft" align="left" valign="top" headers="d12741e148 "><p class="p wrapper">&lt; 0</p></td>
<td class="entry ncol tdleft" align="left" valign="top" headers="d12741e150 "><p class="p wrapper">差</p></td>
</tr>
<tr class="row"><td class="entry ncol tdleft" align="left" valign="top" headers="d12741e148 "><p class="p wrapper">.01 - .20</p></td>
<td class="entry ncol tdleft" align="left" valign="top" headers="d12741e150 "><p class="p wrapper">略差</p></td>
</tr>
<tr class="row"><td class="entry ncol tdleft" align="left" valign="top" headers="d12741e148 "><p class="p wrapper">.21 - .40</p></td>
<td class="entry ncol tdleft" align="left" valign="top" headers="d12741e150 "><p class="p wrapper">尚可</p></td>
</tr>
<tr class="row"><td class="entry ncol tdleft" align="left" valign="top" headers="d12741e148 "><p class="p wrapper">.41 - .60</p></td>
<td class="entry ncol tdleft" align="left" valign="top" headers="d12741e150 "><p class="p wrapper">一般</p></td>
</tr>
<tr class="row"><td class="entry ncol tdleft" align="left" valign="top" headers="d12741e148 "><p class="p wrapper">.61 - .80</p></td>
<td class="entry ncol tdleft" align="left" valign="top" headers="d12741e150 "><p class="p wrapper">优秀</p></td>
</tr>
<tr class="row"><td class="entry ncol tdleft" align="left" valign="top" headers="d12741e148 "><p class="p wrapper">.81 - 1.0</p></td>
<td class="entry ncol tdleft" align="left" valign="top" headers="d12741e150 "><p class="p wrapper">完美</p></td>
</tr>
</tbody>
</table>

其他列中的分数是 *F1 度量*，表示一对人类注释者之间的注释一致程度。该值的范围从 0 到 1，其中分数 1 指示完美一致性。构成可接受一致程度的内容取决于领域数据和类型系统。但是，为了提供示例，下面是项目经理期望在基于 KLUE 类型系统的项目中达到或超过的 F1 阈值：

- 实体类型的提及项：0.85
- 关系：0.8
- 指代链：0.9

对分数的解释取决于类型系统的复杂性、注释的内容量和复杂程度、人类注释者的经验程度以及其他因素。例如，如果注释任务侧重于标注词性，那么您可能会期望看到高分数，因为词性定义明确。但是，需要对文本进行更深入分析的任务（需要人工解释）可能会显示较低分数，直到您执行相关步骤来澄清不确定性的原因为止。

通常，包含许多实体类型和关系类型的类型系统有待做出更多解释，因此，在模型开发的早期阶段，注释者间一致性可能会较低。例如，通过查看分数，您可以了解哪些实体类型分数较低。分数低指示需要改进注释准则。通过查看成对人类注释者的分数，可以确定特定注释者显示的分数是否始终比其他注释者低。如果是，说明此注释者可能在理解准则或使用注释工具方面遇到问题，因此是接受更多培训的候选者。

## 复查注释者间一致性分数
{: #wks_haaccuracy}

确定要升级为参考标准的文档时，必须复查注释者间一致性分数。分数较低的文档是要拒绝并返回给人类注释者进行改进的候选文档。

### 关于本任务

检查注释者间一致性时，您会检查由多个人类注释者注释的文档。如果某个文档未在多个注释集和人类注释者之间共享，那么不会计算注释者间一致性。将注释集添加到任务时，请确保要比较的集包含相同的重叠文档。通过打开**资产和工具** > **文档**页面，单击**注释集**选项卡，然后单击注释集的名称，可以查看相应注释集内有哪些文档。

您可能会遇到找不到重叠文档的情况。例如，如果执行了创建注释集的两轮操作，并将这些注释集都添加到同一任务中，就可能发生这种情况。尽管注释集几乎是在同一时间创建的，也不会包含任何共有的文档。再例如，如果创建了具有重叠文档的注释集，但对每个任务添加一个注释集，而不是将所有注释集都添加到单个任务，那么也找不到任何重叠文档，因此也无法计算注释者间一致性。

### 过程

要评估人类注释者之间的注释一致性，请执行以下操作：

1. 以 {{site.data.keyword.knowledgestudioshort}} 管理员或项目经理身份登录，然后选择工作空间。
1. 选择**资产和工具** > **文档** > **任务**选项卡，然后打开要进行求值的任务。
1. 单击**计算注释者间一致性**。缺省视图会显示一致性分数，以表示成对人类注释者对提及项进行注释的一致程度。顶行显示每对注释者之间的总体一致性，顶行下的表显示成对注释者对文本中特定提及项进行标注的一致程度。
1. 要探索成对人类注释者对关系和指代进行注释的一致程度，请从第一个菜单中选择**关系**或**指代**。
1. 要研究成对注释者对特定重叠文档中的实体、关系或指代进行注释的一致程度，请选择第二个菜单中的**文档**，然后选择要进行求值的一对注释者。
1. 复查分数后，可以决定是要核准还是拒绝处于“已提交”状态的注释集。提交注释集后，在其名称旁边会显示一个复选框。请执行下列其中一个操作：

    - 如果注释集的注释者间一致性分数是可接受的，请选中该复选框并单击**接受**。不与其他注释集重叠的文档将升级为参考标准。重叠的文档必须首先通过裁定进行复查，以便可以解决冲突。
    - 如果注释集的注释者间一致性分数是不可接受的，请选中该复选框并单击**拒绝**。人类注释者需要重新访问注释集以改进注释。

## 裁定
{: #wks_haperform}

如果多个人类注释者在处理同一文档，那么在将注释升级为参考标准之前，您可能需要解决注释之间的不一致性。此冲突解决过程称为“裁定”。

核准由人类注释者提交的注释集后，在注释集之间不重叠的文档会升级为参考标准。在升级重叠文档之前，您应该检查注释是否有冲突。发现不一致的情况时，您必须决定如何解决冲突：是从人类注释者应用的注释中选择正确的注释，还是选择其他注释进行应用。

文档在至少满足下列其中一个条件时，才可供裁定：

- 项目经理同时核准了一个任务中的两个或更多注释集，并且至少两个注释集内存在相同的文档（重叠文档）。
- 项目经理在裁定先前核准的注释集内的文档之前，已核准其他注释集。如果裁定注释集 A 和注释集 B 之间重叠的文档，将注释升级为参考标准，然后核准了具有相同文档的另一个注释集 C，那么新核准的文档中的注释将自动升级为参考标准，因为冲突不再存在。请注意，注释集 C 中升级的注释将覆盖在裁定注释集 A 和 B 中的重叠文档时确定的参考标准。如果在升级注释集 A 和 B 中的注释之前核准了注释集 C，那么可以检查集 C 中的重叠文档是否存在冲突并对其进行裁定。

如果您花费时间来改进注释准则，那么随着时间的推移，用于裁定的时间量可能会缩短。通过提供示例并澄清引起混淆的区域，可以帮助人类注释者从错误中吸取教训，并避免未来发生冲突。

下面是人类注释者不一致的各种方面的一些示例：

- **提及项**

    - Annotator_1 在文本范围上放置了提及项；Annotator_2 没有。
    - Annotator_1 的索引开始或结束早于或晚于 Annotator_2（存在部分重叠或子范围的文本）。
    - Annotator_1 分配的实体类型不同于 Annotator_2 分配的实体类型。

- **关系**

    - Annotator_1 在两个提及项之间创建了关系；Annotator_2 没有。
    - Annotator_1 和 Annotator_2 在相同提及项之间创建了关系，但是关系类型不同。
    - Annotator_1 和 Annotator_2 在相同提及项之间创建了关系，但顺序相反（这种情况极少发生，因为第一个提及项和第二个提及项之间的关系受类型系统约束）。

- **指代链**

    - Annotator_1 的指代链版本包含（或排除）了 Annotator_2 的指代链中排除（或包含）的提及项。Annotator_1 和 Annotator_2 之间实体的一致性会成为评分问题。

## 解决注释冲突
{: #wks_haadjudicate}

裁定步骤支持您对重叠文档中的注释冲突进行复查后，再将注释升级为参考标准。您可以比较一对人类注释者添加的注释，或者将人工注释与当前的参考标准进行比较。

### 开始之前

单击[此链接 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.youtube.com/watch?v=EbexfsuXxoQ&amp;feature=youtu.be){: new_window} 可观看长度为 3 分钟的视频，其中说明了如何对文档进行裁定。

### 关于本任务

人类注释者完成其注释任务后，必须提交已完成的注释集以供复查。对注释者间一致性分数求值后，可以查看不同的注释者对如何对同一文档进行注释。如果注释者间一致性分数可接受，那么可核准该注释集。如果文档在任务中的各注释集之间不重叠，那么核准后的文档中的注释会升级为参考标准。如果文档在各注释集之间重叠，那么应裁定该文档并解决存在的任何注释冲突后，将其中的注释升级为参考标准。

例如，裁定文档时，可能会看到一个注释者使用实体类型 `PeoplePerson` 对提及项 `Barack Obama` 进行了注释。另一个注释者将同一文本字符串作为两个提及项进行注释：为 `Barack` 分配实体类型 `Person`，为 `Obama` 分配实体类型 `Person`。为了帮助确保对机器学习模型进行正确培训，您应该解决这种不一致情况。

{{site.data.keyword.knowledgestudioshort}} 支持一次在两个注释集之间进行裁定，或者在注释集与当前参考标准之间进行裁定的功能。如果文档在两个以上的注释集之间重叠，请裁定您最信任的两个注释集（可能是因为您更信任人类注释者），从而确定文档的参考标准。然后根据初始裁定的结果，对其余的注释集进行裁定。

### 过程

要查看重叠文档并解决冲突，请执行以下操作：

1. 以 {{site.data.keyword.knowledgestudioshort}} 管理员或项目经理身份登录，然后选择工作空间。
1. 选择**资产和工具** > **文档** > **任务**选项卡，然后打开要进行求值的任务。
1. 确认至少有两个注释集处于**冲突**状态。
1. 单击**检查重叠文档以查找冲突**。这将列出冲突的文档。
1. 如果要忽略重叠文档中的冲突并将注释升级为参考标准，而不对其进行裁定，请单击**接受**。
1. 要开始解决重叠文档中的冲突，请单击**检查冲突**。
1. 如果有三个或更多候选项要进行裁定，包括通过人工注释和当前参考标准进行了注释的注释集，请选择要裁定的两个候选项，然后单击**检查冲突**。如果仅存在两个候选项，那么会自动启动裁定工具。

    裁定工具会显示存在多少个提及项、关系和指代链冲突。必须解决提及项冲突后，才能接着解决关系和指代链之间的冲突。

1. 单击以突出显示包含冲突的语句。为了更轻松地专注于未解决的冲突，您可能希望清除**已解决**复选框，并确保选中**未解决**复选框。
1. 单击单个注释，然后单击**接受**或**拒绝**。要撤销刚刚做出的决策，请按 `Ctrl+Z`。

    您还可以单击多个注释，然后单击以接受或拒绝全部所选的注释。如果决定要取消选择所选的注释，请通过单击语句之间的空白处或按 **Esc** 键来清除选择。

    如果注释准则先前已连接到项目，并且您希望帮助选择要应用的正确注释，请单击**查看准则**。根据在托管准则的站点上所设置的访问许可权，您或许能够在打开这些准则后对其进行更新，例如添加澄清和示例。
    {: tip}

1. 要将其他实体类型应用于提及项，请拒绝当前注释，选择提及项（例如 `Barack Obama）`，然后选择正确的实体类型（例如 `Person`）。
1. 继续对语句中的其他冲突执行接受、拒绝和修改操作。解决语句中的所有冲突后，单击**选择所有注释**链接，然后单击**接受**。
1. 单击箭头图标以转至下一个语句。继续操作，直到解决了文档中的所有提及项冲突为止。

    要保存进行中的工作或暂时暂挂当前的裁定会话，请随时单击**保存**。如果要放弃所做的全部更改，请单击**放弃**。如果保存了先前的裁定会话，并且您已准备好继续进行裁定，请单击**恢复**以从上次停止的位置开始裁定冲突。如果已放弃先前的裁定会话，那么必须启动新的裁定会话。
    {: tip}

1. 解决了提及项冲突后，请切换裁定方式以解决关系注释与指代链注释之间发生的任何冲突。

    - 在关系方式下解决冲突类似于解决提及项冲突的方式。您可以逐个语句解决冲突。
    - 指代链可以跨多个语句存在。移至指代方式时，系统会在右侧的窗格中列出每个人类注释者所创建的指代链。选择要裁定的链，然后单击**拒绝链**或**接受链**以拒绝或接受链中的注释，或者单击**合并链**以合并这两个链。如果要除去指代链中的提及项，请单击文档区域中提及项上方标签中的“删除”图标 (-)。

1. 解决文档中的所有冲突后，单击**升级为参考标准**。