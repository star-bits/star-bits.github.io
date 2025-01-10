 # Latent Thought-Token CoT
- [Training Large Language Models to Reason in a Continuous Latent Space](https://arxiv.org/pdf/2412.06769)
- Replication attempts:
  - [casper-hansen/OpenCoconut](https://github.com/casper-hansen/OpenCoconut)
  - [vicksEmmanuel/latent-gemma](https://github.com/vicksEmmanuel/latent-gemma)
  - [lucidrains/coconut-pytorch](https://github.com/lucidrains/coconut-pytorch)

## Reasoning in Latent Space
- discrete한 language space가 아닌 continuous한 latent space에서 reasoning을 하자.
- 인간이 생각을 할 때 뇌의 language network는 대부분 비활성화되어 있다고 함. 생각은 언어로만 하는게 아닐 것. 인간의 언어는 reasoning보다 communication에 최적화되어 있다고 함. 그렇겠지.
- language space에서의 computation은 reasoning보다는 textual coherence를 위해 사용된다.
- last hidden state를 reasoning state로 간주하고 활용하자.
- 이 last hidden state를 word token으로 decode하는 것이 아니라 해당 state를 모델의 input embedding으로 다시 넣어줌. "Instead of mapping between hidden states and language tokens using the language model head and embeddng layer, COCONUT directly feeds the last hidden state as the input embedding for the next token."
- 이렇게 하면 이 reasoning state는 multiple alternative reasoning steps를 encode 할 수 있다고 함. 마치 BFS를 하는 효과라고.

## 비슷한 시도들
- critical token들을 생성하기 전 별도의 reasoning step들을 수행 (Zelikman et al., 2024)
- supervised finetuning으로 reasoning step들을 생성하도록 학습 (Yue et al., 2023; Yu et al., 2023)
- reinforcement learning으로 reasoning step들을 생성하도록 학습 (Wang et al., 2024; Havrilla et al., 2024; Shao et al., 2024; Yu et al., 2024a)
- CoT의 token들을 symbol, pattern, text로 분류하고 역할에 맞게 concise CoT를 생성하도록 유도 (Madaan and Yazdanbakhsh, 2022)
- CoT의 autoregressive 특성 때문에 planning and search가 필요한 복잡한 reasoning에 취약함 (LeCun, 2022; Hao et al., 2023)
- 학습 과정에서 랜덤하게 learnable <pause> token을 삽입 (Goyal et al., 2023)
- internalized CoT (iCoT) (Deng et al., 2024)

## 그래서 어떻게 만들었다는 걸까
![fig2](https://github.com/user-attachments/assets/338e2de1-70eb-4aff-a030-cb59ca4fe951)
- 우리가 원하는 건 n개의 `[Thought]` 토큰을 통해 생각을 하고, 마지막에 답을 내는 것.
- training data에 있는 자연어 CoT step들을 여러 stage에 걸쳐 thought token으로 바꿔감.
- 이 과정을 만들어 내기 위해서 각 stage마다 수 epoch씩 train을 함.
- cross entropy loss는 non-latent (word space) token에 대해서만 계산됨.
- 이러한 multi-stage training strategy는 inspired by Deng et al. (2024).
- 사용한 데이터셋: GSM8K for math reasoning, ProntoQA and ProsQA (직접 만든 ProntoQA의 변형) for logical reasoning.
- word token으로 하는 CoT보다 적은 토큰으로 reasoning task를 수행할 수 있다.

## 생각
- latent space에서 reasoning을 하겠다는 모티베이션은 좋은데, 단순히 training data의 word token으로 이루어진 CoT를 thought token으로 바꿔준 것 이상의 의미가 있을까?
- thought token의 수를 생각해야 하는 내용의 복잡도에 따라 dynamic하게 변할 수 있었으면 좋겠다.
- CoT용 데이터셋을 필요로 한다는 점이 제약.
- 단순히 next thought token prediction이 아니라, 정말로 생각은 latent space상에서 전개하고, 벡터가 생각의 흐름에 따라 변형된 뒤 그 생각을 마지막에 word token으로 decoding 하는 방식이 필요해 보임.
- 한 turn 마다 생각을 하고 여러 문장을 생성한다는 점에서 next sentence prediction보다 인간의 reasoning에 더 가까워 보임. 근데 그게 thought token을 생성함으로서 충분히 가능한지는 의문. 
- 마치 stable diffusion처럼 latent space상에서 생각을 여러 방향으로 steer해서 생각 벡터를 transform하되, 그 optimal path를 RL로 찾아낼 수 있지 않을까.
- 그렇게 만들어 낸 생각 벡터를 preplan으로 기능시켜 매 word token prediction마다 먹여주는 방법은 어떨지.
- steering에 필요한 명시적인 사고 흐름을 몇 개 생각해 볼 수 있음: branching ('on second thought...'), backtracking ('wait, but...'), continuing ('therefore...'), encapsulating ('in summary...')
