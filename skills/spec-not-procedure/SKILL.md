---
name: "spec-not-procedure"
description: "Use when briefing an agent (or a fresh context) on an open problem where a single metric could be gamed or an old solution copied, such as modelling, analysis, design or code."
---

# Spec, not procedure

## When to use

Briefing an agent (or a fresh context) on an open problem where a single metric could be gamed or an old solution copied, such as modelling, analysis, design or code.

## Core idea

Instead of telling Claude what to do, the user should give it a goal, a way to measure it, and constrain the search space. That way you don't bake in your own biases or previous attempts.

- Define ways to measure as many parts of the problem as possible, using different evidence for each, so that Claude can't game the primary metric. Measuring instruments should be fixed.
- A constraint should rule things out, not pick a winner. Give reasons for the constraints to stop Claude blindly following them. Include how strictly the constraints should be adhered to.
- Do not bias towards a particular solution.
- Include facts about the data, system or domain.
- One goal at a time is best.

## Template

- **Goal:** what to produce and why.
- **Fixed:** inputs, definitions and conventions, frozen parts, stated facts, available tooling.
- **Admissible solutions:** constraints on the form of the answer, each with its reason; assumptions allowed only with a stated reason.
- **Measurements:** each tied to an independent source of evidence, with exact definitions so they can be rerun on other candidates; held-out or out-of-sample where possible.
- **Failure conditions:** results that disqualify a candidate even if the scores improve.
- **Reporting:** one row per candidate; trade-offs rather than one winner; which failure conditions each hits; what is untested; the artefacts (code, files, visuals) needed to rerun the measurements.

## Checklist before handing over

- Is every term defined for a reader with no context?
- Does every measurement say where its evidence comes from?
- Could any constraint be read as the answer in disguise?
- Is anything from an old solution (numbers, choices, code, chat context) leaking in?
- For multi-part work: one part per step, stop for review after each, freeze what is done?

## Example

### Goal

Model per-muscle mean fibre length as l = g(θ) − stretch(F), where θ is the elbow-joint angle and F is the summed fibre force.

The length model will form part of a low-fidelity physics model of an FE HBM arm with a hybrid muscle modelling approach, where only the elbow joint can move freely.

The model cannot violate an admissible mechanism or failure condition.

### Fixed

Data from 60 simulations of the FE arm model, where only the elbow joint can move freely.

Simulations ran for 1000 ms, where a fixed, constant stimulation (which is included in the simulation data) was applied to each muscle at t = 0. Time-series data includes:

- per-muscle mean fibre length (mm)
- summed fibre force (N)
- x-y elbow-joint angle (degrees, 0° = straight arm, flexion positive)

10 muscle series (biceps and triceps each split into two heads) with fibre groupings as in `arm_data.py`.

Only use samples from 20 ms onwards (start-up transient before that).

Stated facts:

- Muscle fibres have no force–velocity term.
- SEE is off in the FE model fibres, so any series compliance must come from the 3D mesh.
- The two biceps heads share one stimulation, as do the two triceps heads. The deck has one l_opt for both triceps heads.

### Admissible mechanisms

The stretch must:

- come from a series-elastic element in Hill-type models, or be derivable from soft-tissue mechanics;
- grow with force, its stiffness staying positive, because a series spring cannot get shorter under more load;
- have interpretable parameters, and the same form for all muscles.

g(θ) must give moment arms with the right sign everywhere and a bounded, smooth shape where data are sparse (below 20°, above ~100°).

g(θ) must be fitted to lengths only; torque data are for checking.

You can make simplifying assumptions if there is a physical reason to do so.

### Measurements

- **Lengths:** leave-one-run-out length RMS per muscle.
- **Low-activation offset:** mean length residual at 800–1000 ms in runs where the muscle's own stimulation is < 0.05.
- **Torque check of the moment arms:** residual over the swings of all runs, 20–990 ms, every 5 ms: e(t) = I·θ̈ + c·θ̇ − [Σᵢ rᵢ(θ)·F_FE,i(t) + τ_p(θ)].
  - θ = x-y elbow angle. θ, θ̇, θ̈ from the 10 ms moving-average angle, differentiated numerically. F_FE,i = summed fibre `f_mtu`, 10 ms moving average.
  - I = forearm inertia, c = joint damping, τ_p(θ) = passive elbow torque (everything not carried by the fibres).
  - τ_p, I and c are part of the measurement, not of the model being designed. For every candidate, refit them by the same procedure: τ_p as a natural cubic spline in θ with knots every 20° from 0° to 120°, fitted together with I and c by linear least squares on the training runs' swings. Do not change this procedure.
  - Do not reuse a τ_p fitted with other moment arms: it would favour those moment arms.
  - Score on held-out runs (leave-one-run-out): τ_p, I and c fitted on the training runs, e evaluated on the held-out run.
  - Also report e at each run's EP (mean of 800–1000 ms) and its correlation with co-contraction C.
  - No baseline is given and none is needed: results are compared with a reference model afterwards, outside this task, using exactly this procedure. Report absolute values.
  - Limitation: τ_p absorbs any moment-arm error that depends on angle only, so this checks r only for force-dependent errors; τ_p itself is not validated.
- **End-to-end check only:** EP and trajectory RMS of the physics model (without any discrepancy model).

### Failure conditions

- Stretch decreases with force anywhere in the observed force range, for any muscle.
- Negative or zero series stiffness.
- A moment arm with the wrong sign, or one that swings sharply where there are no data.
- Better length error bought with a worse torque balance. That means the split between angle and stretch is wrong.
- Parameters that are physically implausible: stretch larger than about 10% of l_opt, or stiffnesses that differ by orders of magnitude between similar muscles.

### Reporting

- Fitted length curves versus angle.
- RMS of length per muscle (in mm and %).
- Mean low-activation offset.
- RMS of e over the swings (moment-arm score).
- Trade-offs of modelling approaches.
- Plot of FE model EP vs physics model EP.

Results should be presented visually in HTML format, where possible. Visuals should be understandable without accompanying text.