# Memo Chrome Extension - Development Guide

Complete guide for building, installing, and testing the Memo Chrome extension locally.

## Prerequisites

- Node.js and npm installed
- Google Chrome browser
- Code editor (VS Code, Cursor, etc.)

## Quick Start (5 Minutes)

### 1. Install Dependencies

```bash
# Navigate to project directory
cd memo  # or wherever you cloned the repo
npm install
```

This installs:
- `@anthropic-ai/sdk` - Reference SDK (not used in browser)
- `esbuild` - Fast JavaScript bundler

### 2. Build the Extension

```bash
npm run build
```

This bundles `background.js` into `dist/background.bundle.js` (73.5 KB).

**Note:** Only `background.js` is bundled. Other files (`sidepanel.js`, `content.js`, etc.) are loaded as ES modules directly.

### 3. Load Extension in Chrome

#### Option A: Via Chrome Extensions Page

1. Open Chrome and navigate to:
   ```
   chrome://extensions/
   ```

2. **Enable Developer Mode** (toggle in top-right corner)

3. Click **"Load unpacked"** button

4. Select the project directory (wherever you cloned the repo)

5. The extension should now appear in your extensions list! 🎉

#### Option B: Via Address Bar

1. Type `chrome://extensions/` in address bar
2. Follow steps 2-5 above

### 4. Verify Installation

After loading, you should see:
- ✅ **Memo** extension card with icon
- ✅ Version: 1.0
- ✅ Status: Enabled
- ✅ Service worker: Active (or Inactive until triggered)

## Using the Extension

### Opening the Side Panel

1. **Pin the extension** (click puzzle icon in toolbar, then pin Memo)
2. **Click the Memo icon** in your Chrome toolbar
3. The **side panel** will open on the right side of your browser

### Initial Setup

Before capturing content, configure an LLM provider:

1. Click **Settings** button in the side panel
2. Choose a provider:
   - **Anthropic Claude** (requires API key from https://console.anthropic.com/)
   - **OpenAI GPT** (requires API key from https://platform.openai.com/api-keys)
   - **Google Gemini** (requires API key from https://aistudio.google.com/app/apikey)
   - **Ollama** (local, no API key needed - requires Ollama running on localhost:11434)
3. Enter your API key (or skip for Ollama)
4. Select a model from dropdown
5. Click **"Test Connection"** to verify setup
6. Click **"Save Settings"**

### Capturing Your First Memo

1. Navigate to any webpage
2. Open the Memo side panel
3. Click **"Capture"** button
4. The page will enter **highlight mode**
5. **Hover over elements** to see them highlighted
6. **Click an element** to capture its content
7. Wait 2-10 seconds for AI processing
8. Your memo appears in the side panel!

### Chatting with Your Memos

1. Capture a few memos (try different websites)
2. Use **tag filters** to select context
3. Type a question in the chat input
4. Press Enter or click Send
5. The AI responds with citations to your memos

### Exporting Data

- **Export Single Memo:** Click memo → Export button
- **Export Chat:** Click export button in chat view
- **Export All:** Use storage.js utilities (programmatic)

## Development Workflow

### Making Code Changes

#### Editing Core Scripts

If you modify these files, you need to **reload the extension**:
- `background.js` - **Requires rebuild + reload**
- `sidepanel.js` - **Reload extension only**
- `content.js` - **Reload extension only**
- `ui.js`, `tags.js`, `storage.js` - **Reload extension only**

#### Editing Provider Files

If you modify provider files, **reload the extension**:
- `providers/anthropic-provider.js`
- `providers/openai-provider.js`
- `providers/gemini-provider.js`
- `providers/ollama-provider.js`
- `llm-provider-factory.js`
- `config/provider-config.js`

#### Editing Manifest or Assets

If you modify these files, **reload the extension**:
- `manifest.json`
- `styles.css`
- `icons/*`

### Rebuild and Reload Process

#### When to Rebuild (only for background.js changes)

```bash
npm run build
```

Then reload extension (see below).

#### How to Reload Extension

**Method 1: Extensions Page**
1. Go to `chrome://extensions/`
2. Find Memo extension
3. Click the **circular reload icon** ↻

**Method 2: Keyboard Shortcut (after installing extension reloader)**
1. Install "Extensions Reloader" from Chrome Web Store
2. Use keyboard shortcut (Ctrl+Shift+R or Cmd+Shift+R)

**Method 3: Remove and Re-add**
1. Click "Remove" on extension card
2. Click "Load unpacked" again
3. Select project directory

### Debugging

#### Debugging the Side Panel

1. Open side panel
2. **Right-click** inside the side panel
3. Select **"Inspect"**
4. DevTools opens for side panel context
5. View console logs, errors, network requests

#### Debugging the Background Service Worker

1. Go to `chrome://extensions/`
2. Find Memo extension
3. Click **"Service worker"** link (may say "Inspect views: service worker")
4. DevTools opens for background worker
5. View console logs, errors, API calls

#### Debugging Content Scripts

1. Open any webpage
2. **Right-click** on page → **"Inspect"**
3. In DevTools Console, type:
   ```javascript
   // Check if content script loaded
   console.log('Content script test');
   ```
4. Content script logs appear in page DevTools (not side panel)

#### Common Issues

**Issue: Extension not loading**
- Solution: Check `manifest.json` for syntax errors
- Solution: Ensure `dist/background.bundle.js` exists (run `npm run build`)

**Issue: Side panel blank**
- Solution: Check DevTools console for errors (right-click panel → Inspect)
- Solution: Check if `sidepanel.html` and `sidepanel.js` exist

**Issue: Content capture not working**
- Solution: Check page DevTools console for content script errors
- Solution: Verify permissions in `manifest.json` include `<all_urls>`
- Solution: Try reloading the page after loading extension

**Issue: AI processing fails**
- Solution: Verify API key is correct in Settings
- Solution: Check provider is running (Ollama requires local service)
- Solution: Check background worker DevTools for API errors
- Solution: Verify host_permissions in `manifest.json` include provider URLs

**Issue: Service worker inactive**
- Solution: This is normal! It activates on demand
- Solution: Click extension icon to activate
- Solution: Check "Service worker" link on extensions page to inspect

### Testing

#### Manual Testing Checklist

- [ ] Build completes without errors
- [ ] Extension loads in Chrome
- [ ] Side panel opens and displays UI
- [ ] Settings page allows provider configuration
- [ ] Connection test succeeds for your provider
- [ ] Content capture highlights elements
- [ ] Captured content is processed and saved
- [ ] Memos display in list view
- [ ] Memo detail view shows full content
- [ ] Tags can be created and assigned
- [ ] Chat interface accepts input
- [ ] Chat returns responses with citations
- [ ] Export functionality works
- [ ] Storage persists across browser restarts

#### Automated Testing

```bash
# Run provider tests (Node.js environment)
node tests/run-anthropic-test.js
node tests/run-openai-test.js
node tests/run-gemini-test.js
node tests/run-ollama-test.js

# Run integration tests
node tests/run-all-tests.js

# Run browser-based tests
# Open in browser: tests/test-providers.html
```

#### Testing with Different Providers

1. **Anthropic Claude:**
   - Get API key: https://console.anthropic.com/
   - Models: Sonnet 4.5, Haiku 4.5, Opus 4.1, Opus 4, Sonnet 4, Sonnet 3.7, Sonnet 3.5v2, Haiku 3.5
   - Expected response time: 2-5 seconds

2. **OpenAI GPT:**
   - Get API key: https://platform.openai.com/api-keys
   - Models: GPT-4o, GPT-4.1, GPT-4o-mini
   - Expected response time: 3-8 seconds

3. **Google Gemini:**
   - Get API key: https://aistudio.google.com/app/apikey
   - Models: Gemini 2.5 Pro, Gemini 2.5 Flash
   - Expected response time: 2-6 seconds

4. **Ollama (Local):**
   - Install: https://ollama.ai
   - Start service: `ollama serve`
   - Pull model: `ollama pull llama2`
   - No API key needed
   - Expected response time: 5-30 seconds (varies by hardware)

### Development Tips

#### Fast Iteration

For rapid development of UI changes:
1. Make changes to `sidepanel.js` or `ui.js`
2. Reload extension (no rebuild needed)
3. Close and reopen side panel
4. Changes take effect immediately

#### Provider Development

When adding/modifying providers:
1. Edit provider file in `providers/`
2. Reload extension (no rebuild needed)
3. Test with connection test in Settings
4. Check background worker DevTools for errors

#### Storage Inspection

View extension storage:
1. Open DevTools for side panel
2. Go to **Application** tab
3. Expand **Storage** → **Local Storage** → **chrome-extension://...**
4. View `memos`, `tags`, `chatSessions`, `settings`

#### Clear All Data

To reset extension state:
1. Open DevTools for side panel
2. Console tab, run:
   ```javascript
   chrome.storage.local.clear(() => console.log('Cleared'));
   chrome.storage.sync.clear(() => console.log('Cleared'));
   ```
3. Reload extension

### Performance Monitoring

#### Check Bundle Size

```bash
du -h dist/background.bundle.js
# Currently: 73.5 KB (target: <100 KB)
```

#### Check Memory Usage

1. Open `chrome://extensions/`
2. Click "Details" on Memo extension
3. View "Memory usage" section
4. Typical: ~10-15 MB for background worker

#### Profile Performance

1. Open DevTools for side panel or background worker
2. Go to **Performance** tab
3. Click Record
4. Perform actions (capture memo, chat, etc.)
5. Stop recording
6. Analyze flame graph for bottlenecks

## Project Structure

```
memo/
├── manifest.json              # Extension configuration
├── background.js              # Service worker (bundled)
├── sidepanel.html/js          # Main UI
├── content.js                 # Content capture
├── providers/                 # LLM provider implementations
│   ├── anthropic-provider.js
│   ├── openai-provider.js
│   ├── gemini-provider.js
│   └── ollama-provider.js
├── config/
│   └── provider-config.js     # Provider configuration
├── dist/                      # Build output
│   └── background.bundle.js   # Bundled background worker
├── tests/                     # Test files
├── icons/                     # Extension icons
├── styles.css                 # UI styles
└── [other modules]            # ui.js, tags.js, storage.js, etc.
```

## Common Development Commands

```bash
# Install dependencies
npm install

# Build extension
npm run build

# Run tests
node tests/run-all-tests.js

# Check for outdated packages
npm outdated

# Update dependencies
npm update

# Audit security vulnerabilities
npm audit

# Fix security issues
npm audit fix
```

## Next Steps

1. **Make your first code change:**
   - Edit `sidepanel.js` to add a console log
   - Reload extension
   - Open side panel and check DevTools console

2. **Capture your first memo:**
   - Configure an LLM provider
   - Navigate to a website
   - Capture content and see it processed

3. **Explore the codebase:**
   - Read `CLAUDE.md` for architecture overview
   - Check `metrics/latest.md` for code metrics
   - Browse `tests/` for testing examples

4. **Join the development workflow:**
   - Make changes to providers or UI
   - Test thoroughly with all 4 providers
   - Check metrics with `/metrics` command
   - Commit changes with clear messages

## Getting Help

- **Architecture Questions:** Read `CLAUDE.md`
- **Code Metrics:** Check `metrics/latest.md`
- **Provider Setup:** See provider-specific docs in `docs/`
- **Testing:** Review examples in `tests/`
- **Issues:** Check browser DevTools console and background worker console

Happy developing! 🚀
