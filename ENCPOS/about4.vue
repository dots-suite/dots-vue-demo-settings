<template>
  <article class="about">
    <div class="title app-width-padding">Opening a search page and shaping its facets</div>
    <div class="about-content app-width-padding">
      <p>
        A collection has no search page until its configuration asks for one.
        <code>customRoutes</code> puts the page online, <code>searchConfig</code> decides which
        facets the reader is offered, under which label and in which order, and
        <code>listSection.columns</code> shapes the results table. The three are independent, and
        each may be left out.
      </p>
      <p>
        <b>One prerequisite comes before both.</b> dots-vue does not search the DTS endpoint
        directly: it queries an Elasticsearch index, and that index has to be built first. The
        crawl is the job of
        <a href="https://github.com/dots-suite/dots-cli-es" target="_blank">dots-cli-es</a>, which
        walks the collection tree, collects the documents and their fragments, and populates the
        indexes the search API reads. Which metadata becomes a facet, and under which canonical key,
        is decided there — the settings below can rename, reorder or hide a facet, never invent one.
      </p>
      <p>
        See the
        <a href="https://dots-suite.github.io/dots-cli-es/" target="_blank">dots-cli-es documentation</a>,
        and its
        <a href="https://dots-suite.github.io/dots-cli-es/indexing/" target="_blank">Indexing</a>
        page for running a crawl. Without an index, the page opens and searches return nothing.
      </p>

      <h2>1. Putting the page online: <code>customRoutes</code></h2>
      <p>
        This array declares the extra pages a collection exposes beside its home, list and document
        views. One entry is enough to open a search page:
      </p>
      <pre class="conf">"customRoutes": [
  {
    "name": "Search",
    "path": "search",
    "compName": "SearchPage"
  }
]</pre>
      <ul>
        <li>
          <code>path</code> is the URL segment. This recipe is therefore searchable at
          <code>/ENCPOS/search</code>. The match is <b>exact</b>: a visitor asking for a segment
          that no entry declares is redirected to the home page rather than shown an error.
        </li>
        <li>
          <code>compName</code> names the component to mount. <b><code>SearchPage</code> is case
          sensitive</b>, and is currently the only component a custom route can call.
        </li>
        <li>
          <code>name</code> is the internal route name, used when building links.
        </li>
      </ul>
      <p>
        Nothing else is required on the routing side: once the index exists, the page queries the
        search API of the project, and the reader reaches it from the collection header.
      </p>

      <h2>2. Two families of facets</h2>
      <p>
        <code>searchConfig</code> holds two independent lists. <code>facets</code> governs the
        metadata facets — values a reader ticks, such as an author. <code>temporalFacets</code>
        governs the date ranges, presented as intervals rather than as lists of values.
      </p>
      <p>
        In both lists an entry is identified by <code>key</code>, the canonical metadata key as the
        indexing side writes it: <code>dublinCore.creator</code>,
        <code>extensions.funder</code>. The single exception is <code>collections</code>, the facet
        that lets a reader narrow the search to a sub-collection.
      </p>
      <p>
        <b>The logic is one of exclusion, not of selection.</b> A facet the API returns but the
        configuration never mentions is still displayed, under its default label. Declaring an entry
        therefore serves to do one of three things: exclude it, rename it, or move it.
      </p>

      <h2>3. Excluding: <code>"enabled": false</code></h2>
      <pre class="conf">{
  "key": "dublinCore.publisher",
  "enabled": false
}</pre>
      <p>
        The facet disappears from the panel — and the saving goes further than the display: its key
        is sent to the search API, which then <b>skips computing that aggregation altogether</b>.
        Excluding a facet is therefore also a way of lightening a query.
      </p>
      <p>
        This recipe keeps two metadata facets, the annual volumes and the authors, and turns off
        eight others that the corpus either does not populate or does not need.
      </p>

      <h2>4. Renaming: <code>label</code></h2>
      <pre class="conf">{
  "key": "dublinCore.creator",
  "label": "Auteurs",
  "enabled": true,
  "order": 3
}</pre>
      <p>
        Without a <code>label</code>, a metadata facet is titled with its raw key —
        <code>dublinCore.creator</code> — which is precise but hardly inviting. A temporal facet
        falls back on the label supplied by the API instead.
      </p>
      <p>
        Renaming is purely a matter of display. It changes neither the key queried nor the values
        returned, which is why the same corpus can be presented as “Auteurs” here and under another
        wording elsewhere.
      </p>

      <h2>5. Ordering: <code>order</code></h2>
      <p>
        Facets are sorted by ascending <code>order</code>. The two lists do not treat an unnumbered
        facet the same way:
      </p>
      <ul>
        <li>
          among <b>metadata facets</b>, one left without <code>order</code> is pushed to the end of
          the panel;
        </li>
        <li>
          among <b>temporal facets</b>, one left without <code>order</code> keeps the position the
          API gave it, and numbered facets are inserted relative to it.
        </li>
      </ul>
      <p>
        The <code>collections</code> facet is a case apart: it always opens the panel, whatever
        number it is given.
      </p>
      <p>
        Here the two date ranges are numbered so that the promotion year precedes the subject
        period:
      </p>
      <pre class="conf">"temporalFacets": [
  {
    "key": "dublinCore.created",
    "label": "Promotion (période)",
    "enabled": true,
    "order": 1
  },
  {
    "key": "dublinCore.coverage",
    "label": "Sujet (période)",
    "enabled": true,
    "order": 2
  },
  {
    "key": "dublinCore.issued",
    "enabled": false
  }
]</pre>

      <h2>6. The results table: <code>columns</code></h2>
      <p>
        Facets narrow the result set; <code>columns</code> decides what the reader actually sees of
        each result. The setting does not live under <code>searchConfig</code> but under
        <code>homePageSettings.listSection</code>, because the same array also describes the
        collection listing: one declaration, two views.
      </p>
      <p>
        <b>Left undeclared, the search results table falls back to a single column, the title.</b>
        That is enough to read a result, never enough to compare two — hence the four columns of
        this recipe:
      </p>
      <pre class="conf">"listSection": {
  "columns": [
    { "key": "title",               "label": "Titre",  "width": "2fr" },
    { "key": "dublinCore.creator",  "label": "Auteur", "width": "1fr" },
    { "key": "dublinCore.created",  "label": "Date",   "type": "date", "width": "125px" },
    { "key": "dublinCore.coverage", "label": "Sujet",  "type": "date", "width": "125px" }
  ]
}</pre>
      <p>
        Columns are displayed in the order of the array. Each entry accepts four properties:
      </p>
      <ul>
        <li>
          <code>key</code> — the canonical metadata key to read, the same vocabulary as the facets.
          It is the only required property: an entry without it is silently dropped.
        </li>
        <li>
          <code>label</code> — the column header. Without it the raw key is displayed.
        </li>
        <li>
          <code>type</code> — <code>string</code> unless stated. <code>date</code> is the one that
          changes behaviour: the value is checked against the temporal normalisation and is
          displayed <b>only if both bounds of the period could be resolved</b>. An ambiguous or
          incomplete date leaves the cell empty rather than showing something misleading.
        </li>
        <li>
          <code>width</code> — a CSS grid track: a proportion (<code>2fr</code>) or a fixed size
          (<code>125px</code>). Mixing both is the point — the title takes the space the two date
          columns do not need. If no column declares a width, all share the width equally.
        </li>
      </ul>

      <h2>In short</h2>
      <p>
        <code>customRoutes</code> opens the page, <code>enabled</code> removes a facet from the
        panel and from the query, <code>label</code> makes it readable, <code>order</code> places
        it, and <code>columns</code> shapes the results table. Only exclusion reaches the search
        API; labels, order and columns never leave the front-end.
      </p>
      <p>
        The indexing side documents the same settings from the API's point of view, in
        <a href="https://dots-suite.github.io/dots-cli-es/custom-settings/" target="_blank">Using custom settings</a>.
      </p>
    </div>
  </article>

</template>
<script>
export default {
  name: 'AboutFourthSection',

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
