# Mean Reviewer Output — NeurIPS 2025 Submission 13446
# "1000 Layer Networks for Self-Supervised RL: Scaling Depth Can Enable New Goal-Reaching Capabilities"

---

## Strengths

1. The topic of scaling reinforcement learning is important and timely, given the recent interest in understanding the limits of deep learning across modalities.
2. The paper is generally well-written and the figures are, for the most part, easy to read.
3. The core empirical finding — that depth can help in certain goal-conditioned settings — is interesting, if not yet convincing in its generality.
4. I am not aware of prior work scaling networks to exactly this depth range in this specific self-supervised RL setting (though I have significant concerns about whether the framing is as novel as claimed, see Weaknesses).
5. The inclusion of qualitative behavior visualizations is a nice touch, and the humanoid locomotion results are visually compelling. Though I note that visual impressiveness is not a substitute for rigorous quantitative analysis, and the paper would benefit from

---

## Weaknesses

**1. The central claim is not substantiated by the evidence presented.**
The paper claims that scaling depth "enables new goal-reaching capabilities" and draws an analogy to emergent abilities in large language models. This framing is self-defeating. The authors have not demonstrated emergent capabilities in any meaningful sense — they have demonstrated that deeper networks achieve higher scores on a narrow set of benchmark tasks designed by one of the paper's own co-authors. The use of the word "emergent" is at best imprecise and at worst misleading, and the paper should be more honest about the scope of what has actually been shown.

**2. The benchmark is designed by the authors and is inherently compromised.**
The JaxGCRL benchmark (Bortkiewicz et al., 2024) used exclusively throughout this paper shares an author (M. Bortkiewicz) with the current submission. This is not a minor conflict of interest — it is a fundamental threat to the validity of the evaluation. The benchmark was built for exactly this type of experiment, with environments, metrics, and implementation choices that may have been, consciously or not, tailored to favor the proposed approach. The authors do not acknowledge this conflict anywhere in the paper. A paper claiming to advance the state of the art in goal-conditioned RL should demonstrate results on at least one benchmark with no author overlap.

**3. The "Time at Goal" metric is non-standard and obscures meaningful comparison.**
The paper reports performance as "time steps out of 1000 that the agent is near the goal." This metric is opaque, not standard in the goal-conditioned RL literature, and conveniently aggregates success and precision in a way that inflates the apparent performance of the proposed method. The community standard is success rate. The authors owe the community a comparison using unambiguous metrics that allow direct comparison with the broader literature. The decision to use a non-standard metric, on a benchmark developed by the authors, should raise serious questions about whether the results would hold under conventional evaluation.

**4. The 1051× improvement figure in Table 1 is statistically meaningless and should not be reported.**
The paper prominently reports a 1051× performance improvement on Humanoid Big Maze (from 0.06 ± 0.04 to 59 ± 21). This number is the result of dividing by a baseline score of 0.06 — a value so close to zero, and with a standard error of 0.04, that it is statistically indistinguishable from random behavior. Reporting a 1051× improvement over noise is not evidence of scaling; it is evidence of a poorly chosen baseline condition. The authors present this number in bold in Table 1 as if it constitutes a headline result, which undermines the credibility of all the other numbers in the table.

**5. Performance is non-monotonic with depth in several key environments, which the paper fails to address honestly.**
Table 1 and Figure 6 reveal that performance on Arm Push Easy actually *decreases* from depth 32 (848) to depth 64 (762). The paper glosses over this inconvenient result with a claim that performance "often jumps at specific critical depths," as if non-monotonicity is a feature rather than a failure mode. A method that reliably improves with scale is valuable; a method where you must guess the right depth for each environment, and where the wrong guess costs 10% of performance, is not a scalable recipe. The paper is insufficiently honest about how sensitive the results are to depth selection.

**6. The paper does not report results in the offline setting, claiming it "does not work." This is not a limitation — it is a disqualifying failure for a paper claiming to establish scaling principles.**
Section 4.6 and Figure 18 show that offline scaling "drastically declined in two out of three environments." The authors frame this as a "direction for future work." A paper whose title claims to establish scaling principles for RL, and which explicitly invokes parallels to scaling in language and vision, must account for the fact that the single most practically important setting — offline learning from fixed datasets — is precisely where the method fails. The authors have not explained why their approach fails offline, which suggests they do not understand why it succeeds online. This is a fundamental gap in the paper's theoretical understanding of its own contribution.

**7. The actor loss explodes at 1024 layers, and the authors quietly work around it rather than addressing it.**
The caption of Figure 12 reveals: "we observed the actor loss exploding at the onset of training, so we maintained the actor depth at 512 while using 1024-layer networks only for the two critic encoders." This is not a minor implementation detail — the paper is titled "1000 Layer Networks" and the headline depth of 1024 is achieved only by using mismatched actor and critic depths. The paper does not explain why the actor is unstable at this depth, does not study the phenomenon, and does not discuss whether the same instability might manifest at other depths under different conditions. A scaling paper that cannot stably train its own headline architecture is not ready for publication.

**8. The comparison to SAC is unfair and selectively presented.**
Table 6 shows that Scaled CRL does *not* surpass SAC on Humanoid U-Maze and Humanoid Big Maze — the two most challenging environments in the benchmark and, notably, the environments most prominently featured in the paper's qualitative results. The authors note these as "N/A*" entries. This means the paper's strongest visual results (the humanoid vaulting walls, the humanoid navigating mazes) correspond precisely to the environments where the baseline SAC outperforms the proposed method. The paper should be more honest that its headline qualitative results come from settings where the proposed method is not actually state-of-the-art.

**9. The number of random seeds is insufficient for the scale of claims being made.**
The paper explicitly states (NeurIPS checklist, item 7) that most experiments use only 3 seeds due to "computational constraints." For a paper making strong claims about emergent capabilities and reliable scaling, 3 seeds is not adequate. The error bars in Figure 1 are often wide enough to overlap between adjacent depth conditions, yet the paper draws strong causal conclusions from these results. The scaling community has long recognized that variance across seeds is particularly high in RL. The authors' decision to run fewer seeds on harder experiments is precisely backwards — the most challenging, highest-variance environments require the most seeds.

**10. The "emergent capabilities" framing is not supported by the experimental design.**
The paper shows depth-64 agents exhibit qualitatively different behaviors than depth-4 agents. This is described as analogous to emergent abilities in LLMs. However, the paper provides no controlled evidence that the behavioral change is discontinuous, unexpected at smaller scale, or qualitatively distinct in any operationally meaningful way. The paper shows four snapshots of behavior at four depths and calls it emergence. This conflates "we didn't see it at lower depths in these visualizations" with "it is impossible at lower depths." The claim requires a far more rigorous experimental treatment.

**11. The paper's theoretical justification for why depth helps is post-hoc and unfalsifiable.**
Section 4.5 argues that deeper networks learn richer representations and better capture environment topology. The evidence is a Q-value heatmap (Figure 9) and a PCA trajectory visualization (Figure 10). These are illustrative, not explanatory. Any sufficiently expressive network that achieves higher reward will, by definition, learn more task-relevant representations. The paper has not isolated why depth specifically enables this — it has shown a correlation and labeled it a mechanism.

**12. The InfoNCE objective introduces a batch-size confound that is not adequately disentangled.**
The paper shows in Figure 7 that larger batch sizes become beneficial as network depth increases. The InfoNCE objective is known to be sensitive to the number of negatives, which scales with batch size. It is not clear whether the performance gains attributed to depth are actually gains from effective batch size scaling through the InfoNCE objective, and whether a shallower network with a larger batch size would achieve comparable results. The authors have not disentangled these confounds. The paper stops short of demonstrating that depth provides independent benefit beyond what larger batches achieve at shallow depth.

**13. The wall-clock time comparison conceals the true cost of the method.**
Table 5 shows that Scaled CRL (Depth 64) takes 46.74 hours on Humanoid U-Maze, compared to 3.04 hours for SAC — a 15× increase in compute cost. Table 6 then shows Scaled CRL does not surpass SAC on this environment at all. The paper frames its wall-clock comparison favorably by noting it "outperforms SAC in less wall-clock time in 7/10 environments," but this framing conceals the two environments where it never surpasses SAC despite using 15× the compute. The aggregate framing is misleading.

**14. The baselines are not adequately scaled.**
The paper compares against SAC, SAC+HER, TD3+HER, GCBC, and GCSL — all using standard shallow architectures. If depth helps CRL, the natural question is whether depth would also help SAC or TD3. The paper shows in Figure 13 / Appendix A.2 that these methods "do not benefit from depths beyond four layers," but provides no theoretical explanation for why, and does not investigate whether different architectural choices (e.g., the same residual+LayerNorm+Swish combination) would allow these methods to also scale. Without this, the claim that "the CRL algorithm is key" is confounded with "our specific architectural choices are key," and the paper has not separated these factors.

**15. The paper fails to compare against the most relevant recent work on deep residual networks in RL.**
SimBa (Lee et al., 2024) and BRO (Nauman et al., 2024b) are cited but not adequately compared at matched parameter counts. The paper reports that scaling width with 2048 hidden units (35M parameters) fails to match depth-8 with 256 hidden units (roughly 1M parameters), but does not report whether SimBa-style architectures with comparable parameter budgets also benefit from depth. A fair comparison would match FLOPs or parameters across methods. The absence of this comparison is conspicuous and suggests the evaluation has been structured to favor the proposed approach.

**16. The exploration–expressivity disentanglement experiment (Figure 8) does not support the conclusions drawn.**
The "collector" experiment is presented as definitive evidence that both exploration and expressivity contribute to scaling. But the design is confounded: when using a deep collector, the deep and shallow learners receive different data distributions because the deep collector's *policy* shapes the data, not just the data quantity. The authors have not controlled for this. The shallow learner receiving deep-collector data is training on out-of-distribution trajectories generated by a policy it could not have produced. The experimental design does not isolate expressivity from distributional mismatch.

**17. The MJX/Brax version discrepancy buried in Appendix B.2 is a reproducibility concern that should be in the main text.**
The authors acknowledge that they observed "discrepancies in physics behavior between environment versions" due to different MJX and Brax package versions. This is not a minor implementation note — physics simulation discrepancies can substantially affect learned behavior and benchmark comparability. The paper does not specify which version other groups should use to reproduce the results, and the appendix presentation suggests an attempt to minimize the significance of this issue.

**18. The claim that depth improves generalization via "experience stitching" (Figure 11) is based on a single environment with a single modified training setup.**
The stitching experiment uses one variant of one environment (modified Ant U-Maze) with one specific training modification. The paper presents this as evidence that "increasing network depth results in some degree of stitching." This is a hypothesis, not a finding. The experiment has not been replicated across environments, the train/test split may have been chosen post-hoc, and the word "sometimes" appears in the results description ("depth 64 networks excel, sometimes solving the most challenging goal position"), which is not language consistent with a reliable scaling effect.

**19. The quantitative improvement figures are cherry-picked from extreme environments.**
The paper's headline claims — "2× to 50× improvements" and "outperforming other baselines" — rely heavily on the Humanoid-based environments, which have observation dimension 268 and represent the most favorable conditions for depth scaling according to the paper's own analysis. The improvements on manipulation tasks (Arm Push Easy: 2.5×, Arm Push Hard: 2.4×, Arm Binpick Hard: 5.7×) and on Ant Hardest Maze (1.8×) are substantially more modest. The paper's abstract quotes the extreme end of its result distribution. A paper claiming to establish general scaling principles for RL should not rely on its strongest cherry-picked result as the representative finding.

**20. The representation dimensionality (64) used for the contrastive embeddings is not ablated and is potentially a critical bottleneck.**
Table 7 shows that the representation dimension is fixed at 64 across all experiments. For networks with depth up to 1024 layers, a 64-dimensional embedding bottleneck may be a severe constraint on what the network can express. The paper does not ablate this dimension, does not discuss whether increasing it would further improve performance, and does not discuss whether the scaling benefits would persist at higher representation dimensionality. This is a significant gap in the ablation coverage for a paper whose central claim is about architectural scaling.

**21. The paper provides no theoretical justification for why residual connections specifically enable depth scaling in the CRL setting.**
The use of residual connections is motivated by citation to He et al. (2015) and prior RL work. Figure 5 shows they are essential. But the paper provides no analysis of why residual connections interact with the InfoNCE objective specifically, whether the benefits are due to gradient propagation, feature reuse, or implicit regularization, or whether alternative depth-enabling techniques (e.g., highway networks, dense connections) would work equally well. The paper has identified a necessary component without understanding it, which undermines confidence in the robustness of the result.

**22. The paper does not report variance across seeds in Table 1, which is its primary results table.**
Table 1 reports single values with ± standard errors, but only 3 seeds were used for most results. At 3 seeds, the standard error is highly unreliable, and the table gives a false impression of statistical precision. Several entries (e.g., Ant U5-Maze depth 4: 0.97 ± 0.7) have standard errors larger than the mean value, meaning zero performance is within one standard error. The paper reports 63× improvement over a result that is statistically zero. This is not evidence of scaling.

**23. The qualitative behavior claims (vaulting walls, seated posture) are not quantified.**
Section 4.3 describes the humanoid "folding forward into a leveraged position to propel itself over walls" and "shifting into a seated posture over the intermediary obstacle." These behavioral descriptions are based on visual inspection of rendered videos. No metric is provided for how often these behaviors occur, whether they are stable, whether they generalize to different starting positions, or whether they are genuinely novel versus previously observed in other settings. The paper uses evocative language to describe results that have not been rigorously measured.

**24. The paper does not discuss the relationship between its approach and model-based RL or world models.**
The introduction motivates self-supervised RL as necessary for scaling because RL provides "very few bits of feedback." Model-based RL methods address this by learning world models that provide additional training signal. The paper does not engage with this literature at all, does not compare to any model-based approaches, and does not explain why its specific self-supervised formulation (CRL + depth) is preferable to model-based alternatives at scale. The related work section omits this entire paradigm, which constitutes a significant gap given the paper's framing.

---

## Demands

1. **Provide results on at least one benchmark with no author overlap.** The exclusive use of JaxGCRL, a benchmark co-authored by a paper author, is not acceptable for a paper making general claims about scaling RL. Demonstrate the results on D4RL, Meta-World, or DMControl with the same experimental setup.

2. **Report success rate alongside "Time at Goal" for all experiments.** The current metric is non-standard and does not permit comparison with the broader literature. Provide both metrics or justify with an extensive calibration study.

3. **Disentangle depth from batch size by running the batch size scaling experiments (Figure 7) with controlled FLOP budgets.** At present, it is unknown whether the performance gains attributed to depth would disappear if shallow networks were given the same compute budget and larger batches.

4. **Provide a mechanistic explanation for why offline scaling fails.** Section 4.6 reports failure without analysis. At minimum, provide ablations showing which components (residual connections, LayerNorm, batch size) are responsible for the offline failure.

5. **Run the main results with at least 5 seeds across all environments and depth conditions.** The current 3-seed budget for most experiments is insufficient to support the strength of the claims being made. The standard error estimates based on 3 seeds are unreliable.

6. **Provide a fair comparison to SAC, TD3+HER, and SimBa at matched parameter counts or matched FLOP budgets.** The current baseline comparison uses shallow architectures against a deep proposed method. This is not a fair comparison. Show results where baselines are given the same parameter budget.

7. **Quantify the frequency and stability of the claimed emergent behaviors** (vaulting, seated locomotion). A behavioral analysis based on visual inspection of rendered trajectories is not sufficient evidence for a capability claim. Provide success rates for specific subtasks and test across varied initialization conditions.

---

## Score and Confidence

**Overall Score: 3 / 10**

**Confidence: 5 / 5**

**Summary:** This paper presents empirical evidence that deeper residual networks improve performance of contrastive RL on the JaxGCRL benchmark — a benchmark developed by one of the paper's own authors — but fails to establish the general scaling principles it claims, cannot explain why offline scaling fails, conceals actor instability at its headline 1024-layer depth, and reports its headline 1051× improvement over a baseline that is statistically indistinguishable from zero.
