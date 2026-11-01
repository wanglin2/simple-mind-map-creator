---
name: simple-mind-map-creator
description: '一个用于生成思维导图的技能。最终会生成一个HTML文件，可以在浏览器打开，查看生成的可视化思维导图，还可以进行编辑（新增、修改、删除节点、切换主题、切换结构、导出为图片、svg、pdf等文件）。'
license: MIT
---

# 生成思维导图技能

根据用户提供的内容或需求生成思维导图。

## 工作流程

### 第一步：生成思维导图内容

根据用户提供的内容或需求，生成思维导图内容。

你需要生成一个JSON数据，具体格式请参考：

```js
{
  data: {
    text: '', // 节点的文本
    uid: '',// 节点唯一的id，可不传
    image: '', // 图片的url
    imageTitle: '', // 图片的标题，可为空
    imageSize: { // 图片的尺寸
      width: 100, // 图片的宽度，必传
      height: 100, // 图片的高度，必传
      custom: false // 如果设为true，图片的显示大小不受主题控制，以imageSize.width和imageSize.height为准
    },
    hyperlink: '', // 超链接地址
    hyperlinkTitle: '', // 超链接的标题
    note: '', // 备注的内容
    tag: [// 标签列表
        {
            text: '',
            style: {}// 标签的样式，具体格式可参考下方【标签的样式】
        }
    ],
    generalization: [// 节点的概要，如果没有概要generalization设为null即可
        {
            text: '', // 概要的文本
            // ...其他普通节点的字段都支持，但是不支持children
        }
    ],
    associativeLineTargets: [''],// 如果存在关联线，那么为目标节点的uid列表
    associativeLineText: {},// 关联线文本
    associativeLinePoint: [],// 关联线的坐标数据
    associativeLineTargetControlOffsets: [],// 关联线的坐标偏移数据
    associativeLineStyle: {},// 关联线的样式
    // ...其他样式字段，具体可用样式字段可参考下方【节点的样式】
  },
  children [// 子节点，结构和根节点一致
    {
      data: {},
      children: []
    }
  ]
}
```

- 标签的样式

| 字段名称 | 类型   | 默认值 | 描述                                                           |
| -------- | ------ | ------ | -------------------------------------------------------------- |
| radius   | Number | 3      | 标签矩形的圆角大小                                             |
| fontSize | Number | 12     | 字号，建议文字高度不要大于height                               |
| fill     | String |        | 标签矩形的背景颜色                                             |
| height   | Number | 20     | 标签矩形的高度                                                 |
| paddingX | Number | 8      | 水平内边距，如果设置了width，将忽略该配置                      |
| width    | Number |        | 标签矩形的宽度，如果不设置，默认以文字的宽度+paddingX\*2为宽度 |

- 节点的样式

每个节点都支持如下样式字段：

```js
{
    shape: 'rectangle',// 节点形状：矩形（rectangle）、菱形（diamond）、平行四边形（parallelogram）、圆角矩形（roundedRectangle）、八角矩形（octagonalRectangle）、外三角矩形（outerTriangularRectangle）、内三角矩形（innerTriangularRectangle）、椭圆（ellipse）、圆（circle）
    fillColor: '#549688',
    fontFamily: '微软雅黑, Microsoft YaHei',
    color: '#fff',
    fontSize: 16,
    fontWeight: 'bold',
    fontStyle: 'normal',
    borderColor: 'transparent',
    borderWidth: 0,
    borderDasharray: 'none',
    borderRadius: 5,
    textDecoration: 'none',// overline、underline、line-through、none
    gradientStyle: false,
    startColor: '#549688',
    endColor: '#fff',
    startDir: [0, 0],
    endDir: [1, 0],
    // 连线标记的位置，start（头部）、end（尾部），该配置在showLineMarker配置为true时生效
    lineMarkerDir: 'end',
    // 节点鼠标hover和激活时显示的矩形边框的颜色，主题里不设置，默认会取hoverRectColor实例化选项的值
    hoverRectColor: '',
    // 点鼠标hover和激活时显示的矩形边框的圆角大小
    hoverRectRadius: 5,
    // 文本对齐
    textAlign: 'left',// right、center、justify、left
    // 图片放置位置，相对于整个文本内容
    imgPlacement: 'top', // left、right、bottom、top
    // 标签放置位置
    tagPlacement: 'right', // right（文字右侧）、bottom（文本内容下方）
    // 节点内边距
    paddingX: 15,
    paddingY: 5,
    // 连线的粗细
    lineWidth: 1,
    // 连线的颜色
    lineColor: '#549688',
    // 连线样式
    lineDasharray: 'none',
    // 关联线默认状态的粗细
    associativeLineWidth: 2,
    // 关联线默认状态的颜色
    associativeLineColor: 'rgb(51, 51, 51)',
    // 关联线激活状态的粗细
    associativeLineActiveWidth: 8,
    // 关联线激活状态的颜色
    associativeLineActiveColor: 'rgba(2, 167, 240, 1)',
    // 关联线样式
    associativeLineDasharray: '6,4',
    // 关联线文字颜色
    associativeLineTextColor: 'rgb(51, 51, 51)',
    // 关联线文字大小
    associativeLineTextFontSize: 14,
    // 关联线文字行高
    associativeLineTextLineHeight: 1.2,
    // 关联线文字字体
    associativeLineTextFontFamily: '微软雅黑, Microsoft YaHei',
}
```

`./examples/`目录下也提供了几个实际的数据示例可供参考。

可以适当选择一些合适的emoji表情增加生趣，一定要确保生成的JSON数据是合法的。

### 第二步：选择合适的结构和主题

你需要根据生成的内容选择合适的结构和主题。

所有可用的结构如下：

```json
[
  {
    "name": "思维导图",
    "value": "mindMap"
  },
  {
    "name": "逻辑结构图",
    "value": "logicalStructure"
  },
  {
    "name": "逻辑结构图（左）",
    "value": "logicalStructureLeft"
  },
  {
    "name": "思维导图",
    "value": "mindMap"
  },
  {
    "name": "目录组织图",
    "value": "catalogOrganization"
  },
  {
    "name": "组织结构图",
    "value": "organizationStructure"
  },
  {
    "name": "时间轴",
    "value": "timeline"
  },
  {
    "name": "时间轴",
    "value": "timeline2",
    "desc": "上下交替型"
  },
  {
    "name": "竖向时间轴",
    "value": "verticalTimeline",
    "desc": "三级节点左右交替分布"
  },
  {
    "name": "竖向时间轴",
    "value": "verticalTimeline2",
    "desc": "三级节点全部分布在左侧"
  },
  {
    "name": "竖向时间轴",
    "value": "verticalTimeline3",
    "desc": "三级节点全部分布在右侧"
  },
  {
    "name": "鱼骨图",
    "value": "fishbone"
  },
  {
    "name": "带鱼头鱼尾形状的鱼骨图",
    "value": "fishbone2"
  }
]
```

推荐选择：思维导图、逻辑结构图。

`.themes/themeList.json`文件提供了所有可用的主题，同时`./data/themes/`目录下还提供了各个主题的预览图片，图片名称和主题的`value`字段一一对应。如果你能图片阅读能力，那么可以直接通过图片选择合适的主题。

### 第三步：生成思维导图文件

这一步，你需要将前面生成的思维导图数据、选择的结构、主题填充到HTML文件中，生成一个完整的思维导图HTML文件。

HTML模板文件在`./templates/index.html`。

你需要：

1.将其中的`__MIND_MAP_DATA__`替换为你生成的JSON数据；

2.将其中的`__MIND_MAP_LAYOUT__`替换为你选择的结构数据，即结构列表中的`value`字段对应的值；

3.将其中的`__MIND_MAP_THEME__`替换为你选择的主题数据，即主题列表中的`value`字段对应的值；

4.将其中的`__MIND_MAP_TITLE__`替换为思维导图的标题；

生成完成后需要告知用户生成文件的存放路径。
