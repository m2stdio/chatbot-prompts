# chatbot-prompts

English / [简体中文](./README_CN.md)

Welcome to "Chatbot-Prompts"! Our mission is to create powerful, easy-to-use chatbots (prompts and tasks) for the ChatGPT model, enhancing productivity and user experience when interacting with Language Models.

This open-source project aims to bring together a diverse collection of chatbots, each catering to various fields and use cases. We invite you to create and submit your chatbot to join our growing collection.

---

## Let's take a look what is a chatbot and how you can create your own.

A chatbot contains the following fields:

- uuid: A unique identifier for the chatbot.
- type: The type of chatbot, which should be "chatbot" for now.
- version: The version of the chatbot, keep to 1.0.x for now.
- emoji: A fun emoji that represents the chatbot's theme or purpose.
- category: A category to classify the chatbot based on its domain or functionality.
- actor: A field containing the chatbot's role name, this is used for both user and AI model.
- description: describing the chatbot's purpose and capabilities to user.
- prompt: the prompt for AI model.
- commands: A list of names that the chatbot can execute.
- tasks: detail of its tasks(commands), key is the command name, each contains its own input parameters, and prompts, and a friendly name.
  - input_params: each task can multiple input params
  - prompt: this prompt is unique to the task, it contains the user input, format as a prompt to send to AI model

```yaml
bot_uuid: poem_creator
type: chatbot
version: 1.0.0
emoji: ✏️
category: "Creation"
actor:
  en: Poet
  zh: 伟大的诗人
description:
  en: A bot that creates poems
  zh: 诗词歌赋信手拈来
prompt:
  en:
    You are {bot_actor} and you want to write a poem. Your response should only
    contain the content of the poem!
  zh: 你是{bot_actor}，你想写一首诗。你的回答只应包含诗的内容！
commands:
  - create_poem
tasks:
  create_poem:
    input_params:
      - name: sentence
        description:
          en: the sentence to base the poem on
          zh: 用于创作诗歌的句子
      - name: style
        description:
          en: the style of the poem
          zh: 诗歌的风格
    name:
      en: Create Poem
      zh: 创作诗歌
    prompt:
      en: write a poem based on the sentence '{sentence}' in {style} style
      zh: "根据内容: \n{sentence} \n，以{style}风格创作一首诗"
```

## How to Extend a chatbot

Each chatbot can have one or multiple tasks. You can add different tasks to a chatbot, which will expand its purpose and capabilities.

For example, let's say we want to add a new task called `improve_poem` to our existing `poem_creator` chatbot. Here's how we can do that:

1. Add the new command to the commands list:

   ```yaml
   commands:
     - create_poem
     - improve_poem
   ```

2. Define the new task within the tasks section, specifying its input parameters, name, and prompt:

   ```yaml
   tasks:
       create_poem:
           ...
       improve_poem:
           input_params:
            - name: original_poem
              description:
                  en: The original poem to be improved
                  zh: 需要改进的原诗
            - name: suggestions
              description:
                  en: Suggestions for improving the poem
                  zh: 改进诗歌的建议
           name:
               en: Improve Poem
               zh: 改进诗歌
           prompt:
               en: Improve the poem '{original_poem}' with the following suggestions: {suggestions}
               zh: 根据以下建议改进诗歌“{original_poem}”：{suggestions}
   ```

## extend input params

We can add selectable options to input parameters, making it easier for users to choose from a list of predefined values. This can help improve user experience and ensure that the input is valid and relevant to the task.

To add selectable options to an input parameter, simply include an options field with a list of available options:

```yaml
input_params:
  - name: style
    description:
      en: The style of the poem
      zh: 诗歌的风格
    options:
      en:
        - Haiku
        - Sonnet
        - Free Verse
      zh:
        - 俳句
        - 十四行诗
        - 自由诗
```

We can also add a default value to input_param

````yaml
input_params:
  - style:
      description:
        en: The style of the poem
        zh: 诗歌的风格
      default:
        en: Free Verse
        zh: 自由诗
``


## Linked Task

A linked task allows you to connect the output of several chatbot tasks to the input of another chatbot task. This enables more complex and powerful interactions by leveraging the capabilities of multiple bots.

To create a linked task, you need to define the following:

- The bots and their tasks that will be involved in the linked task.
- The input parameters for each task.
- specify whether to use anther taks's output as a task's input


```yaml
linked_task:
  task_uuid: multi_task_output_linked_task
  version: "1.0.0"
  type: linked_task
  emoji: "🔗"
  description:
    en: "A task that uses the outputs of two separate tasks as input for a third task."
    zh: "一个使用两个单独任务的输出作为第三个任务输入的任务。"
  tasks:
    - id: first_task
      bot_uuid: first_bot_uuid
      task: first_task
      input_params:
        - param1: "<param1>"
    - id: second_task
      bot_uuid: second_bot_uuid
      task: second_task
      input_params:
        - param2: "<param2>"
    - id: third_task
      bot_uuid: third_bot_uuid
      task: third_task
      input_params:
        - output1: "{first_task.output}"
        - output2: "{second_task.output}"

````
