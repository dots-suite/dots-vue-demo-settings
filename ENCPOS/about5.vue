<template>
  <article class="about">
    <div class="title app-width-padding">Settings cascade</div>
    <div class="about-content app-width-padding">
      <h2>Principle</h2>
      <p>
        To configure the display of a project or a collection, it is not recommended modifying the default dots-vue configuration file (<code>default.conf.json</code>).
      </p>
      <p>
        Create instead a dedicated project (here <a href="https://github.com/dots-suite/dots-vue-demo-settings" target="_blank">dots-vue-demo-settings</a>) which will be called when the dots-vue application is launched:<br>
        <code>dots-vue % CUSTOM_SETTINGS_PATH=../dots-vue-demo-settings yarn serve</code>
      </p>
      <p>
        In this settings project, you can redefine the default settings (in <code>custom.conf.json</code>), and then specify them for each collection
        in a configuration JSON file named <code>{collection_id}.conf.json</code>.<br>
        The settings cascade is as follows:
      </p>
      <pre class="desktop">
default.conf.json (dots-vue)
	> custom.conf.json (settings-project)
		> collection.conf.json
			> sub-collection.conf.json
				> …
      </pre>
      <pre class="responsive">
default.conf.json (dots-vue)
 > custom.conf.json (settings-project)
  > collection.conf.json
   > sub-collection.conf.json
    > …
      </pre>
      <p>Inheritances can be difficult to understand. This is the case in this recipe, where many different cases are presented.
        The following section provides an overview of these inheritances to help you understand how they work.
      </p>
      <h2>Recipe settings overview (Thesis abstract, case 1)</h2>
      <!-- Code block -->
      <pre class="desktop">
default.conf.json (dots-vue)
  > custom.conf.json (dots-vue-demo-settings)
    > dots_cookbook.conf.json
      > encpos.conf.json
        > encpos_1849.json
        > encpos_1971.json
        > encpos_1972.json
      </pre>
      <pre class="responsive">
default.conf.json (dots-vue)
> custom.conf.json (dots-vue-demo-settings)
 > dots_cookbook.conf.json
  > encpos.conf.json
   > encpos_1849.json
   > encpos_1971.json
   > encpos_1972.json
      </pre>
      <p>
        The table below illustrates the cascade for different parameters.
      </p>
      <!-- Table desktop -->
      <div class="table-container desktop mt-5">
        <table class="table is-striped is-hoverable is-bordered is-fullwidth">
          <thead>
            <tr>
              <th>parameter</th>
              <th><code>default</code></th>
              <th>> <code>custom</code></th>
              <th>> <code>dots_cookbook</code></th>
              <th>> <code>encpos</code></th>
              <th>> last collection</th>
              <th>last coll. ids</th>
              <th>example</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td><code>mediaTypeEndpoint</code></td>
              <td>tei</td>
              <td>html</td>
              <td>→ (html)</td>
              <td>tei</td>
              <td>→ (tei)</td>
              <td>
                <code>ENCPOS_1849</code> <code>ENCPOS_1971</code>
              </td>
              <td>
                <a target='_blank' :href=exampleUrl1>{{ example1 }}</a> <a target='_blank' :href=exampleUrl2>{{ example2 }}</a>
              </td>
            </tr>
            <tr>
              <td></td>
              <td></td>
              <td></td>
              <td></td>
              <td></td>
              <td>html</td>
              <td><code>ENCPOS_1972</code></td>
              <td>
                <a target='_blank' :href=exampleUrl3>{{ example3 }}</a>
              </td>
            </tr>
            <tr>
              <td><code>displayMode</code></td>
              <td>card</td>
              <td>card</td>
              <td>→ (card)</td>
              <td>→ (card)</td>
              <td>not relevant</td>
              <td></td>
              <td>
                <a target='_blank' :href=exampleUrl4>Example 4</a>
              </td>
            </tr>
            <tr>
              <td><code>cardCollectionPerPage</code></td>
              <td>5</td>
              <td>5</td>
              <td>→ (5)</td>
              <td>3</td>
              <td>not relevant</td>
              <td></td>
              <td>
                <a target='_blank' :href=exampleUrl5>Example 5</a>
              </td>
            </tr>
            <tr>
              <td><code>editByLevel</code></td>
              <td>1</td>
              <td>1</td>
              <td>→ (1)</td>
              <td>0 (document)</td>
              <td>→ (0)</td>
              <td><code>ENCPOS_1849</code> <code>ENCPOS_1971</code></td>
              <td>
                <a target='_blank' :href=exampleUrl1>{{ example1 }}</a> <a target='_blank' :href=exampleUrl2>{{ example2 }}</a>
              </td>
            </tr>
            <tr>
              <td></td>
              <td></td>
              <td></td>
              <td></td>
              <td></td>
              <td>1 (part)</td>
              <td><code>ENCPOS_1972</code></td>
              <td>
                <a target='_blank' :href=exampleUrl3>{{ example3 }}</a>
              </td>
            </tr>
            <tr>
              <td><code>tableOfContentDepth</code></td>
              <td>5</td>
              <td>5</td>
              <td>→ (5)</td>
              <td>→ (5)</td>
              <td>→ (5)</td>
              <td><code>ENCPOS_1849</code> <code>ENCPOS_1971</code> <code>ENCPOS_1972</code></td>
              <td>
                <a target='_blank' :href=exampleUrl1>{{ example1 }}</a> <a target='_blank' :href=exampleUrl2>{{ example2 }}</a> <a target='_blank' :href=exampleUrl3>{{ example3 }}</a>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <!-- Table responsive -->
      <div class="table-container responsive mt-5">
        <table class="table is-hoverable is-fullwidth">
          <thead>
            <tr>
              <th>parameter</th>
              <th>cascade</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td><code>mediaTypeEndpoint</code></td>
              <td>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>default</b></span><span>tei</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>custom</b></span><span>html</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>dots_cookbook</b></span><span>→ (html)</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>encpos</b></span><span>tei</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>last collection</b></span><span>→ (tei)</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span class="no-min-width"><b>last coll. ids</b></span><span class="no-min-width"><code>ENCPOS_1849</code><br><code>ENCPOS_1971</code></span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span class="no-min-width"><b>example</b></span><span class="no-min-width"><a target="_blank" :href="exampleUrl1">{{ example1 }}</a><br><a target="_blank" :href="exampleUrl2">{{ example2 }}</a></span></div>
              </td>
            </tr>
            <tr>
              <td></td>
              <td>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>last collection</b></span><span>html</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span class="no-min-width"><b>last coll. ids</b></span>
                  <span class="no-min-width"><code>ENCPOS_1972</code></span>
                </div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span class="no-min-width"><b>example</b></span>
                  <span class="no-min-width"><a target="_blank" :href="exampleUrl3">{{ example3 }}</a></span>
                </div>
              </td>
            </tr>
            <tr>
              <td><code>displayMode</code></td>
              <td>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>default</b></span><span>card</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>custom</b></span><span>card</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>dots_cookbook</b></span><span>→ (card)</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>encpos</b></span><span>→ (card)</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span class="no-min-width"><b>last collection</b></span>
                  <span class="no-min-width">not relevant</span>
                </div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span class="no-min-width"><b>example</b></span>
                  <span class="no-min-width"><a target="_blank" :href="exampleUrl4">Example 4</a></span>
                </div>
              </td>
            </tr>
            <tr>
              <td><code>cardCollectionPerPage</code></td>
              <td>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>default</b></span><span>5</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>custom</b></span><span>5</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>dots_cookbook</b></span><span>→ (5)</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>encpos</b></span><span>3</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span class="no-min-width"><b>last collection</b></span>
                  <span class="no-min-width">not relevant</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span class="no-min-width"><b>example</b></span>
                  <span class="no-min-width"><a target="_blank" :href="exampleUrl5">Example 5</a></span>
                </div>
              </td>
            </tr>
            <tr>
              <td><code>editByLevel</code></td>
              <td>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>default</b></span><span>1</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>custom</b></span><span>1</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>dots_cookbook</b></span><span>→ (1)</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span class="no-min-width"><b>encpos</b></span>
                  <span class="no-min-width">0 (document)</span>
                </div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span><b>last collection</b></span>
                  <span>→ (0)</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span class="no-min-width"><b>last coll. ids</b></span>
                  <span class="no-min-width"><code>ENCPOS_1849</code><br><code>ENCPOS_1971</code></span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span class="no-min-width"><b>example</b></span>
                  <span class="no-min-width"><a target="_blank" :href="exampleUrl1">{{ example1 }}</a><br><a target="_blank" :href="exampleUrl2">{{ example2 }}</a></span></div>
              </td>
            </tr>
            <tr>
              <td></td>
              <td>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>last collection</b></span><span>1 (part)</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span class="no-min-width"><b>last coll. ids</b></span>
                  <span class="no-min-width"><code>ENCPOS_1972</code></span>
                </div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span class="no-min-width"><b>example</b></span>
                  <span class="no-min-width"><a target="_blank" :href="exampleUrl3">{{ example3 }}</a></span>
                </div>
              </td>
            </tr>
            <tr>
              <td><code>tableOfContentDepth</code></td>
              <td>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>default</b></span><span>5</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>custom</b></span><span>5</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>dots_cookbook</b></span><span>→ (5)</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>encpos</b></span><span>→ (5)</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between"><span><b>last collection</b></span><span>→ (5)</span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span class="no-min-width"><b>last coll. ids</b></span>
                  <span class="no-min-width"><code>ENCPOS_1849</code><br><code>ENCPOS_1971</code><br><code>ENCPOS_1972</code></span></div>
                <div class="is-flex is-flex-direction-row is-justify-content-space-between">
                  <span class="no-min-width"><b>example</b></span>
                  <span class="no-min-width"><a target="_blank" :href="exampleUrl1">{{ example1 }}</a><br><a target="_blank" :href="exampleUrl2">{{ example2 }}</a><br><a target="_blank" :href="exampleUrl3">{{ example3 }}</a></span>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <p>
        Let's take the example of the <code>mediaTypeEndpoint</code> format parameter. By default, dots-vue serves data in XML/TEI (<code>default.conf.json</code>).
        This setting is redefined for the entire dots-vue-demo project (<code>custom.conf.json</code>).
        An exception is made for this recipe, whose default format is XML/TEI (<code>encpos.conf.json</code>).
        Finally, the 1972 volume is given special treatment, with the choice of HTML (<code>encpos_1972.conf.json</code>).
      </p>
    </div>
  </article>

</template>
<script>
import { ref, watch, computed } from 'vue'

export default {
  name: 'AboutFifthSection',
  props: {
    applicationBaseUrl: {
      type: String,
    },
  },
  setup (props) {
    const appBaseUrl = ref(props.applicationBaseUrl)
    console.log('About 3 setup window.location.origin : ', window.location.origin)
    console.log('About 3 setup appBaseUrl : ', appBaseUrl)
    const example1 = ref('ENCPOS_1849_04')
    const example2 = ref('ENCPOS_1971_09')
    const example3 = ref('ENCPOS_1972_18')
    const exampleUrl1 = computed(() => `${window.location.origin}${appBaseUrl.value}ENCPOS/document/${example1.value}`)
    const exampleUrl2 = computed(() => `${window.location.origin}${appBaseUrl.value}ENCPOS/document/${example2.value}`)
    const exampleUrl3 = computed(() => `${window.location.origin}${appBaseUrl.value}ENCPOS/document/${example3.value}`)

    console.log('About 3 setup exampleUrl1 : ', exampleUrl1.value)

    watch(props, (newProps) => {
      appBaseUrl.value = newProps.applicationBaseUrl
      console.log('About 3 watch appBaseUrl : ', appBaseUrl.value)
      exampleUrl1.value = `${window.location.origin}${appBaseUrl.value}ENCPOS/document/${example1.value}`
      exampleUrl2.value = `${window.location.origin}${appBaseUrl.value}ENCPOS/document/${example2.value}`
      exampleUrl3.value = `${window.location.origin}${appBaseUrl.value}ENCPOS/document/${example3.value}`
      console.log('About 3 watch exampleUrl1 : ', exampleUrl1.value)
    }, { immediate: true })

    return {
      appBaseUrl,
      example1,
      example2,
      example3,
      exampleUrl1,
      exampleUrl2,
      exampleUrl3
    }
  }
}
</script>
<style scoped>
table {
  width:100%;
  text-align: center;
  font-size: 12px;
}
table td,
table th {
  white-space: nowrap;
  text-align: left;
  padding: 0.10rem 0.10rem;
  & > b, > div > span > b {
    color:black;
    font-weight: bolder;
  }
  & > div > span:not(.no-min-width)  {
    min-width: 70%;
  }
}
table td:last-child {
  width:100%;
}
.responsive {
  display: none;
}

@media screen and (max-width: 500px) {
  .desktop {
    display: none;
  }
  .table-container.responsive {
    display: block;
    & > table tr, th {
      border: 1px solid;
    }
    & > table td {
      border-top: none;
      border-bottom: none;
      border-left: 1px solid;
    }
  }
  pre.responsive {
    display: block;
    white-space: pre-wrap;
    width: 100%;
    padding-left: 0.5em;
    padding-right: 0.5em;
  }
}
</style>

