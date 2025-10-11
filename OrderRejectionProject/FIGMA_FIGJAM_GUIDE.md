# FigJam Flow Diagram Creation Guide

**Purpose:** Import Mermaid flowchart to FigJam and enhance with additional flows
**Date:** 2025-10-11
**Status:** Step-by-step instructions for beginners

---

## 🎯 What You'll Accomplish

1. ✅ Import your Mermaid flowchart into FigJam
2. ✅ Enhance it with pre-recording flows
3. ✅ Add post-recording analytics flows
4. ✅ Create a comprehensive system diagram

---

## 📋 Prerequisites

### What You Need:
- ✅ Figma account (free): https://figma.com/signup
- ✅ Mermaid code (already saved in `flowchart_mermaid.md`)
- ✅ 30 minutes of time

### Optional (for advanced users):
- Figma MCP setup (instructions in Method 3 below)

---

## 🚀 METHOD 1: Figma Plugin (RECOMMENDED - Easiest!)

**Best for:** Beginners, no export needed, fully editable

### Step 1: Open FigJam

1. Go to: **https://figma.com/figjam**
2. Click **"+ New FigJam file"**
3. Name it: **"Order Rejection System Flow"**

### Step 2: Install Mermaid Plugin

1. In FigJam, click **"Resources"** in the toolbar (or press **Shift + I**)
2. Go to **"Plugins"** tab
3. **Search:** "Mermaid Chart" or "Mermaid"
4. **Click** the plugin result
5. **Click "Install"** (it's free)

### Step 3: Run the Plugin

1. Click **"Resources"** again (or press **Shift + I**)
2. Go to **"Plugins"** tab
3. Find **"Mermaid Chart"** in your installed plugins
4. **Click to run** the plugin

### Step 4: Generate Your Flowchart

1. **Open** `flowchart_mermaid.md` (in this project folder)
2. **Copy** the entire code block (lines starting with "flowchart TD" to the end)
3. **Go back** to FigJam
4. **Paste** the code into the plugin window
5. **Click "Generate Diagram"** or "Create"
6. Your flowchart appears instantly! 🎉

**Advantages:**
- ✅ No export/import steps needed
- ✅ Creates native Figma objects (fully editable)
- ✅ Can change colors, move elements, resize boxes
- ✅ All done in one place

---

## 🖼️ METHOD 2: Screenshot Method (Simplest Backup)

**Best for:** Quick and dirty - if Method 1 doesn't work

### Step 1: Open Mermaid Live Editor

1. Go to: **https://mermaid.live/**
2. You'll see an editor with example code on the left, preview on the right

### Step 2: Paste Your Code

1. **Open** `flowchart_mermaid.md` (in this folder)
2. **Copy** the entire code block (starts with "flowchart TD")
3. **Go back** to Mermaid Live Editor
4. **Delete** the example code from the left panel
5. **Paste** your code
6. Your flowchart appears in the preview pane on the right! 🎉

### Step 3: Save the Image

**Option A: Right-click method**
1. **Right-click** on the diagram in the preview pane
2. Select **"Save image as..."** or **"Copy image"**
3. Save to your computer (or paste into FigJam)

**Option B: Screenshot method** (Mac)
1. Press **Cmd + Shift + 4**
2. Drag to select just the diagram area
3. Screenshot saves to desktop

**Option C: Look for download buttons**
- Some versions have a download icon near the preview pane
- Look for PNG or SVG buttons in the top toolbar

### Step 4: Import to FigJam

1. **Open FigJam:** https://figma.com/figjam
2. **Create new file:** Click "+ New FigJam file"
3. **Drag and drop** your saved image onto the canvas
4. Done! ✅

**Note:** This method gives you a static image, not editable objects

---

## 🛠️ METHOD 3: Figma MCP (Advanced - Programmatic)

**Best for:** Automation, integration with Claude Code

### Prerequisites Check

```bash
# Check if Node.js is installed
node --version

# If not installed:
brew install node
```

### Step 1: Install Figma MCP Server

```bash
npm install -g @modelcontextprotocol/server-figma
```

### Step 2: Get Figma Access Token

1. Go to: **https://www.figma.com/settings**
2. Scroll to **"Personal Access Tokens"**
3. Click **"Create new token"**
4. Name: `Claude Code MCP`
5. **Copy token** (IMPORTANT: Save it somewhere - you'll only see it once!)

### Step 3: Configure Claude Code

Create configuration file:

```bash
# Create directory if it doesn't exist
mkdir -p ~/Library/Application\ Support/Claude

# Create/edit config file
nano ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

Paste this configuration (replace YOUR_TOKEN with actual token):

```json
{
  "mcpServers": {
    "figma": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-figma"],
      "env": {
        "FIGMA_PERSONAL_ACCESS_TOKEN": "YOUR_TOKEN_HERE"
      }
    }
  }
}
```

Save and exit: **Ctrl+X, then Y, then Enter**

### Step 4: Restart Claude Code

Close and reopen Claude Code to load the MCP server.

### Step 5: Verify MCP is Working

Once restarted, I (Claude) will have access to Figma MCP tools:
- `mcp__figma__create_file`
- `mcp__figma__create_frame`
- `mcp__figma__create_shape`
- `mcp__figma__create_connector`

**Then I can programmatically create your flowchart!**

---

## 📐 Enhancing Your Flow Diagram in FigJam

Once you have the basic flowchart imported, here's how to enhance it:

### Layout Structure

```
┌─────────────────────────────────────────────────────────────┐
│                    TOP SECTION                               │
│  Pre-Visit Planning & Intelligence                           │
│  (Morning planning, route optimization, retailer insights)   │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                  MIDDLE SECTION                              │
│  Your Current Flow (Already in Mermaid)                      │
│  (Visit → Order attempt → Rejection → Recording)             │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                  BOTTOM SECTION                              │
│  Post-Recording & Analytics                                  │
│  (Actions, alerts, reporting, follow-ups)                    │
└─────────────────────────────────────────────────────────────┘
```

### Pre-Visit Planning Flows (ADD THESE)

**1. Morning Preparation:**
```
[Sales Rep Logs In]
    ↓
[System Shows Daily Beat Plan]
    ↓
[Displays Retailer List with:]
    • Last order date
    • Payment status
    • Historical rejection patterns
    ↓
[Rep Reviews & Prioritizes]
```

**2. Route Optimization:**
```
[System Calculates Optimal Route]
    ↓
[Considers:]
    • GPS locations
    • Traffic patterns
    • Priority retailers
    • Time windows
    ↓
[Rep Starts Journey]
```

**3. Pre-Visit Intelligence:**
```
[Rep Approaching Retailer]
    ↓
[System Pushes Alert:]
    • Retailer profile
    • Outstanding payment: ₹X
    • Last rejection reason
    • Competitor activity
    • Suggested talking points
    ↓
[Rep Prepared for Visit]
```

### Alternative Outcome Flows (ADD THESE)

**1. Partial Order (not full rejection):**
```
[Retailer Orders Some Items]
    ↓
[System Captures:]
    • Items ordered ✓
    • Items rejected ✗
    • Reasons for each
    ↓
[Records Partial Success]
```

**2. Deferred Order:**
```
[Retailer Says "Come Back Next Week"]
    ↓
[System Records:]
    • Deferral reason
    • Tentative date
    • Required conditions
    ↓
[Sets Follow-up Reminder]
```

### Post-Recording Flows (ADD THESE)

**1. Immediate Actions:**
```
[Rejection Recorded]
    ↓
[System Triggers:]
    • Payment issue? → Alert distributor
    • Service issue? → Notify support team
    • Stock issue? → Flag for supply chain
    ↓
[Suggests Next Steps to Rep]
```

**2. Real-Time Analytics:**
```
[Data Recorded]
    ↓
[System Updates:]
    • Rep dashboard (personal stats)
    • Territory dashboard (team patterns)
    • Brand dashboard (market insights)
    ↓
[Patterns Detected? → Manager Alert]
```

**3. Follow-Up Scheduling:**
```
[Rejection Logged]
    ↓
[System Suggests Follow-Up:]
    • Payment issue? → 1 week
    • Seasonal? → Before festival
    • Stock issue? → When cleared
    ↓
[Adds to Rep's Calendar]
```

---

## 🎨 Design Tips for FigJam

### Color Coding

Use consistent colors to indicate flow types:

- **🟢 Green:** Success paths, completed actions
- **🔴 Red:** Rejection, errors, alerts
- **🟡 Yellow:** Warnings, clarifications needed
- **🔵 Blue:** Data processing, AI/ML steps
- **⚪ Gray:** User decisions, input points

### Shape Guidelines

- **Rounded rectangles:** Processes, actions
- **Diamonds:** Decisions, branching points
- **Circles/Ovals:** Start/End points
- **Cylinders:** Database storage
- **Documents:** Reports, outputs

### FigJam-Specific Features

**1. Sections:**
- Use FigJam sections to organize flows
- Name sections: "Pre-Visit", "Core Flow", "Post-Recording"

**2. Sticky Notes:**
- Add notes for explanations
- Use different colors for: Questions, Ideas, To-Do, Notes

**3. Connectors:**
- Use arrows with labels
- Add conditions on arrows (e.g., "If payment pending")

**4. Widgets:**
- Add voting dots for prioritization
- Use timer for time estimates per step
- Add comments for team feedback

---

## 🔄 Step-by-Step Enhancement Process

### Phase 1: Import (Choose your method above)
- Import basic Mermaid flowchart ✓

### Phase 2: Organize
1. **Create sections** (3 horizontal bands)
2. **Move current flow** to middle section
3. **Label sections** clearly

### Phase 3: Add Pre-Visit Flows
1. **Top section:** Morning planning
2. **Add:** Route optimization
3. **Add:** Pre-visit intelligence
4. **Connect** to main flow

### Phase 4: Add Alternative Outcomes
1. **Branch from** "Order Attempt"
2. **Add:** Partial order path
3. **Add:** Deferred order path
4. **Merge back** to recording or follow-up

### Phase 5: Add Post-Recording Flows
1. **Bottom section:** Immediate actions
2. **Add:** Analytics updates
3. **Add:** Follow-up scheduling
4. **Connect** to end state

### Phase 6: Polish
1. **Align elements** (use FigJam's auto-layout)
2. **Add colors** (consistent scheme)
3. **Add annotations** (sticky notes for context)
4. **Add metrics** (time estimates, success rates)

---

## 📊 Example Enhanced Flow Structure

```
┌─────────────────────────────────────────────────────────────┐
│ PRE-VISIT PLANNING                                           │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  [Morning Login] → [Beat Plan] → [Route Opt] → [Intel Push] │
│                                                               │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        ↓
┌─────────────────────────────────────────────────────────────┐
│ CORE REJECTION RECORDING FLOW (Your Mermaid Diagram)        │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  [Visit] → [Order Attempt] → [Rejection] → [Record]          │
│                     ↓                                         │
│              [Partial Order]                                  │
│              [Deferred Order]                                 │
│                                                               │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        ↓
┌─────────────────────────────────────────────────────────────┐
│ POST-RECORDING & ANALYTICS                                   │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  [Trigger Actions] → [Update Dashboards] → [Schedule FU]    │
│                                                               │
│  [Manager Alerts] ← [Pattern Detection]                      │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

---

## 🎓 Learning Resources

### FigJam Tutorials:
- **Figma Learn:** https://help.figma.com/hc/en-us/articles/1500004424162
- **YouTube:** "FigJam for Beginners"

### Mermaid Documentation:
- **Official Docs:** https://mermaid.js.org/intro/
- **Flowchart Syntax:** https://mermaid.js.org/syntax/flowchart.html

---

## ✅ Checklist: Before You Start

- [ ] Figma account created
- [ ] `flowchart_mermaid.md` file accessible
- [ ] Method chosen (1, 2, or 3)
- [ ] 30 minutes blocked for work

## ✅ Checklist: After Import

- [ ] Basic flow imported to FigJam
- [ ] Canvas organized into 3 sections
- [ ] Pre-visit flows added
- [ ] Alternative outcomes added
- [ ] Post-recording flows added
- [ ] Colors and styling applied
- [ ] Annotations and notes added
- [ ] Team shared for feedback

---

## 🆘 Troubleshooting

### Issue: Mermaid code doesn't render
**Solution:** Check for syntax errors. Copy-paste exactly from `flowchart_mermaid.md`.

### Issue: SVG import looks blurry in FigJam
**Solution:** Use PNG at 2x resolution, or use Method 2 (Figma plugin) for native objects.

### Issue: Can't find Mermaid plugin in Figma
**Solution:** Make sure you're in Figma (not FigJam). Install plugin there, then use in FigJam.

### Issue: MCP not working after setup
**Solution:**
1. Check token is correct
2. Restart Claude Code completely
3. Run `npx -y @modelcontextprotocol/server-figma` in terminal to test

---

## 🎯 Next Steps

After creating your FigJam diagram:

1. **Share with team** for feedback
2. **Present to stakeholders** for approval
3. **Use as reference** for engineering implementation
4. **Update regularly** as system evolves

---

## 📞 Need Help?

- **Figma Community:** https://forum.figma.com/
- **Mermaid Discussions:** https://github.com/mermaid-js/mermaid/discussions

---

**Document Version:** 1.0
**Last Updated:** 2025-10-11
**Your Guide:** Claude Code AI Assistant 🤖
