<script setup lang="ts">
import {CompileTestCase, Profile} from "../pages/compare/compile/common";
import {computed} from "vue";

const props = defineProps<{
  commit: string;
  testCase: CompileTestCase;
  baselineCommit?: string;
}>();

const firstCommit = computed(() => {
  if (props.baselineCommit !== undefined) {
    return props.baselineCommit;
  } else {
    return props.commit;
  }
});

function normalizeProfile(profile: Profile): string {
  if (profile === "opt") {
    return "Opt";
  } else if (profile === "debug") {
    return "Debug";
  } else if (profile === "check") {
    return "Check";
  } else if (profile === "doc") {
    return "Doc";
  }
  return "<invalid profile>";
}
function normalizeScenario(scenario: string): string {
  if (scenario === "full") {
    return "Full";
  } else if (scenario === "incr-full") {
    return "IncrFull";
  } else if (scenario === "incr-unchanged") {
    return "IncrUnchanged";
  } else if (scenario.startsWith("incr-patched")) {
    return "IncrPatched";
  }
  return "<invalid scenario>";
}
</script>

<template>
  <pre><code>./target/release/collector profile_local cachegrind \
    +{{ firstCommit }} \<template v-if="props.baselineCommit !== undefined">
    --rustc2 +{{ props.commit }} \</template>
    --include {{ testCase.benchmark }} \
    --profiles {{ normalizeProfile(testCase.profile) }} \
    --scenarios {{ normalizeScenario(testCase.scenario) }}</code></pre>
</template>

<style scoped lang="scss">
pre {
  background-color: #eeeeee;
}
code {
  user-select: all;
}
</style>
