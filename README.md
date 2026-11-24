# Voca – a voice-first vocabulary coach

Voca teaches you a word, listens to the sentence you say with it, and gives you feedback. You earn XP for good sentences and unlock a no-hints test to move up a level.

**Live demo:** _add your link here (for example `https://your-project.pages.dev`)_

![Voca screenshot](screenshot.png)
_Add a screenshot or a short screen recording and save it as `screenshot.png` in this repo._

---

## Why I built it

Most vocabulary apps test recognition ("pick the right meaning") but never make you use the word. I wanted to build something closer to a coach: hear a word, say it in your own sentence, and get feedback straight away. It was also my first end-to-end product project, so I treated it like one: a clear user problem, a learning loop, and many rounds of testing and fixes.

## What it does

- **Level check.** A 6-question quiz places you at Beginner, Intermediate or Advanced.
- **Voice practice.** Voca says a word, its meaning and an example. You answer out loud (or type) with your own sentence.
- **Feedback.** It checks that you used the word correctly. When AI checking is on, it also corrects grammar, and a sentence with the right word but a grammar slip earns partial credit.
- **Pronunciation practice.** It repeats the word slowly, twice, with a replay button. Typed answers are rejected here, because this exercise is for speaking.
- **Progress, not jumps.** Each good sentence earns 10 XP. At 100 XP a level-up test unlocks: 5 questions, no hints, and you need 4 correct. You move up one level at a time.
- **Smart review.** Words you get wrong come back later in the session, and words you struggle with are chosen first next time.
- **Settings.** Voice choice, volume, speaking speed, a voice test, a microphone test, dark mode, and a progress summary.
- **Feel-good touches.** Confetti and a short chime on a correct answer (the chime follows the volume setting).

Content: 60 words across three levels (20 per level), each with a meaning and an example sentence.

## How it works

```
You speak  →  browser speech recognition  →  text
text  →  checks (length, target word, spelling)  →  AI grading (optional)  →  feedback
feedback  →  browser text-to-speech  →  Voca speaks
```

- **Front end only.** One HTML file with plain JavaScript, no framework and no build step.
- **Speech.** The browser's built-in Web Speech API handles speech recognition and text-to-speech.
- **Progress** is saved on your own device in the browser's local storage. There are no accounts.
- **AI grading** needs a small server to keep an API key private (see "Current status" below).

## Run it locally

Opening the file by double-clicking works for most things, but browsers often block or forget the microphone for local files. Run a tiny local server instead:

```
py -m http.server 8000        # Windows
python3 -m http.server 8000   # Mac / Linux
```

Then open `http://localhost:8000` in Chrome or Edge.

## Deploy it (free)

This is a static site, so any static host works. I use **Cloudflare Pages** connected to this GitHub repo:

1. Put `index.html` at the top level of the repo.
2. In Cloudflare: Workers & Pages → Create application → Pages → Import an existing Git repository.
3. Choose this repo, branch `main`, and `exit 0` as the build command (there is nothing to build).
4. Deploy. Every change pushed to GitHub then publishes automatically.

## Current status and known limitations

- **AI grading is off on a plain static host.** The version in this repo runs in a "basic check mode": it confirms the target word appears in a sentence of four or more words, but it can't judge meaning or grammar. The full grammar and meaning feedback was built and tested inside Claude, where an AI model is available to the page.
- **Next step:** add a small serverless function that calls an AI model with a key stored as a secret, and have the page call that function, falling back to basic mode if it is busy or out of quota.
- **Browser support.** Voice input works in Chrome and Edge (and partly Safari) over `https` or `localhost`. Firefox has no speech recognition, so users type instead.
- **Voices vary by device.** Some voices ignore the volume setting, and very slow speech can sound distorted on some Android phones, so the slow rate is raised on Android.
- **Word list is small.** 60 words is enough to test the loop, not to build a vocabulary.

## What I'd do next

- Move the word list into its own file and grow it, with part of speech and difficulty for each word.
- Add the AI function above so grading works everywhere.
- Add a daily streak and a short end-of-session summary.
- Test with real learners and measure how many finish a session and move up a level.

## Product decisions worth noting

- **Level up with a test, not just XP.** Practice builds XP, but the move to the next level needs an unaided test, so learners don't drift upward without really knowing the words.
- **One level at a time.** The placement quiz sets a start point, and after that progress is gradual.
- **Partial credit for grammar slips.** Using the word correctly counts, but a corrected sentence is spoken back so the learner hears the right version.
- **Honest about limits.** The app says when it is in basic mode, and the pronunciation feature says it can't score individual sounds.
- **Privacy.** Progress stays on the device. When AI checking is on, the sentences a learner submits are sent to an AI model for grading, and the app says so.

## Built with

HTML, CSS and JavaScript, the Web Speech API, and Cloudflare Pages for hosting. Built with AI assistance (Claude): I defined the product, made the design decisions, tested on desktop and a phone, and directed the fixes.

## Author

Rajath R Bhat · [LinkedIn](https://linkedin.com/in/rajathbhat12) · [GitHub](https://github.com/rajathbhat12-jpg)
