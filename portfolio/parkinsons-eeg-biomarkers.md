---
layout: default
title: Resting-state EEG biomarkers of cognitive decline in Parkinson's disease
permalink: /portfolio/parkinsons-eeg-biomarkers/
description: A reproducible resting-state EEG pipeline tested on 522 recordings from four independent datasets to spot cognitive decline in Parkinson's disease earlier.
---

<div class="section">
  <div class="container case-study">
    <p><a href="{{ '/portfolio/' | relative_url }}" style="color:var(--primary); text-decoration:none;">&larr; Portfolio</a></p>
    <h2 style="font-size: 2.2rem; color: var(--secondary); line-height: 1.25; margin-bottom: 10px;">Resting-state EEG biomarkers of cognitive decline in Parkinson’s disease</h2>
    <p class="tags">Clinical EEG · Parkinson’s disease · Biomarkers · Reproducible pipeline · Python · Machine learning</p>

    <p style="font-size: 1.2rem; font-style: italic; color: var(--secondary);">A few minutes of EEG at rest, analyzed with a pipeline tested on 522 recordings from four independent datasets, to help a major Argentine public hospital spot cognitive decline in Parkinson’s patients earlier.</p>

    <h3>Context</h3>
    <p>Parkinson’s is best known for tremor, but loss of memory and thinking is often what disrupts patients’ lives most. About 1 in 4 patients without dementia already has mild cognitive impairment [1], and 83% of those who live 20 years with the disease develop dementia [2]. Argentina has between 100,000 and 220,000 people with Parkinson’s [3, 4], and the number will grow as the population ages [5]. At Hospital Interzonal General de Agudos (HIGA) “General José de San Martín” in La Plata, the province’s leading high-complexity public hospital, that burden is concrete. The hospital handled 208,294 outpatient visits in 2022 and serves about 1.1 million people across La Plata and ten nearby districts [6, 7], an area with an estimated 2,400–5,300 Parkinson’s patients. Up to 1,400 of them may already show early cognitive decline. Testing each of them with full neuropsychological assessments isn’t feasible at that scale. Resting-state EEG is a realistic alternative: a few minutes of recording with the patient simply sitting still, using equipment the hospital already has. It’s cheap, non-invasive, and can be repeated at every visit to track decline and response to treatment.</p>

    <h3>The problem</h3>
    <p>Parkinson’s is known to slow the brain’s resting rhythms, shifting activity toward slow <em>theta</em> waves and away from faster <em>alpha</em> and <em>beta</em> waves. But most EEG studies in this area use one small group of patients, fixed frequency bands and simple measures, and many of their findings don’t hold up when others try to replicate them. Before a hospital can rely on an EEG marker, two questions need answers: <strong>which Parkinson’s-related EEG changes are consistent across independent patient groups, and do any of them track thinking ability (MoCA score) or movement severity (UPDRS score) once age and sex are taken into account?</strong></p>

    <h3>What I did</h3>
    <p>I built a single, reproducible analysis pipeline and applied it, with the same processing steps and only minimal per-dataset adjustments (for example, line-noise frequency and channel layout), to four public resting-state EEG datasets: <a href="https://openneuro.org/datasets/ds004584">ds004584</a>, <a href="https://openneuro.org/datasets/ds007526">ds007526</a>, <a href="https://openneuro.org/datasets/ds008768">ds008768</a>, and the <a href="https://openneuro.org/datasets/ds002778">UC San Diego dataset (ds002778)</a>, in which 15 PD participants were recorded both on and off medication. After removing people who appeared in more than one dataset, that gave 522 recordings from 487 participants. The pipeline:</p>
    <ul>
      <li>Cleans the signal by removing eye, muscle and other artifacts (ICA), with a person reviewing each removal.</li>
      <li>Measures power at each frequency (Welch spectra and relative band power).</li>
      <li>Separates true brain rhythms from background noise (aperiodic 1/f fitting with specparam).</li>
      <li>Finds when each rhythm is actually present, burst by burst (eBOSC).</li>
      <li>Measures how organized those bursts are (ordinal entropy, complexity and Fisher information).</li>
      <li>Lets the data define the frequency bands using rhythmicity instead of fixing them in advance (LAVI/ABBA).</li>
      <li>Tests links to MoCA and UPDRS adjusted for age and sex, and trains conservative classifiers checked with nested cross-validation.</li>
    </ul>

    <h3>What we found</h3>
    <ul>
      <li><strong>More theta in Parkinson’s, consistently.</strong> Theta power rose in Parkinson’s patients in all three patient-versus-control datasets, across almost the whole scalp in two of them (62 of 66 and 58 of 60 electrodes).</li>
      <li><strong>Theta comes in more and longer bursts.</strong> This held after correcting for multiple comparisons, in the full group and in a group matched for age and sex.</li>
      <li><strong>For cognition, how theta activity is organized seems to matter more than how much of it there is.</strong> In ds004584, patients whose theta bursts were more varied and complex had better MoCA scores (ρ ≈ 0.30, q ≈ 0.04, adjusted for age and sex). The amount of theta (bout count, duration and occupancy) showed no link to MoCA (|ρ| ≤ 0.14). The largest independent cohort, ds007526, showed the same pattern: more complex theta bursts went with better cognition, while theta amount again showed no association. There the effect was weaker (ρ ≈ 0.20) and did not survive correction for multiple comparisons. The two smaller cohorts were inconclusive: ds008768 has only 24 PD patients with MoCA scores, and UCSD measured cognition with MMSE in 15 patients. This is the most promising marker for cognition, but it has to be tested on an independent dataset too.</li>
      <li><strong>Some expected markers didn’t hold up.</strong> Lower beta looks secondary and possibly affected by medication, and the background-noise (aperiodic) measures showed no consistent difference.</li>
      <li><strong>Telling patients from controls is possible but modest:</strong> an ROC AUC of about 0.74, where 0.5 is chance and 1.0 is perfect. Resting-state EEG is best used as a screening aid alongside clinical assessment, not a stand-alone test.</li>
    </ul>

    <h3>What the team can reuse</h3>
    <p>The work doesn’t depend on me to keep going:</p>
    <ul>
      <li><strong>A trained analyst.</strong> I trained a team member to understand every step of the pipeline and run the full analysis on their own, from raw EEG to results. New patient data can be processed in-house without outside help.</li>
      <li><strong>Open, reusable code.</strong> The complete pipeline is in a public repository. Because it runs from a single command, the same analysis can be repeated on new recordings, extended with new measures, or checked independently by others.</li>
    </ul>

    <h3>References</h3>
    <ol class="references">
      <li>[1] Aarsland D, et al. Mild cognitive impairment in Parkinson disease: a multicenter pooled analysis. <em>Neurology</em>. 2010;75(12):1062–1069.</li>
      <li>[2] Hely MA, et al. The Sydney multicenter study of Parkinson’s disease: the inevitability of dementia at 20 years. <em>Movement Disorders</em>. 2008;23(6):837–844.</li>
      <li>[3] Argentine Ministry of Health estimate, cited in <em>Los Andes</em>, April 2025.</li>
      <li>[4] Kim DJ, et al. Prevalence and incidence of Parkinson’s disease in Latin America: a meta-analysis. <em>Movement Disorders</em>. 2023.</li>
      <li>[5] United Nations World Population Prospects 2022, cited in <em>Forbes Argentina</em>, July 2023.</li>
      <li>[6] Hospital San Martín La Plata: La Institución.</li>
      <li>[7] INDEC Census 2022, compiled in <em>Población, superficie y densidad por partido</em> (SCBA, 2024).</li>
    </ol>
  </div>
</div>
