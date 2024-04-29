### Zotero编辑器设置
#### 按笔记顺序生成注释笔记
[[📝 摘录笔记]]
修改条目：extensions.zotero.annotations.noteTemplates.highlight

```
{{ if color == '#ff6666'}} <b>🔑</b><blockquote><p><span style="background-color: #ff666680">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #关键内容 #{{tags join=' #'}}</p></blockquote> {{elseif color == '#a28ae5'}} <b>📝生词句型</b><blockquote><p><span style="background-color: #a28ae580">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #生词句型 #{{tags join=' #'}}</p></blockquote> {{elseif color == '#2ea8e5'}} <b>🔏</b><blockquote><p><span style="background-color: #2ea8e580">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #方法工具 #{{tags join=' #'}}</p></blockquote> {{elseif color == '#ffd400'}} <b>💡</b><blockquote><p><span style="background-color: #ffd40080">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #观点论点 #{{tags join=' #'}}</p></blockquote> {{elseif color == '#5fb236'}} <b>📚</b><blockquote><p><span style="background-color: #5fb23680">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #背景综述 #{{tags join=' #'}}</p></blockquote> {{elseif color == '#f19837'}} <b>🔍</b><blockquote><p><span style="background-color: #f1983780">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #分析解释 #{{tags join=' #'}}</p></blockquote> {{elseif color == '#e56eee'}} <b>📌</b><blockquote><p><span style="background-color: #e56eee80">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #结论讨论 #{{tags join=' #'}}</p></blockquote> {{elseif color == '#aaaaaa'}} <b>💣</b><blockquote><p><span style="background-color: #aaaaaa80">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #局限问题 #{{tags join=' #'}}</p></blockquote>{{else}}<p>{{highlight}} {{citation}} {{comment}}</p>{{end}}
```

**修改简化版：**
```
{{ if color == '#ff6666'}} <blockquote><p><span style="background-color: #ff666680">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#a28ae5'}} <blockquote><p><span style="background-color: #a28ae580">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#2ea8e5'}} <blockquote><p><span style="background-color: #2ea8e580">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#ffd400'}} <blockquote><p><span style="background-color: #ffd40080">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#5fb236'}} <blockquote><p><span style="background-color: #5fb23680">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#f19837'}} <blockquote><p><span style="background-color: #f1983780">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#e56eee'}} <blockquote><p><span style="background-color: #e56eee80">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#aaaaaa'}} <blockquote><p><span style="background-color: #aaaaaa80">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote>{{else}}<p>{{highlight}} {{citation}} {{comment}}</p>{{end}}
```
#### 按颜色分类生成注释笔记 （Zotero7用不了）
Step1 修改条目：extensions.zotero.annotations.noteTemplates.highlight
```
{{ if color == '#ff6666'}} <blockquote><p><span style="background-color: #ff666680">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#a28ae5'}} <blockquote><p><span style="background-color: #a28ae580">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#2ea8e5'}} <blockquote><p><span style="background-color: #2ea8e580">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#ffd400'}} <blockquote><p><span style="background-color: #ffd40080">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#5fb236'}} <blockquote><p><span style="background-color: #5fb23680">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#f19837'}} <blockquote><p><span style="background-color: #f1983780">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#e56eee'}} <blockquote><p><span style="background-color: #e56eee80">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote> {{elseif color == '#aaaaaa'}} <blockquote><p><span style="background-color: #aaaaaa80">{{highlight}}</p >📍{{citation}}{{if comment}}<p>✒️批注：{{comment}}</p >{{endif}}<p>🏷️标签: #{{tags join=' #'}}</p></blockquote>{{else}}<p>{{highlight}} {{citation}} {{comment}}</p>{{end}}
```
Step2 修改Better notes 笔记模板：
[[Item-collect annotations by color]]

说明：
<span class="highlight" style="background-color: #ff6666" >高亮 </span> 重点关键
<span class="highlight" style="background-color: #a28ae5" >高亮 </span> 生词句型
<span class="highlight" style="background-color: #ffd40080" >高亮 </span> 背景概述 观点
<span class="highlight" style="background-color: #2ea8e580" >高亮 </span> 方法工具
<span class="highlight" style="background-color: #5fb236" >高亮 </span> 背景综述 参考文献
<span class="highlight" style="background-color: #f19837" >高亮 </span> 结果分析
<span class="highlight" style="background-color: #e56eee" >高亮 </span> 结论讨论
<span class="highlight" style="background-color: #aaaaaa" >高亮 </span> 标题
  
extensions.zotero.annotations.noteTemplates.title

```
<h2>📝 摘录笔记</h2><p style="color:#B0B0B0">创建日期：({{date}})</P>
```