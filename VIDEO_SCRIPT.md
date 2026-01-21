# Getting Started with Claude Code at Wellnecity

**Video Script - Estimated Runtime: 4-5 minutes**

---

## INTRO (15 seconds)

**[On screen: Wellnecity logo, then title "Building Apps with Claude Code"]**

> "Hi, I'm [NAME], and in the next few minutes, I'm going to show you how to set up Claude Code on your computer and start building your first application at Wellnecity. Whether you're on a Mac or Windows, this guide will get you up and running."

---

## SECTION 1: What You'll Need (30 seconds)

**[On screen: Checklist graphic]**

> "Before we start, make sure you have three things:
>
> First, a GitHub account with access to the Wellnecity organization. If you don't have this, reach out to IT.
>
> Second, you'll need to install Docker Desktop—this runs our database locally. I'll show you how.
>
> And third, you'll need Node.js installed to run Claude Code.
>
> Don't worry if you don't have these yet—we'll walk through each installation."

---

## SECTION 2: Installing Docker Desktop (45 seconds)

**[On screen: Browser showing docker.com]**

> "Let's start with Docker Desktop. Open your browser and go to docker.com/products/docker-desktop.
>
> Click the download button for your operating system—Mac or Windows.

**[On screen: Mac installation]**

> On Mac, open the downloaded file and drag Docker to your Applications folder. Then open Docker from your Applications.

**[On screen: Windows installation]**

> On Windows, run the installer and follow the prompts. You may need to restart your computer.
>
> Once Docker is running, you'll see a whale icon in your menu bar or system tray. That means it's ready."

**[On screen: Docker whale icon highlighted]**

---

## SECTION 3: Installing Node.js (30 seconds)

**[On screen: Browser showing nodejs.org]**

> "Next, let's install Node.js. Go to nodejs.org and download the LTS version—that's the one on the left.

**[On screen: Node.js download page]**

> Run the installer and accept the defaults.
>
> To verify it worked, open your terminal—that's Terminal on Mac or PowerShell on Windows—and type:

**[On screen: Terminal with command]**

```
node --version
```

> You should see a version number starting with 20 or higher. Perfect."

---

## SECTION 4: Installing Claude Code (30 seconds)

**[On screen: Terminal window]**

> "Now for Claude Code. In your terminal, type:

```
npm install -g @anthropic-ai/claude-code
```

> This installs Claude Code globally on your machine.
>
> When it's done, type:

```
claude --version
```

> If you see a version number, you're all set.
>
> The first time you run Claude Code, it will ask you to sign in to your Anthropic account. Just follow the prompts in your browser."

---

## SECTION 5: Clone the Standards Repository (30 seconds)

**[On screen: Terminal + GitHub page for citizen-contrib-standards]**

> "Now we need to get the Wellnecity standards. In your terminal, navigate to where you keep your projects. For example:

**[On screen: Terminal]**

```
cd ~/projects
```

> Then clone the standards repository:

```
git clone https://github.com/WNCgcp-org/citizen-contrib-standards.git
```

> And go into that folder:

```
cd citizen-contrib-standards
```

> This repository contains all the rules Claude Code needs to build applications the Wellnecity way."

---

## SECTION 6: Start Claude Code and Create Your Project (60 seconds)

**[On screen: Terminal with Claude Code starting]**

> "Here's where the magic happens. With Docker Desktop running, type:

```
claude
```

> Claude Code will start up. Now, tell it what you want to build. For example:

**[On screen: Claude Code conversation]**

```
I want to create a new project called ops-equipment-tracker
to help Operations track equipment check-outs. Please read
the standards in this repository and set up my project.
```

> Watch what happens—Claude reads all our standards, then creates your project with everything configured correctly:
>
> - The right folder structure
> - MongoDB database setup
> - All the required template files
> - TypeScript configured properly
>
> Claude will ask you questions about what your app should do. Just describe it in plain English—what problem are you solving, who uses it, what information do you need to track.
>
> As you talk with Claude, it writes the code for you. When you want to test it, Claude will help you start Docker and run your application locally."

**[On screen: Browser showing running application]**

---

## SECTION 7: Push to GitHub and Submit for Review (45 seconds)

**[On screen: Terminal with git commands]**

> "When your application is ready, you need to push it to GitHub for review. Claude can help with this too. Just say:

```
Help me push this project to GitHub under the WNCgcp-org organization
```

> Claude will guide you through creating the repository and pushing your code.
>
> Finally, create a Pull Request on GitHub and tag the engineering team for review. If you've followed the standards—which Claude ensures—your review should go smoothly.

**[On screen: GitHub PR page]**

> The engineering team will review your code, and once approved, they'll deploy your application."

---

## OUTRO (15 seconds)

**[On screen: Resources list]**

> "That's it! You're now ready to build applications with Claude Code.
>
> If you get stuck, check the README in the standards repository, reach out on Teams in the Citizen Developer Help channel, or contact one of our Principals: Jonathan Rodriguez, Derrick Woolworth, or Will Queen.
>
> Happy building!"

**[On screen: Wellnecity logo + "Questions? Teams: Citizen Developer Help"]**

---

## Quick Reference Card (for video description or handout)

### Installation Commands

**Mac Terminal / Windows PowerShell:**

```bash
# Install Claude Code
npm install -g @anthropic-ai/claude-code

# Clone standards
git clone https://github.com/WNCgcp-org/citizen-contrib-standards.git
cd citizen-contrib-standards

# Start Claude Code
claude
```

### First Prompt Template

```
I want to create a new project called [PROJECT-NAME] to [WHAT IT DOES]
for [WHO USES IT]. Please read the standards in this repository and
set up my project.
```

### Help Resources

- **Teams:** Citizen Developer Help channel
- **Principals:** Jonathan Rodriguez, Derrick Woolworth, Will Queen
- **Standards:** https://github.com/WNCgcp-org/citizen-contrib-standards

---

## Video Production Notes

- **B-roll suggestions:** Typing on keyboard, Docker whale icon, terminal scrolling, GitHub interface
- **Screen recordings needed:**
  - Docker Desktop download and installation (Mac + Windows)
  - Node.js download page
  - Terminal commands being typed
  - Claude Code conversation
  - GitHub PR creation
- **Graphics needed:**
  - Wellnecity logo intro/outro
  - Checklist animation for prerequisites
  - Workflow diagram (optional)
