<script>
  import { supabase } from '$lib/supabase'
  import Primo, { storage_subscribe, subscribe } from '@bbscdigital/builder'

  // NEXT: finish hooking this up
  subscribe(async ({ table, action, data, id, match, order }) => {
    let res
    if (action === 'insert') {
      res = await supabase.from(table).insert(data)
    } else if (action === 'update') {
      res = await supabase.from(table).update(data).eq('id', id)
    } else if (action === 'delete') {
      if (id) {
        res = await supabase.from(table).delete().eq('id', id).select()
      } else if (match) {
        res = await supabase.from(table).delete().match(match).select()
      }
    } else if (action === 'upsert') {
      res = await supabase.from(table).upsert(data)
    } else if (action === 'select') {
      if (order) {
        res = await supabase
          .from(table)
          .select(data)
          .match(match)
          .order(...order)
      } else {
        res = await supabase.from(table).select(data).match(match)
      }
    }
    if (res.error) {
      console.log('Error: ', { res })
    }
    return res.data
  })

  storage_subscribe(async ({ bucket, action, key, file, options }) => {
    if (action === 'upload') {
      const { data } = await supabase.storage
        .from(bucket)
        .upload(key, file, options)
      const { data: res } = supabase.storage.from('images').getPublicUrl(key)
      return res.publicUrl
    }
  })

  export let data
</script>

<Primo
  role={data.user.role}
  data={{
    site: data.site,
    pages: data.pages,
    symbols: data.symbols,
  }}
>
  <slot />
</Primo>
