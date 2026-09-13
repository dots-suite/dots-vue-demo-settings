<template>
  <article class="about">
    <div class="title app-width-padding">Selecting, ordering and renaming metadata</div>
    <div class="about-content app-width-padding">
      <p>
        A DTS endpoint returns far more metadata than a reader needs to see, in a vocabulary
        (Dublin Core terms, schema.org, DTS itself) that is precise but not always readable.
        Four settings of the collection configuration file decide what reaches the metadata panel,
        in which order, and under which label.
      </p>
      <p>
        This recipe is configured in <code>encpos.conf.json</code>, whose metadata block is quoted
        step by step below.
      </p>

      <h2>1. Declaring the vocabularies: <code>namespaces</code></h2>
      <p>
        DoTS splits its metadata between a <code>dublinCore</code> object and an
        <code>extensions</code> object, each carrying its own vocabulary. dots-vue expands that
        response into full IRIs, then shortens them back into the familiar
        <code>prefix:term</code> form. <code>namespaces</code> is the table it uses for that second
        step:
      </p>
      <pre class="conf">"namespaces": {
  "dts":    "https://dtsapi.org/v1.0#",
  "dct":    "http://purl.org/dc/terms/",
  "schema": "https://schema.org/",
  "dots":   "https://github.com/dots-suite/dots/"
}</pre>
      <p>
        The title of a document, returned by the endpoint under <code>dublinCore.title</code>,
        becomes <code>http://purl.org/dc/terms/title</code> once expanded, then
        <code>dct:title</code> once shortened. <b>That last form is the one every other setting
        below refers to</b>: a prefix missing from this table leaves its metadata under a long IRI,
        which no <code>dct:…</code> or <code>schema:…</code> entry will ever match.
      </p>
      <p>
        Terms that the endpoint already delivers in prefixed form, such as
        <code>dots:resourceIIIFManifest</code>, reach the settings unchanged and are matched
        literally.
      </p>

      <h2>2. Choosing and ordering: <code>metadataDisplayOrder</code></h2>
      <p>
        This list drives both questions at once: <b>an entry is displayed because it is listed, and
        it is displayed where it is listed</b>. The panel follows the order of the array, not the
        order of the API response.
      </p>
      <pre class="conf">"metadataDisplayOrder": [
  "dct:title",
  "schema:author",
  "dct:created",
  "dct:language",
  "dct:coverage",
  "schema:isBasedOn",
  "schema:publisher",
  "dct:issued",
  "dct:bibliographicCitation",
  "schema:license",
  "dct:rights"
]</pre>
      <p>
        Here the title opens the panel and the rights notice closes it, whatever the endpoint
        returned first.
      </p>
      <p>
        Three kinds of entry are accepted:
      </p>
      <ul>
        <li>
          a <b>plain term</b>, such as <code>dct:title</code>;
        </li>
        <li>
          a <b>wildcard</b>, such as <code>dct:*</code>, which takes every remaining term of that
          vocabulary, in alphabetical order. Useful to close a list with “and everything else from
          this namespace”, as the application default does;
        </li>
        <li>
          a <b>sub-key</b>, written with a dot, such as
          <code>dts:download.application/tei+xml</code>, to reach one entry inside a structured
          value.
        </li>
      </ul>
      <p>
        A term listed but absent from the response is simply skipped, and so is a term whose value
        is empty — which is why a list may safely mention metadata that only some documents carry.
        <code>dct:coverage</code>, in the list above, is one of those.
      </p>

      <h2>3. Renaming: <code>metadataRename</code></h2>
      <p>
        The keys of this object are the terms as written in <code>metadataDisplayOrder</code>; the
        values are the labels shown to the reader.
      </p>
      <pre class="conf">"metadataRename": {
  "dct:title":    "Titre",
  "schema:author": "Auteur",
  "dct:created":  "Date du document",
  "dct:language": "Langue",
  "dct:coverage": "Période du sujet",
  "schema:isBasedOn": "Source",
  "schema:publisher": "Publié par",
  "dct:issued":   "Date de publication",
  "schema:license": "Licence",
  "dct:rights":   "Mentions de droits"
}</pre>
      <p>
        Renaming is cosmetic: it changes the label, never the selection or the order. A term left
        out of this object keeps its raw <code>prefix:term</code> form as a label — which is what
        happens here to <code>dct:bibliographicCitation</code>.
      </p>
      <p>
        Two terms may deliberately share a label, so that metadata coming from different
        vocabularies is presented to the reader as a single notion.
      </p>

      <h2>4. Closing the list: <code>excludeMetadata</code></h2>
      <pre class="conf">"excludeMetadata": {
  "onlyDeclared": true
}</pre>
      <p>
        <code>onlyDeclared</code> decides the fate of everything that
        <code>metadataDisplayOrder</code> did not mention:
      </p>
      <ul>
        <li>
          <code>true</code> — <b>nothing else is displayed</b>. The list above is the complete
          panel, and a metadata added later on the endpoint side stays invisible until the
          configuration welcomes it. This is the choice made for this recipe: the panel is curated.
        </li>
        <li>
          <code>false</code> — the declared terms are displayed first, in order, then every
          remaining term is appended. Convenient while exploring a new corpus, since nothing is
          hidden by accident.
        </li>
      </ul>
      <p>
        Two companion lists refine that decision, <code>fields</code> and
        <code>alwaysExclude</code>:
      </p>
      <pre class="conf">"excludeMetadata": {
  "onlyDeclared": false,
  "fields": [
    "dots:*",
    "dts:totalParents"
  ],
  "alwaysExclude": [
    "dct:rights"
  ]
}</pre>

      <h3>How a term is written</h3>
      <p>
        Both lists use the same nomenclature as <code>metadataDisplayOrder</code>: the shortened
        <code>prefix:term</code> form produced by <code>namespaces</code>. Two shapes are
        recognised, and only two:
      </p>
      <ul>
        <li>
          an <b>exact term</b> — <code>dct:bibliographicCitation</code> — which matches that key
          and no other;
        </li>
        <li>
          a <b>whole-vocabulary wildcard</b> — <code>dots:*</code>, <code>schema:*</code> — which
          matches every key beginning with that prefix.
        </li>
      </ul>
      <p>
        The wildcard is recognised only in that exact shape, a prefix followed by
        <code>:*</code>. A partial pattern such as <code>dct:bibli*</code> is read as an ordinary
        term, matches nothing, and fails silently. There is no way to express “everything” in a
        single pattern: list the prefixes.
      </p>
      <p>
        <code>dots:*</code> is the most useful of them in practice. DoTS publishes its own
        working metadata — identifiers, manifest links, internal short titles — under that
        vocabulary: while precious to the application, it does not necessarily need to be
        displayed in the document metadata.
      </p>

      <h3><code>fields</code>: filtering what was not named</h3>
      <p>
        <code>fields</code> applies to the terms the configuration did <b>not</b> name explicitly.
        It filters two populations:
      </p>
      <ul>
        <li>
          the keys collected by a <b>wildcard of <code>metadataDisplayOrder</code></b> — so
          <code>"dct:*"</code> in the display order and <code>"dct:bibliographicCitation"</code> in
          <code>fields</code> means “all Dublin Core terms except that one”;
        </li>
        <li>
          the <b>leftovers appended by phase two</b>, when <code>onlyDeclared</code> is
          <code>false</code>.
        </li>
      </ul>
      <p>
        <b>A term named explicitly in <code>metadataDisplayOrder</code> is immune to
        <code>fields</code></b>, and so is any label used as a value in
        <code>metadataRename</code>. Listing a term in both places is not a contradiction the
        application arbitrates at random: the explicit mention wins.
      </p>
      <p>
        This recipe declares no <code>fields</code>, and needs none:
        <code>onlyDeclared: true</code> already discards everything unnamed, and its display order
        holds no wildcard.
      </p>

      <h3><code>alwaysExclude</code>: the veto</h3>
      <p>
        <code>alwaysExclude</code> is examined first and overrides everything, <b>including an
        explicit mention</b> in <code>metadataDisplayOrder</code>. It is the one way to remove a
        term without editing the display order — useful to suppress, for one collection, a
        metadata inherited from a shared configuration.
      </p>
      <p>
        Neither list needs to mention the application's own navigation keys — <code>member</code>,
        <code>children</code>, <code>level</code>, <code>citeType</code>, the routing entries.
        Those are removed before the settings are even consulted.
      </p>

      <h2>In short</h2>
      <p>
        <code>namespaces</code> makes the terms addressable, <code>metadataDisplayOrder</code>
        selects and orders them, <code>metadataRename</code> labels them, and
        <code>excludeMetadata</code> decides what happens to the rest.
      </p>
      <p>
        None of these four settings has to be repeated in every file: like every other setting,
        they are inherited and may be overridden collection by collection. The “Overview” section
        explains that cascade.
      </p>

      <h2>The result</h2>
      <p>
        The settings above produce the metadata panel of every document of this recipe — see for
        instance
        <a href="https://dots.chartes.psl.eu/cookbook/ENCPOS/document/ENCPOS_1972_18" target="_blank">ENCPOS_1972_18</a>,
        where the <b>document breadcrumb at the top of the page</b> opens the panel. The order of
        the rows, their French labels and the absence of everything else are read straight from
        <code>encpos.conf.json</code>.
      </p>
    </div>
  </article>

</template>
<script>
export default {
  name: 'AboutFirstSection',

  setup () {
  }
}
</script>
<style scoped>
pre.conf {
  background-color: rgba(0, 0, 0, 0.04);
  padding: 0.75em 1em;
  overflow-x: auto;
  font-size: 13px;
  line-height: 1.45;
}

@media screen and (max-width: 500px) {
  pre.conf {
    white-space: pre-wrap;
    padding-left: 0.5em;
    padding-right: 0.5em;
  }
}
</style>
