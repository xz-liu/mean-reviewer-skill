# Mean Reviewer Post-Rebuttal Response
# Submission 13446 — "1000 Layer Networks for Self-Supervised RL"

I have read the authors' rebuttal. It is long. It addresses some of the easier points in my review. It does not address the core concerns, and several of the "responses" I received are not responses at all — they are restatements of claims already in the paper, now accompanied by an acknowledgement that the claim has the limitations I identified. That is not the same as resolving those limitations.

My score remains **3/10**.

---

## On the New Experiments

The authors have provided new tables for GCBC offline scaling, architecture ablations (LayerNorm, Swish), Simba-v2 integration, and FLOPs/memory comparisons. I appreciate the effort. Unfortunately, none of these results address the fundamental concerns I raised, and several of them raise new questions.

**On GCBC offline scaling (Table A in the rebuttal):** The authors present GCBC offline depth scaling on *antmaze-medium-stitch* as evidence that "architectural choices enable offline scaling." I note the following:

1. This is a single dataset on a single OGBench task. The paper's Section 4.6 showed offline CRL fails in 2/3 environments. The authors have now added one positive result for a completely different algorithm on one task. This is not a rebuttal of my concern — it is a cherry-picked new result designed to provide cover. The question was whether the authors' *approach* scales offline. The answer remains: the CRL method, which is the paper's contribution, does not.

2. The GCBC improvement is from 0.474 to 0.634 — an improvement of approximately 0.16 over five depth levels. The authors describe this as "substantial." A performance gain of 0.16 absolute from depth 2 to depth 32, across a single offline task, is not compelling evidence for a general claim about offline scaling.

3. The standard architecture comparison (Table A, row 2) shows performance collapsing to 0.210 ± 0.210 at depth 32 — meaning the standard deviation is equal to the mean, which indicates near-total instability. This is presented as validation that "the architecture is critical." What it actually shows is that depth scaling in offline settings is sensitive and fragile, precisely the concern I raised. The authors have reframed fragility as a positive result.

**On the FLOPs/memory comparison (Table B):** The authors show that depth 64 uses 66.2B FLOPs versus 1039.9B for width 4096. I accept these numbers. However, the relevant comparison for my concern was not depth-64 versus width-4096 — it was depth-64 versus width-2048, which is the widest baseline the paper benchmarks. At width 2048, FLOPs are 262.4B and memory is 673.3 MB. The comparison is less dramatic than the authors imply. And in any case, the FLOPs argument does not address the central issue: that Scaled CRL uses 15× the wall-clock time of SAC on the two environments where it fails. FLOPs are not wall-clock time.

**On the architectural ablations (Table C):** These results are informative. I do not dispute that LayerNorm and Swish are essential components. However, the authors provided these as evidence to address my Point 21, which asked why residual connections specifically enable depth scaling in the CRL setting. The ablations show that residuals + LayerNorm + Swish are jointly essential. That is consistent with my original concern — the paper identifies necessary components without explaining them — and the rebuttal has not provided a mechanistic account. "All three are necessary" is not an explanation for why any of them work.

---

## On the Points the Authors "Accepted"

The authors list 9 points where they "accept the critique and will revise." I find this list notable for what it reveals rather than what it resolves.

The authors have accepted: that the 1051× figure is misleading (Point 4), that non-monotonicity is a real limitation they understated (Point 5), that the 1024-layer headline claim overstates what was achieved (Point 7), that the MJX/Brax discrepancy should be disclosed more prominently (Point 17), that the stitching result is preliminary (Point 18), that the abstract misrepresents the result distribution (Point 19), that the statistical presentation is insufficient (Point 22), and that qualitative behavior claims are not quantified (Point 23).

I want to be direct: this list is a concession that several of the paper's most visible claims are overstated, its headline experiment is not fully realized, its main qualitative results are not quantified, and its statistical reporting is misleading. These are not minor editorial corrections. These are substantive problems with the paper as submitted.

The authors describe these as things they "will fix in the revision." But a submitted NeurIPS paper should not contain a misleading headline result, unquantified behavioral claims presented as contributions, and a misrepresented abstract. The fact that they will fix these things in revision is precisely the argument for not accepting the paper in its current form.

---

## On the Points the Authors Contested

**Point 2 (JaxGCRL co-authorship):** The authors argue that the benchmark is "publicly available," "independently published," and that the task environments are "standard." I find this response unconvincing. The issue is not whether JaxGCRL was formally published. The issue is that the first author of the benchmark is a co-author of this paper, and the benchmark was used exclusively — with no evaluation on any other benchmark — for the primary results. The authors acknowledge this is a "legitimate long-term direction" (i.e., running on other benchmarks) but call it "impractical within the rebuttal period." I note that this response implicitly acknowledges that the remedy I identified — evaluation on independent benchmarks — has merit. The impracticality of doing it now does not make the concern invalid.

**Point 6 (Offline failure):** The authors argue that CRL's offline failure is "algorithm-specific" and that GCBC scales offline. I have addressed the GCBC results above. On the main point: the authors describe the offline failure as informative because it "tells us something about the boundary conditions of contrastive RL." I am not persuaded that a failure one does not understand constitutes a contribution. The paper does not explain why CRL fails offline. It fails; the authors acknowledge it fails; they say future work should investigate. A paper titled to suggest general scaling principles for RL should not have its primary algorithm fail in the offline setting with no explanation provided.

**Point 8 (SAC comparison across reward structures):** The authors now claim that SAC on Humanoid U-Maze uses "carefully engineered reward shaping" that gives it an unfair advantage over CRL's sparse binary reward. This is a significant claim that does not appear in the paper as submitted. If the Humanoid environments use different reward functions for SAC versus CRL, this is a critical methodological detail that the paper buries. The paper's Table 6 presents the comparison as if SAC and Scaled CRL are operating in the same setting. I do not accept the post-hoc reframing of an unfavorable comparison as a "different problem formulation" unless this distinction was clearly stated in the paper. It was not.

---

## On the Points the Authors Partially Accepted

I note that Points 12 (InfoNCE batch-size confound) and 16 (collector experiment confound) were "partially accepted" with the note that the experiments needed to isolate these confounds were not run. These are the experiments I asked for in my Demands section. The authors have confirmed they have not been run. My concern regarding the InfoNCE confound remains unresolved.

I similarly note that Point 14 (baselines not adequately scaled) received a response confirming that the paper cannot explain why depth helps CRL but not SAC/TD3. The reviewer response is: "We have not fully disentangled them." This is the paper's core explanatory gap, and the rebuttal has confirmed it rather than addressed it.

---

## On the Points That Were Not Addressed

The rebuttal does not provide:

- Any results on benchmarks with no author overlap (demanded, confirmed impractical, concern stands).
- Binary success rate alongside Time at Goal for existing experiments (promised for revision; not provided in rebuttal).
- Any controlled experiment separating depth benefits from batch-size benefits under matched FLOPs (confirmed absent; concern stands).
- Any quantified behavioral frequency for vaulting or seated locomotion behaviors (promised for revision; not provided in rebuttal).
- Any comparison to model-based RL (acknowledged absent; concern stands).
- Any explanation of why offline CRL fails (none provided; concern stands).

The rebuttal has confirmed that the majority of my experimental demands were not met. The authors have, to their credit, promised to fix several of these in the revision. I do not believe promises of future revisions should change my current assessment of the submitted work. I am reviewing what was submitted.

---

## Final Assessment

I have read the rebuttal carefully. The authors have confirmed that:
- The headline 1024-layer result is not fully realized (the actor is capped at 512 layers).
- The headline improvement ratio (1051×) is misleading and will be removed.
- The stitching result is preliminary and overstated.
- The abstract misrepresents the result distribution.
- The core algorithm fails offline with no mechanistic explanation.
- The InfoNCE/batch-size confound has not been experimentally disentangled.
- The baselines were not evaluated with scaled architectures.
- The behavioral claims are not quantified.

This is a substantial list of conceded problems. None of the new experiments provided in the rebuttal resolve the fundamental concerns: the scope of the contribution is narrower than claimed, the analysis is less conclusive than presented, and the paper as submitted contains multiple overstated claims that the authors now acknowledge.

Other reviewers may find the empirical improvements compelling enough to outweigh these concerns. I do not. A paper that discovers depth scaling works for one specific self-supervised RL algorithm on one benchmark, admits it does not understand why, admits it fails offline, admits the headline architecture is unstable, and admits its behavioral claims are unquantified — is not ready to establish "scaling principles" for reinforcement learning.

**Score: 3/10. Confidence: 5/5.**

I will not be raising my score. The rebuttal has, if anything, increased my clarity about the gap between what the paper claims and what it demonstrates.
