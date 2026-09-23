---
name: learn-vocab
description: >-
  Automates the creation and updating of an interactive, web-based English vocabulary notebook (eng_vocab.html). Generates phonetics, meanings, sentences, a short story, and morphology analysis.
---

# Learn Vocab

## Overview
This skill automates the process of adding new English vocabulary words to your personal web-based vocabulary notebook (`eng_vocab.html`). When provided with a list of words and a date, it researches the words, generates a short reading story, analyzes morphology, and injects all of this into the interactive HTML application (which includes text-to-speech, a memory game, and a quiz).

## Quick Start
Tell the agent:
"Use the learn-vocab skill to add the following words for 2026/9/20: apple, banana, cherry."

## Workflow

When invoked, strictly follow these steps:

### 1. Input Validation and Correction
- Review the list of English words provided by the user.
- If any word is misspelled or is not a valid English word, **PAUSE** execution and ask the user for clarification. Do not proceed until the user confirms the correct spelling.

### 2. Content Generation
For the confirmed list of words, generate the following content in your thought process:
1.  **Vocabulary Table Data**: For each word, determine its part of speech, traditional Chinese meaning, a simple English example sentence (with a traditional Chinese translation), and its KK phonetic symbol (e.g., `[kəmˋpjutɚ]`).
2.  **Short Story**: Write a simple English short story of **approximately 150 words**. The story MUST use ALL the vocabulary words provided. Keep the vocabulary level simple.
3.  **Bonus Annotations**: If the user provides extra words to annotate, append them below the story.
4.  **Morphology Analysis**: Group the words by common prefixes, roots, or suffixes, and provide a brief morphological explanation (in Traditional Chinese) to help the user memorize them.

### 3. File Initialization & Repair
- The target file is `eng_vocab.html` in the user's current project directory.
- Check if `eng_vocab.html` exists and contains the necessary HTML structure (e.g., `<div class="tab">`, `<div id="NewTabTemplate" class="tabcontent">`, `<script>` tags for the Quiz and Game).
- If the file is missing or severely corrupted, automatically recreate the base HTML template. The template must include:
    - CSS styling for tabs, tables, TTS buttons (`.speak-btn`), and memory game cards (`.memory-card`).
    - The top Tab navigation buttons (including the `QuizTab` and `GameTab` buttons).
    - The `QuizTab` HTML (with multiple choice and dictation modes).
    - The `GameTab` HTML (memory flip game).
    - The `NewTabTemplate` block.
    - The Javascript logic for tab switching, Web Speech API integration (seeking a female English voice), the Quiz logic (modes: mc and dict), and the Memory Game logic.

### 4. HTML Injection
- Modify `eng_vocab.html` using the `replace_file_content` tool.
- **Add Tab Button**: Insert a new tab button (e.g., `<button class="tablinks" onclick="openTab(event, 'Date-YYYYMMDD')">📅 YYYY/M/D</button>`) immediately before the `QuizTab` button.
- **Add Content Block**: Insert the new vocabulary content as a new `<div id="Date-YYYYMMDD" class="tabcontent">` block before the `QuizTab` content block.
- Format the vocabulary as an HTML table (`<thead>` and `<tbody>`). Each English word must be wrapped in `<strong class="word">word</strong>`, followed immediately by a line break and the KK phonetic symbol inside a span: `<br><span style="color:#7f8c8d; font-size:0.9em;">[phonetic]</span>`. This exact structure is required for the Text-to-Speech Javascript to inject the 🔊 button correctly.
- Add the 150-word short story and the morphology analysis below the table inside a styled `.story` div.
- **Update Quiz Javascript (`wordList`)**: You MUST update the `const wordList = [...]` array inside the `<script>` tag. Assign a new `lesson` number for the new day, and add all new vocabulary words with this `lesson` number.
- **Update Quiz Scope Logic**: You MUST insert a new Quiz scope button in the QuizTab UI (e.g. `<button id="scope-6-btn" ...>`). Then, update the `toggleQuizScope` Javascript logic to ensure that the scope check limit (e.g., `currentQuizScope.length === X`) and the loop array (e.g., `[1, 2, ..., X].forEach`) account for the newly added lesson number. Ensure the default state remains that no scopes are selected (`currentQuizScope = []`).

### 5. Retroactive Word Addition (Appending to an existing day)
If the user requests to add one or more words to an *existing* date (rather than creating a new day), you must:
1. Insert the new word into the existing vocabulary table.
2. **Rewrite** the existing short story to naturally incorporate the new word.
3. Update the existing morphology analysis to include the new word.
4. Update the Javascript `wordList` to include the new word with the correct, existing `lesson` number.

### 6. Completion
- Inform the user that the notebook has been updated and encourage them to open `eng_vocab.html` to review their new words, play the memory game, or take the quiz.
- Ask the user if they would like you to run `git add`, `git commit`, and `git push` (using `BypassSandbox: true` if needed) to backup the changes to GitHub.
