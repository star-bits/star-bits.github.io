# Thought Template Augmented Generation
- [Buffer of Thoughts: Thought-Augmented Reasoning with Large Language Models](https://arxiv.org/pdf/2406.04271)
- [YangLing0818/buffer-of-thought-llm](https://github.com/YangLing0818/buffer-of-thought-llm) (official repository)

## Thought Templates
- 특정 reasoning task가 주어지면 해당 task에 적합한 reasoning이 적혀 있는 thought template를 RAG로 갖고와서 생각의 흐름을 따라가게 하자.
- meta-buffer라는 곳에 thought-template라는 series of informative high-level thought들을 저장함.
- thought-template의 reasoning structure를 따라가게 함.
- buffer-manager가 meta-buffer를 동적으로 업데이트 하도록 관리함.
- 이전의 다른 방법론으로는 single-query reasoning(CoT, Few-shot Prompting, 등)과 multi-query reasoning(Least-to-Most, Tree-of-Thought, Graph-of-Thought, 등)이 있다. 전자는 사전 지식이 필요하고 후자는 computationally-intensive하다.
- input query가 들어오면 Problem Distiller가 어떤 종류의 reasoning task인지 판단하고, RAG를 통해 meta-buffer에서 'thought' retrieval을 해서, Instantiated Reasoning을 실행(즉, inference)한다.
- reasoning task의 종류는 Text Comprehension, Creative Language Generation, Common Sense Reasoning, Mathematical Reasoning, Code Programming, Application Scheduling로 구분지었다고 함.
- 각 task의 thought-template 예시 프롬프트와 Problem Distiller, Insantiated Reasoning 프롬프트가 appendix에 제시됨.

## 생각
- 별거 없는 내용인데 자기들 말로는 성능 향상이 크다고 하고, 뉴립스 스포트라이트도 가고 뭐 잘 되나봄.
- 하긴 이렇게 LLM의 성능을 최대한 끌어내는 게 대부분의 use case의 효용에는 더 도움이 될 지도.
- 인위적으로 reasoning task를 6개만으로 분류하는게 좀 걸리기는 하지만 어차피 어지간해서는 LLM을 쓰는 이유도 거기서 거기더라.
- 고객 상담용 챗봇과 같이 narrow한 task의 경우 프롬프트를 잘 작성하면 성능과 만족도에 향상이 있을 것으로 기대됨.
- superhuman intelligence? 야 너는 average data labeler의 intelligence라도 갖춰라 is the new 야 너는 나무위키라도 좀 읽어라.
