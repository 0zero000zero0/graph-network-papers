```
${await new Promise(async (r) => {
  async function getAnnotation(item) {
    try {
      if (!item || !item.isAnnotation()) {
        return null;
      }
      let json = await Zotero.Annotations.toJSON(item);
      json.id = item.key;
      delete json.key;
      for (let key in json) {
        json[key] = json[key] || "";
      }
      json.tags = json.tags || [];
      return json;
    } catch (e) {
      Zotero.logError(e);
      return null;
    }
  }

  async function getAnnotationsByColor(_item, colorFilter) {
    const annots = _item.getAnnotations().filter(colorFilter);
    if (annots.length === 0) {
      return {
        html: "",
      };
    }
    let annotations = [];
    for (let annot of annots) {
      const annotJson = await getAnnotation(annot);
      annotJson.attachmentItemID = _item.id;
      annotations.push(annotJson);
    }

    if (!editor) {
      alert("No active note editor detected. Please open workspace.");
      return r("");
    }
    await editor.importImages(annotations);
    return Zotero.EditorInstanceUtilities.serializeAnnotations(annotations);
  }

  const attachments = Zotero.Items.get(topItem.getAttachments()).filter((i) =>
    i.isPDFAttachment()
  );
  let res = "";
  const colors = ["#ffd400", "#ff6666", "#5fb236", "#2ea8e5", "#a28ae5"];
  const colorNames = ["Yellow", "Red", "Green", "Blue", "Purple"];
  for (let attachment of attachments) {
    res += `<h1>${attachment.getFilename()}</h1>`;
    for (let i in colors) {
      const renderedAnnotations = (
        await getAnnotationsByColor(
          attachment,
          (_annot) => _annot.annotationColor === colors[i]
        )
      ).html;
      if (renderedAnnotations) {
        res += `<h2><p style="background-color:${colors[i]};">${colorNames[i]} Annotations</p></h2>\n${renderedAnnotations}`;
      }
    }
    const renderedAnnotations = (
      await getAnnotationsByColor(
        attachment,
        (_annot) => !colors.includes(_annot.annotationColor)
      )
    ).html;
    if (renderedAnnotations) {
      res += `<h2><p>Other Annotations</p></h2>\n${renderedAnnotations}`;
    }
  }
  r(res);
})}
```