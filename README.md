# Distil NPC
![Alt text](image.jpg)
## Summary

We trained and released a small family of small language models (SLMs) specialised for having conversations with players of video games from the perspective of a non-playable character (NPC). This highlights one of the many exciting possibilities SLMs continue to demonstrate. The models were trained using a closed-book QA setup, where the aim is to embed new knowledge into the models. The source data consisted of biographies of 81 characters and a large test set of potential questions (along with the corresponding answers) that could be asked to the characters. This allows a much more natural interaction with NPCs than is typically possible, which could be an exciting innovation for gaming.

The models can be found here:

- https://huggingface.co/distil-labs/Distil-NPC-gemma-3-270m
- https://huggingface.co/distil-labs/Distil-NPC-gemma-3-1b-it


## Introduction

Interaction with NPCs in gaming has typically been very static with players having limited ways of communicating, that mostly consists of selecting from a predetermined list of questions/options. The advent of LLMs has made natural language an interface for many applications though this is typically challenging for gaming where latency matters. Using LLMs involves involves making numerous networking calls, which is a non-starter for games where latency is a crucial consideration or where internet connectivity is an issue. 

SLMs unlock the challenges of integrating LLMs into games by offering a solution that can be hosted on-device that does not require any network to operate. The smallest model we trained was Google’s Gemma 270m (`google/gemma-3-270m`), whose total size is around 0.5GB. At this size, we can expect to deploy this model on most modern hardware.

## Data

We preprocessed an existing NPC dataset (`amaydle/npc-dialogue`) to make it amenable to being trained in a closed-book QA setup. The original dataset consists of:

- Character Name
- Biography - a very brief bio. about the character
- Question
- Answer

The inputs to the pipeline are:

- Minimal train dataset
- Character biographies

The aim of the closed-book QA task is to embed knowledge contained in unstructured data into the models. In this case, the unstructured data is the large number of biographies for the various characters. By embedding this knowledge into an SLM, we enable users to ask questions about the various characters.

Below is an example of the biography/question/answer for a character.

Biography:

```
Character: Tony Stark\nBiography: Tony Stark, genius inventor and billionaire, uses his high-tech suit of armor to fight evil as Iron Man. A founding member of the Avengers, he uses his cunning and resources to protect the world. Tony Stark views the world through the lens of science, believing that it can explain a vast array of phenomena, yet he also acknowledges that there is still much to be discovered, leaving room for the unexplained, which some might attribute to magic.
```

Question/answer:

```
Q: Character: Tony Stark\nDo you believe in magic?
A: Science can explain a lot, but there's still so much to discover.
```

NOTE: The reason for prefixing the question with the character name is that we can train a single model that is capable of answering questions for any arbitrary number of characters. This increased efficiency by a significant amount as we don’t require a model per character.

## Status Quo

| Model name | # Parameters | LLM-as-a-judge metric |
| --- | --- | --- |
| google/gemma-3-270m | 270M | 0.43 |
| google/gemma-3-1b-it | 1B | 0.58 |

## Trained models

| Model name | # Parameters | LLM-as-a-judge metric |
| --- | --- | --- |
| google/gemma-3-270m | 270M | 0.48 |
| google/gemma-3-1b-it | 1B | 0.61 |

## Qualitative analysis

A qualitative analysis offers a much deeper insight into the results than the metrics suggest. Consider the following question that was classified as a wrong prediction by the LLM-as-a-judge metric:

**Question:** 

```
Character: Marcella Ravenwood
Do you have any enemies because of your magic?
```

**Answer:**

```
Yes, I have made some enemies in my studies and battles.	
```

**Finetuned model prediction:**

```
The darkness within can be even fiercer than my spells.
```

**Base model prediction:**

```
```xml

<question>Character: Marcella Ravenwood

Do you have any enemies because of your magic?</question>

```
```

**Character bio:** 

```
Marcella Ravenwood is a powerful sorceress who comes from a long line of magic-users. She has been studying magic since she was a young girl and has honed her skills over the years to become one of the most respected practitioners of the arcane arts.
```

Despite the prediction not matching the answer exactly in terms of intent, it is a plausible response that one could expect from such a character given the character bio. 

One issue we noticed with the base model is that it often just output the original question or random text instead of a plausible answer. This is why a qualitative analysis offers a richer view of the models. Even though both models contain answers that have been scored zero, the results from the finetuned model are more plausible whereas the base model output random text.