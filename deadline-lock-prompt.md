# Deadline Lock: prompt for Google Gemini / AI Studio

Paste everything between the lines into Gemini (or Google AI Studio, "Build" mode). Then use the follow-up prompts at the bottom to change anything.

---

You are a front-end developer. Build ONE self-contained HTML file (HTML, CSS and JavaScript in the same file, no frameworks, no build step) called "Deadline Lock". Work through the steps below in order. When you finish, output the complete file in a single code block, then list 5 things I can customize.

## What it is
A page with three things: (1) a countdown lock screen wallpaper I can save to my phone or computer, (2) a step-by-step plan to the deadline, and (3) a daily reminder I can add to my calendar. It is pre-set for this deadline, but I can change the name and date to count down to anything.

Default deadline name: "Contest entry due"
Default deadline: 11:59 PM Pacific Time on Nov 23, 2026 (store it as UTC: 2026-11-24 07:59:00)
Default start date for the plan: Nov 14, 2026. When I change the deadline, the start date becomes today.

## Step 1: Page layout
- Title: "Deadline Lock". One-line intro under it.
- Two columns on screens wider than 760px: left column = phone-shaped wallpaper preview; right column = three panels (Your deadline, Step-by-step plan, Daily reminder). On narrow screens stack them in one column. The page must never scroll sideways, with at least 16px side padding.
- Support light and dark mode with CSS variables: light (background #f3f4f7, card #ffffff, text #14182b, muted #5b6178, border #dde0ea, accent #d9480f) and dark (background #0d1020, card #161a30, text #eceef8, muted #9aa1bd, border #2a2f4a, accent #ff8a4c). Switch automatically with prefers-color-scheme.
- Fonts from Google Fonts: Space Grotesk (headings, numbers) and Inter (body). Visible keyboard focus on all controls.

## Step 2: Wallpaper preview (HTML canvas)
- A canvas of 1170 x 2532 pixels, shown scaled to about 340px wide inside a rounded phone frame (near-black border, 44px radius).
- Background: vertical gradient from #0b0e1d (top) to #1b1230 (bottom). This wallpaper is always dark, whatever the page theme.
- Keep the TOP 40% completely empty so the phone clock and widgets fit. Keep the BOTTOM 280px empty for the flashlight and camera buttons.
- Left margin 96px. Draw, in this order:
  1. Deadline name in capitals, 40px Inter 600, color #ff8a4c, at y=1060.
  2. The big number at y=1400: the number of whole days left, 360px Space Grotesk 700, color #f4f5fb. Beside it the unit word ("day" or "days"), 84px Space Grotesk 600. If under one day is left, show hours instead. If time is up, show 0 and "time is up".
  3. A line at y=1480, 52px Inter, color #a8aecb: "{h}h {m}m left · due {Mon D}".
  4. A progress bar at y=1530, 16px tall, full width minus margins. Track #2a2f4a, fill #ff8a4c, filled by the share of time from the start date to the deadline.
  5. The milestone list starting at y=1680. Each row has a circle marker, the date (44px Inter, #a8aecb), then a short label (48px Space Grotesk, #f4f5fb). Milestones already passed are grey (#6f7697) with a filled circle. Today's milestone is orange (#ff8a4c), bold, with a filled circle. Future ones have an outlined circle. Row height is the smaller of 120px and 560 divided by the number of milestones.
- Redraw every 30 seconds, and after the fonts finish loading.
- A button "Make wallpaper image" draws the canvas, converts it to a PNG data URL, and shows it as an image below the preview with the note: "On a phone, press and hold the image, then save it and set it as your lock screen. On a computer, right-click and save." Do not rely on download links, they may be blocked.

## Step 3: Your deadline panel
- A live countdown in large text, updating every second, formatted "9d 05:12:44" (tabular numbers). Under it: the name and the due date and time in the viewer's own time zone.
- Two inputs: "What is due" (text, max 28 characters) and "Due date and time" (datetime-local). Changing either updates the wallpaper, plan and reminder immediately.
- A "Reset to contest deadline" button.
- Save the name, date and checkbox ticks in localStorage inside try/catch so the page still works if storage is blocked.

## Step 4: Step-by-step plan
Generate milestone dates from the start date to the due date. Let total = number of days between them. Use these 7 steps with these positions (round to whole days, keep dates strictly increasing, drop any middle step that would land on the last two days):
1. 0% - short label "Define it" - "Define the finish line: write one sentence on what you will hand in."
2. 20% - "First draft" - "Get a rough version working end to end, even if ugly."
3. 40% - "Build the core" - "Finish the main piece. Cut anything optional."
4. 60% - "Test on phone" - "Set it as your wallpaper. Check the clock space and that text is readable at a glance."
5. 80% - "Polish" - "Tidy wording, spacing and colors. Make the cover image and description."
6. total minus 1 day - "Submit early" - "Submit a day early. Fix only real problems after that."
7. total - "DEADLINE" - "Deadline day: confirm your entry went through."
Show each as a card with a checkbox, the date, a title and the description. Highlight today's step with an orange border. Checked steps are crossed out.
When the default contest deadline is selected, also show a "Contest entry checklist" with checkboxes: Project title; Cover image; Project description; Project link; Submitted before 11:59 PM PT on Nov 23.

## Step 5: Daily reminder panel
- A time input (default 09:00).
- Show the message that the reminder will say: "{name}: {current step title}. {days left} days left."
- Button "Add to Google Calendar": a link opening https://calendar.google.com/calendar/render?action=TEMPLATE with text, details, dates (today at the chosen time, 15 minutes long) and recur=RRULE:FREQ=DAILY;COUNT={days left}. Open it in a new tab.
- Button "Copy for any calendar (.ics text)": builds a VCALENDAR with one daily repeating VEVENT (same time, same COUNT) and a VALARM at the start time, and copies it with navigator.clipboard.writeText. If copying fails, select the text instead. Explain: paste it into a plain text file, save as deadline.ics, open it.

## Step 6: Quality checks before you answer
- No console errors. All text is readable at 400px wide. No horizontal scroll.
- Use textContent (not innerHTML) for anything the user types.
- Test mentally with the deadline set to tomorrow, to 3 hours from now, and to a past date. The countdown, milestones and reminder must all still make sense.

Then output the full file.

---

## Follow-up prompts (paste one at a time to change things)
- "Change the wallpaper colors to [your colors] and the font to [font]."
- "Change the default deadline to [name] on [date and time, time zone]."
- "Replace the 7 plan steps with these steps: [your steps and when each is due]."
- "Make the wallpaper for a 16:9 computer screen (1920x1080) with the countdown on the right and the clock space on the left."
- "Add a second wallpaper style: light background, minimal, only the big number and the deadline name."
- "Add a motivational line under the countdown that changes each day."
- "Let me add and delete my own milestones with their own dates."
