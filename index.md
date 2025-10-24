---
layout: default
title: Choose options
---

<main style="margin:2rem 1rem;max-width:42rem">
  <h1>{{ page.title }}</h1>

  <form id="chooser">
    <fieldset style="border:1px solid #ddd;padding:1rem;border-radius:.5rem">
      <legend>Pick options</legend>

      {%- assign opts = site.data.options | default: 
        (site.data.options == null ? nil : site.data.options) -%}
      {%- if opts -%}
        {%- for o in site.data.options -%}
          <label style="display:block;margin:.35rem 0">
            <input type="checkbox" name="{{ o.id }}">{{ o.label }}
          </label>
        {%- endfor -%}
      {%- else -%}
        <!-- Fallback if you don't use _data/options.yml -->
        <label><input type="checkbox" name="a"> A</label>
        <label><input type="checkbox" name="b"> B</label>
        <label><input type="checkbox" name="c"> C</label>
      {%- endif -%}
    </fieldset>
    <button type="submit" style="margin-top:.75rem;padding:.5rem .9rem">Go</button>
  </form>
</main>

<script>
  // Build canonical key (sorted names joined with '+'), then go to outcome page.
  const OUTCOME_URL = {{ '/outcome.html' | relative_url | jsonify }};

  // Pre-check from ?sel=... so links are shareable
  const params = new URLSearchParams(location.search);
  if (params.has("sel")) {
    params.get("sel").split("+").filter(Boolean).forEach(name => {
      const box = document.querySelector(`input[name="${CSS.escape(name)}"]`);
      if (box) box.checked = true;
    });
  }

  document.getElementById("chooser").addEventListener("submit", (e) => {
    e.preventDefault();
    const sel = [...document.querySelectorAll('#chooser input[type="checkbox"]:checked')]
      .map(i => i.name).sort().join("+"); // e.g. "a+b"
    const url = new URL(OUTCOME_URL, location.origin);
    url.searchParams.set("sel", sel);
    // Also set hash so there’s a CSS :target fallback if you want it later
    url.hash = encodeURIComponent(sel);
    location.href = url.pathname + url.search + url.hash;
  });
</script>
