---
layout: default
title: Modelling how acetylcholine switches a hippocampal neuron into persistent firing
permalink: /portfolio/acetylcholine-ca1-model/
description: A detailed CA1 pyramidal neuron model fitted to control and carbachol recordings to find which ion-channel changes explain the switch into persistent firing.
case_study: true
order: 2
summary: A detailed model of a hippocampal CA1 pyramidal neuron, fitted to recordings made before and during the cholinergic agonist carbachol, to find which ion-channel changes explain the switch into persistent firing.
tags:
  - Computational neuroscience
  - Biophysical modelling
  - NEURON
  - Parameter fitting (CMA-ES)
  - HPC
  - Python
---

<div class="section">
  <div class="container case-study">
    <p><a href="{{ '/portfolio/' | relative_url }}" style="color:var(--primary); text-decoration:none;">&larr; Portfolio</a></p>
    <h2 style="font-size: 2.2rem; color: var(--secondary); line-height: 1.25; margin-bottom: 10px;">Modelling how acetylcholine switches a hippocampal neuron into persistent firing</h2>
    <p class="tags">{{ page.tags | join: " · " }}</p>

    <p style="font-size: 1.2rem; font-style: italic; color: var(--secondary);">A detailed model of a hippocampal CA1 pyramidal neuron, fitted to recordings made before and during the cholinergic agonist carbachol, to find which ion-channel changes explain the switch into persistent firing, and to check that the explanation holds across cells and fitting runs.</p>

    <h3>Context</h3>
    <p>CA1 pyramidal neurons carry most of the output of the hippocampus, the brain region that forms new memories. During attention and exploration, acetylcholine changes how these cells respond: it closes several K⁺ currents and is thought to recruit a Ca²⁺-activated non-specific cation current (I<sub>CAN</sub>). The change is thought to help neurons hold information for seconds after a brief input, and cholinergic signaling declines in Alzheimer’s disease.</p>
    <p>The team had whole-cell current-clamp recordings of CA1 cells in control and under carbachol (CCh), where the cells switch into persistent firing that outlasts the input. They wanted to test whether a non-specific I<sub>CAN</sub> current could explain that switch, with a model that explains the changes channel by channel, makes testable predictions, and provides figures for a grant application.</p>

    <h3>The problem</h3>
    <p>A detailed neuron model has dozens to about a hundred free parameters, and many different combinations reproduce the same voltage traces. A free refit of every parameter to the CCh recordings would match the data and explain nothing. A useful model had to answer three questions: which channels do the control recordings actually constrain; what is the smallest set of changes that turns the control cell into the CCh cell, and is it the same in different cells; and does the persistent firing really depend on I<sub>CAN</sub>, rather than on a fitting artefact?</p>

    <h3>What I did</h3>
    <ul>
      <li><strong>Started from a published model.</strong> I used the detailed CA1 model of Tar et al. [1] in NEURON, with a reconstructed morphology and the recorded current replayed at the soma.</li>
      <li><strong>Built a reproducible fitting pipeline in Python.</strong> Each cell is fitted to its own recordings. The objective scores what an electrophysiologist reads off the trace: spike count and timing, spike shape along the train, and the activity after the input ends. A bounded CMA-ES optimizer runs on a SLURM cluster as one chained job per cell (control fit, then CCh fit), with several independent runs per setting.</li>
      <li><strong>Mapped the parameter landscape.</strong> Parameter-importance analysis (fANOVA), UMAP projections and sensitivity analysis show which parameters the data pin down and which trade off against each other.</li>
      <li><strong>Replaced generic parts with biology.</strong> Where the original model was generic, I replaced it with literature-based components, such as CA1-specific sodium channels [2, 3] and a realistic axon [4].</li>
      <li><strong>Made parameters readable.</strong> Control parameters are physical values in standard units. CCh parameters are expressed as changes relative to control, so each one can be read as a concrete effect of acetylcholine.</li>
      <li><strong>Fitted CCh as a sparse change.</strong> A penalty on every departure from control makes the fit report the smallest change the data demand. Effects count only if they agree across runs, settings and cells. Built-in controls guard against artefacts, including a virtual I<sub>CAN</sub> block that checks whether the persistent firing truly depends on it.</li>
    </ul>

    <h3>Result</h3>
    <ul>
      <li><strong>A clear map of what the data can and can’t tell.</strong> The team knows which parameters the recordings constrain and which ones can’t be pinned down from this data alone.</li>
      <li><strong>A minimal, consistent explanation.</strong> The model reproduces both the control and the CCh recordings with a small set of channel changes that hold across cells and fitting runs, and it gives testable predictions for follow-up experiments.</li>
      <li><strong>Dead ends documented, not hidden.</strong> Mechanisms the literature doesn’t support, and fits that matched the data for the wrong reasons, were recorded, and the pipeline now catches them automatically.</li>
    </ul>

    <h3>What the team kept</h3>
    <ul>
      <li>A self-contained model package: morphology, channel mechanisms, recordings, objective and optimizer, with one command per stage on the cluster.</li>
      <li>Written documentation of the model, its parameters and their sources, and a log of the CCh results.</li>
      <li>Grant figures comparing model and recordings, and a parameter-importance map.</li>
      <li>A set of checks for any future CCh model, including a virtual I<sub>CAN</sub> block and agreement across runs and cells before a change is reported.</li>
    </ul>

    <h3>References</h3>
    <ol class="references">
      <li>[1] Tar L, et al. (2026). Detailed CA1 pyramidal cell model, Káli group.</li>
      <li>[2] Royeck M, et al. (2008). <em>J Neurophysiol</em> 100:2361–2380.</li>
      <li>[3] Lorincz A, Nusser Z (2008). <em>J Neurosci</em> 28:14329–14340; (2010) <em>Science</em> 328:906–909.</li>
      <li>[4] Cohen CCH, et al. (2020). <em>Cell</em> 180:311–322.</li>
    </ol>
  </div>
</div>
