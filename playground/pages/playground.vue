<script setup>
const PARSE_SERVER = 'https://mdc.nuxt.dev/api/parse'

const INITIAL_CODE = `# MDC

MDC stands for _**M**ark**D**own **C**omponents_.

This syntax supercharges regular Markdown to write documents interacting deeply with any Vue component from your \`components/content/\` directory or provided by a module.

## Next steps
- [Install Nuxt Content](/docs/getting-started)
- [Explore the MDC syntax](/docs/syntax)
`
const content = ref(INITIAL_CODE)

const { data: doc, refresh } = await useAsyncData('playground', async () => {
  try {
    return await $fetch(PARSE_SERVER, {
      method: 'POST',
      cors: true,
      body: {
        id: 'content:_file.md',
        content: content.value
      }
    })
  } catch (e) {
    return doc.value
  }
})

const tab = ref('Preview')

const tabs = ref(['Preview', 'AST'])
</script>

<template>
  <div class="playground">
    <textarea v-model="content" @input="refresh" />
    <div class="content">
      <div class="tabs">
        <button
          v-for="name in tabs"
          :key="name"
          class="outline"
          :class="{ active: name === tab }"
          @click="tab = name"
        >
          {{ name }}
        </button>
      </div>
      <ContentRenderer v-if="tab === 'Preview'" :value="doc">
        <template #empty>
          <div>Content is empty.</div>
        </template>
      </ContentRenderer>
      <pre v-if="tab === 'AST'" style="padding: 1rem;">{{ doc }}</pre>
    </div>
  </div>
</template>

<style>
body, html {
  margin: 0;
  padding: 0;
}

.playground {
  display: flex;
  align-items: stretch;
}

textarea {
  flex: 1;
  min-height: 100vh;
  width: 50%;
  border-radius: 0;
}

.content {
  flex: 1;
  width: 50%;
  min-height: 100vh;
  padding: 1rem;
}

.tabs {
  display: flex;
  flex-direction: row;
  padding: 1rem;
  gap: 1rem;
}

.tabs > button {
  opacity: 0.75;
}

.tabs > button.active {
  border-width: 2px;
  opacity: 1;
}
</style>
