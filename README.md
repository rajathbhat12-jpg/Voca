# Voca: a voice-first vocabulary coach

**Live app:** https://voca.rajathbhat12.workers.dev

Voca teaches you a word, listens to a sentence you say or type using it, and gives you feedback and grammar corrections. It is a single-page web app that runs in the browser, with no account and no sign-up.

> **How this was built.** Voca is an AI-assisted ("vibe-coded") project. I did not hand-write the code. I defined the product, tested it, logged the bugs, collected user feedback, and directed an AI coding assistant (Claude) to implement the changes. This repository is a product-management project as much as a coding one: the decisions, the defects found, and the changes made in response to real users are documented in the [product log](#product-log).

---

## What it does

- **Placement test.** A short adaptive quiz (about 3 to 7 questions) places you at Novice, Beginner, Intermediate or Advanced. Every question has an "I don't know" option, so nobody has to guess.
- **Practise out loud.** Voca says a word, gives its meaning and an example, then asks you to use it in your own sentence, by voice or by typing.
- **Feedback you can act on.** An AI model checks that the word is used correctly and naturally, and that the grammar is right. If not, you get an explanation and a corrected sentence.
- **XP and levels.** 10 XP for a clean sentence, 5 XP if the word is right but there is a grammar slip. At 100 XP a no-hints level-up test unlocks (5 mixed challenges, 4 correct to pass).
- **Pronunciation help.** Hear a word again, or hear it slowly.
- **Easier words on request.** After three wrong answers in a row, Voca offers to move you to the level below.
- **80 words** across four levels (20 per level).
- **Settings.** Choose a voice (a female English voice is the default where available), volume, speaking speed, and light or dark mode.

## How it works

```
Browser (index.html)  ──►  Grading Worker (Cloudflare)  ──►  Workers AI (Llama 3.3 70B)
   │                           │
   │  Web Speech API           └─ checks the request, asks the model,
   │  (speech in / out)           cleans up the JSON, returns a verdict
   └─ localStorage (your progress stays on your device)
```

1. **The page** holds the word lists, XP and level logic, and the screens. It is one static HTML file.
2. **Rule-based checks run first, in the browser.** The sentence must contain the target word (or a valid form of it), have enough words, and not just repeat the word. Misheard speech on rare words is handled for spoken answers only.
3. **The AI grades the rest.** The page sends a short prompt to a small Cloudflare Worker, which calls Workers AI and returns a verdict (correct, grammar issues, corrected sentence).
4. **The rules have the last word.** If the AI is too generous, the rule-based checks overrule it, so answers can't be over-credited and XP can't be farmed by repeating one answer.
5. **If the AI is unavailable** (for example, the free daily limit is used up), Voca falls back to a basic check and tells you so with a visible notice.

### Why a separate Worker?
A browser page can't safely call an AI service directly, because any key placed in the page could be copied. The Worker acts as a middleman. It accepts requests only from the app's own origin, limits requests per IP, accepts only Voca's own prompts, and caps prompt length and output size.

## Tech stack

| Part | Choice |
|---|---|
| App | One HTML file with plain JavaScript and CSS |
| Speech | Browser Web Speech API (recognition and synthesis) |
| Hosting | Cloudflare (Workers static assets, deployed from this repo) |
| AI grading | Cloudflare Workers AI, `@cf/meta/llama-3.3-70b-instruct-fp8-fast` |
| Storage | `localStorage` in the learner's browser |

## Privacy

- Progress is saved only on your device.
- When AI checking is available, the sentences you submit are sent to an AI model hosted by Cloudflare to be graded. Voca does not store them.
- The microphone listens only while the mic button is lit.

## Known limitations

- **Voice input works best in Chrome or Edge.** Safari is less reliable and Firefox has no speech recognition. Typing always works.
- **Rare words are sometimes misheard** by the browser's speech recognition. Voca corrects close mishearings on spoken answers, but it can still miss.
- **AI grading is not perfect.** The free model is weaker than larger ones and can be inconsistent on grammar judgements.
- **Daily AI quota.** The free tier covers roughly a few hundred graded sentences a day across all users. After that, Voca uses the basic check until the quota resets.
- **Progress is per browser.** Clearing browser data, or using another device, starts fresh.
- **No licence has been chosen yet.**

## Run it yourself

Voca is a static page, so you can open `index.html` in a browser to try it. Without the grading Worker, it runs in basic-check mode.

To enable AI grading, deploy your own Worker, give it a Workers AI binding named `AI`, and set the `GRADE_URL` constant in `index.html` to your Worker's address. The Worker also needs your site's address in its allowed-origins list.

## Product log

This project is documented as a product, not just code. The log covers:

- **User research and feedback:** what friends, family and early users said, and what changed because of it.
- **Bugs found and fixes:** each defect with steps to reproduce, severity, the fix I directed, and how it was verified.
- **Decisions:** the problem, the evidence, the options considered, the choice made, and the result.

Highlights so far:

- **XP farming.** Repeating one answer earned XP every time. Fixed so each word earns XP once per session.
- **Spoken commands swallowing sentences.** Starting a sentence with "Another…" was treated as the "next" command. Commands now count only in very short utterances.
- **No AI outside the original development environment.** The app moved to a Cloudflare-hosted grading Worker on a free tier, with a visible fallback when AI is unavailable.
- **Words too hard for non-fluent learners** (feedback from a family member). Added a Novice level and an adaptive placement test. In a simulation of 1,000 random guessers, wrong placement at Intermediate or above fell from about 17% (old fixed quiz) to about 2%.
- **Cluttered home page.** Replaced by one-idea-per-card onboarding for new users and a single Continue button for returning users.

## Roadmap

- Competitor analysis of Duolingo: adopt what works, fix what doesn't.
- A daily-word reminder (installable web app with notifications).
- A visual redesign.
- A harder level above Advanced.
- Collecting user numbers (completion, day-2 return) so decisions rest on data.

## Author

**Rajath R Bhat**, Bengaluru, India. [GitHub](https://github.com/rajathbhat12-jpg) · [LinkedIn](http://www.linkedin.com/in/rajathbhat12)

Feedback is welcome. Open an issue or message me.
