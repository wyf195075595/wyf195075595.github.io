---
title: 开发WangEditor插件
date: 2024-04-25 08:23:10
tags: 插件
categories: js
---

插件开发请阅读[官方文档](https://www.wangeditor.com/v5/development.html#%E6%B3%A8%E5%86%8C%E6%8F%92%E4%BB%B6%E5%88%B0-wangeditor)，本次示例为 为 编辑器 拓展 [mermaid](https://mermaid.nodejs.cn/intro/) 流程图。本示例参考官方 [公式插件](https://github.com/wangeditor-team/wangEditor-plugin-formula) 代码

![wangEditor-plugin-formula](https://github.com/wangeditor-team/wangEditor-plugin-formula/blob/main/_img/demo.png?raw=true)

<!--more-->

### 目录结构

```
-RemaindFlow

--MyModalMenu.js   #插入流程图弹窗

--editFlow.js	  #流程图点击浮窗编辑

--renderElem.js    #通过json数据渲染“流程图”元素到编辑器

--elemToHtml.js   #生成“流程图”元素的 HTML

--parseElemHtml.js #解析 HTML 字符串，生成“流程图”元素 json 数据


--withAttachment.js#重写元素在编辑器中的inline【是否块级元素】 和 void【为空判断】

--index.js		  #导出整体模块

--registerCustomElem.js #没有用
```

### 依赖

```json
"katex": "^0.16.10",
"mermaid": "^10.9.1",
"@wangeditor/editor": "^5.1.23",
"@wangeditor/editor-for-vue": "^5.1.12",

# 将 json 字符串渲染成 dom
yarn add snabbdom --peer
```

### 代码

**MyModalMenu.js**

```js
export default class MyModalMenu {
	// JS 语法

	constructor() {
		this.title = 'My menu';
		// this.iconSvg = '<svg >...</svg>'
		this.tag = 'button';
		this.showModal = true;
		this.modalWidth = 300;
	}

	// 菜单是否需要激活（如选中加粗文本，“加粗”菜单会激活），用不到则返回 false
	isActive(editor) {
		// JS 语法
		return false;
	}

	// 获取菜单执行时的 value ，用不到则返回空 字符串或 false
	getValue(editor) {
		// JS 语法
		return '';
	}

	// 菜单是否需要禁用（如选中 H1 ，“引用”菜单被禁用），用不到则返回 false
	isDisabled(editor) {
		// JS 语法
		return false;
	}

	// 点击菜单时触发的函数
	exec(editor, value) {
		// JS 语法
		// Modal menu ，这个函数不用写，空着即可
	}

	// 弹出框 modal 的定位：1. 返回某一个 SlateNode； 2. 返回 null （根据当前选区自动定位）
	getModalPositionNode(editor) {
		// JS 语法
		return null; // modal 依据选区定位
	}

	// 定义 modal 内部的 DOM Element
	getModalContentElem(editor) {
		// JS 语法
		let content = document.createElement('div');
		let btn = document.createElement('button');
		btn.innerText = '确认';
		let text = document.createElement('textarea');

		content.appendChild(text);
		content.appendChild(btn);
		btn.onclick = () => {
			const node = { type: 'remaindFlow', code: text.value, children: [{ text: '' }] };
			editor.restoreSelection(); // 恢复选区
			editor.insertNode(node);
			// mermaid.initialize({ startOnLoad: true });
			// mermaid.init();
			editor.insertBreak();
			editor.move(1);
		};
		return content; // 返回 DOM Element 类型

		// PS：也可以把 $content 缓存下来，这样不用每次重复创建、重复绑定事件，优化性能
	}
}

```

**editFlow.js**

还没写好测试，跟**MyModalMenu.js**结构差不多	

```js
import { 
    DomEditor,
    SlateTransforms,
    SlateRange,
    t,
    genModalTextareaElems,
    genModalButtonElems, 
} from '@wangeditor/editor';
class EditFlowMenu { 
    constructor() {
		this.title = '编辑流程';
		// this.iconSvg = '<svg >...</svg>'
		this.tag = 'button';
		this.showModal = true;
		this.modalWidth = 500;
		this.modalHeight = 400;
        this.buttonId = String(Date.now());
        this.target = null;// 编辑的目标
	}
    getSelectedElem(editor){
        const node = DomEditor.getSelectedNodeByType(editor, 'remaindFlow')
        console.log("getSelectedElem", node);
      if (node == null) return null
      this.target = node;
      return node
    }
  
    /**
     * 获取公式 value
     * @param editor editor
     */
    getValue(editor) {
      const flowElem = this.getSelectedElem(editor)
      console.log("getValue:", flowElem);
      if (flowElem) {
        return flowElem.code || ''
      }
      return ''
    }
  
    isActive(editor) {
      // 无需 active
      return false
    }
  
    exec(editor, value) {
      // 点击菜单时，弹出 modal 之前，不需要执行其他代码
      // 此处空着即可
    }
  
    isDisabled(editor) {
      const { selection } = editor
      if (selection == null) return true
      if (SlateRange.isExpanded(selection)) return true // 选区非折叠，禁用
  
      // 未匹配到 flow node 则禁用
      const flowElem = this.getSelectedElem(editor)
      if (flowElem == null) return true
  
      return false
    }
  
    // modal 定位
    getModalPositionNode(editor) {
      return this.getSelectedElem(editor)
    }
  
    getModalContentElem(editor) {
      const { textareaId, buttonId } = this
      console.log("getModalContentElem!");
      const [textareaContainerElem, textareaElem] = genModalTextareaElems(
        t('编辑流程'),
        textareaId,
        t(`flowchart LR
        A[节点A] -->|开始| B(节点B)
        B --> C(节点C)`)
      )
      const $textarea = textareaElem
      const [buttonContainerElem] = genModalButtonElems(buttonId, t('确认'))
  
      if (this.$content == null) {
        // 第一次渲染
        const $content = document.createElement('div')
        console.log($content);
        // 绑定事件（第一次渲染时绑定，不要重复绑定）
        $content.addEventListener('click', e => {
          e.preventDefault();
          if( e.target.id === buttonId) {
            // 点击确定按钮 `#${buttonId}`
            const value = String($content.querySelector(`#${textareaId}`).value).trim()
            this.updateflow(editor, value)
            editor.hidePanelOrModal() // 隐藏 modal
          }
        })
  
        // 记录属性，重要
        this.$content = $content
      }
  
      const $content = this.$content
      $content.innerHTML = ''; // 先清空内容
  
      // append textarea and button
      $content.append(textareaContainerElem)
      $content.append(buttonContainerElem)
  
      // 设置 input val
      const value = this.getValue(editor)
      $textarea.value = value
  
      // focus 一个 input（异步，此时 DOM 尚未渲染）
      setTimeout(() => {
        $textarea.focus()
      })
  
      return $content
    }
  
    updateflow(editor, value) {
      if (!value) return
      console.log("updateflow:", this.target);
      // 还原选区
      editor.restoreSelection()
  
      if (this.isDisabled(editor)) return
  
      const selectedElem = this.getSelectedElem(editor)
      if (selectedElem == null) return
  
      const path = DomEditor.findPath(editor, selectedElem)
      const props = { code: value }
      SlateTransforms.setNodes(editor, props, { at: path })
    }
}

export default EditFlowMenu
```

**renderElem.js**

```js
import { h } from 'snabbdom';
import { DomEditor } from '@wangeditor/editor';
/**
 * 渲染“附件”元素到编辑器
 * @param elem 附件元素，即上文的 myResume
 * @param children 元素子节点，void 元素可忽略
 * @param editor 编辑器实例
 * @returns vnode 节点（通过 snabbdom.js 的 h 函数生成）
 */
function renderFlow(elem, children, editor) {
	// JS 语法
	console.log("renderFlow:", elem);
	// 获取“附件”的数据，参考上文 myResume 数据结构
	const { code } = elem;

	// 当前节点是否选中
	const selected = DomEditor.isNodeSelected(editor, elem);
	const formulaVnode = h(
		'w-e-flow-card',
		{
			dataset: { code },
		},
		null
	)

	// 构建容器 vnode
	const containerVnode = h(
		'pre',
		{
			props: {
				contentEditable: false, // 不可编辑
				className: 'mermaid'
			},
			style: {
				display: 'inline-block', // inline
				marginLeft: '3px',
				marginRight: '3px',
				border: selected // 选中/不选中，样式不一样
					? '2px solid var(--w-e-textarea-selected-border-color)'
					: '2px solid transparent',
				borderRadius: '3px',
				padding: '3px 3px',
			},
		},
		[code]
	)

	return containerVnode
}
export default {
	type: 'remaindFlow', // 节点 type ，重要！！！
	renderElem: renderFlow,
};

```

**elemToHtml.js**

```js
/**
 * 生成“附件”元素的 HTML
 * @param elem 附件元素，即上文的 myResume
 * @param childrenHtml 子节点的 HTML 代码，void 元素可忽略
 * @returns “附件”元素的 HTML 字符串
 */
function flowToHtml(elem, childrenHtml) {
	// JS 语法
	console.log('flowToHtml:', elem);
	// 获取附件元素的数据
	const { code } = elem;

	// 生成 HTML 代码
	//
	return `<span class="mermaid" data-w-e-type="remaindFlow" data-w-e-is-void data-w-e-is-inline data-code="${code}" ></span>`;
}
export default {
	type: 'remaindFlow', // 节点 type ，重要！！！
	elemToHtml: flowToHtml,
};
```

**parseElemHtml.js**

```js
/**
 * 解析 HTML 字符串，生成“附件”元素
 * @param domElem HTML 对应的 DOM Element
 * @param children 子节点
 * @param editor editor 实例
 * @returns “附件”元素，如上文的 myResume
 */
function parseFlowHtml(domElem, children, editor) {
	// JS 语法
	console.log("parseFlowHtml:", domElem);

	// 从 DOM element 中获取“附件”的信息
	const code = domElem.getAttribute('data-code') || ''; //domElem.innerText
	// 生成“附件”元素（按照此前约定的数据结构）
	const myResume = {
		type: 'remaindFlow',
		code,
		children: [{ text: '' }], // void node 必须有 children ，其中有一个空字符串，重要！！！
	};

	return myResume;
}

export default {
	selector: 'span[data-w-e-type="remaindFlow"]',
	parseElemHtml: parseFlowHtml,
};

```

**withAttachment.js**

```js
import { DomEditor } from '@wangeditor/editor';

function withAttachment(editor) {
	const { isInline, isVoid } = editor;
	const newEditor = editor;

	// 重写 isInline
	newEditor.isInline = (elem) => {
		const type = DomEditor.getNodeType(elem);
		if (type === 'remaindFlow') {
			return true;
		}

		return isInline(elem);
	};

	// 重写 isVoid
	newEditor.isVoid = (elem) => {
		const type = DomEditor.getNodeType(elem);
		if (type === 'remaindFlow') {
			return true;
		}

		return isVoid(elem);
	};

	return newEditor;
}

export default withAttachment;

```

**index.js**

```js
import RemaindFlow from './MyModalMenu';
import renderElemConf from './renderElem';
import elemToHtmlConf from './elemToHtml';
import parseHtmlConf from './parseElemHtml';
import withAttachment from './withAttachment';
import editFlowMenu from './editFlow';
const flowConf = {
	key: 'remaindFlow', // 定义 menu key ：要保证唯一、不重复（重要）
	factory() {
		return new RemaindFlow(); // 把 `YourMenuClass` 替换为你菜单的 class
	},
};
const editFlowMenuConf = {
	key: 'editRemaindFlow', // 定义 menu key ：要保证唯一、不重复（重要）
	factory() {
		return new editFlowMenu(); // 把 `YourMenuClass` 替换为你菜单的 class
	},
}
export default {
	menus: [flowConf],
	editorPlugin: withAttachment,
	renderElems: [renderElemConf],
	elemsToHtml: [elemToHtmlConf],
	parseElemsHtml: [parseHtmlConf],
};

```

**registerCustomElem.js**

这个方案失败了，原因是 **mermaid.render** 方法会使用document查找获取元素，但是影子dom中元素无法通过 document访问。导致 流程图，渲染不上去。像 marked 那种方式渲染的才方便封装 

```js
let newText = marked.render(text);
```



```js
import mermaid from "mermaid";

class WangEditorFlowCard extends HTMLElement{
    constructor() {
        super()
        const shadow = this.attachShadow({ mode: 'open' })
        this.document = shadow.ownerDocument;
        const pre = this.document.createElement('pre');
        pre.style.display = 'inline-block'
        pre.classList.add('mermaid')
        pre.id = 'mermaid';
        shadow.appendChild(pre);
        this.pre = pre;
    }
    // 监听的 attr
    static get observedAttributes() {
        return ['data-code']
    }
    attributeChangedCallback(name, oldValue, newValue) {
        console.log("attributeChangedCallback:", name, oldValue, newValue);
        if (name === 'data-code') {
            if (oldValue == newValue) return
            this.render(newValue || '')
        }
    }

    render(value) {
        const shadowRoot = this.shadowRoot;
        // 设置 Mermaid 图表代码  
        const element = shadowRoot.getElementById('mermaid');  
        element.textContent = value;
        console.log(element.getBoundingClientRect());
        if (element) {  
            console.log("初始化", mermaid);
            mermaid.initialize({ startOnLoad: false });
            mermaid.render(element.id, value, element);
        }
    }
}

if (!window.customElements.get('w-e-flow-card')) {
    window.customElements.define('w-e-flow-card', WangEditorFlowCard)
  }
```



### 引入使用

在 main.js 中引用

```js
// 注册 wangEditor 插件
import { Boot } from '@wangeditor/editor';
// import '@/plugins/RemaindFlow/registerCustomElem.js';

import flowModuleConf from '@/plugins/RemaindFlow';
Boot.registerModule(flowModuleConf);
```



编辑器配置

```js
const toolbarConfig = {
	insertKeys: {
        index: 1, // 插入的位置，基于当前的 toolbarKeys
        keys: ['remaindFlow'],
    },
};

const editorConfig = {
	placeholder: '请输入内容...',
	hoverbarKeys: {
        formula: {
            menuKeys: ['editFormula'], // “编辑公式”菜单
        },
	}
};
```

编辑器更新内容时初始化流程图, 

```js
import mermaid from 'mermaid';

# @onChange="handleChanged"
const handleChanged = (editor) => {
	console.log(editor);
	mermaid.initialize({ startOnLoad: true });
	mermaid.init();
};
```

为啥不在插件代码中写这部分逻辑呢，formula 插件 

katex 可以直接渲染这个元素为 公式，所以使用的自定义元素在影子dom中渲染，但 mermaid 不能这么搞，他对影子DOM无效。暂时没想到更优雅的姿势

```js
katex.render(value, this.span, {
  throwOnError: false,
})
```

