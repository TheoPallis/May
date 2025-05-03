---
tags:
  - 4.Personal
---
#### Notes
*   focuses on writing prompts for the Gemini model within Vertex AI
*   The model takes sequential text as an input and then predicts what the following token should be, based on the data it was trained on. The LLM is operationalized to do this over and over again, adding the previously predicted token to the end of the sequential text for predicting the following token. The next token prediction is based on the relationship between what’s in the previous tokens and what the LLM has seen during its training.
*   to set up the LLM to predict the right sequence of tokens.
*   This process involves tinkering to find the best prompt, optimizing prompt length, and evaluating a prompt’s writing style and structure in relation to the task.
*   Prompts might need to be optimized for your specific model, regardless of whether you use Gemini language models in Vertex AI, GPT, Claude, or an open source model like Gemma or LLaMA.
*   Reducing the output length of the LLM doesn’t cause the LLM to become more stylistically or textually succinct in the output it creates, it just causes the LLM to stop predicting more tokens once the limit is reached. If your needs require a short output length, you’ll also possibly need to engineer your prompt to accommodate.
*   Output length restriction is especially important for some LLM prompting techniques, like ReAct, where the LLM will keep emitting useless tokens after the response you want.
*   LLMs predict probabilities for what the next token could be, with each token in the LLM’s vocabulary getting a probability. Those token probabilities are then sampled to determine what the next produced token will be. Temperature, top-K, and top-P are the most common configuration settings that determine how predicted token probabilities are processed to choose a single output token.
*   Lower temperatures are good for prompts that expect a more deterministic response, while higher temperatures can lead to more diverse or unexpected results. A temperature of 0 (greedy decoding) is
*   deterministic: the highest probability token is always selected (though note that if two tokens have the same highest predicted probability, depending on how tiebreaking is implemented you may not always get the same output with temperature 0).
*   The Gemini temperature control can be understood in a similar way to the softmax function used in machine learning. A low temperature setting mirrors a low softmax temperature (T), emphasizing a single, preferred temperature with high certainty. A higher Gemini temperature setting is like a high softmax temperature, making a wider range of temperatures around the selected setting more acceptable.
*   Top-K sampling selects the top K most likely tokens from the model’s predicted distribution. The higher top-K, the more creative and varied the model’s output; the lower top-K, the more restive and factual the model’s output.
*   Top-P sampling selects the top tokens whose cumulative probability does not exceed a certain value (P). Values for P range from 0 (greedy decoding) to 1 (all tokens in the LLM’s vocabulary).
*   Choosing between top-K, top-P, temperature, and the number of tokens to generate, depends on the specific application and desired outcome, and the settings all impact one another.
*   If temperature, top-K, and top-P are all available (as in Vertex Studio), tokens that meet both the top-K and top-P criteria are candidates for the next predicted token, and then temperature is applied to sample from the tokens that passed the top-K and top-P criteria.
*   If you set temperature to 0, top-K and top-P become irrelevant–the most probable token becomes the next token predicted. If you set temperature extremely high (above 1–generally into the 10s), temperature becomes irrelevant and whatever tokens make it through the top-K and/or top-P criteria are then randomly sampled to choose a next predicted token.
*   If you set top-K to 1, temperature and top-P become irrelevant. Only one token passes the top-K criteria, and that token is the next predicted token. If you set top-K extremely high, like to the size of the LLM’s vocabulary, any token with a nonzero probability of being the next token will meet the top-K criteria and none are selected out.
*   If you set top-P to 0 (or a very small value), most LLM sampling implementations will then only consider the most probable token to meet the top-P criteria, making temperature and top-K irrelevant. If you set top-P to 1, any token with a nonzero probability of being the next token will meet the top-P criteria, and none are selected out.
*   As a general starting point, a temperature of .2, top-P of .95, and top-K of 30 will give you relatively coherent results that can be creative but not excessively so.
*   Finally, if your task always has a single correct answer (e.g., answering a math problem), start with a temperature of 0.
*   A zero-shot prompt is the simplest type of prompt. It only provides a description of a task and some text for the LLM to get started with.
*   A one-shot prompt, provides a single example,
*   A few-shot prompt provides multiple examples to the model.
*   As a general rule of thumb, you should use at least three to five examples for few-shot prompting.
*   The examples should be diverse, of high quality, and well written. One small mistake can confuse the model and will result in undesired output.
*   If you are trying to generate output that is robust to a variety of inputs, then it is important to include edge cases in your examples. Edge cases are inputs that are unusual or unexpected, but that the model should still be able to handle.
*   Contextual prompt: Provides immediate, task-specific information to guide the response. It’s highly specific to the current task or input, which is dynamic.
*   Role prompt: Frames the model’s output style and voice. It adds a layer of specificity and personality.
*   By providing contextual prompts, you can help ensure that your AI interactions are as seamless and efficient as possible. The model will be able to more quickly understand your request and be able to generate more accurate and relevant responses, as you can see in the example of Table 7.
*   Let’s go back to the original prompt, but this time we include the answer of the step back as context and see what it will return.
*   Chain of Thought (CoT) prompting is a technique for improving the reasoning capabilities of LLMs by generating intermediate reasoning steps.
*   Let's think step by step.
*   The answer is 38.
*   Let's think step by step.
*   Self-consistency gives a pseudo-probability likelihood of an answer being correct, but obviously has high costs.
*   It follows the following steps:
*   1. Generating diverse reasoning paths: The LLM is provided with the same prompt multiple times. A high temperature setting encourages the model to generate different reasoning paths and perspectives on the problem.
*   2. Extract the answer from each generated response.
*   3. Choose the most common answer.
*   By generating many Chains of Thoughts, and taking the most commonly occurring answer (“IMPORTANT”), we can get a more consistently correct answer from the LLM.
*   This example shows how self-consistency prompting can be used to improve the accuracy of an LLM’s response by considering multiple perspectives and selecting the most consistent answer.
*   Tree of Thoughts (ToT). It generalizes the concept of CoT prompting because it allows LLMs to explore multiple different reasoning paths simultaneously, rather than just following a single linear chain of thought.
*   Reason and act (ReAct) [10]13 prompting is a paradigm for enabling LLMs to solve complex tasks using natural language reasoning combined with external tools (search, code interpreter etc.) allowing the LLM to perform certain actions,
*   For example, you can use automatic prompt engineering that will help with training a chatbot for a merchandise t-shirt webshop. We want to figure out all the various ways customers could phrase their order for buying a band merchandise t-shirt.
*   1. Write the prompt which will generate the output variants. In this example, I am using gemini-pro to generate 10 instructions.
*   2. Evaluate all of the instruction candidates by scoring the candidates based on a chosen metric, For example, you can use. BLEU (Bilingual Evaluation Understudy) or ROUGE (Recall-Oriented Understudy for Gisting Evaluation).
*   3. Select the instruction candidate with the highest evaluation score. This candidate will be the final prompt you can use in your software application or chatbot. You can also tweak the select prompt and evaluate again.
*   The most important best practice is to provide (one shot / few shot) examples within a prompt.
*   Design with simplicity
*   Try using verbs that describe the action.
*   Be specific about the desired output.
*   Use Instructions over Constraints
*   As a best practice, start by prioritizing instructions, clearly stating what you want the model to do and only use constraints when necessary for safety, clarity or specific requirements.
*   Control the max token length
*   tools like the json-repair library (available on PyPI) can be invaluable in these situations.
*   For CoT prompting, putting the answer after the reasoning is required because the generation of the reasoning changes the tokens that the model gets when it predicts the final answer.
*   We recommend creating a Google Sheet with Table 21 as a template.
	*   Name [name and version of your prompt]
	*   Goal [One sentence explanation of the goal of this attempt]
	*   Model [name and version of the used model]
	*   Temperature [value between 0 - 1]
	*   Token Limit [number]
	*   Top-K [number]
	*   Top-P [number]
	*   Prompt [Write all the full prompt]
	*   Output [Write out the output or multiple outputs]
####  Summary
Here are the main points from the Google whitepaper on Prompt Engineering:

1.  **Definition & Importance:** Prompt engineering is the iterative process of designing effective inputs (prompts) to guide Large Language Models (LLMs) toward desired, accurate outputs. It's crucial because prompt quality significantly impacts LLM performance.
2.  **LLM Configuration is Key:** Effective prompting involves not just the text input but also configuring LLM parameters like:
		*   **Output Length:** Controls the number of tokens generated.
		*   **Sampling Controls:** Determines how the next token is chosen from predicted probabilities (Temperature, Top-K, Top-P). These control randomness vs. determinism and creativity vs. factuality.
3.  **Core Prompting Techniques:**
		*   **Zero-Shot:** Providing only the task description without examples.
		*   **One-Shot / Few-Shot:** Providing one or multiple examples to guide the model's output format, style, or pattern. Highly effective.
		*   **System Prompting:** Defining the overall task or constraints (e.g., "Return output in JSON").
		*   **Role Prompting:** Assigning a specific persona or identity to the model (e.g., "Act as a travel guide").
		*   **Contextual Prompting:** Providing specific background information relevant to the task.
4.  **Advanced Prompting Techniques:**
		*   **Step-back Prompting:** Asking a general question first to activate broader knowledge before tackling the specific task.
		*   **Chain of Thought (CoT):** Instructing the model to output its reasoning steps before the final answer, improving performance on complex tasks.
		*   **Self-Consistency:** Running a CoT prompt multiple times (with sampling) and taking the majority answer for increased robustness.
		*   **Tree of Thoughts (ToT):** Exploring multiple reasoning paths simultaneously.
		*   **ReAct (Reason & Act):** Enabling LLMs to use external tools (like search) by interleaving reasoning steps with actions and observations.
		*   **Automatic Prompt Engineering (APE):** Using an LLM to generate and refine prompts automatically.
5.  **Code Prompting:** LLMs like Gemini can write, explain, translate, and debug code across various languages. Requires testing the generated code.
6.  **Best Practices are Crucial:**
		*   **Provide Examples (Few-Shot):** Often the most effective technique.
		*   **Be Specific & Clear:** Use simple language, define the desired output clearly.
		*   **Use Instructions over Constraints:** Tell the model *what to do* rather than just *what not to do*.
		*   **Experiment:** Iterate on prompts, formats, styles, configurations, and models.
		*   **Use Structured Formats (JSON):** Beneficial for non-creative tasks, reduces hallucinations, allows typing/sorting. Use JSON Repair tools for robustness.
		*   **Use Schemas (JSON Schema):** Define *input* structure to guide the LLM, especially with complex data.
		*   **Document Everything:** Meticulously track prompt versions, configurations, model versions, outputs, and results (a template is provided). This is essential for iteration, debugging, and collaboration.
7.  **Iterative Process:** Prompt engineering is not a one-shot task but requires continuous crafting, testing, analysis, and refinement based on model performance.