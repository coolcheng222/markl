# Compiler-SFC

SFC stands for Single File Component. From now on, I will write about my thoughts and learnings from reading the source code.

## 一. Learn from Readme.md

### 1. Facade Mode

为了保持上下文独立性,方便热更新,

这边的API采用了Facade模式,就是提供一个"门面",用来与内部的模块交互

官方附图:

```
                                  +--------------------+
                                  |                    |
                                  |  script transform  |
                           +----->+                    |
                           |      +--------------------+
                           |
+--------------------+     |      +--------------------+
|                    |     |      |                    |
|  facade transform  +----------->+ template transform |
|                    |     |      |                    |
+--------------------+     |      +--------------------+
                           |
                           |      +--------------------+
                           +----->+                    |
                                  |  style transform   |
                                  |                    |
                                  +--------------------+
```

然后是官方提供的参考代码,可以看到这个模式其实就是把三个模块合成一个对外暴露的接口.

```js
// main script
import script from '/project/foo.vue?vue&type=script'
// template compiled to render function
import { render } from '/project/foo.vue?vue&type=template&id=xxxxxx'
// css
import '/project/foo.vue?vue&type=style&index=0&id=xxxxxx'

// attach render function to script
script.render = render

// attach additional metadata
// some of these should be dev only
script.__file = 'example.vue'
script.__scopeId = 'xxxxxx'

// additional tooling-specific HMR handling code
// using __VUE_HMR_API__ global

export default script
```

这只是一个概念,具体还是得看实现方式

### 2. Workflow

1. 使用`parse`函数将文件转换为descriptors,然后根据这个生成上述的Facade 模块
2. `Script tranform`: 调用`compileScript`,能够处理script代码块和css注入块
3. template: `compileTemplate`将template转换为render function代码
4. style: `compileStyle`将css代码块转换,处理css变量

### 3. 加一个示例

我更喜欢具体的描述,所以从测试代码中随便摘了一段出来(script),就能看清工作流了

```js
export function compileSFCScript(
  src: string,
  options?: Partial<SFCScriptCompileOptions>,
  parseOptions?: SFCParseOptions,
): SFCScriptBlock {
    // parse
  const { descriptor, errors } = parse(src, parseOptions)
  if (errors.length) {
    console.warn(errors[0])
  }
    // compileScript
  return compileScript(descriptor, {
    ...options,
    id: mockId,
  })
}
```

## 二. parse

### 1. 方法签名观察

方法签名: 

```js
export function parse(
  source: string, // 要转换的代码
  options: SFCParseOptions = {},
): SFCParseResult
```

```ts
// 不太懂,等看后面应该就懂了
export interface SFCParseOptions {
  filename?: string
  sourceMap?: boolean
  sourceRoot?: string
  pad?: boolean | 'line' | 'space'
  ignoreEmpty?: boolean
  compiler?: TemplateCompiler
  templateParseOptions?: ParserOptions
}
export interface SFCParseResult {
  descriptor: SFCDescriptor
  errors: (CompilerError | SyntaxError)[]
}
```

```ts
export interface SFCDescriptor {
  filename: string
  source: string
  // 这些代码类型内部值得阅读,不过不在这里,等到了对应模块肯定能读到
  template: SFCTemplateBlock | null
  script: SFCScriptBlock | null
  scriptSetup: SFCScriptBlock | null
  styles: SFCStyleBlock[]
  customBlocks: SFCBlock[]
  cssVars: string[]
  /**
   * whether the SFC uses :slotted() modifier.
   * this is used as a compiler optimization hint.
   * 一个优化的小东西,看后续
   */
  slotted: boolean

  /**
   * compare with an existing descriptor to determine whether HMR should perform
   * a reload vs. re-render.
   * 检查是否需要热更新 是重载还是重新渲染
   * Note: this comparison assumes the prev/next script are already identical,
   * and only checks the special case where <script setup lang="ts"> unused import
   * pruning result changes due to template changes.
   */
  shouldForceReload: (prevImports: Record<string, ImportBinding>) => boolean
}
```

> reload: 重新像浏览器发送请求
>
> re-render: 在页面中重新进行渲染,无需与互联网服务器交互

### 2. 函数体观察: 准备

1. 缓存取出

```js
// 这个函数就是拼串,source+json版option
const sourceKey = genCacheKey(source, {
    ...options,
    compiler: { parse: options.compiler?.parse },
              })
const cache = parseCache.get(sourceKey)
if (cache) {
    return cache
}
```

2. 初始化,解构

```js

const {
    sourceMap = true,
    filename = DEFAULT_FILENAME,
    sourceRoot = '',
    pad = false,
    ignoreEmpty = true,
    compiler = CompilerDOM,
    templateParseOptions = {},
} = options

const descriptor: SFCDescriptor = {
    filename,
    source,
    template: null,
    script: null,
    scriptSetup: null,
    styles: [],
    customBlocks: [],
    cssVars: [],
    slotted: false,
    shouldForceReload: prevImports => hmrShouldReload(prevImports, descriptor),
}

const errors: (CompilerError | SyntaxError)[] = []
```

### 3. AST解析

1. 准备阶段: 无视非Element节点,根据option忽视空白节点(并且没有src属性)

#### 3.1 生成Block(不复杂,仅参考)

```js
function createBlock(
  node: ElementNode,
  source: string,
  pad: SFCParseOptions['pad'],
): SFCBlock 
```

初始化:

```js
const type = node.tag //tag名
const loc = node.innerLoc!
const attrs: Record<string, string | true> = {} //后续处理
const block: SFCBlock = {
    type,
    content: source.slice(loc.start.offset, loc.end.offset),
    loc,
    attrs,
}
```

加入attr

```js
node.props.forEach(p => {
    if (p.type === NodeTypes.ATTRIBUTE) {
        const name = p.name
        attrs[name] = p.value ? p.value.content || true : true
        if (name === 'lang') {
            block.lang = p.value && p.value.content
        } else if (name === 'src') {
            block.src = p.value && p.value.content
        } else if (type === 'style') {
            if (name === 'scoped') {
                ;(block as SFCStyleBlock).scoped = true
            } else if (name === 'module') {
                ;(block as SFCStyleBlock).module = attrs[name]
            }
        } else if (type === 'script' && name === 'setup') {
            ;(block as SFCScriptBlock).setup = attrs.setup
        }
    }
})


```

block.map是后续进行的从块中代码到源代码的位置映射

---------

对于template,生成Block和rootNode(无src)

> template是可以用src导入html的

```js
{
    type: NodeTypes.ROOT,
    source,
    children,
    helpers: new Set(),
    components: [],
    directives: [],
    hoists: [],
    imports: [],
    cached: [],
    temps: 0,
    codegenNode: undefined,
    loc: locStub,
  }
```

对于script,检查是否只有一个原版+一个setup,不然多了报错

对于style,没什么特殊处理

三者都压进**descriptor.template/script/scriptSetup/style**(style可以多个,是数组),如果template和script/scriptSetup都没有,就报错

* 小型细节:当setup出现时,script都不能有src

```js
  if (descriptor.scriptSetup) {
    if (descriptor.scriptSetup.src) {
      errors.push(
        new SyntaxError(
          `<script setup> cannot use the "src" attribute because ` +
            `its syntax will be ambiguous outside of the component.`,
        ),
      )
      descriptor.scriptSetup = null
    }
    if (descriptor.script && descriptor.script.src) {
      errors.push(
        new SyntaxError(
          `<script> cannot use the "src" attribute when <script setup> is ` +
            `also present because they must be processed together.`,
        ),
      )
      descriptor.script = null
    }
  }
```

#### 对style中v-bind的处理

对于style的内容,去掉注释,然后正则匹配到v-bind(,记录start到end,然后push进一个[]再返回

并会将包含::v-slotted的额外放进descriptor.slotted

## 三. compileScript

第一步,构造context

* 设置一些参数
* 使用babel parser生成ast

```js
const ctx = new ScriptCompileContext(sfc, options)
```

