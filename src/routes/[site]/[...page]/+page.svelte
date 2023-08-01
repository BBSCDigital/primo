<script>
  import { PrimoPage } from '@bbscdigital/builder'

  export let data
</script>

<PrimoPage
  page={{
    ...data.page,
    sections: data.sections,
  }}
/>
