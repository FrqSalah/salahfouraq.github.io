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
</div>

<!-- Algolia search scripts -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@algolia/algoliasearch-netlify-frontend@1/dist/algoliasearchNetlify.css" />
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/@algolia/algoliasearch-netlify-frontend@1/dist/algoliasearchNetlify.js"></script>
<script type="text/javascript">
  algoliasearchNetlify({
    appId: 'R188TE8U9T',
    apiKey: '2dab79540b373f6a42b7bdf0d350f433',
    siteId: 'cd8e7573-fb3b-43d4-a29f-b698d26f10c4',
    branch: 'main',
    selector: 'div#search',
  });
</script>