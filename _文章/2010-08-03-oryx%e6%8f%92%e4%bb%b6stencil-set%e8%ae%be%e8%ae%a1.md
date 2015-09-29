---
ID: 220
post_title: Oryx插件stencil set设计
author: riv
post_date: 2010-08-03 18:13:21
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2010/08/03/oryx%e6%8f%92%e4%bb%b6stencil-set%e8%ae%be%e8%ae%a1/
published: true
---
<img alt="" src="http://code.google.com/p/oryx-editor/logo?cct=1276155773" title="oryx" class="alignleft" width="60" height="55" />Oryx采用开放的插件设计，界面插件称为stencil set，可以方便的添加一个stencil set。Nicolas Peters有一篇学士论文Oryx Stencil Set Specification（和他的论文相比，我当年的论文简直是狗屁），中间不但讲述了规范，还有一个实际的例子。论文可以从下面网址下到http://code.google.com/p/oryx-editor/downloads/list。

设计一套新的stencil set最好的方法是从Oryx默认提供的workflownets开始，拷贝修改。

Oryx将一个图形模块称为一个stencil，为一种模型设计的一套图形模块称为stencil set。Oryx引用的方式为从editor.xhtml->json->view&icon。editor.xhtml引用哪个json文件会被使用；json文件中定义有哪些图形模块，图形模块如何显示（在view和icon中定义），每个图形模块有哪些属性，图像模块之间怎么连接，模型模块怎么包含等等。

在主页xhtml文件中指定哪个json文件会用到。
      <script type='text/javascript'>
          function onOryxResourcesLoaded(){
            new ORYX.Editor({
              id: 'oryx-canvas123',
              stencilset: {
                url: ORYX.CONFIG.ROOT_PATH + 'stencilsets/xflow/sflow.json'
              }
          });
        }
      </script>

在json文件的开头定义基本属性。
	"title":"xFlow",
	"namespace":"http://www.xflow.org#",
	"description":"X Flow",

在json的stencils中定义每个图形模块。Workflow Net的json文件中定义了一个diagram的，用于定义主界面的属性定义。
	<li>type属性可以是node，普通的节点；也可以是edge，用于连接节点。</li><li>
id属性在一个stencil set中必须唯一。</li><li>
view属性用于如何显示SVG矢量图。w3schools的教程看来之后基本就会了http://www.w3schools.com/svg/default.asp。</li><li>
icon属性用于显示图标。</li><li>
roles属性用于节点之间如何连接。在后面rules中说明。</li><li>
properties用于指定节点有哪些属性。</li>

需要注意的是，stencils中定义的顺序就是实际显示的顺序。containmentRules中定义的顺序和显示顺序没有关系。

connectionRules用于指定如何连接。下面的例子中，所有role为nodeSource的节点可以连接到role为nodeTarget的节点
		"connectionRules": [
			{
				"role":"controlflow",
				"connects": [
					{
						"from":"nodeSource",
						"to":"nodeTarget"
					},
				]
			}
