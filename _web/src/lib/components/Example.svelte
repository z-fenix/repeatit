<script lang="ts">
  import { get } from "svelte/store";
  import { location, push } from "svelte-spa-router";
  import { codes } from "@/lib/store";
  import examples from "@/examples";
  import update from "immutability-helper";
  import { runIt } from "@/lib/helper/load";
  import { browser } from "$app/environment";
  import { onMount } from "svelte";
  import Select from "../ui/Select.svelte";

  const exampleURL = "/example/";

  let firstLocation = "";
  let selected = "";

  const changeSelected = (vSelected: string) => {
    if (!vSelected) {
      return;
    }

    push(exampleURL + vSelected);

    const values = examples.get(vSelected);
    if (!values) return;

    codes.update((v) =>
      update(v, {
        template: { $set: values.template },
        input: { $set: values.input },
        output: { $set: "" },
        trigger: { $set: !v.trigger },
        error: { $set: false },
        success: { $set: false },
      })
    );

    runIt();
  };

  onMount(() => {
    if (browser) {
      firstLocation = get(location);
      if (firstLocation.startsWith(exampleURL)) {
        selected = firstLocation.replace(exampleURL, "");
      }

      changeSelected(selected);
    }
  });
</script>

<Select>
  <select
    class="w-36 px-3 h-10 py-2 text-gray-800 bg-white border-l border-r cursor-pointer hover:bg-gray-50 rounded-none outline-none appearance-none focus:ring-offset-2 focus:ring-red-400 focus:ring-2"
    bind:value={selected}
    onchange={() => changeSelected(selected)}
  >
    <option value="">--examples--</option>
    {#each [...examples.keys()] as name}
      <option value={name}>{name}</option>
    {/each}
  </select>
</Select>
