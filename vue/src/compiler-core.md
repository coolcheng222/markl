# Compiler-core

是vue的compiler核心代码,没有readme,靠自己猜

## 一. tokenizer.parse

这是vue核心的从`vue代码`转换到`(AST)rootnode`的方法,来研究一下JS怎么做抽象语法树吧

以SFC+DEV的编译为例

### 0. 全局笔记

> 记录一些全局性的内容

#### 0.1 RCDATA和RAWTEXT

源码中频繁提及了这两个词,目前看来

RCDATA是指textarea,title中包裹的内容的一种描述

RAWTEXT特指script和style的内容,且一般认为SFC中除了template就是RAWTEXT



现在已经清楚了,在读tag的时候会因一些原因进入RCDATA模式,该模式表示进入tag时读取内部数据的模式,在该模式下,会直接去寻找进入RCDATA模式时设置的sequence/offset

有时候也会单纯被用作匹配某个sequence

#### 0.2 Sequence机制

开发者为一些长且固定的序列设置了一个Sequence机制,研究一下

```js
/**
 * Sequences used to match longer strings.
 *
 * We don't have `Script`, `Style`, or `Title` here. Instead, we re-use the *End
 * sequences with an increased offset.
 */
export const Sequences: {
  Cdata: Uint8Array
  CdataEnd: Uint8Array
  CommentEnd: Uint8Array
  ScriptEnd: Uint8Array
  StyleEnd: Uint8Array
  TitleEnd: Uint8Array
  TextareaEnd: Uint8Array
} = {
  Cdata: new Uint8Array([0x43, 0x44, 0x41, 0x54, 0x41, 0x5b]), // CDATA[
  CdataEnd: new Uint8Array([0x5d, 0x5d, 0x3e]), // ]]>
  CommentEnd: new Uint8Array([0x2d, 0x2d, 0x3e]), // `-->`
  ScriptEnd: new Uint8Array([0x3c, 0x2f, 0x73, 0x63, 0x72, 0x69, 0x70, 0x74]), // `</script`
  StyleEnd: new Uint8Array([0x3c, 0x2f, 0x73, 0x74, 0x79, 0x6c, 0x65]), // `</style`
  TitleEnd: new Uint8Array([0x3c, 0x2f, 0x74, 0x69, 0x74, 0x6c, 0x65]), // `</title`
  TextareaEnd: new Uint8Array([
    0x3c, 0x2f, 116, 101, 120, 116, 97, 114, 101, 97,
  ]), // `</textarea
}
```

以CDATA为例分析解析方式

```js
private stateCDATASequence(c: number): void {
    if (c === Sequences.Cdata[this.sequenceIndex]) {
    if (++this.sequenceIndex === Sequences.Cdata.length) {
        this.state = State.InCommentLike
        this.currentSequence = Sequences.CdataEnd
        this.sequenceIndex = 0
        this.sectionStart = this.index + 1
    }
} else {
    this.sequenceIndex = 0
    this.state = State.InDeclaration
    this.stateInDeclaration(c) // Reconsume the character
}
}

```

1. 判断当前字符是否对应的上sequenceIndex的字符
2. sequenceIndex自增
3. 判断是否到长度上限,如果到就处理
4. 这里是将sequence提前设置为end,其他地方也有这样处理

> 就这么简单

#### 0.3 fastForwardTo/peek

这机制是我没想到的,快速遍历到某个指定的字符

由于index和buffer都是处理类Tokenizer的属性,所以可以用方法快速遍历到想要的字符

也可以用peek快速查看下一个字符

### 1. 背景介绍

`baseParse`是vue compiler-core的parse.ts中的核心API之一,其解析代码后返回一个RootNode,其中tokenizer.parse是解析的核心代码,该代码**没有返回值**,直接修改baseParse中的root,具体看看如何实现的

### 2. 大体介绍

方法签名:

```js
// 输入vue sfc代码即可
public parse(input: string): void
```

* 以**字符**为单位进行遍历
* 使用enum定义状态,switch case进行判断,初始状态为State.Text
* 全局定义**sectionStart**,记录本次行为的开始位置

调用像这样,每个State一个函数:

```js
switch (this.state) {
    case State.Text: {
        this.stateText(c)
        break
    }
    case State.InterpolationOpen: {
        this.stateInterpolationOpen(c)
        break
    }
```

### 3. 状态转换

vue开发者将常用状态放在case的前端,提升效率

>  注意到vue开发者使用的trick有时就是在状态转换完成时立刻进行一次对应状态的调用

#### 3.1 Text

Text是初始状态,具体状态转换行为:

* 遇到`<`后,执行`onText`进行处理
  * 无条件转换到`BeforeTagName`
  * 重设sectionStart为index
* 在非浏览器状态下遇到`&`(实体&xxx;)
  * 使用baseState属性记录先前的状态,进入`InEntity`,设置解码模式,初始化内容(略写)
* 在非v-pre(不处理)的情况下遇到`{`({{}}形态插值)
  * 进入`InterpolationOpen`状态
  * 执行`stateInterpolationOpen`的部分逻辑(这里不重要) 

##### OnText处理

输入: 读到`<`前的的Text串(从sectionStart到index的范围)

处理:

1. 如果是浏览器,处理(如果是tag,则非style和script)中的实体,解码
2. 从栈中取出父节点(如果没有则根节点),
3. 看代码

```js
if (lastNode && lastNode.type === NodeTypes.TEXT) {
    // merge
    // 父节点,加到content末尾
    lastNode.content += content
    // loc记录节点内容的末尾和开始,这里只更新末尾
    setLocEnd(lastNode.loc, end)
} else {
    // 根节点,创建新的children node,
    parent.children.push({
        type: NodeTypes.TEXT,
        content,
        // 这里记录完整的末尾和开始
        loc: getLoc(start, end),
    })
}
```

#### 3.2 InterpolationOpen

Interpolation就是{{}}插值行为,这个delimiter都是可调的,不过一般不调

Open状态就是记录起始{{的过程

1. 记录一个delimiterIndex,在delimiter数组中一个个对照

2. 如果对不上,就检查inRCData,如果是就转换为`RCData`状态(蒙在鼓里); 不然回到`Text`状态

> 也就是之前是RCData就回RCData,之前是text就回text

3. 如果对上了且已经记录到最后一个delimiter,就

* 先把在第一个delimiter之前的text都用onText记下来(在这里记录是因为之前不确定是不是delimiter)
* 状态转换为`Interpolation`
* 重设sectionStart为**第一个delimiter开始的位置**

#### 3.3 Interpolation

不记录,只关心是否到达DelimiterClose

如果到达,则初始化Index,进入`InterpolationClose`,调用一次stateInterpolationClose(计数)

#### 3.4 InterpolationClose

类似Open

如果对不上Close中的字符,就原地返回Interpolation

如果对上所有字符,就调用`oninterpolation`

##### oninterpolation

传入从{{到}}(两端包含)的起始末尾(end+1)位置

1. 如果是v-pre,则以onText形式处理

2. 遍历括号内的内容前首先排除两端空白,并解码entity(检查是否有&)

```js
let innerStart = start + tokenizer.delimiterOpen.length
let innerEnd = end - tokenizer.delimiterClose.length
while (isWhitespace(currentInput.charCodeAt(innerStart))) {
  innerStart++
}
while (isWhitespace(currentInput.charCodeAt(innerEnd - 1))) {
  innerEnd--
}
```

```js
export function isWhitespace(c: number): boolean {
  return (
    c === CharCodes.Space ||
    c === CharCodes.NewLine ||
    c === CharCodes.Tab ||
    c === CharCodes.FormFeed ||
    c === CharCodes.CarriageReturn
  )
}
```

3. 将节点放到树中

用了一种比onText强一点的方式

```js
function addNode(node: TemplateChildNode) {
  ;(stack[0] || currentRoot).children.push(node)
}
```

这是内容:

指定type,获取loc,createExp比较复杂,看一下

```js
addNode({
    type: NodeTypes.INTERPOLATION,
    content: createExp(exp, false, getLoc(innerStart, innerEnd)),
    loc: getLoc(start, end),
})
```

##### createExp(插值限定介绍)

方法签名

```js
function createExp(
  content: SimpleExpressionNode['content'], // 内容,string
  isStatic: SimpleExpressionNode['isStatic'] = false, // 
  loc: SourceLocation, //位置
  constType: ConstantTypes = ConstantTypes.NOT_CONSTANT,
  parseMode = ExpParseMode.Normal,
)
```

返回一个SimpleExpressionNode

首先根据内容封装一个对象,如果内容是简单标识符就直接返回,不然则用Babel解析成表达式AST Node,放于exp.ast中再返回

#### 3.5 InEntity(略过)

这是所有状态中最后判断的一种

先看进入Entity的处理:

```js
private startEntity() {
    // InEntity只在非browser中发生,盲猜browser会留着给浏览器解析
  if (!__BROWSER__) {
    this.baseState = this.state
    this.state = State.InEntity
    this.entityStart = this.index
    this.entityDecoder!.startEntity(
      this.baseState === State.Text || this.baseState === State.InRCDATA
        ? DecodingMode.Legacy
        : DecodingMode.Attribute,
    )
  }
}
```

后面不看了,Entity的处理也是一个有限状态自动机,比较复杂,但不常用

并且实际上没有记录任何东西,而是检测到结束后就跳过了

#### 3.6 BeforeTagName

> 由于sectionStart不会动,大多数处理直接转阶段,到最后用sectionStart作为起始来收集即可

* 遇到`!`,注释,进`BeforeDeclaration`,设置index+1为sectionstart
* 遇到`?`,可能是特殊处理,进`InProcessingInstruction`,sectionStart设置为index+1
* 遇到字母:
  * 如果Mode是Base(即非html,SFC),直接进入`InTagName`
  * 如果是SFC的根节点,会直接进`InSFCRootTagName`(检测是template或者别的)
  * 如果是HTML,
    * 如果是`t`(\<title\>, \<textarea\>,也就是RCData),进入`BeforeSpecialT`
    * 如果是`s`(script),进入`BeforeSpecialS`
    * 其余进入`InTagName`
  * 如果是`/`,进入`BeforeClosingTagName`
  * 其余情况,回到`Text`,当场进行一次处理

#### 3.7 InSFCRootTagName

```typescript
private stateInSFCRootTagName(c: number): void {
    if (isEndOfTagSection(c)) {
        const tag = this.buffer.slice(this.sectionStart, this.index)
        if (tag !== 'template') {
            this.enterRCDATA(toCharCodes(`</` + tag), 0)
        }
    	this.handleTagName(c)
    }
}
```

读到TagName的末尾符才处理(>或者/或者空白)

直接看如何进入RCDATA,与其描述不如写在这,后面肯定用的上;让我疑惑的是这里为什么是RCDATA,是不是RCDATA和RAWDATA共用一个标志位

```js
public enterRCDATA(sequence: Uint8Array, offset: number): void {
    // 补充: 从tagname看来,这方法就是用来匹配一个sequence的适配函数,比如script从r开始匹配
    this.inRCDATA = true
    this.currentSequence = sequence
    this.sequenceIndex = offset
}
```

handleTagName: 

1. 调用全局处理:

`currentOpenTag`是一个parse.js文件中的临时变量,承担的工作是作为记录tag的node

在这里就是进行初始化了

类型是ELEMENT

```js
onopentagname(start, end) {
  const name = getSlice(start, end)
  currentOpenTag = {
    type: NodeTypes.ELEMENT,
    tag: name,
    ns: currentOptions.getNamespace(name, stack[0], currentOptions.ns),
      // namespace,一般指向Namespaces.HTML,根据options改变
    tagType: ElementTypes.ELEMENT, // will be refined on tag close
    props: [],
    children: [],
    loc: getLoc(start - 1, end),
    codegenNode: undefined,
  }
},
```

2. 转换到`BeforeAttrName`状态

3. 取消sectionStart,设置为-1

#### 3.8 BeforeDeclaration

这是在读到`<!`之后的状态

* 如果读到`[`,就进入CDATA(xml的一种标记),状态为`CDATASequence`,设置sequenceIndex为0
* 读到`-`,进入`BeforeComment`
* 其他进入`InDeclaration`

#### 3.9 InCommentLike

直接fastforward找到end,然后设置sequence开始匹配,匹配完完整的end以后,进行处理

如果匹配到一半发现不对,就重设sequenceIndex

处理comment(有处理cdata,但忽略),然后返回`Text`

```js
oncomment(start, end) {
    if (currentOptions.comments) {
        addNode({
            type: NodeTypes.COMMENT,
            content: getSlice(start, end),
            loc: getLoc(start - 4, end + 3),
        })
    }
},
```

#### 3.10 BeforeSpecialS/T

两个逻辑一样,如果匹配到style/script/title/textarea的一个字符就进下一个阶段`SpecialStartSequence`

用enterRCDATA记录sequence匹配状态

不然回`InTagName`

#### 3.11 SpecialStartSequence

```ts
const isEnd = this.sequenceIndex === this.currentSequence.length
const isMatch = isEnd
    ? // If we are at the end of the sequence, make sure the tag name has ended
          isEndOfTagSection(c)
    : // Otherwise, do a case-insensitive comparison
    (c | 0x20) === this.currentSequence[this.sequenceIndex]

```

先观察是否匹配到末尾,如果是,就匹配end(>,/,空白),不然就匹配(无视大小写)

> c | 0x20 对字母专用的转小写

> 也就是说,这个前两个字母不能大写,后面可以?(注意,仅限html部分,没试过)

```ts
if (!isMatch) {
    this.inRCDATA = false
} else if (!isEnd) {
    this.sequenceIndex++
    return
}
this.sequenceIndex = 0
this.state = State.InTagName
this.stateInTagName(c)
```

由于外部在循环,这里没匹配到end直接进入循环,而没匹配到match则直接跟结束一样处理,无论如何都会回到`InTagName`

#### 3.12 InTagName

只匹配end,无论如何都会进入`BeforeAttrName`

匹配完记录一个tagnode

```js
currentOpenTag = {
    type: NodeTypes.ELEMENT,
    tag: name,
    ns: currentOptions.getNamespace(name, stack[0], currentOptions.ns),
    tagType: ElementTypes.ELEMENT, // will be refined on tag close
    props: [],
    children: [],
    loc: getLoc(start - 1, end),
    codegenNode: undefined,
}
```

#### 3.13 BeforeAttrName(主线)

遇到`>`说明收尾了,进行结算,后进入`RCDATA`或者`Text`(按照标志位),重设sectionStart,并且:

##### onopentagend

如果在SFCroot,给currentOpenTag记录一个innerLoc(inner的位置)的start=end+1

* 然后把current node  push进当前父node中

但是push完还能处理,比如

```ts
const { tag, ns } = currentOpenTag!
if (ns === Namespaces.HTML && currentOptions.isPreTag(tag)) {
   inPre++ //用来处理pre(原格式展示)
}
if (currentOptions.isVoidTag(tag)) {
  onCloseTag(currentOpenTag!, end) // 处理自关闭的tag,直接找到>给分配一个loc end,不放栈顶
} else {
  stack.unshift(currentOpenTag!) // 放在栈顶
  if (ns === Namespaces.SVG || ns === Namespaces.MATH_ML) {
    tokenizer.inXML = true
  }
}
currentOpenTag = null // 处理完了,消除
```

##### 其他

匹配到`/`,就进入`InSelfClosingTag`,并且会看一眼后面是不是`>`,不然报错

```ts
this.cbs.onerr(ErrorCodes.UNEXPECTED_SOLIDUS_IN_TAG, this.index)
```

然后是一段比较特殊的代码

```ts
// 这里表达的意思是 在opening中读到</,可以有一种对IDE宽容的做法,就是视作end
// 太特殊了,仅记录在此
else if (c === CharCodes.Lt && this.peek() === CharCodes.Slash) {
    // special handling for </ appearing in open tag state
    // this is different from standard HTML parsing but makes practical sense
    // especially for parsing intermediate input state in IDEs.
    this.cbs.onopentagend(this.index)
    this.state = State.BeforeTagName
    this.sectionStart = this.index
}
```

* 匹配到`=`,报错,其他非空白字符就进handleAttrStart

##### handleAttrStart

如果匹配到`v-`,进入`InDirName`

如果匹配到特殊指令,比如`@(on)`,`./:(bind)`,`#(slot)`,进入`InDirArg`,并处理ondirname

如果没什么特殊的就`InAttrName`

```ts
 c === CharCodes.Dot ||
      c === CharCodes.Colon ||
      c === CharCodes.At ||
      c === CharCodes.Number // 这是井号
```

##### ondirname

```js
   const name =
      raw === '.' || raw === ':'
        ? 'bind'
        : raw === '@'
          ? 'on'
          : raw === '#'
            ? 'slot'
            : raw.slice(2)
```

如果不在v-pre并且没name就报错,如果v-pre并且name没东西(在这里没可能),就加一个正常ATTRIBUTE

```js
currentProp = {
    type: NodeTypes.ATTRIBUTE,
    name: raw,
    nameLoc: getLoc(start, end),
    value: undefined,
    loc: getLoc(start),
}
```

一般情况下,这里的分支都进入这个地方:

```js
currentProp = {
    type: NodeTypes.DIRECTIVE, // 指令
    name, // 功能
    rawName: raw, //原指令
    exp: undefined, //待写
    arg: undefined,
    modifiers: raw === '.' ? [createSimpleExpression('prop')] : [],
    //这里会添加一个NodeTypes.SIMPLE_EXPRESSION的node在其中,暂时没理解
    loc: getLoc(start),
}
```

如果遇到v-pre:

1. 设置inVPre = true

2. ```
   currentVPreBoundary = currentOpenTag
   ```

3. 将所有处理过的节点,DIRECTIVE转回普通attr

#### 3.14 InDirName

* 遇到`=`,或者任何end(/,>,空白),则处理ondirname

并且处理handleAttrNameEnd(转到`AfterAttrName`)

> 这里和InAttrName处理相同

* 遇到`:`,也是ondirname(上一节有),转到`InDirArg`
* 遇到`.`,也ondirname.进`InDirModifier`,modifiers的type是SIMPLE_EXPRESSION

##### handleAttrNameEnd

转到`AfterAttrName`,立即进行一次state处理

进行一个全局处理

```js
onattribnameend(end) {
    const start = currentProp!.loc.start.offset
    const name = getSlice(start, end)
    if (currentProp!.type === NodeTypes.DIRECTIVE) {
        currentProp!.rawName = name
    }
    // check duplicate attrs
    if (
        currentOpenTag!.props.some(
        // 检查是否已经有对应的属性了,有就报错
        p => (p.type === NodeTypes.DIRECTIVE ? p.rawName : p.name) === name,
        )
    ) {
        emitError(ErrorCodes.DUPLICATE_ATTRIBUTE, start)
    }
},
```

#### 3.15 AfterAttrName

(遇到空白会跳过)

遇到`=`,进`BeforeAttrValue`

遇到`/`,`>`,处理onattribend(在最后讲),重设sectionStart,并回到`BeforeAttrName`,立即处理一次

```js
this.cbs.onattribend(QuoteType.NoValue, this.sectionStart)
```

遇到其他非空白字符,说明到下一个attr了,调用onattribend,并handleAttrStart(在BeforeAttrName介绍)

#### 3.16 AttrValue处理

分为 单引号 双引号 和 无引号,各分配了一个状态,不展开

1. 记录引号种类的意义是,fastforwardto另一个引号,然后继续进行

2. 当没有引号时,会处理entity,遇到一些字符时报错,比如`"`,`'`,`=`

处理:

先通过onattribdata将value值和位置记录在变量中

然后进行一次onattribend

回到`BeforeAttrName`状态

##### onattribend

> 在没有v-pre的情况下进行处理

```js
export enum QuoteType {
  NoValue = 0,
  Unquoted = 1,
  Single = 2,
  Double = 3,
}
```

第一步,预处理(设置prop的loc end,转换entity)

如果没有 value,且不是v-pre,直接将currentProp推进currentOpenTag

如果是**ATTRIBUTE**:

1. 预处理(将class中的空白压缩成空格,啥也没有就报错)

2. 

```js
currentProp!.value = {
    type: NodeTypes.TEXT,
    content: currentAttrValue,
    loc:
    quote === QuoteType.Unquoted
    ? getLoc(currentAttrStartIndex, currentAttrEndIndex)
    : getLoc(currentAttrStartIndex - 1, currentAttrEndIndex + 1),
}
```

3. 如果检测到SFC root的template的lang并非html,则进入RCDATA,匹配</template

如果是**DIRECTIVE**:比较复杂,都是伏笔,直接贴代码

```js
// directive
let expParseMode = ExpParseMode.Normal
if (!__BROWSER__) {
    if (currentProp.name === 'for') {
        expParseMode = ExpParseMode.Skip
    } else if (currentProp.name === 'slot') {
        expParseMode = ExpParseMode.Params
    } else if (
        currentProp.name === 'on' &&
        currentAttrValue.includes(';')
    ) {
        expParseMode = ExpParseMode.Statements
    }
}
currentProp.exp = createExp(
    currentAttrValue,
    false,
    getLoc(currentAttrStartIndex, currentAttrEndIndex),
    ConstantTypes.NOT_CONSTANT,
    expParseMode,
)
if (currentProp.name === 'for') {
    // 生成一个记录着两端变量的node
    currentProp.forParseResult = parseForExpression(currentProp.exp)
}
```



##### v-for的parse

```js
// 拆分a of b的regex
export const forAliasRE: RegExp = /([\s\S]*?)\s+(?:in|of)\s+(\S[\s\S]*)/
```

```js
const result: ForParseResult = {
    source: createAliasExpression(RHS.trim(), exp.indexOf(RHS, LHS.length)),
    value: undefined,
    key: undefined,
    index: undefined,
    finalized: false,
}
```

然后用正则一个个填