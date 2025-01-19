# Process Reward Model for Code-Augmented Step-By-Step Verified Reasoning Trajectories
- [rStar-Math: Small LLMs Can Master Math Reasoning with Self-Evolved Deep Thinking](https://arxiv.org/pdf/2501.04519)
- [microsoft/rStar](https://github.com/microsoft/rStar) (official repository but the link is 404 as of Jan 19)

## policy model에게 파이썬으로 MCTS rollout을 생성하라고 시킨 뒤 code execution을 verify하고 reasoning trace가 최종적으로 정답에 도달하면 각 step마다의 Q-value를 산정해서 reward model을 Process Preference Model로 train함
- policy model이 생성을 하고 reward model이 생성을 prune합니다.
- 이 MCTS 과정이 test-time search.
- 생성을 파이썬 코드로 하라고 시키기 때문에 매 스텝마다 code execution을 확인할 수 있음. (논리 확인은 아니지만 적어도 산수 확인은 되니까.)
- 이런 training 과정을 4번에 걸쳐 하는데, 첫 두 라운드는 terminal-guided임. 즉, 정답에 도달하는지만 확인하고, 정답에 도달한 trace로 finetuning을 함. 
- 이때 reward model(LLM에서 헤드만 갈아끼워서 [-1, 1]range를 예측함)을 train함. policy model이 MCTS rollout을 하고, 정답에 도달한 경우, Q-value를 역으로 되돌아가며 산정해서, 각 reasoning step이 얼마나 옳았는지 점수를 매기는 것.
- 단, reward model은 Q-value를 그대로 학습하는 것이 아니라 잘 한 경우의 Q-value와 못 한 경우의 Q-value를 binary classification 하는 법을 학습함. 이 process reward model을 Process Preference Model (PPM)이라고 부름.
- 나머지 두 라운드에서는 reward model이 pruning을 함. 
- 이 과정을 얘네는 self-evolving SFT라고 부름.
![fig1](https://github.com/user-attachments/assets/c91136a7-f524-4567-a6c6-a51c3dae7253)
- 수학 문제 푸는 데는 벤치마크 따라 o1-preview, o1-mini보다 낫기도 하다고.
- 샘플링을 64개까지 늘이니까 성능이 더 좋아지더라. scaling up the test-time compute.
![fig3](https://github.com/user-attachments/assets/d0dfa4d1-b443-4419-8f35-d88d37f02df5)
- terminal-guided annotation을 할 때 intermediate node들의 점수를 내는 방식을 [AlphaGo](https://arxiv.org/pdf/1712.01815)와 [rStar](https://arxiv.org/pdf/2408.06195)에서 가져왔다고 함.
- 초딩 수준 수학 문제 데이터셋으로는 reasoning 능력을 향상시킬 수 없더라.
- GPT-4로 문제와 솔루션 데이터를 새로 만들었고, 10개의 솔루션을 생성하게 한 뒤 3개 이상의 동일한 답이 나온 경우만 사용함.
- 고퀄의 reasoning trajectory 데이터셋이 핵심이다. 답만 맞는게 아니라 중간 스텝도 고퀄이어야 함.
![table6](https://github.com/user-attachments/assets/2bf5f96d-0f68-4ec0-87a9-001f184532e3)
- round마다 조금 더 똑똑해지고 그걸 기반으로 MCTS를 하면서 발전하는게 socratic learning 같군.
- MCTS-driven deep thinking은 intrinsic self-reflection 능력의 emergence를 보여주더라. 데이터셋에서 self-reflection이 없었음에도.

## 생각
- step-by-step reasoning trajectory 데이터에서 각 step의 logical leap의 정도도 중요할텐데. 이걸 reward model이 확인하고 regulate하는 방법이 있을까?
- step-by-step *verified* reasoning trajectory인게 중요하다고 한다. STaR의 방법으론 intermediate step들의 정확도를 장담할 수 없었으니 거기서 더 발전된 방법이긴 한데, code execution과 PPM도 그 중간 과정들이 논리적으로 올바른지는 보장하지 못함. 그리고 이게 가능할 것 같지도 않음.
- general commonsense reasoning도 파이썬을 사용해서 기호화된-reasoning-화 시킬 수 있지 않을까? 자연어의 ambiguity들을 제거할 수 있을 것. 인간들은 항상 인과관계와 필요/충분조건을 헷갈려 한다.
- MCTS의 selection, expansion, rollout, backpropagation이 곧 인간의 사고 흐름을 나타낸다고 볼 수 있겠군. 이걸로 충분한가?
- 더 똑똑한 모델을 만드는 데에 step-by-step reasoning trace 데이터셋만 있으면 되는 걸까? latent space를 이리저리 더 augment하는게 필요한건지, 큰 의미가 있을지 잘 모르겠음. 기존 decoder-only transformer에 간단히 갖다붙일 수 있는 방법이 아니고서는 next-token prediction이 충분히 잘 되고 단순하고 robust해서 다른 방법의 메리트를 찾기 힘들듯. 그리고 데이터셋이 주는 explainability도 장점임. 그냥 수학 교육 자료로 써도 되겠다.
- o1이 생각하는 과정을 다 보여주지 않는 이유는 이걸 다 보여주면 training dataset으로 써먹을까봐인듯. 모델의 구조에서 변화가 있는게 아니라 데이터셋이 전부라서? MCTS 정도를 적용한? 생각하는 과정은 그냥 `<thought>`, `</thought>` 같은걸로 구분짓고 보여주지 않는 거고?
