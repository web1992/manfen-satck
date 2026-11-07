---
name: unslop
description: "从任意文稿中剔除 AI 写作痕迹。"
disable-model-invocation: true
---

# Unslop

编辑文本以去除 AI 模式。扫描下列模式后重写。保留含义，并贴合既定语气。

## Patterns to detect and fix

规则编号是稳定 id，供其他 skill 引用。删除某条规则会留下空号。

### Content

3. **Superficial -ing phrases.** "highlighting...", "ensuring...", "reflecting...", "showcasing...", "fostering...". 删除或用具材来源展开。
5. **Vague attributions.** "Experts believe", "Industry reports suggest", "Some critics argue". 写明来源或删除。

### Language

7. **AI vocabulary.** Additionally, crucial, delve, enduring, enhance, fostering, garner, interplay, intricate, landscape (abstract), pivotal, showcase, tapestry (abstract), testament, underscore, vibrant. 换成平实用词。
8. **Fancy ways to say "is".** "serves as", "stands as", "boasts", "features". 用 "is" 或 "has"。
9. **"Not just X, but Y."** 直接陈述要点。
10. **Rule of three.** 不要强行凑成三组。用自然的数量。
11. **Synonym cycling.** Protagonist, main character, central figure, hero 同段轮番出现。选定一个并重复使用。
12. **False ranges.** "from X to Y" 而 X、Y 不在有意义的尺度上。直接列举主题。

### Style

13. **Em dash overuse.** 完全避免 em dash。只用句号或逗号（不用括号、en dash，也不用连字符冒充破折号）。若需分隔念头，结束该句或用逗号。
14. **Colon overuse.** 冒号可在列表或示例前使用，不要当作句中连接符。"If you're coming from traditional automation: instead of registering event handlers, you describe conditions" 里的冒号没有增加信息。改写为让要点独立成立、无需对比框架。"Describing when the scheduler should fire works best as plain English." 同义，且无拐杖标点。
15. **Boldface overuse.** 不要给每个专有名词或缩写加粗。
16. **Inline-header lists.** 特征是粗体标签加冒号重复该行内容："**Performance:** Performance improved...". 改成散文。粗体引语以句号结尾、点明条目且后跟真正新细节（"**Schema in TypeScript.** Tables live in one file."）可以接受，不算痕迹。
17. **Title case headings.** 使用 sentence case。
18. **Decorative emojis.** 从标题和列表项中移除。
19. **Curly quotes.** 使用 straight quotes。

### Communication artifacts

20. **Chatbot phrases.** "I hope this helps!", "Let me know if...", "Of course!", "Certainly!", "Found the smoking gun!" 删除。
22. **Sycophantic tone.** "Great question! You're absolutely right!" 直接回应。

### Filler

23. **Filler phrases.** "In order to" 改为 "To"。"Due to the fact that" 改为 "Because"。删除 "It is important to note that"。
24. **Excessive hedging.** "could potentially possibly be argued that it might" 改为 "may"。
25. **Generic conclusions.** "The future looks bright." 陈述具体计划或事实。

### Jargon

26. **Abstract metaphor nouns.** Substrate (base), wedge, wedge in (add), vector (way, method), locus, vantage, nexus, primitive as a noun, harness as a metaphor, surface as in "API surface", bedrock, scaffolding as a metaphor, modality, paradigm, gold-plating (more than the job needs), ratchet as a metaphor (the mechanism's real name, or a limit that only tightens), evacuate for moving code (move out), endgame (the last phase), north star, flywheel. 读起来像技术用语，但通常有更平实具体的词。选具体词。

### Plain speech

27. **Say what it does, not how it feels.** "the database stays close at hand", "SQL you can read", "types that follow your schema" 在说感受。改法应点明机制或数字："`.toSQL()` returns the exact string sent to the database", "a column rename fails the build"。先问这句话让读者做什么或知道什么，再写那个。若无法改写成具体指令、事实或数字，删掉。再查一遍：若句中原封不动出现在别的项目文档里，对本文毫无意义。删掉。
28. **Shorten or split dense sentences.** 若读者需回溯才能解析一句，拆成两句或删从句。一句一个想法。
29. **Active voice.** 优先主动语态。抓住 "is/are/was/were + past participle" 并点明施事："queries are validated" 改为 "the compiler validates queries"，"the file is parsed by the loader" 改为 "the loader parses the file"。仅当施事未知或确实不重要时可用被动。
30. **Cut adverbs, or use a stronger verb.** "runs quickly" 改为 "is fast" 或具体数字，"significantly improves" 改为测得 delta。副词撑弱动词说明动词选错了。
31. **Prefer the plain word.** "utilize" 与 "leverage" 改为 "use"，"facilitate" 改为 "help"，"numerous" 改为 "many"，"in the event that" 改为 "if"。更花哨的同义词很少更清晰。
32. **Mannered prose.** 有字面说法却用隐喻或辞藻：警句（"wire it or delete it"）、为效果用的修辞碎片、拟人化代码（"the plan holds it"）、比喻性动词（"rides along", "stands on"）、套话框架。"A dial worth turning" 改为 "a parameter worth varying"。直说本意。规则 26 涵盖隐喻名词。
33. **Over-compression.** 省略冠词、无动词碎片、符号语、缩写让读者解码而非阅读。"Parser rejects bad date → exit 2, no write" 改为 "The parser rejects a bad date, exits with code 2, and writes nothing." 写完整句，保留冠词与动词，展开箭头与缩写。
