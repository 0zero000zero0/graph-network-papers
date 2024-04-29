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
        <tr>
        <th>
            <p style="text-align: right">摘要 </p>
        </th>
        <td>
            ${topItem.getField('abstract')}
        </td>
    </tr>
</table>
<h2>📝 精读笔记</h2>
    <p>🚩<b>研究问题</b>:: </p>
    <p>🧱<b>理论基础</b>:: </p>
    <p>🔐<b>研究方法</b>:: </p>
    <p>✨<b>研究结论</b>:: </p>
    <p>🔭<b>研究展望</b>:: </p>
    <p>📝<b>全文小结</b>:: </p>
    <p>🗂<b>文章类型</b>:: #📚理论专题 #📖范式方法 #🔔具体应用  #📝案例分析 #🔍研究综述</p>
    <p>🎉<b>创新点</b>:: </p>
    <p>💡<b>启发和收获</b>:: </p>
    <p>🔒<b>研究局限性</b>:: </p>
    <p>🔗<b>比较阅读</b>:: </p>
    <p></p>

