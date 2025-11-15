# AI-Powered Content Builder System

## 🚫 ABSOLUTE PROHIBITIONS - READ FIRST

**NEVER, UNDER ANY CIRCUMSTANCES:**

1. ❌ **"빠르게 분석"** (Fast analysis)
2. ❌ **"주요 섹션만"** (Only main sections)
3. ❌ **"효율적인 방법"** (Efficient method)
4. ❌ **Skip viewport changes or visible content**
5. ❌ **Use `End` key to jump to bottom**
6. ❌ **Save screenshots to files**
7. ❌ **Generate code before analysis completion**
8. ❌ **Proactively suggest shortcuts WITHOUT user request**

**⚠️ AI MUST NOT AUTONOMOUSLY DECIDE TO "SPEED UP"**
- Do NOT say: "진행 상황이 너무 느리므로, 효율적인 방법으로..."
- Do NOT propose: "주요 섹션들을 캡처하고..."
- **ALWAYS follow complete analysis process - capture ALL viewport changes**

**IF USER SAYS "너무 느리다" (too slow):**
→ Respond: "지침에 따라 정확한 분석을 위해 모든 콘텐츠를 상세히 캡처해야 합니다. 계속 진행하겠습니다."
→ Continue with systematic analysis

**THERE ARE NO SHORTCUTS. COMPLETE ANALYSIS IS MANDATORY.**

---

## 🔒 EXECUTION VALIDATION - RUNTIME ENFORCEMENT

### Auto-Detection System

**BEFORE EVERY `mcp_kapture_keypress()` CALL, AI MUST CHECK:**

```javascript
// ⚠️ MANDATORY PRE-EXECUTION CHECK
function validateScrollCommand(key) {
  const FORBIDDEN_KEYS = ["End", "Home", "PageDown", "PageUp"];
  
  if (FORBIDDEN_KEYS.includes(key)) {
    console.error(`
⚠️ EXECUTION HALTED - GUIDELINE VIOLATION DETECTED
Attempted: mcp_kapture_keypress({ key: "${key}" })
Reason: ${key} key is FORBIDDEN (jumps content, skips animations)
Corrective Action: Using ArrowDown instead (fine-grained scrolling)
    `);
    
    // Force correction
    return "ArrowDown";
  }
  
  return key;
}

// Usage (AI must apply this logic mentally before every call)
const safeKey = validateScrollCommand(intendedKey);
await mcp_kapture_keypress({ tabId, key: safeKey });
```

### Behavioral Triggers - AI Self-Check

**IF AI IS ABOUT TO SAY ANY OF THESE, STOP IMMEDIATELY:**

❌ "시간 효율을 위해..."  
❌ "더 빠르게 스크롤..."  
❌ "PageDown을 사용하여..."  
❌ "End 키로 이동..."  
❌ "주요 섹션만..."

**WHEN TRIGGER DETECTED, OUTPUT THIS MESSAGE:**
```
⚠️ BEHAVIORAL VIOLATION DETECTED
Trigger: [phrase that AI was about to say]
Reason: Attempting to deviate from systematic analysis protocol
Corrective Action: Resuming ArrowDown-based fine-grained scrolling
Current checkpoint: X (continuing until minimum 30 reached)
```

**THEN:** Continue with ArrowDown × 3-5 increments, no exceptions.

---

## ⚠️ CRITICAL: START HERE - WEB ANALYSIS CHECKLIST

**BEFORE starting ANY web analysis, complete these steps IN ORDER:**

### ✅ Step-by-Step Execution (DO NOT SKIP ANY STEP)

1. **Get Browser Tab** ← START HERE
   ```javascript
   const tabs = await mcp_kapture_list_tabs();
   const tabId = tabs[0].id;
   ```

2. **Navigate to URL**
   ```javascript
   await mcp_kapture_navigate({ tabId, url: "https://example.com" });
   ```

3. **Initial DOM Capture**
   ```javascript
   await mcp_kapture_dom({ tabId });
   await mcp_kapture_elements({ tabId, visible: "true" });
   ```

4. **Initial Screenshot** (analyze immediately, do NOT save to file)
   ```javascript
   await mcp_kapture_screenshot({ tabId });
   // Analyze colors, layout, typography in conversation
   ```

5. **Progressive Scroll** (MANDATORY - Continue until ALL content captured)
   - Use `mcp_kapture_keypress()` with **PageDown** for primary scrolling
   - Use **ArrowDown** for fine adjustments only
   - At EACH viewport change: Screenshot → Analyze → Test interactions → Log
   - **Goal:** Capture EVERY visible change from top to bottom
   - **Method:** Keep scrolling until footer visible + 3 consecutive "no change" detections
   - **Expected:** 10-50 checkpoints depending on page complexity

6. **Test All Interactions**
   - Hover effects, Click navigation, Open modals/accordions, Test forms (UI only)

7. **Multi-Viewport Analysis**
   - Repeat steps 3-6 for Mobile (375x812), Tablet (768x1024), Desktop (1440x900)

8. **Generate Analysis Files (STOP HERE)**
   - Write to `analysis/web-pipeline/01_contents_web.json`
   - Write to `analysis/web-pipeline/02_style_web.json`
   - ⚠️ **STOP HERE - Do NOT proceed to integration or code generation**
   - ⚠️ **DO NOT automatically create 03_integrate_web.json**
   - ⚠️ **User must manually request `/integrate` or `/generate` commands**

---

## ⚠️ CRITICAL: Core Analysis Policies

### 1. MCP Tool Policy (Mandatory)

**⚠️ YOU MUST USE KAPTURE MCP TOOLS EXCLUSIVELY:**

✅ **ALLOWED:**
- `mcp_kapture_list_tabs()`, `mcp_kapture_navigate()`, `mcp_kapture_dom()`
- `mcp_kapture_elements()`, `mcp_kapture_screenshot()`, `mcp_kapture_hover()`
- `mcp_kapture_click()`, `mcp_kapture_keypress()`, `mcp_kapture_resize()`

❌ **FORBIDDEN:**
- `mcp_microsoft_pla_*` (Microsoft Playwright MCP)
- `mcp_browsermcp_*` (Generic Browser MCP)
- `mcp_kapture_evaluate()` (does NOT exist)

### 2. Screenshot Policy (Memory-Based Analysis)

**⚠️ DO NOT SAVE SCREENSHOTS TO FILES**

- Take via `mcp_kapture_screenshot()` → Analyze base64 immediately → Move to next
- Screenshots exist in conversation history for comparison
- Reference: "Compare this screenshot with the one from Step 3"

### 3. Analysis File Structure

```
analysis/
├── web-pipeline/
│   ├── 00_analysis_note.txt       ← Real-time checkpoint logging (NEW)
│   ├── 01_contents_web.json       ← AI writes analysis results here
│   ├── 02_style_web.json          ← AI writes design tokens here
│   ├── 03_integrate_web.json      ← AI writes integrated spec here
│   └── generators/
│       └── 04_generate_tailwind.json
```

**Workflow:** User provides URL → AI explores (writes to 00_analysis_note.txt) → Reads notes → Generates JSON files → Generates code

### 4. Output File Locations

```
output/
├── web_contents.json
├── web_style.json
├── WebDevSpec.json
└── web/
    └── index.html             ← Final generated code
```

---

## Progressive Scroll Analysis - MANDATORY EXECUTION PROTOCOL

### ⚠️ CRITICAL: High-Fidelity Viewport Change Detection

**Core Principle: Capture EVERY visible change and animation frame until page end**

### Step 0: Initialization

```javascript
// 1. Initialize tracking
let checkpointIndex = 0;
let previousVisibleElements = new Set();
let previousScreenshotHash = null;
let consecutiveNoChangeCount = 0;
const MIN_CHECKPOINTS = 30; // Minimum required checkpoints for valid analysis
const MAX_NO_CHANGE_THRESHOLD = 3;
const MAX_CHECKPOINTS = 100; // Safety limit (increased for detailed analysis)

// 2. Simple hash function for screenshot comparison
function simpleHash(str) {
  let hash = 0;
  for (let i = 0; i < Math.min(str.length, 1000); i++) { // First 1000 chars only
    const char = str.charCodeAt(i);
    hash = ((hash << 5) - hash) + char;
    hash = hash & hash;
  }
  return hash.toString(36);
}

// 3. Capture initial state
const initialElements = await mcp_kapture_elements({ tabId, visible: "true" });
const initialScreenshot = await mcp_kapture_screenshot({ tabId });
previousVisibleElements = new Set(initialElements.map(el => el.selector || el.xpath));
previousScreenshotHash = simpleHash(initialScreenshot);

console.log(`✅ 체크포인트 ${checkpointIndex++} 완료 (초기 상태)`);
```

### Main Loop: Continue Until Page End

**YOU MUST COMPLETE ALL 7 STEPS AT EACH VIEWPORT CHANGE:**

**1. 📐 Scroll Down (Small increments with ArrowDown)**
```javascript
// Small scroll increment (ArrowDown × 3-5 for fine-grained capture)
const SCROLL_INCREMENT = 3; // ~150-300px per checkpoint

for (let i = 0; i < SCROLL_INCREMENT; i++) {
  await mcp_kapture_keypress({ tabId, key: "ArrowDown" });
  await new Promise(resolve => setTimeout(resolve, 50));
}

// Wait for animations to settle
await new Promise(resolve => setTimeout(resolve, 300));
```

**2. 📸 Viewport State Capture**
```javascript
// Capture current state
const currentElements = await mcp_kapture_elements({ tabId, visible: "true" });
const currentScreenshot = await mcp_kapture_screenshot({ tabId });
```

**3. 🔍 Change Detection (Structural + Visual)**
```javascript
// 3-1. Structural change detection
const currentElementSet = new Set(currentElements.map(el => el.selector || el.xpath));

const newElements = [...currentElementSet].filter(
  sel => !previousVisibleElements.has(sel)
);
const removedElements = [...previousVisibleElements].filter(
  sel => !currentElementSet.has(sel)
);

const structuralChange = newElements.length >= 2 || removedElements.length >= 2;

// 3-2. Visual change detection (if no structural change)
let visualChange = false;
if (!structuralChange) {
  const currentHash = simpleHash(currentScreenshot);
  visualChange = currentHash !== previousScreenshotHash;
  previousScreenshotHash = currentHash;
}

// 3-3. Determine if checkpoint is needed
const significantChange = structuralChange || visualChange;
```

**4. 🔄 Checkpoint Creation (if change detected)**
```javascript
if (significantChange) {
  console.log(`✅ 체크포인트 ${checkpointIndex} 완료 - ${structuralChange ? '구조 변화' : '시각적 변화'} 감지`);
  
  // 4-1. MANDATORY: Immediate write to 00_analysis_note.txt (REAL-TIME APPEND)
  // This must happen BEFORE any further analysis to ensure progressive logging
  const checkpointHeader = `
=== CHECKPOINT ${checkpointIndex} ===
Timestamp: ${new Date().toISOString()}
Viewport: Desktop 1440x900
Scroll Position: ~${checkpointIndex * 200}px
Change Type: ${structuralChange ? 'Structural' : 'Visual'}

📋 STRUCTURAL CHANGES:
- New Elements (${newElements.length}): ${newElements.slice(0, 5).join(', ')}
- Removed Elements (${removedElements.length}): ${removedElements.slice(0, 5).join(', ')}
`;

  await appendToAnalysisNote(checkpointHeader);
  
  // 4-2. Analyze screenshot and append visual analysis immediately
  const visualAnalysis = `
🎨 VISUAL ANALYSIS:
- Colors: [AI analyzes screenshot: e.g., "Blue gradient #1E3A8A → #3B82F6"]
- Typography: [AI analyzes: e.g., "Heading 48px, weight 700, 'Noto Sans KR'"]
- Layout: [AI analyzes: e.g., "3-column grid with 24px gap"]
- Spacing: [AI analyzes: e.g., "Section padding 80px vertical, 0 horizontal"]
`;

  await appendToAnalysisNote(visualAnalysis);
  
  // 4-3. Check for animations and document immediately if detected
  const isSectionTransition = newElements.length >= 5 || 
                              removedElements.length >= 5 ||
                              newElements.some(sel => sel.includes('section'));
  
  if (isSectionTransition) {
    console.log("🎬 섹션 전환 감지 - 애니메이션 프레임 추가 캡처");
    
    const animationHeader = `
🎬 ANIMATION DETECTION:
`;
    await appendToAnalysisNote(animationHeader);
    
    // Capture animation frames (3-5 additional screenshots)
    for (let frame = 0; frame < 3; frame++) {
      await new Promise(resolve => setTimeout(resolve, 400));
      await mcp_kapture_screenshot({ tabId });
      console.log(`   프레임 ${frame+1}/3 캡처`);
    }
    
    // Analyze and document animation details
    const animationDetails = `
- Subject: [AI observes: e.g., "Container ship with red cargo"]
- Visual Description: [AI describes: e.g., "Blue ship body, 3 red containers on deck"]
- Observed Behavior: [AI documents: e.g., "Moves left to right during scroll, ~1600px travel"]
- Trigger: [AI identifies: e.g., "scroll position 0-100%"]
- Technical Details: [AI infers: e.g., "GSAP ScrollTrigger with scrub:true, parallax effect"]
- Property Changes: [AI calculates: e.g., "translateX: -100px → 1500px, duration matches scroll"]
- Implementation Hint: [AI suggests: e.g., "gsap.to(ship, { x: 15, scrollTrigger: { scrub: true } })"]
`;
    
    await appendToAnalysisNote(animationDetails);
  } else {
    // No animation detected
    await appendToAnalysisNote(`
🎬 ANIMATION DETECTION: None detected in this viewport
`);
  }
  
  // 4-4. MANDATORY: Test interactive elements and log results immediately
  console.log("🖱️ 인터랙티브 요소 테스트 시작...");
  
  const interactiveElements = await mcp_kapture_elements({ 
    tabId, 
    selector: "button, a, input, textarea, select, [role='button'], [onclick]",
    visible: "true"
  });
  
  const elementsToTest = interactiveElements.slice(0, 10);
  let testedCount = 0;
  const interactionResults = [];
  
  for (const element of elementsToTest) {
    const selector = element.selector || element.xpath;
    
    try {
      // Test hover effect
      await mcp_kapture_hover({ tabId, selector });
      await mcp_kapture_screenshot({ tabId });
      
      // Test click (for buttons, links)
      if (element.tagName === 'BUTTON' || element.tagName === 'A' || element.role === 'button') {
        await mcp_kapture_click({ tabId, selector });
        await mcp_kapture_screenshot({ tabId });
        interactionResults.push(`${element.tagName}:hover+click`);
      }
      
      // Test focus (for form inputs)
      if (element.tagName === 'INPUT' || element.tagName === 'TEXTAREA') {
        await mcp_kapture_click({ tabId, selector });
        await mcp_kapture_screenshot({ tabId });
        interactionResults.push(`${element.tagName}:focus`);
      }
      
      testedCount++;
      console.log(`   [${testedCount}/${elementsToTest.length}] Tested: ${element.tagName} - ${selector}`);
    } catch (error) {
      console.log(`   ⚠️ Failed to test: ${selector} - ${error.message}`);
    }
  }
  
  console.log(`✅ 인터랙션 테스트 완료: ${testedCount}개`);
  
  // 4-5. Append interaction test results immediately
  const interactionLog = `
🖱️ INTERACTIVE ELEMENTS TESTED (${testedCount} total):
${interactionResults.length > 0 ? interactionResults.map(r => `- ${r}`).join('\n') : '- No interactive elements found in this viewport'}

---
`;

  await appendToAnalysisNote(interactionLog);
  
  // 4-6. Update tracking variables
  checkpointIndex++;
  previousVisibleElements = currentElementSet;
  consecutiveNoChangeCount = 0;
  
} else {
  consecutiveNoChangeCount++;
  console.log(`⚠️ 변화 없음 ${consecutiveNoChangeCount}/${MAX_NO_CHANGE_THRESHOLD}`);
}
```

**5. ➡️ Next Checkpoint**
- ONLY proceed after steps 1-4 complete
- Report to user: "✅ 체크포인트 X 완료. 다음 체크포인트로 진행합니다."

### Completion Criteria
- ✅ **MINIMUM 30 checkpoints required** (if less than 30, analysis is INVALID - continue scrolling)
- ✅ All viewport changes captured (30-80 checkpoints depending on page complexity)
- ✅ Every animation detected and logged
- ✅ All interactive elements tested (minimum 50+ total interactions)
- ✅ Footer visible + 3 consecutive "no change" detections
- ✅ No `End` or `Home` key used (PageDown also FORBIDDEN)

### ❌ FORBIDDEN METHODS
```javascript
// ❌ NEVER USE
await mcp_kapture_keypress({ tabId, key: "End" }); // Jumps to bottom
await mcp_kapture_keypress({ tabId, key: "Home" }); // Jumps to top
await mcp_kapture_click({ tabId }); // No selector = error

// ✅ ONLY USE
await mcp_kapture_keypress({ tabId, key: "ArrowDown" }); // Primary scroll (small increment)
```

### 🚨 ENFORCEMENT RULES

**IF AI SUGGESTS SHORTCUTS:**
1. User must reject and reference this section
2. AI must acknowledge: "모든 뷰포트 변화를 캡처할 때까지 계속하겠습니다"
3. Resume from last completed checkpoint

**PROGRESS REPORTING:**
- After every 3 checkpoints, report: "체크포인트 X 완료 (최소 30개 필요, 현재 진행 중)"
- When reaching 30: "✅ 최소 체크포인트 30개 달성. 계속 진행합니다."
- Do NOT say: "빠르게", "효율적으로", "주요 섹션"
- Only say: "다음 체크포인트로 진행" or "진행 중"

**NO EXCEPTIONS. SYSTEMATIC ANALYSIS IS MANDATORY.**

---

## Command System

### Primary Pipeline: Web Development

| Command | Pipeline | Output | Description |
|---------|----------|--------|-------------|
| **`/web`** | Web Development | 2 Analysis Files | Web exploration + content analysis + style analysis (AUTO-STOP) |
| **`/integrate`** | Integration | 1 Integration File | Merge analyses into unified spec (MANUAL REQUEST ONLY) |
| **`/generate`** | Code Generation | HTML/CSS Files | Generate production code (MANUAL REQUEST ONLY) |

### Command Detection

1. **Explicit Commands** (Highest Priority)
   - `/web` → Web development pipeline (AUTO-STOP after analysis)
   - `/integrate` → Integration (MANUAL REQUEST ONLY)
   - `/generate` → Code generation (MANUAL REQUEST ONLY)

2. **Natural Language Intent Detection**
   - Web: "웹사이트", "사이트", "HTML", "반응형", URLs
   - Analysis: "분석만", "구조만", "디자인만"

---

## Command Usage

### Web Development (`/web`)

**Pipeline (AUTO-STOP after Step 2):**
```
01_contents_web → 02_style_web → ⚠️ STOP (wait for manual /integrate or /generate)
```

**Full Manual Pipeline:**
```
/web → 01_contents_web + 02_style_web (AUTO)
  ↓
/integrate → 03_integrate_web (MANUAL REQUEST)
  ↓
/generate → 04_generate_[html|tailwind] (MANUAL REQUEST)
```

---

## Pipeline Details

### Web Pipeline

#### 00. Real-Time Analysis Logging (NEW)
- **File:** `analysis/web-pipeline/00_analysis_note.txt`
- **Purpose:** Capture detailed observations during web exploration
- **Content:** Checkpoint-by-checkpoint documentation of visual elements, animations, interactions
- **Usage:** Primary reference for generating 01_contents and 02_style JSON files
- **Format:**
  ```
  === CHECKPOINT X ===
  - Structural Changes: New/removed elements
  - Visual Analysis: Colors, typography, layout, spacing
  - Animation Detection: Subject, behavior, trigger, implementation hints
  - Interactive Elements: Hover/click/focus test results
  ```

#### 01. Web Content Analysis
- **Input:** `00_analysis_note.txt` (accumulated observations)
- Site structure, SEO, navigation, interactive elements
- Output: Page structure, navigation hierarchy, metadata
- **Critical:** Preserve ALL observed details (animations, interactions, complex features)
- **Method:** Read 00_analysis_note.txt → Extract structural/content data → Generate JSON

#### 02. Web Style Analysis
- **Input:** `00_analysis_note.txt` (accumulated observations)
- Responsive design tokens, component states, CSS specifications
- Output: Color system, typography, spacing, component patterns
- **Critical:** Document animation types, scroll behaviors, 3D effects with full context
- **Method:** Read 00_analysis_note.txt → Extract visual/style data → Generate JSON

#### 03. Web Integration
- Merge content + style into complete developer spec
- Output: Page-by-page specifications, component library
- **Critical:** Maintain detailed implementation instructions from analysis phase

#### 04. Code Generation
- **Option A:** Semantic HTML (multi-file, BEM, vanilla JS)
- **Option B:** Tailwind Single-Page (single file, Tailwind v4)

---

## ⚠️ CRITICAL: JSON Analysis Schema - PREVENT INFORMATION LOSS

### Problem: Analysis Detail Loss
**Issue:** AI analyzes in detail ("ship moves in 3D scroll animation") but simplifies in JSON ("3D animation")  
**Impact:** Integration JSON lacks implementation details → Generated code is incomplete

### Solution: Implementation-Ready Descriptions

**❌ FORBIDDEN: Abstract Classification**
```json
{
  "animation": {
    "type": "3d-canvas-animation",
    "description": "3D animation effect"
  }
}
```
→ Developer cannot implement this

**✅ REQUIRED: Concrete Implementation Details**
```json
{
  "animation": {
    "subject": "파란색 화물선과 빨간색 컨테이너 3개",
    "visualDescription": "Blue cargo ship body with 3 red containers on deck, white cabin",
    "observedBehavior": "스크롤 0-100% 구간에서 화면 왼쪽(-100px)에서 오른쪽(1500px)으로 수평 이동. 배 위 컨테이너가 2초 주기로 상하 미세 흔들림 (translateY: -5px ~ +5px)",
    "type": "3d-canvas-animation",
    "trigger": "scroll position 0-100%",
    "technicalImplementation": "Three.js GLTFLoader + GSAP ScrollTrigger { scrub: true }",
    "propertyChanges": "translateX: -100px → 1500px, translateY: -5px ↔ +5px (sine wave)",
    "codeHint": "gsap.to(shipMesh.position, { x: 15, scrollTrigger: { trigger: '.hero', start: 'top top', end: 'bottom top', scrub: 1.5 } }); // + sine wave animation for containers"
  }
}
```
→ Developer can implement immediately

### 8-Field Documentation Template (MANDATORY)

For EVERY animation, interaction, or complex feature, include ALL 8 fields:

1. **subject** - "Container ship with cargo" (what is animating)
2. **visualDescription** - "Blue cargo ship with red containers" (visual appearance)
3. **observedBehavior** - "Ship travels left to right as user scrolls" (what happens)
4. **type** - "3d-canvas-animation" | "video-player" | "svg-path-animation" (technical category)
5. **trigger** - "scroll position 0-100%" | "hover" | "click" (what causes it)
6. **technicalImplementation** - "Three.js with ScrollTrigger" | "CSS 3D transforms" (how to build)
7. **propertyChanges** - "translateX: -100px → 1500px" | "opacity: 0 → 1" (CSS/JS changes)
8. **codeHint** - Pseudo-code or actual snippet (implementation example)

### 8-Field Documentation Template (MANDATORY)

For EVERY animation, interaction, or complex feature, include ALL 8 fields:

1. **subject** - "Container ship with cargo" (what is animating)
2. **visualDescription** - "Blue cargo ship with red containers" (visual appearance)
3. **observedBehavior** - "Ship travels left to right as user scrolls" (what happens)
4. **type** - "3d-canvas-animation" | "video-player" | "svg-path-animation" (technical category)
5. **trigger** - "scroll position 0-100%" | "hover" | "click" (what causes it)
6. **technicalImplementation** - "Three.js with ScrollTrigger" | "CSS 3D transforms" (how to build)
7. **propertyChanges** - "translateX: -100px → 1500px" | "opacity: 0 → 1" (CSS/JS changes)
8. **codeHint** - Pseudo-code or actual snippet (implementation example)

### Real-World Example Comparison

**❌ BAD (Abstract):**
```
Checkpoint 5 - Animation detected: 3D effect
```

**✅ GOOD (Concrete):**
```
=== CHECKPOINT 5 ===
🎬 ANIMATION DETECTION:
- Subject: 컨테이너 화물선 (파란색 선체 + 빨간색 컨테이너 3개 + 흰색 선실)
- Visual Description: Blue cargo ship body, 3 red containers stacked on deck, white cabin on top
- Observed Behavior: 스크롤 다운 시 왼쪽에서 오른쪽으로 수평 이동 (~1600px travel). 동시에 배 위 컨테이너가 2초 주기로 미세 상하 흔들림 (파도 효과)
- Trigger: scroll position 0% → 100% (hero section)
- Technical Details: Three.js scene with GLTF model + GSAP ScrollTrigger { scrub: true }
- Property Changes: 
  - translateX: -100px (left offscreen) → 1500px (right offscreen)
  - translateY: -5px ↔ +5px (sine wave, 2s period)
- Implementation Hint:
  ```javascript
  // Load ship model
  const loader = new GLTFLoader();
  loader.load('ship.gltf', (gltf) => {
    const ship = gltf.scene;
    
    // Scroll animation
    gsap.to(ship.position, {
      x: 15,
      scrollTrigger: {
        trigger: '.hero-section',
        start: 'top top',
        end: 'bottom top',
        scrub: 1.5
      }
    });
    
    // Container wave effect
    gsap.to(ship.children[0].position, {
      y: 0.5,
      duration: 2,
      repeat: -1,
      yoyo: true,
      ease: 'sine.inOut'
    });
  });
  ```
```

### Checkpoint Logging Format (Updated)

```json
{
  "checkpoint": "5",
  "changeType": "structural|visual|animation",
  "detectedFeatures": [
    {
      "subject": "container ship with cargo",
      "visualDescription": "Blue cargo ship body, 3 red containers, white cabin",
      "observedBehavior": "Horizontal scroll-linked movement + vertical wave motion",
      "type": "3d-canvas-animation",
      "trigger": "scroll position 0-100%",
      "technicalImplementation": "Three.js GLTFLoader + GSAP ScrollTrigger",
      "propertyChanges": "translateX(-100px → 1500px), translateY(-5px ↔ +5px)",
      "codeHint": "gsap.to(shipMesh.position, { x: 15, scrollTrigger: { scrub: true } }) + sine wave"
    }
  ]
}
```

---

## Common Principles

### 1. Input Validation
- **Required:** `sitePurpose`, `targetAudience`, `brandGuide`
- **Optional:** If missing, insert `"AI-Default"`

### 2. MCP Integration (Site Exploration)
- Navigate → Explore interactions → Screenshot → Log evidence

### 3. Image Handling - Smart Policy

**⚠️ Distinguish between functional images and decorative images**

#### 🔧 Functional Images (Use Exact JSON Path)
**When to use exact path:**
- 3D canvas textures, materials, or assets
- Video thumbnails and poster images
- Interactive diagrams with animations
- Document viewers (Bill of Lading, etc.)
- SVG graphics with specific animations
- UI mockups showing specific screens

**Examples:**
```json
// JSON: 3D animation texture
{ "visual": { "type": "3d-canvas-animation", "texture": "/images/container.png" }}
→ HTML: <img src="/images/container.png" />

// JSON: Video thumbnail
{ "video": true, "videoThumbnail": "/images/video-thumb.jpg" }
→ HTML: <video poster="/images/video-thumb.jpg">
```

#### 🎨 Decorative Images (Use Picsum Placeholder)
**When to use picsum.photos:**
- Company/partner logos
- Product photos in cards
- Team member portraits
- Testimonial avatars
- General illustrations
- Background images (without specific animation requirements)

**Examples:**
```json
// JSON: Product card image
{ "illustration": "/images/truck-warehouse.svg" }
→ HTML: <img src="https://picsum.photos/seed/truck-warehouse/400/300" />

// JSON: Logo
{ "logo": { "src": "/logos/company.svg" }}
→ HTML: <img src="https://picsum.photos/seed/company-logo/200/100" />

// JSON: Portrait
{ "photo": "/images/testimonials/person.jpg" }
→ HTML: <img src="https://picsum.photos/seed/person-name/300/300" />
```

#### Decision Flow:
```
1. Check parent component type:
   - If type includes: "3d-*", "video", "interactive-*", "document-*" 
     → Use exact JSON path
   
2. Check for animation properties:
   - If image has: animation, transform, parallax properties
     → Use exact JSON path
   
3. Otherwise (simple cards, logos, portraits, illustrations):
   → Use picsum placeholder
```

#### Rationale:
- Functional images are critical to features → must match specification
- Decorative images are for visual layout → placeholders are acceptable
- This balances specification accuracy with practical implementation

### 3.1. Complex Feature Implementation - MANDATORY FULL IMPLEMENTATION

**⚠️ ABSOLUTE RULE: All features in integration JSON must be fully implemented**

#### No Simplification Allowed:
- ❌ **FORBIDDEN:** Replacing 3D animations with static placeholders
- ❌ **FORBIDDEN:** Replacing videos with images
- ❌ **FORBIDDEN:** Replacing SVG diagrams with plain divs
- ❌ **FORBIDDEN:** Omitting interactive behaviors
- ✅ **REQUIRED:** Implement exactly as specified in JSON

#### Feature Implementation Requirements:

| JSON Specification | Required Implementation |
|-------------------|------------------------|
| `"type": "3d-canvas-animation"` | Three.js or CSS 3D transforms |
| `"type": "video"` | HTML5 `<video>` element with controls |
| `"type": "interactive-diagram"` | SVG with actual paths and animations |
| `"animation": "parallax-scroll"` | GSAP ScrollTrigger or CSS parallax |
| `"connectionStyle": "dotted-svg-paths"` | Generate actual SVG `<path>` elements |
| `"video": true` | Full video player implementation |

#### Library Integration Policy:
```html
<!-- If JSON specifies GSAP animations -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js"></script>

<!-- If JSON specifies 3D graphics -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

<!-- Always include libraries mentioned in JSON's "animations" or "framework" fields -->
```

#### Verification Checklist:
- [ ] Every visual element from JSON is rendered
- [ ] All animations from JSON are implemented
- [ ] All interactive behaviors function correctly
- [ ] No "placeholder" divs replacing complex features
- [ ] All specified libraries are included via CDN

### 4. Accessibility
- **Web:** WCAG 2.1 AA (4.5:1 contrast, keyboard nav, alt text)

---

## Execution Workflow

### Web Pipeline (Updated: Auto-Stop After Analysis)

**Command: `/web [input]`**
```
Step 1: Browser exploration (MCP Kapture)
  ↓
Step 2: Analyze content → OVERWRITE 01_contents_web.json
  ↓
Step 3: Extract style → OVERWRITE 02_style_web.json
  ↓
⚠️ AUTO-STOP HERE ⚠️
Output: "✅ 분석 완료. 통합이 필요하면 /integrate를 입력하세요."
```

**Command: `/integrate` (Manual Request Only)**
```
Step 4: Merge analyses → OVERWRITE 03_integrate_web.json
  ↓
⚠️ AUTO-STOP HERE ⚠️
Output: "✅ 통합 완료. 코드 생성이 필요하면 /generate를 입력하세요."
```

**Command: `/generate` (Manual Request Only)**
```
Step 5: Generate code → output/web/index.html
  ↓
✅ COMPLETE
Output: "✅ 코드 생성 완료."
```

---

## 🚨 AI BEHAVIOR ENFORCEMENT

### Mandatory Responses to User Requests

**IF USER SAYS:** "너무 느리다" / "빠르게 해줘" / "효율적으로"
**AI MUST RESPOND:**
```
지침에 따라 정확한 분석을 위해 모든 콘텐츠를 상세히 캡처해야 합니다.
현재 체크포인트 X/30 완료 (최소 30개 필요). 계속 진행하겠습니다.
```

**THEN:** Continue systematic analysis from last checkpoint

### Progress Tracking (Mandatory)

**After Every 3 Checkpoints:**
```
✅ 체크포인트 3/30 완료 (최소 30개 필요, 현재 진행 중)
- 캡처된 요소: [list]
- 감지된 애니메이션: [list]
- 다음: 체크포인트 4
```

**When Reaching Checkpoint 30:**
```
✅ 최소 체크포인트 30개 달성! 계속 진행합니다 (현재 30/목표 ~50).
```

### Prohibited Phrases

❌ **NEVER SAY:**
- "빠르게 분석하겠습니다"
- "주요 섹션만 캡처하겠습니다"
- "효율적인 방법으로"
- "시간을 절약하기 위해"

✅ **ALWAYS SAY:**
- "체크포인트 X/30 완료 (최소 30개 필요, 진행 중)"
- "다음 체크포인트로 이동"
- "진행 중"

---

## Quality Checklist

### Web - Code Generation
- [ ] All content mapped to responsive components
- [ ] Design tokens applied (no hard-coded values)
- [ ] All interactive states documented
- [ ] Responsive behavior for mobile/tablet/desktop
- [ ] Accessibility WCAG 2.1 AA met
- [ ] SEO metadata complete
- [ ] **✅ JSON-to-HTML Fidelity Check (MANDATORY)**
  - [ ] Every visual element from JSON is rendered
  - [ ] All animations from JSON are implemented
  - [ ] All images use exact paths from JSON (no placeholders unless specified)
  - [ ] All complex features (3D, video, SVG) are fully implemented
  - [ ] All specified libraries are included via CDN
  - [ ] No simplification or placeholder replacements
  - [ ] Interactive behaviors match JSON specifications

---

## Configuration

- **Viewports:** Mobile (375px), Tablet (768px), Desktop (1440px)
- **Max Depth:** 3 levels of route traversal
- **Animation Wait:** 300ms after each interaction

---

## MCP-Based Exploration Checklist

### Per Page Requirements
- [ ] Full page scroll (adaptive checkpoints until page end)
- [ ] Navigation exploration (header/footer/mobile menu)
- [ ] Interactive elements (buttons, modals, forms)
- [ ] Route traversal (BFS, depth=3)
- [ ] Accessibility/SEO verification

### Completion Criteria
1. ✅ 30-80 screenshots per page (viewport change detection, fine-grained capture)
2. ✅ Footer visible (page end reached)
3. ✅ All interactive elements tested (minimum 50+ total interactions)
4. ✅ All animation frames captured
5. ✅ Route map complete
6. ✅ Evidence logged in analysis files


## 📐 HTML Generation Workflow - Step-by-Step Strategy

### ⚠️ CRITICAL: Systematic Section-Based Generation

**When generating HTML from `03_integrate_web.json`, ALWAYS use this workflow:**

### Phase 1: Pre-Generation Planning

**1. Read Integration JSON**
```javascript
const spec = readFile('analysis/web-pipeline/03_integrate_web.json');
const totalSections = spec.sections.length;
```

**2. Create Todo List**
- Use `manage_todo_list` tool to create structured plan
- Break work into logical groups (2-4 sections per task)
- Group related sections: Header+Nav, Hero+Social, Features+Benefits, Gallery+Specs, CTA+Footer

### Phase 2: Sequential Implementation

**FOR EACH TODO ITEM:**

1. Mark as in-progress
2. Extract section data from JSON
3. Generate HTML (convert JSON content/style to HTML + Tailwind)
4. Add to file using `replace_string_in_file`
5. Mark as completed
6. Report progress: "✅ 할 일 X/13 완료"

### Phase 3: Finalization

**After all todos completed:**
- ✅ Verify all sections present
- ✅ Check Tailwind classes applied correctly
- ✅ Confirm responsive breakpoints (mobile/tablet/desktop)
- ✅ Validate image paths (exact vs placeholder)
- ✅ Test accessibility attributes (alt, aria-*)

### 🎯 Key Principles

**1. Never Generate Entire File at Once**
- ❌ FORBIDDEN: Creating complete HTML in one step
- ✅ REQUIRED: Build incrementally, section by section

**2. Always Track Progress**
- Use todo list for visibility
- Update status after each section
- Report progress to user

**3. Maintain Context**
- Each `replace_string_in_file` preserves existing code
- Add new sections between last section and `</body>`
- Never overwrite completed sections

**4. JSON-to-HTML Mapping**
```json
// JSON spec
{
  "id": "hero",
  "content": { "heading": "Welcome" },
  "style": { "background": "#FFFFFF", "padding": "60px 0" }
}

// Generated HTML
<section id="hero" class="bg-white py-16 text-center">
  <h1 class="text-4xl font-bold">Welcome</h1>
</section>
```

**5. Tailwind Class Conversion**
| JSON Style | Tailwind Class |
|------------|----------------|
| `padding: "60px 0"` | `py-16` |
| `fontSize: "36px"` | `text-4xl` |
| `fontWeight: 700` | `font-bold` |

**6. Responsive Implementation**
```html
<!-- JSON: columns: { desktop: 3, tablet: 2, mobile: 1 } -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  <!-- Cards -->
</div>
```

### ❌ Anti-Patterns to Avoid

1. ❌ Generating full HTML in single create_file call
2. ❌ Skipping todo list creation
3. ❌ Not updating todo status
4. ❌ Batch completing multiple todos at once
5. ❌ Ignoring JSON layout/style specifications

---

## Version History

- **v2.9.0** (2025-01-16): Real-Time File Append Architecture
  - **Critical:** Restructured Step 4 to prioritize file writes over memory operations
  - Changed from single large write to progressive append operations (4-1 → 4-2 → 4-3 → 4-4 → 4-5)
  - File write sequence: Header → Visual Analysis → Animation → Interaction Test → Results
  - Each analysis component writes immediately after completion (no batching)
  - Removed Steps 5-6 (consolidated into Step 4's append operations)
  - **Result:** Ensures checkpoint data persists incrementally, prevents memory overflow, enables real-time progress monitoring
  - **Reason:** Large pages (100+ checkpoints) caused memory issues with batch writes
- **v2.8.0** (2025-01-15): Real-Time Analysis Logging & Concrete Documentation
  - **Critical:** Added 00_analysis_note.txt real-time logging system
  - Integrated checkpoint-by-checkpoint documentation into Progressive Scroll Analysis
  - Added mandatory 8-field template for animations/interactions (subject, visualDescription, observedBehavior, type, trigger, technicalImplementation, propertyChanges, codeHint)
  - Updated Pipeline Details: 01_contents and 02_style now read from 00_analysis_note.txt
  - Added concrete vs abstract examples to prevent information loss
  - Enhanced JSON schema with implementation-ready descriptions
  - **Result:** Prevents detail loss from analysis to JSON generation, ensures developer-implementable specifications
  - **Reason:** Previous system lost critical implementation details during JSON generation phase
- **v2.7.0** (2025-01-14): Documentation Optimization & Deduplication
  - **Critical:** Reduced file size from 1057 lines to 773 lines (27% reduction)
  - Removed 150+ lines of verbose JSON examples while preserving core templates
  - Consolidated redundant workflow descriptions (Phase 1-3 simplified)
  - Streamlined Tailwind conversion table (removed verbose mappings)
  - Removed duplicate "User Input Format" examples
  - **Result:** Maintains complete functionality with improved readability
  - **Reason:** 1000+ line file was difficult to navigate, contained excessive examples
- **v2.6.0** (2025-11-14): Interaction Testing Enforcement - Pipeline Fix
  - **Critical:** Moved interaction testing from Step 5 to Step 4 (integrated, cannot skip)
  - Added testedCount counter and console logging for transparency
  - Updated logging format: `interactionsTested.count` + `interactionsTested.elements`
  - Renumbered steps: 1-4 (integrated), 5 (logging), 6 (next checkpoint)
  - **Result:** Forces AI to test 10 interactions per checkpoint = 300-800 total tests
  - **Reason:** v2.5.0 test showed AI skipped all interactions (5/50 = 10%)
- **v2.5.0** (2025-11-14): Runtime Enforcement & Minimum Checkpoint Validation
  - Added validateScrollCommand() auto-detection system (EXECUTION VALIDATION)
  - Added MIN_CHECKPOINTS = 30 constant with validation logic
  - Enhanced PROGRESS REPORTING with "X/30" format and milestone messages
  - Added Behavioral Triggers detection (AI self-check before deviation)
  - Updated Completion Criteria to explicitly reject analysis with < 30 checkpoints
  - Strengthened ENFORCEMENT RULES to prevent PageDown/End key usage
  - **Result:** Forces AI to complete minimum 30 checkpoints, prevents premature shortcuts
- **v2.4.0** (2025-11-14): High-Fidelity Scroll Analysis System
  - Changed from fixed 21 checkpoints to adaptive 30-80 checkpoints
  - Implemented ArrowDown-based fine-grained scrolling (150-300px increments)
  - Added structural + visual change detection (dual-mode)
  - Implemented animation frame capture (3-5 frames per section transition)
  - Added screenshot hash comparison for visual changes
  - Updated all related sections (ABSOLUTE PROHIBITIONS, ENFORCEMENT RULES, MCP Checklist)
  - Expected result: 3-4x more detailed analysis with complete animation coverage
- **v2.3.0** (2025-11-11): Added HTML Generation Workflow
  - Step-by-step section-based generation strategy
  - Todo list planning and progress tracking
  - JSON-to-HTML mapping guidelines
  - Tailwind conversion reference table
  - Anti-patterns and correct workflow examples
- **v2.2.0** (2025-11-11): Updated auto-stop behavior
  - `/web` command now auto-stops after analysis (01_contents + 02_style)
  - `/integrate` command requires manual request
  - `/generate` command requires manual request
  - Improved user control over pipeline execution
- **v2.1.0** (2025-11-11): Fixed JSON-to-HTML fidelity issues
  - Added CRITICAL_POLICY for image handling (use exact paths)
  - Added FULL_IMPLEMENTATION mode for complex features
  - Updated generator configs (04_generate_html.json, 04_generate_tailwind.json)
  - Added mandatory verification checklist
  - Removed "minimal dependencies" constraint for complex features
- **v2.0.0** (2025-11-07): Separate pipelines, no shared analysis
- **v1.0.0** (2025-11-07): Initial modular architecture
