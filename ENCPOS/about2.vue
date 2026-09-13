<template>
  <article class="about">
    <div class="title app-width-padding">Collection, document, fragment: editorial segmentation</div>
    <div class="about-content app-width-padding">
      <h2>Thesis abstract as a document</h2>
      <p>
        In this case, the publisher wishes to emphasize the semantic structuring of the corpus: the annual volume is not considered as a document (a volume), but as the annual collection of published positions. Consequently, each position becomes a document in this annual collection.
      </p>
      <p>
        The hierarchy is as follows:
      </p>
      <pre class="desktop">
encpos                    collection (level 1)
  > year                  collection (level 2)
    > abstract            document
      > section           fragment (level 1, i.e. part)
        > sub-section     fragment (level 2, i.e. chapter)
      </pre>
      <pre class="responsive">
encpos            collection (level 1)
> year            collection (level 2)
 > abstract       document
  > section       fragment
                  (level 1 - part)
   > sub-section  fragment
                  (level 2 - chapter)
      </pre>
      <p>
        The various sections and subsections (part, chapter, etc.) of each abstract are declared in the endpoint Navigation response, for example:
        <a href="https://dots.chartes.psl.eu/demo/api/dts/navigation?resource=ENCPOS_1972_18&down=2" target="_blank">https://dots.chartes.psl.eu/demo/api/dts/navigation?resource=ENCPOS_1972_18&down=2</a>
      </p>
      <h2>Define editorial segmentation for display</h2>
      <p>
        Would you like to consult the all abstract on a single page? Or do you want to browse by part or sub-part (chapter)?
        You can define this editorial segmentation for each collection in its JSON configuration file, using the <code>editByLevel</code> parameter.
      </p>
      <p>Different cases are illustrated in this recipe:</p>
      <ul>
        <li>1849 and 1971: document level (<code>"editByLevel": 0</code>, default setting for the recipe defined in <code>encpos.conf.json</code>)</li>
        <lI>1972 : part level (<code>"editByLevel": 0</code>, custom setting for that collection defined in its JSON configuration file <code>encpos_1972.conf.json</code><code></code> )</lI>
      </ul>
      <p>
        Rather than defining the segmentation level numerically, it's possible to specify it semantically, according to <code>citeType</code> value (DTS Navigation Endpoint), using the <code>editByCiteType</code> parameter.<br>
        This feature is illustrated in the Theater recipe.
      </p>
      <h2>Table of contents depth</h2>
      <p>
        Independently of this editorial segmentation, it is possible to define the depth of a document's table of contents (TOC) using the <code>tableOfContentDepth</code> query parameter.<br>
        For example, if 1972 abstracts are segmented by part (level 1), their TOC are detailed at chapter level (level 2).
      </p>
      <p>
        DoTS-Vue automatically provides a link for each TOC item according to the scenario: fragment link if the level corresponds to the editorial segmentation, anchor if the level is finer than this segmentation.
      </p>
    </div>
  </article>

</template>
<script>
export default {
  name: 'AboutSecondSection',

  setup () {
  }
}
</script>
<style scoped>
.responsive {
  display: none;
}

@media screen and (max-width: 500px) {
  .desktop {
    display: none;
  }
  .responsive {
    display: block;
    padding-left: 0.5em;
    padding-right: 0.5em;
  }
}
</style>
