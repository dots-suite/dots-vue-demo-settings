<template>
  <article class="about">
    <div class="title app-width-padding">Features covered in this recipe</div>
    <div class="about-content app-width-padding">
      <h2>Data format selection</h2>
      <p>
        By default, when displaying a document or a fragment, the response format of the DTS Document endpoint is XML/TEI.
        DoTS, thanks to the <code>mediaType</code> request parameter, can deliver a document or a fragment in HTML format.
      </p>
      <p>
        For each collection, the JSON configuration file allows you to define the format (XML/TEI or HTML) using the <code>mediaTypeEndpoint</code> parameter.
      </p>
      <p>
        The default XML/TEI format is defined for this recipe in the <code>encpos.conf.json</code> configuration file: <code>"mediaTypeEndpoint": "tei"</code>.
      </p>
      <p>
        But this configuration is redefined specifically for the 1972 sub-collection, in its configuration file <code>encpos_1972.conf.json</code>: <code>"mediaTypeEndpoint": "html"</code>.
      </p>
      <h2>Styling</h2>
      <!--<h3>REVOIR LE STYLE</h3>-->
      <p>
        <b>For a response in XML/TEI format</b>.<br>
        By default, the <a href="https://www.tei-c.org/release/xml/tei/stylesheet/tei.css" target="_blank">CSS stylesheet defined by the TEI Consortium</a> are applied.
        But for each collection, it's possible to call other CSS stylesheets from its JSON configuration file, thanks to the <code>collectionCustomCss</code> parameter.
      </p>
      <p>
        Examples:
      </p>
      <ul>
        <li>
          TEI Consortium CSS stylesheets: ENCPOS_1849_08<br>
          see <code>encpos.conf.json</code>: <code>"collectionCustomCss": false</code>
        </li>
        <li>
          Custum CSS stylesheets: ENCPOS_1971_12<br>
          see <code>encpos_1971.conf.json</code>: <code>"collectionCustomCss": "dots_cookbook"</code>
        </li>
      </ul>
      <p>
        The <code>collectionCustomCss</code> parameter value points to the collection containing the CSS folder.<br>
        Here, in <code>encpos_1971.conf.json</code>, the value <code>dots_cookbook</code> points to the stylesheets of <code>dots_cookbook/assets/css</code>.
      </p>
      <p>To customize TEI elements, edit the "PART 3" of the stylesheet <i>collectionCustomCss.css</i>
      </p>
      <p>
        To fully understand overload logic, read the “Overview” section.
      </p>
      <!--<h3>REVOIR LE STYLE</h3>-->
      <p>
        <b>For a response in HTML format</b>.<br>
        To customize HTML elements, the process is similar:
        <ul>
          <li>use the <code>collectionCustomCss</code> parameter to provide a stylesheet in the <code>collection/assets/css</code>,</li>
          <li>edit the "PART 2" of the provided stylesheet.</li>
        </ul>
      </p>
    </div>
  </article>

</template>
<script>
export default {
  name: 'AboutThirdSection',

  setup () {
  }
}
</script>
<style scoped>
</style>
