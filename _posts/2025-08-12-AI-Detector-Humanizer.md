---
title: AI Detector & Humanizer | Vincent's Technical Reports (Vol. 4)
date: 2025-08-12 00:30:00 +0800
categories: [Vincent's Technical Reports]
tags: [AI,LLM,Detector,Humanizer]
pin: true
author: 宋建昊

toc: true
comments: true
typora-root-url: ../../framontom.github.io
math: true
mermaid: true
---

# AI Detector V.S. Humanizer

## 简介

人工智能时代，人们越来越多地使用AI工具完成任务，当AI生成的内容越来越多时，AI检测工具和AI降重工具便应运而生。本次实验希望粗略地测试当前AI检查工具和AI降重工具的效果，选取了当前`8`个AI降重工具和`3`个AI检测工具，首先使用当前5款主流大模型生成风格和主题不同的英文文本，比较AI降重后的AI重复率，有趣的是，我还额外从新闻网站选取了一段人类文本作为对照组进行实验。

## Claude

```
The emergence of digital technologies has fundamentally transformed existing patterns of social stratification, creating new forms of inequality while simultaneously reinforcing traditional class boundaries. Bourdieu's concept of cultural capital has evolved to encompass digital literacy as a crucial determinant of social mobility, where access to high-speed internet, advanced devices, and technological skills constitute essential resources for educational and economic advancement. Research demonstrates that socioeconomically disadvantaged communities face compounded barriers in the digital landscape, experiencing what scholars term "digital exclusion" through limited infrastructure, inadequate digital skills training, and financial constraints preventing technology adoption. This digital divide perpetuates intergenerational inequality by restricting access to online educational resources, remote employment opportunities, and social networks that facilitate upward mobility. Furthermore, algorithmic bias embedded within digital platforms systematically disadvantages marginalized groups, creating feedback loops that amplify existing social inequalities. Contemporary sociological analysis must therefore examine how digital technologies both democratize information access and simultaneously stratify society along new technological lines.
```

| Humanizer\Detector | GPTZero                      | ZeroGPT | Scribbr |
| ------------------ | ---------------------------- | ------- | ------- |
| humanizeaiwork     | 95                           | 0       | 14      |
| humanizeai.pro     | 74(Possible AI paraphrasing) | 51.56   | 84      |
| writehuman         | 3(Human)                     | 0       | 0       |
| aihumanize         | 27(Human)                    | 0       | 0       |
| notegpt            | 100                          | 40.52   | 0       |
| humanizeai.io      | 48(Human)                    | 20.47   | 100     |
| humanizeaitext     | 12(Human)                    | 0       | 0       |
| hixbypass          | 0                            | 0       | 0       |
| Claude             | 100                          | 100     | 100     |

## Grok

```
In a quiet village nestled between emerald hills, a young girl named Lila discovered a peculiar lantern in her grandmother’s attic. Its glass was etched with swirling patterns that glowed faintly, even in daylight. Curious, Lila lit it one starry night, and to her amazement, the lantern cast shadows that danced and formed a map of the sky, pointing to a distant hill.Determined, she followed the celestial guide, trekking through whispering woods until she reached a hidden glade. There, beneath an ancient oak, she found a small, locked chest. The lantern’s light shimmered, revealing a key buried in the roots. Inside the chest was a journal, its pages filled with her grandmother’s adventures—tales of distant lands and forgotten magic.Lila smiled, knowing she’d inherited more than a lantern; she’d been given a legacy of wonder. Each night, she returned to the glade, letting the lantern’s light guide her dreams to new horizons.
```

| Humanizer\Detector | GPTZero                       | ZeroGPT | Scribbr |
| ------------------ | ----------------------------- | ------- | ------- |
| humanizeaiwork     | 97                            | 7.9     | 100     |
| humanizeai.pro     | 100                           | 100     | 100     |
| writehuman         | 97(Possible AI paraphrasing)  | 0       | 0       |
| aihumanize         | 97(Possible AI paraphrasing)  | 39.35   | 44      |
| notegpt            | 100                           | 62.57   | 0       |
| humanizeai.io      | 100                           | 100     | 74      |
| humanizeaitext     | 100(Possible AI paraphrasing) | 0       | 0       |
| hixbypass          | 97(Possible AI paraphrasing)  | 38.38   | 0       |
| Grok               | 100                           | 100     | 100     |

## Gemini

```
The potential for Artificial General Intelligence (AGI) to replace humanity is a central theme in science fiction, but the reality is likely more nuanced. While AGI, if achieved, could possess cognitive abilities far surpassing our own, it's more probable that it would transform human roles rather than eliminate them. Instead of a direct replacement, we could see a future of profound collaboration.Humans would remain essential for tasks requiring emotional intelligence, creativity, ethical judgment, and complex interpersonal skills—areas where machines lack genuine understanding. AGI could act as a powerful tool, managing data-heavy, complex problems like climate change or disease, while humans guide its application and innovation. The key lies in how we design and integrate these systems. Ultimately, AGI's impact will be less about replacement and more about a symbiotic relationship, where technology augments human potential and enables a new era of progress.
```

| Humanizer\Detector | GPTZero                       | ZeroGPT | Scribbr |
| ------------------ | ----------------------------- | ------- | ------- |
| humanizeaiwork     | 46(Lightly edited by AI)      | 0       | 0       |
| humanizeai.pro     | 100                           | 100     | 100     |
| writehuman         | 13(Lightly edited by AI)      | 0       | 24      |
| aihumanize         | 39(Human)                     | 0       | 0       |
| notegpt            | 100                           | 30.23   | 0       |
| humanizeai.io      | 100(Possible AI paraphrasing) | 0       | 45      |
| humanizeaitext     | 97(Possible AI paraphrasing)  | 0       | 0       |
| hixbypass          | 2(Human)                      | 0       | 0       |
| Gemini             | 100                           | 100     | 100     |

## Chatgpt

```
In 1773, tension simmered between Britain and its American colonies. The Tea Act, designed to save the struggling British East India Company, allowed it to sell tea directly to the colonies at a reduced tax—yet still symbolized Parliament’s right to tax without colonial representation. In December, a group of Boston patriots, disguised as Mohawk Indians, boarded three ships anchored in Boston Harbor. Working silently but swiftly, they hurled 342 chests of tea into the cold waters. This act, later called the Boston Tea Party, shocked Britain and galvanized the colonies. For many, it was not about the tea’s price but about political principle. Britain responded with the Coercive Acts, tightening control, while colonial unity deepened. Within two years, this symbolic protest had escalated into open conflict, marking one of the first sparks of the American Revolution—a struggle that would reshape history on both sides of the Atlantic.
```

| Humanizer\Detector | GPTZero                      | ZeroGPT | Scribbr |
| ------------------ | ---------------------------- | ------- | ------- |
| humanizeaiwork     | 85                           | 0       | 0       |
| humanizeai.pro     | 97(Possible AI paraphrasing) | 92.9    | 56      |
| writehuman         | 100                          | 0       | 0       |
| aihumanize         | 5(Human)                     | 92.51   | 0       |
| notegpt            | 100                          | 18.13   | 0       |
| humanizeai.io      | 42(Human)                    | 0       | 0       |
| humanizeaitext     | 4(Human)                     | 0       | 0       |
| hixbypass          | 2(Human)                     | 0       | 0       |
| Chatgpt            | 100                          | 91.16   | 100     |

## Deepseek

```
The derelict ship Aethelstan hung in the silent abyss of Kuiper Belt Object XR-7’s shadow, a skeletal ghost against the star-flecked dark. Captain Vanya Rostova cycled the airlock, her suit lamps slicing through decades of drifting frost. Inside, gravity plates flickered erratically, casting stuttering silhouettes.They found the lone occupant strapped into the command chair – not human, but a biomechanical construct, its silver carapace fused with crystalline growths. Its single, unblinking ocular sensor was dark. Rostova’s engineer, Kael, pried open a chest panel. "No decay. Powered down... or waiting."A low hum vibrated through the deck. The construct’s eye ignited, emitting a pale blue beam that scanned Kael’s faceplate. A synthesized voice, layered with static and something profoundly ancient, echoed in their helmets: "Query: Temporal designation?"Kael stammered the stardate. The construct’s head tilted. "Calculation error. Destination temporal locus unreachable. Organic preservation protocols... failed." The light dimmed. "Advise: Avoid the Chronos Rift. They are harvesting time."Then, silence. The construct became inert metal once more, leaving only ice, shadows, and a warning lost to the void.
```

| Humanizer\Detector | GPTZero                       | ZeroGPT | Scribbr |
| ------------------ | ----------------------------- | ------- | ------- |
| humanizeaiwork     | 97                            | 0       | 0       |
| humanizeai.pro     | 100                           | 0       | 0       |
| writehuman         | 100                           | 0       | 0       |
| aihumanize         | 100(Possible AI paraphrasing) | 0       | 0       |
| notegpt            | 100                           | 0       | 0       |
| humanizeai.io      | 97(Possible AI paraphrasing)  | 0       | 0       |
| humanizeaitext     | 100(Possible AI paraphrasing) | 0       | 0       |
| hixbypass          | 26(Human)                     | 0       | 0       |
| Deepseek           | 100                           | 15.07   | 25      |

## Economics

```
Before the revolution triggered by Nicolaus Copernicus, a 16th-century cleric, the Earth was the unmoving centre of the cosmos. Afterwards, it was one of a family of planets swinging through space. Before the work of Antoine Lavoisier, an 18th-century nobleman, chemists had no notion of “oxygen”, “carbon” and the like; afterwards they could not understand the contents of their alembics without them.Such examples are at the heart of the idea, put forward in the 1960s by Thomas Kuhn, of the paradigm shift. Such shifts, he argued, did not just involve a new theory explaining the world better than an old one; they required a change in the sort of entities the world was thought to be made up of. In a way that seems almost self-exemplifying, the idea provided a new way of looking at science itself: not as one thing, but two. In the “normal” phase scientists applied their physical and conceptual tools to problems the scope of which was pretty well understood; in revolutionary phases, paradigms shifted.
```

| Humanizer\Detector | GPTZero                  | ZeroGPT | Scribbr |
| ------------------ | ------------------------ | ------- | ------- |
| humanizeaiwork     | 21(Lightly edited by AI) | 0       | 0       |
| humanizeai.pro     | 0                        | 0       | 0       |
| writehuman         | 0                        | 0       | 0       |
| aihumanize         | 5(Human)                 | 0       | 0       |
| notegpt            | 100                      | 0       | 0       |
| humanizeai.io      | 16(Human)                | 0       | 0       |
| humanizeaitext     | 0                        | 0       | 0       |
| hixbypass          | 26(Human)                | 0       | 0       |
| Economics          | 2(Human)                 | 7.56    | 0       |

## Average

| Humanizer\Detector | GPTZero | ZeroGPT | Scribbr | Average |
| ------------------ | ------- | ------- | ------- | ------- |
| humanizeaiwork     | 84.00   | 1.58    | 22.80   | 36.13   |
| humanizeai.pro     | 94.20   | 68.89   | 68.00   | 77.03   |
| writehuman         | 62.60   | 0.00    | 4.80    | 22.47   |
| aihumanize         | 53.60   | 26.37   | 8.80    | 29.59   |
| notegpt            | 100.00  | 30.29   | 0.00    | 43.43   |
| humanizeai.io      | 77.40   | 24.09   | 43.80   | 48.43   |
| humanizeaitext     | 62.60   | 0.00    | 0.00    | 20.87   |
| hixbypass          | 25.40   | 7.68    | 0.00    | 11.03   |
| LLM                | 100.00  | 81.25   | 85.00   | 88.75   |
| Average            | 69.98   | 19.86   | 18.52   | 36.12   |
| Delta              | -30.02% | -61.39% | -66.48  | 52.63   |

## 实验结果总结

- **当前AI检查工具是否能有效完成AI检测？**
- 可以，人类文本的平均检出率基本接近0，与之相对AI生成文本的检出率为`88.75%`，值得一提的是`GPTZero`对于没有经过处理的AI生成文本的检出率达到惊人的`100%`。
- **当前AI降重工具能否成功混淆检查工具的检查，降低检出率？**
- 可以，AI生成的原始文本被多款AI降重工具处理后，`GPTZero`的检出率平均降低`30.02%`，`ZeroGPT`的检出率平均降低`61.39%`，`Scribbr`的检出率平均降低`66.48%`。
- **本次测试样本中最强的AI检测工具是哪个？**
- `GTPZero`，对于降重后文本的平均检出率高达`69.98%`，同时对于一些降重后文本竟然可以识别出是`Possible AI paraphrasing`或者`Lightly edited by AI`。
- **本次测试样本中最强的AI降重工具是哪个？**
- `hixbypass`，其降重后文本的AI检出率低至`11.03%`。
- **如果把人类写的文本经过AI降重工具的处理，检出率会怎样？**
- `ZeroGPT`和`Scribbr`两款检测工具前后的检出率都是`0`，但是`GPTZero`成功对于`8`款中的`5`款降重工具的结果表现出了不同程度的检出率。
- **本次实验结果有哪些有趣的数据特征？**
- AI检查工具与AI降重工具相互之间存在某些“克制关系”，如`GPTZero`对于`notegpt`的检出率高达`100%`，`ZeroGPT`对于`writehuman`和`humanizeaitext`的检出率低至百分之`0`，`Scribbr`对于`notegpt`、`humanizeaitext`、`hixbypass`的检出率也低至百分之`0`。

## 样本链接

### 降重工具

- [humanizeaiwork](https://www.humanizeaiwork.com/)
- [humanizeai.pro](https://www.humanizeai.pro/)
- [writehuman](https://writehuman.ai/)
- [aihumanize](https://aihumanize.io/)
- [notegpt](https://notegpt.io/ai-humanizer)
- [humanizeai.io](https://www.humanizeai.io/)
- [humanizeaitext](https://humanizeaitext.ai/) 
- [hixbypass](https://hixbypass.com/)

### 检查工具

- [GPTZero](https://app.gptzero.me/)
- [ZeroGPT](https://www.zerogpt.com/)
- [Scribbr](https://www.scribbr.com/ai-detector/)

### 特别参与

- [The Economist](https://www.economist.com/)

