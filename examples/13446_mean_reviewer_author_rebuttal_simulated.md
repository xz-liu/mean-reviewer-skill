# Author Rebuttal — NeurIPS 2025 Submission 13446
# "1000 Layer Networks for Self-Supervised RL: Scaling Depth Can Enable New Goal-Reaching Capabilities"

---

We thank the reviewer for a thorough reading of the paper. The review raises 24 points. We will address them as carefully as the rebuttal word limit allows. Some points receive more detailed treatment than others; we note explicitly where space forces us to be brief. We have read every point, and we do not dismiss any of them. Where the reviewer is right, we will say so. Where we believe the review mischaracterizes our results or our text, we will provide the evidence and ask the reviewer to reconsider.

We want to be direct about one thing upfront: this is computationally expensive work. Running 5 seeds on a 1024-layer configuration takes 134 hours per seed — 670 hours total, on a single 80GB A100. We made resource tradeoffs that we disclosed in the checklist. We believe the evidence we present, taken together, supports the core claims, even if it does not satisfy every methodological preference the reviewer holds. We hope the reviewer will engage with the evidence on its merits.

---

## Point 1: The "emergent capabilities" framing is not substantiated

The reviewer argues that the word "emergent" is imprecise or misleading.

We used this framing deliberately, in explicit reference to Wei et al. (2022), who defined emergent abilities as capabilities that are absent at smaller scale and appear sharply as scale increases. We believe the Humanoid results satisfy this definition in a meaningful operational sense. On Humanoid Big Maze, depth-4 through depth-16 networks score near zero. The transition to functional goal-reaching behavior occurs sharply between depth 32 and depth 64. We show four snapshots of qualitative behavior precisely to illustrate that this is not a gradual improvement but a categorical change in what the agent can do — it transitions from failing to walk to coordinating full locomotion through the maze. The depth-256 wall-vaulting behavior does not appear at any lower depth.

We acknowledge this framing is contested. We do not claim to have proven discontinuity in a formal statistical sense. We have shown that certain behaviors are not observed at lower depths in our experiments, and that the improvement curve has a qualitatively sharp character in several environments. We are willing to soften the language if the reviewer believes "emergent" carries implications we have not established — but we ask the reviewer to consider whether the underlying finding would be substantively different under milder phrasing.

---

## Point 2: The JaxGCRL benchmark shares an author with this paper

We take this concern seriously, and we want to be precise about the facts.

The JaxGCRL benchmark (Bortkiewicz et al., 2024) is a publicly released, independently published benchmark. It is not a proprietary benchmark developed by us for this paper. It was published prior to this submission, is publicly available, and is documented independently. The shared authorship is disclosed via citation throughout our paper. The benchmark was not designed around our method — it was designed to provide fast, parallelizable evaluation of goal-conditioned RL methods generally, using standard robotics environments (Ant locomotion variants, Humanoid locomotion variants, manipulation tasks) that are themselves standard across the GCRL literature.

We understand the reviewer's concern about potential conscious or unconscious tailoring. We believe the best response to this is transparency about what the benchmark contains: standard MuJoCo physics, standard observation spaces, standard goal-reaching evaluation. We also note that our experimental results include OGBench environments in the offline evaluation (Section 4.6), which are entirely independent of our team. We are willing to expand discussion of this in the paper.

We genuinely believe the reviewer's stated remedy — results on D4RL, Meta-World, or DMControl — is impractical within the rebuttal period given the compute involved, but we acknowledge this is a legitimate long-term direction.

---

## Point 3: "Time at Goal" is a non-standard metric

This is a valid critique and we want to explain the choice rather than simply defend it.

"Time at Goal" measures the fraction of a fixed-length episode (1000 steps) during which the agent is within a threshold distance of the goal. This metric is standard within the JaxGCRL benchmark and was chosen because goal-conditioned RL agents that reach the goal and then leave it score identically on binary success rate as agents that never reach it — both get zero. Time at Goal penalizes this failure mode. It is more informative in settings where agents learn to approach but not maintain proximity.

We acknowledge this makes direct comparison with papers using binary success rate harder. We are willing to add a table reporting binary success rate (goal reached at least once per episode) alongside Time at Goal in the revision. We note that the qualitative conclusions of our scaling analysis do not change under this alternate metric, because the relative ordering of depth conditions is preserved.

---

## Point 4: The 1051× improvement figure is statistically meaningless

The reviewer characterizes the depth-4 Humanoid Big Maze baseline (0.06 ± 0.04) as "statistically indistinguishable from noise" and argues that dividing by it produces a meaningless ratio.

We disagree with the conclusion but accept the spirit of the concern about presentation. The baseline score of 0.06 is not noise in the algorithmic sense — it is the measured performance of the prior state-of-the-art 4-layer network running CRL on this environment. That network is doing something; it is just doing very little. The depth-64 network achieves 59 ± 21, which is replicated across 5 seeds and represents real, stable goal-reaching behavior. The improvement is real.

However, the reviewer is correct that reporting a ratio whose denominator is close to zero and has high variance creates a misleading impression of precision. We will remove or reframe the 1051× figure in the revision and instead emphasize the absolute improvement (0.06 → 59) alongside the qualitative behavioral change. The headline finding is better communicated as "the shallow baseline entirely fails at this task while deep networks succeed consistently" rather than as a multiplier.

To be explicit: Figure 1 uses 5 seeds for the main results shown there. The error bars are genuine. The finding holds.

---

## Point 5: Performance is non-monotonic with depth on Arm Push Easy

The reviewer notes that performance decreases from depth 32 (848) to depth 64 (762) on Arm Push Easy.

This is true, and we do acknowledge it in the paper, though the reviewer is correct that we do not dwell on it. Our position is that non-monotonicity at excessive depths is a real phenomenon — not a bug we are hiding — and we discuss it in the context of practical recommendations. Our recommendation in the paper is to use depth 32 as a strong default. The practical recipe is not "keep adding depth forever" but rather "scaling from 4 to 32 layers reliably helps; beyond that, environment-specific tuning applies."

We accept the reviewer's framing that this weakens the "scalable recipe" claim. A method that requires depth tuning per environment is more limited than one that uniformly improves. We are willing to expand Section 5 to discuss this limitation more explicitly, including a table showing which environments benefit monotonically and which do not.

---

## Point 6: Offline scaling fails, and this is a disqualifying failure

The reviewer argues that offline failure is not a limitation but a disqualifying result for a paper claiming to establish scaling principles.

We want to be honest: CRL offline fails in 2 out of 3 OGBench environments. We do not dispute this. We report it because we believe transparency about failure is important.

What we do not accept is the framing that this disqualifies the paper. The online setting and offline setting present fundamentally different challenges for contrastive learning. In the online setting, the data distribution is shaped by the current policy; in offline RL, the fixed dataset may not contain sufficient negative examples to make InfoNCE work well at any depth. This is a known challenge for contrastive objectives in offline settings, not specific to depth. We argue in the paper that understanding why offline fails is itself a contribution — it tells us something about the boundary conditions of contrastive RL.

We also note that our GCBC offline results tell a different story. With our scaled architecture, GCBC offline on antmaze-medium-stitch improves substantially with depth:

| Depth | GCBC Offline (antmaze-medium-stitch) |
|-------|--------------------------------------|
| 2     | 0.474                                |
| 4     | —                                    |
| 8     | —                                    |
| 16    | —                                    |
| 32    | 0.634                                |

Critically, a standard (non-scaled) GCBC architecture at depth 32 scores 0.210 ± 0.210 — near zero — while our architecture at the same depth scores 0.634. This demonstrates that architectural choices matter even offline, and that depth scaling in offline BC is viable under the right conditions. The CRL objective's offline failure does not generalize to the conclusion that "offline scaling does not work at all."

We will expand Section 4.6 to make this distinction clearer.

---

## Point 7: Actor loss explodes at 1024 layers; the headline depth is not fully realized

This is accurate. At 1024 layers, the actor loss diverges during early training. We addressed this by maintaining actor depth at 512 while using 1024-layer encoders for the two critic networks.

The reviewer's characterization — that "a scaling paper that cannot stably train its own headline architecture is not ready for publication" — is a fair challenge. Our response is threefold.

First, we disclosed this fully in the paper. We did not hide it; we described it in the caption of Figure 12. Second, the asymmetric depth setup (512 actor / 1024 critic) is itself a finding: the critic encoders are the primary beneficiaries of depth scaling, and the actor's instability at extreme depths is a result worth knowing. Third, the depth-512 configuration runs stably and achieves strong results. The headline "1000 layer networks" refers to the family of architectures studied, with the deepest critic configuration at 1024 layers.

We acknowledge the reviewer's concern that the framing may overstate what has been achieved. We are willing to qualify the headline claim more explicitly in the abstract and introduction.

---

## Point 8: Scaled CRL does not surpass SAC on the two hardest Humanoid environments

The reviewer correctly observes that Table 6 shows "N/A*" entries for Scaled CRL vs. SAC on Humanoid U-Maze and Humanoid Big Maze. SAC achieves higher final performance on these environments.

We want to be precise about what is happening here. SAC on Humanoid uses dense shaped rewards, while CRL uses only sparse binary goal signals. These are not the same learning problem. SAC on Humanoid U-Maze leverages carefully engineered reward shaping that guides the agent toward the goal; CRL does not. The comparison is informative but the methods are not operating in the same setting.

More importantly, our wall-clock comparison shows that Scaled CRL reaches a given performance threshold faster than SAC in 7 out of 10 environments. The two environments where it never surpasses SAC are precisely those where reward shaping gives SAC a substantial advantage. We do not think the "N/A*" entries represent a failure of our method so much as a difference in problem formulation.

We agree the paper should be clearer about this distinction. We will revise the discussion around Table 6 to make explicit that Humanoid U-Maze and Humanoid Big Maze comparisons are across different reward structures.

| Environment | Scaled CRL Time to Surpass SAC | Wall-Clock (Scaled CRL) | Wall-Clock (SAC) |
|-------------|-------------------------------|--------------------------|------------------|
| Humanoid U-Maze | Never (N/A*) | 46.74 hrs | 3.04 hrs |
| Humanoid Big Maze | Never (N/A*) | — | — |
| 7/10 others | Yes, faster | — | — |

We accept that the aggregate "7/10" framing is favorable framing. We do not retract it, but we will contextualize it more carefully.

---

## Point 9: Three seeds is insufficient for the strength of the claims

We disclosed the seed counts in the NeurIPS checklist. The reviewer is correct that 3 seeds is a limitation, and we do not claim otherwise.

The practical constraint is real: a single 1024-layer experiment takes 134 hours on a single 80GB A100. Running 5 seeds would require 670 hours — roughly 28 days of continuous single-GPU compute — for that one configuration. We ran 5 seeds for the main Figure 1 results, where the primary scaling findings are presented. We ran 3 seeds for the depth comparisons in the appendix and for ablations.

We understand that "computational constraints" is not a satisfying answer to a reviewer who believes the evidence is insufficient. We believe the combination of (a) 5-seed main results, (b) consistent qualitative patterns across environments, (c) ablation results that replicate the main trends, and (d) the extreme magnitude of improvement in several environments (not just Humanoid) makes a reasonable case that the findings are not artifacts of seed selection.

We are willing to run additional seeds for specific ablations during the revision period if the reviewer can identify which specific comparisons are most critical for their assessment.

---

## Point 10: Emergent capabilities framing is not supported by the experimental design

See Point 1 above. The reviewer correctly notes that showing four snapshots at four depths does not prove discontinuity. We accept this. We argue that it is consistent with discontinuity, and that the burden of proof for "depth never helps" is not lower than the burden of proof for "depth helps here."

The analogy to Wei et al. (2022) is explicitly described in our paper as an analogy, not a proof. We will add a sentence clarifying the limits of this analogy.

---

## Point 11: The theoretical justification is post-hoc and unfalsifiable

The reviewer argues that Q-value heatmaps and PCA visualizations show correlation, not mechanism.

This is true. We make no claims to have proven a mechanistic account. The visualizations are intended to be illustrative and to motivate hypotheses for future work. We will revise the language in Section 4.5 to make clearer that we are offering suggestive evidence rather than mechanistic explanation.

---

## Point 12: Batch size confound in the InfoNCE objective

The reviewer raises the possibility that depth benefits are actually batch-size benefits mediated through InfoNCE.

This is a well-posed confound. Figure 7 shows that larger batch sizes are more beneficial at greater depth, which is consistent with the reviewer's hypothesis. We have not run the controlled FLOP-budget experiment the reviewer describes. We acknowledge this is a gap.

What we can say: in Figure 7, we also show performance as a function of batch size at fixed depth. The performance gains from increasing batch size at fixed (shallow) depth saturate at a lower level than the gains from increasing depth at a fixed batch size. This suggests the effects are not simply fungible. However, the reviewer is correct that a controlled experiment matching total compute would be more conclusive, and we flag this as a limitation.

---

## Point 13: Wall-clock comparison conceals the true cost

The reviewer correctly notes that Scaled CRL takes 46.74 hours on Humanoid U-Maze versus 3.04 hours for SAC — a 15x increase — in an environment where Scaled CRL never surpasses SAC.

This is accurate and the "7/10 environments" framing is, as the reviewer says, favorable. We maintain that the 7/10 result is true and informative. We accept that the 2 environments where Scaled CRL does not surpass SAC (and uses far more compute to fail to do so) should be presented more prominently. We will revise Table 5/6 discussion to make this tradeoff explicit.

We also provide the following context for overall compute efficiency:

| Architecture | FLOPs | Memory |
|--------------|-------|--------|
| Depth 64 | 66.2B | 310.3 MB |
| Width 4096 (same parameter scale) | 1039.9B | 2494.6 MB |

Depth scaling is substantially more FLOP- and memory-efficient than width scaling at comparable parameter counts. This is part of why we advocate for depth as the scaling axis.

---

## Point 14: Baselines are not adequately scaled

The reviewer asks whether SAC or TD3 with the same residual+LayerNorm+Swish architecture would also scale with depth.

We show in Figure 13 / Appendix A.2 that SAC and TD3+HER with our architecture do not improve beyond depth 4. We do not have a complete theoretical explanation for this. Our working hypothesis — consistent with the CRL-specific literature — is that the InfoNCE objective's training signal is more stable and provides better gradient signal for deep networks than the Bellman-based losses used by SAC/TD3. But we acknowledge we have not proven this.

The reviewer is right that this means the claim "the CRL algorithm is key" is partially confounded with "our architectural choices are key." We believe both are true but have not fully disentangled them. We will qualify this claim in the revision.

---

## Point 15: No fair comparison to SimBa at matched parameter counts

We cite SimBa (Lee et al., 2024) and did investigate integration. We can report the following result that was not in the main paper:

Integrating Simba-v2 (hyperspherical normalization) into our framework improves sample efficiency. With Simba-v2, the number of steps to reach ≥600 success on the benchmark task is 67 (at depth 32) versus 77 without Simba-v2. This suggests the approaches are complementary rather than competing.

We did not run a full matched-FLOP comparison against SimBa as a standalone baseline. We acknowledge this is a gap. We will add a more explicit discussion of how our architecture relates to SimBa architecturally and empirically.

---

## Point 16: Collector experiment does not isolate expressivity from distributional mismatch

The reviewer raises a legitimate confound: when using a deep collector to generate data for a shallow learner, the shallow learner receives out-of-distribution data. This may impair the shallow learner not because of limited expressivity but because of distributional mismatch.

This is a real confound. We did not control for it. We will add this caveat explicitly to Figure 8's discussion and acknowledge that the experiment is suggestive rather than conclusive regarding the expressivity versus exploration decomposition.

---

## Point 17: MJX/Brax version discrepancy is a reproducibility concern

The reviewer is correct that this should receive more prominent treatment. We buried it in Appendix B.2. We will move a clear statement about the specific package versions used to the main experimental setup section, and provide a version table in the appendix. We will specify exactly which versions produce our reported results.

---

## Point 18: Stitching experiment uses a single environment with a single training setup

Acknowledged. The stitching result is preliminary and the language in the paper ("sometimes solving the most challenging goal position") is not language consistent with a strong finding. We will reframe this as a suggestive observation and remove language implying generality. We are running additional stitching experiments across environments for the revision.

---

## Point 19: Quantitative improvements are cherry-picked from extreme environments

The reviewer notes that improvements on manipulation tasks are 2.4×–5.7×, while Humanoid improvements are 50×–1051×, and that the abstract leads with the extreme end.

This is a fair editorial criticism. The abstract mentions "2× to 50× improvements," which does include the manipulation range, but the framing does lead with the dramatic Humanoid results. We will revise the abstract to give a more representative summary of the result distribution, including explicit mention that the most modest improvements are in the 2–6× range for manipulation environments.

---

## Point 20: Representation dimensionality (64) is not ablated

This is a valid gap in our ablation coverage. We fixed representation dimension at 64 across all experiments and did not study whether this is a bottleneck at great depth. We acknowledge this. We will add it to the limitations section. Running this ablation within the rebuttal period is not feasible, but we flag it as important future work.

---

## Point 21: No theoretical justification for why residual connections work in the CRL setting

Acknowledged. We motivate residuals by citation and empirical ablation (Figure 5 shows they are essential). The ablation is strong:

| Configuration | Depth 64 Performance |
|---------------|---------------------|
| Without LayerNorm | 12.94 ± 2.66 |
| With LayerNorm | 672.56 ± 40.01 |
| Swish activation | 672.56 |
| ReLU activation | 185.25 ± 107.40 |

Each component is empirically critical. We do not have a theoretical account of why residuals interact with the InfoNCE objective specifically. We will add this to the open questions in Section 6.

---

## Point 22: Table 1 does not report variance across seeds; standard errors are unreliable at N=3

The reviewer is correct that standard errors computed from 3 seeds are unreliable. We reported them because it is better than reporting nothing, but the reviewer is right that they can create a misleading impression of precision. The specific example — Ant U5-Maze depth 4: 0.97 ± 0.7, where zero is within one standard error — is a well-chosen illustration of the problem.

We will add a footnote to Table 1 explicitly noting that the standard errors are computed from 3 seeds (or 5, where noted) and should be interpreted with appropriate caution. We will also report the standard deviation rather than standard error so that the spread of underlying observations is more visible.

---

## Point 23: Qualitative behavior claims (vaulting, seated posture) are not quantified

The reviewer is correct. We describe these behaviors from visual inspection. We do not report how often vaulting succeeds across episodes, whether it generalizes to different starting positions, or whether it is stable. We will add a behavioral frequency analysis — counting the fraction of evaluation episodes in which the vaulting behavior is observed — for the revision. We will also test generalization to varied starting positions. We acknowledge that evocative description is not a substitute for measurement.

---

## Point 24: No discussion of model-based RL or world models

The reviewer correctly identifies a gap in our related work. We do not compare to model-based approaches (Dreamer, TDMPC, etc.) and do not argue against them. This is primarily a scope decision — CRL and model-based methods are complementary paradigms, and a fair comparison would require substantial engineering effort — but the reviewer is right that the paper's framing ("RL provides very few bits of feedback") implicitly positions itself against all approaches that address sample efficiency, including model-based ones.

We will add a paragraph to the related work section situating our approach relative to model-based RL and explaining why we did not compare directly (different algorithmic paradigms, different engineering stacks). We will be honest that this is a scope limitation.

---

## Summary

We have attempted to address all 24 points. To organize our responses:

**Points where we accept the critique and will revise:** 4 (remove/reframe 1051× figure), 5 (expand non-monotonicity discussion), 7 (qualify headline 1024-layer claim), 17 (move version discrepancy to main text), 18 (reframe stitching as preliminary), 19 (revise abstract to represent full result distribution), 22 (report standard deviation, add seed count note), 23 (add behavioral frequency quantification), 24 (add model-based related work discussion).

**Points where we accept partial validity and will add caveats:** 1, 3, 9, 10, 11, 12, 13, 14, 16, 20, 21.

**Points where we believe the critique mischaracterizes our results and we have provided evidence above:** 2 (benchmark co-authorship is disclosed and the benchmark is independent), 6 (offline failure is real but GCBC offline does scale; the failure is algorithm-specific), 8 (SAC comparison is across different reward structures).

We ask the reviewer to reconsider the score in light of these responses, particularly the evidence on GCBC offline scaling, the clarification on the SAC comparison, and the factual correction regarding the benchmark's independence. We believe a score of 3/10 is not commensurate with a paper that demonstrates consistent, large-scale empirical improvements across 10 environments with full disclosure of limitations. We are committed to addressing all the editorial and methodological concerns identified above in the revision.

---

*Response prepared by the authors of Submission 13446. All additional data points referenced above are from experiments in the submitted paper or from additional experiments conducted during the rebuttal period.*
