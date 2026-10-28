---
layout: default
title: Portfolio
permalink: /portfolio/
description: Case studies in clinical EEG and computational neuroscience by Dr. Roman Baravalle, from Parkinson’s disease biomarkers to biophysical neuron models.
---

<div class="section">
  <div class="container">
    <h2 style="font-size: 2.5rem; color: var(--secondary); margin-bottom: 20px;">Portfolio</h2>

    <div class="grid-3" style="margin-top: 40px;">
      {% assign case_studies = site.pages | where: "case_study", true | sort: "order" %}
      {% for study in case_studies %}
      <div class="card">
        <p class="tags">{{ study.tags | slice: 0, 3 | join: " · " }}</p>
        <h3>{{ study.title }}</h3>
        <p>{{ study.summary }}</p>
        <a href="{{ study.url | relative_url }}" style="color:var(--primary); font-weight:bold; margin-top:15px; display:inline-block;">Read case study &rarr;</a>
      </div>
      {% endfor %}
    </div>
  </div>
</div>
