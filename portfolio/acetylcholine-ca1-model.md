---
layout: default
title: Modelling how acetylcholine switches a hippocampal neuron into persistent firing
permalink: /portfolio/acetylcholine-ca1-model/
description: A detailed CA1 pyramidal neuron model fitted to control and carbachol recordings to find which ion-channel changes explain the switch into persistent firing.
---

<div class="section">
  <div class="container case-study">
    <p><a href="{{ '/portfolio/' | relative_url }}" style="color:var(--primary); text-decoration:none;">&larr; Portfolio</a></p>
    <h2 style="font-size: 2.2rem; color: var(--secondary); line-height: 1.25; margin-bottom: 10px;">Modelling how acetylcholine switches a hippocampal neuron into persistent firing</h2>
    <p class="tags">Computational neuroscience · Biophysical modelling · NEURON · Parameter fitting (CMA-ES) · HPC · Python</p>

    <p style="font-size: 1.2rem; font-style: italic; color: var(--secondary);">A detailed model of a hippocampal CA1 pyramidal neuron, fitted to recordings made before and during the cholinergic agonist carbachol, to find which ion-channel changes explain the switch into persistent firing, and to check that the explanation holds across cells and fitting runs.</p>

    <h3>Context</h3>
    <p>CA1 pyramidal neurons carry most of the output of the hippocampus, the brain region that forms new memories. During attention and exploration, acetylcholine changes how these cells respond. Through muscarinic M1 receptors it closes several K⁺ currents and recruits a Ca²⁺-activated cation current (I<sub>CAN</sub>). In CA1 this current is carried mainly by the TRPM4 channel [1]. The change is thought to help neurons hold information for seconds after a brief input, and cholinergic signaling declines in Alzheimer’s disease.</p>
    <p>The team had whole-cell current-clamp recordings of CA1 cells in control and under carbachol (CCh). Under CCh the cell keeps firing through the current step, the voltage between spikes climbs, and spikes shrink and broaden. When the step ends, the membrane stays depolarized on a plateau for about 100–250 ms. A TRPM4 blocker removes the ramp and the plateau. The team wanted a model that explains these changes channel by channel, makes testable predictions, and provides figures for a grant application.</p>

    <h3>The problem</h3>
    <p>A detailed neuron model has dozens to about a hundred free parameters, and many different combinations reproduce the same voltage traces. A free refit of every parameter to four CCh current steps would match the data and explain nothing. A useful model had to answer three questions: which channels do the control recordings actually constrain; what is the smallest set of changes that turns the control cell into the CCh cell, and is it the same in different cells; and does the post-step plateau really depend on I<sub>CAN</sub>, as the blocker experiments suggest, rather than on a fitting artefact such as depolarization block?</p>

    <h3>What I did</h3>
    <ul>
      <li><strong>Started from a published model.</strong> I used the detailed CA1 model of Tar et al. [2] in NEURON: a reconstructed morphology (556 segments) with spines folded into the dendrites, simulated at 33 °C with the recorded current replayed at the soma.</li>
      <li><strong>Built a reproducible fitting pipeline in Python.</strong> Each individual cell is fitted on four depolarizing steps and one hyperpolarizing pulse. The objective scores what an electrophysiologist reads off the trace: spike count and timing, spike height and width along the train, the rise of the troughs, and the level, duration and decay of the plateau. A bounded CMA-ES optimizer runs on a SLURM cluster as one chained job per cell (control fit → best seed promoted → CCh fit), with 10 seeds per setting.</li>
      <li><strong>Mapped the parameter landscape.</strong> fANOVA importance over 5,000 fits, UMAP projections and local stiff/sloppy sensitivity analysis show which parameters the data pin down and which trade off against each other.</li>
      <li><strong>Replaced generic parts with biology.</strong> Where the original model was generic, I replaced it with literature-based components: Nav1.2 and Nav1.6 sodium channels with CA1 expression shares [3, 4], an axon initial segment and a myelinated axon with nodes [5], TRPM4 on the soma and apical trunk [6], and TRPM4 fed by a Ca²⁺ nanodomain at voltage-gated Ca²⁺ channels, not by internal Ca²⁺ stores, which experiments rule out in CA1 [1].</li>
      <li><strong>Made parameters readable.</strong> Control parameters are physical values in standard units that can be typed into a channel file. CCh parameters are scale factors and voltage shifts relative to control, such as “×0.3 M-current” or “+5 mV”.</li>
      <li><strong>Fitted CCh as a sparse change.</strong> A penalty on every departure from control makes the fit report the smallest change the data demand. Effects count only if they agree across seeds, penalty strengths and both cells. Built-in controls guard against artefacts: all spiking currents stay on, and a virtual TRPM4 block (I<sub>CAN</sub> off) must lower the plateau by at least 10 mV, mirroring the blocker experiment.</li>
    </ul>

    <h3>Result</h3>
    <ul>
      <li><strong>Control firing is set by a few channels.</strong> In the first cell analysed, five parameters explain about 75% of the fit variance: the dendritic delayed rectifier, the A-type K⁺ current, fast Na⁺ inactivation and activation, and M-current activation. Most Ca²⁺-activated and afterhyperpolarization conductances are weakly constrained and compensate for each other.</li>
      <li><strong>The CCh firing needs two changes at once:</strong> a strong reduction of K⁺ conductances (M-current, slow AHP, BK and K⁺ leak, to about 1–18% of control) and slow Na⁺ inactivation that builds up between spikes. Neither works alone. The M-current is what caps depolarization in the control cell: removing it lets a step depolarize the cell 4–8 mV further, while removing any other K⁺ current changes it by 2 mV or less.</li>
      <li><strong>The two cells agree</strong> on the direction and size of the main changes: the K⁺ blocks, the Kv2 increase, the Na⁺ inactivation shifts and the slow closing of I<sub>CAN</sub>.</li>
      <li><strong>The plateau is a dendritic TRPM4 effect.</strong> Early versions locked into endless firing. I traced this to a Na⁺ window current in the dendrites and fixed it with long-term Na⁺ inactivation. The model then produced the first true plateau, from a TRPM4 hot spot on the apical trunk with a Ca²⁺ sensitivity (K<sub>d</sub> ≈ 0.2 µM) consistent with an independent estimate from resting-potential data [6].</li>
      <li><strong>Dead ends were documented, not hidden.</strong> These include an internal-store mechanism that the literature does not support, and fits that silently switched TRPM4 off and faked the plateau with depolarization block. Both are now caught by the pipeline’s checks.</li>
    </ul>

    <h3>What the team kept</h3>
    <ul>
      <li>A self-contained model package: morphology, channel mechanisms, recordings, objective and optimizer, with one command per stage on the cluster.</li>
      <li>Written documentation of the model, its parameters and their sources, the CCh results log, and channel kinetics estimated directly from the recordings.</li>
      <li>Grant figures comparing model and recordings, and a parameter-importance map.</li>
      <li>A set of checks for any future CCh model: all currents on, a virtual TRPM4-block replay, and agreement across seeds and cells before a change is reported.</li>
    </ul>

    <h3>References</h3>
    <ol class="references">
      <li>[1] Combe CL, et al. (2023). <em>eLife</em> 12:e84387.</li>
      <li>[2] Tar L, et al. (2026). Detailed CA1 pyramidal cell model, Káli group.</li>
      <li>[3] Royeck M, et al. (2008). <em>J Neurophysiol</em> 100:2361–2380.</li>
      <li>[4] Lorincz A, Nusser Z (2008). <em>J Neurosci</em> 28:14329–14340; (2010) <em>Science</em> 328:906–909.</li>
      <li>[5] Cohen CCH, et al. (2020). <em>Cell</em> 180:311–322.</li>
      <li>[6] Riquelme D, et al. (2021). <em>Front Cell Neurosci</em> 15:614540.</li>
    </ol>
  </div>
</div>
