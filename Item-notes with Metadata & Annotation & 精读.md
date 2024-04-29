```
<h1>${topItem.getField("title")}</h1>
<h2>💡 Meta Data</h2>
<table>
    <tr>
        <th>
            <p style="text-align: right">Title </p>
        </th>
        <td>
            ${topItem.getField('title')}
        </td>
    </tr>
    <tr>
        <th>
            <p style="text-align: right">Journal </p>
        </th>
        <td>
            ${topItem.getField('publicationTitle')}
        </td>
    </tr>
    <tr>
        <th>
            <p style="text-align: right">1<sup>st</sup> Author </p>
        </th>
        <td>
            ${topItem.getField('firstCreator')}
        </td>
    </tr>
    <tr>
        <th>
            <p style="text-align: right">Authors </p>
        </th>
        <td>
            ${topItem.getCreators().map((v)=>v.firstName+" "+v.lastName).join("; ")}
        </td>
    </tr>
    <tr>
        <th>
            <p style="text-align: right">Pub. date </p>
        </th>
        <td>
            ${topItem.getField('date')}
        </td>
    </tr>
    <tr>
        <th>
            <p style="text-align: right">引用 </p>
        </th>
        <td>
            ${topItem.getField('archiveLocation')}
        </td>
    </tr>
    <tr>
        <th>
            <p style="text-align: right">分区 </p>
        </th>
        <td>
            ${topItem.getField('libraryCatalog')}
        </td>
    </tr>
</table>
<h2>⭐ 精读笔记</h2>
	<h3>论文内容</h3>
		<p>🗂<b>文章类型</b>:: #📚理论专题 #📖范式方法 #🔔具体应用 #📝案例分析 #🔍研究综述</p>
	    <p>🚩<b>研究问题</b>:: </p>
	    <p>🧱<b>理论基础</b>:: </p>
	    <p>🔐<b>研究方法</b>:: </p>
	    <p>✨<b>研究结论</b>:: </p>
	    <p>🔭<b>研究展望</b>:: </p>
	    <p>📝<b>全文小结</b>:: </p>
    <h3>我的思考</h3>
	    <p>🎉<b>研究创新</b>:: </p>
	    <p>🔒<b>研究局限</b>:: </p>
	    <p>💡<b>启发收获</b>:: </p>
	    <p>🔗<b>比较阅读</b>:: </p>
    <p></p>
${itemNotes.map((noteItem)=>{
const noteLine = `
<p>
    ${noteItem.getNote()}
    <p style="background-color: pink;"><strong>Merge Date: </strong> ${new Date().toISOString().substr(0,10)+" "+ new Date().toTimeString()}</p>
</p>`;
copyNoteImage(noteItem);
return noteLine;
}).join("\n")}
```