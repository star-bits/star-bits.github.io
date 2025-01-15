# Exchanging Pretraining and Test-Time Compute
- [Scaling LLM Test-Time Compute Optimally can be More Effective than Scaling Model Parameters](https://arxiv.org/pdf/2408.03314)

## 같은 FLOPs에서 test-time compute와 pretraining cost의 tradeoff 관계
- test-time compute의 방법들:
  - input level:
    - input prompt를 augment해서 인풋 토큰을 추가해주는 방법 (few-shot, "think step-by-step").
    - 인풋 토큰을 추가하지 않고 RL-inspired finetuning method로 improved proposal distribution을 만들어 내는 방법 (STaR, ReST).
    - iteratively asking the model to revise its response해서 proposal distribution을 improve하는 self-critique 방법.
  - output level:
    - multiple candidates를 sampling 하는 방법. learned verifier 또는 (process-based) reward model이 필요함. best-of-N, beam search, lookahead search를 적용할 수 있음.
- 프롬프트의 어려운 정도에 따라, 쉬운 문제의 경우 모델이 항상 정답에 근접한 답을 내기 때문에 revision의 반복으로 정답을 낼 수 있고, 어려운 문제의 경우 search가 필요하기 때문에 하나의 답을 revision하는 것만으로는 정답을 내기 어려움. 따라서 difficulty level에 따라 adaptive하게 optimal test-time computation strategy를 사용함.
- 여기서는 revision과 search를 사용하고, search 중에서는 beam search를 사용함. [MATH 벤치마크](https://arxiv.org/pdf/2103.03874)와 PaLM-2 모델을 사용함. PRM에는 PRM800k 데이터셋을 사용하고, GPT-4로 생성된 데이터셋이기 때문에 [Wang et al.](https://arxiv.org/pdf/2312.08935)에서의 방법을 이용해 PaLM-2에 맞도록 distribution shift을 적용했다고 함. Appendix D 참고.
- 이렇게 test-time compute에 들어가는 FLOPs에 대해 생각해보면, 작은 모델을 pretrain하고 test-time에서 답변을 똑똑하게 만드느냐, 큰 모델을 pretrain해서 답변을 똑똑하게 만드느냐에 따른 트레이드오프 관계가 성립함.

![fig9](https://github.com/user-attachments/assets/56f1d2f4-6405-4695-8aae-135ef6059366)

- FLOPs를 M의 배수로 늘여가며 성능 향상을 본다고 했을 때,
- M (X + Y) = total FLOPs
- X = 6 N D_pretrain
  - [왜 6?](https://arxiv.org/pdf/2203.15556)
- Y = 2 N D_inference
  - [왜 2?](https://arxiv.org/pdf/2401.00448)
- where N: the number of model parameters, D_pretrain: the number of tokens used for pretraining, D_inference: the number of tokens generated at inference time.
- R은 D_inference와 D_pretrain의 비율임. deploy하는 환경에 따라 R이 1보다 크거나 작을 수 있음. 여기서는 R이 0.16, 0.79, 22일 때를 비교함.
- 각각의 경우에 따라 별의 위치와 선의 위치를 비교하는것이 핵심. (별은 약 14배 더 큰 모델의 성능을 나타냄.)
- 문제가 어렵거나 R이 클 경우, 즉 별이 선보다 높이 있을 때, pretraining에 더 투자하는 것이 낫다.
- 문제가 쉽거나 R이 작을 경우, 즉 별이 선보다 낮게 있을 때, test-time compute를 활용하는 게 낫다.
