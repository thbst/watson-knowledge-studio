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

本文档适用于 {{site.data.keyword.cloud}} 上的 {{site.data.keyword.knowledgestudiofull}}。要查看 {{site.data.keyword.IBM_notm}} Marketplace 上先前版本的 {{site.data.keyword.knowledgestudioshort}} 的文档，请[单击此链接 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://console.bluemix.net/docs/services/knowledge-studio/dictionaries.html){: new_window}。
{: tip}

# 创建字典
{: #dictionaries}

字典用于帮助 {{site.data.keyword.knowledgestudioshort}} 机器注释器了解领域的语言。
{: shortdesc}

## 字典
{: #wks_dictionaries}

在机器学习中，字典将共享某些共同内容的词和短语分组在一起。字典中的条目并不表示该条目中的所有词都具有相同含义，但模型会平等对待这些词。

字典是对于信息抽取等效的词或短语的列表，这意味着这些词或短语在用于标识实体和关系提及项时是可互换的。

假设有以下示例：一个字典条目包含一周七天。为了注释文档，人类注释者将实体类型 DAY_OF_WEEK 分配给文本中的提及项 Monday 和 Friday。由于字典平等对待一周七天，因此有助于确保机器学习模型在运行时，对看不见的文档中的 Tuesday、Wednesday 和一周中其他日期进行正确注释。此外，平等对待这些词也有利于在周围文本中进行信息抽取。机器学习模型从培训示例中 Monday 和 Friday 附近的文本进行学习所了解的内容将适用于机器学习模型在一周中其他日期附近所看到的文本，因为字典声明这些词对于信息抽取是等效的。

> **注**：无需创建包含星期几信息的字典；像这样的多个通用字典会构建到应用程序中。其他字典包括国家或地区、位置名称、数字词、动物、植物、疾病、度量词（例如，ounce 和 meter）以及称呼头衔词（例如，Mr. 和 Mrs.）。不能禁用或编辑内置字典。

请避免添加具有多重含义的条目。例如，在关于赛车的领域中，仅当文本中没有同时频繁讨论金融机构时，包含术语 *bank*（指一种道路特征）才有意义。如果源文档中经常出现该词的两个含义，那么最好从两种类型的字典中去除该词：与道路特征关联的字典以及与金融机构关联的字典。

可以通过手动添加单个条目在 {{site.data.keyword.knowledgestudioshort}} 中创建字典。{{site.data.keyword.knowledgestudioshort}} 还支持上传多种类型字典文件的功能。

### 如何使用字典？

字典可通过多种方式使用，这些方式均是可选的。字典由机器学习模型用于提供对于信息抽取等效的词或短语，并在预注释期间用于引导注释工作。

- **在机器学习中使用**

    与字典关联的实体类型不用于定义机器学习模型的规则。机器学习会对文档中的提及项进行单独评估。机器学习不会仅仅因为提及项与特定实体类型的关联字典中的条目相匹配，就假定该提及项具有该实体类型。它会考虑该信息，但将其视为通过语言分析收集的其他多条信息中的一条信息。事实上，如果字典中没有任何术语出现在参考标准文档中，那么机器学习模型根本就不会使用该字典。

- **在预注释中使用**

    字典对于以下预注释过程很重要。
    - 字典预注释器：运行字典预注释器时，可使该字典与类型系统中的实体类型相关联。
    - 基于规则的模型：可以选择将字典与规则类相关联。然后，运行基于规则的模型以对文档进行预注释时，类将从类型系统映射到实体类型。因此，字典术语也会映射到基于规则的模型的实体类型，虽然过程迂回曲折。

    在这两种情况下，字典都提供了系统可以找到并注释为提及项的术语。字典会为每个提及项分配与包含该术语的字典关联的实体类型。当人类注释者开始处理已预注释的新文档时，许多提及项已经根据字典条目进行了注释。因此，人类注释者有更多时间专注于为需要更深入分析的提及项分配实体类型。

### 语言注意事项

- 对于巴西葡萄牙语、英语、法语、德语、意大利语和西班牙语，{{site.data.keyword.knowledgestudioshort}} 目前未提供用于指定不区分大小写字典匹配的选项，但字典条目会与大写文本相匹配。例如，字典中的“vehicle”与文本中的“vehicle”、“Vehicle”或“VEHICLE”相匹配，而字典中的“Sat”与文本中的“Sat”或“SAT”相匹配，但与“sat”不匹配。
- 对于日语和韩国语，预注释期间的字典匹配是区分大小写的。
- 对于阿拉伯语，{{site.data.keyword.knowledgestudioshort}} 假定阿拉伯语文本存储时未调整字型，并将数字字型视为存储级别属性。有关 {{site.data.keyword.knowledgestudioshort}} 如何处理阿拉伯语字符字型和数字字型的详细信息，请参阅[配置阿拉伯语支持](/docs/services/watson-knowledge-studio/language-support.html#wks_langsupp_ar)。

### CSV 文件字典
{: #wks_dictionaries__cvsdict}

逗号分隔值 (`CSV`) 格式的字典也称为标准字典格式，是上传后可编辑的文件。可以上传的 `CSV` 文件的最大大小为 1 MB。如果您有更大的字典文件，请将大型文件分解成多个文件，然后将这些文件逐个上传到 {{site.data.keyword.knowledgestudioshort}} 工作空间的单个字典中。

总之，要求是必须使用文本编辑器来创建 `CSV` 文件，而不能使用类似 Microsoft Excel 这样的软件，并且该文件必须使用 UTF-8 编码，且在文本流开头不包含字节顺序标记 (BOM)。文件中的第一行必须指定以下列标题：

```
lemma,poscode,surface
```
{: screen}

文件中的其余行指定字典条目，其中：

- **`lemma`**

    指定条目最具代表性的词格式。

- **`poscode（阿拉伯语、巴西葡萄牙语、英语、法语、德语、意大利语和西班牙语）`**

    指定用于标识词性的代码。字典注释器使用此词性信息来帮助进行语句记号化。
    - 0 - 未知

        > **注**：此代码支持您希望上传一个机器生成的大型字典，而该字典并未在每个条目中包含词性信息的场景。缺省情况下，可以将“未知”分配给所有条目。如果可能，请避免使用此代码。

    - 1 - 代词
    - 2 - 动词
    - 3 - 名词
    - 4 - 形容词
    - 5 - 副词
    - 6 - 介系词
    - 7 - 感叹词
    - 8 - 连词
    - 9 - 限定词
    - 10 - 量词

    在英语中，名词 (3)、动词 (2) 和形容词 (4) 是用于字典条目的最常见词性。

    > **注**：词性不会自动确定提及项的类型。不要假定所有名词都等同于实体类型的提及项，也不要假定所有动词等同于关系类型的提及项。例如，*American* 是形容词，但最好将其注释为实体类型 GPE（地缘政治实体）或 PERSON。*Met* 是动词，但可能最好将其注释为 EVENT_METING。

    在使用复合词的其他语言（如德语）中，词性信息的准确性在帮助确定词语边界方面甚至更重要。

- **`poscode（日语）`**

    指定用于标识词性的代码。在像日语这样不使用空格来表示词语边界的语言中，词性值对于文本记号化和预注释非常重要。
    - 19 - 名词
    - 23 - 公共前缀
    - 24 - 公共后缀
    - 140 - 专有名词（姓氏）
    - 141 - 专有名词（名字）
    - 146 - 专有名词（人名）
    - 142 - 专有名词（组织）
    - 144 - 专有名词（位置名称）
    - 143 - 专有名词（区域）
    - 145 - 专有名词（其他）

- **`poscode（韩国语）`**

    指定用于标识词性的代码。在像韩国语这样不使用空格来表示词语边界的语言中，词性值对于文本记号化和预注释非常重要。
    - 10010 - 名词
    - 10300 - 专有名词（姓氏）
    - 10310 - 专有名词（名字）
    - 110360 - 专有名词（人名）
    - 10320 - 专有名词（组织）
    - 10340 - 专有名词（位置名称）
    - 10330 - 专有名词（区域）
    - 10350 - 专有名词（其他）

- **`surface`**

    指定等效术语（也称为“表面形式”）。将词元作为表面形式重复，并使用逗号来分隔多个表面形式。如果表面形式包含逗号，请用引号将该表面形式括起。

例如：

```
lemma,poscode,surface
IBM,3,IBM Corp.,IBM,"International Business Machines, Inc."
Department of Energy,3,DOE,Department of Energy
premium,4,premium,premium-grade
```
{: screen}

**相关概念**：

[从其他工作空间上传资源](/docs/services/watson-knowledge-studio/exportimport.html)

**相关任务**：

[使用字典预注释器对文档进行预注释](/docs/services/watson-knowledge-studio/preannotation.html#wks_preannot)

## 向工作空间添加字典
{: #wks_projdictionaries}

添加字典是创建模型中的一个可选步骤。字典非常有用，因为您可借助字典快速开始注释过程。

### 关于本任务

如果提供了字典，那么可以在文档上运行该字典的预注释器。预注释器找到字典中表示的术语后，会自动对其进行注释。对文档执行这一轮初始处理简化了人类注释者的作业，因为注释者可以查看预注释器添加的注释，并对其进行更正或向其添加内容。注释者不必从头开始。

以下限制适用于字典：

- 每个字典最多 15,000 个条目

    > **注**：此限制不适用于上传为字典 CSV 文件的字典。只读字典可以包含更多条目。

- 每个工作空间最多 64 个字典

### 过程

要向工作空间添加字典，请执行以下操作：

1. 以 {{site.data.keyword.knowledgestudioshort}} 管理员或项目经理身份登录，然后打开**资产和工具** > **预注释器** > **字典**选项卡。
1. 执行下列其中一项任务：

    - 单击**上传字典**按钮，选择字典，然后单击**上传**。上传字典后，单击**管理字典**以查看该字典并将其与实体类型相关联。

        - 可以上传包含已从其他 {{site.data.keyword.knowledgestudioshort}} 工作空间下载的字典的 ZIP 文件。必须先以 JSON 格式上传从其他工作空间下载的类型系统，然后才能上传相应的字典文件。针对您从其他 {{site.data.keyword.knowledgestudioshort}} 工作空间复用的字典，可以编辑和添加条目。有关更多详细信息，请参阅[从其他工作空间上传资源](/docs/services/watson-knowledge-studio/exportimport.html)。

        此外，还支持上传 CSV 文件，但直接将其上传为字典将创建仅预览字典，您无法对其进行编辑或用于对文档进行预注释。要上传可编辑并可用于预注释的 CSV 文件，请单击**管理字典**，然后单击**创建字典**以首先创建字典，然后将 CSV 内容作为条目上传到这一新创建的字典。

    - 单击**管理字典**按钮以创建新字典，随后可以向其添加字典条目。

        单击**创建字典**按钮，并为字典指定描述性名称。选择可最好地描述字典用途的实体类型，然后单击**保存**。

1. 要向字典添加条目，请执行下列其中一项任务：

    - 单击**添加条目**以添加字典条目。指定词元（术语最具代表性的词形式）。
    - 单击**上传**以上传包含字典条目的 `CSV` 文件，然后浏览以选择该文件。CSV 文件必须小于 1 MB。

1. 上传或添加条目后，可以编辑这些条目。

    打开条目以指定等效术语（称为“表面形式”）。每个表面形式的长度都必须小于或等于 256 个字符。可以更改哪个表面形式用作词元。

    例如，词元 *{{site.data.keyword.IBM_notm}}* 可能具有类似于 *{{site.data.keyword.IBM_notm}} Corp.* 和 *International Business Machines, Inc.* 的表面形式。

1. 为字典中的每个词元和表面形式选择相应的词性。

    词性信息由记号化器使用，另外在预注释期间也会使用词性信息。

1. 单击**保存**以存储更改。

### 后续步骤

运行预注释器，以使用已创建的字典对源文档执行初始一轮处理，并向这些文档添加注释。

**相关任务**：

[使用字典预注释器对文档进行预注释](/docs/services/watson-knowledge-studio/preannotation.html#wks_preannot)

**相关参考**：

[多语言支持](/docs/services/watson-knowledge-studio/language-support.html)