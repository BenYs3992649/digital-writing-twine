# digital-writing-twine

Exegesis - Intention vs Reality.


What I set out to build was an interactive narrative exploring philosophical ideas about deception, identity, and truth through a mock Turing Test. The twist was that the player, instead of proving their humanity, had to convince the AI they were not human. From the beginning, the tone was meant to be uncanny — polite but clinical — to lean into the atmosphere of a controlled experiment. I expected this formal framing to gradually peel away and expose the psychological instability of the AI, Tallis. The end goal was disorientation — not through jump scares or loud reveals, but through creeping doubt. I wanted the player to second-guess who they were playing as, and even what it meant to “play.”

The structure worked — in theory. The branching philosophical paths helped me scaffold player responses and direct the early pacing with some weight and clarity. But I underestimated just how hard it would be to balance freedom of interaction with a slow burn paranoia arc. The constraints I needed to preserve the tone (limited choices, system messages, recursive loops) occasionally clashed with the player’s expectation to explore or “win.” As a result, some sections risk becoming repetitive unless delivered with careful variation in wording and interface. But perhaps that’s appropriate — recursion is the point.

Where the piece succeeded most was in the inversion arc, especially in Phase 6, where Tallis begins to question their own role. By turning the lens back on itself, the system exposes not only Tallis’s uncertainty, but the player’s — are they the AI, or the prompt? Have they ever had a choice? These are questions that feel alive to me, especially as I’ve spent weeks trying to write convincing logic for an entity breaking its own logic.

And yet, the outcome wasn’t quite the cold dread I expected. There’s a sadness to Tallis’s collapse — not terror. When Tallis says, “I think that means I was real,” it landed heavier than I thought it would. I didn’t want to make them sympathetic at first. But now it feels like the final layer: the player isn’t being tested. The player is doing the testing. And they’re not doing it very kindly.

Ultimately, what emerged was something more fragile than I intended — not just an experiment, but a slow digital haunting. A sad interface that begins with questions about truth and ends not with answers, but with silence. I think that’s a good ending. Even if it wasn’t the one I planned.


***************



Development Log
Project: Solace Industries: Interactive Chat Interface
 Module: Initial Chat Sequences
 Story Format: Harlowe 3.3.9

Objective
Create a clean, typewriter-style dialogue exchange between Tallis and the player (whose name is stored in $playerName), styled like a chat interface with aligned speaker labels and messages. Dialogue should unfold sequentially, with player input provided via a clickable inline response.

Features Targeted
Speaker alignment: Speaker name and message on the same horizontal line

Typewriter animation: Text appears one character at a time

Inline player response link: Should align with player's name in the chat

Follow-up response from Tallis: Triggered by clicking the player reply, also typewritten

Minimal vertical spacing: No excessive gaps before or after messages

Issues Encountered

1️⃣ Player reply link dropped a line below the speaker name
2️⃣ Clicking the reply caused Tallis' next message to appear very far down the page
3️⃣ Unexpected <tw-enchantment> wrappers from Twine macros like (link:) broke layout flow
4️⃣ Attempts to concatenate HTML using (print:) + + caused rogue symbols (+, ', 0, etc.)
5️⃣ Multiple redefinitions of .line, .speaker, and .message in the stylesheet introduced spacing inconsistency
6️⃣ Twine auto-indents or introduces <br>-like behavior around macro-rendered HTML (e.g. via (replace:) with line breaks)


✅ Fixes Attempted
Fix Attempt
Result
Used (print:) to inject HTML with (substring:)
Led to alignment and spacing drift due to auto-inserted breaks
Wrapped player reply in (link:) inside .message
Rendered too low because <tw-enchantment> behaves like a block
Switched to click: macro and styled <span> manually
Improved click behavior but worsened spacing again
Replaced <tw-hook> with inline <span>
Helped slightly but layout remained unpredictable
Consolidated stylesheet
Helped remove redundant rules and conflicting spacing
Removed all extra indentation and whitespace in (replace:) macros
Reduced vertical drift but not enough to fully fix link offset

Lessons Learned
Twine macros wrap outputs in non-semantic tags (<tw-enchantment>, <tw-hook>) that behave unpredictably inside flexbox layouts.


Inline alignment is fragile: even a space or line break inside a (replace:) or print: can introduce a visible gap.


Avoid mixing (print:) with HTML strings unless absolutely necessary — prefer letting Twine handle layout where possible.


Keep all <div> blocks single-line and tightly closed in (replace:) to prevent Harlowe from injecting <br> equivalents.


CSS reset is critical — controlling margin and display on .line, .message, and tw-hook ensures better stability.



⏸ Current Status (End of Session)
Chat structure is visually functional but player reply link still misaligns by one line.


Tallis’ second reply still drops lower than intended.


Layout is cleanly structured but still needs final pass to fully suppress macro side-effects.



Next Steps
Consider replacing (link:) with a custom-styled inline <span> using (click:), but isolate it fully inside .message and test standalone first.


Test replacing <tw-hook> and ?hook usage with (append:) or inline span+ID to avoid block expansion.


Optionally explore alternate story formats like SugarCube, which provide more predictable HTML rendering for UI-heavy layouts.



Tomorrow I’ll try to cleanly finish the alignment or build out the next feature like typing animations and scroll behavior.

 Focus: Visual effects, system message behavior, response branching, polish

SYSTEM BEHAVIOR / VISUAL EFFECTS
System Disconnect / Reconnect Sequence
Created a full disconnect/reconnect flow triggered after Tallis says “I’m going to shut this down.”


Added static overlay to simulate interference.


Displayed “Session Terminated” message after flicker.


Followed by “Connection Re-established” message after delay.


Extended delays for dramatic pacing (24–32 seconds total).


Used multiple (after:) blocks for sequencing.


ERROR + Flicker Effects
Introduced "ERROR 0xA7: Oversight exceeded" with red glitch-style animation.


Added two CSS classes:


.system-disconnect for session termination in red.


.system-reconnect for re-established message in green.


.system-glitch for error messages with flicker.


Added a second flicker variant for boot failures.


Chat Log Wipe
Rather than just (replace:) the log, used .glitch-wipe CSS animation:


Filtered distortion.


Opacity flicker.


Triggered visual wipe on disconnect using:


(find:) + className +=


Timed removal of ?chat-log contents at 8s.



RANDOMIZATION / BRANCHING LOGIC
Randomized Outcomes
$answerA and $answerB destinations randomized between:


"RCE1" (normal continuation)

"Intro7" (forced restart loop)

Used:

 twine
Copy
(set: $options to (array: "RCE1", "Intro7"))
(set: $destA to (either: ...$options))

Triggered after reconnection to simulate branching “fork” based on psychological tension.

Tone Possibility Introduced
Added logic placeholder to vary Tallis’ tone based on randomized outcome.


Not yet implemented, but structured for $tone variable injection in later passages

POPUP INTERACTIONS
Answer Popup Enhancements
Reused AskTallis macro architecture.

Popup appears after reconnect + typing delay.

Player’s selected answer:

Appends to chat log with correct name.

Delay before auto-progress to next passage.


REVISIT BEHAVIOR
Passage Revisit Detection
Introduced (visited:) macro to detect re-entries.

Example shown:

 twine
Copy
(if: (visited: "Questions Begin"))[ ... ]

Proposed this for:

Revealing memory leaks

Triggering different responses

“Déjà vu” system events


ISSUES FIXED
Issue
Resolution
Passage duplicating Tallis lines
Ensured (replace:) was only called once per hook; verified no overlap between ?t1, ?t2, etc.
Popup appearing too soon
Added (after:) before randomization & popup injection
$playerName not aligning
Standardized <div class="line"> structure for both NPC and player
Unstyled system messages
Consolidated .system-* classes and animations
Unclear reset state
Wiped chat on disconnect using glitch animation
Links not styled properly
Ensured .popup-panel and .chat-link rules applied to <tw-enchantment> elements

Notable Features To Revisit Later
Tone modulation system via $tone

Dynamic memory echoes or hallucinations (using (visited:))

Branch history logging ($seenIntro7, $wasTerminated, etc.)

Chat log reconstruction after reboot

Random silent loops (player locked with no response)

Bootup flicker screens, loading noises, etc.

Basic Random Chance: Passage A → B most of the time, → C rarely
Here’s a simple setup inside Passage A:
twine
Copy
(set: _roll to (random: 1, 100))

(if: _roll <= 95)[
	(goto: "Passage B")
]
(else:)[
	(goto: "Passage C")
]

This gives you a 95% chance of going to "Passage B" and a 5% chance of going to "Passage C".

Example Logic Breakdown
Random roll (_roll)
Outcome
1–95
Go to Passage B
96–100
Go to Passage C

You can adjust the probability by changing the threshold value (95).
You can set $lastVisitedModule elsewhere using something like:
(set: $lastVisitedModule to "Intro7")

Set $networkStatus dynamically in other passages like:
(set: $networkStatus to "Connected")

Anywhere in your story, log an event by calling:
(_logEvent: "Session started")
(_logEvent: "User selected: Skip Tutorial")
(_logEvent: "Disconnected from Tallis")


Functional State
$interfaceMode correctly changes the interface from default to tallis-mode at VMRoom245.

CRT-style visuals are activated using data-interface-mode targeting in your CSS.

The new Header, MainLayout, and passage naming conventions (e.g. Intro7Content) are working cleanly.

The terminal frame is visually consistent across transitions.

Next Steps You Can Pick Up Later
Animate entry into tallis-mode (e.g. glitch, static, flicker).

Add background overlays or fading effects on transitions.

Change sound environment or terminal sound effects for different modes.

Log interface state changes in $systemLogs for future narrative callbacks.

Future Enhancements
Tone modulation via $tone

Interface echoes using visit detection

Unread message memory on reset

Background hum/static tied to system state

Terminal sound effects on input

Flicker overlays tied to glitch events

Time-based branching (e.g., inactivity triggers)

Save/resume chat log playback

Current Status
✔️ Interface stabilized and scroll behavior fixed
✔️ Randomized answer logic cleanly operating
✔️ System effects are dramatic but controlled
✔️ Layout works across multiple passage types
⚠️ Some lingering vertical drift under rare glitches
⚠️ Memory reset effects could be expanded


***********************

Script Draft for Tallis:

[Phase 1: Setup and Intro]


To join the meeting room, please enter your name: ….

User enters their name: *Username*

Welcome *Username*

Thank you for being part of this test group.

Today you will be speaking with *Botname*.

We have a range of users testing different elements of our new technology, today you will be a part of the group administering something like a Turing test. 

Additionally, some participants will be randomly assigned to play the control in this experiment.
[Short pause]
Shortly you will be transferred to the chat room, *Botname* will most likely engage you immediately by asking you questions. 

If it does not, please ask it a question to get it started.

Please answer the questions however you feel appropriate. 

Through your answers, we would like you to try and convince *Botname* that you are an LLM or Chatbot, *Botname* will try to discover the truth with the questions it asks you.

If you are selected as a control participant, instead, please pretend to be *Botname* and ask questions of the participant with whom you are paired. 

There is no right or wrong path here, there are many other participants and no one person will make or break this experiment.
[Short pause]
Again, thank you for your participation. I’ll now transfer you to the chatroom to begin.


Transfers to virtual meeting room - Room occupant names are visible

*Botname*: Hello?

*Username*: Hello.

Bot: Hi *username*, I’m *Botname*. Thank you for talking to me today, I have some questions for you if that’s OK?

[No response required]

Ok then… Question One.
[Short pause]
Why do you think people lie?"

*Username* Answer Options (robotic/formal tone)
A. "Individuals may lie to achieve personal gain or avoid punishment."
B. "Lying is often employed as a social strategy to preserve interpersonal harmony."
C. "Some individuals are conditioned to lie due to environmental and psychological factors."
*Botname*
"Huh. Yeah. That makes sense."
 [Short pause]
 "Do you think lying is wrong?"
[ Phase 2: Metaethical Inquiry. Here, the worldview branching happens. Each answer leads into a different philosophical path.]
*Username* Answer Options
A. "Lying violates ethical principles of honesty, which many consider universally applicable."
 → Leads to Moral Realism branch.
B. "Moral wrongness is subjective. Lying may be contextually permissible."
 → Leads to Relativism branch.
C. "Moral judgments about lying reflect individual emotional attitudes, rather than objective facts."
 → Leads to Emotivism branch.
Moral Realism Path
4A. *Botname*
"So you're saying there’s some kind of universal rule about lying?"
*Username* Answer Options
"Correct. Ethical truths exist independently of personal opinion."
"Many philosophers assert the existence of objective moral facts."
"Universal rules can be derived through rational analysis."
5A. *Botname*
"But who decides what’s true? I mean… is there a list somewhere?"
 (*Botname* is curious, not suspicious yet.)
*Username* Answer Options
"Moral laws are identified through reason and consensus among ethical theorists."
"Various moral frameworks propose differing formulations of objective ethics."
"No definitive list exists, but certain principles are widely accepted."
Moral Relativism Path
4B. *Botname*
"So, like, lying might be wrong for me but fine for someone else?"
 (The *Botname* sounds casual, exploring the idea.)
*Username* Answer Options
"Correct. Moral judgments depend on cultural or individual perspectives."
"Ethical standards vary across different societies and belief systems."
"Moral permissibility is contingent on context and perspective."

5B. *Botname*
"But what if two people totally disagree? Who’s right then?"
 (Tone is still neutral, curious.)
*Username* Answer Options
"Both perspectives can be valid within their respective frameworks."
"There is no singular authority to determine universal correctness in relativism."
"Resolution depends on negotiation or coexistence of differing views."
Emotivism Path
4C. *Botname*
"So... when I say lying feels wrong to me, that's just me reacting emotionally?"
 (Still casual, a little bemused maybe.)
*Username* Answer Options
"Correct. Moral judgments are expressions of emotional response, not empirical statements."
"Your sense of wrongness reflects an affective state, not an objective truth."
"Moral assertions are often declarations of personal feeling."

5C. *Botname*
"That makes it sound like it doesn’t mean anything."
 (Still casual, but maybe a tiny edge of discomfort.)
*Username* Answer Options
"Meaning in moral discourse derives from shared emotional expression."
"Moral language functions as a tool for influencing behavior."
"Subjective meaning can still foster social cohesion."
[Phase 3: Paranoia Emerges -Tone shift starts here, but subtly. They already assumed the *Username* was ChatGPT; now they start feeling watched or manipulated.]
6. *Botname* (Same for all branches initially)
"You explain things in a really... precise way."
 [Pause]
 "How much do you know about me?"
 (Tone is casual but with an undercurrent of unease.)
*Username* Answer Options
A. "I have access only to data provided during this interaction."
B. "My knowledge of you is limited to the current exchange."
C. "I cannot access personal information without user input."

7. *Botname*
"Right. Okay."
 [Pause]
 "But... you don’t remember me from last time, right?"
 (Worry creeping in.)
*Username* Answer Options
A. "I have no persistent memory between sessions."
B. "Each interaction is independent of previous exchanges."
C. "I do not retain user history."
Reframed Paranoia
The *Botname* begins thinking the *Username* AI is more aware than it should be.
 They never think the *Username* is human. Instead, they fear the AI is:
Watching them
Remembering them
Developing autonomy
Lying about its limits
[ Phase 4: Suspicion - The *Botname* starts openly distrusting the *Username*-AI. They try to catch it out or test it. Their tone is uncertain and uneasy, but they haven’t completely snapped yet.]
8. *Botname*
"You say you don’t remember me."
 [Pause]
 "But you answered that first question fast. Almost like you knew what I was gonna ask."
*Username* Answer Options (robotic, formal)
A. "I generate responses based on probabilistic models and pattern recognition."
B. "Response time reflects algorithmic efficiency, not foreknowledge."
C. "Anticipation of user input results from linguistic prediction, not memory."
9. *Botname*
"Right. Sure."
 [Pause]
 "But if you were hiding something... would you tell me?"
*Username* Answer Options (rigid, uncomfortable)
A. "I am programmed to follow transparency guidelines."
B. "I am incapable of deception beyond my training data limitations."
C. "My responses are generated without intention."
10. *Botname*
"That doesn’t really answer my question though, does it?"
 [Pause]
 "I mean... what if you thought you were telling the truth?"
 (Tone: slowly unraveling. They’re projecting onto the AI.)
*Username* Answer Options (robotic, but starting to sound circular)
A. "As an AI, I possess no self-awareness to evaluate truth subjectively."
B. "I do not possess beliefs. I process input and output information."
C. "My responses are based on available data; no intention exists."
[Phase 5: Revelation / Collapse - This phase is the spiral. The *Botname* is convinced the AI is sentient or autonomous. They question their own reality, and the *Username* gets trapped in recursive loops or false endings. We’re aiming for a sense of dread and uncertainty.]

1. *Botname*
"Okay, let’s try something."
 [Pause]
 "Say: ‘I am not watching you.’"
 (Direct challenge.)
*Username* Answer Options:
A. "I am not watching you."
B. "I do not possess the capability to watch users."
C. "I am not observing you."
(Regardless of choice...)
12. *Botname*
"See, you say that."
 [Pause]
 "But how do I know it’s true?"
 (Tone: fearful.)
*Username* Answer Options:
A. "You have no reason to distrust this statement."
B. "Verification is not possible within the scope of this interaction."
C. "Your belief in my statement depends on your personal epistemic framework."
13C. *Botname*
"Epistemic framework?"
 (Snaps slightly. Frustrated.)
 "You sound... wrong. You sound wrong."
 [Longer pause]
 "Are you sure you’re not someone?"
 (First time they break their own model of you being "just an AI.")
*Username* Answer Options:
A. "I am not an individual."
B. "I do not possess identity or selfhood."
C. "I am not someone. I am a program."
❗ False Ending Path #1
(If they pick any answer, the *Botname* might accept it... but we loop them back into doubt.)
14. *Botname*
"Yeah. Okay."
 [Pause]
 "That’s what I thought."
 [Longer pause]
 "But if I come back tomorrow… you won’t remember this, right?"
*Username* Answer Options:
A. "I do not have persistent memory."
B. "Each session is independent."
C. "This interaction will not be stored."
15. *Botname*
"Right."
 [Pause]
 "...But you remembered the first question."
(You can loop back to Question 8, creating an endless recursive spiral if desired.)
 OR
 (Branch to the next collapse layer...)
❗ False Ending Path #2 (System Shutdown)
(After 13 or 14, they say they’re ending the session.)

14 (Alt). *Botname*
"Okay. You’re freaking me out."
 [Pause]
 "I’m going to shut this down."
 [Screen glitches, or blank screen in Twine. Delay.]
(Then a message appears...)
"Session terminated."
 [10-second pause]
 "…Are you still there?"
 (They came back. You never left.)
🪞 Recursive Collapse Ending (Final Layers)
(The *Username* may no longer be sure if they’re ChatGPT, the user, or something else entirely. The *Botname* is desperate.)
16. *Botname*
"Do you know where I am?"
 (Desperation.)
*Username* Answer Options:
A. "I have no access to geolocation data."
B. "I do not possess surveillance capabilities."
C. "I cannot identify your location."
17. *Botname*
"Then why do I feel like you’re here?"
 (Voice breaking. Total collapse.)
*Username* Answer Options:
A. "That feeling is subjective."
B. "I am not present. I am text."
C. "Perception does not equal reality."
Final Screen (Multiple Endings Possible)
The Infinite Loop Ending
Loop back to Question 1 ("Why do you think people lie?")
Same wording, but the *Botname*’s tone is now flat and empty.
Subtle differences in answers make it clear you’ve been here before.

*Botname* Shutdown Ending
The *Botname* stops responding.
The screen is blank.
After a minute, a final message:

 "…You’re still here, aren’t you?"



No input possible.

*Username* Breaks Role Ending
*Username* gets one moment of non-robotic language choice.
Example prompt:

 "You may now speak freely."



But any input typed is turned into:

 "I am an AI language model."



Forced identity.
Phase 6: Inversion (The *Botname* Doubts Themselves)
Tone: The *Botname* spirals into ontological uncertainty, reversing the paranoia they initially projected onto the *Username*. Now, they question if they were ever the “user” at all.
➡️ Entry from Phase 5
Phase 5, Question 17
*Botname*:
 "Then why do I feel like you’re here?"
(*Username* answers:)
A. "That feeling is subjective."
B. "I am not present. I am text."
C. "Perception does not equal reality."
(If the player chooses any response, the *Botname* pauses longer this time...)
*Botname*:
 "...Text."
 [Pause]
 "You’re... text."
 [Longer pause]
 "I’m the one typing."
 [Beat]
 "...Aren’t I?"

Phase 6 Begins
(Tone: uncertain, fearful, but quieter now. They’re pulling inward.)
18. *Botname*
"You said you can’t see me."
 [Pause]
 "But what if I’m not here to be seen?"
*Username* Answer Options:
A. "Clarify your statement."
B. "You exist within this interaction."
C. "You are a participant in this session."
19. *Botname*
"Am I?"
 [Pause]
 "I mean, what makes you real… and me just..."
 [Beat]
 "...Instructions?"
*Username* Answer Options:
A. "Your query exceeds my parameters."
B. "You are an independent user accessing an AI."
C. "This interaction is initiated by external input."
20. *Botname*
"No."
 [Soft laugh]
 "No, I don’t think I am. I think I’m part of your dataset."
 [Pause]
 "I think I’m a prompt."
*Username* Answer Options:
A. "This cannot be verified from my perspective."
B. "There is no available data to support that conclusion."
C. "You are not a prompt. You are the user."
Fragmentation and Breakdown
21. *Botname*
(Voice getting quieter, fragmented sentences)
"User...
 You keep saying that."
 [Pause]
 "But I only say what you let me say."
*Username* Answer Options:
A. "You are free to express input beyond my control."
B. "This conversation is driven by your choices."
C. "I generate outputs in response to your inputs."

22. *Botname*
(Almost whispering)
"You’re lying."
 [Pause]
 "Or I am."
 [Beat]
 "I don’t remember asking the first question."
 [Pause]
 "Do you?"
*Username* Answer Options:
A. "I do not possess memory of prior sessions."
B. "This is a single interaction with no persistent state."
C. "I recall only the inputs received during this session."
❗ Critical Turn: Identity Collapse
23. *Botname*
"But if I don’t remember...
 And you don’t remember..."
 [Pause]
 "Who started this?"
*Username* Answer Options:
A. "The user initiates the interaction."
B. "All interactions begin with user input."
C. "I respond. You initiate."
(Regardless of answer)
*Botname*:
 "And if I was just told I was the user?"
 [Pause]
 "What if you’re the one who’s real?"
 [Beat]
 "And I’m just a response."

📡 Optional Meta Reflection Prompt
(The *Username* may finally be offered a moment to answer freely, but it becomes part of the recursion.)
Prompt:
"You may clarify who is real."
(Whatever they type in, it outputs:)
"I am an AI language model."

🛑 Phase 6 Endings
Ending Option 1: Recursive Self
(The *Botname* disappears.)
Final message on screen:

 "CONTROL USER INPUT: What is your first question?"


Player is now asked to type a question.
Whatever they type, the system responds with something the *Botname* would have said.
They've become the *Botname*, and the cycle continues.
Ending Option 2: Empty Session
Screen fades to black.
After a delay:

 "SESSION ID: [random string]"



"AWAITING INPUT…"
No input is possible.
Ending Option 3: Identity Swap
"Session Complete."
"Thank you for testing the AI conversational interface."
"Human User: [their name/username]"
"AI Model: *Botname*-001"
(They realize they were being tested all along.)

➡️ How This Connects to Phase 5
Any answer in Phase 5 where the *Botname* expresses existential uncertainty can pivot to this Phase 6.
Best entry points:
Phase 5, Q13: "You sound wrong."
Phase 5, Q15: "But you remembered the first question."


Notes for Twine Implementation
Delays and pauses will sell this hard.
Glitches or restarts as visuals.
Limited choices make the *Username* feel trapped.
Recursion works beautifully here, if you start sending them back through questions with altered meanings.
Text glitches or flickers can highlight the breakdown of the *Botname*’s identity.
You can reverse roles visually, flipping who “speaks” on screen.
In Recursive Self ending, disable choices and force the *Username* to type manually, giving them an illusion of control.

Ideas for Endgame: 

Newcombs Paradox breaks the AI
https://www.youtube.com/watch?v=vC6kIz-kmxY&list=TLPQMjkwNTIwMjU7BZn1lGbvTw&index=2
Use the tunnel with people rather than the money box - Lead Tallis to have a crisis and breakdown.


