# RL through Socratic Learning
- [Boundless Socratic Learning with Language Games](https://arxiv.org/pdf/2411.16905) (아이디어만 던지는 논문임. 이걸 position paper라고 하나봄.)

## Socratic Learning이란 무엇인가
- pure recursive self-improvement. (self-improvement: agent's own outputs influence its future learning)
- resursive self-improvement where agent's inputs and outputs are compatible (i.e., live in the same space), and outputs become future inputs.
- potentially unbounded improvement in a closed system. 즉, RL.
- RL agents' behavior changes the data distribution it learns on, which in turn affects its behavior policy.
- 핵심 요소: feedback, broad enough coverage of experience/data, scale (resource).

## feedback
- true purpose resides in the external observer. feedback can only come from a proxy. 
- fundamental challenge. feedback must sufficiently aligned with the observer's evaluation metric.
- most common pitfall is a poorly designed reward function that becomes exploitable over time.
- self-correction of RL? what can self-correct is behavior given feedback, not the feedback itself.
- well-defined, grounded metrics in language space are often limited to narrow tasks, while more general-purpose mechanisms like AI-feedback are exploitable, especially if the input distribution is permitted to shift.
- next-token prediction loss is grounded, but insufficiently aligned with downstream usage, and unable to extrapolate beyond the training data.
- human preferences are aligned by definition, but prevent learning in a closed system. (you need a human in the closed system.) caching such preferences into a learned reward model (RLHF) makes it self-contained but exploitable and potentially misaligned in the long-run, as well as weak on out-of-distribution data.

## coverage
- by definition, a self-improving agent determines the distribution of data it learns from. (자기가 훈련할 데이터를 만들어내니까.)
- preventing drift, collapse, or just narrowing of the generative distribution in a recursive process may be challenging.
- needs to preserve sufficient coverage of the data distribution.
- coverage condition implies that the socratic learning system must keep generating language data, while preserving or expanding diversity over time.

## scale
- RL works at scale.
- scaling up compute sufficiently, even relatively straightforward RL algorithms can solve problems previously thought out of reach
- betting on scaling up computation (as opposed to building in human knowledge) has consistently paid off in the history of AI

## 어떻게 구현할 것인가 - Language Game
- Wittgenstein's notion of language games. 언어를 입출력으로 사용하는 agent들 간의 interaction. 그리고 그 상호작용에 대한 scoring function.
- scalable mechanism for unbounded interactive data generation and self-play.
- coverage와 feedback 조건에 부합함. there is no form of interactive generation with tractiabel feedback that is not a language game.
- "It is not the words that capture meaning, but only the interactive nature of language can do so."
- narrow (data distribution) game은 reliable score function을 가질 수 있지만 당연히 coverage 문제를 가짐.
- 반대로, no individual lanuage game is perfectly aligned 이기 때문에 어떤 meta-critic이 filter the many games according to whether they make an overall net-positive condtribution 하도록 해 judge which games should be played 할 수 있음.
- language game의 structural leniency가 확장성을 갖게 함.

## 생각
- circle of scholars language game을 한다면 각각의 agent에게 어떤 다른 특성을 부여해 줘야 할까.
- 내부적으로 로직 계산에 파이썬을 사용할 수 있게 해서 현실에 그나마 ground시킬 수 있을 것.
- latent vector를 활용하거나, latent diffusion을 적용하거나 하는 방법 없이 그냥 data distribution을 잘 생성하도록 감독하는 것 만으로 next-token predictor가 A-Superhuman-I가 가능할까? synthetic data만으로 extrapolation이 가능한가?
- closed-system에서 지성이 발전하는 걸 지켜본다면 마치 closed-system인 지구에서 인간들이 서로의 상호작용만으로 지성을 발전시켜나가는 것을 보는 것 같겠군. 우주도 가만히 두면 알아서 지성체들이 등장하더라.
