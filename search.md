---
layout: default
title: Search
permalink: /search/
---

<div class="search-container">
  <h1>Search</h1>
  <p>Use the search box below to find content across the site:</p>
  
  <!-- This is the container where Algolia will render the search interface -->
<div id="search"></div>
<div id="search-results"></div> <!-- This is where results will be displayed -->
</div>

<!-- Algolia search scripts -->
<script type="text/javascript">
  document.addEventListener("DOMContentLoaded", function () {
    const searchContainer = document.querySelector("#search");
    const resultsContainer = document.querySelector("#search-results");

    // Initialize Algolia Search
    const search = algoliasearchNetlify({
      appId: 'R188TE8U9T',
      apiKey: '2dab79540b373f6a42b7bdf0d350f433',
      siteId: 'cd8e7573-fb3b-43d4-a29f-b698d26f10c4',
      branch: 'main',
      selector: 'div#search',
    });

    search.on('input', async function (event) {
      const query = event.target.value;

      if (!query) {
        resultsContainer.innerHTML = "";
        return;
      }

      // Perform a search query
      const results = await search.client.search([{
        indexName: '<YOUR_INDEX_NAME>',
        query: query,
        params: { hitsPerPage: 10 }
      }]);

      // Render results
      renderResults(results.hits);
    });

    function renderResults(hits) {
      resultsContainer.innerHTML = hits
        .map(hit => `<div class="search-result"><a href="${hit.url}">${hit.title}</a></div>`)
        .join('');
    }
  });
</script>
