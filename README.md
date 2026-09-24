# Matrix-Free Covariance Response for DA-Ready Weather Ensembles

<p align="center">
  <b>A lightweight, observation-centered diagnostic for asking whether weather ensembles carry covariance structure useful for data assimilation.</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/NeurIPS%202026-GlobalSouthAI-blue" alt="NeurIPS 2026 GlobalSouthAI">
  <img src="https://img.shields.io/badge/Data%20Assimilation-Covariance%20Response-006699" alt="Data Assimilation">
  <img src="https://img.shields.io/badge/Weather-GEFS-4B8BBE" alt="GEFS">
  <img src="https://img.shields.io/badge/Method-Matrix--Free-success" alt="Matrix-Free">
  <img src="https://img.shields.io/badge/Python-3.12%2B-blue" alt="Python 3.12+">
  <img src="https://img.shields.io/badge/License-MIT-yellow.svg" alt="MIT License">
</p>

---

## Matrix-Free Covariance-Response Approach for Data Assimilation-Ready Weather Ensembles

**Himanshu Singh¹ · Steven J. Greybush¹ · Laura E. Dailey¹ · Melissa Adrian² · Romit Maulik²**

¹ Department of Meteorology and Atmospheric Science, The Pennsylvania State University  
² School of Mechanical Engineering, Purdue University

**Accepted at GlobalSouthAI @ The Fortieth Annual Conference on Neural Information Processing Systems (NeurIPS 2026)**

---

## 🌦️ The Question

AI weather models are increasingly capable of generating fast and inexpensive forecasts and ensembles.

But an ensemble intended for **data assimilation (DA)** needs to do more than produce accurate forecasts or realistic ensemble spread.

Its perturbations must also encode meaningful **background-error covariance structure**.

That raises a practical question:

> **Before paying the computational cost of full data-assimilation cycling, can we cheaply test whether an ensemble spreads observational information in a DA-relevant way?**

This work develops a lightweight, observation-centered diagnostic for addressing that question.

---

## 💡 Main Idea

Rather than explicitly constructing and storing an enormous background-error covariance matrix, we ask a much more targeted question:

> **How does the ensemble covariance respond to a localized, observation-like perturbation?**

The method evaluates the **action of the covariance** directly from ensemble perturbations.

This makes the calculation **matrix-free** and reduces the computational requirement from dense covariance storage/application to a calculation that scales with the state dimension and ensemble size.

The result is a diagnostic designed to sit **before expensive complete DA cycling**.

---

## 🧭 Method at a Glance

The framework consists of three steps.

### 1. Probe the ensemble covariance

A localized observation-like probe is introduced at a selected location.

Instead of constructing the full covariance matrix, the method directly evaluates how the ensemble covariance acts on that probe.

The resulting spatial response asks:

> **If information enters here, where does the ensemble covariance say that information should propagate?**

### 2. Build historical covariance responses

Historical ensemble states provide an archive of covariance responses.

A simple static baseline treats historical cases equally.

### 3. Condition on the current atmospheric state

Not every historical atmosphere is equally relevant to the current flow.

The proposed query-conditioned construction therefore assigns greater influence to historical cases whose meteorological descriptors more closely resemble the current atmospheric state.

This produces a **flow-conditioned covariance response** while retaining the matrix-free computation.

---

## ⚡ Why Matrix-Free?

High-dimensional weather states make explicit covariance matrices expensive to construct, store, and manipulate.

The proposed diagnostic avoids that bottleneck.

### Dense covariance approach

**Storage/application scaling:** `O(K²)`

### Matrix-free covariance response

**Cost and memory scaling:** `O(K × Nₑ)`

where `K` is the state dimension and `Nₑ` is the ensemble size.

This distinction becomes increasingly important as weather models and AI-generated ensemble states move toward very high spatial resolution.

---

## 🛰️ GEFS Demonstration

The method is demonstrated using **NOAA/NCEP Global Ensemble Forecast System (GEFS)** ensemble data and **500 hPa geopotential height (HGT500)**.

HGT500 provides a useful large-scale measure of mid-tropospheric circulation and weather-system structure.

The experiment compares:

- a **static historical covariance response**,
- a **flow-conditioned historical covariance response**, and
- the **query-time GEFS covariance response** used as the reference for the diagnostic.

---

## 🔬 What Does the Figure Show?

<p align="center">
  <img src="gefs_hgt500_q060_4panel_final_submit_fixedspacing.pdf" width="90%" alt="GEFS HGT500 matrix-free covariance-response experiment">
</p>

<p align="center">
  <i>
  Observation-centered covariance responses for GEFS HGT500:
  static historical averaging versus flow-conditioned historical weighting.
  </i>
</p>

The representative case considers a localized probe for the **8 June 2026 00 UTC GEFS case at +6 h lead**.

### (a) Static archive response

Historical covariance responses are averaged without accounting for the atmospheric state associated with the current query.

This produces a generic historical response.

### (b) Flow-conditioned response

Historical cases are weighted according to their meteorological similarity to the query state.

The resulting covariance response changes substantially, allowing atmospheric states that more closely resemble the current situation to exert greater influence.

### (c) Static-response error

Relative to the query-time GEFS covariance response, the static historical construction exhibits substantial localized disagreement.

### (d) Flow-conditioned error

Conditioning on meteorologically similar historical states reduces the localized response error in the illustrated case.

For this example, the reported **local gain is 1.212**.

### Historical weighting structure

The lower panel visualizes how historical GEFS cases are weighted as a function of the query state.

The structured weight distribution illustrates an important feature of the method:

> **Historical covariance information is not treated as equally relevant—the atmospheric flow determines which historical cases should matter most.**

---

## 🧠 Interpretation

The main point is not simply that one historical weighting strategy produces a smaller error in one example.

The broader idea is that **ensemble quality for data assimilation has a covariance dimension that is not captured by forecast RMSE or ensemble spread alone**.

A useful ensemble should encode how uncertainty propagates spatially and dynamically when observational information is introduced.

This motivates a hierarchy of evaluation:

**forecast accuracy → ensemble spread → covariance response → full DA cycling**

The proposed method targets the covariance-response stage as a relatively inexpensive diagnostic before committing to the final and substantially more expensive DA experiment.

---

## 🤖 Why This Matters for AI Weather Ensembles

Fast AI weather emulators can dramatically reduce forecast cost.

But inexpensive ensemble generation does not automatically guarantee that the resulting perturbations possess covariance structures suitable for assimilation.

The matrix-free covariance-response diagnostic provides a way to ask:

> **Does an AI-generated ensemble propagate observation-like information in a physically and statistically meaningful way?**

This makes the framework potentially useful as a screening tool for:

- learned weather ensembles,
- generative ensemble models,
- archive-based ensembles,
- reduced-cost ensemble systems, and
- conventional numerical weather prediction ensembles.

Importantly, this diagnostic is **not a replacement for complete data-assimilation cycling**. It is intended as a computationally lighter screen that can help identify ensemble configurations worth carrying forward to more expensive DA evaluation.

---

## 🌍 Resource-Aware Perspective

Full data-assimilation experiments can require substantial computational infrastructure.

A matrix-free diagnostic that avoids explicit high-dimensional covariance construction provides a possible intermediate evaluation stage for research groups or forecasting environments where repeated complete DA cycling is expensive.

The central computational philosophy is therefore:

> **Interrogate the covariance structure first. Run the expensive assimilation experiment when the ensemble has earned it.**

---

## 📄 Paper

### Matrix-Free Covariance-Response Approach for Data Assimilation-Ready Weather Ensembles

**Himanshu Singh, Steven J. Greybush, Laura E. Dailey, Melissa Adrian, Romit Maulik**

The Pennsylvania State University · Purdue University

**GlobalSouthAI @ NeurIPS 2026**

The paper introduces an observation-centered, matrix-free covariance-response diagnostic and demonstrates flow-conditioned historical covariance responses using GEFS HGT500 data.

---

## 💬 Peer-Review Highlights

The work was accepted following peer review at **GlobalSouthAI @ NeurIPS 2026**.

> **“The paper addresses a useful problem.”**  
> — Reviewer 4FDL

> **“The matrix-free calculation of the covariance action is straightforward and computationally efficient.”**  
> — Reviewer 4FDL

> **“Evaluating whether AI-generated weather ensembles are suitable for data assimilation is an important and timely problem.”**  
> — Reviewer 78i7

> **“The matrix-free formulation offers a practical scalability advantage for high-dimensional weather states.”**  
> — Reviewer 78i7

> **“The proposed method could serve as a lightweight screening step before expensive full data-assimilation cycling.”**  
> — Reviewer 78i7

### ⚡ Notable Reviewer Comment

> **“I would consider it suitable for a poster or lightning talk if the authors add results across multiple cases, clearly define the method and its parameters, and correct the dataset citation.”**  
> — Reviewer 4FDL

The reviews recognized the computational motivation, matrix-free scalability, observation-centered formulation, and relevance of evaluating covariance structure for AI-generated weather ensembles.

They also identified an important next step: **validation across more atmospheric regimes, probe locations, forecast leads, and ensemble systems.**

For transparency, the complete reviews — including limitations and critical feedback — are available on OpenReview.

---

## 🚧 Current Scope

This work should be interpreted as a **covariance-response diagnostic**, rather than as evidence that an ensemble is fully DA-ready.

The current study demonstrates the idea through a representative GEFS HGT500 case.

Full validation requires broader testing across:

- multiple weather regimes,
- multiple probe locations,
- different forecast lead times,
- larger historical archives,
- additional atmospheric variables,
- AI-generated or generative ensembles, and
- ultimately, complete data-assimilation cycling.

These are natural next steps toward determining how strongly covariance-response diagnostics predict actual downstream DA performance.

---

## 🔭 Research Direction

The longer-term question is broader than this particular diagnostic:

> **How should AI-generated weather ensembles be evaluated when their ultimate purpose is data assimilation rather than forecasting alone?**

Forecast RMSE and spread are necessary diagnostics, but DA introduces additional structural requirements.

Covariance response provides one possible bridge between **ensemble generation** and **assimilation utility**.

This creates a natural research progression:

**AI ensemble generation → uncertainty calibration → covariance structure → observation response → DA cycling → forecast impact**

---

## 📚 Citation

If this work is useful in your research, please cite:

```bibtex
@inproceedings{singh2026matrixfree,
  title     = {Matrix-Free Covariance-Response Approach for
               Data Assimilation-Ready Weather Ensembles},
  author    = {Singh, Himanshu and Greybush, Steven J. and
               Dailey, Laura E. and Adrian, Melissa and Maulik, Romit},
  booktitle = {GlobalSouthAI Workshop at NeurIPS 2026},
  year      = {2026}
}
```

---

## 📜 License

Code in this repository is released under the **MIT License**.

© 2026 Himanshu Singh and collaborators.
