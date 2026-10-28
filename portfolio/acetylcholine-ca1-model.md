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

    <p style="font-size: 1.2rem; font-style: italic; color: var(--secondary);">A detailed model of a hippocampal CA1 pyramidal neuron, fitted to recordings made before and during the cholinergic agonist carbachol, to map which balances of ion channels produce the cell’s firing pattern, and which changes acetylcholine makes to switch it into persistent firing.</p>

    <h3>Context</h3>
    <p>CA1 pyramidal neurons carry most of the output of the hippocampus, the brain region that forms new memories. Each cell has a characteristic electrophysiological signature: a typical way of responding to injected current, set by the mix of ion channels in its membrane. During attention and exploration, acetylcholine changes that response: it closes several K⁺ currents and is thought to recruit a Ca²⁺-activated non-specific cation current (I<sub>CAN</sub>). The change is thought to help neurons hold information for seconds after a brief input, and cholinergic signaling declines in Alzheimer’s disease.</p>
    <p>The team had whole-cell current-clamp recordings of CA1 cells responding to hyperpolarizing and depolarizing current steps, in control and under carbachol (CCh), where the cells switch into persistent firing that outlasts the input. They wanted a model that answers their research questions channel by channel, makes predictions they can test in future experiments, and provides figures for a grant application.</p>

    <h3>The research questions</h3>
    <ol>
      <li><strong>What range of ion-channel settings produces the characteristic CA1 firing pattern?</strong> Many combinations of channels can produce the same responses, so the goal was to find the rules by which these neurons balance their channels to keep their signature, and which parameters control which features of the response, such as the latency to the first spike, the firing frequency and the symmetry of the intervals between spikes.</li>
      <li><strong>What does acetylcholine change?</strong> The same questions for cholinergic modulation: what are the key channel changes that turn the control cell into the CCh cell, including the role of a non-specific I<sub>CAN</sub> current, and which changes are responsible for which features of the altered firing?</li>
    </ol>

    <h3>The problem</h3>
    <p>A detailed neuron model has dozens to about a hundred free parameters, far more than anyone can picture at once. Fitting it once gives one answer out of many equally good ones, and a free refit of every parameter to the CCh recordings would match the data and explain nothing. The challenge was to show the space of good solutions in a way that reveals its rules: whether it forms one cluster or several, where its boundaries and holes are, and which parameters actually matter.</p>

    <h3>What I did</h3>
    <ul>
      <li><strong>Started from a published model.</strong> I used the detailed CA1 model of Tar et al. [1] in NEURON, with a reconstructed morphology and the recorded current replayed at the soma. Where it was generic, I replaced parts with literature-based components, such as CA1-specific sodium channels [2, 3] and a realistic axon [4].</li>
      <li><strong>Built a reproducible fitting pipeline in Python.</strong> Each cell is fitted to its own responses to current steps. The objective scores what an electrophysiologist reads off the trace, feature by feature: spike count and timing, spike shape along the train, and the activity after the input ends. A bounded CMA-ES optimizer runs on a SLURM cluster as one chained job per cell (control fit, then CCh fit), with many independent runs, so the result is a population of good solutions rather than a single one.</li>
      <li><strong>Mapped the space of good solutions (question 1).</strong> UMAP projections show whether good solutions form one cluster or several, and where the gaps are. To make the map readable, I chose axes from the data: parameter-importance analysis (fANOVA) drops the parameters that barely affect the fit, sensitivity analysis groups correlated parameters into combined directions, and colouring by the fitting error shows the boundary between parameter sets that reproduce the cell and those that don’t.</li>
      <li><strong>Linked parameters to features.</strong> Because the objective is scored feature by feature, the same analysis shows which parameters drive each part of the response, giving predictions the team can test in future experiments.</li>
      <li><strong>Fitted acetylcholine as a sparse change (question 2).</strong> CCh parameters are expressed as changes relative to control, so each one reads as a concrete effect of acetylcholine. A penalty on every departure from control makes the fit report the smallest change the data demand, and effects count only if they agree across runs, settings and cells. Built-in controls guard against artefacts, including a virtual I<sub>CAN</sub> block that checks whether the persistent firing truly depends on it.</li>
    </ul>

    <h3>Result</h3>
    <ul>
      <li><strong>A readable map of the control phenotype.</strong> The team can see which parameters the recordings constrain, which trade off against each other, and which can’t be pinned down from this data alone.</li>
      <li><strong>A minimal, consistent account of the cholinergic switch.</strong> The model reproduces both the control and the CCh recordings with a small set of channel changes that hold across cells and fitting runs.</li>
      <li><strong>Predictions for the next experiments.</strong> Links between specific channels and specific features of the firing, for both questions, that the team can now test in the lab.</li>
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
